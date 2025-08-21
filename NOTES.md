## Link sharing

### Steps Overview

1. [x] Create Strapi project (`backend`)
2. [x] Create `Notes` collection with `title` + `content`
3. [x] Add sample notes
4. [x] Configure public permissions for `find` + `findOne`
5. [x] Add Brevo API key to `.env`
6. [x] Install Brevo SDK
7. [x] Add email sender service (`src/services/brevo.js`)
8. [x] Create `/notes/:id/shareLink` route + controller
9. [x] Test with `curl` → **email received**

---

### Phase 1 – Core email endpoints

10. **Lock down routes**

* Remove `auth: false` from the `shareLink` route.
* Add GitHub Auth
* Require authenticated users to call it.
* Add roles/permissions later for fine control.

11. **Add “request edit access” route**

* `/notes/:id/requestEdit`
* Sends an email to the note owner saying *User X wants to edit this note.*

12. **Add “add editors” route**

* `/notes/:id/addEditors`
* Accepts a list of emails → sends invitations with preview/edit links.

13. **Centralize email templates**

* Store HTML templates (maybe in `/emails`) so you’re not repeating strings.
* Later swap them with Brevo templates if desired.

---

### Phase 2 – Data tracking

14. **Add `owner` and `editors` fields to `Notes`**

* Relation: `owner` → `User` (one-to-one)
* Relation: `editors` → `User` (many-to-many)
* Store who owns and who can edit.

15. **Record share requests**

* Optional: create a `ShareRequests` collection that logs:

  * note ID
  * requester email
  * status (`pending`, `approved`, `rejected`)

---

### Phase 3 – Frontend integration (Nuxt)

16. **Scaffold Nuxt frontend project**

* Create `frontend/` with Nuxt 3.
* Add Axios (or `$fetch`) for API calls.

17. **Display notes list + preview page**

* Fetch from `/notes` and `/notes/:id`.

18. **Add “Share” button on preview page**

* Opens a modal or form for entering recipient email(s).
* Calls `/notes/:id/shareLink` → shows success message.

19. **Add “Request Edit Access” button**

* Calls `/notes/:id/requestEdit`.

20. **Handle “Add Editors” (owner only)**

* Simple form to enter multiple emails.
* Calls `/notes/:id/addEditors`.

---

### Phase 4 – Polish & scale

21. **Validation + error handling**

* Prevent empty emails or invalid formats.
* Catch Brevo API errors gracefully.

22. **Secure endpoints with Strapi auth**

* Use JWT (built-in).
* Ensure only owners can add editors.

23. **Add rate limiting / throttling**

* Prevent spamming invites.

24. **Switch to Brevo dynamic templates**

* Create templates in Brevo dashboard.
* Send template IDs instead of inline HTML.

25. **Logging & monitoring**

* Add logs in Strapi for every email send (success/failure).
* Optionally expose `/emails/logs` for admins.


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

Create a public preview route that allows anyone (including unauthenticated users) to view shared notes.

- Add Preview route `src/api/note/routes/preview.js`
```js
'use strict';

module.exports = {
    routes: [
        {
            method: 'GET',
            path: '/notes/preview/:id',
            handler: 'note.preview',
            config: {
                auth: false,
            }
        }
    ]
}
```

