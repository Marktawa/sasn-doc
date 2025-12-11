<!-- When done, abridge this guide -->

# Build a Note Sharing app with Social Authentication using Nuxt and Strapi

## Introduction

- Description of article
This article is about building a Note sharing app with social authentication using Nuxt and Strapi. It will start with building the app as a Notes app (Note-taking app) with no authentication. Afterwards, the Notes app will have authentication included, that is, Social Auth (GitHub and Facebook). After including social auth, the last phase will make the app be able to share notes to other authenticated users.

- Feature overview/list
Here is a list of the features which will be included in the app:
* Create a note
* Read a note
* Update a note
* Delete a note

* Create a user account for your notes
* Log in and Log out of your app

* Preview of notes
* Sharing via email the notes to other users
* Requesting edit access to a note by another user
* Add editors/collaborators to your notes (update your note)

- Demo video
- Links to demo video, GitHub repo, live link

## Prerequisites

- Knowledge of Strapi (Link to Quickstart), (Link to What is Strapi)
- Knowledge of HTML, CSS, JavaScript, Nuxt and Vue.js
- Node.js installed on your machine (latest LTS version)
- GitHub account
- Facebook account
- Google?
- Strapi Cloud account
- Brevo account

## Steps to be taken?

- Note taking system > Auth > Note Sharing system

## Create Strapi app

```shell
npx create-strapi-app@latest backend \
 --skip-cloud \
 --skip-db \
 --no-example \
 --js \
 --use-npm \
 --install \
 --no-git-init
```

```shell
cd backend
```

Create admin
```shell
npm run strapi admin:create-user -- --firstname=Kai --lastname=Doe --email=chef@strapi.io --password=Gourmet1234
```

Start your Strapi server.
```shell
npm run develop
```

Open browser and visit the admin page at `http://localhost:1337/admin`. Login and view your dashboard.

The details to login to the admin are `chef@strapi.io` for the admin and `Gourmet1234` for the password.

