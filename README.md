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

<!-- End of Latest Video -->

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

## Test `find`for Authenticated Users

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

For now we just overriding the `find` controller method is fine. As for others `findOne`, `delete`, `update`, and `create` we will override them when we implement error handling and security.

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

## Add Sharing: Phase 1

We will now implement the sharing functionality for this app. The app in its current state already has the ability to share notes. All the owner of a note has to do to share a note is simply send the link manually to another user. In other words, any authenticated user with a link to the note can view, edit or delete a note right in the frontend User Interface of the app.

To prove this we can grab the link to the note of one user and try to open it logged in as another user in the browser.

Make sure your Strapi server and Nuxt development server are running.

In one browser instance log in to the Notes app as one user. Visit one of the notes in the list and copy the URL to the note in the address bar.

Open another browser instance (one which doesn't use the login credentials of the first user) then log in to the Notes app as the second user. Once logged in, paste the URL of the note link you copied earlier. You should be able to view the note as predicted.
<!-- Add screenshot -->

Try editing the note as well. You should see your changes reflected, even though you don't own the note.
<!-- Add screenshot -->
