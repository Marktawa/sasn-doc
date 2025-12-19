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
