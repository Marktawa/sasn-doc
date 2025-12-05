## 7

I am now working on the "Update Note Flow". I need help with updating `pages/notes/[id].vue`

## 6

For the given code, please check out the 'Create "Create Note page"' section then outline to me what considerations have to be made in order to produce the `pages/notes/create.vue` file.

## 5

Revise the code:
- Rename "Note-collaborator" collection to "Collaborator" collection
- Functionality for users to accept invites is unnecessary. We only want users to be added as editors by note owner. Therefore the email is used to inform a collaborator that they have been added as a collaborator to a note
- inviteToken, invitedAt, respondedAt unnecessary

```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
  // Override create to ensure users can only create notes for themselves
  async create(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to create a note');
    }

    // Extract title and content from request, ignore any user field attempts
    const { title, content } = ctx.request.body.data || ctx.request.body;

    // Create the note with the authenticated user as owner
    const note = await strapi.entityService.create('api::note.note', {
      data: {
        title,
        content,
        user: user.id, // Force the user to be the authenticated user
        isShared: false, // Default to not shared
      },
      populate: ['user'],
    });

    ctx.body = note;
  },

  // Override delete to ensure users can only delete their own notes
  async delete(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to delete a note');
    }

    const { id } = ctx.params;

    // First, check if the note exists and belongs to the user
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have permission to delete it');
    }

    // Delete the note
    const deletedNote = await strapi.entityService.delete('api::note.note', id);

    ctx.body = deletedNote;
  },

  // Override update to allow both owners and editor collaborators
  async update(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to update a note');
    }

    const { id } = ctx.params;
    const { title, content } = ctx.request.body.data || ctx.request.body;

    // Check access using helper method
    const access = await this.checkNoteAccess(user.id, id);
    if (!access) {
      return ctx.notFound('Note not found or you do not have permission to update it');
    }

    // Check if user has edit permission
    if (access.type === 'collaborator' && access.permission !== 'editor') {
      return ctx.forbidden('You do not have edit permission for this note');
    }

    // Get current note data
    const existingNote = await strapi.entityService.findOne('api::note.note', id, {
      fields: ['isShared', 'user'],
    });

    // Update the note (preserve original owner and sharing status)
    const updatedNote = await strapi.entityService.update('api::note.note', id, {
      data: {
        title,
        content,
        // Maintain original owner and sharing status
        user: existingNote.user,
        isShared: existingNote.isShared,
      },
      populate: ['user'],
    });

    ctx.body = updatedNote;
  },

  async find(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to view notes');
    }

    // Get notes owned by user
    const ownedNotes = await strapi.entityService.findMany('api::note.note', {
      filters: { user: user.id },
      populate: ['user'],
    });

    // Get notes user collaborates on
    const collaborations = await strapi.entityService.findMany('api::collaborator.collaborator', {
      filters: { user: user.id },
      populate: { 
        note: { 
          populate: ['user'],
        },
      },
    });

    const collaboratedNotes = collaborations.map(collab => ({
      ...collab.note,
      collaboratorPermission: collab.permission,
      isCollaborator: true,
    }));

    // Mark owned notes
    const markedOwnedNotes = ownedNotes.map(note => ({
      ...note,
      isOwner: true,
    }));

    // Combine and return all accessible notes
    const allNotes = [...markedOwnedNotes, ...collaboratedNotes];
    
    ctx.body = allNotes;
  },

  async findOne(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to view a note');
    }

    const { id } = ctx.params;

    // Check access using helper method
    const access = await this.checkNoteAccess(user.id, id);
    if (!access) {
      return ctx.notFound('Note not found or you do not have access');
    }

    const note = await strapi.entityService.findOne('api::note.note', id, {
      populate: ['user'],
    });

    // Add access information to response
    ctx.body = {
      ...note,
      accessType: access.type,
      permission: access.permission || 'owner',
    };
  },

  // New preview method for public access to shared notes
  async preview(ctx) {
    const { id } = ctx.params;

    // Fetch the note, but only if it has been shared
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { isShared: true }, // Only allow access to shared notes
      fields: ['title', 'content', 'isShared'],
      populate: {
        user: {
          fields: ['username'], // Only include username for attribution
        },
      },
    });

    if (!note) {
      return ctx.notFound('Note not found or has not been shared');
    }

    // Return the note data with limited user info
    ctx.body = {
      id: note.id,
      title: note.title,
      content: note.content,
      author: note.user?.username || 'Anonymous',
      sharedAt: new Date().toISOString(), // Optional: timestamp when accessed
    };
  },

  async shareLink(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to share a note');
    }

    const { id } = ctx.params;
    const { to } = ctx.request.body;

    // Fetch note, but only if it belongs to the logged-in user
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      fields: ['title', 'isShared'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    // Mark the note as shared if it isn't already
    if (!note.isShared) {
      await strapi.entityService.update('api::note.note', id, {
        data: { isShared: true },
      });
    }

    const previewUrl = `${process.env.APP_URL || 'http://localhost:3000'}/notes/preview/${id}`;

    await sendEmail({
      to,
      subject: `Shared note: ${note.title || `#${id}`}`,
      html: `<p>You were invited to view a note.</p>
             <p><a href="${previewUrl}">Open note</a></p>`,
    });

    ctx.body = {
      ok: true,
      sentTo: Array.isArray(to) ? to : [to],
      previewUrl,
    };
  },

  // Optional: Method to stop sharing a note
  async unshare(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to unshare a note');
    }

    const { id } = ctx.params;

    // Fetch note, but only if it belongs to the logged-in user
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      fields: ['title', 'isShared'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    // Mark the note as not shared
    await strapi.entityService.update('api::note.note', id, {
      data: { isShared: false },
    });

    ctx.body = {
      ok: true,
      message: 'Note is no longer shared publicly',
    };
  },

  // Add editors to collaborate on a note
  async addEditors(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to add editors');
    }

    const { id } = ctx.params;
    const { emails } = ctx.request.body;

    // Validate that user owns the note
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      fields: ['title'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have permission to add editors');
    }

    const emailList = Array.isArray(emails) ? emails : [emails];
    const addedEditors = [];
    const errors = [];

    for (const email of emailList) {
      try {
        // Check if user exists in system
        const existingUser = await strapi.query('plugin::users-permissions.user').findOne({
          where: { email },
        });

        if (!existingUser) {
          errors.push({ email, error: 'User not found in system' });
          continue;
        }

        // Check for existing collaboration
        const existingCollaboration = await strapi.entityService.findMany('api::collaborator.collaborator', {
          filters: {
            note: id,
            user: existingUser.id,
          },
        });

        if (existingCollaboration.length > 0) {
          errors.push({ email, error: 'User is already a collaborator' });
          continue;
        }

        // Create collaboration record
        const collaboration = await strapi.entityService.create('api::collaborator.collaborator', {
          data: {
            note: id,
            user: existingUser.id,
            permission: 'editor',
            addedBy: user.id,
            addedAt: new Date(),
          },
        });

        // Send notification email
        await sendEmail({
          to: email,
          subject: `You've been added as an editor to: ${note.title}`,
          html: `
            <p>You've been added as an editor by ${user.username || user.email} to collaborate on a note.</p>
            <p><strong>Note:</strong> ${note.title}</p>
            <p><strong>Permission:</strong> Editor</p>
            <p>You can now edit this note when you log into the application.</p>
          `,
        });

        addedEditors.push({
          email,
          userId: existingUser.id,
          username: existingUser.username,
        });
      } catch (error) {
        errors.push({ email, error: error.message });
      }
    }

    ctx.body = {
      ok: true,
      editorsAdded: addedEditors.length,
      addedEditors,
      errors,
    };
  },

  // Get list of collaborators for a note
  async getCollaborators(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in');
    }

    const { id } = ctx.params;

    // Check if user has access to this note (owner or collaborator)
    const hasAccess = await this.checkNoteAccess(user.id, id);
    if (!hasAccess) {
      return ctx.forbidden('You do not have access to this note');
    }

    const collaborators = await strapi.entityService.findMany('api::collaborator.collaborator', {
      filters: { note: id },
      populate: {
        user: { fields: ['username', 'email'] },
        addedBy: { fields: ['username', 'email'] },
      },
    });

    ctx.body = collaborators;
  },

  // Remove a collaborator (owner only)
  async removeCollaborator(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in');
    }

    const { id, userId } = ctx.params;

    // Verify user owns the note
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have permission');
    }

    // Remove collaboration
    const collaborations = await strapi.entityService.findMany('api::collaborator.collaborator', {
      filters: { note: id, user: userId },
    });

    if (collaborations.length > 0) {
      await strapi.entityService.delete('api::collaborator.collaborator', collaborations[0].id);
    }

    ctx.body = { ok: true, message: 'Collaborator removed' };
  },

  // Helper method to check note access
  async checkNoteAccess(userId, noteId) {
    // Check if user owns the note
    const ownedNote = await strapi.entityService.findOne('api::note.note', noteId, {
      filters: { user: userId },
    });

    if (ownedNote) return { type: 'owner', note: ownedNote };

    // Check if user is a collaborator
    const collaboration = await strapi.entityService.findMany('api::collaborator.collaborator', {
      filters: { 
        note: noteId, 
        user: userId 
      },
      populate: { note: true },
    });

    if (collaboration.length > 0) {
      return { 
        type: 'collaborator', 
        permission: collaboration[0].permission,
        note: collaboration[0].note 
      };
    }

    return false;
  },
}));
```


## 4

How do I update the code such that other users are able to view a note once it has been shared?

### Implementation

- Strapi install - backend
- Create Admin
```shell
npm run strapi admin:create-user -- --firstname=Kai --lastname=Doe --email=chef@strapi.io --password=Gourmet1234
```

- Login Strapi Admin 
- Created `Notes` collection 
- Added `title` and `content` fields 
- Added sample entries to `Notes` collection 
- Added `find` and `findOne` permission to `Notes` collection for public user 
- Created Brevo API key 
- Added Brevo API key to env var

Install Brevo SDK
```shell
npm install @getbrevo/brevo
```

- Add Brevo sender helper, add `src/services/brevo.js`
```js
'use strict';

