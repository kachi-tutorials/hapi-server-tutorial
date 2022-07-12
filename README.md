# Hapi JS Tutorial

**`Hapi Js`** is a powerful and robust open-source node JS framework for developing JSON API. Hapi JS is one of the most preferred frameworks for node Js due to it's well-developed plugin system.

We'll be making a **`Hapi Js`** server from scratch using **Node Js**.

## Installation

In order to follow this tutorial you will need to following:

- You must have [**`Node`**](https://nodejs.org/en/download/) installed - ideally at the LTS _(long term support)_ version.

Run the following command on your terminal to check if you have node installed:

```bash
node --version
```

In this tutorial, I will be using [**Visual studio code**](https://code.visualstudio.com/) as the text editor and [**Postman**](https://www.postman.com/) for the **`HTTP requests`**, but feel free to use whichever tools you want.

## Set up

Let's start by making running the following commands:

```bash
mkdir hapi_tutorial
cd hapi_tutorial
npm init -y
```

### What did we just do?

1. Creates a folder called **`hapi_tutorial`**.
2. Should contain a file titled **`package.json`** file with the default values.

Now we have our **`package.json`** file, let's install **`Hapi Js`** by running the following command in our terminal:

```bash
npm i @hapi/hapi
```

Your **`package.json`** file should now look something like this:

```json
{
  "name": "hapi_tutorial",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@hapi/hapi": "^20.2.2"
  }
}
```

Now we're ready to start creating your project!

## Adding our files

Next we'll be creating 3 files: **`index.js`**, **`server.js`** and **`router.js`** file.

Let's do that by running the following command in your terminal:

```bash
touch index.js router.js server.js
```

## Router.js file

Traditionally, you'll start from the **`index.js`** file but for this tutorial, we'll start with the **`router.js`** file.

Add the following code to your **`router.js`** file:

```javascript
const router = [
  {
    method: "GET",
    path: "/",
    handler: (req, h) => {
      return "Hello World!";
    },
  },
];

module.exports = router;
```

## Server.js file

Let's add the following to our **`server.js`** file:

```javascript
const Hapi = require("@hapi/hapi");
const router = require("./router");

const server = Hapi.server({
  port: 8000,
  host: "localhost",
});

router.forEach((path) => server.route(path));

module.exports = server;
```

Let's break this down what we've done:

1. We've imported Hapi and initiated our server.
2. We've set our **`port`** to **`8000`** and **`host`** to **`"localhost"`**.
3. We've then imported our **`router`** and created a route for each **`path`** .

## Index.js

```javascript
const server = require("./server");

(async () => {
  await server.start();
  console.log("🚀 Server listening %s/ 🚀", server.info.uri);
})();
```

Let's break this down what we've done:

1. We're importing our server from **`server.js`**
2. We're creating a self calling **async** function.
3. First we'll start the server and then if it's successful it will log the server endpoint

So let's run our app to make sure. Let's just run this command in our terminal:

```bash
node index.js
```

If everything has gone according to plan, we should see the following on our terminal:

```bash
🚀 Server listening http://127.0.0.1:8000/ 🚀
```

Now clicking on this link [**`http://127.0.0.1:8000`**](http://127.0.0.1:8000/) should return **`"Hello World!"`**

## Adding a controllers file

Controllers are the way we prevent our router file from getting cluttered.

Lets start by creating our **`controllers`** directory and our first controller:

```
mkdir controllers
touch controllers/events.controllers.js
```

We'll call our first controller **`events.controllers.js`**.

Add the following code into our **`events.controllers.js`** file:

```javascript
const events_db = [];

const getEvents = (request, h) => {
  return events_db;
};

const postEvent = (request, h) => {
  events_db.push(request.payload);

  return "Event Created!";
};

module.exports = {
  getEvents,
  postEvent,
};
```

### What did we just do?

For now we're using an empty array named **`events_db`** to store our data.

- The **`GET`** request will return anything currently stored in the array.
- The **`POST`** request will push data into this array.

## Update our router

Now let's update our **`router.js`** file:

```javascript
const { postEvent, getEvents } = require("./controllers/events.controllers");

const router = [
  {
    method: "GET",
    path: "/",
    handler: (req, h) => {
      return "Hello World!";
    },
  },
  {
    method: "POST",
    path: "/post_event",
    handler: postEvent,
  },
  {
    method: "GET",
    path: "/events_list",
    handler: getEvents,
  },
];

module.exports = router;
```

## Making our requests

Let's try and make a **`POST`** request to [**`http://127.0.0.1:8000/post_event`**](http://127.0.0.1:8000/post_event) with the following data:

```json
{
  "name": "test event",
  "adultsOnly": false,
  "attendees": 100,
  "description": "test description"
}
```

A successful **`POST`** request should return this response:

```
'Event Created!'
```

Finally a **`GET`** request to [**`http://127.0.0.1:8000/events_list`**](http://127.0.0.1:8000/events_list) should return the response:

```json
[
  {
    "name": "test event",
    "adultsOnly": false,
    "attendees": 100,
    "description": "test description"
  }
]
```

If you restart your server, this data should be gone as we are only temporarily storing it in an array.

And that's all she wrote! Thanks for reading!
