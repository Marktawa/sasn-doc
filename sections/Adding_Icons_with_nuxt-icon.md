
## Adding Icons with `nuxt-icon`

For this project, we will use the [nuxt-icon](https://github.com/nuxt-modules/icon) module to add icons to our application. This module allows you to easily include icons directly in your templates without additional configuration or plugins.

Install the module by running:

```bash
npm install nuxt-icon
```

Next, register the module in your `nuxt.config.ts` file:

```ts
export default defineNuxtConfig({
  modules: ['nuxt-icon']
})
```

The `name` attribute specifies the icon to display. `nuxt-icon` supports [Iconify](https://icon-sets.iconify.design/), which offers a wide range of icons from popular libraries like **Material Design Icons (mdi)**, **FontAwesome (fa)**, and more.

To find the exact icon names, browse the [Iconify icon collections](https://icon-sets.iconify.design/).

Using `nuxt-icon` simplifies icon management and keeps your project lightweight without the need for extra plugins or manual configuration.
