# AngularJS - Routing


## Lesson Outline

We will be creating a basic page about a Vet. This will have links which load various template files via the Angular Router. Final code is available in the `instructor_examples/routing` directory, but I'll be building it temporarily in `instructor_examples/live_coding`.

1. Running Angular Server with http-server
1. Routing Overview
1. Mini exercise
1. What we are building
1. Push State Overview
1. Write initial HTML
1. Write initial JavaScript and app config
1. Enable HTML5 Push state
1. Create Contact Template
1. Create Contact Controller
1. Create Contact Route
1. Create About Template and Route
1. Pets page; Using parameter from URL,  Route / Controller / Template
1. Pricing page; Pass data from Route to Template
1. Joke page; using template script tag
1. All page; nesting partials/templates
1. Default / Fallback Page
1. Google Link / External page
1. Final Code


## Running Angular Server with http-server

**Note:** Sometimes the page gets cached and updates are not seen. Try a hard refresh by holding down the shift key when you refresh

Sometimes hard refresh isn't enough, try `clear browsing history` 

We're going to create a basic AngularJS application, without an Express server for this exercise. We want to be able to browser to [http://localhost:8080](http://localhost:8080). We are *not* using `nodemon` for this exercise, nor do we need `mongod`.

- Install `npm-server` by running `npm install -g http-server` from the terminal.
- Create a directory called `routing`. `mkdir routing` and `cd routing`
- Create empty `app.js` and `index.html` files inside of it. `touch app.js index.html`  
- Run the server with `http-server -o`
- View the (currently blank) files by browsing to [http://localhost:8080](http://localhost:8080)

## Routing Overview

The Angular Router enables navigation from one view and controllers to the next as users perform application tasks such as clicking on links.

This is similar the routing in Express that we saw prior, and the routing that we will see in Rails in Unit 4.

Another way to think about it is when you have a specific URL, you want to have a specific section of code run.

For example, you might have `/products` to show an index of all your products, `/products/:id` to show a single product, `/login` that displays the login screen, ` /register` that displays the registration screen.

Routing will also pass the application *parameters* from the URL, such as a product id, or a search query string like `/search?query=apple`. The application can then use these parameters as a form of user input.

### Mini Exercise

Write a short list of three possible routes that an application might have, and one parameter that you might use for input. Respond in a thread here on Slack.

## What We Are Building

We're going to build the beginnings an AngularJS application, focusing almost exclusively on the routes. The view templates, controllers, HTML and CSS will be bare-bones to let us focus on this.

My cat Jade is feeling a little sick, so we're going to think about building a basic page for a Veterinarian.

![Example Site Image](https://i.imgur.com/RMadUTk.png "Example Site")

Pages we need:

- Home (/)
- Contact (`/contact`)
- About (`/about`)
- Pricing (`/pricing`)
- Cats (`/pets/cats`)  - Both of these will use just one route/controller/template
- Dogs (`/pets/dogs`)  - Both of these will use just one route/controller/template
- Joke of the Day (`/joke`) - This will use an inline template
- All (`/all`) - This will combine multiple templates
- Bad URL (`/badurl`) - This will show a default / fallback page
- Link to Google - external link

These are a bit arbitrary and artificial of goals, but will help give us some more context to talk about specific links.

## Push State Overview

You can update the browser's url without refreshing the page using JS

- this is called Push State.  It's part of HTML5
- up until recently, the only way to update the url is make a new requests
  - click a link
  - type into browser's url bar and hit enter
- now you can do that with javascript

What can Angular do with this?

- angular will listen for changes in the url
- next, angular will render a template into a specific part of the page that we define
- it will then attach a controller to this template and alias it `as` a variable which can be referenced in the HTML

### Write Initial HTML

We're going to setup most of the HTML we need upfront. This is a bit much to type, but this is the biggest single section of code we'll see at once here.

- We need to include Angular and [ngRoute](https://docs.angularjs.org/api/ngRoute), which is a separate file
- This approach is similar to express, where functionality that is not crucial is moved to separate packages (ejs, method-override, etc...)
- Add a link the `/contact` and other pages outlined above. You do this as you'd create any other relative link. In a moment, we'll see how to handle this with our JavaScript.
- Add a reference to our JavaScript file in the html, and add `<body ng-app="VetApp">`
- Next, tell angular where to render the templates with the `ng-view` directive
- Route relative to a base url (/ in this case) with `<base href="/">`


file: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title></title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js" charset="utf-8"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.min.js" charset="utf-8"></script>
  <script src="app.js" charset="utf-8"></script>
  <base href="/" target="_blank">
</head>
<body ng-app="MyApp">
  <h1>Vets and Pets!</h1>
  <nav>
    <ul>
      <li><a href="/">Home (/)</a></li>
      <li><a href="/contact">Contact</a></li>
      <li><a href="/about">About</a></li>
      <li><a href="/pricing">Pricing</a></li>
      <li><a href="/joke">Joke of the Day</a></li>
    </ul>

    <h3>Pets</h3>
    <ul>
      <li><a href="/pets/cats">Cats</a></li>
      <li><a href="/pets/dogs">Dogs</a></li>
    </ul>
  </nav>

  <!-- This is the main view that will be filled in with view templates -->
  <main ng-view></main>
</body>
</html>
```

## Write initial JS

We need one dependency for our module

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);
```

We need to create a `config` for our module to make this work.  A module config runs only once at load time.

file: js/app.js
```javascript
const app = angular.module('MyApp', ['ngRoute']);

//.config just runs once on load.  Looks like a controller, but without a name
app.config(function() { });
```

Let's add two important services. This is via Dependency Injection, and will perhaps seem a little odd at first.

- `$locationProvider` handles push state
  - push state
  - allows you to update the URL in your browser without reloading the page
  - also updates your history when URL changes via push state
- `$routeProvider`
  - creates event listeners which render a template into ng-view when the browser URL matches its URL

file: `app.js`
```javascript
//include $routeProvider and $locationProvider
const app = angular.module('MyApp', ['ngRoute']);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });
}]);
```

## Create Contact Templates

Create a partial file that has some text inside of it, and uses the `ctrl.phone`, which we will populate and link together with the Controller and Route in the next steps

file: contact.html
```html
<h2>Contact Us:</h2>
{{ctrl.phone}}
```

## Create Contact Controller

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });
}]);
```

## Create Contact Route

Now let's add a basic route within the config function. We'll use the `ContactController` we added above to tie to all together. After this, the link should show a new view when clicked on!

```javascript
$routeProvider.when('/contact', {
  templateUrl: 'contact.html', // render http://localhost:3000/contact.html
  controller: 'ContactController', // attach controller ContactController
  controllerAs: 'ctrl' // alias for ContactController (like ng-controller="ContactController as ctrl")
});
```

*Question for Slack*: Why would a controller alias be useful? Respond in a threaded message.

Then we create a basic empty controller by adding `app.controller('ContactController', function() { });`. Then assign the value of `this.phone` so we can use it in the template.

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });
}]);
```

