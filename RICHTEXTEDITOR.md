## Integrating a rich text editor

### Install and Configure `@vueup/vue-quill`

The first goal is to get the rich text editor to appear on the page. We won't worry about loading or saving data.

Add `@vueup/vue-quill` and its peer dependency `quill` by running the following command in `frontend` the project directory of your Nuxt frontend:
```shell
npm install @vueup/vue-quill quill
```

Configure styles by updating `nuxt.config.ts`:
```ts
export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  ssr: false, // Disable SSR for client-side storage
  css: [
    '@vueup/vue-quill/dist/vue-quill.snow.css' // Load "Snow" theme
  ]
  nitro: {
    devProxy: {
      '/api': {
        target: 'http://localhost:1337/api',
        changeOrigin: true
      }
    }
  }
});
```

The `css` property has been added to your `defineNuxtConfig` block. This tells Nuxt to load the editor's default "Snow" theme stylesheet globally.

### Add basic notes page

Create a basic note page by creating a `pages/notes/[id].vue` file:
```shell
mkdir -p pages/notes
touch pages/notes/[id].vue
```

Add the following code to `pages/notes/[id].vue`:
```vue
<template>
  <div class="container">
    <h1>Note Editor</h1>
    <ClientOnly>
      <QuillEditor
        v-model:content="content"
        theme="snow"
        :options="editorOptions"
        content-type="html"
      />
    </ClientOnly>
  </div>
</template>

<script setup lang="ts">
import { QuillEditor } from '@vueup/vue-quill'
import { ref } from 'vue'

const content = ref('<h2>Start typing your note...</h2><p></p>')

const editorOptions = {
  modules: {
    toolbar: [
      ['bold', 'italic', 'underline'],
      [{ 'header': 1 }, { 'header': 2 }],
    ]
  },
  placeholder: 'Compose an epic...'
}
</script>

<style>
.container {
  width: 80%;
  margin: 2rem auto;
}

.ql-editor {
  min-height: 200px;
  background-color: white;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  font-size: 1.1rem;
}
</style>
```

Start your Nuxt frontend development server:
```shell
npm run dev
``` 

Visit a URL like [localhost:3000/notes/123](http://localhost:3000/notes/123). The ID (`123`) doesn't matter yet.

You should see a page with the heading "Note Editor" and a functional rich text editor below it. The toolbar should have buttons for bold, italic, underline, and two header levels. You should be able to type in the editor and format the text.

![Notes Page with Basic Editor](https://res.cloudinary.com/craigsims808/image/upload/v1750947923/strapi/sasn/notes-page-with-basic-editor_ddcbuj.png)

## Load data from a Mock API within Nuxt

Now that we have a visible editor, our next goal is to populate it with data from our Strapi backend.

In Nuxt 3, creating a mock API is incredibly simple thanks to the built-in server engine, Nitro. Any file placed in the `server/api` directory automatically becomes an API endpoint.

### Create the Mock API Endpoint

In your `frontend` project, create a `server/api/notes` directory for the mock notes API.
```shell
mkdir -p server/api/notes
```

Create a dynamic route `[id].get.ts` that accepts a note `id`
```shell
touch server/api/notes/[id].get.ts
```

- The `[id]` creates a dynamic parameter.
- The `.get.ts` suffix tells Nuxt this file handles `GET` requests for this route.

### Add mock data and logic

The file `server/api/notes/[id].get.ts` will act as our temporary server. It will define a sample note and send it back if the `id` from the URLl matches.

Open `server/api/notes/[id].get.ts` and add the following code:
```ts
const mockNote = {
  id: '123',
  title: 'My First Note from the API'
  content: '<h2>Welcome!</h2><p>This content was loaded from our <strong>mock API endpoint</strong> inside Nuxt.</p>'
}

export default defineEventHandler((event) => {
  const noteId = event.context.params?.id

  if (noteId === mockNote.id) {
    return mockNote
  }
})
```

### Fetch Data in the Page Component

Modify `pages/notes/[id].vue` to fetch data from the `/api/notes/:id` endpoint. We will use Nuxt 3's `useAsyncData` composable.

Update `pages/notes/[id].vue` with the following code:
```vue
<template>
  <div class="container">
    <h1>{{ title }}</h1>
    <ClientOnly>
      <QuillEditor
        v-model:content="content"
        theme="snow"
        :options="editorOptions"
        content-type="html"
      />
    </ClientOnly>
  </div>
</template>

<script setup lang="ts">
import { QuillEditor } from '@vueup/vue-quill'
import { ref } from 'vue'

const route = useRoute()
const title = ref('Note Editor')
const content = ref('')

const { data: note } = await useAsyncData(
  `note-${route.params.id}`, 
  () => $fetch(`/api/notes/${route.params.id}`) 
)

if (note.value) {
  title.value = note.value.title
  content.value = note.value.content
}

const editorOptions = {
  modules: {
    toolbar: [
      ['bold', 'italic', 'underline'],
      [{ 'header': 1 }, { 'header': 2 }],
    ]
  },
  placeholder: 'Compose an epic...'
}
</script>

<style>
.container {
  width: 80%;
  margin: 2rem auto;
}

.ql-editor {
  min-height: 200px;
  background-color: white;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
  font-size: 1.1rem;
}
</style>
```

Run your Nuxt frontend project and navigate to `localhost:3000/notes/123` in your browser.

The page should load with the title "My First Note from the API" and the editor should contain the corresponding content from our mock API file.

![]
