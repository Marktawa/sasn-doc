## User Authentication using Social Login Providers: GitHub Authentication

### Register OAuth App in GitHub

1. Visit the OAuth Apps list page [https://github.com/settings/developers](https://github.com/settings/developers)
2. Click on **New OAuth App** button
3. Fill the information:
   - **Application name**: `Notes Sharing App`
   - **Homepage URL**: `http://localhost:1337/`
   - **Application description**: `Strapi notes sharing application`
   - **Authorization callback URL**: `http://localhost:1337/api/connect/github/callback`
4. Click **Register application** and you will be redirected to the OAuth application settings page.
5. Click **Generate a new client secret** and copy it. Copy the **Client ID** as well.

![GitHub OAuth Settings](https://res.cloudinary.com/craigsims808/image/upload/v1750258727/strapi/sasn/github-oauth-settings-live_abbowv.png)

### Configuring the GitHub Provider in Strapi

1. Open your Strapi application at `http://localhost:1337`
2. Click on Settings
3. Under settings, click on Providers
4. Click on the **GitHub** provider
5. Fill the information:
   - **Enable**: `ON`
   - **Client ID**: `YOUR_GITHUB_CLIENT_ID`
   - **Client Secret**: `YOUR_GITHUB_CLIENT_SECRET`
   - **The redirect URL to your front-end app**: `http://localhost:3000/connect/github`
   -   **Enable**: `TRUE`
6. Click **Save**

![GitHub provider settings](https://res.cloudinary.com/craigsims808/image/upload/v1750343147/strapi/sasn/github-provider-settings_moeeki.png)

### Configure Public User Permissions

Go to **Settings** then **Users & Permissions** then **Roles** then **Public**.

Remove all the Public User Permissions for the Notes collection (`find`, `findOne`, `create`, `delete`, and `update`).
![Public User Permissions Removed]()
<!-- Add Screenshot -->

### Configure Authenticated User Permissions

Go to **Settings** then **Users & Permissions** then **Roles** then **Authenticated**.

Select the **Notes** collection and enable the `find`, `findOne`, `create`, `delete`, and `update` permissions for the Authenticated User then click **Save**.
![Authenticated User Permissions Updated]()

### Get Temporary Code

Visit the `http://localhost:1337/api/connect/github` in your browser.

GitHub will prompt for login and authorization. 
![GitHub prompt for authorization](https://res.cloudinary.com/craigsims808/image/upload/v1765351776/strapi/sasn/16-github-user-login-2_gvcaw4.png)

After approval GitHub redirects to `http://localhost:3000/connect/github?access_token=GITHUB_CODE`

Nothing will display as page does not exist. However you can copy the code given by GitHub in the URL of the response.

### Test to see if User is Added to User collection

Use the code given to add a user to the database and get a JWT for the user by the running the following command in your terminal:

```shell
curl -X GET http://localhost:1337/api/auth/github/callback?access_token=GITHUB_CODE > test6.json
```

The response you will get is as below. It contains the `jwt` string as well as the user details retrieved from GitHub.
```json
{
  "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MiwiaWF0IjoxNzY1MzE3MzYyLCJleHAiOjE3Njc5MDkzNjJ9.3ssHu9rhve4yhOm3pn2QnpOG8zddSKvAHdIX0rmK75w",
  "user": {
    "id": 2,
    "documentId": "qhgtn1vzzgkm1vggepb7p20b",
    "username": "Marktawa",
    "email": "markmunyaka@gmail.com",
    "provider": "github",
    "confirmed": true,
    "blocked": false,
    "createdAt": "2025-12-09T21:56:02.372Z",
    "updatedAt": "2025-12-09T21:56:02.372Z",
    "publishedAt": "2025-12-09T21:56:02.373Z"
  }
}
```

Open the **Users** collection in the **Content Manager** of your Strapi Dashboard. You should see the new user you created listed in the User collection.
![New user added to User collection](https://res.cloudinary.com/craigsims808/image/upload/v1765351976/strapi/sasn/17-user-listed-in-user-collection_qrqgig.png)

### Use JWT to Access Protected Endpoints

Copy the `jwt` from the response you got earlier in the terminal.
<!-- JWT:  -->

Create a file called `.secret` in your working directory
```shell
touch .secret
```

Add JWT code to `.secret`:
```
GITHUB_JWT_TOKEN=your-code
```

Load the token inside `.secret` into your shell:
```shell
export $(cat .secret | xargs)
```

### Test Token

Perform a `find` request as an Authenticated User:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

```json 
{
    "data": [
        {
            "id": 1,
            "documentId": "ia21j5a0o6muvwf0nvegce7d",
            "title": "1 First Note",
            "content": "This is content for the first note. It has been updated once.",
            "createdAt": "2025-12-02T14:14:11.679Z",
            "updatedAt": "2025-12-02T14:25:39.027Z",
            "publishedAt": "2025-12-02T14:25:39.024Z"
        },
        {
            "id": 2,
            "documentId": "oslromztui4d2eo5shehw5g2",
            "title": "2 Second Note",
            "content": "This is the content for the second note.",
            "createdAt": "2025-12-02T14:14:46.455Z",
            "updatedAt": "2025-12-02T14:14:46.455Z",
            "publishedAt": "2025-12-02T14:14:46.453Z"
        },
        {
            "id": 3,
            "documentId": "n32nsdoh01d6hqoq711qbpt1",
            "title": "3 Third Note",
            "content": "This is the content for the third note.",
            "createdAt": "2025-12-02T14:15:11.889Z",
            "updatedAt": "2025-12-02T14:15:11.889Z",
            "publishedAt": "2025-12-02T14:15:11.886Z"
        },
        {
            "id": 4,
            "documentId": "rfrc94r6v4iqqfdersj59vpd",
            "title": "4 Fourth Note",
            "content": "This is the content for the fourth note.",
            "createdAt": "2025-12-02T14:15:34.665Z",
            "updatedAt": "2025-12-02T14:15:34.665Z",
            "publishedAt": "2025-12-02T14:15:34.663Z"
        },
        {
            "id": 5,
            "documentId": "fl1d8z0xjso9qh0os36gceqq",
            "title": "5 Fifth Note",
            "content": "This is the content for the fifth note.",
            "createdAt": "2025-12-02T14:16:01.793Z",
            "updatedAt": "2025-12-02T14:16:01.793Z",
            "publishedAt": "2025-12-02T14:16:01.792Z"
        }
    ],
    "meta": {
        "pagination": {
            "page": 1,
            "pageSize": 25,
            "pageCount": 1,
            "total": 5
        }
    }
}
```

You should get a list of notes as seen above.

Perform a `find` request as a Public User:
```shell
curl -X GET "http://localhost:1337/api/notes"
```

You should get a `403` forbidden HTTP status code error. No list of notes appears.

<!-- TO DO -->
```json 
```

<!-- End of Latest Video -->

<!--
Beginning of New Video
- Nuxt, Strapi are running (Port forward Strapi)
- Check `.secret`, github provider, 
-->

### Add User from User Permissions Plugin with One to Many relation field

Make sure your Strapi server is running then log in your Strapi Admin Dashboard. Click **Content-Type Builder** and select the **Note** collection.

Click **Edit** to add a new field to the **Note** collection.

Add a `Relations` field whereby `User (from: user-permissions-user)`, then click on `Users have many notes`.
<!-- Add Screenshot -->

Update Notes collection entries. Both the User and Notes. 
<!-- Add Screenshot -->

For the demo to work, some notes will be assigned to one user in the Content Manager and the other notes assigned to the other user in the Content Manager.

### Add Second GitHub Authenticated User

Visit the `http://localhost:1337/api/connect/github` in your browser.

Copy the URL from the response you get.

<!--
URL: http://localhost:3000/connect/github?access_token=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&raw%5Baccess_token%5D=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&raw%5Bscope%5D=user&raw%5Btoken_type%5D=bearer
-->

Request user's JWT token:
```shell
curl -X GET http://localhost:1337/api/auth/github/callback?access_token=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 > test6.json
```

Get the response JSON:
```json
{
    "jwt": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MywiaWF0IjoxNzY1OTY1OTkzLCJleHAiOjE3Njg1NTc5OTN9.kA3FtTQZ0K2D1PIKlqmzKcEpfKN2-z2qFgjZPmts-nU",
    "user": {
        "id": 3,
        "documentId": "fvnrf50togtocsbfpzydir76",
        "username": "craigsims808",
        "email": "craigsimakando@gmail.com",
        "provider": "github",
        "confirmed": true,
        "blocked": false,
        "createdAt": "2025-12-17T10:06:33.724Z",
        "updatedAt": "2025-12-17T10:06:33.724Z",
        "publishedAt": "2025-12-17T10:06:33.724Z"
    }
}
```

Check to see if user has been added to the User collection in the Content Manager.
<!-- Add Screenshot -->

Add JWT code to `.secret`:
```
GITHUB_JWT_TOKEN_2=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MywiaWF0IjoxNzY1OTY1OTkzLCJleHAiOjE3Njg1NTc5OTN9.kA3FtTQZ0K2D1PIKlqmzKcEpfKN2-z2qFgjZPmts-nU
```

Load the token inside `.secret` into your shell:
```shell
export $(cat .secret | xargs)
```

### Test Token

Perform a `find` request as an Authenticated User:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN_2"
```

<!-- 
At least two users. Each user has their own notes 
markmunyaka@gmail.com
craigsimakando@gmail.com

USER added off screen
-->

### Add notes for newly added user

Visit the Content Manager in your Strapi Dashboard. Assign some notes to the newly created user you just added.
<!-- Add Screenshot -->

### Test `find`for Authenticated Users

If we tested the `find` method right now using one of the authenticated users, we will discover that the response will include all notes in the database. Even the ones that don't belong to the authenticated user. That is undesirable.

Perform a `find` request as an Authenticated User:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

<!-- Response -->
```json 
```

The response includes all notes not just the owner's notes.

### Override the `find` method in the controller

Strapi allows users to override the `find` method in the Notes controller and implement custom logic to fit your purposes. 

In this case we want the `find` method in the Notes controller to only just return notes belonging to the owner and no more.

Stop your Strapi server and open the `src/api/note/controllers/note.js` file in your code editor.

Replace the code with this:

```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;

module.exports = createCoreController('api::note.note', ({ strapi }) => ({ 
  async find(ctx) {
    const user = ctx.state.user;

    return await strapi.documents('api::note.note').findMany({
      filters: { user: user.id },
    });
  },
}));
```

### Test `find` for Authenticated User

Perform a `find` request as an Authenticated User:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

<!-- Response -->
```json 
[
    {
        "id": 1,
        "documentId": "ia21j5a0o6muvwf0nvegce7d",
        "title": "1 First Note",
        "content": "This is content for the first note. It has been updated once.",
        "createdAt": "2025-12-02T14:14:11.679Z",
        "updatedAt": "2025-12-17T10:10:01.037Z",
        "publishedAt": "2025-12-17T10:10:01.027Z",
        "locale": null
    },
    {
        "id": 3,
        "documentId": "n32nsdoh01d6hqoq711qbpt1",
        "title": "3 Third Note",
        "content": "This is the content for the third note.",
        "createdAt": "2025-12-02T14:15:11.889Z",
        "updatedAt": "2025-12-17T10:10:56.458Z",
        "publishedAt": "2025-12-17T10:10:56.451Z",
        "locale": null
    },
    {
        "id": 5,
        "documentId": "fl1d8z0xjso9qh0os36gceqq",
        "title": "5 Fifth Note",
        "content": "This is the content for the fifth note.",
        "createdAt": "2025-12-02T14:16:01.793Z",
        "updatedAt": "2025-12-17T10:11:29.569Z",
        "publishedAt": "2025-12-17T10:11:29.557Z",
        "locale": null
    }
]
```
<!-- Add Response -->

The response should now include only the owner's notes.

For now we just overriding the `find` controller method is fine. As for others `findOne`, `delete`, `update`, and `create` we will override them when we implement error handling and security.

<!-- 
End of Video 
-->

## Add Authentication to Nuxt

### Update Notes Landing page with a Login Button

Replace the static "Click here to view your notes" link with a "Login with GitHub" button that initiates the authentication flow.

Update `pages/index.vue`:
```vue
<template>
  <h1>Notes App</h1>
  <p>Welcome to the Notes App</p>
  <a href="http://localhost:1337/api/connect/github">
    <button>Login with GitHub</button>
  </a>
</template>
```

What this does:

* Replaces the direct link to `/notes` with a GitHub authentication flow
* Points to Strapi's GitHub OAuth endpoint (http://localhost:1337/api/connect/github)
* When clicked, the user will:
  - Be redirected to GitHub to authorize your app
  - Be redirected back to Strapi after authorization
  - Strapi will generate a JWT token

**Test it:**

Run your development servers and visit `http://localhost:3000`. Click "Login with GitHub" and verify you're redirected to GitHub's authorization page.
<!-- Add Screenshot -->

### Handle OAuth Callback and Store JWT (Server-Side)

After the user authorizes your app on GitHub, Strapi redirects them back to your application with an `access_token`. We need to exchange this token for a Strapi JWT and user profile, then store the JWT in a secure HTTP-only cookie.

#### Update Strapi Redirect URL Configuration

First, ensure your Strapi GitHub provider is configured to redirect back to your Nuxt app after authentication.

In your Strapi admin panel:
1. Navigate to **Settings** → **Providers** → **GitHub**
2. Set the **Redirect URL** to: `http://localhost:3000/auth/callback`
<!-- Add Screenshot -->

This tells Strapi where to send the user after successful GitHub authentication.

#### Create Server-Side Auth Callback Handler

Create a new folder named `auth` inside the `server/api` folder:
```shell
mkdir -p server/api/auth
```

Create a file named `callback.get.js` inside the `server/api/auth` directory:
```shell
touch server/api/auth/callback.get.js
```

Add the following code to `server/api/auth/callback.get.js`:
```js
export default defineEventHandler(async (event) => {
  // Get the access_token from the query parameters sent by Strapi
  const query = getQuery(event)
  const accessToken = query.access_token

  // Exchange the access_token for the Strapi JWT and user profile
  const strapiResponse = await $fetch(
    `http://localhost:1337/api/auth/github/callback?access_token=${accessToken}`
  )

  // strapiResponse contains { jwt, user }
  const { jwt, user } = strapiResponse

  // Set the Strapi JWT as an HTTP-only cookie
  setCookie(event, 'auth_token', jwt, {
    httpOnly: true,
    secure: false,
    sameSite: 'lax',
    maxAge: 60 * 60 * 24 * 7,
    path: '/'
  })

  // Redirect to the notes page
  return sendRedirect(event, '/notes')
})
```

#### Understanding the Complete Login Flow

Let's break down the entire authentication flow:

1. User clicks "Login with GitHub" on `http://localhost:3000`
2. Frontend redirects to Strapi: `http://localhost:1337/api/connect/github`
3. Strapi redirects to GitHub login page where the user authorizes the app
4. GitHub redirects back to Strapi: `http://localhost:1337/api/connect/github/callback?code=abcdef`
5. Strapi exchanges the code for an access_token with GitHub
6. Strapi redirects to your Nuxt callback: `http://localhost:3000/auth/callback?access_token=xyz123`
7. Nuxt server handler receives the request and extracts the `access_token`
8. Nuxt server calls Strapi again: `http://localhost:1337/api/auth/github/callback?access_token=xyz123`
9. Strapi validates the token with GitHub, retrieves the user's profile, matches it with a Strapi user, and returns `{ jwt, user }`
10. Nuxt server stores the Strapi JWT in an HTTP-only cookie
11. Server redirects the user to `/notes`
12. All future requests to Strapi will include this JWT for authentication

#### Test the Server-Side Authentication Flow

1. Make sure both your Strapi and Nuxt servers are running
2. Visit `http://localhost:3000`
3. Click "Login with GitHub"
4. Authorize the application on GitHub
5. You should be redirected directly to `/notes`
<!-- Add Screenshot -->

To verify the JWT was stored:
- Open your browser's Developer Tools
- Go to **Application** → **Cookies** → `http://localhost:3000`
- You should see an `auth_token` cookie marked as `HttpOnly` containing your Strapi JWT
<!-- Add Screenshot -->

### Send Authentication Token with API Requests

Now that we have the Strapi JWT stored in an HTTP-only cookie, we need to update our Notes List page to send this token with every API request to Strapi. This will allow Strapi to identify the authenticated user and return only their notes.

#### Update Notes List Page to Include Authentication

Since we're using server-side rendering and the JWT is stored in an HTTP-only cookie, we need to use Nuxt's server routes to proxy our API requests. This allows us to:
- Read the HTTP-only cookie on the server
- Attach the JWT as a Bearer token in the Authorization header
- Forward the request to Strapi

#### Create Server Route for Fetching Notes

Create a file named `notes.get.js` inside the `server/api` directory:
```shell
touch server/api/notes.get.js
```

Add the following code to `server/api/notes.get.js`:
```js
export default defineEventHandler(async (event) => {
  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Fetch notes from Strapi with authentication
  const notes = await $fetch('http://localhost:1337/api/notes', {
    headers: {
      Authorization: `Bearer ${token}`
    }
  })

  return notes
})
```

#### Update Notes List Page

Update `pages/notes/index.vue` to use the new server route:
```vue
<script setup>
  const { data: notes } = await useFetch('/api/notes')
</script>

<template>
  <h1>Notes App</h1>
  <h2>Notes List</h2>
  
  <!-- List of existing notes -->
  <ul>
    <li v-for="note in notes.data" :key="note.id">
      <h3>{{ note.title }}</h3>
      <NuxtLink :to="`/notes/${note.documentId}`">
        <button>View Note</button>
      </NuxtLink>
    </li>
  </ul>

  <!-- Link to the Create Note page -->
  <NuxtLink to="/notes/create">
    <button>Create a New Note</button>
  </NuxtLink>
</template>
```

#### How This Works

1. User visits `/notes` after successful authentication
2. Server-side fetch calls `/api/notes` (our Nuxt server route)
3. Server route reads the JWT from the HTTP-only cookie
4. Server route makes authenticated request to Strapi at `http://localhost:1337/api/notes` with `Authorization: Bearer <JWT>`
5. Strapi's custom controller filters notes to only return those belonging to the authenticated user
6. Server returns the filtered notes to the page
7. Page renders with only the user's notes

#### Test Authenticated Notes List

1. Make sure both your Strapi and Nuxt servers are running
2. Visit `http://localhost:3000` and login with GitHub
3. After authentication, you should be redirected to `/notes`
4. You should now see **only your notes**, not notes from other users
<!-- Add Screenshot -->

### Update Individual Note Page for Authenticated Requests

Now we need to update the individual note view page to fetch note data using authenticated requests through our server routes.

#### Create Server Route for Fetching Individual Note

Create a file named `[id].get.js` inside the `server/api/notes` directory:
```shell
touch server/api/notes/[id].get.js
```

Add the following code to `server/api/notes/[id].get.js`:
```js
export default defineEventHandler(async (event) => {
  // Get the note ID from the URL parameter
  const id = getRouterParam(event, 'id')
  
  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Fetch the specific note from Strapi with authentication
  const note = await $fetch(`http://localhost:1337/api/notes/${id}`, {
    headers: {
      Authorization: `Bearer ${token}`
    }
  })

  return note
})
```

#### Update Individual Note Page

Update `pages/notes/[id].vue` to use the new server route:
```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`/api/notes/${route.params.id}`)
</script>

