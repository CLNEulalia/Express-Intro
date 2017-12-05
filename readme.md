# Express

## Learning Objectives

- Compare and contrast JavaScript in the browser vs on the server
- Use `npm` to manage project dependencies
- Use `module.exports` and `require` to organize code
- Build a server-side application with Express
- Use Handlebars templates to simplify rendering in Express
- Use and configure middleware (e.g. body-parser to handle form submissions)

## Framing
> 5 minutes / 0:05

Let's think about the JavaScript we've written so far:

* Where has the JavaScript code we've written been executed?
* What are some common tasks we've used JavaScript for?
* Which part of our applications (the front end or the back end) have used JavaScript?

This lesson introduces two new tools: Node and Express

* **Node** is a JavaScript runtime used to build Server-side applications
* **Express** is an un-opinionated web development framework, written in Node.

The JavaScript we write today is the same JavaScript we've come to know and love; it's the environment that's different. The JavaScript we wrote previously was for browsers and the client. The JavaScript we're writing today will be run by Node on our laptops simulating a server.

## We Do: Setting up a Node Project
> 15 minutes / 0:20

You have Node installed on your laptop. If you don't believe me, run `node` in your terminal. Doing so will pop you in to a JavaScript REPL in Node.

The way we set up JavaScript projects for Node is a little different from how we set up projects for the browser. Instead of including our JavaScript in a script tag in our HTML, we're going to execute our code in the command line with Node. We're also going to use a tool called NPM for managing dependencies.

### Working with Node
To get started, lets create a new directory in our Sandbox called `intro-to-node/`. Create an `index.js` file inside of it. Open the file in Atom and add `console.log("hello node");` to the first line. To run the file, run `node index.js` in the terminal.

You just executed JavaScript with Node!

This JavaScript is the same as the JavaScript we wrote in the browser, with some minor differences. In the browser, the global object was the `window`, referring to the browser window. What do you think will happen if you change your `index.js` file to `console.log(window);`?

In Node, the global object is `process`. If you try to log `window`, Node assumes you're trying to reference a variable called `window`.

### Working with NPM
**NPM** stands for *Node Package Manager*. It's a tool that does exactly what it says: it manages packages for Node. It manages these packages with a manifest inside of a `package.json` file.

Run `npm init` in your `intro-to-node/` directory from before. You'll be asked a series of questions which will generate a `package.json` file. When it's finished, you'll be ready to build out your project and include third party code!

To install and include a package that someone else wrote in your project, you'll use the `npm` command line interface. The command for installing a package is `npm install <packageName>`, where `packageName` is the name of the package you want to install. By itself, `npm install` will only download the package. You need to also pass in the `--save` or `-S` flag to update your `package.json` file.