![Strapi Admin Dashboard](https://res.cloudinary.com/craigsims808/image/upload/v1750250272/strapi/sasn/basic-strapi-overview_fhjixd.png)

## Basic Overview of a Strapi application?

- Admin Panel Dashboard
- Content Manager
- Content-Type Builder
- Media Library
- Settings
- Marketplace
- Project File Tree Structure
- Links to Project Structure and Overview of Strapi Dashboard

## Create Notes collection

- Content-Type Builder
![Content-Type Builder](https://res.cloudinary.com/craigsims808/image/upload/v1758651648/strapi/sasn/content-type-builder_ozgfy1.png)
- Note collection with title(short text), content(long text) fields
![Note Collection](https://res.cloudinary.com/craigsims808/image/upload/v1758651812/strapi/sasn/note-collection_yxmcjl.png)

## Add Permissions for Notes Collection
- Add Public user permissions for Notes (find, findOne, create, delete, update)
![Public Permissions for Note collection](https://res.cloudinary.com/craigsims808/image/upload/v1758651996/strapi/sasn/public-permissions-note_nzq2fq.png)

## Add sample entries for Notes collection

- Visit the Content Manager and add sample notes to the Notes collection
![Sample Note entries](https://res.cloudinary.com/craigsims808/image/upload/v1758652196/strapi/sasn/sample-note-entries_mruep0.png)

- Test `find`
- Curl
```shell
curl -X GET http://localhost:1337/api/notes \
-H "Content-Type: application/json"
```

- Result
```json
{
    "data": [
        {
            "id": 1,
            "documentId": "p9qth3ydp8segnkulfrjd0fv",
            "title": "First Note",
            "content": "This is content for the first note.",
            "createdAt": "2025-09-23T18:27:45.292Z",
            "updatedAt": "2025-09-23T18:27:45.292Z",
            "publishedAt": "2025-09-23T18:27:45.287Z"
        },
        {
            "id": 2,
            "documentId": "qxls4t0s7tnyq42vhm4z7zxl",
            "title": "Second Note",
            "content": "This is content for the second note.",
            "createdAt": "2025-09-23T18:28:14.746Z",
            "updatedAt": "2025-09-23T18:28:14.746Z",
            "publishedAt": "2025-09-23T18:28:14.744Z"
        },
        {
            "id": 3,
            "documentId": "gb37iiqre4qm1us25fynu4m5",
            "title": "Third Note",
            "content": "This is content for the third note.",
            "createdAt": "2025-09-23T18:28:38.223Z",
            "updatedAt": "2025-09-23T18:28:38.223Z",
            "publishedAt": "2025-09-23T18:28:38.221Z"
        },
        {
            "id": 4,
            "documentId": "awoi8cgp18fi1xuz7qw3va7b",
            "title": "Note To Delete",
            "content": "This content for the note which will be deleted",
            "createdAt": "2025-09-23T19:07:18.370Z",
            "updatedAt": "2025-09-23T19:07:18.370Z",
            "publishedAt": "2025-09-23T19:07:18.361Z"
        },
        {
            "id": 5,
            "documentId": "sa0dnznt6xr78qprs07dkds3",
            "title": "Updated Note",
            "content": "Content of Note to be updated",
            "createdAt": "2025-09-23T19:08:31.874Z",
            "updatedAt": "2025-09-23T19:08:31.874Z",
            "publishedAt": "2025-09-23T19:08:31.871Z"
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

- Test `findOne`
- Curl (Use `documentId`)
```shell
curl -X GET http://localhost:1337/api/notes/p9qth3ydp8segnkulfrjd0fv \
-H "Content-Type: application/json"
```

- Result
```json
{
    "data": {
        "id": 1,
        "documentId": "p9qth3ydp8segnkulfrjd0fv",
        "title": "First Note",
        "content": "This is content for the first note.",
        "createdAt": "2025-09-23T18:27:45.292Z",
        "updatedAt": "2025-09-23T18:27:45.292Z",
        "publishedAt": "2025-09-23T18:27:45.287Z"
    },
    "meta": {}
}
```

- Test `create`, 
```shell
curl -X POST http://localhost:1337/api/notes \
-H "Content-Type: application/json" \
-d '{
       "data": {
           "title": "6 Created Note Title",
           "content": "This is the content of the created note"
          }
      }'         
```

- Result
```json
{
    "data": {
        "id": 6,
        "documentId": "v8an9b0iav2ia1f8si2d5b16",
        "title": "Created Note Title",
        "content": "This is the content of the created note",
        "createdAt": "2025-09-23T19:10:44.740Z",
        "updatedAt": "2025-09-23T19:10:44.740Z",
        "publishedAt": "2025-09-23T19:10:44.738Z"
    },
    "meta": {}
}
```

- Test`delete`, 
- Curl (Use `documentId`)
```shell
curl -X DELETE http://localhost:1337/api/notes/q3ltflda3l8j9xuuejytfofj
```

- Result will be a `204` HTTP code from the Strapi server. (No JSON)
```json
```

- Test `update`
```shell
curl -X PUT http://localhost:1337/api/notes/ia21j5a0o6muvwf0nvegce7d \
-H "Content-Type: application/json" \
-d '{
      "data": {
         "content": "This is content for the first note. It has been updated once."
        }
     }'
```

- Result
```json
{
    "data": {
        "id": 5,
        "documentId": "sa0dnznt6xr78qprs07dkds3",
        "title": "Updated Note title",
        "content": "Content of Note to be updated",
        "createdAt": "2025-09-23T19:08:31.874Z",
        "updatedAt": "2025-09-23T19:16:25.917Z",
        "publishedAt": "2025-09-23T19:16:25.915Z"
    },
    "meta": {}
}
```

## Create Nuxt frontend app

### Download and Install Nuxt

On your local machine, create a new folder named `frontend`. This will be your Nuxt project directory.
```shell
mkdir frontend
```

Open `frontend`.
```shell
cd frontend
```

Create a `package.json` file inside `frontend`.
```shell
touch package.json
```

Add the following to `package.json`:
```json
{
  "scripts": {
    "build": "nuxt build",
    "dev": "nuxt dev",
    "generate": "nuxt generate",
    "preview": "nuxt preview",
    "postinstall": "nuxt prepare",
    "start": "node .output/server/index.mjs"
  },
  "dependencies": {
    "nuxt": "latest",
    "vue": "latest"
  }
}
```

Install the dependencies listed in the `package.json` file by running the following command:
```shell
npm install
```

### Create Hello World Nuxt app

Create a `pages` folder:
```shell
mkdir pages
```

Create an `index.vue` file inside the `pages` folder:
```shell
touch pages/index.vue
```

Add this:
```vue
<template>
    <h1>Hello, World!</h1>
</template>
```

Run the development server:
```shell
npm run dev
```

Visit `http://localhost:3000` to view your site. You should see a web page with the text "Hello, World!".
![Nuxt page Hello World](https://res.cloudinary.com/craigsims808/image/upload/v1751151422/strapi/getting-started-with-nuxt/static-hello-world-web-page_rjkxnm.png)

### Create Notes Home page

Update `pages/index.vue` with the following code:
```vue
<template>
    <h1>Notes App</h1>
    <p>Welcome to the Notes App</p>
    <a href="/notes">Click here to view your notes</a>
</template>
```

Run the development server:
```shell
npm run dev
```

Visit `http://localhost:3000` to view your updated home page. 
![Notes Home page](https://res.cloudinary.com/craigsims808/image/upload/v1758655336/strapi/sasn/notes-home-page_xvteli.png)

### Create Notes List page

<!--
Create a `server` folder and an `api` folder:
```shell
mkdir -p server/api
```

Create a `notes.js` file inside the `server/api` folder
```shell
touch server/api/notes.js
```

Add the following code to `server/api/notes/js`:
```js
```
-->

Create a new folder named `notes` inside the `pages` folder:
```shell
mkdir pages/notes
```

Inside the `pages/notes` directory, create a new file named `index.vue`:
```shell
touch pages/notes/index.vue
```

Add the following code to `pages/notes/index.vue`:
```vue
<script setup>
  const { data: notes } = await useFetch('http://localhost:1337/api/notes')
</script>

<template>
  <h1>Notes App</h1>
  <h2>Notes List</h2>
  <ul>
    <li v-for="note in notes.data">{{ note.title }}</li>
  </ul>
</template>
```

Run the Nuxt development server:
```shell
npm run dev
```

Visit `http://localhost:3000/notes` to view your Notes list page. 
![Notes List page](https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/01-notes-list_yydtxz.png) 

### Create Individual Note page

Inside the `pages/notes` directory, create a new file named `[id].vue`:

```shell
touch pages/notes/[id].vue
```

Add the following code to `pages/notes/[id].vue`:
```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`http://localhost:1337/api/notes/${route.params.id}`)
</script>

<template>
  <h1>Notes App</h1>
  <h2>{{ note.data.title }}</h2>
  <p>{{ note.data.content }}</p>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

Update `pages/notes/index.vue` to add links to individual notes:
```vue
<script setup>
  const { data: notes } = await useFetch(`http://localhost:1337/api/notes`)
</script>

<template>
  <h1>Notes App</h1>
  <h2>Notes List</h2>
  <ul>
    <li v-for="note in notes.data">
      <h3>{{ note.title }}</h3>
      <NuxtLink :to="`/notes/${note.documentId}`">
        <button>View Note</button>
      </NuxtLink>
    </li>
  </ul>
</template>
```

Visit `http://localhost:3000/notes` and click on any note to view the individual note page.

Here is a screenshot of the updated Notes List page:

![Updated Notes List page](https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/02-updated-notes-list_xubtgl.png)

Here is a screenshot of an Individual Note page

![Individual Note page]https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/03-individual-note-page_x7iaqu.png)

### Set up "Create Note" flow

<!-- ARTICLE Revision Consider breaking down the concept using API routes first and seeng the output in the console -->
The process for creating a note will involve server-side rendering. This means that we need the following files/paths:
* One path for creating notes `pages/notes/create.vue`, 
* Another path for confirming whether or not the note has been created containing the link to the created note `pages/notes/created.vue` and,
* A third path for the created note itself (where we can view the contents of the created note). This one is dynamically generated and we have already created it: `pages/notes/[id].vue`.

The link to the "Create Note" page/path should be available in the Notes List page right underneath the list of notes. Once a user clicks the button, it should lead them to the "Create Note" page.

Within the "Create Note" page, there should be two form text input fields. One for entering the title. The other for entering the content or body of the note. And a "Create Note" button right below the form inputs. Once the user clicks the button, it should lead them to the "Created Note" page. 

Within the "Created Note" page, the user should be able to see whether or not the note was created. If the note was created successfully, a message should appear confirming the success including the link to the note. If the note creation failed, a message confirming the failure of the note creation process should appear.

#### Create `create.vue` file

Inside the `pages/notes` directory, create a new file named `create.vue`:

```shell
touch pages/notes/create.vue
```

Add the following code to `create.vue`:
```vue
<template>
  <h1>Notes App</h1>
  <h2>Create a New Note</h2>

  <form action="/api/notes/create" method="POST">
    <div>
      <label for="title">Title:</label>
      <input type="text" name="title" id="title" required />
    </div>

    <div>
      <label for="content">Content:</label>
      <textarea name="content" id="content" required></textarea>
    </div>
    
    <button type="submit">Create Note</button>
  </form>
</template>
```

#### Create Nuxt Internal API route for handling create notes

To make the form above work, we need to create a server handler: `server/api/notes/create.post.js`. This is Nuxt Server route will:
- Parse the incoming form data using `readBody`
- Format the data and wrap it into a structure Strapi expects: `{ data: { title: '...', content: '...' } }`.
- Use `$fetch` internally on the server to send the data to Strapi `(http://localhost:1337/api/notes)`.
- Handle Redirect:
  * On Success: The Nuxt server route should issue a `302` HTTP status code for Redirect and redirect the user to the `notes/created` route. The `documentId` of the newly created note will be appended as a query parameter so that the success page knows what to display (e.g., `sendRedirect(event, 'notes/created?status=success&id=' + response.data.documentId)`)
  * On Failure: Redirect to `/notes/created?status=error`.

Create the `server`, `server/api`, and `server/api/notes` directories as follows:
```shell
mkdir -p server/api/notes
```
Create a file named `create.post.js` inside the `server/api/notes` directory:
```shell
touch server/api/notes/create.post.js
```

Add the following code to `server/api/notes/create.post.js`:
```js
export default defineEventHandler(async (event) => {
  // Read form data sent from the browser
  const body = await readBody(event)

  try {
    // Send data to Strapi
    const strapiResponse = await $fetch('http://localhost:1337/api/notes', {
      method: 'POST',
      body: {
        data: {
          title: body.title,
          content: body.content
        }
      }
    })

    // Handle Success
    if (strapiResponse.data) {
      const noteId = strapiResponse.data.documentId
      return sendRedirect(event, `/notes/created?success=1&id=${noteId}`)
    }

    // Fallback if no data returned
    return sendRedirect(event, '/notes/created?success=0')

  } catch (error) {
    // Handle Error
    console.error('Failed to create note:', error)
    return sendRedirect(event, '/notes/created?success=0')
  }
})
```

Here is the summary of the flow:
1. User fills out HTML form on `pages/notes/create.vue`.
2. Browser POSTs data to Nuxt Internal API (`/api/notes/create`).
3. Nuxt Server reformats data and POSTs to Strapi.
4. Strapi responds with JSON to Nuxt Server.
5. Nuxt Server commands the Browser to Redirect to `pages/notes/created.vue`.

Next, create a `created.vue` file inside the `pages/notes` directory:
```shell
touch pages/notes/created.vue
```

#### Create page to view Note Creation Status

Inside the `pages/notes` directory, create a new file named `created.vue`:

Add the following code to `pages/notes/created.vue`:
```vue
<script setup>
  const route = useRoute()
  const isSuccess = route.query.success === '1'
  const noteId = route.query.id
</script>

<template>
  <h1>Notes App</h1>

  <!-- SUCCESS STATE -->
  <div v-if="isSuccess">
    <h2>Note Created Successfully!</h2>
    <p>Your note has been saved to the database.</p>

    <!-- Link to the individual note using the ID from the query param -->
    <NuxtLink :to="`/notes/${noteId}`">
      <button>View Created Note</button>
    </NuxtLink>

    <NuxtLink to="/notes">Back to Notes List</NuxtLink>
  </div>

  <!-- FAILURE STATE -->
  <div v-else>
    <h2>Note Creation Failed</h2>
    <p>There was an error creating your note. Please try again</p>

    <NuxtLink :to="`/notes/create`">
      <button>Try Again</button>
    </NuxtLink>

    <NuxtLink to="/notes">Back to Notes List</NuxtLink>    
  </div>
</template>
```

The page is rendered on the server. Nuxt looks at the URL and retrieves the value of the `success` variable as well as the value of the `id`. For example if the URL is `http://localhost:3000/notes/created?success=1&id=abc1234`, `route.query.success` will be `1` and the `route.query.id` will be `abc1234`.

The `v-if` code block evaluates the status of the `isSuccess` variable. 
* If `true`: The server generates the HTML containing the "Success" headers and the link to `/notes/abc1234`. The "Failure state" div is completely removed from the HTML sent to the browser.
* If `false`: The server generates the HTML containing the "Failure" state headers and the "Try Again" button to make another attempt.

#### Update Notes List page with Create Note link

Add a "Create a New Note" link underneath the list of notes inside the `pages/notes/index.vue` file.

```vue
<script setup>
  const { data: notes } = await useFetch('http://localhost:1337/api/notes')
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

  <!-- NEW: Link to the Create Note page -->
  <NuxtLink to="/notes/create">
    <button>Create a New Note</button>
  </NuxtLink>
</template>
```

#### Test Note Creation 

Run the Nuxt development server:
```shell
npm run dev
```

Make sure your Strapi server is running, then visit `http://localhost:3000/notes`. Click on the "Create a New Note" link underneath the list of notes.
![Updated Notes list](https://res.cloudinary.com/craigsims808/image/upload/v1765350049/strapi/sasn/04-updated-notes-list_mihzpo.png)

You will be directed to the page for creating notes, `http://localhost:3000/notes/create`
![Create Note page](https://res.cloudinary.com/craigsims808/image/upload/v1765350049/strapi/sasn/05-create-a-new-note-in-ui_xefmys.png

Enter text into the form inputs and click "Create" to create a note. If all is working well, you should be redirected to the `http://localhost:3000/notes/created` page where you will see the link to the note and confirmation of the success of its creation.
![Success: Created Note page](https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/06-note-creation-success_l5q7nw.png)

Click 'View Created Note' button to view the created note:
![Created Note ](https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/07-created-note_ylpimj.png)

<!--
If you instead find a message like "Note Creation Failed ..." indicating failure, then it means something went wrong. Check that the Strapi server is running, check that you entered the correct values for the URL, query ID and any other potential syntax errors as well.
![Failure: Created Note page]()-->
<!-- Add Screenshot -->


### Set up "Update Note" flow

The process for updating a note will involve server-side rendering. This means that we need the following file paths:
* One path for editing notes `pages/notes/edit.vue`,
* Another path for confirming that the note has been edited containing the link to the edited noted `pages/notes/edited.vue` and,
* A third path for the edited note itself (where we view the contents of the edited note). `pages/notes/[id.vue]`.

The link to the "Edit Note" page/path should be available in the page for viewing an individual note right underneath the content of the note. Once a user clicks the button, it should lead them to the "Edit Note" page.

Within the "Edit Note" page, there should be two form text input fields. One for the title, pre-filled with the note's title. The other field for the content, prefilled with the notes's content. A "Update Note" button right below the form inputs. Once the user clicks the button, it should lead them to the "Edited Note" page.

Within the "Edited Note" page, the user see confirmation that the note was edited. 

The `pages/notes/[id.vue]` file should be updated with logic that passes the `documentId` of the note from the URL.

#### Update `pages/notes/[id].vue`

Update the `pages/notes/[id].vue` file with the following code:

```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`http://localhost:1337/api/notes/${route.params.id}`)
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
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

#### Create `edit.vue` file

Inside the `pages/notes` directory, create a new file name `edit.vue`:

```shell
touch pages/notes/edit.vue
```

Add the following code to `edit.vue`:
```vue
<script setup>
  const route = useRoute()
  // Grab the ID from the URL query string
  const noteId = route.query.id
  // Fetch the existing note data to pre-fill the form
  const { data: note } = await useFetch(`http://localhost:1337/api/notes/${noteId}`)
</script>

<template>
  <h1>Notes App</h1>
  <h2>Edit Note</h2>
  
  <form action="/api/notes/update" method="POST">
    <!-- Hidden input to tell the backend which note to update -->
    <input type="hidden" name="documentId" :value="noteId" />
    <div>
      <label for="title">Title:</label>
      <!-- Pre-fill title -->
      <input type="text" name="title" id="title" v-model="note.data.title" required />
    </div>
    <div>
      <label for="content">Content:</label>
      <!-- Pre-fill content -->
      <textarea name="content" id="content" v-model="note.data.content" required></textarea>
    </div>
    <button type="submit">Update Note</button>
  </form>
</template>
```

This page handles the "Update View". It performs the following steps:
* Server-Side Fetch: Uses the `id` from the URL to fetch the existing note data.
* Pre-fill Form: Binds the fetched data to the form inputs so the user sees what they are editing.
* Submission: Uses a standard HTML form submission (`POST`) to a new Nuxt server handler (`/api/notes/update`).
* Hidden ID: Includes a hidden input field to pass the `documentId` to the server handler, ensuring Strapi updates the correct record.

#### Create Nuxt Internal API route for handling update notes

Create a file named `update.post.js` inside `server/api/notes` folder:
```shell
touch server/api/notes/update.post.js
```

Add the following code to `server/api/notes/update.post.js`:
```js
export default defineEventHandler(async (event) => {
  // Parse incoming form data
  const body = await readBody(event)
  const { documentId, title, content } = body

  // Send PUT request to Strapi
  const strapiResponse = await $fetch(`http://localhost:1337/api/notes/${documentId}`, {
    method: 'PUT',
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

#### Create `edited.vue` file for confirmation

Add a new file named `edited.vue` inside the `pages/notes` folder:
```shell
touch pages/notes/edited.vue
```

Add the following code to `pages/notes/edited.vue`:
```vue
<script setup>
  const route = useRoute()
  const noteId = route.query.id
</script>

<template>
  <h1>Notes App</h1>
  <h2>Note edited successfully</h2>
  <p>Your note has been updated</p>

  <NuxtLink :to="`/notes/${noteId}`">
    <button>View Edited Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to All Notes</NuxtLink>
</template>
```

#### Test Note Update

Run the Next development server:
```shell
npm run dev
```

Make sure your Strapi server is running, then visit `http://localhost:3000/notes`. Click on an individual note and click the "Edit Note" button underneath the note content.
![Updated Individual note page](https://res.cloudinary.com/craigsims808/image/upload/v1765350049/strapi/sasn/08-updated-individual-note-page_gzus7a.png)

You will be directed to the editing notes page. The title and content for the note should already be pre-filled.
![Update note page](https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/09-edit-note-page_r0p5lk.png)

Edit the note's title and content and click "Update Note". You should be redirected to the `http://localhost:3000/notes/updated` page where you will see the link to the note and confirmation of its update.
![Updated Note page](https://res.cloudinary.com/craigsims808/image/upload/v1765350049/strapi/sasn/10-note-edit-success_ti0a5a.png)

Click 'View Updated Note' button to view the updated note
![Updated Note](https://res.cloudinary.com/craigsims808/image/upload/v1765350049/strapi/sasn/11-note-view-updated_xbzma2.png)

### Set up "Delete Note" flow

The process for deleting a note will involve server-side rendering. This means that we need the following paths:
* One path for deleting notes `pages/notes/delete.vue`,
* Another path for confirming that the note has been deleted `pages/notes/deleted.vue`, and

The link to the "Delete Note" page should be below the content of the note to be deleted in the Note view page.

Within the "Delete Note" page should be the title and the content of the note to be deleted and a message asking the user whether they want to delete the note "Are you sure you want to delete this note?". A "Yes" button upon click leads the user to the "Deleted Note" page and a "No" button that returns the user to the Note View page.

Within the "Deleted Note" page, the user will see a confirmation that the note was deleted.

#### Update `page/notes/[id].vue`

Update the `pages/notes/[id].vue` file with the following code:

```vue
<script setup>
  const route = useRoute()
  const { data: note } = await useFetch(`http://localhost:1337/api/notes/${route.params.id}`)
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

#### Create `delete.vue` file

Inside the `pages/notes` directory, create a new file named `delete.vue`

```shell
touch pages/notes/delete.vue
```

Add the following code to `delete.vue`:
```vue
<script setup>
  const route = useRoute()
  // Grab the ID form the URL query string
  const noteId = route.query.id
  // Fetch the existing note data
  const { data: note } = await useFetch(`http://localhost:1337/api/notes/${noteId}`)
</script>

<template>
  <h1>Notes App</h1>
  <h2>Delete Note</h2>

  <h3>{{ note.data.title }}</h3>
  <p>{{ note.data.content }}</p>

  <p>Are you sure you want to delete this note?</p>
  <form action="/api/notes/delete" method="POST">
    <!-- Hidden input to tell the backend whtich note to delete -->
    <input type="hidden" name="documentId" :value="noteId" />

    <!-- YES Button: submits the form -->
    <button type="submit">Yes</button>
  </form>

  <!-- NO Button: links back to View Note page -->
  <NuxtLink :to="`/notes/${noteId}`">
    <button>No</button>
  <NuxtLink>
</template>
```

#### Create Nuxt Internal API route for handling delete notes

Create a file named `delete.post.js` inside `server/api/notes` folder:
```shell
touch server/api/notes/delete.post.js
```

Add the following code to `server/api/notes/delete.post.js`:
```js
export default defineEventHandler(async (event) => {
  // Parse incoming form data
  const body = await readBody(event)
  const { documentId } = body

  // Send DELETE request to Strapi
  await $fetch(`http://localhost:1337/api/notes/${documentId}`, {
    method: 'DELETE'
  })

  // Redirect to the "Deleted" confirmation page
  return sendRedirect(event, '/notes/deleted')
})
```

#### Create `deleted.vue` file for confirmation

Add a new file named `deleted.vue` inside the `pages/notes` folder:
```shell
touch pages/notes/deleted.vue
```

Add the following code to `pages/notes/deleted.vue`:
```vue
<template>
  <h1>Notes App</h1>
  <h2>Note deleted successfully</h2>
  <p>Your note has been deleted</p>
  <br>
  <NuxtLink to="/notes">Back to All Notes</NuxtLink>
</template>
```

#### Test Note Delete

Run the Next development server:
```shell
npm run dev
```

Make sure your Strapi server is running, then visit `http://localhost:3000/notes`. Click on an individual note and click the "Delete Note" button underneath the note content.
![Updated View Note page](https://res.cloudinary.com/craigsims808/image/upload/v1765351005/strapi/sasn/12-updated-view-note_gyopwf.png)

You will be directed to the delete note page. The title and content for the note should be displayed along with a confirmation question.
![Delete note page](https://res.cloudinary.com/craigsims808/image/upload/v1765351338/strapi/sasn/13-delete-note_dz8f4h.png)

Click "Yes". You should be redirected to the `http://localhost:3000/notes/deleted` page where you will see the confirmation of its deletion.
![Deletion Confirmed](https://res.cloudinary.com/craigsims808/image/upload/v1765351339/strapi/sasn/14-deletion-confirmed_vg2faw.png)

Click 'Back to All Notes' link to return to the list and verify the note is gone.
![Notes List without Deleted Note](https://res.cloudinary.com/craigsims808/image/upload/v1765351339/strapi/sasn/15-notes-list-without-deleted-note_toyfqc.png)

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

Request user's JWT token:
```shell
curl -X GET http://localhost:1337/api/auth/github/callback?access_token=GITHUB_CODE > test6.json
```

Get the response JSON:
```json
```

Check to see if user has been added to the User collection in the Content Manager.
<!-- Add Screenshot -->

Add JWT code to `.secret`:
```
GITHUB_JWT_TOKEN_2=your-code
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

## Test `find`, `findOne`, `create`, `update`, and `delete` for Authenticated Users

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

Stop your Strapi server and open the `src/api/controllers/note.js` file in your code editor.

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
```
<!-- Add Response -->

The response should now include only the owner's notes.

### Override the `findOne` method in the Notes controller

Just as in the `find` method we can also override the `findOne` method using the Notes controller. This way `findOne` will only return a note belonging to the owner.

Update `src/api/controllers/note.js` with the following code:
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

  async findOne(ctx) {
    const user = ctx.state.user;
    
    const { id } = ctx.params;

    const note = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    return note;
  },
}));
```

### Test `findOne`

Grab one of the `documentId` from a note you created earlier and take note of the user. If you have to, perform a `find` request for said user and then capture a `documentId` for a note.

Next perform a `findOne` request as an authenticated user:
```shell
curl -X GET "http://localhost:1337/api/notes/documentId" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

<!-- Add Response -->
```json
```

The response should reveal the note with the matching `documentId`.

Perform a `findOne` request as an authenticated user trying to access a note you don't own:
```shell
curl -X GET "http://localhost:1337/api/notes/documentId" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

<!-- Add Response -->
```json
```

The response should be "Note not found or you do not have access" as outlined in the code for the `findOne` method in the controller. An HTTP status code of `403` Fobidden Error code.

This means that an authenticated user only has access to the notes they own.

### Override the `create` method in the Notes controller

Just as in the previous methods, we can also override the `create` method in the Notes controller. This way `create` will only be used to create notes for the given owner. That is, an owner cannot create notes for another user.

Update `src/api/controllers/note.js` with the following code:
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

  async findOne(ctx) {
    const user = ctx.state.user;
  
    const { id } = ctx.params;

    const note = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    return note;
  },

  async create(ctx) {
    const user = ctx.state.user;

    const { title, content } = ctx.request.body.data || ctx.request.body;
    const note = await strapi.documents('api::note.note').create({
      data: {
        title,
        content,
        user: user.id,
      },
      populate: ['user'],
    });
    ctx.body = note;
  },
}));
```

The security benefit here is that the user ID comes from `ctx.state.user` (the authenticated user), not from the request body, preventing users from creating notes on behalf of other users.

### Test `create`

Next perform a `create` request to create a note as an authenticated user:
```shell
curl -X POST "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "6 Sixth Note",
      "content": "This is the content for the sixth note."
    }
  }'
```

<!-- Add Response -->
```json
```

The response should give you a newly created note.

Next perform a `create` request to create note owned by a different user:
```shell
curl -X POST "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "7 Seventh Note",
      "content": "This is the content for the seventh note.",
      "user": 3
    }
  }'
```

<!-- Add Response -->
```json
```

The response should give you a newly created note, except it will still belong to the authenticated user who made the request not for the user defined in the request.

### Override the `delete` method in the Notes controller

Like in the previous methods, we can override the `delete` method in the Notes controller. This way `delete` will only be used to delete notes fo the given owner. No user can delete notes owned by other users.

Update `src/api/controllers/note.js` with the following code:
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

  async findOne(ctx) {
    const user = ctx.state.user;
  
    const { id } = ctx.params;

    const note = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    return note;
  },

  async create(ctx) {
    const user = ctx.state.user;

    const { title, content } = ctx.request.body.data || ctx.request.body;
    const note = await strapi.documents('api::note.note').create({
      data: {
        title,
        content,
        user: user.id,
      },
      populate: ['user'],
    });
    ctx.body = note;
  },

  async delete(ctx) {
    const user = ctx.state.user

    const { id } = ctx.params;

    const note = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have permission to delete it')
    }

    const deletedNote = await strapi.documents('api::note.note').delete({
      documentId: id,
    });

    ctx.body = deletedNote;
  },
}));
```

Here is the flow:
- Ownership verification: Finds the note with a filter ensuring it belongs to the user
- Authorization: Returns 404 if note doesn't exist or doesn't belong to the user
- Deletion: Only deletes after confirming ownership
- Response: Returns the deleted note data

This two-step approach (`find` then `delete`) is crucial for security—it prevents users from deleting notes that don't belong to them, even if they know the `documentId`.

### Test `delete`

Next perform a `delete` request to delete a note as an authenticated user:

```shell
curl -X DELETE "http://localhost:1337/api/notes/14" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

<!-- Add Response -->
```json
```

Verify the note is gone
```shell
curl -X GET "http://localhost:1337/api/notes/14" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

You should get a note not found

Try to delete a note that doesn't exist or doesn't belong to the user.
```shell
curl -X DELETE "http://localhost:1337/api/notes/999999" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

<!--Add Response -->
```json
```
The operation should not work and the expected response is, "Note not found or you do not have permission to delete it"

### Override the `update` method in the Notes controller

Like in the previous methods, we can override the `update` method in the Notes controller. This way `update` will only be used to update notes for the given owner. No user can update notes owned by other users.

Update `src/api/controllers/note.js` with the following code:
```js
const { createCoreController } = require('@strapi/strapi').factories;

module.exports = createCoreController('api::note.note', ({ strapi }) => ({ 
  async find(ctx) {
    const user = ctx.state.user;

    return await strapi.documents('api::note.note').findMany({
      filters: { user: user.id },
    });
  },

  async findOne(ctx) {
    const user = ctx.state.user;
  
    const { id } = ctx.params;

    const note = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have access');
    }

    return note;
  },

  async create(ctx) {
    const user = ctx.state.user;

    const { title, content } = ctx.request.body.data || ctx.request.body;
    const note = await strapi.documents('api::note.note').create({
      data: {
        title,
        content,
        user: user.id,
      },
      populate: ['user'],
    });
    ctx.body = note;
  },

  async delete(ctx) {
    const user = ctx.state.user

    const { id } = ctx.params;

    const note = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
      populate: ['user'],
    });

    if (!note) {
      return ctx.notFound('Note not found or you do not have permission to delete it')
    }

    const deletedNote = await strapi.documents('api::note.note').delete({
      documentId: id,
    });

    ctx.body = deletedNote;
  },

  async update(ctx) {
    const user = ctx.state.user;

    const { id } = ctx.params;
    const { title, content } = ctx.request.body.data || ctx.request.body;

    //Check if note exists and belongs to user
    const existingNote = await strapi.documents('api::note.note').findOne({
      documentId: id,
      filters: { user: user.id },
    });

    if (!existingNote) {
      return ctx.notFound('Note not found or you do not have permission to update it');
    }

    // Update note without changing user
    const updatedNote = await strapi.documents('api::note.note').update({
      data: {
        documentId: id,
        title,
        content,
        user: user.id,
      },
      populate: ['user'],
    });

    ctx.body = updatedNote;
  }
}));
```

Here is the flow:
- Ownership verification: Confirms note belongs to the user before updating
- Protected fields: Prevents changing `user` field via the update endpoint
- Authorization: Returns `404` if note doesn't exist or user lacks permission.

This approach ensures users can only update their own notes and prevents privilege escalation by locking down sensitive fields like ownership status.

### Test `update`

Next perform an `update` request to update a note as an authenticated user:
```shell
curl -X PUT "http://localhost:1337/api/notes/14" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated Note Title",
      "content": "This is the updated content of my note"
    }
  }'
```

<!-- Response -->
```json
{
  "data": {
    "id": 14,
    "documentId": "abc123",
    "title": "Updated Note Title",
    "content": "This is the updated content of my note",
    "isShared": false,
    "user": {
      "id": 1,
      "username": "youruser"
    },
    "createdAt": "2024-12-01T10:00:00.000Z",
    "updatedAt": "2024-12-10T15:30:00.000Z"
  }
}
```

Verify the note was updated
```shell
curl -X GET "http://localhost:1337/api/notes/14" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

You should see the updated title and content
<!-- Add response -->
```json
```

Try to update a note that doesn't exist or doesn't belong to the user.
```shell
curl -X PUT "http://localhost:1337/api/notes/999999" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Trying to update someone else's note",
      "content": "This should fail"
    }
  }'
```

You should get a "Note not found or you do not have permission to update it" error.

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

* Replaces the direct link to /notes with a GitHub authentication flow
* Points to Strapi's GitHub OAuth endpoint (http://localhost:1337/api/connect/github)
* When clicked, the user will:
  - Be redirected to GitHub to authorize your app
  - Be redirected back to Strapi after authorization
  - Strapi will generate a JWT token


**Test it:**

Run your development servers and visit `http://localhost:3000`. Click "Login with GitHub" and verify you're redirected to GitHub's authorization page.
<!-- Add Screenshot -->

### Handle OAuth Callback and Store JWT Token (Server-Side)

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

1. **User clicks "Login with GitHub"** on `http://localhost:3000`
2. **Frontend redirects to Strapi**: `http://localhost:1337/api/connect/github`
3. **Strapi redirects to GitHub** login page where the user authorizes the app
4. **GitHub redirects back to Strapi**: `http://localhost:1337/api/connect/github/callback?code=abcdef`
5. **Strapi exchanges the code for an access_token** with GitHub
6. **Strapi redirects to your Nuxt callback**: `http://localhost:3000/auth/callback?access_token=xyz123`
7. **Nuxt server handler receives the request** and extracts the `access_token`
8. **Nuxt server calls Strapi again**: `http://localhost:1337/api/auth/github/callback?access_token=xyz123`
9. **Strapi validates the token with GitHub**, retrieves the user's profile, matches it with a Strapi user, and returns `{ jwt, user }`
10. **Nuxt server stores the Strapi JWT** in an HTTP-only cookie
11. **Server redirects the user to `/notes`**
12. **All future requests** to Strapi will include this JWT for authentication

#### Why This Two-Step Process?

The `access_token` from step 7 is a **GitHub access token**, not a Strapi JWT. Strapi needs to:
- Verify the token with GitHub
- Get the user's GitHub profile (email, username, etc.)
- Match or create a Strapi user
- Generate a Strapi-specific JWT

This is why step 8 (the callback to Strapi) is essential.

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

1. **User visits `/notes`** after successful authentication
2. **Server-side fetch** calls `/api/notes` (our Nuxt server route)
3. **Server route reads the JWT** from the HTTP-only cookie
4. **Server route makes authenticated request** to Strapi at `http://localhost:1337/api/notes` with `Authorization: Bearer <JWT>`
5. **Strapi's custom controller** filters notes to only return those belonging to the authenticated user
6. **Server returns the filtered notes** to the page
7. **Page renders** with only the user's notes

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

1. **User clicks "View Note"** on a note from the notes list
2. **Page requests `/api/notes/{documentId}`** (our Nuxt server route)
3. **Server route extracts the note ID** from the URL parameter
4. **Server route reads the JWT** from the HTTP-only cookie
5. **Server route makes authenticated request** to Strapi with `Authorization: Bearer <JWT>`
6. **Strapi's custom `findOne` controller** verifies the note belongs to the authenticated user
7. **Server returns the note data** to the page
8. **Page renders** with the note's title and content

#### Test Authenticated Individual Note View

1. Visit `http://localhost:3000/notes` while logged in
2. Click "View Note" on any of your notes
3. You should see the note's title and content
<!-- Add Screenshot -->

4. Try accessing a note that doesn't belong to you by manually entering a different `documentId` in the URL
5. You should get a "Note not found" response (because Strapi filters by user ownership)
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

1. **User fills out the create note form** on `/notes/create`
2. **Form submits to `/api/notes/create`** (our Nuxt server route)
3. **Server route reads the form data** (title and content)
4. **Server route reads the JWT** from the HTTP-only cookie
5. **Server route makes authenticated POST request** to Strapi with `Authorization: Bearer <JWT>`
6. **Strapi's custom `create` controller** automatically associates the note with the authenticated user (from `ctx.state.user`)
7. **Server redirects** to the success page with the new note's `documentId`

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

1. **User clicks "Edit Note"** on an individual note page
2. **Edit page fetches note data** through `/api/notes/${noteId}` with authentication
3. **Form is pre-filled** with the note's current title and content
4. **User modifies the note** and clicks "Update Note"
5. **Form submits to `/api/notes/update`** (our Nuxt server route)
6. **Server route reads the form data** (documentId, title, content)
7. **Server route reads the JWT** from the HTTP-only cookie
8. **Server route makes authenticated PUT request** to Strapi with `Authorization: Bearer <JWT>`
9. **Strapi's custom `update` controller** verifies the note belongs to the user before updating
10. **Server redirects** to the success page with the note's `documentId`

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

8. Try to edit a note that doesn't belong to you by manually entering a different `documentId` - the edit page should not load the note data

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

1. **User clicks "Delete Note"** on an individual note page
2. **Delete confirmation page fetches note data** through `/api/notes/${noteId}` with authentication
3. **Page displays the note** with "Are you sure you want to delete this note?"
4. **User clicks "Yes"** to confirm deletion
5. **Form submits to `/api/notes/delete`** (our Nuxt server route)
6. **Server route reads the form data** (documentId)
7. **Server route reads the JWT** from the HTTP-only cookie
8. **Server route makes authenticated DELETE request** to Strapi with `Authorization: Bearer <JWT>`
9. **Strapi's custom `delete` controller** verifies the note belongs to the user before deleting
10. **Server redirects** to the success page confirming deletion

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

8. Try to delete a note that doesn't belong to you by manually entering a different `documentId` - the delete confirmation page should not load the note data
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

1. **User clicks "Logout"** button
2. **Browser navigates to `/api/auth/logout`** (our Nuxt server route)
3. **Server route deletes the `auth_token` cookie**
4. **Server redirects user** to the home page (`/`)
5. **User is now logged out** and must login again to access notes

#### Test Logout Functionality

1. Visit `http://localhost:3000/notes` while logged in
2. You should see "Logged in as: [your GitHub username]" at the top
<!-- Add Screenshot -->

3. Click the "Logout" button
4. You should be redirected to the home page (`/`)
<!-- Add Screenshot -->

5. Try visiting `http://localhost:3000/notes` directly - you should see an error or empty data since you're no longer authenticated
<!-- Add Screenshot -->

6. Click "Login with GitHub" to login again

### Add Route Protection for Authenticated Pages

Now we need to protect our notes pages so that unauthenticated users cannot access them. If a user tries to visit `/notes` or any note-related page without being logged in, they should be redirected to the home page.

#### Create Server Middleware for Authentication Check

Nuxt server middleware runs on every request, making it perfect for checking authentication before pages load.

Create a `middleware` folder inside the `server` directory:
```shell
mkdir server/middleware
```

Create a file named `auth.js` inside the `server/middleware` directory:
```shell
touch server/middleware/auth.js
```

Add the following code to `server/middleware/auth.js`:
```js
export default defineEventHandler(async (event) => {
  const url = getRequestURL(event)
  
  // Check if the request is for a protected route (anything under /notes or /api/notes or /api/auth/me)
  const isProtectedPage = url.pathname.startsWith('/notes')
  const isProtectedAPI = url.pathname.startsWith('/api/notes') || url.pathname === '/api/auth/me'
  
  // Skip auth check for login callback and logout routes
  const isAuthRoute = url.pathname.startsWith('/auth/callback') || url.pathname === '/api/auth/logout'
  
  if ((isProtectedPage || isProtectedAPI) && !isAuthRoute) {
    // Check if user has an auth token
    const token = getCookie(event, 'auth_token')
    
    if (!token) {
      // No token found - redirect to home page
      if (isProtectedPage) {
        return sendRedirect(event, '/')
      }
      // For API routes, return 401 Unauthorized
      if (isProtectedAPI) {
        throw createError({
          statusCode: 401,
          statusMessage: 'Unauthorized'
        })
      }
    }
  }
})
```

#### How Route Protection Works

1. **Middleware runs on every request** before the page or API route handler
2. **Checks if the route is protected** (anything under `/notes`, `/api/notes`, or `/api/auth/me`)
3. **Skips auth check** for public routes like `/auth/callback` and `/api/auth/logout`
4. **Reads the `auth_token` cookie** to verify authentication
5. **If no token is found**:
   - For page routes (`/notes/*`): Redirects to home page
   - For API routes (`/api/notes/*`): Returns 401 Unauthorized error
6. **If token exists**: Request proceeds normally to the page or API handler

#### Test Route Protection

**Test 1: Access notes while logged out**
1. Make sure you're logged out (visit `/api/auth/logout` if needed)
2. Try to visit `http://localhost:3000/notes` directly
3. You should be immediately redirected to the home page
<!-- Add Screenshot -->

**Test 2: Access individual note while logged out**
1. While logged out, try to visit a specific note URL like `http://localhost:3000/notes/abc123`
2. You should be redirected to the home page
<!-- Add Screenshot -->

**Test 3: Normal authenticated flow**
1. Visit `http://localhost:3000` and click "Login with GitHub"
2. After authentication, you should be able to access `/notes` normally
3. You should be able to view, create, edit, and delete notes
4. Click "Logout" and verify you can no longer access `/notes`
<!-- Add Screenshot -->

**Test 4: API protection**
1. While logged out, open your browser's Developer Console
2. Try to manually fetch `http://localhost:3000/api/notes`
3. You should receive a 401 Unauthorized response
<!-- Add Screenshot -->

Your notes app now has complete authentication with route protection! Users must login with GitHub to access any notes functionality, and all CRUD operations are secured to each user's own data.

## Add Email

### Configure Brevo

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

### Add A Brevo Service

In the root of your Strapi install folder, create a new file named `brevo.js` inside the `src/services` folder:

```shell
touch src/service/brevo.js
```

Add the following code to `src/service/brevo.js`:
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

Update your `.env` file in Strapi's install folder as with following variables below the other variables:

```
SENDER_EMAIL=paul.zimba96@gmail.com
SENDER_NAME=Notes App
APP_URL=http://localhost:3000
```

### Test Brevo Email Service

Before integrating email sharing into the main app, let's verify that Brevo is configured correctly and can send emails.

#### Create Test Email Route

Create a new folder named `email` inside the `src/api` directory:
```shell
mkdir src/api/email
```

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

Create a file named `email.js` inside the `src/api/email/controllers` directory:
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
            html: '<h1>Success</h1><p>Your Brevo integration is working correctly.</p>',
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
-H "Content-Type: application/json" \
-d '{"email": "your-email@example.com"}'
```

Replace `your-email@example.com` with your actual email address.

If everything is configured correctly, you should:
1. Receive a JSON response: `{ "message": "Test email sent successfully", "result": {...} }`

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

## Add Sharing Functionality to Notes in Strapi Backend

We will now add the ability to share notes with other users via email. When you share a note, the recipient will receive an email with a link to view the note. 

### Update Note Collection Type

First, we need to add a field to store information about who a note is shared with.

Open the Strapi Admin Panel at `http://localhost:1337/admin` and navigate to **Content-Type Builder** then **Note** under **COLLECTION TYPES**.


Click **Add another field** and select **Boolean** type. Name it `isShared`. Set the default value to `false`.
<!-- Add Screenshot -->

Click **Add another field** and select **JSON** type. Name it `sharedWith`. Click **Finish** and **Save** and wait for the Strapi server to restart.
<!-- Add Screenshot -->

The `sharedWith` field will store an array of objects containing information about users who have access to the note:
```js
// Example structure
[
    {
        email: "viewer@example.com",
        canEdit: false
    }
]
```
<!-- Careful -->

Your Notes collection should now contain the following fields:
- `title`,
- `content`,
- `isShared`, and
- `sharedWith`

<!-- Add Screenshot -->

#### Test to See new Payload

Since we have added the `isShared` and `sharedWith` fields to the Notes collection, it would be wise just to see how the new response looks like.

In your terminal perform a `GET` request for the notes belonging to one user using `curl`:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $GITHUB_JWT_TOKEN"
```

You should see in your response, each note with 2 new fields: `isShared` and `sharedWith`. `isShared` will have a value of `false`:
```json
```
<!-- Add Response -->

### Create Share Route

Open the `src/api/note/routes/note.js` file and add a new route for sharing notes:
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
                middlewares:[],
            }
        }
    ]
}
```

### Implement Share Controller Method

Open the `src/api/note/controllers/note.js` file and add the `share` method:
```js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;
const { sendEmail } = require('../../../services/brevo');

module.exports = createCoreController('api::note.note', ({ strapi }) => ({
    async find(ctx) {
        const user = ctx.state.usr;
        return await strapi.documents('api::note.note').findMany({
            filters: { user: user.id },
        });
    },''

    async findOne(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
            filters: { user: user.id },
            populate: ['user'],
        });

        if (!note) {
            return ctx.notFound('Note not found or you do not have access');
        }

        return note;
    },

    async create(ctx) {
        const user = ctx.state.user;
        const { title, content } = ctx.request.body.data || ctx.request.body;

        const note = await strapi.documents('api::note.note').create({
            data: {
                title,
                content,
                user: user.id,
                isShared: false,
            }
            populate: ['user'],
        });

        ctx.body = note;
    },

    async update(ctx) {
        const user = ctx.state.user;;
        const { id } = ctx.params;
        const { title, content } = ctx.request.body.data || ctx.request.body;

        const existingNote = await strapi.documents('api::note.note').findONe({
            documentId: id,
            filters: { user: user.id },
            fields: ['isShared'],
        });

        if (!existingNote) {
            return ctx.notFound('Note not found or you do not have permission to update it');
        }


        const updatedNote = await strapi.documents('api::note.note').update({
            documentId: id,
            data: {
                title,
                content,
                user: user.id,
                isShared: existingNote.isShared,
            },
            populate: ['user'],
        });

        ctx.body = updatedNote;
    },

    async delete(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;

        const note = await strapi.documents('api::note.note').findONe({
            documentId: id,
            filters: { user: user.id },
            populate: ['user'],
        });

        if (!note) {
            return ctx.notFound('Note not found or you do not have permission to delete it');
        }

        const deletedNote = await strapi.documents('api::note.note').delete({
            documentId: id,
        });

        ctx.body = deletedNote;
    },

    async share(ctx) {
        const user = ctx.state.user;
        const { id } = ctx.params;
        const { email } = ctx.request.body;

        const note = await strapi.documents('api::note.note').findOne({
            documentId: id,
            filters: { user: user.id },
        });

        if (!note) {
            return ctx.notFound('Note not found or you do not have permission to share it');
        }

        const sharedWith = note.sharedWith || [];
        const alreadyShared = sharedWith.find(share => share.email === email);

        if (alreadyShared) {
            return ctx.badRequest('Note is already shared with this email');
        }

        sharedWith.push({
            email: email,
            canEdit: false,
        });

        await strapi.documents('api::note.note').update({
            documentId: id,
            data: {
                sharedWith: sharedWith,
                isShared: true,
            }
        });

        const shareLink = `${process.env.APP_URL}/notes/${id}`;
        await sendEmail({
            to: email,
            subject: `${user.username} shared a note with you`,
            html: `
                <h2>A note has been shared with you</h2>
                <p><strong>${user.username}</strong> has shared the note "<strong>${note.title}</strong>" with you.</p>
                <p><a href="${shareLink}">Click here to view the note</a></p>
                `,
        });

        ctx.send({
            message: 'Note shared successfully',
            sharedWith: email,
        });
    },
}));
```

### How the Share Flow Works

* Owner clicks "Share Note" on their note
* Owner enters recipient's email address
* Request is sent to `/api/notes/{documentId}/share` route with the email
* Strapi verifies the note belongs to the requesting user
* Strapi checks if note is already shared with that email
* Strapi adds the email to the `sharedWith` array with `canEdit: false`
* Strapi sets `isShared: true` on the note
* Strapi sends email to the recipient with a link to view the note.
* Recipient receives email and can click the link to view the shared note.

### Test the `share` Endpoint

Use curl to test sharing a note:
```shell
curl -X POST "http://localhost:1337/api/notes/YOUR_NOTE_ID/share" \
    -H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
    -H "Content-Type: application/json" \
    -d '{"email": "recipient@example.com"}'
```

Replace `YOUR_NOTE_ID` with an actual note documentId and `recipient@example.com` with a real email address.

You should receive a response:
```json
{
  "message": "Note shared successfully",
  "sharedWith": "recipient@example.com"
}
```

And the recipient should receive an email with a link to view the note.
<!-- Add screenshot -->


### Update `findOne` Controller to Allow Shared Access

Currently, the `findOne` controller only allows the note owner to view their notes. We need to update it to also allow users who have been given shared access to view the note.

Open the `src/api/note/controllers/note.js` file and update the `findOne` method:

```js
async findOne(ctx) {
  const user = ctx.state.user;
  const { id } = ctx.params;

  // First, try to find the note without filters to check sharing
  const note = await strapi.documents('api::note.note').findOne({
    documentId: id,
    populate: ['user'],
  });

  if (!note) {
    return ctx.notFound('Note not found');
  }

  // Check if user is the owner
  const isOwner = note.user.id === user.id;

  // Check if note is shared with this user
  const sharedWith = note.sharedWith || [];
  const hasSharedAccess = sharedWith.some(share => share.email === user.email);

  // Allow access if user is owner OR has shared access
  if (!isOwner && !hasSharedAccess) {
    return ctx.notFound('Note not found or you do not have access');
  }

  return note;
},
```

#### How the Updated Flow Works

* User requests a note by visiting `/notes/{documentId}`
* Strapi fetches the note without filtering by owner
* Strapi checks if the requesting user is the owner by comparing user IDs
* Strapi checks if the note is shared with the requesting user's email
* If user is owner OR has shared access -> Return the note
* If neither condition is TRUE -> Return 404: "Note not found or you do not have access"

### Test Shared Note Access

#### Add User Tokens

To test shared note access, we will first create 3 GitHub authenticated users.
* 1 user will be the owner of the note with the token labeled: `OWNER_JWT`
* The second user will be the recipient of the note with token labeled: `RECIPIENT_JWT`
* The final user will be another user unrelated to the note, with token labeled: `OTHER_JWT`

First we will create the tokens for the users

1. `OWNER_JWT`:
Visit `http://localhost:1337/api/connect/github` in your browser. Go through the auth flow and retrieve the `access_token` from the response.

Use the `access_token` to generate a `JWT` for the user and store it as `OWNER_JWT` inside `.secret`.

Run the following command in your terminal:
```shell
OWNER_JWT=$(curl -s "http://localhost:1337/api/auth/github/callback?access_token=GITHUB_CODE" \
  | grep -o '"jwt":[^,]*' \
  | cut -d':' -f2 \
  | tr -d '"')

echo "$OWNER_JWT" > .secret
```

2. `RECIPIENT_JWT`:
Repeat the same procedure for `RECIPIENT_JWT` using a different GitHub account:
```shell
RECIPIENT_JWT=$(curl -s "http://localhost:1337/api/auth/github/callback?access_token=GITHUB_CODE" \
  | grep -o '"jwt":[^,]*' \
  | cut -d':' -f2 \
  | tr -d '"')

echo "$RECIPIENT_JWT" > .secret
```

3. `OTHER_JWT`:
Repeat the same procedure for `OTHER_JWT` using a different GitHub account:
```shell
OTHER_JWT=$(curl -s "http://localhost:1337/api/auth/github/callback?access_token=GITHUB_CODE" \
  | grep -o '"jwt":[^,]*' \
  | cut -d':' -f2 \
  | tr -d '"')

echo "$OTHER_JWT" > .secret
```

Next load the tokens inside `.secret` into your shell so that you can access them as environment variables (OS):
```shell
export $(cat .secret | xargs)
```

#### Test 0: Load the list of notes for the owner

Load the lists of notes for the owner. We do this so as to retrieve `documentId`'s to pick a note we want to share.

Run the following command in your terminal and store the result in a file named `temp.json`:
```shell
curl -X GET "http://localhost:1337/api/notes" \
  -H "Authorization: Bearer $OWNER_JWT > temp.json
```

You should get the following response. A list of notes for the user together with the `documentId`'s:
<!-- Add Response -->
```json
```

#### Test 1: Owner can still view their notes

Install `jq` to make the following process smoother:
```shell
sudo apt update
sudo apt install jq -y
```

Retrieve a `documentId` from one of the notes in the previous step in the file named `temp.json`. This note will be shared.
```shell
NOTE_ID=$(jq -r '.data[0].documentId' temp.json)
```

Assign the value of the `documentId` to a variable named `NOTE_ID`  write it to the `.secret` file:
```shell
echo "NOTE_ID=$NOTE_ID" > .secret
```

Load `.secret` into the shell:
```shell
export $(cat .secret | xargs)
```

Run the following command in your terminal:
```shell
curl -X GET "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $OWNER_JWT"
```

You should see the full note data in the response.
<!-- Add Response -->
```json
```

#### Test 2: Share a note with another user

Retrieve the email of the user with `RECIPIENT_TOKEN` you want to share the note with:
```shell
curl -s -X GET http://localhost:1337/api/users/me \
  -H "Authorization: Bearer $RECIPIENT_TOKEN" \
  -o temp.json
```

You should get the following result:
```json

```

Extract email from the `temp.json` file:
```shell
RECIPIENT_EMAIL=$(jq -r '.email' temp.json)
```

 `RECIPIENT_EMAIL` is where you will store the value of the recipient's email inside `.secret`:
```shell
echo "RECIPIENT_EMAIL=$RECIPIENT_EMAIL" > .secret
```

Load `.secret` into the shell:
```shell
export $(cat .secret | xargs)
```

Run the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/share" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$RECIPIENT_EMAIL\"}"
```

You should get the following response:
<!-- Add Response -->
```json
```

You should see the message, "Note shared successfully".

#### Test 3: Recipient can view the shared note

Run the following command in your terminal as the user with the recipient token `RECIPIENT_JWT`:
```shell
curl -X GET "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $RECIPIENT_JWT"
```

You should get the following response:
<!-- Add Response -->
```json
```

This means that the recipient user can view the shared note.

#### Test 4: Non-shared user cannot view the note

Try to access the note as the non-shared user with token: `OTHER_JWT`:
```shell
curl -X GET "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $OTHER_JWT"
```

You should receive a 404 error: "Note not found or you do not have access"
<!-- Add Response -->
```json
```

## Implement Unshare Endpoint

Now we need to add functionality to remove shared access from a user. This allows the note owner to revoke access to a previously shared note.

### Add Unshare Route

Open the `src/api/note/routes/note.js` file and add a new route for unsharing notes:
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
            path: '/notes/:id/unshare',
            handler: 'note.unshare',
            config: {
                policies: [],
                middlewares: [],
            },
        },
    ],
};
```

### Implement Unshare Controller Method

Open the `src/api/note/controllers/note.js` file and add the `unshare` method after the `share` method:

```js
async unshare(ctx) {
    const user = ctx.state.user;
    const { id } = ctx.params;
    const { email } = ctx.request.body;

    const note = await strapi.documents('api::note.note').findOne({
        documentId: id,
        filters: { user: user.id },
    });

    if (!note) {
        return ctx.notFound('Note not found or you do not have permission to unshare it');
    }

    const sharedWith = note.sharedWith || [];
    const updateSharedWith = sharedWith.filter(share => share.email !== email);

    if (sharedWith.length === updatedSharedWith.length) {
        return ctx.badRequest('Note was not shared with this email');
    }

    const isShared = updatedSharedWith.length > 0;

    await strapi.documents('api::note.note').update({
        documentId: id,
        data: {
            sharedWith: updatedSharedWith,
            isShared: isShared,
        },
    });

    ctx.send({
        message: 'Note unshared successfully',
        unsharedFrom: email,
    });
},
````

### How the Unshare Flow works

* Owner clicks "Unshare" on a note that has been shared
* Owner selects the email to remove access from
* Request sent to `/api/notes/{documentId}/unshare` with the email
* Strapi verifies the note belongs to the requesting user
* Strapi filters out the emaiil from the `sharedWith` array
* Strapi checks if there are any remaining shared users
* If no users remain, `isShared: false`
* If users remain, `isShared: true`
* Strapi updates the note with the new sharing status
* User with removed access can no longer view the note

### Test the Unshare Endpoint

#### Add User Tokens

We will use the JWTs we added when testing shared note access in the previous step. The tokens we will use are `OWNER_JWT` and `RECIPIENT_JWT`.

We will also use the `documentId` for the note we shared in the previous step stored as `NOTE_ID` in the `.secret` file.

We will assume that the note is still shared.

Load `.secret` into the shell:
```shell
export $(cat .secret | xargs)
```

#### Test 1: Unshare a note from a user

Run the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/unshare" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$RECIPIENT_EMAIL\"}"
```

You should get the following response:
<!-- Add Response -->
```json
{
  "message": "Note unshared successfully",
  "unsharedFrom": "recipient@example.com"
}
```

#### Test 2: Verify the user can no longer access the note

Try to access the note as the use who was just unshared:
```shell
curl -X GET "http://localhost:1337/api/notes/YOUR_NOTE_ID" \
  -H "Authorization: Bearer $RECIPIENT_JWT"
```

You should receive a 404 error: "Note not found or you do not have access"

<!-- Add Response -->

#### Test 3: Try to unshare from an email that wasn't shared

Run the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/YOUR_NOTE_ID/unshare" \
  -H "Authorization: Bearer $OWNER_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"email": "never-shared@example.com"}'
```

<!-- Add Response -->

#### Test 4: Verify `isShared` flag updates correctly

Share a note with two users. We will share with the users with `RECIPIENT_TOKEN` and the user with `OTHER_TOKEN`, we created earlier. We already have the email for the user with the `RECIPIENT_TOKEN` accessed as `RECIPIENT_EMAIL`. We just need to get the email for the user with `OTHER_TOKEN` and store it as `OTHER_EMAIL`.

The note we will use is the one stored as `NOTE_ID` in `.secret`.

Retrieve the email of the user with `OTHER_TOKEN` you want to share the note with:
```shell
curl -s -X GET http://localhost:1337/api/users/me \
  -H "Authorization: Bearer $OTHER_TOKEN" \
  -o temp.json
```

You should get the following result:
```json

```

Extract email from the `temp.json` file:
```shell
OTHER_EMAIL=$(jq -r '.email' temp.json)
```

 `OTHER_EMAIL` is where you will store the value of the other user's email inside `.secret`:
```shell
echo "OTHER_EMAIL=$OTHER_EMAIL" > .secret
```

Load `.secret` into the shell:
```shell
export $(cat .secret | xargs)
```

Share to User 1 (`RECIPIENT_EMAIL`):
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/share" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$RECIPIENT_EMAIL\"}"
```

Share to User 2 (`OTHER_EMAIL`):
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/share" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$OTHER_EMAIL\"}"
```

Unshare from User 2 (`OTHER_EMAIL`):
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/unshare" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$OTHER_EMAIL\"}"
```

<!-- Add response -->
```json
```

Perform a `findOne` request for the note:
```shell
curl -X GET http://localhost:1337/api/notes/$NOTE_ID \
  -H "Authorization: Bearer $OWNER_JWT" \
```

<!-- Add response -->
```json
```

The note should still have `isShared: true` since it's still shared with `RECIPIENT_EMAIL` user.

## Implement "Add Editor" feature in Strapi in backend

Now we need to add functionality to promote a user from read-only access to editor access. This allows the note owner to grant edit permissions to users who already have shared access to the note.

### Add Editor Route

Open the `src/api/note/routes/note.js` file and add a new route for adding editors:
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
      path: '/notes/:id/unshare',
      handler: 'note.unshare',
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

### Implement Add Editor Controller Method

Open the `src/api/note/controllers/note.js` file and add the `addEditor` method after the `unshare` method:
```js
async addEditor(ctx) {
  const user = ctx.state.user;
  const { id } = ctx.params;
  const { email } = ctx.request.body;

  const note = await strapi.documents('api::note.note').findOne({
    documentId: id,
    filters: { user: user.id },
  });

  if (!note) {
    return ctx.notFound('Note not found or you do not have permission to add editors');
  }

  const sharedWith = note.sharedWith || [];
  const sharedUser = sharedWith.find(share => share.email === email);

  if (!sharedUser) {
    return ctx.badRequest('Note is not shared with this email. Share the note first before adding as editor.');
  }

  if (sharedUser.canEdit) {
    return ctx.badRequest('User already has edit permissions');
  }

  sharedUser.canEdit = true;

  await strapi.documents('api::note.note').update({
    documentId: id,
    data: {
      sharedWith: sharedWith,
    },
  });

  const editLink = `${process.env.APP_URL}/notes/${id}`;
  await sendEmail({
    to: email,
    subject: `You can now edit "${note.title}"`,
    html: `
      <h2>Edit Permission Granted</h2>
      <p><strong>${user.username}</strong> has granted you permission to edit the note "<strong>${note.title}</strong>".</p>
      <p><a href="${editLink}">Click here to edit the note</a></p>
    `,
  });

  ctx.send({
    message: 'Editor added successfully',
    editor: email,
  });
},
```

#### How the Add Editor Flow Works

1. Owner clicks "Add Editor" on a shared note.
2. Owner selects the email to grant edit permissions.
3. Request sent to `/api/notes/{documentId}/add-editor` with the email
4. Strapi verifies the note belongs to the requesting user
5. Strapi checks if the note is already shared with that email
6. If not shared: Returns error "Share the note first"
7. If already has edit permission: Returns error "User already has edit permissions"
8. Strapi updates the user's `canEdit` flag to `true`
9. Strapi sends email notification to the editor
10. Editor can now edit the note

### Test the Add Editor Endpoint

For these tests we will use the users and variables we added earlier in the `.secret` file.

Load the `.secret` file to the shell:
```shell
export $(cat .secret | xargs)
```

#### Test 1: Try to add editor without sharing first

Run the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/add-editor" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$OTHER_EMAIL\"}"
```

<!-- Add Response -->
```json
```

You should receive a `400` error: "Note is not shared with this email. Share the note first before adding as editor."

#### Test 2: Share a note, then add as editor

<!--
First share the note by running the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/YOUR_NOTE_ID/share" \
  -H "Authorization: Bearer $OWNER_JWT_TOKEN" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$OTHER_EMAIL\"}"
```
-->

Add the user with view access as an editor by running the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/add-editor" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$RECIPIENT_EMAIL\"}"
```

You should receive a response:
```json
{
  "message": "Editor added successfully",
  "editor": "editor@example.com"
}
```

And the editor should receive an email notification.
<!-- Add Screenshot -->

#### Test 3: Try to add the same editor twice

Run the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/add-editor" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d -d "{\"email\": \"$RECIPIENT_EMAIL\"}"
```

You should receive a 400 error: "User already has edit permissions"

<!-- Add response -->
```json
```

## Update the Update Controller to Allow Editors

Currently, only the note owner can update a note. We need to modify the `update` controller to also allow users who have been granted editor permissions to update the note.

### Update the Update Method

Open the `src/api/note/controllers/note.js` file and update the `update` method:
```js
async update(ctx) {
    const user = ctx.state.user;
    const { id } = ctx.params;
    const { title, content } = ctx.request.body.data || ctx.request.body;

    //Fetch the note without filtering by owner
    const existingNote = await strapi.documents('api::note.note').findOne({
        documentId: id,
        populate: ['user'],
    });

    if (!existingNote) {
        return ctx.notFound('Note not found');
    }

    //Check if user is the owner
    const isOwner = existingNote.user.id === user.id;

    //Check if user had edit permissions
    const sharedWith = existingNote.sharedWith || [];
    const sharedUser = sharedWit.find(share => share.email === user.email);
    const hasEditPermission = sharedUser && sharedUser.canEdit;


    //Allow update if user is owner OR has edit permission
    if (!isOwner && !hasEditPermission) {
        return ctx.forbidden('You do not have permission to update this note');
    }


    const UpdatedNote = await strapi.documents('api::note.note').update({
        documentId: id,
        data: {
            title,
            content,
            user: existingNote.user.id,
            isShared: existingNote.isShared,
            sharedWith: existingNote.sharedWith,
        },
        populate: ['user'],
    });

    ctx.body = updatedNote;
},
```

### How the Updated Flow Works

* User attempts to update a note
* Strapi fetches the note without filtering by owner
* Strapi checks if the user is the owner by comparing user IDs
* Strapi checks if the user has edit permission by looking in the `sharedWith' array for their email with `canEdit: true`
* If user is owner OR has edit permission: Allow the update
* if neither condition is true: Return `403` HTTP Forbidden error "You do not have permission to update this note"
* Strapi updates the note while preserving ownership, sharing status, and shared users list

### Test Editor Update Permissions

For these tests we will use the users and variables we added earlier in the `.secret` file.

Load the `.secret` file to the shell:
```shell
export $(cat .secret | xargs)
```

#### Test 1: Owner can still update their notes

Run the following command in your terminal:
```shell
curl -X PUT "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated by owner",
      "content": "Owner updated content"
    }
  }'
  ```

  The note should be updated successfully.

  <!-- Add Response -->

  #### Test 2: Editor can update the shared note

  We will try update as an editor to a note we added an editor for in the previous test for adding editors.

  Run the following command in your terminal:
  ```shell
  curl -X PUT "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $RECIPIENT_JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Updated by editor",
      "content": "Editor updated content"
    }
  }'
  ```

  The note should be updated successfully.
  <!-- Add Response -->

<!-- Test read-only attempts to update -->
<!-- Test non-shared user attempts to update -->

## Implement Remove Editor Endpoint

Now we need to add functionality to revoke edit permissions from a user while maintaining their read-only access. This allows the note owner to downgrade an editor back to viewer status.

### Add Remove Editor Route

Open the `src/api/note/route/note.js` file and add a new route for removing editors:

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
            path: '/notes/:id/unshare',
            handler: 'note.unshare',
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
            }
        },
        {
            method: 'POST',
            path: '/notes/:id/remove-editor',
            handler: 'note.removeEditor',
            config: {
                policies: [],
                middlewares: [],
            }
        }
    ]
}
```

### Implement Remove Editor Controller Method

Open the `src/api/note/controllers/note.js` file and add the `removeEditor` method after the `addEditor` method:

```js
async removeEditor(ctx) {
    const user = ctx.state.user;
    const { id } = ctx.params;
    const { email } = ctx.request.body;

    const note = await strapi.documents('api::note.note').findOne({
        documentId: id,
        filters: { user: user.id },
    });

    if (!note) {
        return ctx.notFound('Note not found or you do not have permission to remove editors');
    }

    const sharedWith = note.sharedWith || [];
    const sharedUser = sharedWith.find(share => share.email === email);

    if (!sharedUser) {
        return ctx.badRequest('Note is not shared with this email');
    }

    if (!sharedUser.canEdit) {
        return ctx.badRequest('User does not have permissions');
    }

    sharedUser.canEdit = false;

    await strapi.documents('api::note.note').update({
        documentId: id,
        data: {
            sharedWith: sharedWith,
        },
    });

    ctx.send({
        message: 'Editor permissions removed successfully',
        user: email,
    })
}
```

### How the Remove Editor Flow works

* Owner clicks "Remove Editor" on a note that has editors
* Owner selects the email to revoke edit permissions from
* Request sent to `/api/notes/{documentId}/remove-editor` with the email
* Strapi verifies the note belongs to the requesting user
* Strapi checks if the note is shared with that email
* If not shared: Returns error "Note is not shared with this email"
* If user doesn't have edit permission: Returns error "User does not have edit permissions"
* Strapi updates the user's `canEdit` flag to `false`
* User retains read-only access but can no longer edit the note.

### Test the Remove Editor Endpoint

For these tests we will use the users and variables we added earlier in the `.secret` file.

The `.secret` file has the following values:
```
NOTE_ID=<documentId>
OWNER_JWT=<ownertoken>
OWNER_EMAIL=<owneremail>
OTHER_JWT=<otherusertoken>
OTHER_EMAIL=<otheruseremail>
RECIPIENT_JWT=<recipienttoken>
RECIPIENT_EMAIL=<recipientemail>
```

Load the `.secret` file to the shell:
```shell
export $(cat .secret | xargs)
```

- `NOTE_ID` is the `documentId` of the note which has been given edit access
- `OWNER_JWT` is the JWT for the owner of the note which has an editor added.

>**NOTE:**
>*Replace the values in the `.secret` file with the actual values for your users*

These tests assume you executed the other tests in the previous steps

#### Test 1: Remove Editor permissions from an editor

In this step we will remove an editor we added in the previous steps with the email of `RECIPIENT_EMAIL`.

Run the following command in your terminal:
```shell
curl -X POST "http://localhost:1337/api/notes/$NOTE_ID/remove-editor" \
  -H "Authorization: Bearer $OWNER_JWT" \
  -H "Content-Type: application/json" \
  -d "{\"email\": \"$RECIPIENT_EMAIL\"}"
```

You should receive a response like this:
```json
{
  "message": "Editor permissions removed successfully",
  "user": "$RECIPIENT_EMAIL"
}
```

#### Test 2: Verify user can still view but not edit

Try to view the note as the former editor
```shell
curl -X GET "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $RECIPIENT_JWT"
```

The note should be returned successfully (read access maintained)
<!-- Add Response -->
```json
```

Try to update the note as the former editor:
```shell
curl -X PUT "http://localhost:1337/api/notes/$NOTE_ID" \
  -H "Authorization: Bearer $RECIPIENT_JWT" \
  -H "Content-Type: application/json" \
  -d '{
    "data": {
      "title": "Trying to update",
      "content": "Should fail"
    }
  }'
```

You should receive a `403` error: "You do not have permission to update this note"
<!-- Add Response -->
```json
```

<!-- Test 3: Try to remove editor from a read-only user -->
<!-- Test 4: Try to remove editor from non-shared user -->