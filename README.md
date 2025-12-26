# Build a Note Sharing app with Social Authentication using Nuxt and Strapi

## Introduction

This guide is about building a Note sharing app with social login using Nuxt and Strapi.

The building of the app will be split into multiple phases. It will start as a Note taking app with no authentication. It will move on to becoming a Note taking with authentication or registered users. The next phase will then see the app become a Note sharing app. Addition of the Note sharing features will be done in phases as well. Note sharing can begin with sharing with read access for everyone and then slowly advance in phases to a users requesting edit access.

Here is a list of the features which will be included in the app:
* Creating notes
* Reading notes
* Updating notes
* Deleting notes
* User account creation
* Logging in and logging out of your Notes app
* Sharing of notes

Here is a list of some resources you will find helpful as an aid to the guide:
- Demo video []()
- GitHub repo with source code []()
- Live link to the working app []()

## Prerequisites

To follow along with the guide you will need:
* Knowledge of Strapi. Check out the [Quickstart guide]() to get started.
* Knowledge of HTML, CSS, JavaScript, Nuxt, and Vue.js
* Node.js installed on your machine. (latest LTS version). [Download Node.js]()
* Strapi Cloud account. [Sign up here]()
* GitHub account. [Sign up here]()
* Facebook developer account. [Sign up here]()
* Brevo account. [Sign up here]()

<!-- ## Steps to be taken -->

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
* Another path for confirming the note has been created containing the link to the created note `pages/notes/created.vue` and,
* A third path for the created note itself (where we can view the contents of the created note). This one is dynamically generated and we have already created it: `pages/notes/[id].vue`.

The link to the "Create Note" page/path should be available in the Notes List page right underneath the list of notes. Once a user clicks the button, it should lead them to the "Create Note" page.

Within the "Create Note" page, there should be two form text input fields. One for entering the title. The other for entering the content or body of the note. And a "Create Note" button right below the form inputs. Once the user clicks the button, it should lead them to the "Created Note" page. 