## You Turn:

Make a template, route and controller for a `/about` page. Don't worry about passing data from the controller to the template.

## Using URL Params for Individual Pets Pages

We can access url params with a pattern that is similar to express:

```javascript
$routeProvider.when('/pets/:id', {  // when http://localhost:3000/pets/:id - :id is a param just like in express
  templateUrl: 'pets.html',
  controller: 'PetController',
  controllerAs: 'ctrl'
});
```
Inside the controller, we can access those url params using `$routeParams` that we pass to the function:

```javascript
app.controller('PetController', ['$routeParams', function ($routeParams) {
  this.id = $routeParams.id; //access :id from url
}]);
```

These are used to pass `cats` and `dogs` from the links we created earlier in the `ul` elements, but they could be used to pass numbers or other data from the URL into the routes and controller:

```html
<h3>Pets</h3>
<ul>
  <li><a href="/pets/cats">Cats</a></li>
  <li><a href="/pets/dogs">Dogs</a></li>
</ul>
```

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });
}]);
```

## Create Pricing Page: Pass data from Route to Controller to Template

You can pass data from the route into the controller, and then use it in a template.

Here we pass the price of *$1 million dollars* from the route to the controller. Note the function definition uses `$route` instead of `$routeParams`!

Create a `PricingController`, route and template:

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.controller('PricingController', ['$route', function ($route) {
  this.price = $route.current.price;
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/pricing', {
    templateUrl: 'pricing.html',
    controller: 'PricingController',
    controllerAs: 'ctrl',
    price: '$1 trillion dollars'
  });
}]);
```