const Brevo = require('@getbrevo/brevo');

const api = new Brevo.TransactionalEmailsApi();
// Read from .env -> BREVO_API_KEY
api.setApiKey(Brevo.TransactionalEmailsApiApiKeys.apiKey, process.env.BREVO_API_KEY);

async function sendEmail({ to, subject, html }) {
  const sendSmtpEmail = new Brevo.SendSmtpEmail();

  // Accept a string or an array of emails
  const list = Array.isArray(to) ? to : [to];

  sendSmtpEmail.to = list.map((email) => ({ email }));
  sendSmtpEmail.sender = {
    email: process.env.SENDER_EMAIL || 'no-reply@example.com',
    name: process.env.SENDER_NAME || 'Notes App',
  };
  sendSmtpEmail.subject = subject || 'Test email';
  sendSmtpEmail.htmlContent = html || '<p>Hello from Strapi + Brevo!</p>';

  return api.sendTransacEmail(sendSmtpEmail);
}

module.exports = { sendEmail };
```

Update `.env` file:
```
SENDER_EMAIL=paul.zimba96@gmail.com
SENDER_NAME=Notes App
APP_URL=http://localhost:3000
```

- Add public route for testing, `src/api/note/routes/share-link.js`
```js
'use strict';

module.exports = {
  routes: [
    {
      method: 'POST',
      path: '/notes/:id/shareLink',
      handler: 'note.shareLink',
      config: {
        auth: false,
      },
    },
  ],
};
```

- Update controller, `src/api/note/controllers/note.js`
```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
  async shareLink(ctx) {
    const { id } = ctx.params;
    const { to } = ctx.request.body; // string email or array of emails

    // Minimal fetch to include title (optional)
    const note = await strapi.entityService.findOne('api::note.note', id, {
      fields: ['title'],
    });

    const previewUrl = `${process.env.APP_URL || 'http://localhost:3000'}/notes/preview/${id}`;

    await sendEmail({
      to,
      subject: `Shared note: ${note?.title || `#${id}`}`,
      html: `<p>You were invited to view a note.</p>
             <p><a href="${previewUrl}">Open note</a></p>`,
    });

    ctx.body = { ok: true, sentTo: Array.isArray(to) ? to : [to], previewUrl };
  },
}));
```

- Test with `curl`
```shell
curl -X POST http://localhost:1337/api/notes/1/shareLink \
  -H "Content-Type: application/json" \
  -d '{"to":"markmunyaka@gmail.com"}'
