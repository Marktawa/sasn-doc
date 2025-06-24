
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