Within the "Created Note" page, the user should see that the note was created. If the note was created successfully, a message should appear confirming the success including the link to the note. 

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
- Handle Redirect - On Success: The Nuxt server route should issue a `302` HTTP status code for Redirect and redirect the user to the `notes/created` route. The `documentId` of the newly created note will be appended as a query parameter so that the success page knows what to display (e.g., `sendRedirect(event, 'notes/created?status=success&id=' + response.data.documentId)`)

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
        //Send data to Strapi
        const strapiResponse = await $fetch('http://localhost:1337/api/notes', {
            method: 'POST',
            body: {
                data: {
                    title: body.title,
                    content: body.content
                }
            }
        })

        //Handle Success
        if (strapiResponse.data) {
            const noteId = strapiResponse.data.documentId
            return sendRedirect(event, `/notes/created?success=1&id=${noteId}`)
        }

        // Fallback if no data returned
        return sendRedirect(event, '/notes/created?success=0')
    
    } catch (error) {
        // Handle Error
        console.error('Failed to create a note:', error)
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

     <!-- Success State -->
     <div v-if="isSuccess">
        <h2>Note Created Successfully!</h2>
        <p>Your note has been saved to the database</p>

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

        <NuxtLink to="/notes/create">
            <button>Try Again</button>
        </NuxtLink>

        <NuxtLink to="/notes">Back to Notes List</NuxtLink>
     </div>
</template>
```

The page is rendered on the server. Nuxt looks at the URL and retrieves the value of the `success` variable as well as the value of the `id`. For example if the URL is `http://localhost:3000/notes/created?success=1&id=abc1234`, `route.query.success` will be `1` and the `route.query.id` will be `abc1234`.

The `v-if` code block evaluates the status of the `isSuccess` variable.
* if `true`: The server generates the HTML containing the "Success" headers and the link to `/notes/abc1234`. The "Failure state" div is completely removed from the HTML sent to the browser.
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
    <ul>
        <li v-for="note in notes.data">
            <h3>{{ note.title }}</h3>
            <NuxtLink :to="`/notes/${note.documentId}`">
                <button>View Note</button>
            </NuxtLink>
        </li>
    </ul>

    <NuxtLink to="/notes/create">
        <button>Create a New Note</button>
    </NuxtLink>
    <br />
    <br />
    <NuxtLink to="/">Back to Home</NuxtLink> 
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
![Created Note](https://res.cloudinary.com/craigsims808/image/upload/v1765350048/strapi/sasn/07-created-note_ylpimj.png)

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

<!-- End of Video -->

<!--
Beginning of New Video
- Nuxt, Strapi are running (Port forward Strapi)
- Check `.secret`, github provider, 
-->

### Add User from User Permissions Plugin with "Many to One" relation field

Make sure your Strapi server is running then log in your Strapi Admin Dashboard. Click **Content-Type Builder** and select the **Note** collection.

Click **Edit** to add a new field to the **Note** collection.

Add a `Relations` field whereby `User (from: user-permissions-user)`, then click on `Users have many notes` and name it `user`.
![Notes collection relation field in Content Type Builder](https://res.cloudinary.com/craigsims808/image/upload/v1766202233/strapi/sasn/24-user-relation-field-in-notes-collection_fkce81.png)

These are the current fields in the Note collection: `title`, `content` , and user.
![Current Notes collection in Content-Type Builder](https://res.cloudinary.com/craigsims808/image/upload/v1766202606/strapi/sasn/19-update-notes-collection-with-user-relation-field_kq98az.png)


In the **Content Manager**, select one note and you will notice that the appearance of a user drop-down.
![Updated Notes collection entry showing user, title, and content](https://res.cloudinary.com/craigsims808/image/upload/v1766204529/strapi/sasn/20-notes-entry-with-title-content-user_yucysu.png)

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
![Two users in User collection](https://res.cloudinary.com/craigsims808/image/upload/v1766200546/strapi/sasn/22-two-users-in-user-collection-list_foxs51.png)

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

You should get the list of all notes as the response.

<!-- 
At least two users. Each user has their own notes 
markmunyaka@gmail.com
craigsimakando@gmail.com

USER added off screen
-->

### Add notes for newly added user

Visit the Content Manager in your Strapi Dashboard. Assign some notes to the newly created user you just added.
![User collection view for new user with notes assigned to user](https://res.cloudinary.com/craigsims808/image/upload/v1766200938/strapi/sasn/21-user-entry-with-assigned-notes_mrvrpk.png)

The objective is to have some notes for each user as seen in the Note collection entries' view.
![Note collection entries with notes for both users](https://res.cloudinary.com/craigsims808/image/upload/v1766201159/strapi/sasn/23-notes-collection-entries-with-both-users-assigned-notes-each_r2x98g.png)

### Test `find`for Authenticated Users

If we tested the `find` method right now using one of the authenticated users, we will discover that the response will include all notes in the database. Even the ones that don't belong to the authenticated user. That is undesirable.

Perform a `find` request as an Authenticated User:
<!-- TODO: Consider redoing the request with the user field populated. Check Strapi docs. You may have to comment out the `find` method override -->
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
<!-- TODO: Consider redoing the request with the user field populated. Check Strapi docs -->
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

You will then get an HTTP `404` 'Not Found' error code as the redirect page: "http://localhost:3000/auth/callback" does not exist.
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

Create a new folder named `routes/auth` inside the `server` folder:
```shell
mkdir -p server/routes/auth
```

Create a file named `callback.get.js` inside the `server/api/auth` directory:
```shell
touch server/routes/auth/callback.get.js
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
5. Strapi exchanges the code for an `access_token` with GitHub
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
5. You should be redirected directly to `/notes`. No notes will appear, instead you will get a Server Error.
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
    <li v-for="note in notes" :key="note.id">
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

## File Upload

In this phase of the project we will add the ability to add a file attachment to a note. Thus when a user creates or edits a note, they can add/upload a file to the note. The idea here is that once a user attachs a file, it is uploaded to the Strapi server and stored using Strapi's **Media Library** plugin.

Of course, a new field would need to be added to the Notes collection in the Strapi Dashboard. The name of the field can be `attachment` and the type for the field should be a `url`. Why a URL? That's because we need to use the URL of the file stored in the Strapi backend. The idea here is that once a user clicks the link to the attachment in the View Note page then they can download it to their machine.

For this to work, in the Nuxt UI, the "View Note" page will need to be updated with an "Attachment" section which sits right below the content of the note. Within the "Attachment" section, there should be a URL/link to the attachment which has been added. Amongst the buttons like "Share", "Add Editor", "Request Edit", "Edit Note", and "Delete Note" there should be a "Attach a file" button.

When a user clicks the "Attach a file" button it should lead them to the "File Upload" page. In the "File Upload" page, a form field of `input` type being `file`. A label with the message "Attach a file to your note" should be there as well. A user chooses a file from their local device storage. Once the file is chosen, the user then clicks a "Submit" button. This submits the form to a Nuxt server route.

The Nuxt server route will read the body from the form data then make an upload to the Strapi server. It will then retrieve the link to the attachment from the response. This link will be used to update the `attachment` field of the note. On receiving the response, the server route should redirect the user to the confirmation page.

The confirmation page will display the success message of the file upload with the link to the file as well as the link to the note.

### Add Attachment Field to Notes Collection

Make sure your Strapi server is running then log in to your Strapi Admin Dashboard. Click **Content-Type Builder** and select the **Note** collection.

Click **Add another field** to add a new field to the **Note** collection.

Select **Text** as the field type, then choose **Short text**.
<!-- Change to url type -->

Name the field `attachment` and click **Finish**.
<!-- Add Screenshot -->

Click **Save** to apply the changes to the Note collection.

Your Strapi server will restart automatically to apply the schema changes.

### Update Public and Authenticated User Permissions for Upload

Now we need to configure permissions to allow file uploads through Strapi's Upload plugin.

#### Configure Authenticated User Permissions for Upload

Go to **Settings** → **Users & Permissions** → **Roles** → **Authenticated**.

Scroll down to find the **Upload** plugin section.

Select the following permissions for the Authenticated role:
- `upload` - Allows users to upload files
<!-- Add Screenshot -->

Click **Save** to apply the changes.

#### Verify Public User Permissions

Go to **Settings** → **Users & Permissions** → **Roles** → **Public**.

Ensure that no Upload permissions are enabled for Public users, as file uploads should only be available to authenticated users.
<!-- Add Screenshot -->

### Test File Upload Endpoint

Before integrating file uploads into the main app, let's verify that file uploads work correctly with Strapi's Upload plugin.

#### Test File Upload

First, create a test file to upload. You can use any small file (image, PDF, text file, etc.).

Upload a file to Strapi using curl:
```shell
curl -X POST "http://localhost:1337/api/upload" \
-H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
-F "files=@/path/to/your/test-file.pdf"
```

Replace `/path/to/your/test-file.pdf` with the actual path to your test file.

If successful, you should receive a response like:
```json
[
    {
        "id": 1,
        "name": "test-file.pdf",
        "alternativeText": null,
        "caption": null,
        "width": null,
        "height": null,
        "formats": null,
        "hash": "test_file_a1b2c3d4e5",
        "ext": ".pdf",
        "mime": "application/pdf",
        "size": 123.45,
        "url": "/uploads/test_file_a1b2c3d4e5.pdf",
        "previewUrl": null,
        "provider": "local",
        "provider_metadata": null,
        "createdAt": "2025-12-18T10:30:00.000Z",
        "updatedAt": "2025-12-18T10:30:00.000Z"
    }
]
```

Copy the `url` value from the response (e.g., `/uploads/test_file_a1b2c3d4e5.pdf`). This is the relative URL to the uploaded file.

#### Test Updating Note with Attachment URL

Now update one of your notes to include the attachment URL:
```shell
curl -X PUT "http://localhost:1337/api/notes/$NOTE_ID" \
-H "Content-Type: application/json" \
-H "Authorization: Bearer $GITHUB_JWT_TOKEN" \
-d '{
      "data": {
         "attachment": "http://localhost:1337/uploads/test_file_a1b2c3d4e5.pdf"
      }
     }'
```

If successful, you should receive a response showing the updated note with the attachment field populated:
```json
{
    "data": {
        "id": 1,
        "documentId": "ia21j5a0o6muvwf0nvegce7d",
        "title": "1 First Note",
        "content": "This is content for the first note.",
        "attachment": "http://localhost:1337/uploads/test_file_a1b2c3d4e5.pdf",
        "createdAt": "2025-12-02T14:14:11.679Z",
        "updatedAt": "2025-12-18T10:35:00.000Z",
        "publishedAt": "2025-12-18T10:35:00.000Z"
    },
    "meta": {}
}
```

#### Verify File is Accessible

Visit the uploaded file in your browser to confirm it's accessible:
```
http://localhost:1337/uploads/test_file_a1b2c3d4e5.pdf
```

You should be able to view or download the file.

#### Check Media Library

Open your Strapi Admin Dashboard and navigate to **Media Library**. You should see your uploaded test file listed there
<!-- Add Screenshot -->

### Update Individual Note Page to Display Attachment

Now we need to update the individual note page to display the attachment link when a file has been uploaded to the note.

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
  
  <!-- Attachment section -->
  <div v-if="note.data.attachment">
    <h3>Attachment</h3>
    <a :href="`{note.data.attachment}`" target="_blank">
      <button>Download Attachment</button>
    </a>
  </div>
  
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

#### How This Works

The page now includes an "Attachment" section that:
- Only displays if the note has an attachment (`v-if="note.data.attachment"`)
- Shows a "Download Attachment" button that links to the file
- Opens the file in a new tab (`target="_blank"`)

If you updated a note with an attachment in the previous testing step, you can now visit that note in your browser at `http://localhost:3000/notes/NOTE_ID` and you should see the "Attachment" section with the download button.
<!-- Add Screenshot -->

### Update Individual Note Page with Attach File Button

Now we'll add an "Attach File" button to the individual note page that allows users to upload and attach a file to their note.

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
  
  <!-- Attachment section -->
  <div v-if="note.data.attachment">
    <h3>Attachment</h3>
    <a :href="`http://localhost:1337${note.data.attachment}`" target="_blank">
      <button>Download Attachment</button>
    </a>
  </div>
  
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
  <NuxtLink :to="`/notes/attach-file?id=${route.params.id}`">
    <button>Attach File</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

The "Attach File" button is now positioned between the "Add Editor" and "Delete Note" buttons, allowing users to upload and attach files to their notes.

### Create File Upload Page

Inside the `pages/notes` directory, create a new file named `attach-file.vue`:

```shell
touch pages/notes/attach-file.vue
```

Add the following code to `pages/notes/attach-file.vue`:

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
  
  <h2>Attach a File</h2>
  
  <h3>{{ note.data.title }}</h3>
  <p>{{ note.data.content }}</p>
  
  <p>Select a file to upload and attach to this note:</p>
  
  <!-- Note the enctype="multipart/form-data" required for file uploads -->
  <form action="/api/notes/attach-file" method="POST" enctype="multipart/form-data">
    <input type="hidden" name="noteId" :value="noteId" />
    
    <div>
      <label for="file">File:</label>
      <input type="file" name="file" id="file" required />
    </div>
    <br>
    
    <button type="submit">Upload File</button>
  </form>
  
  <br>
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Cancel</button>
  </NuxtLink>
</template>
```

This page displays:
- The note's title and content to confirm which note gets the attachment
- A file input field (`type="file"`) allowing the user to select a file from their device
- A form configured with `enctype="multipart/form-data"`, which is necessary for sending binary file data to the server
- A "Upload File" button to submit the form
- A "Cancel" button to return to the note view without uploading


### Create Nuxt Server Route for File Upload

We need a server route that can handle `multipart/form-data`, upload the file to Strapi's media library, and then update the specific note record with the resulting file URL.

Create a file named `attach-file.post.js` inside the `server/api/notes` directory:

```shell
touch server/api/notes/attach-file.post.js
```

Add the following code to `server/api/notes/attach-file.post.js`:

```js
export default defineEventHandler(async (event) => {
  // 1. Parse the multipart form data coming from the browser
  const items = await readMultipartFormData(event)

  // Extract the noteId and the file from the parsed data
  const noteIdItem = items.find(item => item.name === 'noteId')
  const fileItem = items.find(item => item.name === 'file')

  const noteId = noteIdItem.data.toString()
  const token = getCookie(event, 'auth_token')

  // 2. Prepare FormData to send to Strapi
  const strapiFormData = new FormData()

  // Convert the file buffer to a Blob so FormData accepts it
  const fileBlob = new Blob([fileItem.data], { type: fileItem.type })

  // 'files' is the key Strapi expects for the upload endpoint
  strapiFormData.append('files', fileBlob, fileItem.filename)

  // 3. Upload the file to Strapi
  const uploadResponse = await $fetch('http://localhost:1337/api/upload', {
    method: 'POST',
    headers: {
      Authorization: `Bearer ${token}`
    },
    body: strapiFormData
  })

  // Strapi returns an array of uploaded files. We take the first one.
  const uploadedFile = uploadResponse[0]
  const fileUrl = uploadedFile.url

  // 4. Update the Note with the attachment URL
  await $fetch(`http://localhost:1337/api/notes/${noteId}`, {
    method: 'PUT',
    headers: {
      Authorization: `Bearer ${token}`,
      'Content-Type': 'application/json'
    },
    body: {
      data: {
        attachment: fileUrl
      }
    }
  })

  // 5. Redirect to success page
  return sendRedirect(event, `/notes/attach-success?success=1&id=${noteId}`)
})

```

#### How This Works

1.  **Parsing:** The route uses `readMultipartFormData` to parse the incoming binary stream. This breaks the request down into an array of items (fields and files).
2.  **Extraction:** We locate the `noteId` text field and the `file` buffer from that array.
3.  **Re-packaging:** To forward the file to Strapi, we create a new server-side `FormData` instance. We convert the raw buffer from the browser request into a `Blob` and append it under the key `files`, which Strapi's upload plugin requires.
4.  **Upload:** We send a POST request to Strapi's `/api/upload` endpoint. Note that we do **not** manually set `Content-Type: multipart/form-data` in the header; the `$fetch` client handles this automatically to ensure the correct boundary strings are set.
5.  **Linking:** Strapi returns the file details, including its URL. We then make a second request (PUT) to the specific note to update its `attachment` text field with this URL.
6.  **Redirect:** Finally, the user is redirected to a success page.

### Create File Upload Success Page

Create a file named `attach-success.vue` inside the `pages/notes` directory:

```shell
touch pages/notes/attach-success.vue
```

Add the following code to `pages/notes/attach-success.vue`:

```vue
<script setup>
  const route = useRoute()
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

  <h2>File Attached Successfully!</h2>
  <p>Your file has been uploaded and attached to the note.</p>
    
  <NuxtLink :to="`/notes/${noteId}`">
    <button>Back to Note</button>
  </NuxtLink>
    
  <NuxtLink to="/notes">
    <button>Back to Notes List</button>
  </NuxtLink>
</template>
```

This page serves as a confirmation screen. Once the user is redirected here, they are assured that the file upload process completed successfully and the database record has been updated. They can then navigate back to the note to view (and download) the newly attached file.

### Test File Upload Flow

Now that all the pieces are in place, let's test the complete file upload functionality.

Make sure both your Strapi backend (`npm run develop`) and Nuxt frontend (`npm run dev`) are running.

Go to `http://localhost:3000`, log in (if you aren't already), and click "View Note" on one of your existing notes.

You should see the "Attach File" button in the list of actions. Click it.
<!-- Add Screenshot -->

You will be taken to the file selection page. Click "Choose File" (or "Browse") and select a file from your computer (e.g., a PDF or an image). Then click "Upload File".
<!-- Add Screenshot -->

After a brief moment for the upload to process, you should be redirected to the "File Attached Successfully!" page.
<!-- Add Screenshot -->


Click the "Back to Note" button. You should now see a new **Attachment** section appear below the note content.
<!-- Add Screenshot -->


Click the "Download Attachment" button. It should open your uploaded file in a new browser tab.
<!-- Add Screenshot -->

Congratulations! You have successfully implemented file attachments using Nuxt 3's server-side handling and Strapi's Media Library.

## Integrating a Rich Text Editor

Our Notes Sharing app needs a Rich Text editor to enhance the appearance of notes. The Rich text editor of choice for this guide is **Tiptap**. It supports Vue the library which Nuxt is built on. It is also highly customizable and provides great documentation. Check out their [Get Started](https://tiptap.dev/docs/editor/getting-started/overview) page for more details.

### Phase 1: Experiment with Tiptap (Don't touch main app yet!)

Before integrating the rich text editor into your main Notes app, you will create a test page to experiment with Tiptap and understand how it works.

#### Install Tiptap dependencies

Install the required Tiptap packages:
```shell
npm install @tiptap/vue-3 @tiptap/starter-kit
```

The `@tiptap/vue-3` package provides the Vue 3 components and composables for Tiptap, while `@tiptap/starter-kit` includes essential extensions like bold, italic, headings, lists, and more.

#### Create test page for Tiptap

Inside the `pages` directory, create a new file named `test-editor.vue`:
```shell
touch pages/test-editor.vue
```

Add the following code to `pages/test-editor.vue`:
```vue
<script setup>
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import { ref } from 'vue'

const htmlContent = ref('<p>Start typing here...</p>')

const editor = useEditor({
    content: htmlContent.value,
    extensions: [StarterKit],
    onUpdate: ({ editor }) => {
        htmlContent.value = editor.getHTML()
    }
})
</script>

<template>
  <h1>Tiptap Test</h1>

  <div v-if="editor">
    <button @click="editor.chain().focus().toggleBold().run()">Bold</button>
    <button @click="editor.chain().focus().toggleItalic().run()">Italic</button>
  </div>

  <EditorContent :editor="editor" />
  <hr>

  <h3>HTML Output:</h3>
  <pre>{{ htmlContent }}
</template>

<style>
.ProseMirror {
    border: 1px solid #ccc;
    padding: 10px;
    min-height: 150px;
}

.ProseMirror:focus {
    outline: none;
}
</style>
```

#### Test the Tiptap editor

Run the Nuxt development server:
```shell
npm run dev
```

Visit `http://localhost:3000/test-editor` to view your Tiptap test page.
<!-- Add Screenshot -->

Experiment with the editor by:
1. Typing text directly in the editor area
2. Selecting text and clicking Bold or Italic buttons
3. Observing the HTML output below - this is what will be save to Strapi

#### Key Observations

* The editor generates HTML that can be stored in your existing Strapi `content` field of text type.
* No changes needed to your Strapi Content-Type Builder
* The HTML can later be displayed using Vue's `v-html` directive

Once you are comfortable with how Tiptap works, you will be ready to integrate it into your Notes app's create and edit flows.

### Phase 2: Integrate Tiptap into Create Note Flow

Now that you have experimented with Tiptap and understand how it works, you can integrate it iinto your Notes app's create flow.

#### Update `pages/notes/create.vue` with Tiptap editor

Add the following code to `pages/notes/create.vue`:
```vue
<script setup>
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import { ref } from 'vue'

const title = ref('')
const content = ref('')

const editor = useEditor({
    content: content.value,
    extensions: [StarterKit],
    onUpdate: ({ editor }) => {
        content.value = editor.getHTML()
    }
})
</script>

<template>
    <h1>Notes App</h1>
    <h2>Create a New Note</h2>

    <form action="/api/notes/create" method="POST">
        <label for="title">Title:</label>
        <input type="text" name="title" v-model="title" required />

        <label for="content">Content:</label>
        <div v-if="editor">
            <button type="button"
            @click="editor.chain().focus().toggleBold().run()">Bold</button>
            <button type="button"
            @click="editor.chain().focus().toggleItalic().run()">Italic</button>
        </div>

        <EditorContent :editor="editor" />
        <input type="hidden" name="content" :value="content" />

        <button type="submit">Create Note</button>
    </form>
</template>

<style>
.ProseMirror {
    border: 1px solid #CCC;
    padding: 10px;
    min-height: 150px;
}

.ProseMirror:focus {
    outline: none;
}
</style>
```

Key changes from the original `create.vue`:

* **Imported Tiptap**: Added imports for `useEditor`, `EditorContent`, and `StarterKit`.
* **Content ref**: Created a `content` ref to store the HTML generated by the editor
* **Editor initialization**: Set up the editor with `useEditor()`, passing the `StarterKit` extensions and an `onUpdate` callback that captures the HTML.
* **Toolbar buttons**: Added simple Bold and Italic buttons. Note the `type="button"` attribute prevents them from submitting the form.
* **EditorContent component**: Replaced the `<textarea>` with `<EditorContent>` which renders the Tiptap editor.
* **Hidden input**: Added a hidden input field to submit the HTML content to the server. This is necessary because the form submission needs the HTML value.
* **Basic styling**: Added minimal CSS to give the editor a border and remove the focus outline.

THe form still submits to `/api/notes/create` just like before. The HTML content from the editor is passed through the hidden input field.

#### Update Nuxt Internal API route for handling HTML content

The good news is that your existing `server/api/notes/create.post.js` file already handles HTML content perfectly! No changes are needed.

The current code in `server/api/notes/create.post.js` works just fine

#### Test Note Creation with Rich Text

Run the Nuxt development server:
```shell
npm run dev
```

Make sure your Strapi server is running, then visit `http://localhost:3000/notes`. Click on the "Create a New Note" button.

You will be directed to the page for creating notes, `http://localhost:3000/notes/create`. You should now see the Tiptap editor with Bold and Italic buttons above the content area.

![Create Note page with Tiptap]()
<!-- Add Screenshot -->

Enter a title for your note and type some content in the editor. Try using the formatting buttons:
1. Type some text
2. Select a portion of the text
3. Click "Bold" to make it bold
4. Select another portion and click "Italic" to make it italic

Click "Create Note" to create your note. You should be redirected to the `http://localhost:3000/notes/created` page where you will see confirmation of the note's creation.

![Success: Created Note with Rich Text]()
<!-- Add Screenshot -->

Click "View Created Note" to view the note. You'll notice that the formatted text appears, but the HTML tags are visible as plain text instead of being rendered as formatted content.

![Created Note showing HTML tags]()
<!-- Add Screenshot -->

This is expected behavior because we haven't updated the note view page to render HTML yet. The next phase will address this by updating how notes are displayed.

### Phase 3: Integrate Tiptap into Update Note Flow

#### Update `pages/notes/edit.vue` with Tiptap editor

Now you'll integrate Tiptap into the edit flow so users can update their notes with rich text formatting.

Update `pages/notes/edit.vue` with the following code:

```vue
<script setup>
import { useEditor, EditorContent } from '@tiptap/vue-3'
import StarterKit from '@tiptap/starter-kit'
import { ref, watch } from 'vue'

const route = useRoute()
const noteId = route.query.id
const { data: note } = await useFetch(`http://localhost:1337/api/notes/${noteId}`)

const title = ref(note.value.data.title)
const content = ref(note.value.data.content)

const editor = useEditor({
  content: content.value,
  extensions: [StarterKit],
  onUpdate: ({ editor }) => {
    content.value = editor.getHTML()
  }
})

// Update editor content when note data is loaded
watch(() => note.value, (newNote) => {
  if (newNote && editor.value) {
    title.value = newNote.data.title
    editor.value.commands.setContent(newNote.data.content)
  }
})
</script>

<template>
  <h1>Notes App</h1>
  <h2>Edit Note</h2>
  
  <form action="/api/notes/update" method="POST">
    <input type="hidden" name="documentId" :value="noteId" />
    
    <label for="title">Title:</label>
    <input type="text" name="title" id="title" v-model="title" required />
    
    <label for="content">Content:</label>
      
    <div v-if="editor">
      <button type="button" @click="editor.chain().focus().toggleBold().run()">Bold</button>
      <button type="button" @click="editor.chain().focus().toggleItalic().run()">Italic</button>
    </div>

    <EditorContent :editor="editor" />
    <input type="hidden" name="content" :value="content" />
    
    <button type="submit">Update Note</button>
  </form>
</template>

<style>
.ProseMirror {
  border: 1px solid #ccc;
  padding: 10px;
  min-height: 150px;
}

.ProseMirror:focus {
  outline: none;
}
</style>
```

Key changes from the original `edit.vue`:

* **Imported Tiptap**: Added imports for `useEditor`, `EditorContent`, and `StarterKit`.
* **Content ref**: Created a `content` ref initialized with the fetched note's content.
* **Title ref**: Created a `title` ref initialized with the fetched note's title.
* **Editor initialization**: Set up the editor with the existing note content, so users see their current content when editing.
* **Watch for data**: Added a `watch` to update the editor when the note data loads, ensuring the HTML content is properly displayed in the editor.
* **Toolbar buttons**: Added Bold and Italic buttons with `type="button"` to prevent form submission.
* **EditorContent component**: Replaced the `<textarea>` with `<EditorContent>`.
* **Hidden input**: Added a hidden input field to submit the updated HTML content.

The form still submits to `/api/notes/update` just like before, passing the HTML content through the hidden input field.

#### Verify the API route handles HTML content

Your existing `server/api/notes/update.post.js` file already handles HTML content correctly. No changes are needed.

The current code in `server/api/notes/update.post.js` works just fine.

#### Test Note Update with Rich Text

Run the Nuxt development server:
```shell
npm run dev
```

Make sure your Strapi server is running, then visit `http://localhost:3000/notes`. Click on a note that you created earlier (preferably one with rich text formatting).

Click the "Edit Note" button. You will be directed to the edit page at `http://localhost:3000/notes/edit?id=YOUR_NOTE_ID`.

![Edit Note page with Tiptap]()
<!-- Add Screenshot -->

The editor should display your existing note content. If the note has HTML formatting, it will be rendered in the editor (bold text appears bold, etc.).

Make some changes to the content:
1. Add or remove text
2. Apply or remove bold/italic formatting
3. Edit the title if desired

Click "Update Note". You should be redirected to the `http://localhost:3000/notes/edited` page with confirmation of the update.

![Success: Edited Note with Rich Text]()
<!-- Add Screenshot -->

Click "View Edited Note" to see the updated note. Like before, the HTML tags will still be visible as plain text because we haven't updated the display page yet. Phase 4 will fix this.

### Phase 4: Display Rich Content in Note View

#### Update `pages/notes/[id].vue` to render HTML

Now that you can create and edit notes with rich text formatting, you need to update the note view page to properly display the HTML content instead of showing the raw HTML tags.

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
  <div v-html="note.data.content"></div>
  
  <!-- Attachment section -->
  <div v-if="note.data.attachment">
    <h3>Attachment</h3>
    <a :href="`http://localhost:1337${note.data.attachment}`" target="_blank">
      <button>Download Attachment</button>
    </a>
  </div>
  
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
  <NuxtLink :to="`/notes/attach-file?id=${route.params.id}`">
    <button>Attach File</button>
  </NuxtLink>
  <br>
  <NuxtLink :to="`/notes/delete?id=${route.params.id}`">
    <button>Delete Note</button>
  </NuxtLink>
  <br>
  <NuxtLink to="/notes">Back to Notes List</NuxtLink>
</template>
```

Key change from the original `[id].vue`:

* **v-html directive**: Changed `<p>{{ note.data.content }}</p>` to `<div v-html="note.data.content"></div>`. The `v-html` directive renders the HTML string as actual HTML instead of displaying it as plain text.

**Important Security Note**: The `v-html` directive should only be used with trusted content. Since users are creating their own notes in your app, this is safe. However, if you were displaying content from untrusted sources, you would need to sanitize the HTML first to prevent XSS (Cross-Site Scripting) attacks.

#### Test Rich Text Display

Run the Nuxt development server:
```shell
npm run dev
```

Make sure your Strapi server is running, then visit `http://localhost:3000/notes`. 

Click on any note to view it. The note content should now display with proper formatting instead of showing HTML tags.

![Individual Note with Rendered Rich Text]()
<!-- Add Screenshot -->

Your Notes app now fully supports rich text editing! Users can:
* Create notes with bold, italic, and other formatting
* Edit notes while preserving their formatting
* View notes with properly rendered HTML content

All of this works with your existing Strapi setup. No changes to the Content-Type Builder were needed. The HTML is simply stored as text in the `content` field and rendered when displayed.

## Adding “Load More” feature for Notes List

In this section, we’ll enhance the Notes list so that it loads a small set of notes on first render and allows the user to load more on demand. The implementation keeps **SSR for the initial render** and adds **client-side pagination** for the “Load More” interaction, using the existing `pages/notes/index.vue` file as the base.

### Fetch an initial, paginated set of notes (SSR)

Instead of fetching all notes at once, we fetch only the first **5 notes** during server-side rendering. This keeps the page fast and SEO-friendly.

Replace the contents of `pages/notes/index.vue` with the following code:
```vue
<script setup>
import { ref } from 'vue'

const INITIAL_LIMIT = 5
const LOAD_MORE_STEP = 2

// SSR: initial fetch
const { data: notesResponse } = await useFetch('/api/notes', {
  query: {
    'pagination[start]': 0,
    'pagination[limit]': INITIAL_LIMIT
  }
})

// Auth (unchanged)
const { data: user } = await useFetch('/api/auth/me')
```

### Set up client-side pagination state

We initialize client-side state using the data returned from the SSR fetch. This allows us to append more notes later without reloading the page.

Append the following code to `page/notes/index.vue`:
```vue
const notes = ref(notesResponse.value.data)
const total = notesResponse.value.meta.pagination.total
const loadedCount = ref(notes.value.length)
const loading = ref(false)
```

* `notes` holds the currently displayed notes.
* `total` is the total number of notes in Strapi.
* `loadedCount` tracks how many notes are already loaded.
* `loading` prevents duplicate requests.

### Implement the “Load More” handler

When the user clicks “Load More”, we fetch the next batch of notes (2 at a time) and append them to the existing list.

Append the following code to `page/notes/index.vue`:
```vue
const loadMore = async () => {
  if (loading.value || loadedCount.value >= total) return

  loading.value = true

  const { data } = await useFetch('/api/notes', {
    query: {
      'pagination[start]': loadedCount.value,
      'pagination[limit]': LOAD_MORE_STEP
    }
  })

  notes.value.push(...data.value.data)
  loadedCount.value += data.value.data.length
  loading.value = false
}
</script>
```

### Update the template to use the new state

We render notes from the local `notes` array and conditionally show the “Load More” button until all notes are loaded.

Append the following code to `page/notes/index.vue`:
```vue
<template>
  <h1>Notes App</h1>

  <div>
    <p>Logged in as: <strong>{{ user.username }}</strong></p>
    <a href="/api/auth/logout">
      <button>Logout</button>
    </a>
  </div>

  <h2>Notes List</h2>

  <ul>
    <li v-for="note in notes" :key="note.id">
      <h3>{{ note.title }}</h3>
      <NuxtLink :to="`/notes/${note.documentId}`">
        <button>View Note</button>
      </NuxtLink>
    </li>
  </ul>

  <button
    v-if="loadedCount < total"
    @click="loadMore"
    :disabled="loading"
  >
    {{ loading ? 'Loading…' : 'Load More' }}
  </button>

  <NuxtLink to="/notes/create">
    <button>Create a New Note</button>
  </NuxtLink>
</template>
```

### Test "Load More" feature

Open your Strapi Admin Dashboard. Visit the Content Manager and open the **Notes** collection.

Create at least 10 notes for one user, for us to test out the "Load More" feature effectively.
<!-- Add Screenshot -->

Start your Nuxt development server:
```shell
npm run dev
```

Visit the Notes List - `localhost:3000/notes` page in your browser to view the list of notes.

* The page initially renders **5 notes on the server**.
* Each click on **“Load More”** fetches **2 additional notes**.
* The button disappears once all notes are loaded.
* The solution is simple, SSR-friendly, and works cleanly with Strapi’s built-in pagination.
<!-- Add Screenshot -->

This approach gives you the best balance between performance, SEO, and user experience.


