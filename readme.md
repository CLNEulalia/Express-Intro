# Express

## Learning Objectives

- Compare and contrast JavaScript in the browser vs on the server
- Review using `npm` to manage project dependencies
- Review using `require()` to import packages in our code
- Build a server-side application with Express
- Use Handlebars templates to simplify rendering in Express
- Use and configure middleware (e.g. body-parser to handle form submissions)

## Framing

> 5 minutes / 0:05

Let's think about the JavaScript we've written for project 1:

- Where has the JavaScript code we've written been executed?
- What are some common tasks we've used JavaScript for?
- Which part of our applications (the front end or the back end) have used JavaScript?

What about the exercise this morning (building a node package)?

- Where was our code executed?
- Which part of our application was using JavaScript?

This morning introduced Node:

- **Node** is a JavaScript runtime used to build server-side applications

This lesson will introduce a new tool, called Express:

- **Express** is an un-opinionated web development framework, written in Node.

The JavaScript we write today is the same JavaScript we've come to know and love; it's the environment that's different. The JavaScript we wrote previously was for browsers and the client. The JavaScript we're writing today will be run by Node on our computers, simulating a server.

## We Do: Setting up a Node Project

> 15 minutes / 0:20

Run `node` in your terminal. Doing so will pop you in to a JavaScript REPL (Read-Eval-Print-Loop) in Node. The REPL allows us to run JavaScript from our terminal.

The way we set up JavaScript projects for Node is a little different from how we set up projects for the browser. Instead of including our JavaScript in a script tag in our HTML, we're going to execute our code in the command line with Node. We also use a tool called NPM for managing dependencies.

### Working with Node

Lets get started!

> 1. Create a new directory in our Sandbox called `intro-to-node/`
> 2. Create an `index.js` file inside of it
> 3. Open the file in your text editor and add `console.log('hello node')` to the first line.
> 4. Run `node index.js` in the terminal to run the file

This JavaScript is the same as the JavaScript we wrote in the browser, with some minor differences. In the browser, the global object was the `window`, referring to the browser window. What do you think will happen if you change your `index.js` file to `console.log(window);`?

In Node, the global object is `process`. If you try to log `window`, Node assumes you're trying to reference a variable called `window`.

### Working with NPM

<details>
	<summary>What is NPM?</summary>
	
	**NPM** stands for *Node Package Manager*. It's a tool that does exactly what it says: it manages packages for Node. 
</details>

It manages these packages with a manifest inside of a `package.json` file.

Create a `package.json` file in the `intro-to-node` directory with `npm init `. Then answer or skip (enter) the prompted questions.

```bash
$ npm init
```

- What is a `package.json` file?

Open up and inspect the contents of your `package.json` file.

To install and include a package that someone else wrote in your project, you'll use the `npm` command line interface. The command for installing a package is `npm install <packageName>`, where `packageName` is the name of the package you want to install. 

`npm install` will download the entire package (all dependencies) and update your `package.json` file.

