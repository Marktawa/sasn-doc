## Add Sharing: Phase 1

We will now implement the sharing functionality for this app. The app in its current state already has the ability to share notes. All the owner of a note has to do to share a note is simply send the link manually to another user. In other words, any authenticated user with a link to the note can view, edit or delete a note right in the frontend User Interface of the app.

To prove this we can grab the link to the note of one user and try to open it logged in as another user in the browser.

Make sure your Strapi server and Nuxt development server are running.

In one browser instance log in to the Notes app as one user. Visit one of the notes in the list and copy the URL to the note in the address bar.

Open another browser instance (one which doesn't use the login credentials of the first user) then log in to the Notes app as the second user. Once logged in, paste the URL of the note link you copied earlier. You should be able to view the note as predicted.
<!-- Add screenshot -->

Try editing the note as well. You should see your changes reflected, even though you don't own the note.
<!-- Add screenshot -->

## Add Sharing: Phase 2

In this second phase of implementing the sharing functionality we will do a few things. 

<!--
Phase 2. Add email. Test email. Add `share` route. Add `share` controller. Add email sending for `share` controller. This means that each time an author shares a note, the link to the note is shared to all the note app users. Nuxt UI. Add "Share" button in Note View page. "Are you sure you want to share" page. "Share" confirmation page. `share` Nuxt server route is created to request the `share` endpoint etc
-->

We will add email functionality to the Strapi server. Next, we will add a `share` route for sharing notes via email to other users. Then, a `share` method in the Notes controller will be created to implement the sharing of notes. In the frontend we will update the "View Note" page with "Share" button. This will enable the author to initiate the note sharing process.

### Add Email: Configure Brevo

[Sign in to your Brevo account](https://login.brevo.com/), and generate a new API key in the [API keys page](https://app.brevo.com/settings/keys/api).
<!-- Add Screenshot -->

In your Strapi install folder, add the `BREVO_API_KEY` by updating the `.env` file.

```
BREVO_API_KEY=your-brevo-api-key
```

Install the [Brevo SDK](https://www.npmjs.com/package/sib-api-v3-sdk)
```shell
npm install @getbrevo/brevo
```

### Add Email: Add A Brevo Service

In the root of your Strapi install folder, create a new file named `brevo.js` inside the `src/services` folder:

```shell
touch src/service/brevo.js
```

Add the following code to `src/service/brevo.js`:
```js
'use strict';

const Brevo = require('@getbrevo/brevo');

const api = new Brevo.TransactionalEmailsApi();
//Read from .env -> BREVO_API_KEY
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
    sendSmtpEmail.subjext = subject || 'Test email';
    sendSmtpEmail.htmlContent = html || '<p>Hello from Strapi + Brevo!</p>';

    return api.sendTransacEmail(sendSmtpEmail);
}

module.exports = { sendEmail };
```

Update your `.env` file in Strapi's install folder with the following variables:
```
SENDER_EMAIL=paul.zimba96@gmail.com
SENDER_NAME=Notes App
APP_URL=http://localhost:3000
```

- `SENDER_EMAIL`: This is the email you used to register your Brevo account
- `SENDER_NAME`: The name of the app. In this case "Notes App"
- `APP_URL`: This is the frontend URL to the Nuxt app

### Add Email: Test Email Service

Before integrating email sharing into the main app, let's verify that Brevo is configured correctly and can send emails.

#### Create Test Email Route

Create a `routes` folder inside `src/api/email`:
```shell
mkdir src/api/email/routes
```

Create a file named `email.js` inside the `src/api/email/routes` directory:
```shell
touch src/api/email/routes/email.js
```

Add the following code to `src/api/email/routes/email.js`:
```js
'use strict';

module.exports = {
    routes: [
        {
            method: 'POST',
            path: '/email/test',
            handler: 'email.test',
            config: {
                policies: [],
                middlewares: [],
            },
        },
    ],
};
```

#### Create Test Email Controller

Create a `controllers` folder inside `src/api/email`:
```shell
mkdir src/api/email/controllers
```

Create a file name `email.js` inside the `src/api/email/controllers` directory:
```shell
touch src/api/email/controllers/email.js
```

Add the following code to `src/api/email/controllers/email.js`:
```js
'use strict';

const { sendEmail } = require('../../../service/brevo');

module.exports = {
    async test(ctx) {
        const { email } = ctx.request.body;

        const result = await sendEmail({
            to: email,
            subject: 'Test Email from Strapi + Brevo',
            html: '<h1>Success</h1><p>Your Brevo Integration works</p>',
        });

        ctx.send({
            message: 'Test email sent successfully',
            result: result,
        });
    },
};
```

#### Test the Email Service

Restart your Strapi server, then use curl to test the email endpoint:
```shell
curl -X POST "http://localhost:1337/api/email/test" \
-H "Content-Type: appliation/json" \
-d '{"email": "your-email@example.com"}'
```

Replace `your-email@example.com` with a working email address whose inbox you have access to.

If everything is configured correctly, you should:

1. Receive a response like this:
```json
{ 
    "message": "Test email sent successfully", 
    "result": {...} 
}
```

2. Receive an email in your inbox with the subject "Test Email from Strapi + Brevo"
<!-- Add Screenshot -->

#### Troubleshooting

If you don't receive the email:
- Check your spam/junk folder
- Verify your `BREVO_API_KEY` is correct in the `.env` file
- Verify your `SENDER_EMAIL` is configured in Brevo
- Check the Strapi server logs for any error messages
- Ensure you restarted the Strapi server after adding the environment variables

Once you've confirmed the email is working, you can delete the test files:
```shell
rm -rf src/api/email
```

### Create `share` method in Notes controller

Now we need to create a custom `share` method in the Notes controller that will send an email to the other users of the Notes App. The email will contain the URL of the note being shared.

Open the `src/api/note/controllers/note.js` file in your code editor.

Add the `share` method to the controller:
```js
'use strict';
 
const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../service/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
    async find(ctx) {
        const user = ctx.state.user;

        return await strapi.documents('api::note.note').findMany({
            filters: { user: user.id },
        })
    },

    async share(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
        });

        const allUsers = await strapi.documents('plugin::users-permissions.user').findMany();
        const emails = allUsers.map(u => u.email);

        const noteUrl = `${process.env.APP_URL}/notes/${id}`;

        await sendEmail({
            to: emails,
            subject: `${user.username} shared a note with you`,
            html: `
                <h2>You've been shared a note!</h2>
                <p><strong>${user.username}</strong> has shared a note with you on Notes App.</o>
                <p><strong>Note Title:</strong> ${note.title}</p>
                <p><a href=${noteUrl}">Click here to view the note</a></p>
                `,
        });

        ctx.send({
            message: 'Note shared successfully with all users',
            noteUrl,
        });
    },
}));
```

#### How the Share Method Works

The method gets the extracts the Note ID from the URL parameters. It then uses the note's `documentId` to retrieve teh note's title. It then fetches all the users from the Users & Permissions Plugin, extracts the email addresses for each users into array. A full URL to the note is constructed using the `APP_URL` from the environment variables. Using the Brevo service, an email is then sent to the all the users of the Notes App. The email will contain the sharer's username, the note's title, and a clickable link to view the note.

### Create `share` route

Now we need to create a custom route that maps to the `share` method we just created.

Create a new file named `custom.js` inside the `src/api/note/routes` directory:
```shell
touch src/api/note/routes/custom.js
```

Add the following code to `src/api/note/routes/custom.js`:
```js
'use strict';

module.exports = {
    routes: [
        {
            method: 'POST',
            path: '/notes/:id/share',
            handler: '/note.share',
            config: {
                policies: [],
                middlewares: [],
            },
        },
    ],
};
```

This configuration:
- Creates a custom `POST` route at `/notes/:id/share`
- Maps the route to the `share` method in the Notes controller
- Strapi will automatically load this custom route alongside the default CRUD routes

### Test `share` endpoint

Load the `documentId` of one of your notes into your `.secret` file
```
NOTE_ID=<your-note's-documentId>
```

Load the variables in `.secret` into your shell's enviroment variables
```shell
export $(cat .secret | xargs)
```

Restart your Strapi server, then test the share endpoint with curl:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/share" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

If successful, you should:

1. Receive a response like:
```json
{
    "message": "Note shared successfully with all users",
    "noteUrl": "http://localhost:3000/notes/NOTE_ID"
}
```

2. You should receive an email with the note details and a clickable link
<!-- Add Screenshot -->

### Update Individual Note Page with Share Button

Now we'll add a "Share" button to the individual note page that allows the owner to share their note with all other users.

Update `pages/notes/[id].vue` with the following code:
```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`/api/notes/${route.params.id}`)
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <div>
    <p>Logged in as: <strong>{{ user.username }}</strong></p>
    <a href="/api/auth/logout">
      <button>Logout</button>
    </a>
  </div>
  
  <h2>{{ note.data.title }}</h2>
  <p>{{ note.data.content }}</p>
  <br>
  <NuxtLink :to="`/notes/edit?id=${route.params.id}`">
    <button>Edit Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/share?id=${route.params.id}`">
    <button>Share Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

The "Share Note" button is now positioned between the "Edit Note" and "Delete Note" buttons, allowing users to easily share their notes with all other users of the app.

### Create Share Note Page

Inside the `pages/notes` directory, create a new file named `share.vue`:
```shell
touch pages/notes/share.vue
```

Add the following code to `pages/notes/share.vue`:
```vue
<script setup>
  const route = useRoute()
  const noteId = route.query.id
  const { data: note } = await useFetch(`/api/notes/${noteId}`)
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>Share Note</h2>
  
  <h3>{{ note.data.title }}</h3>
  <p>{{ note.data.content }}</p>
  
  <p>Are you sure you want to share this note with all other users?</p>
  
  <form action="/api/notes/share" method="POST">
    <input type="hidden" name="noteId" :value="noteId" />
    
    <button type="submit">Yes, Share Note</button>
  </form>
  
  <br>
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Cancel</button>
  </NuxtLink>
</template>
```

This page displays:
- The note's title and content for confirmation
- A message asking the user to confirm they want to share with all other users
- A "Yes, Share Note" button that submits the form
- A "Cancel" button that returns to the individual note page without sharing

### Create Nuxt Server Route for Share

Create a file named `share.post.js` inside the `server/api/notes` directory:
```shell
touch server/api/notes/share.post.js
```

Add the following code to `server/api/notes/share.post.js`:
```js
export default defineEventHandler(async (event) => {
  // Parse incoming form data
  const body = await readBody(event)
  const { noteId } = body

  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Send share request to Strapi
  const response = await $fetch(`http://localhost:1337/api/notes/${noteId}/share`, {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json',
    }
  })

  // Redirect to success page
  return sendRedirect(event, `/notes/shared?success=1&id=${noteId}`)
})
```

#### How This Works

1. User confirms they want to share the note on the share confirmation page
2. Form submits to `/api/notes/share` (our Nuxt server route)
3. Server route extracts the `noteId` from the form data
4. Server route reads the JWT from the HTTP-only cookie
5. Server route makes an authenticated POST request to Strapi's custom share endpoint
6. Strapi sends emails to all users with the note link
7. Server redirects to the success page with the share status

### Create Share Confirmation Page

Create a file named `shared.vue` inside the `pages/notes` directory:
```shell
touch pages/notes/shared.vue
```

Add the following code to `pages/notes/shared.vue`:
```vue
<script setup>
  const route = useRoute()
  const isSuccess = route.query.success === '1'
  const noteId = route.query.id
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>Note Shared Successfully!</h2>
  <p>All users have been sent an email with a link to your note.</p>
    
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Back to Note</button>
  </NuxtLink>
    
  <NuxtLink to="/notes">
    <button>Back to Notes List</button>
  </NuxtLink>
