# Getting Started with Nuxt and APIs

## Introduction

In this article, you will learn how to create APIs using Nuxt. Nuxt supports Server Side Rendering (SSR) which is part of creating APIs.

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

## Create Nuxt project

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

## Create a simple "Hello, World!" API using Nuxt

In this step we will create an API endpoint that returns the text "Hello" using Nuxt.

- Create server folder
- Create api folder
- Create hello.js file inside api folder
- Add code to server/api/hello.js
```js
export default defineEventHandler(async (event) => {
  return "Hello"
})
```
- Run nuxt server
- Curl localhost:3000/api/hello
- Hello in terminal

## Create a simple "Hello" json API usint Nuxt

- Update server/api/hello.js
```js
export default defineEventHandler(async (event) => {
   return { message: "Hello" }
})
```
- Run nuxt server
- curl localhost:3000/api/hello
- { message: "Hello" } in terminal

## Read from JSON file API Nuxt

- Create hello.json file in project root
```json
{
 "message": "Hello"
}
```
- Update server/api/hello.js
```js
import fs from 'fs/promises'
import path from 'path'

export default defineEventHandler(async (event) => {
  const filePath = path.join(process.cwd(), 'hello.json')
    const fileContent = await fs.readFile(filePath, 'utf-8')
    return JSON.parse(fileContent)
})
```
- Run nuxt server
- curl localhost/api/hello                              
- { message: "Hello" } in terminal

## Read data from external API endpoint

- Create server/api/data.js
```js
import fs from 'fs/promises'
import path from 'path'

export default defineEventHandler(async (event) => {
  const filePath = path.join(process.cwd(), 'hello.json')
  const fileContent = await fs.readFile(filePath, 'utf-8')
  return JSON.parse(fileContent)
})
```

- Update server/api/hello.js
```js
export default defineEventHandler(async (event) => {
    const response = await $fetch('/api/data')
    return response
})
```

- Run nuxt server
- curl localhost/api/hello                              
- { message: "Hello" } in terminal

## Read data from external API endpoint

- Use in memory array/object easier to prototype with
- Payload is similar to Strapi
```js
let payload =
{
    "data": [
        {
            "id": 1,
            "name": "Gary"
        },
        {
            "id": 2,
            "name": "Solomon"
        }
    ]
}
```

OR

```js
let payload =
{
    {
        "id": 1,
        "name": "Gary"
    },
    {
        "id": 2,
        "name": "Solomon"
    }
}
```

OR 

```js
let payload = [Gary, Solomon]
```

OR

```js
let payload = Gary
```
- Create `server/api/external.js`
- Add in-memory data store
```js
let payload = 
{
    "data": [
        {
            "id": 1,
            "name": "Gary"
        },
        {
            "id": 2,
            "name": "Solomon"
        }
    ]
}

export default defineEventHandler(() => {
   return payload
})
```
- Run nuxt server
- curl localhost:3000/api/external                  
- See `payload` in terminal

## Show API Data in `app.vue`

- When payload is a variable
```js
let payload = Gary
```
- `server/api/get.js` stays the same
- Update `server/api/external.js` uses the payload as a variable
```js
let payload = "Gary"

export default defineEventHandler(() => {
    return payload
})
```
- Update `app.vue`
```vue
<script setup>
const { data: name } = await useFetch('/api/external')
</script>

<template>
    <h1>Names from API</h1>
    <p>{{ name }}</p>
</template>
```
- Run nuxt server
- Visit localhost:3000
- Screenshot showing Names from API and `Gary`

## Show API Data in `app.vue` payload is array

- Update `server/api/external.js`
```js
let payload = ["Gary", "Solomon"]

export default defineEventHandler(() => {
    return payload
})
```

- Update `app.vue`
```vue
<script setup>
const { data: names } = await useFetch('/api/external')
</script>

<template>
    <h1>Names from API</h1>
    <ul>
        <li v-for="name in names">
            {{ name }}
        </li>
    </ul>
</template>
```
- Run nuxt server
- Visit localhost:3000
- Screenshot Names from API with array `Gary`, `Solomon`

## API Data Read `app.vue` Variations Array of Objects

- Update `server/api/external.js`
```js
let payload = 
{
    "data": [
        {
            "id": 1,
            "name": "Gary"
        },
        {
            "id": 2,
            "name": "Solomon"
        }
    ]
}

export default defineEventHandler(() => {
    return payload
})
```

- Update `app.vue`
```vue
<script setup>
const { data: persons } = await useFetch('/api/external')
</script>

<template>
    <h1>Names from API</h1>
    <ul>
        <li v-for="person in persons.data">
            {{ person.name }}
        </li>
    </ul>
</template>
```
- Run nuxt server
- Visit localhost:3000
- Screenshot Names from API `Gary`, `Solomon`

## POST using curl to `server/api/external.js` External API

## Post data to API endpoint using curl

- Update server/api/data.js
```js
import fs from 'fs/promises'
import path from 'path'

export default defineEventHandler(async (event) => {
  const method = getMethod(event)
  const filePath = path.join(process.cwd(), 'hello.json')
  
  if (method === 'POST') {
    // Handle POST request to update data
    const body = await readBody(event)
    const { message } = body
      
    // Read existing data or create new
    let existingData = {}
    const existingContent = await fs.readFile(filePath, 'utf-8')
    existingData = JSON.parse(existingContent)
      
    // Update the data
    const updatedData = {
    ...existingData,
    message,
    }
      
    // Write back to file
    await fs.writeFile(filePath, JSON.stringify(updatedData, null, 2))
    return updatedData

  } else {
    // Handle GET request (default behavior)
    const filePath = path.join(process.cwd(), 'hello.json')
    const fileContent = await fs.readFile(filePath, 'utf-8')
    return JSON.parse(fileContent)
} 
})
```

- Run nuxt server
- POST request in terminal
```shell
curl -X POST http://localhost:3000/api/data \
  -H "Content-Type: application/json" \
  -d '{"message": "Hello World"}'
``` 
- Response is
{ message: 'Hello World' }
- hello.json becomes
```json
{
  "message": "Hello World"
}
```

## Post data to API endpoint using server route

- Create server/api/hello-post.js