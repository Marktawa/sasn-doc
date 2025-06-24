## Introduction

Social Authentication is becoming popular in the software industry because of the convenience it provides to users.

In this tutorial, we'll be learning how to integrate social authentication into our [Strapi](https://strapi.io/) Application, and we'll be building a simple Notes Sharing Application with Strapi backend and [Nuxt.js](https://strapi.io/integrations/nuxtjs-cms) frontend, we'll also use [Quill.js](https://quilljs.com/) as our text editor, [Nodemailer](https://nodemailer.com/) for emails, we'll integrate infinite scrolling and many more features.

## Prerequisites

What you will need for this tutorial:
- Basic knowledge of [Vue.js](https://vuejs.org/)
- Knowledge of JavaScript
- [Node.js](https://nodejs.org/en/) (Active LTS v20 or v22)

## What you will build

Here's what the final version of our Application looks like:

[Application preview](https://api-prod.strapi.io/uploads/Application_preview_d0be607826.png)

![Application preview](https://api-prod.strapi.io/uploads/Application_preview_d0be607826.png)

You can find the source code for the application using this [GitHub Repository Link](https://github.com/Marktawa/social-auth-strapi-nuxt)

<!--
You can find the GitHub repository for the [frontend application](https://github.com/oviecodes/nuxt-strapi-notesapp) and the repository for the [backend application](https://github.com/oviecodes/strapi_notesapp).
-->

Let's get started with our Strapi Backend setup.

## What is Strapi?

The [Strapi documentation](https://docs.strapi.io/cms/quick-start) says that "Strapi offers a lot of flexibility. Whether you want to go fast and quickly see the final result, or would rather dive deeper into the product, we got you covered."

By making the admin panel and API extensible through a plugin system, Strapi enables the world's largest companies to accelerate content delivery while building beautiful digital experiences.

## Installing Strapi

The [documentation](https://docs.strapi.io/cms/installation/cli) walks you through installing Strapi from the CLI, the minimum requirements for running Strapi, and how to create a quickstart project.

The Quickstart project uses SQLite as the default database.

```bash
yarn create strapi-app backend # using yarn
npx create-strapi-app backend  # using npx
```
 
Your package manager will create a directory with the name `backend` and will install Strapi.

If you have followed the instructions correctly, you should have Strapi installed on your machine.

```bash
cd backend
yarn develop # using yarn
npm run develop # using npm
```

To start our development server, Strapi starts our app on `http://localhost:1337/admin`

## Basic overview of a Strapi application

Strapi provides an admin panel to edit, create APIs, and provide editable code, and it's easy to edit the code and use JavaScript.

On the Strapi admin panel, we have the **Content Manager** which contains `collection types`. These are similar to models or schema in a database. A collection type has fields that are like properties. Strapi offers a wide range of options for fields, including `Text`, `Short Text`, `Relations`, and many more.

We also have the **Content-Type Builder** and the **Media Library**. The **Content-Type Builder** allows users to create different content types, while the **Media Library** contains all the media uploads done by users.

![Basic overview of Strapi app](https://res.cloudinary.com/craigsims808/image/upload/v1750250272/strapi/sasn/basic-strapi-overview_fhjixd.png)

Finally, we have the **Marketplace** and the **Settings** sections. The **Settings** section allows us to set the user permissions, roles, and many more essential functions.

Strapi also provides us with code generated based on the User's setting and actions in the admin panel, and we can open up this code with our favorite code editor to explore it more.

I'm using Visual Studio Code.

![Strapi source code in VS Code](https://res.cloudinary.com/craigsims808/image/upload/v1750250969/strapi/sasn/strapi-in-vscode_o8ffo7.png)

The `API` section (directory) contains the different Content-Type present in our Application. It has a code to edit depending on what goals we plan to achieve with our Application.

We can edit default settings, and we can create new services, routes, endpoints, add environment variables and do so much more all from the code.

For more information about the code composition of Strapi and what each section (directory) does, feel free to look at the Strapi [project structure](https://docs.strapi.io/cms/project-structure).

## Building the backend API

Now that the Strapi backend is up and running, we can start building the backend API. We'll create the backend using the Strapi admin panel.

![Strapi Admin Panel Content Manager](https://res.cloudinary.com/craigsims808/image/upload/v1750251232/strapi/sasn/strapi-admin-panel-content-manager_a7ydf4.png)

## Building the Notes collection-type

Next, we are going to create the Notes Collection Type. Follow these steps below to create your first Collection Type.

1.  Open up the Strapi Admin Panel.
2.  Navigate to the **Content-Type Builder** section
3.  Under **COLLECTION TYPES**, click **Create new collection type**
4.  A popup window should come up and prompt you to enter a display name, type `Note` and then click **Continue**.
5.  Another popup should come up where you can choose the fields you want the Collection Type to have.

Next, we are going to choose all the fields on the **Notes** Collection Type. Follow the step below to choose your types.

1.  On the popup window, click `Text`, name the field `title`, leave the type selection as `Short Text`, and add another field.
2.  Select `Rich Text`, name the field `content`, then click on add another field
3.  Select `JSON`, name the field `Editors`, then click on add another field
4.  Select `Relations`, and then click on the dropdown that's on the right side of the popup window, select `User (from: users-permissions-user)`, then click on Users have many notes. It should look like the image below.
5. Click **Finish** and wait for the server to restart.

![Relations User_permissions_user](https://res.cloudinary.com/craigsims808/image/upload/v1750252168/strapi/sasn/relation-notes-users-permissions-user_p44ryh.png)

If you follow the steps above correctly, the final **Notes** collection type schema should look like the image below.

![Notes collection type with fields](https://res.cloudinary.com/craigsims808/image/upload/v1750252572/strapi/sasn/final-notes-collection-type_sjkjje.png)

### Setting the permissions for Note collection type

Now, we have successfully created our **Notes** collection type, let's add and assign a permission level on the **Notes** collection type for authenticated User by following the steps below.

1.  Click **Settings** in the side menu
2.  Click **Roles** under **USERS & PERMISSIONS PLUGIN**.
3.  It will display a list of roles. Click **Authenticated**.
4.  Scroll down, under **Permissions**, click **Note**, then check all the checkboxes except `find`.

![Note Permissions Authenticated User](https://res.cloudinary.com/craigsims808/image/upload/v1750253535/strapi/sasn/note-permissions-authenticated-user_ncrnta.png)

### Setting user-permissions for authenticated user

Next, we will also create and assign user permissions for an authenticated user by following the steps below.

1.  Scroll down, under **User-Permissions**, navigate to **USER**, then check `findOne` and `me`.
2.  Click Save, then go back.

![User Permissions Authenticated User](https://res.cloudinary.com/craigsims808/image/upload/v1750253989/strapi/sasn/user-permissions-authenticated-user_ax0b5t.png)

### Setting the permissions on the Notes collection-type for Public user

Next, we will also create and assign permissions on **Notes** collection-type for our public users by following the steps below.

1.  Go back to **Settings**, click **Roles** under **USER & PERMISSIONS PLUGIN** then click **Public**
2.  Scroll down, under **Permissions**, click **Note**, then check `findOne` and `find`.

![Note permissions for Public user](https://res.cloudinary.com/craigsims808/image/upload/v1750254436/strapi/sasn/note-public-user-permissions_pywpti.png)

### Setting the Media Library permissions for public user

We will also create and assign upload permissions for our public users by following the steps below.

1.  Scroll down, under **Media Library**.
2.  Select `find`, `destroy`, `findOne` and `upload`.

![Media Library permissions for public user](https://res.cloudinary.com/craigsims808/image/upload/v1750254671/strapi/sasn/medila-library-permissions-public-user_ts9fvy.png)

### Setting user-permissions for public user

Lastly, we will also create and assign user permissions for our public users by following the steps below.

1.  Scroll down, under **User-permissions**
2.  Navigate to **USER**.
3.  Then check `find`, `findOne`, and `me`.

![User Permissions for public user](https://res.cloudinary.com/craigsims808/image/upload/v1750254850/strapi/sasn/user-permissions-public-user_vxiede.png)

Click **Save** to save your permissions.

## Installing Nuxt.js

Next, we will install and configure Nuxtjs to work with our Strapi backend.

To install Nuxt.js, visit [the Nuxt.js docs](https://nuxt.com/docs/getting-started/installation) or run one of these commands to get started.

```shell
npm create nuxt frontend
```

<!--
The command will ask you some questions (name, Nuxt options, U.I. framework, TypeScript, linter, testing framework, etc.).

We want to use Nuxt in SSR mode, Server Hosting, and Tailwind CSS as our preferred CSS framework, so select those, then choose whatever options for the rest.

Preferably leave out C.I., commit-linting, style-linting, and the rest but do whatever you like. All we'll be needing in this tutorial is what I've mentioned above.
-->

Answer the prompts as follows:

```
❯ Which package manager would you like to use?
npm
> Initialize git repository?
No
❯ Would you like to install any of the official modules?
No
```

Once all questions are answered, it will install all the dependencies. The next step is to navigate to the project folder and launch it.

```shell
cd frontend
```

<!--
Update `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  compatibilityDate: '2025-05-15',
  devtools: { enabled: true },
    vite: {
    server: {
      allowedHosts: true,
    },
  },
})
```
-->

```shell
npm run dev
```

We should have the Nuxt.js Application running on [`http://localhost:3000`](http://localhost:3000).

![Nuxt Default Frontend](https://res.cloudinary.com/craigsims808/image/upload/v1750255505/strapi/sasn/nuxt-default-frontend_tqoblo.png)

## Install Tailwind CSS with Nuxt

Install `@tailwindcss/vite` and its peer dependencies via npm.
```shell
npm install tailwindcss @tailwindcss/vite
```

Add the `@tailwindcss/vite` plugin to your Nuxt configuration file, `nuxt.config.ts` as a Vite plugin.
```ts
import tailwindcss from "@tailwindcss/vite";
export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  vite: {
    plugins: [
      tailwindcss(),
    ],
  },
});
```

<!--
Update `nuxt.config.ts`:

```ts
import tailwindcss from "@tailwindcss/vite";
export default defineNuxtConfig({
  compatibilityDate: '2025-05-15',
  devtools: { enabled: true },
    vite: {
    plugins: [
      tailwindcss(),
    ],
    server: {
      allowedHosts: true,
    },
  },
})
```
-->

Create an `./assets/css/main.css` file and add an `@import` that imports Tailwind CSS.
```css
@import "tailwindcss";
```

Add your newly-created `./assets/css/main.css` to the `css` array in your `nuxt.config.ts` file.

```ts
import tailwindcss from "@tailwindcss/vite";
export default defineNuxtConfig({
  compatibilityDate: "2024-11-01",
  devtools: { enabled: true },
  css: ['~/assets/css/main.css'],
  vite: {
    plugins: [
      tailwindcss(),
    ],
  },
});
```

<!--
Update `nuxt.config.ts`:

```ts
import tailwindcss from "@tailwindcss/vite";
export default defineNuxtConfig({
  compatibilityDate: '2025-05-15',
  devtools: { enabled: true },
  css: ['~/assets/css/main.css'],
  vite: {
    plugins: [
      tailwindcss(),
    ],
    server: {
      allowedHosts: true,
    },
  },
})
```
-->

## Conclusion

That should be all for now. The following article will examine how we can add various providers (Facebook, GitHub) to our Strapi backend and build the appropriate frontend pages to handle redirects.

This tutorial is divided into 3 parts 1) Getting started ✅ 2) [Adding Login Providers](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-adding-social-providers) ⏭️ 3) [Customization](https://strapi.io/blog/social-authentication-with-strapi-and-nuxt-js-customization)