</template>
```

This page confirms that emails were sent to all the users and provides navigation options.

### Test Note Sharing Flow

Make sure both your Strapi and Nuxt servers are running

Visit `http://localhost:3000/notes` while logged in

Click "View Note" on any of your notes

You should see a "Share Note" button between the "Edit Note" and "Delete Note" buttons
<!-- Add Screenshot -->

Click "Share Note"

You should see the note's title and content with the confirmation message "Are you sure you want to share this note with all other users?"
<!-- Add Screenshot -->

Click "Yes, Share Note"

You should be redirected to the success page showing "Note Shared Successfully!"
<!-- Add Screenshot -->

Check your email inbox. You should each have received an email with:
   - Subject: "[Your username] shared a note with you"
   - The note's title
   - A clickable link to view the note
<!-- Add Screenshot -->

Click the link in the email - you should be able to view the shared note
<!-- Add Screenshot -->

The note sharing flow is now complete, allowing any authenticated user to share their notes with all other users of the application via email.

## Add Sharing: Phase 3

In this third phase of implementing the sharing functionality, we will add the "Request To Edit" feature. This allows users who have access to a shared note to request edit permissions from the note's author. When a user requests to edit, an email will be sent to the author with the requester's username and a link to the note.

### Create `request-edit` method in Notes controller