- Update controller `src/api/note/controllers/note.js`
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

  async preview(ctx) {
    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        fields: ['title', 'content'],
        populate: {
            user: {
                fields: ['username'],
            },
        },
    });

    if (!note) {
        return ctx.notFound('Note not found');
    }

    ctx.body = {
        id: note.id,
        title: note.title,
        content: note.content,
        author: note.user?.usrname || 'Anonymous',
        sharedAt: new Date().toISOString(),
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

- Start your Strapi server
- Go to Settings > Users & Permissions > Roles > Public
- Enable `preview` permission for Notes collection

- Test
```shell
curl -X GET "http://localhost:1337/api/notes/preview/1" \
    -H "Content-Type: application/json"
```

> **NOTE:**
>
> For notes which have been shared, viewing is for the public

- Start Strapi Server
- Update Notes Collection
- Add a new field to Notes collection named `isShared` of Boolean type with a default value of `false`

- Update the controller
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

  async preview(ctx) {
    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { isShared: true },
        fields: ['title', 'content', 'isShared'],
        populate: {
            user: {
                fields: ['username'],
            },
        },
    });

    if (!note) {
        return ctx.notFound('Note not found');
    }

    ctx.body = {
        id: note.id,
        title: note.title,
        content: note.content,
        author: note.user?.usrname || 'Anonymous',
        sharedAt: new Date().toISOString(),
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

    if (!note.isShared) {
        await strapi.entityService.update('api::note.note', id, {
            data: { isShared: true },
        })
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

  async unshare(ctx) {
    const user = ctx.state.user;
    if (!user) {
        return ctx.unauthorized('You must be logged in to unshare a note');
    }

    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { user: user.id },
        fields: ['title', 'isShared'],
    });

    if (!note) {
        return ctx.notFound('Note not found or you do not have access');
    }

    await strapi.entityService.update('api::note.note', id, {
        data: { isShared: false },
    });

    ctx.body = {
        ok: true,
        message: 'Note is no longer shared publicly',
    };
  },
}));
```

- Add unshare route `src/api/note/routes/unshare.js`
```js
'use strict';

module.exports = {
    routes: [
        {
            method: 'POST',
            path: '/notes/:id/unshare',
            handler: 'note.unshare',
        },
    ],
};
```

- Update permissions
- Settings > Users & permissions > Roles > Authenticated
- Add the `unshare` permission for Notes

- Test
- Access note that hasn't been shared
```shell
curl -X GET "http://localhost:1337/api/notes/preview/1"
```

- Share the note
```shell
curl -X POST "http://localhost:1337/api/notes/1/shareLink" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"to":"markmunyaka@gmail.com"}'
```

- Access note thet has been shared
```shell
curl -X GET "http://localhost:1337/api/notes/preview/1"
```

- Unshare note
```shell
curl -X POST "http://localhost:1337/api/notes/1/unshare" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

- Access note again

### USER2TOKEN

- User 2 mustn't be able to view User 1 notes. Reverse must be true

- User 1 - markmunyaka@gmail.com `USER1TOKEN`
- User 2 - craigsimakando@gmail.com `USER2TOKEN`

- Create token
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
USER2_JWT_TOKEN=your-code
```
USER2_JWT_TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MiwiaWF0IjoxNzU1NjM0MzMxLCJleHAiOjE3NTgyMjYzMzF9.07yFTpwzRMCmGNyQsgyVgZMtTO3SQhA5wD3otK1NeLE

Load it into your shell:
```shell
export $(cat .secret | xargs)
```

- View USER 2 notes using USER 2 token
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $USER2_JWT_TOKEN"
```

- Attepmt to View USER 1 notes using USER 2 token
```shell
curl -X GET "http://localhost:1337/api/notes/1" \
  -H "Authorization: Bearer $USER2_JWT_TOKEN"
```

### CRUD

### Create Note

- For `create` all we need to do is to update permissions for an authenticated user to be able to create a note.
- I don't know about this, but does the `create` method need to be overridden in the controller so that a user can only create notes that they will own. They cannot create notes for another user.

- Start Strapi server
- Settings > Users & Permissions > Roles > Authenticated
- Enable `create` permission for Notes
- Stop Strapi server
- Override `create` method to ensure users can only create notes for themselves.
- Update controller
```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
    async create(ctx) {
        const user = ctx.state.user;
        if (!user) {
            return ctx.unauthorized('You must be logged in to create a note');
        }

        const { title, content } = ctx.request.body.data || ctx.request.body;

        const note = await strapi.entityService.create('api::note.note', {
            data: {
                title,
                content,
                user: user.id,
                isShared: false,
            },
            populate: ['user'],
        });

        ctx.body = note;
    },

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

  async preview(ctx) {
    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { isShared: true },
        fields: ['title', 'content', 'isShared'],
        populate: {
            user: {
                fields: ['username'],
            },
        },
    });

    if (!note) {
        return ctx.notFound('Note not found');
    }

    ctx.body = {
        id: note.id,
        title: note.title,
        content: note.content,
        author: note.user?.usrname || 'Anonymous',
        sharedAt: new Date().toISOString(),
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

    if (!note.isShared) {
        await strapi.entityService.update('api::note.note', id, {
            data: { isShared: true },
        })
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

  async unshare(ctx) {
    const user = ctx.state.user;
    if (!user) {
        return ctx.unauthorized('You must be logged in to unshare a note');
    }

    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { user: user.id },
        fields: ['title', 'isShared'],
    });

    if (!note) {
        return ctx.notFound('Note not found or you do not have access');
    }

    await strapi.entityService.update('api::note.note', id, {
        data: { isShared: false },
    });

    ctx.body = {
        ok: true,
        message: 'Note is no longer shared publicly',
    };
  },
}));
```