<template>
  <h1>Notes App</h1>
  <h2>{{ note.data.title }}</h2>
  <p>{{ note.data.content }}</p>
  <br>
  <NuxtLink :to="`/notes/edit?id=${route.params.id}`">
    <button>Edit Note</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

#### How This Works

1. User clicks "View Note" on a note from the notes list
2. Page requests `/api/notes/{documentId}` (our Nuxt server route)
3. Server route extracts the note ID from the URL parameter
4. Server route reads the JWT from the HTTP-only cookie
5. Server route makes authenticated request to Strapi with `Authorization: Bearer <JWT>`
6. Server returns the note data to the page
7. Page renders with the note's title and content

#### Test Authenticated Individual Note View

1. Visit `http://localhost:3000/notes` while logged in
2. Click "View Note" on any of your notes
3. You should see the note's title and content
<!-- Add Screenshot -->

### Update Create Note Flow for Authenticated Requests

Now we need to update the create note flow to send authenticated requests to Strapi. This ensures that new notes are automatically associated with the logged-in user.

#### Update Create Note Server Handler

Update `server/api/notes/create.post.js` to include the JWT token:
```js
export default defineEventHandler(async (event) => {
  // Read form data sent from the browser
  const body = await readBody(event)
  
  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Send data to Strapi with authentication
  const strapiResponse = await $fetch('http://localhost:1337/api/notes', {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`
    },
    body: {
      data: {
        title: body.title,
        content: body.content
      }
    }
  })

  // Redirect to success page
  const noteId = strapiResponse.data.documentId
  return sendRedirect(event, `/notes/created?success=1&id=${noteId}`)
})
```

#### How This Works

1. User fills out the create note form on `/notes/create`
2. Form submits to `/api/notes/create` (our Nuxt server route)
3. Server route reads the form data (title and content)
4. Server route reads the JWT from the HTTP-only cookie
5. Server route makes authenticated POST request to Strapi with `Authorization: Bearer <JWT>`
<!--6. Strapi's custom `create` controller automatically associates the note with the authenticated user (from `ctx.state.user`) -->
6. Server redirects to the success page with the new note's `documentId`

#### Test Authenticated Note Creation

1. Visit `http://localhost:3000/notes` while logged in
2. Click "Create a New Note"
3. Fill in the title and content fields
4. Click "Create Note"
<!-- Add Screenshot -->