Now we need to create a custom `request-edit` method in the Notes controller that will send an email to the note's author when another user requests to edit their note.

Open the `src/api/note/controllers/note.js` file in your code editor.

Add the `requestEdit` method to the controller:
```js
'use strict';
 
const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../service/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
    async find(ctx) {
        const user = ctx.state.user;

        return await strapi.documents('api::note.note').findMany({
            filters: { user: user.id },
        })
    },

    async share(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
        });

        const allUsers = await strapi.documents('plugin::users-permissions.user').findMany();
        const emails = allUsers.map(u => u.email);

        const noteUrl = `${process.env.APP_URL}/notes/${id}`;

        await sendEmail({
            to: emails,
            subject: `${user.username} shared a note with you`,
            html: `
                <h2>You've been shared a note!</h2>
                <p><strong>${user.username}</strong> has shared a note with you on Notes App.</o>
                <p><strong>Note Title:</strong> ${note.title}</p>
                <p><a href=${noteUrl}">Click here to view the note</a></p>
                `,
        });

        ctx.send({
            message: 'Note shared successfully with all users',
            noteUrl,
        });
    },

    async requestEdit(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
            populate: ['user'],
        });

        const authorEmail = note.user.email;

        const noteUrl = `${process.env.APP_URL}/notes/${id}`;

        await sendEmail({
            to: authorEmail,
        subject: `$user.username} request to edit your note`
        html: `
            <h2>Edit Request</h2>
            <p><strong>${user.username}</strong> (${user.email} has requested permission to edit your note.</p>
            <p><strong>Note Title:</strong> ${note.title}</p>
            <p><a href="${noteUrl}">Click here to view the note</a></p>
            `
        });

        ctx.send({
            message: 'Edit request sent successfully to the note author'
        });
    },
}));
```

#### How the Request Edit Method Works

The `requestEdit` method gets the note `documentId` from the URL parameters. It retrieves the note and populates the author/user relationship to access the author's details. The author's email address is extracted from the populated user data. Then the note's link i.e. the full URL to the note using the `APP_URL` from environment variables is constructed. Next using the Brevo service, an email is sent to the note's author containing:
   - The requester's username and email
   - The note's title
   - A clickable link to view the note
A success message back to the client after the email is sent.

### Create `request-edit` route

Now we need to create a custom route that maps to the `requestEdit` method we just created.

Open the `src/api/note/routes/custom.js` file in your code editor.

Add the request-edit route to the routes array:
```js
'use strict';

module.exports = {
    routes: [
        {
            method: 'POST',
            path: '/notes/:id/share',
            handler: 'note.share',
            config: {
                policies: [],
                middlewares: [],
            },
        },
        {
            method: 'POST',
            path: '/notes/:id/request-edit',
            handler: 'note.requestEdit',
            config: {
                policies: [],
                middlewares: [],
            },
        },
    ],
};
```

This configuration:
- Adds a custom POST route at `/notes/:id/request-edit`
- Maps the route to the `requestEdit` method in the Notes controller
- Works alongside the existing share route

### Test Request Edit Endpoint

Restart your Strapi server, then test the request-edit endpoint with curl:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/request-edit" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $GITHUB_JWT_TOKEN_2"
```

In this case we used the JWT token of a user who is not the author to request to edit a note

If successful, you should:

Receive a response like:
```json
{
    "message": "Edit request sent successfully to the note author",
    "noteUrl": "http://localhost:3000/notes/NOTE_ID"
}
```

The note's author should receive an email with:
   - Subject: "[Requester's username] requests to edit your note"
   - The requester's username and email address
   - The note's title
   - A clickable link to view the note
<!-- Add Screenshot -->

### Update Individual Note Page with Request Edit Button

Now we'll add a "Request Edit" button to the individual note page that allows users to request edit permission from the note's author.

Update `pages/notes/[id].vue` with the following code:
```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`/api/notes/${route.params.id}`)
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>{{ note.data.title }}</h2>
  <p>{{ note.data.content }}</p>
  <br>
  <NuxtLink :to="`/notes/edit?id=${route.params.id}`">
    <button>Edit Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/share?id=${route.params.id}`">
    <button>Share Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/request-edit?id=${route.params.id}`">
    <button>Request Edit</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