- Test Create Functionality
```shell
curl -X POST "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Tweflth Note via POST",
      "content": "This is the content for the twelfth note entry"
    }
  }'
```

- Create note for another user (Will not work, it will assign to auth user)
```shell
curl -X POST "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Thirteenth Note via POST",
      "content": "This is the content for the thirteenth note entry",
      "user": 2
    }
  }'
```

### Delete

- For `delete` we need to update permissions for an authenticated user to delete a note.
- The `delete` method needs to be overidden in the controller so that user only deletes notes they own.

- Update permissions:
- Settings > Users & Permissions > Roles > Authenticated
- Enable `delete` permission for Notes

- Update controller
```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
    async create(ctx) {
        const user = ctx.state.user;
        if (!user) {
            return ctx.unauthorized('You must be logged in to create a note');
        }

        const { title, content } = ctx.request.body.data || ctx.request.body;

        const note = await strapi.entityService.create('api::note.note', {
            data: {
                title,
                content,
                user: user.id,
                isShared: false,
            },
            populate: ['user'],
        });

        ctx.body = note;
    },

  async delete(ctx) {
    const user = ctx.state.user;
    if (!user) {
        return ctx.unauthorized('You must be logged in to delete a note');
    }

    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { user: user.id },
        populate: ['user'],
    });

    if (!note) {
        return ctx.notFound('Note not found or you do not have permission to delete it');
    }

    const deletedNote = await strapi.entityService.delete('api::note.note', id);

    ctx.body = deletedNote;
  },

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

  async preview(ctx) {
    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { isShared: true },
        fields: ['title', 'content', 'isShared'],
        populate: {
            user: {
                fields: ['username'],
            },
        },
    });

    if (!note) {
        return ctx.notFound('Note not found');
    }

    ctx.body = {
        id: note.id,
        title: note.title,
        content: note.content,
        author: note.user?.usrname || 'Anonymous',
        sharedAt: new Date().toISOString(),
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

    if (!note.isShared) {
        await strapi.entityService.update('api::note.note', id, {
            data: { isShared: true },
        })
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

  async unshare(ctx) {
    const user = ctx.state.user;
    if (!user) {
        return ctx.unauthorized('You must be logged in to unshare a note');
    }

    const { id } = ctx.params;

    const note = await strapi.entityService.findOne('api::note.note', id, {
        filters: { user: user.id },
        fields: ['title', 'isShared'],
    });

    if (!note) {
        return ctx.notFound('Note not found or you do not have access');
    }

    await strapi.entityService.update('api::note.note', id, {
        data: { isShared: false },
    });

    ctx.body = {
        ok: true,
        message: 'Note is no longer shared publicly',
    };
  },
}));
```

- Test
- Create note
```shell
curl -X POST "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Note to Delete",
      "content": "This note will be deleted"
    }
  }'
```

- Get your notes to find ID of note you just created
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

- Delete your own note (should work)
```shell
curl -X DELETE "http://localhost:1337/api/notes/14" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

- Try to delete a note that doesn't exist or doesn't belong to you
```bash
curl -X DELETE "http://localhost:1337/api/notes/999999" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```
 *Expected: "Note not found or you do not have permission to delete it"*

- Verify the note is gone
```bash
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```


### Update

- Controller
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

  // Override update to ensure users can only update their own notes
  async update(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to update a note');
    }

    const { id } = ctx.params;
    const { title, content } = ctx.request.body.data || ctx.request.body;

    // Check if note exists and belongs to user
    const existingNote = await strapi.entityService.findOne('api::note.note', id, {
      filters: { user: user.id },
      fields: ['isShared'], // Get current sharing status
    });

    if (!existingNote) {
      return ctx.notFound('Note not found or you do not have permission to update it');
    }

    // Update the note (don't allow changing user or isShared via update)
    const updatedNote = await strapi.entityService.update('api::note.note', id, {
      data: {
        title,
        content,
        // Explicitly maintain existing user and isShared values
        user: user.id,
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
}));
```

