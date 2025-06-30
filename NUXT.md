# Getting Started with Nuxt 

In this tutorial you will learn how to get started with Nuxt.

Nuxt is a framework for building web apps. It is based on Vue.js, a frontend JavaScript library.

## Prerequisites

- [Railway Account](https://railway.com). Host your app on Railway.
- You should have Node.js installed on your machine. Download it from - [Node.js __ Download Node.js](https://nodejs.org/en/download) webpage

### Installing Node.js using `fnm`

You can use [`fnm`](https://github.com/Schniz/fnm) a cross-platform Node.js version manager.

Open your terminal, download and install `fnm` using this command:
```shell
curl -o- https://fnm.vercel.app/install | bash
```

Download and install an Long Term Support (LTS) version of Node.js:
```shell
fnm install lts
```

Verify the Node.js version:
```shell
node -v
```

## Create Hello World Nuxt app

### Download and Install Nuxt

On your local machine, create a new folder named `my-app`. This will be your Nuxt project directory.
```shell
mkdir my-app
```

Open `my-app`.
```shell
cd my-app
```

Create a `package.json` file inside `my-app`.
```shell
touch package.json
```

Add the following to `package.json`:
```json
{
  "scripts": {
    "build": "nuxt build",
    "dev": "nuxt dev",
    "generate": "nuxt generate",
    "preview": "nuxt preview",
    "postinstall": "nuxt prepare",
    "start": "node .output/server/index.mjs"
  },
  "dependencies": {
    "nuxt": "latest",
    "vue": "latest"
  }
}
```

Install the dependencies listed in the `package.json` file by running the following command:
```shell
npm install
```

### Create Hello World html page

Create a `public` folder then a `dev` folder inside `public`:
```shell
mkdir -p public/dev
```

Create a file named `index.html` inside the `dev` folder:
```shell
touch public/dev/index.html
```

Add the following code to `index.html`:
```html
<body>
    <h1>Hello, World!</h1>
</body>
```

Run your Nuxt app development server:
```shell
npm run dev
```

This starts the Nuxt development server on port `3000`. Visit `localhost:3000/dev` in your browser to view the home webpage you just created:
![Static Home web page Hello World](https://res.cloudinary.com/craigsims808/image/upload/v1751151422/strapi/getting-started-with-nuxt/static-hello-world-web-page_rjkxnm.png)

### Create Hello World Nuxt app

Create a `pages` folder:
```shell
mkdir pages
```

Create an `index.vue` file inside the `pages` folder:
```shell
touch pages/index.vue
```

Add this:
```vue
<template>
    <h1>Hello, World!</h1>
</template>
```

If you care to notice, the contents of `public/dev/index.html` and `pages/index.vue` are the same. This is because `public/dev` folder will be used as a test bed to store sample html files before the vue file is created. It makes for a seamless dev process.

Run the development server:
```shell
npm run dev
```

Visit `http://localhost:3000` to view your site. You should see a web page with the text "Hello, World!".
![Nuxt page Hello World](https://res.cloudinary.com/craigsims808/image/upload/v1751151422/strapi/getting-started-with-nuxt/static-hello-world-web-page_rjkxnm.png)

## Build your Hello World Nuxt app

Stop your Nuxt project development server by pressing the keyboard combo `CTRL` + `C` in your terminal.

Run the `build` command
```shell
npm run build
```

This creates an `.output` directory with all your your application, server and dependencies ready for production.

Run the following command to test the production version of your Nuxt app:
```shell
npm run start
```

Visit `http://localhost:3000` to view your site. You should once again see a web page with the text "Hello, World!".
![Nuxt page Hello World](https://res.cloudinary.com/craigsims808/image/upload/v1751151422/strapi/getting-started-with-nuxt/static-hello-world-web-page_rjkxnm.png)

## Deploy Nuxt app to Railway

Create a new file named `.gitignore` in your Nuxt project folder:
```shell
touch .gitignore
```

Add the following to `.gitignore`:
```.gitgnore
# Nuxt dev/build outputs
.output
.data
.nuxt
.nitro
.cache
dist

# Node dependencies
node_modules

# Logs
logs
*.log

# Misc
.DS_Store
.fleet
.idea

# Local env files
.env
.env.*
!.env.example
```

Install the [Railway CLI tool](https://docs.railway.com/guides/cli):
```shell
bash <(curl -fsSL cli.new)
```

Login to your Railway account:
```shell
railway login --browserless
```

Create a new Railway project:
```shell
railway init
```

Link your current Nuxt project directory to the Railway project you just created:
```shell
railway link
```

Create a new service in the Railway project:
```shell
railway add --service my-nuxt-app
```

Associate your Nuxt project directory with the service you just created in the Railway project:
```shell
railway service
```

Deploy your app.
```shell
railway up
```

When the site is ready, generate a domain and test your deployment.
```shell
railway domain
```

Visit the newly generated Railway domain for your Nuxt production app to view:
![Nuxt app deployed on Railway](https://res.cloudinary.com/craigsims808/image/upload/v1751152834/strapi/getting-started-with-nuxt/deployed-hello-world-nuxt-app-railway_pw8jym.png)

## Create Form Submission Nuxt App

### Create Form Submission Static HTML pages

Inside the `public/dev` folder, create a new file named `form.html`:
```shell
touch public/dev/form.html
```

Add the following code to `public/dev/form.html`:
```html
<body>
    <h1>Hello, World!</h1>
    <form action="submission.html" method="GET">
        <input type="text" />
        <button type="submit">Submit</button>
    </form>
</body>
```

Inside the `public/dev` folder, create a new file named `submission.html`:
```shell
touch public/dev/submission.html
```

Add the following code to `public/dev/submission.html`:
```html
<body>
    <h1>Hello, Mark!</h1>
    <a href="form.html">Return to Form</a>
</body>
```

Run the development server:
```shell
npm run dev
```

Visit  `localhost:3000/dev/form.html`.
![Form html webpage](https://res.cloudinary.com/craigsims808/image/upload/v1751153654/strapi/getting-started-with-nuxt/form-html-webpage_dcorly.png)

Enter a value into the form and submit.
![Form submission response html webpage](https://res.cloudinary.com/craigsims808/image/upload/v1751153720/strapi/getting-started-with-nuxt/form-submission-response-html_muhsvs.png)

This flow simulates what we want to build using Nuxt. In the case of Nuxt the form submission response will be dynamic.

### Log "Hello, World!" to the Nuxt server console

Create a `pages/form.vue` file:
```shell
touch pages/form.vue
```

Add the following code to `pages/form.vue`:
```vue
<template>
    <h1>Hello, World!</h1>
</template>

<script setup>
if (process.server) {
    console.log('Hello, World!');
}
</script>
```

This will log the text "Hello, World!" in your Nuxt development server terminal each time `localhost:3000/form` is loaded.

Run the Nuxt development server:
```shell
npm run dev
```

Visit `localhost:3000/form` in your browser and you should see the text "Hello, World!".
![Hello World with console webpage](https://res.cloudinary.com/craigsims808/image/upload/v1751151422/strapi/getting-started-with-nuxt/static-hello-world-web-page_rjkxnm.png)

Check your Nuxt development server console and you should see the text "Hello, World!".
![Hello World inside console](https://res.cloudinary.com/craigsims808/image/upload/v1751155311/strapi/getting-started-with-nuxt/hello-world-with-console-terminal_h7zm7l.png)


### Log "Hello, World!" for every form submission

Update your `pages/form.vue` file with the following code:
```vue
<template>
    <h1>Hello, World!</h1>
    <form action="/api/submit" method="POST">
        <input type="text" name="name" />
        <button type="submit">Submit</button>
    </form>
</template>
```

This renders a simple form at `/form` and submits data via `POST` to `/api/submit`.

Create the file `server/api/submit.ts` which is the server API endpoint to handle the form.

Create `server/api` directory:
```shell
mkdir -p server/api
```

Create `server/api/submit.ts` file:
```shell
touch server/api/submit.ts
```

Add the following code to `server/api/submit.ts`:
```ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'POST') {
    console.log('Hello, World!');
  }
});
```

Run your Nuxt development server:
```shell
npm run dev
```

Visit `localhost:3000/form` in your browser and make a form submission 
![Nuxt form app](https://res.cloudinary.com/craigsims808/image/upload/v1751159441/strapi/getting-started-with-nuxt/nuxt-form-app_xf3pz3.png)

you should the text "Hello, World!" in your server console.
![Hello World in console](https://res.cloudinary.com/craigsims808/image/upload/v1751159440/strapi/getting-started-with-nuxt/hello-world-with-console-terminal-2_g4cptc.png)

### Log form submission to console 

Update `server/api/submit.ts`:
```ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'POST') {
    const body = await readBody(event); // Parses form-encoded data or JSON
    console.log('Hello,', body);
  }
});
```

- Run server
- Check console "Hello, [Object: null prototype] { name: 'Mark' }"

### Retrieve name from form submission

Update `server/api/submit.ts`:
```ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'POST') {
    const body = await readBody(event); // Parses form-encoded data or JSON
    console.log('Hello,', body.name);
  }
});
```

- Run server
- Check console "Hello, Mark"

### Redirect to a different page

Update `server/api/submit.ts`:
```ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'POST') {
    const body = await readBody(event); // Parses form-encoded data or JSON
    console.log('Hello,', body.name);

    // Redirect after successful submission
    return sendRedirect(event, '/submission');
  }
});
```

Create `pages/submission.vue`:
```vue
<template>
    <h1>Thank you for your submission!</h1>
</template>
```

- Run server
- Check console "Hello, Mark"
- View `/submission` after workflow
![Static form submission response](https://res.cloudinary.com/craigsims808/image/upload/v1751160878/strapi/getting-started-with-nuxt/static-nuxt-form-submission_ras1nb.png)

### Pass form submission to `/submission` page using query string

Update `server/api/submit.ts`:
```ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'POST') {
    const body = await readBody(event); // Parses form-encoded data or JSON
    console.log('Hello,', body.name);

    // Redirect after successful submission
    return sendRedirect(event, `/submission?name=${encodeURIComponent(body.name)}`);
  }
});
```

Update `pages/submission.vue`:
```vue
<template>
    <h1>Hello, {{ name }}!</h1>
</template>

<script setup>
    const route = useRoute();
    const name = route.query.name
</script>
```

- Run server
- Check console "Hello, Mark"
- View `/submission` after workflow
![Dynamic form submission response using query string](https://res.cloudinary.com/craigsims808/image/upload/v1751161503/strapi/getting-started-with-nuxt/dynamic-form-response-with-query-string_ciicw8.png)

### Hide query string using cookie

Update `server/api/submit.ts`:
```ts
export default defineEventHandler(async (event) => {
  if (getMethod(event) === 'POST') {
    const body = await readBody(event); 
    console.log('Hello,', body.name);

    setCookie(event, 'submittedName', body.name, {
      path: '/',
      maxAge: 10, // expires quickly (10 seconds)
    });

    return sendRedirect(event, `/submission`);
  }
});
```

Update `pages/submission.vue`:
```vue
<template>
    <h1>Hello, {{ name }}!</h1>
</template>

<script setup>
import { useCookie } from '#app';

const name = useCookie('submittedName').value
</script>
```

- Run server
- Check console "Hello, Mark"
- View `/submission` after workflow
![Dynamic form submission response without query string](https://res.cloudinary.com/craigsims808/image/upload/v1751162093/strapi/getting-started-with-nuxt/dynamic-form-without-query_kqcbpi.png)