file: `pricing.html`
```html
<h2>Pricing</h2>
{{ctrl.price}}
```


file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.controller('PricingController', ['$route', function ($route) {
  this.price = $route.current.price;
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/pricing', {
    templateUrl: 'pricing.html',
    controller: 'PricingController',
    controllerAs: 'ctrl',
    price: '$1 trillion dollars'
  });
}]);
```


## Joke page; using template script tag

You can have a template inside a script tag instead of an external file.

- the advantage to this is that it doesn't need to make an extra http request to get the template
- the downside is that your html gets more bloated

Here we'll create a `joke.html` template, but instead of making a new file, we'll put the contents inside a `script` tag in `index.html`.  Also, create a link to `/joke` in the navigation list.

```html
<script type="text/ng-template" id='joke.html'>
  <h2>Pet Joke of the Day<h2/>
  <p>{{ctrl.joke}}</p>
</script>
```

file: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title></title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js" charset="utf-8"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.min.js" charset="utf-8"></script>
  <script src="app.js" charset="utf-8"></script>
  <base href="/" target="_blank">
</head>
<body ng-app="MyApp">
  <h1>Vets and Pets!</h1>
  <nav>
    <ul>
      <li><a href="/">Home (/)</a></li>
      <li><a href="/contact">Contact</a></li>
      <li><a href="/about">About</a></li>
      <li><a href="/pricing">Pricing</a></li>
      <li><a href="/joke">Joke of the Day</a></li>
    </ul>

    <h3>Pets</h3>
    <ul>
      <li><a href="/pets/cats">Cats</a></li>
      <li><a href="/pets/dogs">Dogs</a></li>
    </ul>
  </nav>

  <!-- This is the main view that will be filled in with view templates -->
  <main ng-view></main>

  <script type="text/ng-template" id='joke.html'>
    <h2>Pet Joke of the Day</h2>
    <p>{{ctrl.joke}}</p>
  </script>
</body>
</html>
```

Then, create a `JokeController` and `/joke` route. Also, create an array of jokes in the `JokeController`, and pass the first one to `this.joke` so that the joke is displayed in the template.

If you have a moment of extra time, try to figure out how to make a random joke appear each time on the page.


file `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.controller('JokeController', ['$route', function () {
  this.joke = "Why don't cats like online shopping? They prefere a cat-alogue"
}]);

app.controller('PricingController', ['$route', function ($route) {
  this.price = $route.current.price;
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/pricing', {
    templateUrl: 'pricing.html',
    controller: 'PricingController',
    controllerAs: 'ctrl',
    price: '$1 trillion dollars'
  });

  $routeProvider.when('/joke', {
    templateUrl: 'joke.html',
    controller: 'JokeController',
    controllerAs: 'ctrl'
  });
}]);
```

## All Page - Nesting Partials/Templates

Combining multiple partials is possible by having multiple `ng-include` elements in an html file. This is a great way to reuse code. Our example here is a bit artificial, but will show you how to reuse partials we've already created above all together.

Create an `all.html` file, and make `ng-include` elements for `contact.html`, `pricing.html` and `joke.html`.

file: `all.html`
```html
<ng-include src="contact.html"></ng-include>
<ng-include src="pricing.html"></ng-include>
<ng-include src="joke.html"></ng-include>
```

Then create a link to `/all` in the `index.html`

file: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title></title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js" charset="utf-8"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.min.js" charset="utf-8"></script>
  <script src="app.js" charset="utf-8"></script>
  <base href="/" target="_blank">
