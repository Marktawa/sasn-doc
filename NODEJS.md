# Getting Started with Node.js

This article is about getting you going with Node.js. Node.js is a JavaScript runtime mostly used for building web apps.

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

## Create Hello World Node.js app

### Installation and Setup

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
    "start": "node server.js"
  },
}
```

### Render plain text "Hello, World!"

Create a `server.js` file your project directory.
```js
const { createServer } = require('node:http');

const hostname = '127.0.0.1';
const port = 3000;

const server = createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello, World!');
});

server.listen(port, hostname, () => {
    console.log(`Server running at http://${hostname}:${port}/`);
})
```

Run your Node.js server:
```shell
npm run dev
```

Visit `localhost:3000` in your browser and you will see the plain text, "Hello, World!"
![Plain text Hello, World!]()