Lets install the [reverse-string](https://www.npmjs.com/package/reverse-string) package:

```bash
$ npm install reverse-string
```

Check your `package.json` file after the command finishes running:

<details>
	<summary>What changed, if anything?</summary>
	
	There should now be a `dependencies` object with `reverse-string` in it.
</details>

<details>
	<summary>Where did the package come from?</summary>
	
	Packages installed and managed with NPM generally come from the [NPM Registry](https://www.npmjs.com), a registry of third-party packages that you can install and use in your projects.
</details>

<details>
	<summary>Where was the package downloaded?</summary>
	
	Packages managed by NPM are installed in a `node_modules/` directory. NPM will create this directory for you if there isn't already one present.
</details>

For full documentation on `package.json`, [click here](https://docs.npmjs.com/files/package.json).

### Using Third-party packages

We've now installed `reverse-string` with NPM - but how do we use it. Node has a `require()` method for including packages and other files.

At the top of your `index.js` file, use `require()` to import the `reverse-string` package and use the `reverse()` method to test that the package is imported.

```js
const reverse = require('reverse-string')
console.log(reverse('hello world'))
```

Run the file with Node:

```bash
$ node index.js
```

What was logged? How did we do it?

We've covered running JavaScript files with Node, installing third-party libraries with NPM and then using those libraries in your own JavaScript. Next, we'll cover all of this again but with Express, a web framework for Node.

## Introducing Express

> 5 minutes / 0:25

Express is a minimalistic web framework. Compared to web frameworks like Django and Ruby on Rails, Express is tiny. But it was intentionally designed that way. Throughout Express' history and development, the core of the web framework has gotten smaller as more and more functionality is spun-off into separate packages.

Express feels "close to the wire" - i.e. you will be building out the functionality that you want. This minimalism comes with some trade-offs.  On the one hand, you won't have unnecessarily complicated code in your application or things that you don't need. It also means you'll be responsible for building out everything you do need.

Additionally, Express is very unopinionated: it doesn't really care how you structure your app, for instance, and doesn't provide any guidance on how to do so. That makes it extremely flexible and practical for a lot of different types and sizes of applications; it also means that you have to figure out the structure yourself. PayPal uses Express, but built a more opinionated framework (Kraken.js) on top of it to give its developers more structure.

At it's core, Express is meant to be a very light abstraction over the native Node HTTP modules as a way of giving developers a few convenient features:

- Routing
- Middleware
- Subapplications (bonus)

## We Do: Hello World with Express

> 15 minutes / 0:40

Let's jump right into creating a simple "Hello World" Express application as we explore these four key features.

Create and enter a new directory `hello-express` in your sandbox. Then, create a `package.json` file using npm.

```bash
$ mkdir hello-express
$ cd hello-express
$ npm init -y
```

<details>
  <summary>What does `npm init -y` do?</summary>

`npm init` creates a blank `package.json` file by prompting you for some user input. Using the `-y` flag will accept the details for all prompts.

</details>

The next thing we need to do is install the Express module:

```bash
$ npm install express
```

We can see in our `package.json` that the default main file for a node app is `index.js`. We could change this, but we'll use the default for now.

Let's make a new `index.js` file and give it the following contents...

```js
const express = require("express")
const app = express()

app.listen(4000, () => {
  console.log("app listening on port 4000")
})
```

What's going on here?

- we're requiring the Express module, which is a function that returns an instance of a web app
- we're invoking the module, instantiating a constant `app` which holds all the methods and state we use to write and run our web app
- we're starting our server (and app) by listening on port 4000 for incoming requests

When we run the application from the terminal `node index.js`, we can see `app listening on port 4000` printed to the terminal. The process continues to run, occupying the shell until we hit `ctrl + c`, just like pervious servers we have run.

If we visit `http://localhost:4000` in the browser, we'll see something like this:

```
Cannot GET /
```

Our app is running and we're able to visit it in the browser. But we're missing routes!

### Routing

> 10 minutes / 0:50

The first key feature that Express provides is simple and easy routing.

A *route* is a path and an HTTP verb. Express contains a method for each HTTP verb which in turn accepts a path as the first argument then some number of callback functions. We'll start with just one callback function. 

In the example below, the callback function is given two arguments: the first represents the HTTP request object, and the second represents the HTTP response object.

Let's update `index.js`. Add this above `app.listen()`:

```js
app.get("/", (request, response) => {
  response.send("Hello World")
})
```

We've added a route and a handler for requests to the "/" route. In this case, we're sending the string `"hello world"` as the response. Let's see if this takes effect in the browser:

```
Cannot GET /
```

No change. The running server won't change until we restart it  and refresh the page. Once we do that, we'll see:

```
Hello World
```

Constantly needing to restart the server will get very tedious, very quickly. Instead, we can use the `nodemon` module to run our server. Instead of requiring `nodemon` in our code, we use `nodemon` from the command line. Then, `nodemon` will restart our server for us whenever a file is changed.

To check if you have nodemon, run: `nodemon -v`.

**If you do not already have nodemon installed**

> In the terminal, run: `npm install --global nodemon`

> When using the `--global` flag (or `-g` for short), we're specifying that `nodemon` will be installed "globally" so we can utilize `nodemon` across all of our node applications (and also that it is not included in our project dependencies).

We start up our application a bit differently now. 

> In the terminal, run: `nodemon index.js`

### Turn and Talk

> 10 minutes / 1:00

Take a few minutes to walk through our code with a neighbor. Make sure you understand the purpose of each line of code. Reach out to other neighbors for clarification.

Compare your express server with server we built from scratch.

- What is similar?
- What is different?

### Params in Express

> 10 minutes / 1:10

Route parameters give us flexibility when writing routes in express.

Let's update `index.js` to include...

```js
app.get("/:name", (req, res) => {
  res.send(`hello ${req.params.name}`)
})
```
> Note: the `request` and `response` objects are often shortened to just `req` and `res`.

- Our route has changed! What is different?

Route parameters are named sections of our path, they are placeholders (similar to variables) that capture values at their location in a URL. These values are held in the `req.params` object and can be used to deliver custom responses to an HTTP request.

Now if we visit `http://localhost:4000/bob`, we should see:

```
hello bob
```

## Break

> 10 minutes / 1:20

## You Do: 99 Bottle of Beer

> 15 minutes / 1:35

The exercise can be found [here](https://git.generalassemb.ly/ga-wdi-exercises/99_bottles_express).

**Hint**: you can send HTML elements in your GET response

## Views

> 20 minutes /  1:55

Let's leverage our [solution to 99 Bottles of
Beer](https://github.com/ga-dc/99_bottles_express/tree/solution) to learn about views.

Handlebars is a JavaScript module for templating. Handlebars, a templating language, allows us to write a simple front-end within the same application as our server. We will create a template that can change depending on the responses sent from our server. Fortunately, handlebars looks a lot like HTML!

Handlebars is a light-weight tool, and it is simple in its functionality. It's useful for building small applications very quickly, however it is limited by its simplicity. In the coming weeks we will transition to React.js, a more powerful front-end framework, to build more complex and dynamic applications.

[Here are some other templating languages](https://github.com/expressjs/express/wiki#template-engines).

Install Handlebars as a project dependency: 

```bash
$ npm install hbs
```

In `index.js`, let's [configure our express app](https://expressjs.com/en/guide/using-template-engines.html) to use Handlebars as its "view engine":

```js
app.set("view engine", "hbs")
```

Let's go ahead and create a directory that will contain our templates in the root directory of the Express 99 Bottles application. In the terminal:

```bash
$ mkdir views
$ touch views/index.hbs
$ touch views/layout.hbs
```

Let's change up our existing `index.js` to utilize a template rather than sending in a string directly. In `index.js`:

```js
app.get("/:numberOfBottles", (req, res) => {
  let bottles = req.params.numberOfBottles
  let next = bottles - 1
  res.render("index", {bottles, next})
})
```

Instead of directly building a string as the response to that `GET` request, we want to render a view using Handlebars.
The `.render` method takes two arguments...

  1. The name of the view we want to render
  2. An object with values that will be made available in the view

The only problem is our view is empty! Let's go ahead and change that now. In `views/layouts.hbs`:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Express Intro</title>
  </head>
  <body>
   {{{body}}}
  </body>
</html>
```

Notice the `{{{body}}}` syntax. This is because Handlebars by default escapes HTML and you need the additional set of brackets to indicate that you want to render the tags in the body as HTML.

This allows us to utilize files in that folder in the layout.

Finally we should update our index view to reflect the same strings we had before. In `views/index.hbs`:

```html
{{bottles}} bottles of beer on the wall.
{{#if next}}
  <a href='/{{next}}'>Take One Down, Pass it Around</a>
{{else}}
  <a href='/'>Start Over</a>
{{/if}}
```

> This syntax for the conditional statement is a [built-in helper from Handlebars](http://handlebarsjs.com/block_helpers.html).

Start your server back up using `nodemon index.js`, and refresh your page to see it render.

## Introducing Middleware

> 20 minutes / 2:15

Let's personalize our 99 bottles app.  We'll make a welcome page with a form asking for user's name.

We need a route and a view with a form. In `index.js`:

```js
app.get("/", (req, res) => {
  res.render("welcome")
})
```

Now, we'll create a welcome file at the command line:
`touch views/welcome.hbs`

```html
<!-- views/welcome.hbs -->
<h1>Welcome to 99 Bottles</h1>
<form action="/" method="post">
  <label for="player_name">Please enter your name</label>
  <input id="player_name" type="text" name="player_name">
  <input type="submit">
</form>
```

Refresh your browser, and submit a name in the form:

```
Cannot POST /
```

### How can we fix this?

> In `index.js`:

```js
app.post("/", (req, res) => {
  res.send("Hello there!")
})
```

Well this works, but it's not super valuable, and we are not capturing the user input. 

How can we greet the name submitted in the form?

That's where middleware comes in...

By default, Express does not handle information posted from a form. in order to get form or JSON data in a POST request, we need to install middleware – code that runs in between receiving the request and sending the response.

### You Do: `body-parser` Walkthrough

> 10 minutes / 2:25

The middleware we will install is called **body-parser**. It used to be included to Express by default, but was removed and made into its own module to make Express more minimal.

In the terminal:

```bash
$ npm install body-parser
```

In `index.js`:

```js
// configure app to use body parser
// below your other require() statements
const bodyParser = require("body-parser")

// below require and before any routes
// tell Express to use bodyParser
app.use(bodyParser.json()) //handles json post requests
app.use(bodyParser.urlencoded({ extended: true })) // handles form submissions
```

> Only the `urlencoded` body-parser middleware is necessary to get this form working.

The JSON bodyparser is necessary if we want to handle AJAX requests with JSON bodies.

Another thing to note is that, in Express, `req.params` holds just path params. Anything handled by the bodyParser (JSON or form bodies) will be held in `req.body`.

So we change the final post request in `index.js` to:

```js
app.post("/", (req, res) => {
  res.send(`hello ${req.body.player_name}`)
})
```

Once we've confirmed that is working, we'll integrate the name into our index template...

```js
app.post("/", (req, res) => {
  res.render("index", {
    player_name: req.body.player_name,
    bottles: 99,
    next: 98
  })
})
```

And to our view in `index.hbs`:

```html
{{#if player_name}}
  Hey {{player_name}}, there are {{bottles}} left on the wall.
{{/if}}
```

Be prepared to answer the following questions after completing this walkthrough:

- What is the purpose of `body-parser`?
- What is the difference between `bodyParser.urlencoded` and `bodyParser.json`?
- How do we go about accessing values sent through a form in Express?

## Closing

> 5 minutes / 2:30

Express is a minimal and flexible web framework for building web applications with Node. Later on, we'll see how we can integrate Express with a database to persist data.

Today we have reviewed two key features of Express: routing and middleware. Express has other convenient features that make server-side Javascript more simple. Explore Express.js docs [here](http://expressjs.com/) Express also makes it possible to create subapplications which can be handy as we scale to larger projects. You can learn more in the bonus section below.



## You Do: Emergency Compliment (Homework)

[Emergency Compliment](https://github.com/ga-dc/compliment-express)

## Bonus: Subapplications

As our applications get larger, we will need more and more routes. Storing all of these routes in the same .js file can become confusing and difficult to manage. Subapplications in express provide a modular approach to building large applications. Express calls these *routers* and they let us break up our application into discrete sections based on our routes.

Inside our Express app, lets create a `controllers/` directory and a `bottles.js` file inside of it.

In Node, to separate code across multiple files, we'll use `require()` and `module.exports`. If we want to export something from one file, we'll add it to the `module.exports` object or use it to overwrite the `module.exports` object.

Lets build out our `bottles/` subapplication inside of `bottles.js`:

```
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.send('bottles');
});

module.exports = router;
```

By assigning our `router` to `module.exports`, we're exporting the `router` object. We can import it in another file with `require()`. Back in our `index.js`:

```
const bottlesController = require('./controllers/bottles.js');

app.use('/bottles', bottlesController);
```

Now any route that we add to our `router` inside of `controllers/bottles.js` will be available to us under the URL `/bottles`!