The "Request Edit" button is now positioned between the "Share Note" and "Delete Note" buttons, allowing users to request edit permission from the note's author.

### Create Request Edit Confirmation Page

Inside the `pages/notes` directory, create a new file named `request-edit.vue`:
```shell
touch pages/notes/request-edit.vue
```

Add the following code to `pages/notes/request-edit.vue`:
```vue
<script setup>
  const route = useRoute()
  const noteId = route.query.id
  const { data: note } = await useFetch(`/api/notes/${noteId}`)
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>Request Edit Permission</h2>
  
  <h3>{{ note.data.title }}</h3>
  <p>{{ note.data.content }}</p>
  
  <p>By clicking submit, your request will be sent to the author.</p>
  
  <form action="/api/notes/request-edit" method="POST">
    <input type="hidden" name="noteId" :value="noteId" />
    
    <button type="submit">Submit Request</button>
  </form>
  
  <br>
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Cancel</button>
  </NuxtLink>
</template>
```

This page displays:
- The note's title and content for reference
- A message informing the user that their request will be sent to the author
- A "Submit Request" button that submits the form
- A "Cancel" button that returns to the individual note page without sending a request

### Create Nuxt Server Route for Request Edit

Create a file named `request-edit.post.js` inside the `server/api/notes` directory:
```shell
touch server/api/notes/request-edit.post.js
```