</head>
<body ng-app="MyApp">
  <h1>Vets and Pets!</h1>
  <nav>
    <ul>
      <li><a href="/">Home (/)</a></li>
      <li><a href="/contact">Contact</a></li>
      <li><a href="/about">About</a></li>
      <li><a href="/pricing">Pricing</a></li>
      <li><a href="/joke">Joke of the Day</a></li>
      <li><a href="/all">Contact, Pricing and Joke!</a></li>
    </ul>

    <h3>Pets</h3>
    <ul>
      <li><a href="/pets/cats">Cats</a></li>
      <li><a href="/pets/dogs">Dogs</a></li>
    </ul>
  </nav>

  <!-- This is the main view that will be filled in with view templates -->
  <main ng-view></main>

  <script type="text/ng-template" id='joke.html'>
    <h2>Pet Joke of the Day</h2>
    <p>{{ctrl.joke}}</p>
  </script>

</body>
</html>
```

Create an `AllController` that will give us the data needed for those partials. There are cleaner ways of not-repeating ourselves for this data, but for now let's just repeat the code to keep things simple.

Then, create a route to `/all` that will use the `AllController` and the `all.html` partial.

```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.controller('JokeController', ['$route', function () {
  const jokes = ["Why don't cats like dogs?",
    "What is the difference between a duck?"];

  //you can access variables created in the when action like this
  this.joke = jokes[0];
}]);

app.controller('PricingController', ['$route', function ($route) {
  this.price = $route.current.price;
}]);

app.controller('AllController', ['$route', function () {
  const jokes = ["Why don't cats like dogs?",
    "What is the difference between a duck?"];

  this.joke = jokes[1];
  this.phone = '555-1212';
  this.price = '$1 billion dollars';
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/pricing', {
    templateUrl: 'pricing.html',
    controller: 'PricingController',
    controllerAs: 'ctrl',
    price: '$1 trillion dollars'
  });

  $routeProvider.when('/joke', {
    templateUrl: 'joke.html',
    controller: 'JokeController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/all', {
    templateUrl: 'all.html',
    controller: 'AllController',
    controllerAs: 'ctrl'
  });
}]);
```

## Default / Fallback routes

What if we have a link that doesn't match the routes defined? We can set a catch-all default route in that case using `otherwise`. You can either have a specific controller and template you'd like to use, or you can redirect it with `redirectTo` in the `app.js` file.

```javascript
$routeProvider.otherwise({
  redirectTo: '/'
});
```

In `index.html` then you can put a link do a page that otherwise doesn't exist:

```html
<a href="/badurl">/badurl</a>
```

Afterward, `app.js` and `index.html` should look like these:

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.controller('JokeController', ['$route', function () {
  const jokes = ["Why don't cats like dogs?",
    "What is the difference between a duck?"];

  //you can access variables created in the when action like this
  this.joke = jokes[0];
}]);

app.controller('PricingController', ['$route', function ($route) {
  this.price = $route.current.price;
}]);

app.controller('AllController', ['$route', function () {
  const jokes = ["Why don't cats like dogs?",
    "What is the difference between a duck?"];

  this.joke = jokes[1];
  this.phone = '555-1212';
  this.price = '$1 billion dollars';
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/pricing', {
    templateUrl: 'pricing.html',
    controller: 'PricingController',
    controllerAs: 'ctrl',
    price: '$1 trillion dollars'
  });

  $routeProvider.when('/joke', {
    templateUrl: 'joke.html',
    controller: 'JokeController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/all', {
    templateUrl: 'all.html',
    controller: 'AllController',
    controllerAs: 'ctrl'
  });

  $routeProvider.otherwise({
    redirectTo: '/'
  });
}]);
```

file: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title></title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js" charset="utf-8"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.min.js" charset="utf-8"></script>
  <script src="app.js" charset="utf-8"></script>
  <base href="/" target="_blank">
</head>
<body ng-app="MyApp">
  <h1>Vets and Pets!</h1>
  <nav>
    <ul>
      <li><a href="/">Home (/)</a></li>
      <li><a href="/contact">Contact</a></li>
      <li><a href="/about">About</a></li>
      <li><a href="/pricing">Pricing</a></li>
      <li><a href="/joke">Joke of the Day</a></li>
      <li><a href="/all">Contact, Pricing and Joke!</a></li>
      <li><a href="/badurl">/badurl</a>, redirects to default home page (/)</li>
    </ul>

    <h3>Pets</h3>
    <ul>
      <li><a href="/pets/cats">Cats</a></li>
      <li><a href="/pets/dogs">Dogs</a></li>
    </ul>
  </nav>

  <!-- This is the main view that will be filled in with view templates -->
  <main ng-view></main>

  <script type="text/ng-template" id='joke.html'>
    <h2>Pet Joke of the Day</h2>
    <p>{{ctrl.joke}}</p>
  </script>