Lets install the [reverse-string](https://www.npmjs.com/package/reverse-string) package:

```
npm install --save reverse-string
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

### Using Third-party packages
We've now installed `reverse-string` with NPM - but how do we use it. Node has a `require()` method for including packages and other files.

Switch back to your `index.js` file and add `const reverse = require('reverse-string');` to the very top of the file. Then add `console.log(reverse('hello world'));` below your `require()` statement. If you run the file with Node, you should get `dlrow olleh` logged in your terminal.

We've covered running JavaScript files with Node, installing third-party libraries with NPM and then using those libraries in your own JavaScript. Next, we'll cover all of this again but with Express, a web framework for Node.

## Introducing Express
> 5 minutes / 0:25

Express is a minimalistic web framework. Compared to web frameworks like Django and Ruby on Rails, Express is tiny. But it was intentionally designed that way. Throughout Express' history and development, the core of the web framework has gotten smaller as more and more functionality is spun-off into separate packages.

Express' minimalism comes with some trade-offs. On the one hand, Express feels "close to the wire": there isn't a lot of "magic" like there is with Rails. This means you won't have cruft in your application or things that you don't need; it also means you'll be responsible for building out everything you do need.

Additionally, Express is very unopinionated: it doesn't really care how you structure your app, for instance, and doesn't provide any guidance on how to do so. That makes it extremely flexible and practical for a lot of different types and sizes of applications; it also means that you have to figure out the structure yourself. PayPal uses Express, but built a more opinionated framework (Kraken.js) on top of it to give it's developer more structure.

At it's core, Express is meant to be a very light abstraction over the native Node HTTP modules as a way of giving developers four key features:
1. Routing
2. Subapplications
3. Middleware
4. Conveniences

## We Do: Hello World with Express
> 15 minutes / 0:40

Let's jump right into creating a simple "Hello World" Express application as we explore these four key features.

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
$ npm install --save express
```

> the `--save` flag updates your `package.json` file with your new dependency. We could manually edit the `package.json` file but conventionally we use the CLI tool.

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

When we run the application – `$ node index.js` – we can see `app listening on port 4000` printed to the terminal. The process continues to run, occupying the shell until we hit `ctrl + c`, just like pervious servers we have run.

If we visit `http://localhost:4000` in the browser, we'll see something like this:

```
Cannot GET /
```

Our app is running and we're able to visit it in the browser. But we're missing routes!

### Routing
> 10 minutes / 0:50

The first key feature that Express provides is simple and easy routing.

A *route* is a path and an HTTP verb. Express contains a method for each HTTP verb which in turn accepts a path as the first argument then some number of callback functions. Each callback is given two objects and a function: one object representing the request, one object representing our response and a function representing the next callback.

Let's update `index.js`. Add this above `app.listen()`:

```js
app.get("/", (request, response, next) => {
  response.send("Hello World")
})
```

We'll explore the `next()` function later when we talk about middleware.

We've added a route and a handler for requests to the "/" route. In this case, we're sending the string `"hello world"` as the response. Let's see if this takes effect in the browser:

```
Cannot GET /
```

No change. The running server won't change until we restart it  and refresh the page. Once we do that, we'll see:

```
Hello World
```

Constantly needing to restart the server will get very tedious, very quickly. Instead, we can use the `nodemon` module to run our server. The `nodemon` node module (a portmanteau of "node monitor") performs a similar task to `sinatra/reloader` but goes about it slightly differently. Instead of requiring `nodemon` in our code, we use `nodemon` from the command line. Then, `nodemon` will restart our server for us whenever a file is changed

In the terminal, run:

```bash
$ npm install --global nodemon
```

> When using the `--global` flag (or `-g` for short), we're specifying that `nodemon` will be installed "globally" so we can utilize `nodemon` across all of our node applications (and also that it is not included in our project dependencies).

We start up our application a bit differently now. In the terminal, run:

```bash
$ nodemon index.js
```

### Turn and Talk
> 10 minutes / 1:00

Let's take a second to compare this `GET '/'` handler in express to [how we would write a handler in Sinatra](https://github.com/ga-wdi-exercises/emergency_compliment/blob/solution/server.rb#L14-L19).

* What is different between the route we just defined in Express and the one we defined in Sinatra?
* What is similar?

### Params in Express
> 10 minutes / 1:10

Remember `params` in Sinatra and Rails? Implementing route params in Express is very similar!

Let's update `index.js` to include...

```js
app.get("/:name", (req, res) => {
  res.send(`hello ${req.params.name}`)
})
```

Now if we visit `http://localhost:4000/bob`, we should see:

```
hello bob
```

## Break
> 10 minutes / 1:20

## Subapplications
> 15 minutes / 1:35

Subapplications in Express are very closely related to the idea of Controllers from MVC. Express calls these *routers* and they let us break up our application into discrete sections based on our routes.

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

## You Do: 99 Bottle of Beer
> 15 minutes / 1:50

The exercise can be found [here](https://git.generalassemb.ly/ga-wdi-exercises/99_bottles_express).

## Views
> 20 minutes /  2:10

Let's leverage our [solution to 99 Bottles of
Beer](https://github.com/ga-dc/99_bottles_express/tree/solution) to learn about views.

Handlebars is a JavaScript module for templating. Handlebars templates are very similar to the `.erb` files we used in Sinatra and Rails in that we use code to augment our HTML.

Handlebars is not the only templating engine we can use. [There are many others](https://github.com/expressjs/express/wiki#template-engines).

Install Handlebars as a project dependency:

```bash
$ npm install --save hbs
```

In `index.js`, let's [configure our express app](https://expressjs.com/en/guide/using-template-engines.html) to use Handlebars as its "view engine":

```js
app.set("view engine", "hbs")
```

Let's go ahead and create a directory that will contain our templates in the root directory of
the Express 99 Bottles application. In the terminal:

```bash
$ mkdir views
$ touch views/index.hbs
$ touch views/layout.hbs
```

Let's change up our existing `bottles.js` to utilize a template rather than sending in a string directly. In `bottles.js`:

```js
app.get("/:numberOfBottles?", ( req, res ) => {
  let bottles = req.params.numberOfBottles || 99
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
<!doctype html>
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

## Middleware
> 20 minutes / 2:30

Let's personalize our 99 bottles app.  We'll make a welcome page with a form asking for user's name.

We need a route and a view with a form:

```js
app.get("/", (req, res) => {
  res.render("bottles/welcome")
})
```

```html
<!-- views/bottles/welcome.hbs -->
<h1>Welcome to 99 Bottles</h1>
<form action="/bottles" method="post">
  <label for="player_name">Please enter your name</label>
  <input id="player_name" type="text" name="playerName">
  <input type="submit">
</form>
```

Submit a name:

```
Cannot POST /
```

### How can we fix this?

> In `bottles.js`:

```js
app.post("/", (req, res) => {
  res.send("Hello there!")
})
```

Well it works, but it's not super valuable, we're not even getting our parameters. Let's greet the name submitted in the form:

```js
app.post("/", (req, res) => {
  res.send("Hello " + req.params.player_name)
})
```

If we resubmit our form, we should get back `hello undefined`.That seems weird. To be sure let's `console.log(req.params)`.

It's an empty object!

Our HTML form information is not in `req.params`. That's because Express is not handling information posted from a form. We need to install middleware – code that runs in between receiving the request and sending the response – in order to get form or JSON data in a POST request. Rails and Sinatra already include the middleware to handle this. Express by default does not, so we need to install it manually.

### You Do: `body-parser` Walkthrough
> 10 minutes

The middleware we will install is called **body-parser**. It used to be included to Express by default, but was removed and made in to it's own module to make Express more minimal.

In the terminal:

```bash
$ npm install --save body-parser
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

So we change the final post request in `bottles.js` to:

```js
app.post("/", (req, res) => {
  res.send(`hello ${req.body.playerName}`)
})
```

And finally, we'll integrate it the name into our index template...

```js
app.post("/", (req, res) => {
  res.render("index", {
    player_name: req.body.player_name,
    bottles: 99,
    next: 98
  })
})
```

And to our view:

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
Express is a minimal and flexible web framework for building web applications with Node. Later on, we'll see how we can integrate Express with a database to persist data. Once we do, we'll be able to build applications using Express just like the ones we built using Rails and Sinatra.

Remember that there are four key features of Express: routing, subapplications, middleware and some other conveniences. Can you explain what each does? What are some of the conveniences that Express provides?

## You Do: Emergency Compliment (Homework)

[Emergency Compliment](https://github.com/ga-dc/compliment-express)

> Looking at the [Sinatra solution](https://github.com/ga-dc/emergency_compliment/tree/solution) for this assignment might be helpful.