```

---

- Update route for testing, `src/api/note/routes/share-link.js`
```js
'use strict';

module.exports = {
  routes: [
    {
      method: 'POST',
      path: '/notes/:id/shareLink',
      handler: 'note.shareLink',
    },
  ],
};
```

- Start Strapi Server

- Register an OAuth App in GitHub
    - Github Developer Settings > OAuth Apps > New OAuth App
    - New OAuth App
    - Application Name: `My Notes App`
    - Homepage URL: `http://localhost:1337/`
    - Authorization URL: `http://localhost:1337/api/connect/github/callback`
    - Copy `Client ID` and `Client Secret`

- Configure Provider in Strapi
    - Go to Settings > Users & Permissions Plugin > Providers
    - Find GitHub
    - Toggle Enabled to true
    - Client ID: `GitHub Client ID`
    - Client Secret: `GitHub Client Secret`
    - Redirect URL: `http://localhost:1337/api/auth/github/callback`
    - Save

### Configure Public Permissions

- Go to Settings > Users & Permissions > Roles > Public
- Remove `find` and `findOne` permissions for `Notes`.

- Go to Settings > Users & Permissions > Roles > Authenticated
- Add `shareLink`, `find`,and `findOne` permissions for `Notes`.

### Get Temporary code

- Visit the URL `http://localhost:1337/api/connect/github` in your browser.
- GitHub prompts for login and authorization.
- After approval GitHub redirects to `http://localhost:1337/api/auth/callback?code=GITHUB_CODE`
- You will see the following JSON response in your browser