Add the following code to `server/api/notes/request-edit.post.js`:
```js
export default defineEventHandler(async (event) => {
  // Parse incoming form data
  const body = await readBody(event)
  const { noteId } = body

  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Send request-edit request to Strapi
  const response = await $fetch(`http://localhost:1337/api/notes/${noteId}/request-edit`, {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json',
    }
  })

  // Redirect to success page
  return sendRedirect(event, `/notes/request-edit-success?success=1&id=${noteId}`)
})
```

#### How This Works

User confirms they want to request edit permission on the confirmation page. The form submits to the `api/notes/request-edit` endpoint (our Nuxt server route). The server route extracts the `noteId` (`documentId`) from the form data. The server route reads the JWT from the HTTP-only cookie. Then makes an authenticated POST request to Strapi's custom `request-edit` endpoint. The Strapi receives the request and sends an email to the note's author with the requester's details. The Nuxt server reads Strapi's response and redirects the user to the success page with the request status.

### Create Request Edit Success Page

Create a file named `request-edit-success.vue` inside the `pages/notes` directory:
```shell
touch pages/notes/request-edit-success.vue
```

Add the following code to `pages/notes/request-edit-success.vue`:
```vue
<script setup>
  const route = useRoute()
  const isSuccess = route.query.success === '1'
  const noteId = route.query.id
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>Edit Request Sent Successfully!</h2>
  <p>Your request has been sent to the note's author.</p>
    
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Back to Note</button>
  </NuxtLink>
    
  <NuxtLink to="/notes">
    <button>Back to Notes List</button>
  </NuxtLink>