```shell
curl -X PUT "http://localhost:1337/api/notes/15" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated Title",
      "content": "Updated content"
    }
  }'
```


## Add Editors
- Sent when the note author grants edit access to someone.

### Create `Collaborator` Collection Type

- Start Strapi server
- Admin > Content Type Builder > `Collaborator`
- `note`: Relation - Note has many Collaborators
- `user`: Relation - User has many Collaborators
- `permission`: Enumeration - `editor`, `viewer`
- `addedBy`: Relation - User has many Collaborators as adder
- `addedAt`: DateTime


### Add Add Editors Endpoint

- Stop Strapi Server
- Create `src/api/note/routes/collaboration.js`
```js
// src/api/note/routes/collaboration.js
'use strict';

module.exports = {
  routes: [
    {
      method: 'POST',
      path: '/notes/:id/add-editors',
      handler: 'note.addEditors',
    },
    {
      method: 'GET',
      path: '/notes/:id/collaborators',
      handler: 'note.getCollaborators',
    },
    {
      method: 'DELETE',
      path: '/notes/:id/collaborators/:userId',
      handler: 'note.removeCollaborator',
    },
  ],
};
```

- Update controller
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
            //addedBy: user.id,
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
       // addedBy: { fields: ['username', 'email'] },
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

### Update Permissions

- Start Strapi server
- Settings > Users & Permissions > Roles > Authenticated. 
- For `Note`, Enable:
✅ addEditors (was inviteEditors)
✅ getCollaborators
✅ removeCollaborator
- For `Collaborator`, enable:
✅ find, findOne

- Test
- Add editors to a note
```shell
curl -X POST "http://localhost:1337/api/notes/1/add-editors" \
  -H "Authorization: Bearer $OWNER_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"emails": ["editor@example.com", "another@example.com"]}'
```

```shell
curl -X POST "http://localhost:1337/api/notes/1/add-editors" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"emails": ["craigsimakando@gmail.com"]}'
```

- Get collaborators
```shell
curl -X GET "http://localhost:1337/api/notes/1/collaborators" \
  -H "Authorization: Bearer $OWNER_JWT_TOKEN"
```

```shell
curl -X GET "http://localhost:1337/api/notes/1/collaborators" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

- Remove collaborators
```shell
curl -X DELETE "http://localhost:1337/api/notes/1/collaborators/USER_ID" \
  -H "Authorization: Bearer $OWNER_JWT_TOKEN"
```

```shell
curl -X DELETE "http://localhost:1337/api/notes/1/collaborators/USER_ID" \
  -H "Authorization: Bearer $OWNER_JWT_TOKEN"
```


## Request Edit Access
- Sent when someone requests edit access from the note author

### Add Route

- `request-access`
```js
// src/api/note/routes/collaboration.js
'use strict';

