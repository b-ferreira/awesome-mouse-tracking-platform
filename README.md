# app

I've considered it as an stand-alone application in terms of development process. Using Webpack and ES6 resources we can build the source code and use the generated dist content together with a simple HTML page.
In order to test it as an stand-alone application, we can use the scripts commands from its package.json file, which is decribed further in this document.

### The main idea

The application's general purpose is track the mouse movement and the mouse click while the user keeps the mouse over the application.
To achieve this, I've created a JavaScript module called **mouse-tracker** that exposes two methods: **startTracking** and **stopTracking**.
Everytime the user puts the mouse over the banner, we call **startTracking**, and if the user puts the mouse out of the banner, we call **stopTracking**

###### startTracking(action)

When this method is called, we create a `setInterval` statement that attempts to register a new mouse coordinate into an array within an interval of **500ms**.
Everytime a new coordinate is pushed to the array, we check if its size is equal a configuration constant that determines the maximum of elements the array can keep. When the number of elements within the array reaches the configuration value, we try to persist all coordinates at the backend. Whenever it fails or returns succesfully, we clear the array and start from the beggining.
I also attached an event handler on `document.body` in order to listen for *mouse movements (onmousemove)*, everytime this handler is called we refresh the value of last coordinate captured.
A click handler is also attached on banner's button and on its body. If the user clicks on both of them, we save the current mouse position at the backend and clear the array of coordinates. After that, a new page is oppened with the `action` value as URL.

###### stopTracking

This method is just used clear the `setInterval` statement and persist all coordinates left within the array of coordinates.

### Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:3000
npm run dev

# build for production with minification
npm run build
```

### Dependencies

This module dependes on **data-ws (backend application 1)** and **logger-ws (backend application 2)**. Without both of them running, the application will not work properly.

# data-ws (Backend application 1)

This is a simple Web Service created on top of [**Restify**](http://restify.com/). Its main purpose is provide the initial configuration for the **app** web application.
It runs at `http://localhost:1111` and exposes just one resource at `/data` endpoint which returns an static JSON with the following content:

```json
{
  "action": "https://bannerwise.io/",
  "imageUrl": "https://via.placeholder.com/350x150",
  "buttonText": "Click Me"
}
```

### Build Setup

``` bash
# install dependencies
npm install

# run the Web Service
node src/index.js
```

# logger-ws (Backend application 2)

This Web Service provides resources for persistence of mouse tracking data. It runs at `http://localhost:2222` and exposes a resource through two endpoints:

* `GET /register` used to list all mouse tracking data from a **MongoDB** database. (If the dabase is available)
* `POST /register` used to persist a collection of mouse tracking data into a **MongoDB** database (If the dabase is available) and log the coordinates sent into process stdout.

The mouse tracking data strcuture is represented by the following **MongoDB** document:

```json
{
  "id": "1239o1283019745019823",
  "type": "mouse-move | click",
  "x": 123,
  "y": 123,
  "dateTime": 12312451231
}
```

### Build Setup

``` bash
# install dependencies
npm install

# run the Web Service
node src/index.js
```

# sample page

There's a sample folder where you can check the **app** running accordingly the task description, I mean, within an `<iframe>`.
Before run the sample page, you must start the Web Services, **data-ws** and **logger-ws**. After that you must serve the sample folder through some `http-server`:

``` bash
# install nodejs based http-server
npm i http-server -g

# navigate to the sample folder and run the http-server...
http-server -p 8081

# after that, open your browser and navigate to http://localhost:8081
```

# Bonus

### Frontend Application

I created a Frontend project that uses VueJS to render a table with all mouse tracking data that we have at the database.
The project was created with [`vue-cli`](https://github.com/vuejs/vue-cli) (the webpack-simple tempalte) and uses Webpack as build tool.
The main idea was receive real-time data from a websocket, but unfortunately I couldn't setup this approach within the time I had, so I'll keep it as a `TODO`.
It depends on the **logger-ws** in order to access the tracking data, so remember that you need run it before start the web application.

### Build Setup

Since this one also uses Webpack, the build setup is similar to the **app** process:

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification, which creates a dist folder at the project's root folder.
npm run build

# http-server at dist folder also works.
http-server 8082
```

### Backend Application

I used the **logger-ws** to store all data sent from **app** on a **MongoDB** database, which is hosted at [mlab](https://mlab.com/).