</template>
```

This page handles confirms that the edit request was sent to the note's author and provides navigation options.

### Test Request Edit Flow

Make sure both your Strapi and Nuxt servers are running.

Log in as the first user and share one of their notes.

In another browser window, using the email account for the second user, open the mailbox and view the email. Copy the URL for the shared note.
<!-- Add Screenshot -->

Paste the note URL into the address bar to view the shared note.

You should see a "Request Edit" button between the "Share Note" and "Delete Note" buttons.
<!-- Add Screenshot -->

Click "Request Edit".

You should see the note's title and content with the message "By clicking submit, your request will be sent to the author."
<!-- Add Screenshot -->

Click "Submit Request".

You should be redirected to the success page showing "Edit Request Sent Successfully!".
<!-- Add Screenshot -->

Check the email inbox of the note's author (the first user) - they should have received an email with:
    - Subject: "[Second user's username] requests to edit your note"
    - The requester's username and email address
    - The note's title
    - A clickable link to view the note
<!-- Add Screenshot -->

## Add Sharing: Phase 4

In this fourth phase of implementing the sharing functionality, we will add the "Add Editor" feature. This allows note authors to add other users as editors to their notes. When an author receives an edit request email, they can visit the note and add that user (or any other user) as an editor by providing their email address. The newly added editor will receive a confirmation email with a link to the note.

### Create `addEditor` method in Notes controller

Now we need to create a custom `addEditor` method in the Notes controller that will send a confirmation email to the user being added as an editor.

Open the `src/api/note/controllers/note.js` file in your code editor.

Add the `addEditor` method to the controller:
```js
'use strict';
 
const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../service/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
    async find(ctx) {
        const user = ctx.state.user;

        return await strapi.documents('api::note.note').findMany({
            filters: { user: user.id },
        });
    },

    async share(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        // Fetch the note
        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
        });

        // Get all users
        const allUsers = await strapi.documents('plugin::users-permissions.user').findMany();

        // Extract email addresses
        const recipientEmails = allUsers.map(u => u.email);

        // Build the shareable link
        const noteUrl = `${process.env.APP_URL}/notes/${id}`;

        // Send email to all users
        await sendEmail({
            to: recipientEmails,
            subject: `${user.username} shared a note with you`,
            html: `
                <h2>You've been shared a note!</h2>
                <p><strong>${user.username}</strong> has shared a note with you on Notes App.</p>
                <p><strong>Note Title:</strong> ${note.title}</p>
                <p><a href="${noteUrl}">Click here to view the note</a></p>
            `,
        });

        ctx.send({
            message: 'Note shared successfully with all users',
            noteUrl,
        });
    },

    async requestEdit(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        // Fetch the note with author information
        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
            populate: ['user'],
        });

        // Get the author's email
        const authorEmail = note.user.email;

        // Build the note link
        const noteUrl = `${process.env.APP_URL}/notes/${id}`;

        // Send email to the note author
        await sendEmail({
            to: authorEmail,
            subject: `${user.username} requests to edit your note`,
            html: `
                <h2>Edit Request</h2>
                <p><strong>${user.username}</strong> (${user.email}) has requested permission to edit your note.</p>
                <p><strong>Note Title:</strong> ${note.title}</p>
                <p><a href="${noteUrl}">Click here to view the note</a></p>
            `,
        });

        ctx.send({
            message: 'Edit request sent successfully to the note author',
            noteUrl,
        });
    },

    async addEditor(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;
        const { email } = ctx.request.body;

        // Fetch the note
        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
        });

        // Build the note link
        const noteUrl = `${process.env.APP_URL}/notes/${id}`;

        // Send confirmation email to the new editor
        await sendEmail({
            to: email,
            subject: `You've been added as an editor`,
            html: `
                <h2>Editor Access Granted</h2>
                <p><strong>${user.username}</strong> has added you as an editor to their note.</p>
                <p><strong>Note Title:</strong> ${note.title}</p>
                <p>You can now edit this note.</p>
                <p><a href="${noteUrl}">Click here to view and edit the note</a></p>
            `,
        });

        ctx.send({
            message: 'Editor added successfully',
            email,
            noteUrl,
        });
    },
}));
```

#### How the Add Editor Method Works

The `addEditor` method first verifies the user is authenticated via `ctx.state.user`. It then gets the note ID(`documentId`) from the URL parameters and the editor's email from the request body. It uses the note ID to retrieve the note and get its title for the email. A link to the note is constructed using the `APP_URL` from environment variables. Using the Brevo service, a confirmation email is sent to the new editor containing:
   - The note author's username
   - The note's title
   - A message confirming editor access
   - A clickable link to view and edit the note
Finally, a success message is sent back to the client with the editor's email and note URL.

### Create `add-editor` route

Now we need to create a custom route that maps to the `addEditor` method we just created.

Open the `src/api/note/routes/custom.js` file in your code editor.

Add the add-editor route to the routes array:
```js
'use strict';