module.exports = {
  routes: [
    {
      method: 'POST',
      path: '/notes/:id/add-editors',
      handler: 'note.addEditors',
    },
    {
      method: 'POST',
      path: '/notes/:id/request-access',
      handler: 'note.requestAccess',
    },
    {
      method: 'GET',
      path: '/notes/:id/collaborators',
      handler: 'note.getCollaborators',
    },
    {
      method: 'DELETE',
      path: '/notes/:id/collaborators/:userId',
      handler: 'note.removeCollaborator',
    },
  ],
};
```

### Update Controller

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

  // Request edit access to a note
  async requestAccess(ctx) {
    const user = ctx.state.user;
    if (!user) {
      return ctx.unauthorized('You must be logged in to request access');
    }

    const { id } = ctx.params;

    // Get the note and its owner
    const note = await strapi.entityService.findOne('api::note.note', id, {
      populate: {
        user: { fields: ['email', 'username'] },
      },
    });

    if (!note) {
      return ctx.notFound('Note not found');
    }

    // Check if user is already the owner
    if (note.user.id === user.id) {
      return ctx.badRequest('You already own this note');
    }

    // Check if user is already a collaborator
    const existingCollaboration = await strapi.entityService.findMany('api::collaborator.collaborator', {
      filters: {
        note: id,
        user: user.id,
      },
    });

    if (existingCollaboration.length > 0) {
      return ctx.badRequest('You already have access to this note');
    }

    // Send request email to note owner
    const noteUrl = `${process.env.APP_URL || 'http://localhost:3000'}/notes/${id}`;
    
    await sendEmail({
      to: note.user.email,
      subject: `Access request for your note: ${note.title}`,
      html: `
        <p>Hello ${note.user.username || note.user.email},</p>
        <p><strong>${user.username || user.email}</strong> (${user.email}) has requested edit access to your note:</p>
        <p><strong>Note:</strong> ${note.title}</p>
        <p><a href="${noteUrl}">View Note</a></p>
        <p>If you want to grant access, you can add them as an editor through the application.</p>
        <br>
        <p>This is an automated message. Please do not reply to this email.</p>
      `,
    });

    ctx.body = {
      ok: true,
      message: 'Access request sent to note owner',
      noteTitle: note.title,
      owner: note.user.username || note.user.email,
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

- Start Strapi
- Update Permissions
- Settings > Users & Permissions > Roles > Authenticated, for `Note` collection, enable:
- ✅ requestAccess

- Test
```shell
curl -X POST "http://localhost:1337/api/notes/1/request-access" \
  -H "Authorization: Bearer $USER_JWT_TOKEN" \
  -H "Content-Type: application/json"
```

```shell
curl -X POST "http://localhost:1337/api/notes/4/request-access" \
  -H "Authorization: Bearer $USER2_JWT_TOKEN" \
  -H "Content-Type: application/json"
```



## Image uploads with Auth

- Strapi 5 project create called backend
- Create admin
- Start server
- Login
- Create collection called `Post` with `title`(text), `content`(Rich text), `image`(Media)
- Add entries using Content Manager
- Open all permissions `create`, `find`, `findOne`, `update`, `delete` for `Post` for Public user
- Open all permissions `find`, `findOne`, `upload`, `destroy` for `Upload` for Public user

- Test Read all posts
```shell
curl -X GET "http://localhost:1337/api/posts"
```

- Test Read all uploaded files
```shell
curl -X GET "http://localhost:1337/api/upload/files"
```

- Test Read one post
```shell
curl -X GET "http://localhost:1337/api/posts/1"
```

- Test Read one uploaded file
```shell
curl -X GET "http://localhost:1337/api/upload/files/1
```

- Test create file upload using curl
```shell
curl -X POST "http://localhost:1337/api/upload" \
  -F "files=@/path/to/your/image.jpg"
```

- Test destroy file upload using curl
```shell
curl -X DELETE "http://localhost:1337/api/upload/files/1"
```

- Test update file upload using curl
```shell
curl -X POST "http://localhost:1337/api/upload?id=1" \
  -F 'fileInfo={"name":"updated-filename","alternativeText":"Updated alt text description","caption":"Updated caption for the image"}'
```

### Test Create post using curl
- Upload image first and retrieve file ID
```shell
curl -X POST "http://localhost:1337/api/upload" \
  -F "files=@/path/to/your/image.jpg"
```

This returns a response like:
```json
[
  {
    "id": 1,
    "name": "image.jpg",
    "alternativeText": null,
    "caption": null,
    "width": 1920,
    "height": 1080,
    "url": "/uploads/image_abc123.jpg",
    ...
  }
]
```

Note file `id` from response and then include it in data object to create a post
```shell
curl -X POST "http://localhost:1337/api/posts" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "My First Post",
      "content": "This is the content of my first post",
      "image": 1
    }
  }'
```

- Test Update post using curl
```shell
curl -X PUT "http://localhost:1337/api/posts/1" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated Post Title",
      "content": "This is the updated content"
    }
  }'
