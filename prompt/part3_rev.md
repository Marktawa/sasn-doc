## Introduction

In this tutorial, we’ll be learning how to integrate social authentication into our [Strapi](https://strapi.io/) application. In order to do this, we’ll be building simple notes sharing application with Strapi backend and [Nuxt.js](https://strapi.io/integrations/nextjs-cms) frontend, we’ll also use [Quill.js](https://quilljs.com/) as our text editor, [Nodemailer](https://nodemailer.com/) for emails, we’ll integrate infinite scrolling and many more features.

In the third part of the tutorial series on social authentication with Strapi you will learn how to integrate vue-quill-editor to enable users to create content, image uploads, copying links, and email sharing.

In the [first part](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-getting-started), was all about getting started, we looked at a couple of things like getting started with Strapi, installing Strapi, and building a backend API with Strapi.

And in the [second part](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-adding-social-providers), we learned how to add social providers (Github) and the Nuxtjs frontend.

## Integrating vue-quill-editor

We need a way for our application users to create and edit notes, that’s why we’re going to integrate [vue-quill-editor](https://www.npmjs.com/package/vue-quill-editor).

Execute the following lines of code

```
npm install vue-quill-editor //using npm
```

Open up your `nuxt.config.js` file then add the following lines of code

```js
css: [ 'quill/dist/quill.snow.css', 'quill/dist/quill.bubble.css', 'quill/dist/quill.core.css' ],

plugins: [
  {
      src: '~/plugins/vue-quill-editor',
      mode: 'client'
  },
]
```

Create a `vue-quill-editor.js` file in the plugins folder, then add the following lines of code to the file

```js
import Vue from 'vue'
import VueQuillEditor from 'vue-quill-editor/dist/ssr'
Vue.use(VueQuillEditor)
```

Now that we have successfully install and set-up vue-quill-editor, we can use it in our application.

### Building the note page

Execute the following lines of code to create a `notes/_id.vue` file

```
cd pages
mkdir notes
touch _id.vue
```

Open up the file and fill it up with the lines of code below.

```vue
<template>
  <div>
    <Nav />
    <div class="w-4/5 sm:w-2/3 mx-auto">
      <button class="button--blue my-3" @click="toggleAddEditors">
        Add Editor
      </button>
      <NuxtLink :to="`/notes/preview/${res.id}`" class="button--blue">
        Preview
        <span><font-awesome-icon :icon="['fas', 'eye']" /></span>
      </NuxtLink>
      <div
        v-if="addEditor"
        class="absolute hex left-0 top-0 bottom-0 right-0 w-full"
      >
        <div class="bg-white sm:w-1/3 w-4/5 shadow-lg p-10 mx-auto mt-32">
          <p v-if="error" class="text-red-600 my-3">{{ error }}</p>
          <p v-if="success" class="text-green-600 my-3">{{ success }}</p>
          <form @submit="addNewEditor">
            <input
              v-model="editorEmail"
              class="p-5 font-bold w-full border-3 border-black-500"
              type="email"
              placeholder="Email"
            />
            <button type="submit" class="button--blue my-3">Add</button>
          </form>
          <button type="submit" class="button--green" @click="toggleAddEditors">
            Cancel
          </button>
        </div>
      </div>
      <div id="toolbar"></div>
      <div
        ref="quill-editor"
        v-quill:myQuillEditor="editorOption"
        class="quill-editor shadow-2xl"
        :content="content"
        @change="onEditorChange($event), update()"
        @blur="onEditorBlur($event)"
        @focus="onEditorFocus($event)"
        @ready="onEditorReady($event)"
      >
        <form ref="formInput">
          <input
            id="file"
            ref="input"
            name="files"
            class="file"
            type="file"
            style="display: none"
            @change="doUpload"
          />
        </form>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  async middleware({ $auth, route, redirect, store, $strapi, $axios }) {
    const token = $auth.$storage.getUniversal('jwt')
    const response = await $axios.get(`/notes/${route.params.id}`, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    })
    const note = await response.data
    const noteAuthorId = note.users_permissions_user.id
    if (
      $auth.$storage.getUniversal('user') === null ||
      ($auth.$storage.getUniversal('user').id !== noteAuthorId &&
        note.Editors.findIndex((editor) => {
          return editor.id === $auth.$storage.getUniversal('user').id
        }) === -1)
    ) {
      return redirect(`/notes/preview/${route.params.id}`)
    }
  },
  async asyncData({ $strapi, store, route }) {
    const note = await $strapi.$notes.findOne(route.params.id)
    store.commit('setNote', note)
  },
  data() {
    const self = this
    return {
      res: '',
      error: '',
      isAuthor: '',
      title: '',
      token: this.$auth.$storage.getUniversal(`jwt`),
      content: '',
      addEditor: false,
      editorEmail: '',
      editorOption: {
        // some quill options
        modules: {
          toolbar: {
            container: [
              ['bold', 'italic', 'underline', 'strike'], // toggled buttons
              ['link'],
              ['blockquote', 'code-block'],
              ['image'],
              [{ header: 1 }, { header: 2 }], // custom button values
              [{ list: 'ordered' }, { list: 'bullet' }],
              [{ script: 'sub' }, { script: 'super' }], // superscript/subscript
              [{ indent: '-1' }, { indent: '+1' }], // outdent/indent
              [{ direction: 'rtl' }], // text direction
              [{ size: ['small', false, 'large', 'huge'] }], // custom dropdown
              [{ header: [1, 2, 3, 4, 5, 6, false] }],
              [{ color: [] }, { background: [] }], // dropdown with defaults from theme
              [{ font: [] }],
              [{ align: [] }],
              ['clean'], // remove formatting button
            ],
            handlers: {
              image() {
                this.quill.format('image', false) // disable the quill internal upload image method
                self.imgHandler(this)
              },
            },
          },
        },
      },
    }
  },
  watch: {
    res(newRes, oldRes) {
      if (this.$auth.$storage.getUniversal('user')) {
        this.isAuthor =
          newRes.users_permissions_user.id ===
          this.$auth.$storage.getUniversal('user').id
      }
    },
  },
  created() {
    const res = this.$store.getters.getNote
    console.log('res', res)
    this.title = res.title
    this.res = res
    this.content = res.content ? res.content : ''
  },
  methods: {
    handleRemove(file, fileList) {
    },
    handlePreview(file) {
    },
    onEditorBlur(editor) {
    },
    onEditorFocus(editor) {
      this.$refs['quill-editor'].firstElementChild
        .getElementsByTagName('h1')
        .forEach((el) => console.log(el.textContent))
    },
    onEditorReady(editor) {
    },
    onEditorChange({ editor, html, text }) {
      this.content = html
    },
    imgHandler(handle) {
      this.quill = handle.quill
      const inputfile = this.$refs.input
      inputfile.click()
    },
    async update() {
      const params = {
        title: this.$refs[
          'quill-editor'
        ].firstElementChild?.getElementsByTagName('h1')[0]?.textContent,
        content: this.content,
      }
      console.log('params', params)
      await this.$axios.$put(`/notes/${this.$route.params.id}`, params, {
        headers: {
          Authorization: `Bearer ${this.token}`,
        },
      })
    },
  },
}
</script>
<style scoped>
.container {
  margin: 0 auto;
  padding: 50px 0;
}
.ql-toolbar span {
  color: #fff;
}
</style>
```

What we’ve done here is to build out the layout of the notes page, where users could create and edit notes.

The application takes the text-content of the first `<h1></h1>` tag to be the name of the note.

The page has a middleware to check if a user is logged in or not and to check user permissions. We also fetch data and commit it to the store in the asyncData hook.

### Building our store

Nuxt.js supports vuex right out the box

Execute the following lines

```
cd store
```

Fill up the `index.js` file with the following lines of code

```js
export const state = () => ({
   note: {}
})
export const getters = {
   getNote: (state) => state.note
}
export const actions = {
}
export const mutations = {
    setNote: (state, currentNote) => (state.note = currentNote),
    updateEditors: (state, editors) => (state.note.Editors = editors)
}
```

We’ve set up the vuex store completely,

## Email sharing

We need a way for users to share notes, request edit access and add editors. That’s what we’re going to build in this section.

### Build the Strapi backend for email sharing

Open up your Strapi backend in your code editor. We’ll be using [email-templates](https://www.npmjs.com/package/email-templates) and pug for email templating.

To install email-templates and pug, execute the following lines of code.

```
yarn add email-templates pug //using yarn

npm install email-templates pug //using npm
```

Now we can start building our templates

```
mkdir emails
cd emails 
mkdir addEditors requestAccess shareLink
cd addEditors 
touch html.pug subject.pug text.pug
```

Fill up the `subject.pug` file with the following

```pug
= `Hi ${email}, Permission from NoteApp`
```

Fill up the `html.pug` file with the following

```pug
h1 Hey there
    p.
        #{author} just granted you edit access to a note. We just need to make sure you are aware. 
        Please, click the link below to check the note. 
a(href=`http://localhost:3000/notes/${id}`) View note!
```

then

```
cd ..
cd requestAccess
touch html.pug subject.pug text.pug
```

Fill up the `subject.pug` file with the following

```pug
= `Hi ${author}, Permission from NoteApp`
```

Fill up the `html.pug` file with the following

```pug
h1 Hey there
    p.
        #{email} would like to request edit access of your note. We just need to make sure you are aware. 
        Please, click the button below and to grant edit access. 
a(href=`http://localhost:3000/notes/${id}`) grant access!
```

then

```
cd ..
cd shareLink
touch html.pug subject.pug text.pug
```

Fill up the `subject.pug` file with the following

```pug
= `Hi ${author}, Permission from NoteApp`
```

Fill up the `html.pug` file with the following

```pug
h1 Hey there
    p.
        #{sharer} just invited you to view a note. Please, click the link below to check the note. 
a(href=`http://localhost:3000/notes/preview/${id}`) View note!
```

Next, carry out the following

```
cd ../..
cd api
cd note
cd services
```

Open up the `note.js` file and fill it up as follows.

```js
'use strict';

const Email = require('email-templates');
const email = new Email({
    message: {
        from: process.env.GMAIL_USER
    },
    send: true,
    transport: {
        service: 'Gmail',
        host: 'smtp.gmail.com',
        port: 465,
        ssl: true,
        tls: true,
        auth: {
            user: process.env.GMAIL_USER, // your gmail username
            pass: process.env.GMAIL_PASSWORD //your gmail password
        }
    }
});
module.exports = {
    send: (to, template, locals) => {
        // Setup e-mail data.
        return new Promise((resolve, reject) => {
            resolve(
                email.send({
                    template,
                    message: {
                        to
                    },
                    locals
                })
            );
        });
    }
};
```

Rename your `.env.example` file to `.env` then open it up and fill in your credentials.

```
GMAIL_PASSWORD=yourpassword
GMAIL_USER=example@gmail.com
```

Execute the following

```
cd ..
cd config
```

Open up the `routes.json` file and add the following to it

```json
{
  "method":"POST",
  "path": "/notes/:id/addEditors",
  "handler": "note.addEditors",
  "config": {
    "policies": []
  }
},
{
  "method":"POST",
  "path": "/notes/:id/requestEditAccess",
  "handler": "note.requestEditAccess",
  "config": {
    "policies": []
  }
},
{
  "method":"POST",
  "path": "/notes/:id/shareLink",
  "handler": "note.shareLink",
  "config": {
    "policies": []
  }
}
```

then finally,

```
cd ..
cd controllers
```

Open up the `note.js` file and fill it up with the following code

```js
'use strict';

module.exports = {
    async addEditors(ctx) {
        const { editorEmail, noteAuthor } = ctx.request.body;
        const id = ctx.params.id;
        const locals = {
            email: editorEmail,
            id,
            author: noteAuthor
        };
        strapi.services.note
            .send(editorEmail, 'addEditors', locals)
            .then((res) => console.log(res.originalMessage))
            .catch((err) => console.log(err));
        ctx.send({
            ok: true
        });
        console.log(ctx.request.body);
    },
    async requestEditAccess(ctx) {
        const { noteAuthor, userEmail } = ctx.request.body;
        const id = ctx.params.id;
        const locals = {
            email: userEmail,
            id,
            author: noteAuthor
        };
        strapi.services.note
            .send(noteAuthor, 'requestAccess', locals)
            .then((res) => console.log(res.originalMessage))
            .catch((err) => console.log(err));
        ctx.send({
            ok: true
        });
    },
    async shareLink(ctx) {
        console.log(ctx.request.body);
        const { sharer, recievers } = ctx.request.body;
        const id = ctx.params.id;
        const locals = {
            id,
            sharer
        };
        recievers.forEach((reciever) => {
            locals.reciever = reciever;
            strapi.services.note
                .send(reciever, 'shareLink', locals)
                .then((res) => console.log(res.originalMessage))
                .catch((err) => console.log(err));
            ctx.send({
                ok: true
            });
        });
    }
};
```

Now, our Strapi application supports email-sharing, next we’ll integrate the email-sharing ability in the Nuxt.js front-end.

### Updating our `notes/_id.vue` file

Open up your `notes/_id.vue` file and add the following lines of code to your methods object

```vue
async addNewEditor(e) {
      e.preventDefault()
      try {
        const [newEditor] = await this.$strapi.$users.find({
          email: this.editorEmail,
        })
        console.log(newEditor)
        const oldEditors = this.res.Editors
        if (
          newEditor !== undefined &&
          oldEditors.findIndex((editor) => editor.email === newEditor.email) ===
            -1
        ) {
          const updatedEditors = [...oldEditors, newEditor]
          // call api to send mail
          await this.$axios.$post(
            `/notes/${this.res.id}/addEditors`,
            {
              editorEmail: this.editorEmail,
              noteAuthor: this.$auth.$storage.getUniversal('user').email,
            },
            {
              headers: {
                Authorization: `Bearer ${this.token}`,
              },
            }
          )
          // update editors in strapi backend
          await this.$axios.$put(
            `/notes/${this.$route.params.id}`,
            {
              Editors: updatedEditors,
            },
            {
              headers: {
                Authorization: `Bearer ${this.token}`,
              },
            }
          )
          // update editors in store
          this.$store.commit('updateEditors', updatedEditors)
          this.success = `Author added Successfully`
          this.error = ''
        } else {
          this.error = `The user with that email doesn't exist or is already an editor`
          this.success = ''
          console.log(`${this.error}`)
        }
        this.editorEmail = ''
      } catch (e) {
        this.$nuxt.error(e)
      }
    },
    toggleAddEditors() {
      this.addEditor = !this.addEditor
      this.error = ''
      this.success = ''
    },
```

Now, we can add editors using their emails, provided the user with the email is a user in our database.

### Building our preview page

Execute the following lines of code to create a `notes/preview/_id.vue` page.

```
cd pages
cd notes
mkdir preview
touch _id.vue
```

Open up the `_id.vue` file and fill it up with the following code

```vue
<template>
  <div>
    <Nav />
    
    <div class="w-4/5 sm:w-2/3 mx-auto">
      <div v-if='error' class="hex absolute left-0 top-0 h-full w-full">
        <div
         class="border-3 bg-white sm:w-1/3 w-4/5 shadow-lg p-10 mx-auto mt-32"
        >
          <p class="my-3">{{ error }}</p>
          <button class="button--blue" @click="clearError">Ok</button>
        </div>
      </div>
      <div class="flex items-center space-x-5">
        <NuxtLink
          v-if="isEditor"
          class="button--green"
          :to="`/notes/${note.id}`"
        >
          Edit
           <span><font-awesome-icon :icon="['fas', 'pen']" /></span>
        </NuxtLink>
        <button
          v-if="!isEditor"
          class="button--green"
          @click="requestEditAccess"
        >
          Request Edit Permissions
        </button>
        <Share v-if="user" :id="note.id" class="z-10" />
        <p class="cursor-pointer" @click="doCopy">
          Copy Link
           <span><font-awesome-icon :icon="['fas', 'copy']" /></span>
        </p>
      </div>
      <!--<h1 class="my-3 text-4xl font-black">{{ note.title }}</h1>-->
      <div
        v-quill:myQuillEditor="editorOption"
        class="quill-editor shadow-2xl"
        :content="note.content"
        @ready="onEditorReady($event)"
        @focus="onEditorFocus($event)"
      ></div>
    </div>
  </div>
</template>
<script>
export default {
  async asyncData({ $strapi, route }) {
    const note = await $strapi.$notes.findOne(route.params.id)
    return { note }
  },
  data() {
    return {
      error: '',
      user: this.$auth.$storage.getUniversal('user'),
      message: 'http://localhost:3000' + this.$route.fullPath,
      token: this.$auth.$storage.getUniversal('jwt'),
      editorOption: {
        modules: {
          toolbar: '',
        },
      },
    }
  },
  computed: {
    isEditor() {
      return (
        this.$auth.$storage.getUniversal('user') !== null &&
        (this.$auth.$storage.getUniversal('user').id ===
          this.note.users_permissions_user.id ||
          this.note.Editors.findIndex((editor) => {
            return editor.id === this.$auth.$storage.getUniversal('user').id
          }) !== -1)
      )
    },
  },
  methods: {
    onEditorReady(editor) {
      editor.disable()
    },
    onEditorFocus(editor) {
      editor.disable()
    },
    assignValue(e) {
      console.log(e.target.value)
      this.recieverEmail = e.target.value
    },
    async requestEditAccess(e) {
      e.preventDefault()
      console.log(`requesting edit access...`)
      if (this.$auth.$storage.getUniversal('user') !== null) {
        await this.$axios.$post(
          `/notes/${this.note.id}/requestEditAccess`,
          {
            noteAuthor: this.note.users_permissions_user.email,
            userEmail: this.$auth.$storage.getUniversal('user').email,
          },
          {
            headers: {
              Authorization: `Bearer ${this.token}`,
            },
          }
        )
      } else {
        this.error = 'please login to request access'
        console.log('please login to request access')
      }
    },
    clearError() {
      this.error = ""
    },
  },
}
</script>
<style scoped>
.container {
  width: 60%;
  margin: 0 auto;
  padding: 50px 0;
  background: #fff;
}
</style>
```

### Building the Share component

Execute the following lines of code to create a `Share.vue` file

```vue
cd components
touch Share.vue
```

Open up the file and fill it up with the following code

```vue
<template>
  <div>
    <button class="button--blue my-3" @click="toggleShare">
      Share
       <span><font-awesome-icon :icon="['fas', 'share']" /></span>
    </button>
    <div v-if="share" class="hex absolute left-0 top-0 h-full w-full">
      <div
        class="border-3 bg-white sm:w-1/3 w-4/5 shadow-lg p-10 mx-auto mt-32"
      >
        <form @submit="shareLink">
          <input v-model="emails" type="email" class="w-full p-3 my-5" placeholder="Email" />
          <button type="submit" v-if="emails" class="button--blue my-3">Send</button>
        </form>
        <button type="submit" class="button--green" @click="toggleShare">
          Cancel
        </button>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: 'Share',
  props: ['id'],
  data() {
    return {
      share: false,
      emails: '',
    }
  },
  methods: {
    async shareLink(e) {
      e.preventDefault()
      console.log(`sharing link`)
      const token = this.$auth.$storage.getUniversal('jwt')
      if (this.$auth.$storage.getUniversal('user') !== null) {
        const recieverEmails = this.emails.split(', ')
        console.log(recieverEmails)
        await this.$axios.$post(
          `/notes/${this.id}/shareLink`,
          {
            sharer: this.$auth.$storage.getUniversal('user').email,
            recievers: recieverEmails,
          },
          {
            headers: {
              Authorization: `Bearer ${token}`,
            },
          }
        )
        this.emails = ''
      } else {
        console.log(`please login to share`)
      }
    },
    toggleShare() {
      this.share = !this.share
    },
  },
}
</script>
<style scoped></style>
```

Now, we have our share component working, Let’s create a way for users to copy the link of an article.

## Copying Links

We’ll use a package called [nuxt-clipboard](https://www.npmjs.com/package/nuxt-clipboard), execute the following lines of code to install nuxt-clipboard

```
yarn add nuxt-clipboard //using yarn

npm install nuxt-clipboard //using npm
```

Open up your `nuxt.config.js` file and add the following lines of code

```js
modules: [
  //...other modules
  'nuxt-clipboard'
]
```

Execute the following line of code

```
cd pages
cd notes 
cd preview
```

Open up the `_id.vue` file and add the following lines of code to the methods object.

```vue
doCopy() {
  this.$copyText(this.message).then(
    function (e) {
      alert('Copied')
    },
    function (e) {
      alert('Can not copy')
    }
  )
},
```

Now, we are able to copy links and share links.