5. You should be redirected to the success page
6. Click "View Created Note" to see your new note
7. The note should be automatically associated with your user account
<!-- Add Screenshot -->
8. Visit the Notes list page to confirm the note appears.

### Update Edit Note Flow for Authenticated Requests

Now we need to update the edit note flow to send authenticated requests to Strapi. This ensures that users can only edit their own notes.

#### Create Server Route for Fetching Note to Edit

First, we need to update the edit page to fetch the note data through an authenticated server route.

The edit page already uses `/api/notes/${noteId}` which we created earlier, so it will automatically fetch the note with authentication.

#### Update Edit Note Server Handler

Update `server/api/notes/update.post.js` to include the JWT token:
```js
export default defineEventHandler(async (event) => {
  // Parse incoming form data
  const body = await readBody(event)
  const { documentId, title, content } = body
  
  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Send PUT request to Strapi with authentication
  const strapiResponse = await $fetch(`http://localhost:1337/api/notes/${documentId}`, {
    method: 'PUT',
    headers: {
      Authorization: `Bearer ${token}`
    },
    body: {
      data: {
        title: title,
        content: content,
      }
    }
  })

  return sendRedirect(event, `/notes/edited?id=${documentId}`)
})
```

#### How This Works

1. User clicks "Edit Note" on an individual note page
2. Edit page fetches note data through `/api/notes/${noteId}` with authentication
3. Form is pre-filled with the note's current title and content
4. User modifies the note and clicks "Update Note"
5. Form submits to `/api/notes/update` (our Nuxt server route)
6. Server route reads the form data (`documentId`, `title`, `content`)
7. Server route reads the JWT from the HTTP-only cookie
8. Server route makes authenticated PUT request to Strapi with `Authorization: Bearer <JWT>`
9. Server redirects to the success page with the note's `documentId`.

#### Test Authenticated Note Update

1. Visit `http://localhost:3000/notes` while logged in
2. Click "View Note" on any of your notes
3. Click "Edit Note"
4. Modify the title and/or content
5. Click "Update Note"
<!-- Add Screenshot -->

