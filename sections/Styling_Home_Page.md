
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
  <div class="min-h-screen flex flex-col sm:flex-row justify-center items-center text-center p-8 bg-gray-50">
    <div class="sm:w-1/2 w-full mb-8 sm:mb-0">
      <h1 class="text-4xl font-bold mb-4">Welcome to the NoteApp</h1>
      <p class="mb-2 text-lg text-gray-700">Your number one notes sharing application</p>
      <p class="mb-6 text-gray-600">Share your notes with anybody across the globe</p>
      <NuxtLink to="/login">
        <button class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 transition">
          Login
        </button>
      </NuxtLink>
    </div>
    <div class="sm:w-1/2 hidden sm:block">
      <img
        src="~assets/undraw_Sharing_articles_re_jnkp.svg"
        alt="Sharing Articles Illustration"
        class="w-full h-auto"
      />
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

![Notes App Home Page Preview](https://res.cloudinary.com/craigsims808/image/upload/v1750673565/strapi/sasn/r-nuxt-home-page_xgroea.png)