module.exports = {
    routes: [
        {
            method: 'POST',
            path: '/notes/:id/share',
            handler: 'note.share',
            config: {
                policies: [],
                middlewares: [],
            },
        },
        {
            method: 'POST',
            path: '/notes/:id/request-edit',
            handler: 'note.requestEdit',
            config: {
                policies: [],
                middlewares: [],
            },
        },
        {
            method: 'POST',
            path: '/notes/:id/add-editor',
            handler: 'note.addEditor',
            config: {
                policies: [],
                middlewares: [],
            },
        },
    ],
};
```

This configuration:
- Adds a custom POST route at `/notes/:id/add-editor`
- Maps the route to the `addEditor` method in the Notes controller
- Works alongside the existing share and request-edit routes

### Test Add Editor Endpoint

Restart your Strapi server, then test the add-editor endpoint with curl:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/add-editor" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
-d '{"email": "editor@example.com"}'
```

In this case we used the JWT token of the author of the note as they are the ones who can add an editor.

Replace `editor@example.com` with the email address of the user you want to add as an editor.

If successful, you should:

1. Receive a response like:
```json
{
    "message": "Editor added successfully",
    "email": "editor@example.com",
    "noteUrl": "http://localhost:3000/notes/NOTE_ID"
}
```

2. The new editor should receive an email with:
   - Subject: "You've been added as an editor"
   - The note author's username
   - The note's title
   - A confirmation message about editor access
   - A clickable link to view and edit the note
<!-- Add Screenshot -->

### Update Individual Note Page with Add Editor Button

Now we'll add an "Add Editor" button to the individual note page that allows note authors to add other users as editors to their notes.

Update `pages/notes/[id].vue` with the following code:

```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`/api/notes/${route.params.id}`)
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>{{ note.data.title }}</h2>
  <p>{{ note.data.content }}</p>
  <br>
  <NuxtLink :to="`/notes/edit?id=${route.params.id}`">
    <button>Edit Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/share?id=${route.params.id}`">
    <button>Share Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/request-edit?id=${route.params.id}`">
    <button>Request Edit</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/add-editor?id=${route.params.id}`">
    <button>Add Editor</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

The "Add Editor" button is now positioned between the "Request Edit" and "Delete Note" buttons, allowing note authors to grant edit access to other users.

### Create Add Editor Page

Inside the `pages/notes` directory, create a new file named `add-editor.vue`:
```shell
touch pages/notes/add-editor.vue
```