</body>
</html>
```

## Google Link / External Pages

If we include a link to another site using `http`, instead of `https` it skips the AngularJS Routes and simply opens a new tab/window.

We do this by just adding a standard link in the `index.html`
```html
<li><a href="http://www.google.com">google</a></li>
```

## Final code

Here are our main two files, `index.html` and `app.js`. The partial files can be found in `instructor_examples/routing`

file: `index.html`
```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8">
  <title></title>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js" charset="utf-8"></script>
  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular-route.min.js" charset="utf-8"></script>
  <script src="app.js" charset="utf-8"></script>
  <base href="/" target="_blank">
</head>
<body ng-app="MyApp">
  <h1>Vets and Pets!</h1>
  <nav>
    <ul>
      <li><a href="/">Home (/)</a></li>
      <li><a href="/contact">Contact</a></li>
      <li><a href="/about">About</a></li>
      <li><a href="/pricing">Pricing</a></li>
      <li><a href="/joke">Joke of the Day</a></li>
      <li><a href="/all">Contact, Pricing and Joke!</a></li>
      <li><a href="/badurl">/badurl</a>, redirects to default home page (/)</li>

      <!-- only this link skips the router because it starts with http -->
      <li><a href="http://www.google.com">google</a> loads external site</li>
    </ul>

    <h3>Pets</h3>
    <ul>
      <li><a href="/pets/cats">Cats</a></li>
      <li><a href="/pets/dogs">Dogs</a></li>
    </ul>
  </nav>

  <!-- This is the main view that will be filled in with view templates -->
  <main ng-view></main>

  <script type="text/ng-template" id='joke.html'>
    <h2>Pet Joke of the Day</h2>
    <p>{{ctrl.joke}}</p>
  </script>

</body>
</html>
```

file: `app.js`
```javascript
const app = angular.module('MyApp', ['ngRoute']);

app.controller('ContactController', function () {
  this.phone = '555-1212';
});

app.controller('PetController', ['$routeParams', function ($routeParams) {
  // access :id from url
  this.id = $routeParams.id;
}]);

app.controller('JokeController', ['$route', function () {
  const jokes = ["Why don't cats like dogs?",
    "What is the difference between a duck?"];

  //you can access variables created in the when action like this
  this.joke = jokes[0];
}]);

app.controller('PricingController', ['$route', function ($route) {
  this.price = $route.current.price;
}]);

app.controller('AllController', ['$route', function () {
  const jokes = ["Why don't cats like dogs?",
    "What is the difference between a duck?"];

  this.joke = jokes[1];
  this.phone = '555-1212';
  this.price = '$1 billion dollars';
}]);

app.config(['$routeProvider','$locationProvider', function($routeProvider,$locationProvider) {
  // Enables Push State
  $locationProvider.html5Mode({ enabled: true });

  $routeProvider.when('/contact', {
    templateUrl: 'contact.html',
    controller: 'ContactController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/about', {
    templateUrl: 'about.html'
    // We don't need to specify a controller, because there's no data
  });

  // when http://localhost:3000/url1/:id - :id is a param just like in express
  $routeProvider.when('/pets/:id', {
    templateUrl: 'pets.html',
    controller: 'PetController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/pricing', {
    templateUrl: 'pricing.html',
    controller: 'PricingController',
    controllerAs: 'ctrl',
    price: '$1 trillion dollars'
  });

  $routeProvider.when('/joke', {
    templateUrl: 'joke.html',
    controller: 'JokeController',
    controllerAs: 'ctrl'
  });

  $routeProvider.when('/all', {
    templateUrl: 'all.html',
    controller: 'AllController',
    controllerAs: 'ctrl'
  });

  $routeProvider.otherwise({
    // if browser url doesn't match any of the above...
    // here you can do something like above if you'd like with a template and a controller
    redirectTo: '/' // or you can redirect to another url.
    // redirection can happen in any 'when' action; I just happened to do it here.
    // I could have put it in one of the above sections too
  });
}]);
```
