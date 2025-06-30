## Introduction

In this tutorial, we'll be learning how to integrate social authentication into our [Strapi](https://strapi.io/) application. We'll be building a simple notes sharing application with Strapi backend and [Nuxt 3](https://nuxt.com/) frontend. We’ll also use [Quill.js](https://quilljs.com/) as our text editor, [Nodemailer](https://nodemailer.com/) for emails, we’ll integrate infinite scrolling and many more features.

This is the second part of the tutorial series on social authentication with Strapi. The [first part](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-getting-started) was all about getting started, we looked at a couple of things like getting started with Strapi, installing Strapi, and building a backend API with Strapi.

What you'll need for this tutorial:

- Basic knowledge of [Vue.js](https://vuejs.org/)
- Knowledge of JavaScript
- [Node.js](https://nodejs.org/en/) (Active LTS v20 or v22)

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
4. Under **Use cases** select `Authenticate and request data from users with Facebook Login` and click **Next**.
5. Under **Business** select `I don't want to connect a business portfolio yet.` and click **Next**.
6. Under **Publishing Requirements** leave everything as is and click **Next**.
7. Under **Overview** leave everything as is and click **Go to dashboard**.
8. Click **Basic** under **App settings** and under **App domains** add `http://localhost:1337` then click **Save changes**.
9. Click **Dashboard** and select **Customize the Authenticate and request data from users with Facebook Login use case**.
10. Under **Facebook Login** pick **Settings**.
11. Add the **Valid OAuth Redirect URI**, which is `http://localhost:1337/api/connect/facebook/callback` and select **Save changes**.
12. Copy your **App ID** and **App secret** by visiting **Basic** under **App settings**

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
export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  ssr: false, // Disable SSR for client-side storage
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