6. You should be redirected to the success page
7. Click "View Edited Note" to see your updated note
<!-- Add Screenshot -->

### Update Delete Note Flow for Authenticated Requests

Now we need to update the delete note flow to send authenticated requests to Strapi. This ensures that users can only delete their own notes.

#### Update Delete Note Page to Fetch with Authentication

The delete page already uses `/api/notes/${noteId}` which we created earlier, so it will automatically fetch the note with authentication to display for confirmation.

#### Update Delete Note Server Handler

Update `server/api/notes/delete.post.js` to include the JWT token:
```js
export default defineEventHandler(async (event) => {
  // Parse incoming form data
  const body = await readBody(event)
  const { documentId } = body
  
  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Send DELETE request to Strapi with authentication
  await $fetch(`http://localhost:1337/api/notes/${documentId}`, {
    method: 'DELETE',
    headers: {
      Authorization: `Bearer ${token}`
    }
  })

  // Redirect to the "Deleted" confirmation page
  return sendRedirect(event, '/notes/deleted')
})
```

#### How This Works

1. User clicks "Delete Note" on an individual note page
2. Delete confirmation page fetches note data through `/api/notes/${noteId}` with authentication
3. Page displays the note with "Are you sure you want to delete this note?"
4. User clicks "Yes" to confirm deletion
5. Form submits to `/api/notes/delete` (our Nuxt server route)
6. Server route reads the form data (documentId)
7. Server route reads the JWT from the HTTP-only cookie
8. Server route makes authenticated DELETE request to Strapi with `Authorization: Bearer <JWT>`
9. Server redirects to the success page confirming deletion

#### Test Authenticated Note Deletion

1. Visit `http://localhost:3000/notes` while logged in
2. Click "View Note" on any of your notes
3. Click "Delete Note"
4. You should see the note's title and content with a confirmation message
<!-- Add Screenshot -->