```

- Test Delete post using curl
```shell
curl -X DELETE "http://localhost:1337/api/posts/1"
```

- Test using create multiple file uploads
```shell
curl -X POST "http://localhost:1337/api/upload" \
  -F "files=@/path/to/image1.jpg" \
  -F "files=@/path/to/image2.jpg" \
  -F "files=@/path/to/image3.png"
```

- Test using create post with multiple file uploads
- Upload files first as above
```shell
curl -X POST "http://localhost:1337/api/posts" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Post with Multiple Images",
      "content": "This post has multiple images attached",
      "image": [1, 2, 3]
    }
  }'
```

- Test using curl destroy multiple file uploads
```shell
# Delete file 1
curl -X DELETE "http://localhost:1337/api/upload/files/1"

# Delete file 2
curl -X DELETE "http://localhost:1337/api/upload/files/2"

# Delete file 3
curl -X DELETE "http://localhost:1337/api/upload/files/3"
```
> *Note: Strapi does not support batch delete in a single request, so you need to separate calls for each file*

- Test using curl delete post with multiple file uploads
```shell
curl -X DELETE "http://localhost:1337/api/posts/1"
```
> *Note: This deletes the post but keeps the uploaded files in the media library. Files need to be deleted separately*

- Test using curl update multiple file uploads
```shell
# Update file 1
curl -X POST "http://localhost:1337/api/upload?id=1" \
  -F 'fileInfo={"alternativeText":"Updated alt text for image 1","name":"updated-image-1"}'

# Update file 2
curl -X POST "http://localhost:1337/api/upload?id=2" \
  -F 'fileInfo={"alternativeText":"Updated alt text for image 2","name":"updated-image-2"}'

# Update file 3
curl -X POST "http://localhost:1337/api/upload?id=3" \
  -F 'fileInfo={"alternativeText":"Updated alt text for image 3","name":"updated-image-3"}'
```

- Test using curl update post with multiple file uploads
```shell
curl -X PUT "http://localhost:1337/api/posts/1" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated Post Title with Multiple Images",
      "content": "Updated content with multiple images",
      "image": [1, 2, 3, 4]
    }
  }'
  ```



- User should only be able to access (find/findOne) images they own (i.e. they uploaded)
- Users should only be able to update file Info for images they own
- Users should only be able to create file uploads for their account
- Users should only be able to destroy files they own
<!-- - Custom endpoint to change image, it first checks if you own or have access to the post containing the image. Then allows you to update fileinfo (But editor cannot destroy the image, they can only change the image) -->

- Users should only be able to create posts with referenced image IDS that belong to the authenticated user. User cannot reference images that they don't own in their post.
- Users should only be able to update posts with referenced image IDs that belong to the authenticated user. User cannot update a post and reference an image that doesn't belong to them.
- When user deletes a post, the associated images need to be deleted as well
- If user has been given edit access to a post shared by post owner they are only allowed to change an image, they cannot delete an image or upload an image to the given post if there wasn't any image to begin with.
- If user deletes account, all their posts are deleted, the user info is deleted and the associated file uploads are deleted

#### Authentication & Authorization Edge Cases
- No access for unauthenticated users
- There is only one class of users who have signed up for an account
- The authentication system uses JWT via a Social provider. So if token has expired the I believe the response is automatically a 403

#### Post Ownership & Access Control
- No such thing as public posts
- Users should only be able to update posts they own or have granted editing access to. No user can delete a post they don't own even a post they have been give editing access to
- As for shared editing, only the owner grants access. A collection called "Collaborator" can be created with a one to many relation field to a Post and another one to many relation field to a User

#### File Reference Integrity
- No referencing of images within the content of posts, so this doesn't apply. Once an image is deleted, the post is updated to look as a post with no image
- What do you mean by orphaned image references?
- Once an image has been deleted, the post is updated as well to account for a lack of image. Once again, no image is referenced as a link within the content field. Think of the image as a cover image to help make sense of its purpose

#### Cascade Delete Scenarios
- Deleting an account, means all posts that the user owns are deleted. If user has been given edit access to posts, then that permission is revoked

#### Shared Editing Clarifications
- Editor can replace all images
- Editors can modify image metadata
- Thanks for the question with regards to uploading an image vs changing image. It seems I made a mistake. Therefore an editor can upload an image as well as delete an image to make this easier. However they can't delete a post of course

#### Data Consistency
- I agrree there needs to be validation that image IDs exist before allowing them to be referenced in posts
- As for concurrent operations, leave that for now unless you have some sort of simple solution

#### Additional Considerations
- Rate limiting can wait
- File size/type can wait
- Storage quota can wait


- Add image endpoint, remove image endpoint, change image endpoint is the same as add image





- Image upload
- Add media field called image to Notes collection
- Change `content` field from text to rich text
- Update controller to account for `image` field

- Pagination (Infinite Loading) (Use filter)

### Create Nuxt frontend

- Install Nuxt
```shell
mkdir frontend
```

```shell
npm create nuxt frontend
```

Answer the prompts as follows:

```
❯ Which package manager would you like to use?
npm
> Initialize git repository?
No
❯ Would you like to install any of the official modules?
No
```

Once all questions are answered, it will install all the dependencies. The next step is to navigate to the project folder and launch it.

```shell
cd frontend
```

<!--
Update `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  compatibilityDate: '2025-05-15',
  devtools: { enabled: true },
    vite: {
    server: {
      allowedHosts: true,
    },
  },
})
```
-->

```shell
npm run dev
```

Update `pages/index.vue`:
```vue
<template>
    <h1>Welcome to the Notes Sharing App</h1>
    <p><a href="http://localhost:1337/api/connect/github">Login</a> to access notes</p>
