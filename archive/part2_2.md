## Introduction

In this tutorial, we'll be learning how to integrate social authentication into our [Strapi](https://strapi.io/) application. We'll be building a simple notes sharing application with Strapi backend and [Nuxt 3](https://nuxt.com/) frontend. We’ll also use [Quill.js](https://quilljs.com/) as our text editor, [Nodemailer](https://nodemailer.com/) for emails, we’ll integrate infinite scrolling and many more features.

This is the second part of the tutorial series on social authentication with Strapi. The [first part](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-getting-started) was all about getting started, we looked at a couple of things like getting started with Strapi, installing Strapi, and building a backend API with Strapi.

What you'll need for this tutorial:

- Basic knowledge of [Vue.js](https://vuejs.org/)
- Knowledge of JavaScript
- [Node.js](https://nodejs.org/en/) (Active LTS v20 or v22)

Here’s what the final version of our application looks like

![](https://paper-attachments.dropbox.com/s_79BF70BAA3D70DB191E3093D0EAD323EB7F6E2886FE3F69EF110CEB48794F12A_1617459545504_notes_app-notes5.jpg)

![](https://paper-attachments.dropbox.com/s_79BF70BAA3D70DB191E3093D0EAD323EB7F6E2886FE3F69EF110CEB48794F12A_1617459752392_notes_app-notes6.jpg)

You can find the source code for the application using this [GitHub Repository Link](https://github.com/Marktawa/social-auth-strapi-nuxt)

## Table of Contents

* GitHub Authentication
* Configuring the GitHub Provider in Strapi
* Facebook Authentication
* Configuring the Facebook Provider in Strapi
* Home Page
* Login Page
* Handling Authentication Redirects
* Navigation Component
* Nuxt Configuration

## GitHub Authentication

1. Visit the OAuth Apps list page [https://github.com/settings/developers](https://github.com/settings/developers)
2. Click on **New OAuth App** button
3. Fill the information:
   - **Application name**: `strapi-notes-app`
   - **Homepage URL**: `http://localhost:1337/`
   - **Application description**: `Strapi notes sharing application`
   - **Authorization callback URL**: `http://localhost:1337/connect/github/callback`
4. Click **Register application** and you will be redirected to the OAuth application settings page.
5. Click **Generate a new client secret** and copy it. Copy the **Client ID** as well.

<!--Add screenshot of settings-->
<!-- Client ID: Ov23liOtqTI9d0mHYCZ1 -->
<!-- Client Secret: b0814556ede5bd8a9f7dde23ff5e9d6d77d74587 -->

![GitHub OAuth Settings](https://res.cloudinary.com/craigsims808/image/upload/v1750258727/strapi/sasn/github-oauth-settings-live_abbowv.png)

## Configuring the GitHub Provider in Strapi

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

## Facebook Authentication

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

## Building the Home Page

Delete the `app.vue` file in the root of your Nuxt frontend project.

Create or update `pages/index.vue`:

```vue
<template>
  <div>
    <h1>Welcome to the NoteApp</h1>
    <p>Your number one notes sharing application</p>
    <p>Share your notes with anybody across the globe</p>
    <NuxtLink to="/login">
      <button>Login</button>
    </NuxtLink>
  </div>
</template>
```

In this step, we've created a simple home page that serves as the entry point to our notes application. By deleting the default `app.vue` file, we allow Nuxt 3's file-based routing system to take control, where the `pages/index.vue` file automatically becomes the route for the root URL (`/`).

The home page includes:

* A welcome message introducing the application
* A brief description of the app's purpose
* A navigation link to the login page using Nuxt's `<NuxtLink>` component, which provides client-side navigation for better performance.

This minimal structure gives users a clear starting point and directs them toward the authentication flow we'll implement next.

Run your Nuxt development server to view your home page.
![Notes app Home page](https://res.cloudinary.com/craigsims808/image/upload/v1750673565/strapi/sasn/r-nuxt-home-page_xgroea.png)

## Login Page

Create `pages/login.vue`:

```vue
<template>
  <div>
    <h2>Social Login</h2>
    <div>
      <a href="http://localhost:1337/api/connect/github">
        <button>Login with GitHub</button>
      </a>
      <a href="http://localhost:1337/api/connect/facebook">
        <button>Login with Facebook</button>
      </a>
    </div>
  </div>
</template>
```

The login page provides users with social authentication options through GitHub and Facebook.

When users click Social Login buttons, they're taken to the respective social platform's OAuth flow. After successful authentication on the external platform, users are redirected back to our Strapi backend, which then processes the authentication and redirects them to our frontend with the necessary authentication tokens.

![Notes app Login page](https://res.cloudinary.com/craigsims808/image/upload/v1750673673/strapi/sasn/notes-app-login-page_p7cksj.png)

## Handling Authentication Redirects

Create the directory structure and file:

```bash
mkdir -p pages/connect
touch pages/connect/[provider].vue
```

Fill `pages/connect/[provider].vue`:

```vue
<template>
  <div>
    <h1>Authenticating...</h1>
  </div>
</template>

<script setup>
const route = useRoute()
const router = useRouter()

const provider = route.params.provider
const access_token = route.query.access_token

onMounted(async () => {
  try {
    const data = await $fetch(`/auth/${provider}/callback?access_token=${access_token}`, {
      baseURL: 'http://localhost:1337/api'
    })
    
    const { jwt, user } = data
    
    // Store in session storage
    sessionStorage.setItem('jwt', jwt)
    sessionStorage.setItem('user', JSON.stringify({
      username: user.username,
      id: user.id,
      email: user.email
    }))
    
    await router.push(`/users/${user.id}`)
  } catch (error) {
    console.error('Authentication error:', error)
    await router.push('/login')
  }
})
</script>
```

This dynamic route captures the authentication callback from Strapi, extracts the JWT token and user data, stores them in session storage, and redirects the user to their profile page. The `[provider]` filename creates a catch-all route that handles both GitHub and Facebook authentication flows.

## User Page

Create the directory structure and file:

```bash
mkdir -p pages/users
touch pages/users/[id].vue
```

Fill `pages/users/[id].vue`:

```vue
<template>
  <div>
    <Nav />
    <div>
      <button @click="createNewNote">Create Note</button>
      <h1>Your Notes</h1>
      <div v-if="notes">
        <div v-for="note in notes" :key="note.id">
          <NuxtLink :to="`/notes/${note.id}`">
            <h2>{{ note.title }}</h2>
          </NuxtLink>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const route = useRoute()
const router = useRouter()

// Get stored authentication data
const jwt = process.client ? sessionStorage.getItem('jwt') : null
const user = process.client ? JSON.parse(sessionStorage.getItem('user') || '{}') : {}

// Fetch user and notes data
const { data: userData } = await $fetch(`/users/${route.params.id}`, {
  baseURL: 'http://localhost:1337/api',
  headers: {
    Authorization: `Bearer ${jwt}`
  }
})

const { data: notes } = await $fetch('/notes', {
  baseURL: 'http://localhost:1337/api',
  query: {
    'filters[users_permissions_user][id][$eq]': route.params.id,
    'sort[0]': 'publishedAt:desc',
    'pagination[start]': 0,
    'pagination[limit]': 10
  }
})

const createNewNote = async () => {
  try {
    const { data } = await $fetch('/notes', {
      baseURL: 'http://localhost:1337/api',
      method: 'POST',
      headers: {
        Authorization: `Bearer ${jwt}`,
        'Content-Type': 'application/json'
      },
      body: {
        data: {
          title: 'New Note',
          content: 'Start Writing',
          users_permissions_user: user.id
        }
      }
    })
    
    await router.push(`/notes/${data.id}`)
  } catch (error) {
    console.error('Error creating note:', error)
  }
}
</script>
```

This protected user dashboard fetches and displays the authenticated user's notes from Strapi. It includes functionality to create new notes and uses the stored JWT token for authorized API requests. The dynamic [id] route parameter allows each user to access their personal note collection.

## Navigation Component

Create `components/Nav.vue`:

```vue
<template>
  <nav>
    <div>
      <h1>NotesApp</h1>
    </div>
    <div>
      <NuxtLink v-if="user?.id" :to="`/users/${user.id}`">
        {{ user.username }}
      </NuxtLink>
      <button v-if="user?.username" @click="logout">Logout</button>
      <NuxtLink v-else to="/login">
        <button>Sign in</button>
      </NuxtLink>
    </div>
  </nav>
</template>

<script setup>
const router = useRouter()

const user = ref({})

onMounted(() => {
  if (process.client) {
    const userData = sessionStorage.getItem('user')
    if (userData) {
      user.value = JSON.parse(userData)
    }
  }
})

const logout = () => {
  if (process.client) {
    sessionStorage.removeItem('user')
    sessionStorage.removeItem('jwt')
  }
  router.push('/login')
}
</script>
```

This reusable navigation component conditionally displays user information and authentication controls based on login status, while handling logout functionality by clearing session storage.

Run your Nuxt development server and test the complete authentication flow from login to user dashboard.

![User page](https://res.cloudinary.com/craigsims808/image/upload/v1750674280/strapi/sasn/notes-app-user-page_g1q6yv.png)

## Nuxt Configuration

Update your `nuxt.config.ts`:

```ts
import tailwindcss from "@tailwindcss/vite";

export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  ssr: false, // Disable SSR for client-side storage
  css: ['~/assets/css/main.css'],
  vite: {
    plugins: [
      tailwindcss(),
    ],
  },
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

The configuration disables SSR to support client-side session storage and sets up a proxy to route API requests to the Strapi backend during development.

## Setting Up the Default Layout

We will define the default layout for our Nuxt 3 application. This layout applies to all pages and ensures consistent styling and structure throughout the app.

Create a `layouts` directory in the root of your Nuxt 3 project (if it does not already exist) and add a file named `default.vue` inside it:

```bash
mkdir -p layouts
touch layouts/default.vue
```

Then, open `layouts/default.vue` and add the following code:

```vue
<template>
  <div>
    <NuxtPage />
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

In this layout file, the `<NuxtPage />` component serves as a placeholder where the content of each page will be rendered. The `<style>` block contains global CSS rules that apply across the application, ensuring consistent fonts, button styles, editor appearance, and layout behavior.

This setup ensures that all pages share a uniform look and feel without the need to redefine these styles in every component.

## Styling the Home Page

Before styling the Home Page, make sure you have the required illustration in your project.

Create an `assets` directory in your Nuxt 3 project (if it does not already exist) and download the following SVG illustration into that folder:

```bash
mkdir -p assets
curl -o assets/undraw_Sharing_articles_re_jnkp.svg https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_Sharing_articles_re_jnkp.svg
```

Alternatively, you can manually download the file from the following link and save it as:

```
assets/undraw_Sharing_articles_re_jnkp.svg
```

🔗 [undraw_Sharing_articles_re_jnkp.svg](https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_Sharing_articles_re_jnkp.svg)

Update your `pages/index.vue` file with the following content:

```vue
<template>
    <div class="min-h-screen flex justify-center items-center text-center mx-auto sm:pl-24 bg-yellow-200">
        <div class="w-1/2 sm:text-left sm:m-5">
            <div>
                <h1 class="text-3xl sm:text-6xl font-black sm:pr-10 leading-tight text-blue-900">
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
            <img class="" src="~assets/undraw_Sharing_articles_re_jnkp.svg" />

        </div>
    </div>
</template>

<script setup>
// No script logic needed for this page
</script>

<style>
/* Sample Tailwind apply (if preferred)
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

In this step, we enhanced the Home Page using Tailwind CSS utility classes to create a modern, responsive layout. On larger screens (`sm` and above), the text and image appear side-by-side; on mobile devices, the illustration is hidden to maintain a clean layout.

Additionally, a `<style>` block is included to maintain legacy custom styles from the original article. While Tailwind covers most styles, this block preserves font and spacing choices for those who wish to retain this specific aesthetic.

To view the styled Home Page, start the development server:

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) in your browser. The Home Page should appear like this:

![Notes App Home Page Preview](https://res.cloudinary.com/craigsims808/image/upload/v1750359324/strapi/sasn/nuxt-home-page_xq6bih.png)

## Adding Icons with `nuxt-icon`

For this project, we will use the [`nuxt-icon`](https://github.com/nuxt/icon) module to add icons to our application. This module allows you to easily include icons directly in your templates without additional configuration or plugins.

Install the module by running:

```shell
npm install @nuxt/icon
```

Next, register the module in your `nuxt.config.ts` file:
```ts
import tailwindcss from "@tailwindcss/vite";

export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  ssr: false,
  css: ['~/assets/css/main.css'],
  modules: ['@nuxt/icon'],
  vite: {
    plugins: [
      tailwindcss(),
    ],
  },
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

You can now use icons in any Vue component using the `<Icon>` component.

## Styling the Login Page

Download the following SVG illustration into the `assets` folder:

```bash
curl -o assets/undraw_secure_login_pdn4.svg https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_secure_login_pdn4.svg
```

Alternatively, you can manually download the file from the following link and save it as:

```
assets/undraw_secure_login_pdn4.svg
```

🔗 [undraw_secure_login_pdn4.svg](https://raw.githubusercontent.com/oviecodes/nuxt-strapi-notesapp/refs/heads/master/assets/undraw_secure_login_pdn4.svg)

Update your `pages/login.vue` file with the following content:

```vue
<template>
    <div class="min-h-screen flex justify-center items-center text-center mx-auto sm:pl-24 bg-yellow-200">
        <div class="w-1/2 hidden sm:block m-5 p-6">
            <img src="~assets/undraw_secure_login_pdn4.svg" />
        </div>
        <div class="sm:w-1/2 w-4/5">
            <h2 class="m-5 font-black text-3xl">Social Login</h2>
            <div class="shadow-xl bg-white p-10">
                <a href="http://localhost:1337/api/connect/github" class="cursor-pointer m-3 button--blue shadow-xl">
                    <span><Icon name="fa6-brands:github" /></span>
                    Github
                </a>
                <a href="http://localhost:1337/api/connect/facebook" class="cursor-pointer m-3 button--blue shadow-xl">
                    <span>
                        <Icon name="fa6-brands:facebook" />
                    </span>
                    Facebook
                </a>
            </div>
        </div>
    </div>
</template>

<script setup>
// No script logic needed for this page
</script>
```

In this step, we styled the Login Page using **Tailwind CSS utility classes** and included icons for GitHub and Facebook using the `nuxt-icon` module. The buttons are styled to reflect the branding of their respective platforms, ensuring a familiar and intuitive user experience.

To view the styled Login Page, start the development server:

```bash
npm run dev
```

Then open [http://localhost:3000/login](http://localhost:3000/login) in your browser. The Login Page should appear similar to the following:

![Notes App Login Page Preview](https://res.cloudinary.com/craigsims808/image/upload/v1750365270/strapi/sasn/login-page_jiutci.png)

## Styling the User Page

Update your `pages/users/[id].vue` file with the following content:

```vue
<template>
  <div>
    <Nav />
    <div class="sm:w-2/3 w-4/5 mt-10 mx-auto">
      <button class="button--blue" @click="createNewNote">Create Note</button>
      <h1 class="my-5 text-2xl font-black">Your Notes</h1>
      <div v-if="notes.length" class="mx-auto sm:grid grid-cols-3 gap-2">
        <div
          v-for="note in notes"
          :key="note.id"
          class="rounded border-5 border-blue-400 p-10 sm:flex shadow-lg h-48 items-center justify-center text-center"
        >
            <NuxtLink :to="`/notes/${note.id}`">
                <h2 class="text-xl">{{ note.title }}</h2>
            </NuxtLink>
        </div>
      </div>
      <div v-else class="text-gray-600 mt-4">No notes available.</div>
    </div>
  </div>
</template>

<script setup>
const route = useRoute()
const router = useRouter()

const jwt = process.client ? sessionStorage.getItem('jwt') : null
const user = process.client ? JSON.parse(sessionStorage.getItem('user') || '{}') : {}

const { data: userData } = await $fetch(`/users/${route.params.id}`, {
  baseURL: 'http://localhost:1337/api',
  headers: {
    Authorization: `Bearer ${jwt}`
  }
})

const { data: notes } = await $fetch('/notes', {
  baseURL: 'http://localhost:1337/api',
  query: {
    'filters[users_permissions_user][id][$eq]': route.params.id,
    'sort[0]': 'publishedAt:desc',
    'pagination[start]': 0,
    'pagination[limit]': 10
  }
})

const createNewNote = async () => {
  try {
    const { data } = await $fetch('/notes', {
      baseURL: 'http://localhost:1337/api',
      method: 'POST',
      headers: {
        Authorization: `Bearer ${jwt}`,
        'Content-Type': 'application/json'
      },
      body: {
        data: {
          title: 'New Note',
          content: 'Start Writing',
          users_permissions_user: user.id
        }
      }
    })
    
    await router.push(`/notes/${data.id}`)
  } catch (error) {
    console.error('Error creating note:', error)
  }
}
</script>
```

In this step, we styled the User Page to display a clean dashboard layout. It includes a top bar with a "Create Note" button, and a list of notes with hover effects. Tailwind utility classes are used for layout, spacing, colors, and typography.

The `<style>` block adds a global font family override for headings to maintain visual consistency.

## Styling the Nav Component

Update your `components/Nav.vue` file with the following content:

```vue
<template>
  <div class="p-6 mb-4 shadow-lg bg-dark">
    <div class="sm:w-2/3 mx-auto flex justify-between items-center">
        <div>
            <h1>NotesApp</h1>
        </div>
        <div class="flex sm:space-x-5 space-x-2 items-center">
            <NuxtLink :to="`/users/${user.id}`">
             <p v-if="user?.id">
                <span><Icon name="fa-solid:user" /></span>
                {{ user.username }}
            </p>
            </NuxtLink>
            <button class="button--blue" @click="logout" v-if="user?.username">Logout</button>
            <NuxtLink class="button--green" v-else to="/login">Sign in</NuxtLink>
        </div>
    </div>    
  </div>
</template>

<script setup>
const router = useRouter()

const user = ref({})

onMounted(() => {
  if (process.client) {
    const userData = sessionStorage.getItem('user')
    if (userData) {
      user.value = JSON.parse(userData)
    }
  }
})

const logout = () => {
  if (process.client) {
    sessionStorage.removeItem('user')
    sessionStorage.removeItem('jwt')
  }
  router.push('/login')
}
</script>
```

To view the styled User Page together with the styled Nav component, start the development server then open [localhost:3000/users/1](http://localhost:3000/users/1). The User Page should appear similar to the following:

![Styled User Page](https://res.cloudinary.com/craigsims808/image/upload/v1750794367/strapi/sasn/styled-user-page_nicuwj.png)

## Conclusion

This stage of the project provides the core functionality for social authentication with Strapi and Nuxt 3. The application now supports GitHub and Facebook login, user authentication, and basic note management without the complexity of additional styling frameworks or infinite loading features.

In the next part of this series, we'll look at adding more advanced features like note editing and sharing capabilities. In the next article we’ll look at how we can integrate `vue-quill-editor` to enable users create content, image uploads, copying links and email sharing.

This tutorial is divided into 3 parts

*   1- [Getting started](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-getting-started) ✅
*   2- Adding Login Providers ✅
*   3- [Customization](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-customization) ⏭️