5. Click "Yes" to confirm deletion
6. You should be redirected to the "Note deleted successfully" page 
<!-- Add Screenshot -->
7. Click "Back to All Notes" and verify the note is no longer in your list
<!-- Add Screenshot -->

### Add Logout Functionality and Display User Information

Now that all CRUD operations are secured with authentication, we need to add logout functionality and display the logged-in user's information across authenticated pages.

#### Create Server Route to Get Current User

Create a file named `me.get.js` inside the `server/api/auth` directory:
```shell
touch server/api/auth/me.get.js
```

Add the following code to `server/api/auth/me.get.js`:
```js
export default defineEventHandler(async (event) => {
  // Get the JWT from the HTTP-only cookie
  const token = getCookie(event, 'auth_token')

  // Fetch current user info from Strapi
  const user = await $fetch('http://localhost:1337/api/users/me', {
    headers: {
      Authorization: `Bearer ${token}`
    }
  })

  return user
})
```

#### Create Server Route for Logout

Create a file named `logout.get.js` inside the `server/api/auth` directory:
```shell
touch server/api/auth/logout.get.js
```

Add the following code to `server/api/auth/logout.get.js`:
```js
export default defineEventHandler(async (event) => {
  // Delete the auth token cookie
  deleteCookie(event, 'auth_token')

  // Redirect to the home page
  return sendRedirect(event, '/')
})
```