</template>
```

## What is this article about?

- Social Auth
- Notes Sharing App
- Strapi Backend
- Quill.js as text editor
- Nodemailer for emails
- Infinite scrolling

- Add PostgreSQL database
- Notes collection type
- Fields Text - title, Rich Text - content, JSON - Editors, Relations - User from users-permission Users have many notes
- Permissions - Authenticated User - Application - All boxes except `Count and Find`
- Permissions - Authenticated User - User - findONe and me
- Permissions - Public User - Application - find and findOne
- Permissions - Public User - Upload - find, destroy, findOne, upload
- Permissions - Public User - User - find, findOne and me

- Add Login providers (GitHub and Facebook) Strapi and Nuxt
- Home page (Welcome message, Description, Login link)
- User page (Create note, Notes displayed on User page, infinite scroll)
- Navigation component (App Title, Username, Logout, Signin)

- Integrate Quill editor
- Notes page - Create and Edit notes (Editor, preview, Add, Cancel)
- Store feature (Vuex, Pinia)
- Image uploads
- Host images on Cloudinary
- Email sharing (Users share notes, Request edit access and add editors using their emails provided user with email is a user in database) email-templates, pug
- Preview page
- Share component
- Copying Links (way for users to copy links) and share links
- Hosting on Strapi Cloud and on Vercel or Netlify

## Approach

- Notes taking app using Next.js SSR (Create notes, edit notes, delete notes, copy notes)
- Add Editor
- Add Infinite Scroll or Pagination
- Image Uploads
- Notes taking app using Next and Strapi (Notes saved in Strapi backend)
- Notes taking app using Next and Strapi with Auth
- Notes sharing app using Next and Strapi (Users share notes for others to view using email, Users request to edit notes via email, Editors are added via email)
- Hosting on Strapi Cloud and on Vercel or Netlify

- All localhost links should be inside backticks and not be clickable links
- Railway deployment guides (Wordpress with persistence, Strapi with persistence, Nuxt SSR, Nuxt SSG)
- Railway Community Engineer or Support Engineer Role
- Railway using Terraform, GraphQL API
- Look at Nuxt server routes, APIs and the use cases
- Look at building simple APIs using Node.js versus hardcoding the returns
- Nuxt SSR issues (Cookies, Session Storage, Query strings, useState)

## Track for Nuxt

- Install Nuxt
- Hello World HTML
- Hello World Nuxt
- Build
- Deploy
- Form Submission HTML (Simulation)
- Log Hello World to server console
- Log Hello World to server consolde for every form submission (API, Server Route)
- Log Form submission to server console

## Track for Nuxt API

- Install Nuxt
- Hello World API
- Hello JSON API
- Hello JSON file API
- Read data from External API using Nuxt API
- Post data to API endpoint using curl