```json
{
  "jwt": "YOUR_JWT_TOKEN",
  "user": {
    "id": 1,
    "username": "example",
    "email": "user@example.com"
  }
}
```

### Use JWT to Access Protected Endpoints
- Copy `jwt` from the response

- Add jwt code to `.secret`:
```
GITHUB_JWT_TOKEN=your-code
```

Load it into your shell:
```shell
export $(cat .secret | xargs)
```

- Call API endpoints
```shell
curl -X POST "http://localhost:1337/api/notes/1/shareLink" \
-H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
-H "Content-Type: application/json" \
-d '{"to":"markmunyaka@gmail.com"}'
```

### Update `Notes` collection fields

- Add a `Relations` field whereby `User (from: users-permissions-user)`, then click on Users have many notes.
- Update Notes collection entries. Both User and Notes.

### Test using `JWT` tokens

```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json"
```

- Authenticated User should only see their own notes
### Override the `find`, `findOne`, and `shareLink` controller

```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
  async find(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to view notes');
    }

    return await strapi.entityService.findMany('api::note.note', {
      filters: { user: user.id },
      populate: ['user'],
    });
  },

  async findOne(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to view a note');
    }

    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    return note;
  },

  async shareLink(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to share a note');
    }

    const { id } = ctx.params;
    const { to } = ctx.request.body;

    // Fetch note, but only if it belongs to the logged-in user
    const note = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      fields: ['title'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    const previewUrl = `${process.env.APP_URL || 'http://localhost:3000'}/notes/preview/${id}`;

    await sendEmail({
      to,
      subject: `Shared note: ${note.title || `#${id}`}`,
      html: `<p>You were invited to view a note.</p>
             <p><a href="${previewUrl}">Open note</a></p>`,
    });

    ctx.body = {
      ok: true,
      sentTo: Array.isArray(to) ? to : [to],
      previewUrl,
    };
  },
}));
```

- Test:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

```shell
curl -X GET "http://localhost:1337/api/notes/1" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

### Implement Collaborators


## 3

How should I go about rewriting the section? I like to work incrementally. I start with the simplest, most barebone implementation then slowly graduate the complexity, showcasing my progress each step of the way. One feature at a time. Styling is only done after I have confirmed that the app works.

## 2

Take a look at the following text. Please list out the features that are being implemented.

## 1

I need to update the article `part3.md`. It is part of an article series. `part1_rev.md` and `part2_rev.md` are parts of the article series I have updated already.

I have decided to make some changes to the `part3.md` article update.I have decided that:
- Images will be hosted locally
- The Strapi backend will be deployed to Strapi Cloud
- THe Nuxt frontend will be deployed to Cloudflare Pages
- Styling will be done after confimring the app works and after deploying a barebones version of the app. Less code to deal with makes it easier to read

 This article is very difficlut to follow. It has long code snippets that are hard to understand and seems more like a info dump than a teaching process. I am thinking about simplifying it so that it works with current versions of Nuxt and Strapi. 

When I write articles, I don't include the functionality and styling in one go. I prefer writing about a feature in its simplest barebones state without any styling. Then test each feature to see if it works. I also don't bundle up features all at once. I like writing feature by feature taking the simplest and most minimal approach first. This helps me think. The styling is only done after I have confirmed that all the features work. 

Briefly outline to me how I can go about this?
