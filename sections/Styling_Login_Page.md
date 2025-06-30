
## Styling the Login Page

Update your `pages/login.vue` file with the following content:

```vue
<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-50 p-8">
    <div class="bg-white p-8 rounded shadow-md w-full max-w-md text-center">
      <h2 class="text-3xl font-bold mb-6">Social Login</h2>
      <div class="flex flex-col space-y-4">
        <a href="http://localhost:1337/api/connect/github">
          <button class="flex items-center justify-center w-full bg-gray-800 text-white px-4 py-2 rounded hover:bg-gray-900 transition">
            <nuxt-icon name="mdi:github" class="mr-2" />
            Login with GitHub
          </button>
        </a>
        <a href="http://localhost:1337/api/connect/facebook">
          <button class="flex items-center justify-center w-full bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 transition">
            <nuxt-icon name="mdi:facebook" class="mr-2" />
            Login with Facebook
          </button>
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

![Notes App Login Page Preview](https://res.cloudinary.com/craigsims808/image/upload/v1750673673/strapi/sasn/notes-app-login-page_p7cksj.png)
