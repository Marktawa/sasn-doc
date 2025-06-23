## Introduction

In this tutorial, we’ll be learning how to integrate social authentication into our [Strapi](https://strapi.io/) application. In order to do this we’ll be building a simple notes sharing application with Strapi backend and [Nuxt.js](https://nuxtjs.org/) frontend, we’ll also use [Quill.js](https://quilljs.com/) as our text editor, [Nodemailer](https://nodemailer.com/) for emails, we’ll integrate infinite scrolling and many more features.

This is the second part of the tutorial series on social authentication with strapi. The [first part](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-getting-started) was all about getting started, we looked at a couple of things like getting started with Strapi, installing Strapi, and building a backend API with Strapi.

What’ll you need for this tutorial:

*   Basic knowledge of [Vue.js](https://vuejs.org/)
*   Knowledge of JavaScript
*   [Node.js](https://nodejs.org/en/) (Active LTS v20 or v22)

Here’s what the final version of our application looks like

![](https://paper-attachments.dropbox.com/s_79BF70BAA3D70DB191E3093D0EAD323EB7F6E2886FE3F69EF110CEB48794F12A_1617459545504_notes_app-notes5.jpg)

![](https://paper-attachments.dropbox.com/s_79BF70BAA3D70DB191E3093D0EAD323EB7F6E2886FE3F69EF110CEB48794F12A_1617459752392_notes_app-notes6.jpg)

You can find the source code for the application using this [GitHub Repository Link](https://github.com/Marktawa/social-auth-strapi-nuxt)

## Adding Login Providers

Table of contents

*   GitHub authentication and getting our GitHub credentials
*   Configuring the GitHub provider in our Strapi backend
*   Facebook authentication and getting our Facebook credentials
*   Configuring the Facebook provider in our Strapi backend
*   Building the Home page
*   Building the Login page
*   Building the Nuxt.js frontend to handle redirects
*   Building the user page

To learn more about adding login providers to your Strapi application, feel free to look at the official [Strapi documentation for Users & Permissions](https://docs.strapi.io/cms/features/users-permissionsl). The documentation gives everything from login to JWT to adding 3rd parting providers.

## GitHub authentication and getting our GitHub credentials

1.  Visit the OAuth Apps list page [https://github.com/settings/developers](https://github.com/settings/developers)
2.  Click on **New OAuth App** button
3.  Fill the information
    *   **Application name**: strapi-notes-app
    *   **Homepage URL**: `http://localhost:1337/`
    *   **Application description**: Strapi notes sharing application
    *   **Authorization callback URL**: `http://localhost:1337/connect/github/callback`
4. Click **Register application** and you will be redirected to the OAuth application settings page.
5. Click **Generate a new client secret** and copy it. Copy the **Client ID** as well.

<!--Add screenshot of settings-->
<!-- Client ID: Ov23liOtqTI9d0mHYCZ1 -->
<!-- Client Secret: b0814556ede5bd8a9f7dde23ff5e9d6d77d74587 -->

![GitHub OAuth Settings](https://res.cloudinary.com/craigsims808/image/upload/v1750258727/strapi/sasn/github-oauth-settings-live_abbowv.png)

## Configuring the GitHub provider in our Strapi backend

1.  Open up your Strapi application that’s hosted on `http://localhost:1337`
2.  Click **Settings**
3.  Under **Users & Permissions plugin** select **Providers**
4.  Click **github** provider.
5.  Fill the information (replace with your own client ID and secret):
    *   **Enable**: `ON`
    *   **Client ID**: `YOUR_GITHUB_CLIENT_ID`
    *   **Client Secret**: `YOUR_GITHUB_CLIENT_SECRET`
    *   **The redirect URL to your front-end app**: `http://localhost:3000/connect/github`
    *   **Enable**: `TRUE`
6. Click **Save**

![GitHub provider settings](https://res.cloudinary.com/craigsims808/image/upload/v1750343147/strapi/sasn/github-provider-settings_moeeki.png)

## Facebook authentication and getting our Facebook credentials

1.  Visit the Developer Apps list page [https://developers.facebook.com/apps/](https://developers.facebook.com/apps/).
2.  Click **Create App**.
3.  Fill the **App name**: `Strapi Notes App` and the **App contact email** in the **App details** section and click **Next**.
<!--
4.  Setup a **Facebook Login** product
5.  Click on the **PRODUCTS > Facebook login > Settings** link in the left menu
6.  Fill the information and save:
    *   **Valid OAuth Redirect URIs**: `https://localhost:1337/connect/facebook/callback`
7.  Then, click on **Settings** in the left menu
8.  Then on **Basic** link
9.  You should see your Application ID and secret, save them for later
-->
4. Under **Use cases** select `Authenticate and request data from users with Facebook Login` and click **Next**.
5. Under **Business** select `I don't want to connect a business portfolio yet.` and click **Next**.
6. Under **Publishing Requirements** leave everything as is and click **Next**.
7. Under **Overview** leave everything as is and click **Go to dashboard**.
8. Click **Basic** under **App settings** and under **App domains** add `http://localhost:1337` then click **Save changes**.
9. Click **Dashboard** and select **Customize the Authenticate and request data from users with Facebook Login use case**.
10. Under **Facebook Login** pick **Settings**.
11. Add the **Valid OAuth Redirect URI**, which is `http://localhost:1337/api/connect/facebook/callback` and select **Save changes**.
12. Copy your **App ID** and **App secret** by visiting **Basic** under **App settings**

<!--App ID 759961263122964 -->
<!--App Secret ad69c02c65684438ec76188def0cb613 -->

![Facebook OAuth Settings](https://res.cloudinary.com/craigsims808/image/upload/v1750354011/strapi/sasn/facebook-oauth-settings_byz3cd.png)

## Configuring the Facebook provider in our Strapi backend

1.  Open up your Strapi application that’s hosted on `http://localhost:1337`
2.  Click on Settings
3.  Under settings, Click on Providers
4.  Click on the **Facebook** provider
5.  Fill the information (replace with your own client ID and secret):
    *   **Enable**: `ON`
    *   **Client ID**: `YOUR_CLIENT_ID`
    *   **Client Secret**: `YOUR_CLIENT_SECRET`
    *   **The redirect URL to your front-end app**: `http://localhost:3000/connect/facebook`
    *   **Enable**: `TRUE`
6. Click **Save**

![Facebook provider settings](https://res.cloudinary.com/craigsims808/image/upload/v1750355423/strapi/sasn/facebook-provider-settings_vwt0r2.png)

## Building the Home page

Now, we can start building the frontend of our application. let’s start by building our components, but first let’s create a layout using `layouts/default.vue`.

*   To begin, open up your Nuxt.js project,
*   Create `layouts` directory and create a new `default.vue` file then fill it up with the following code.

```vue
  <template>
  <div>
    <slot />
  </div>
</template>
<style>
html {
  font-family: 'Source Sans Pro', -apple-system, BlinkMacSystemFont, 'Segoe UI',
    Roboto, 'Helvetica Neue', Arial, sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
}
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
}
.button--green {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid #3b8070;
  color: #3b8070;
  text-decoration: none;
  padding: 10px 30px;
}
.button--green:hover {
  color: #fff;
  background-color: #3b8070;
}
.button--green:focus {
  outline: 0px !important;
}
.button--blue {
  display: inline-block;
  border-radius: 4px;
  border: 1px solid skyblue;
  color: blue;
  text-decoration: none;
  padding: 10px 30px;
}
.button--blue:hover {
  color: #fff;
  background-color: skyblue;
}
.button--blue:focus {
  outline: 0px !important;
}
.ql-container.ql-snow {
  border: 0px !important;
}
.quill-editor img {
  margin: 0 auto;
  width: 60%;
}
.ql-toolbar {
  position: sticky;
  top: 0;
  z-index: 100;
  background: white;
  color: #fff;
  border: 0px !important;
}
.quill-editor {
  min-height: 200px;
  padding: 10%;
  overflow-y: auto;
}
.hex {
  z-index: 9999999999;
  background: rgba(0, 0, 0, 0.5);
  overflow: hidden;
  position: fixed;
}
</style>
```

Create a `pages` directory and add an `index.vue` file in it. Fill it up with the following code.

```vue
<template>
    <div
    class="min-h-screen flex justify-center items-center text-center mx-auto sm:pl-24 bg-yellow-200"
    >
    <div class="w-1/2 sm:text-left sm:m-5">
        <div>
        <h1
            class="text-3xl sm:text-6xl font-black sm:pr-10 leading-tight text-blue-900"
        >
            Welcome to the NoteApp
        </h1>
        <p class="sm:block hidden my-5">
            Your number one notes sharing application
            <br />
            Share your notes with anybody across the globe
        </p>
        </div>
        <div class="links">
        <NuxtLink to="/login" class="button--blue shadow-xl"> Login </NuxtLink>
        </div>
    </div>
    <div class="w-1/2 hidden sm:block">
        <img
        class=""
        src="~/assets/undraw_Sharing_articles_re_jnkp.svg"
        />
        
    </div>
    </div>
</template>
<script>
export default {}
</script>
<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
@apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
    margin: 0 auto;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
}
.title {
    font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
    display: block;
    font-weight: 300;
    font-size: 100px;
    color: #35495e;
    letter-spacing: 1px;
}
.subtitle {
    font-weight: 300;
    font-size: 42px;
    color: #526488;
    word-spacing: 5px;
    padding-bottom: 15px;
}
.links {
    padding-top: 15px;
}
</style>
```

Add the following files to your `assets` folder:
- [undraw_Access_account_re_8spm.svg](https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_Access_account_re_8spm.svg)
- [undraw_Sharing_articles_re_jnkp.svg](https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_Sharing_articles_re_jnkp.svg)
- [undraw_Social_notifications_re_xcbi.svg](https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_Social_notifications_re_xcbi.svg)
- [undraw_secure_login_pdn4.svg](https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_secure_login_pdn4.svg)

With the above code we’ve built the homepage of our notes sharing application. Run your Nuxt server to view the changes.

![Nuxt Home page 1](https://res.cloudinary.com/craigsims808/image/upload/v1750359324/strapi/sasn/nuxt-home-page_xq6bih.png)


The next task is to build a login page, from which users can login into our application.

## Building the Login page

Execute the following code to create a `login.vue` file.
```shell
cd pages
touch login.vue
```

Fill up the `login.vue` file with the code below
```vue
<template>
  <div
    class="min-h-screen flex justify-center items-center text-center mx-auto sm:pl-24 bg-yellow-200"
  >
    <div class="w-1/2 hidden sm:block m-5 p-6">
      <img 
      src="~/assets/undraw_secure_login_pdn4.svg"
      alt="Secure Login Illustration" 
      />
    </div>
    <div class="sm:w-1/2 w-4/5">
      <h2 class="m-5 font-black text-3xl">Social Login</h2>
      <div class="shadow-xl bg-white p-10">
        <a
          href="http://localhost:1337/api/connect/github"
          class="cursor-pointer m-3 button--blue shadow-xl"
        >
          <span><font-awesome-icon :icon="['fab', 'github']" /></span>
          Github
        </a>
        <a
          href="http://localhost:1337/connect/facebook"
          class="cursor-pointer m-3 button--blue shadow-xl"
        >
          <span>
            <font-awesome-icon :icon="['fab', 'facebook']" />
          </span>
          Facebook
        </a>
      </div>
    </div>
  </div>
</template>
<script>
export default {}
</script>
<style lang="scss" scoped></style>
```

In the code above, we create links to our Strapi backend’s Facebook and GitHub logic using `<a><a/>` tags. When a user clicks on the link, our login logic is ready to execute. We are also using Font Awesome to display icons, let’s see how to do that:

### Installing and Using Font Awesome

In your terminal, execute the following code to install font-awesome
```shell
npm install @fortawesome/vue-fontawesome @fortawesome/free-brands-svg-icons @fortawesome/free-solid-svg-icons
```

Create a new file under `plugins/fontawesome.js`:
```js
import { library } from '@fortawesome/fontawesome-svg-core'
import { FontAwesomeIcon } from '@fortawesome/vue-fontawesome'
import { faGithub, faFacebook } from '@fortawesome/free-brands-svg-icons'

library.add(faGithub, faFacebook)

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.component('FontAwesomeIcon', FontAwesomeIcon)
})
```

Update your `nuxt.config.ts` file with the following lines of code:
```ts
import tailwindcss from "@tailwindcss/vite";
export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  css: ['~/assets/css/main.css'],
  plugins: ['~/plugins/fontawesome.js'],
  vite: {
    plugins: [
      tailwindcss(),
    ],
  },
});
```

Now we should see the Facebook and GitHub icons displayed correctly.

Visit [localhost:3000/login](http://localhost:3000/login) to view the changes.

![Login page](https://res.cloudinary.com/craigsims808/image/upload/v1750365270/strapi/sasn/login-page_jiutci.png)

## Building the Nuxt.js frontend to handle redirects

Execute the following:
```shell
cd pages
mkdir connect
cd connect
touch [provider].vue
```

Open up the `[provider].vue` file and add the following code

```vue
<template>
  <div class="min-h-screen flex justify-center items-center">
    <div class="text-center">
      <h1 class="text-2xl mb-4">Authenticating...</h1>
      <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-500 mx-auto"></div>
    </div>
  </div>
</template>

<script setup>
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()

// Only run on client side
onMounted(async () => {
  const provider = route.params.provider
  const accessToken = route.query.access_token

  if (accessToken) {
    try {
      // Call Strapi's auth callback endpoint
      const response = await $fetch(`http://localhost:1337/api/auth/${provider}/callback`, {
        method: 'GET',
        query: {
          access_token: accessToken
        }
      })

      if (response.jwt && response.user) {
        // Store authentication data in localStorage (client-side only)
        if (process.client) {
          localStorage.setItem('jwt', response.jwt)
          localStorage.setItem('user', JSON.stringify({
            username: response.user.username,
            id: response.user.id,
            email: response.user.email
          }))
        }

        // Redirect to user page
        await router.push(`/users/${response.user.id}`)
      } else {
        throw new Error('No JWT or user data received')
      }
    } catch (error) {
      console.error('Authentication error:', error)
      await router.push('/login?error=auth_failed')
    }
  } else {
    console.error('No access token provided')
    await router.push('/login?error=no_token')
  }
})
</script>
```

In the code segment above, we are handling redirects from the Strapi backend after a user successfully logs in via a third-party provider like GitHub or Facebook.

Nuxt's routing system allows us to capture the dynamic part of the URL using square bracket notation. The file we created, `connect/[provider].vue` captures any path that matches `/connect/:provider`, where `:provider` could be GitHub, Facebook, or any other provider you’ve enabled in Strapi.

When a user is redirected back to our frontend by Strapi, the URL will contain an `access_token` query parameter. In the example:

```
http://localhost:3000/connect/github?access_token=XYZ
```

We extract this token using Nuxt 3's `useRoute()` composable, and then make an API call to the backend (Strapi) to exchange the token for a valid JWT and user object.

Once we receive this data, we store the JWT and user details in localStorage (you could also store it in cookies or Nuxt’s useState or useCookie, depending on your security needs). After storing the authentication details, we programmatically redirect the user to their profile page using `useRouter().push()`.

<!--
### Installing and using @nuxtjs/auth-next

Execute the following to install @nuxtjs/auth-next

```
yarn add @nuxtjs/auth-next //using yarn

npm install @nuxtjs/auth-next //using npm
```

Open up your `nuxt.config.js` file and add the following code

```js
modules: [
  //...other modules
  '@nuxtjs/auth-next',
]
```
-->

<!-- TODO: Add `http://localhost:1337/api/connect/github to GitHub OAuth settings -->

## Building the user page

Let's build the user profile page that fetches user data and notes, allows note creation, and supports infinite scroll.

Execute the following to create the user page:
```shell
cd pages
mkdir users
touch users/[id].vue
```

Add the following to `[id].vue`:
```vue
<template>
  <div>
    <Nav />
    <div class="sm:w-2/3 w-4/5 mt-10 mx-auto">
      <button class="button--blue" @click="createNewNote">Create Note</button>
      <h1 class="my-5 text-2xl font-black">Your Notes</h1>
      
      <div v-if="loading" class="text-center">
        <p>Loading your notes...</p>
      </div>
      
      <div v-else-if="notes && notes.length > 0" class="mx-auto sm:grid grid-cols-3 gap-2">
        <div
          v-for="(note, i) in notes"
          :key="i"
          class="rounded border-5 border-blue-400 p-10 sm:flex shadow-lg h-48 items-center justify-center text-center"
        >
          <NuxtLink :to="`/notes/${note.id}`">
            <h1 class="text-xl">
              {{ note.title }}
            </h1>
          </NuxtLink>
        </div>
      </div>
      
      <div v-else class="text-center">
        <p>No notes found. Create your first note!</p>
      </div>
      
      <infinite-loading 
        v-if="notes && notes.length > 0" 
        spinner="spiral" 
        @infinite="infiniteHandler" 
      />
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import InfiniteLoading from 'v3-infinite-loading'
import 'v3-infinite-loading/lib/style.css'

const route = useRoute()
const router = useRouter()

const userId = route.params.id
const notes = ref([])
const start = ref(0)
const limit = 3
const loading = ref(true)
const jwt = ref('')

onMounted(async () => {
  // Check if user is authenticated
  if (process.client) {
    const storedJwt = localStorage.getItem('jwt')
    const storedUser = localStorage.getItem('user')
    
    if (!storedJwt || !storedUser) {
      await router.push('/login')
      return
    }
    
    jwt.value = storedJwt
    
    // Verify the current user matches the page user
    const user = JSON.parse(storedUser)
    if (user.id.toString() !== userId) {
      await router.push(`/users/${user.id}`)
      return
    }
  }

  // Load initial notes
  await loadNotes()
})

const loadNotes = async () => {
  try {
    loading.value = true
    
    const response = await $fetch('/api/notes', {
      baseURL: 'http://localhost:1337',
      headers: { Authorization: `Bearer ${jwt.value}` },
      query: {
        'filters[users_permissions_user][id][$eq]': userId,
        'pagination[start]': start.value,
        'pagination[limit]': limit,
        'sort': 'publishedAt:DESC'
      }
    })

    notes.value = response.data || []
    loading.value = false
  } catch (error) {
    console.error('Error loading notes:', error)
    loading.value = false
    // If unauthorized, redirect to login
    if (error.statusCode === 401 || error.status === 401) {
      if (process.client) {
        localStorage.removeItem('jwt')
        localStorage.removeItem('user')
      }
      await router.push('/login')
    }
  }
}

const createNewNote = async () => {
  try {
    const newNotePayload = {
      data: {
        title: 'New Note',
        content: '<p>Start Writing</p>',
        users_permissions_user: userId,
        Editors: []
      }
    }

    const res = await $fetch('/api/notes', {
      method: 'POST',
      baseURL: 'http://localhost:1337',
      headers: { Authorization: `Bearer ${jwt.value}` },
      body: newNotePayload
    })

    if (res.data?.id) {
      await router.push(`/notes/${res.data.id}`)
    }
  } catch (error) {
    console.error('Error creating note:', error)
    alert('Failed to create note. Please try again.')
  }
}

const infiniteHandler = async ($state) => {
  try {
    start.value += limit
    
    const response = await $fetch('/api/notes', {
      baseURL: 'http://localhost:1337',
      headers: { Authorization: `Bearer ${jwt.value}` },
      query: {
        'filters[users_permissions_user][id][$eq]': userId,
        'pagination[start]': start.value,
        'pagination[limit]': limit,
        'sort': 'publishedAt:DESC'
      }
    })

    if (response.data && response.data.length > 0) {
      notes.value.push(...response.data)
      $state.loaded()
    } else {
      $state.complete()
    }
  } catch (error) {
    console.error('Error loading more notes:', error)
    $state.error()
  }
}
</script>

<style lang="scss" scoped></style>
```

In this step, we built the user page that displays the logged-in user's profile information and their notes, fetched from the Strapi backend. The page lists all notes belonging to the user and provides a button that allows the user to create a new note.

We also integrated infinite scrolling using the v3-infinite-loading package, which is compatible with Vue 3 and Nuxt 3. This ensures that additional notes are loaded automatically as the user scrolls down the page.

JWT tokens are retrieved from localStorage, where they were stored during the authentication redirect process. These tokens are then sent in the Authorization header to authenticate API requests.

<!--
### Setting up @nuxtjs/axios

[@nuxtjs/axios](https://axios.nuxtjs.org/) is automatically integrated with Nuxt.js if you chose the option while installing Nuxt.js. We just have to set up our `baseURL` .

Open up your `nuxt.config.js` file and add the following lines of code.

```js
// Axios module configuration: https://go.nuxtjs.dev/config-axios
axios: {
    baseURL: 'https://strapi-notesapp.herokuapp.com'
},
```


### Installing and Setting up @nuxtjs/strapi

Execute the following lines of code to install @nuxtjs/strapi.

```
yarn add @nuxtjs/strapi //using yarn

npm install @nuxtjs/strapi //using npm
```

Open up your `nuxt.config.js` file and add the following lines of code.

```js
modules: [
  //...other modules
  @nuxtjs/strapi
]

strapi: {
    entities: [ 'notes', 'users' ],
    url: 'http://localhost:1337'
}
```
-->

### Installing and using v3-infinite-loading

[`v3-infinite-loading`](https://github.com/oumoussa98/vue3-infinite-loading) is a package that allows us integrate infinite scrolling into our application.

Run the following command in your terminal to install `v3-infinite-loading`:
```shell
npm install v3-infinite-loading
```

Open up your `nuxt.config.ts` file and update it as follows:
```ts
import tailwindcss from "@tailwindcss/vite";

export default defineNuxtConfig({
  compatibilityDate: '2025-05-15',
  devtools: { enabled: true },
  css: ['~/assets/css/main.css'],
  plugins: ['~/plugins/fontawesome.js', '~/plugins/v3-infinite-loading.ts'],
  vite: {
    plugins: [
      tailwindcss(),
    ],
  },
})
```

Create a file called `infiniteloading.ts` in the `plugins` directory and add the following code:

```ts
import { defineNuxtPlugin } from '#app'
import InfiniteLoading from 'v3-infinite-loading'
import 'v3-infinite-loading/lib/style.css'

export default defineNuxtPlugin((nuxtApp) => {
  nuxtApp.vueApp.component('InfiniteLoading', InfiniteLoading)
})
```

### Building the Nav component

Execute the following to create a `Nav.vue` file
```shell
mkdir components
cd components
touch Nav.vue
```

Open up the `Nav.vue` file and fill it up with the following code.
```vue
<template>
  <nav class="bg-blue-600 text-white p-4">
    <div class="container mx-auto flex justify-between items-center">
      <NuxtLink to="/" class="text-xl font-bold hover:text-blue-200">
        NoteApp
      </NuxtLink>
      <div class="flex space-x-4 items-center">
        <span v-if="user" class="text-sm">
          Welcome, {{ user.username }}
        </span>
        <button 
          @click="logout" 
          class="bg-red-500 hover:bg-red-600 px-4 py-2 rounded text-sm transition-colors"
        >
          Logout
        </button>
      </div>
    </div>
  </nav>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const user = ref(null)

onMounted(() => {
  if (process.client) {
    const userData = localStorage.getItem('user')
    if (userData) {
      user.value = JSON.parse(userData)
    }
  }
})

const logout = () => {
  if (process.client) {
    localStorage.removeItem('jwt')
    localStorage.removeItem('user')
  }
  router.push('/login')
}
</script>
```

Run your Nuxt development server.

```shell
npm run dev
```

View the home page, perform the auth flow and then you should view the user landing page.

![User Landing Page](https://res.cloudinary.com/craigsims808/image/upload/v1750494446/strapi/sasn/user-landing-page_rdbrpi.png)

## Conclusion

We’ve come to the end of the second article, in the next article we’ll look at how we can integrate `vue-quill-editor` to enable users create content, image uploads, copying links and email sharing.

This tutorial is divided into 3 parts

*   1- [Getting started](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-getting-started) ✅
*   2- Adding Login Providers ✅
*   3- [Customization](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-customization) ⏭️

