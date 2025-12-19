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