Add the following code to `pages/notes/add-editor.vue`:
```vue
<script setup>
  const route = useRoute()
  const noteId = route.query.id
  const { data: note } = await useFetch(`/api/notes/${noteId}`)
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>
  
  <h2>Add Editor</h2>
  
  <h3>{{ note.data.title }}</h3>
  <p>{{ note.data.content }}</p>
  
  <p>Enter the email address of the user you want to add as an editor:</p>
  
  <form action="/api/notes/add-editor" method="POST">
    <input type="hidden" name="noteId" :value="noteId" />
    
    <div>
      <label for="email">Email Address:</label>
      <input type="email" name="email" id="email" required />
    </div>
    
    <button type="submit">Submit</button>
  </form>
  
  <br>
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Cancel</button>
  </NuxtLink>
</template>
```

This page displays:
- The note's title and content for reference
- A prompt asking for the email address of the user to be added as an editor
- An email input field for entering the editor's email address
- A "Submit" button that submits the form
- A "Cancel" button that returns to the individual note page without adding an editor

### Create Nuxt Server Route for Add Editor

Create a file named `add-editor.post.js` inside the `server/api/notes` directory:
```shell
touch server/api/notes/add-editor.post.js
```

Add the following code to `server/api/notes/add-editor.post.js`:
```js
export default defineEventHandler(async (event) => {
    // Parse incoming form data
    const body = await readBody(event)
    const { noteId, email } = body

    // Get the JWT from the HTTP-only cookie
    const token = getCookie(event, 'auth_token')

    const response = await $fetch(`http://localhost:1337/api/notes/${noteId}/add-editor`, {
        method: 'POST',
        headers: {
            Authorization: `Bearer ${token}`,
            'Content-Type': 'application/json',
        },
        body: {
            email: email
        }
    })

    return sendRedirect(event, `/notes/add-editor?success=1&id=${noteId}`)
})
```

The user enters an email address on the `/notes/add-editor` page. The form is submitted to the Nuxt server route at `/api/notes/add-editor`. The server route will extract the `noteId` (`documentId`) and `email` from the form data. The server route will read the JWT from the HTTP-only cookie. Then an authenticated POST request is made to Strapi's custom `add-editor` endpoint with the email. Strapi sends a confirmation email to the new editor with the note link. On receiving the response from the Strapi server, the Nuxt server will execute a redirect to the success page confirm that an editor has been added.

### Create Add Editor Success Page

Create a file named `add-editor-success.vue` inside the `pages/notes` directory:
```shell
touch pages/notes/add-editor-success.vue
```

Add the following code to `pages/notes/add-editor-success.vue`:
```vue
<script setup>
  const route = useRoute()
  const isSuccess = route.query.success === '1'
  const noteId = route.query.id
  const { data: user } = await useFetch('/api/auth/me')
</script>

<template>
  <h1>Notes App</h1>
  
  <!-- User info and logout -->
  <p>Logged in as: <strong>{{ user.username }}</strong></p>
  <a href="/api/auth/logout">
    <button>Logout</button>
  </a>

  <h2>Editor Added Successfully!</h2>
  <p>The user has been added as an editor and will receive a confirmation email.</p>
    
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Back to Note</button>
  </NuxtLink>
    
  <NuxtLink to="/notes">
    <button>Back to Notes List</button>
  </NuxtLink>
</template>
```

This page handles both success and failure states:
- **Success State**: Confirms that the editor was added successfully and that they will receive a confirmation email with navigation options
- **Failure State**: Displays an error message and offers the option to try adding the editor again or return to the notes list

### Test Add Editor Flow

Make sure both your Strapi and Nuxt servers are running.

Log in as the first user (the note author).

Click "View Note" on any of your notes.

You should see an "Add Editor" button positioned between the "Request Edit" and "Delete Note" buttons.
<!-- Add Screenshot -->

Click "Add Editor".

You should be directed to the form page asking for an email address.
<!-- Add Screenshot -->

Enter the email address of the second user (the one you want to grant edit access to).

Click "Submit".

You should be redirected to the success page showing "Editor Added Successfully!".
<!-- Add Screenshot -->

Check the email inbox of the second user. They should have received an email with:
   - Subject: "You've been added as an editor"
   - The note author's username
   - The note's title
   - A clickable link to view and edit the note
<!-- Add Screenshot -->