#### Update Notes List Page with User Info and Logout

Update `pages/notes/index.vue` to display user information and logout button:
```vue
<script setup>
  const { data: notes } = await useFetch('/api/notes')
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
  
  <h2>Notes List</h2>
  
  <!-- List of existing notes -->
  <ul>
    <li v-for="note in notes.data" :key="note.id">
      <h3>{{ note.title }}</h3>
      <NuxtLink :to="`/notes/${note.documentId}`">
        <button>View Note</button>
      </NuxtLink>
    </li>
  </ul>

  <!-- Link to the Create Note page -->
  <NuxtLink to="/notes/create">
    <button>Create a New Note</button>
  </NuxtLink>
</template>
```

#### Update Individual Note Page with User Info and Logout

Update `pages/notes/[id].vue` to include user information and logout:
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
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

#### How the Logout Flow Works

1. User clicks "Logout" button
2. Browser navigates to `/api/auth/logout` (our Nuxt server route)
3. Server route deletes the `auth_token` cookie
4. Server redirects user to the home page (`/`)
5. User is now logged out and must login again to access notes

#### Test Logout Functionality

1. Visit `http://localhost:3000/notes` while logged in
2. You should see "Logged in as: [your GitHub username]" at the top
<!-- Add Screenshot -->

3. Click the "Logout" button
4. You should be redirected to the home page (`/`)
<!-- Add Screenshot -->

5. Try visiting `http://localhost:3000/notes` directly - you should see an error or empty data since you're no longer authenticated
<!-- Add Screenshot -->

6. Click "Login with GitHub" to login again.
