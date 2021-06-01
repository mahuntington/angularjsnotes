# Intro to AJAX and AngularJS HTTP Service

## Learning Objectives
- Describe what is AJAX
- Describe what AJAX is used for
- Describe what are 3rd Party APIs and How they are used
- Describe what are API keys and how to use them
- Review Query Parameters and how they can be used in 3rd Party API requests
- Set up $http service for an Angular App
- Learn how to use $http to make requests
- Learn how to incorporate data from $http requests into a SPA using AngularJS

##  AJAX

AJAX stands for Asynchronous JavaScript an XML.

XML was once a popular way to store and send data over the internet and it is still used. However, JSON has become the predominant way to send data over the internet.

When we will use AJAX, we will be sending and receiving JSON.

AJAX allows us to only reload portions of a web page. Thinking of an embedded google map, you can click around it and change the view/data, without having to reload the page every single time.

## Third Party APIs

Many web sites have their own data, but they can pull in other data. For example, many news sites have a weather widget. This widget gets its data from a weather resource.

There are many APIs that can be used by individuals and companies. Some are totally free, some are available for a small fee, and some are really expensive.

There are APIs for
- Weather
- Stocks
- Beer
- Dictionaries
- Books
- Sports
- Art
- Games
- Movies

[Here is one list of APIs](https://github.com/toddmotto/public-apis)

## API Keys

Many APIs are restricted. Maintaining data on a server can get expensive and the data on a lot of these sites is valuable.

The two main ways individuals/companies can get access to APIs is through API keys - a special set of characters that is purchased through the website. Every time you make a request, the key must be used, this lets the API keep track of how many requests you make and limit/charge you accordingly.

The other way is OAuth. OAuth is a tangent to what we'll talk about today, but if you want to learn more, here is a [good start](https://stackoverflow.com/questions/4201431/what-exactly-is-oauth-open-authorization).

Typically, API keys go in as query parameters. Query parameters go at the end of a URL. They start with a `?` and then have key value pairs. Many key-value pairs can be used and each key-value pair can be used by separating them with an equal sign.

Here is an example of a request to OMDB (open movie data base), for a movie with the title `Eraserhead` and the `apikey` (this is a fake key).

```
http://www.omdbapi.com/?t=Eraserhead&apikey=x9903Ddc
```

A response in JSON will be sent and could be seen in the browser

![eraserhead omdb json](https://i.imgur.com/PvhRgo8.png)


## Mini Movie App

We're going to build an AngularJS single page app that has a text input, a button and when a user inputs a movie, they will get a set of movies that match from OMDB.

### Setup

In student_examples
- `mkdir movies`
- `cd movies`
- `touch app.js index.html`
- `atom .`

**Index.html**
- html boilerplate
- link angular
```
https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js
```
- link `app.js` below angular
- set `<html ng-app="MovieApp">`
- set `<body ng-controller="MainController as ctrl">`
- Inside the body include {{ 2+ 2 }} this should evaluate and display the number 4 when we have everything set up correctly

**app.js**

- set a variable app to the angular module. Remember the `ng-app` name in the html must match the string

```js
const app = angular.module('MoviesApp', []);
```

We're going to use the $http service, so we need to configure it.

In our `app.controller`, our first argument will still be `MainController`

Our second argument will be an array.

The array will have two items. The first is a string `$http`, and the second is an ES5 function with `$http` passed in as an argument.

The set of brackets is going to look a bit odd at first, but we'll get used to it.

We'll be coding all our angular functionality inside the curly braces.

```js
app.controller('MainController', ['$http', function($http) {
  // code goes here
}]); // closes app.controller
```

If we did it right, we should see the number 4 evaluate in the browser

Let's include the title as data from our angular app. That will let us quickly know if we have an error with our basic code.

**app.js**



```js
app.controller('MainController', ['$http', function($http) {
  this.appName = "Gerard Von Schtieffel's MüvieHaüs"
}]); // closes app.controller
```

**index.html**
```html
<body ng-controller="MainController as ctrl">
  <h1>Welcome to {{ctrl.appName}}</h1>
</body>
```

### Using $http

We're going to be making requests to OMDB using `$http`. We'll be viewing those results in Chrome's Console. Once we build out the functionality, we'll start incorporating our data into our web page.

Go to [request an api key from OMDB](http://www.omdbapi.com/apikey.aspx)

Fill out the form, you should get an email asking you to validate your key within a few minutes. Hold on to this key, we'll be using it soon enough.

`$http` is a powerful AngularJS function. jQuery has a similar function called `$.ajax`. Vanilla JS also has its own version called 'fetch'. All follow a very similar pattern. Since we are already using AngularJS we'll use Angular's.

An http request requires:
- headers ($http handles most of this for us)
- a url to send the request
- a method (get, post, put, delete...)
- a way to send data if it is a post or put request
- a way to wait for the response before doing something with the incoming data
- a way to do something with the incoming data
- a way to handle errors

Let's build our first $http request.

`$http` is a function. it takes one argument which **MUST** be an object. We'll code this out together after we put together our query string

The object requires a minimum of two key value pairs
- `method` - a string: `GET`, `POST`, `PUT`, `DELETE`
- `url` - a string of where to send the request

- if the request is sending data (ie input from input fields), a third key value pair is required
- `data`: an object of key value pairs
Since we are just sending GET requests, we won't need `data` for now

**app.js**

Let's build our url

```js
this.baseURL = 'http://www.omdbapi.com/?';
this.apikey = 'apikey=' + 'your api key here';
this.query = 't=';
this.movieTitle = 'Eraserhead';
this.searchURL = this.baseURL + this.apikey + '&' + this.query + this.movieTitle;

console.log(this.searchURL);
```
our searchURL should look like (the api key is fake, it should be the one you got via email)

```js
http://www.omdbapi.com/?apikey=9999999&t=Eraserhead
```

Note: including your API key in the `app.js` and then pushing it up to github makes your API key findable. OMDB keys are not that valuable, so it shouldn't be a worry.

However, there are services that cost thousands of dollars a month. People write bots to look for exposed API keys to steal them. We don't have time to cover hiding API keys today. But keep it in mind for your projects.

```js
$http()
```

```js
$http({})
```

```js
$http({
  method: 'GET',
})
```

```js
$http({
  method: 'GET',
  url: this.searchURL
})
```

This will send our request, but now we have to handle the response. The $http function returns a promise. A promise is an object that handles asynchronous operations.

Remember, JavaScript just runs through the code top to bottom. It doesn't wait for one thing to finish before moving on to the next thing.

One way we've been handling waiting is by using callbacks. Callbacks can get unwieldy. Promises can be a nicer way to write our code. Often you'll be working with promises that were already created for you from a library or framework (like `$http`)

A promise is a way of writing code that says 'hey, wait for me, and I _promise_ I'll send you a response soon. _Then_, you can do what you need to do'

A promise has three states
- Pending
- Fulfilled (completed successfully)
- Rejected (it failed)

After calling the initial function (in our case `$http()`), all we need to do is chain another function `.then()`

`.then()` takes two callback functions.

The first one is `onFulfilled` this one is executed if the promise is fulfilled. This function can do whatever we want, for now, we'll just console.log the response.

The second one is `onRejected` this one is executed instead of the first one if the promise has failed in some way.

```js
$http({
  method: 'GET',
  url: this.searchURL
}).then( response => {
  console.log(response);
})
```

Expected Appearance:
![success OMDB Eraserhead console response](https://i.imgur.com/sBKmcT7.png)

## Extracting the data from our response

Our response has a lot of useful stuff in it, status, headers, config... useful stuff that our users do not care about.

Our users care about the data about the movie. When we look at the object we get back in the console, we can see our data of interest is inside `data` so let's grab it.

```js
$http({
  method: 'GET',
  url: this.searchURL
}).then( response => {
  console.log(response.data);
})
```

Expected Appearance:
![response.data](https://i.imgur.com/m08tmH4.png)

## Error handling

When things work, it's awesome. When things fail, having helpful errors can help us track down what is wrong.

Our `.then()` function takes a second argument, a callback to handle errors.

Also, promises have a `.catch()` method that help us catch any other errors. Let's code it out.

```js
$http({
  method: 'GET',
  url: this.searchURL
}).then( response => {
  console.log(response.data);
}, error => {
  console.error( error );
})
```

```js
$http({
  method: 'GET',
  url: this.searchURL
}).then( response => {
  console.log(response.data);
}, error => {
  console.error( error );
}).catch( err => console.error('Catch: ' , err ));
```
## putting $http in a function

Right now, as soon as we load the page, we send this get request. We need a way to control when this function is executed.

Let's wrap it in a function

```js
this.getMovies = () => {

}
```
copy paste our $http function into `this.getMovies`

```js
this.getMovies = () => {
  $http({
    method: 'GET',
    url: this.searchURL
  }).then( response => {
    console.log(response.data);
  }, error => {
    console.error( error );
  }).catch( err => console.error('Catch: ' , err ));
}
```

### Triggering our function from our web page

Let's create a form and on submit, it will send our request.

We'll use a new directive `ng-submit` so that when the submit button is pushed it will trigger whatever function is assigned to it.

**index.html**

```html
<form ng-submit="ctrl.getMovies()">
	<button type="submit">SUBMIT</button>
</form>
```

Let's test it, don't forget to push the button.

Expected Appearance:
![movie form](https://i.imgur.com/yjP0CGQ.png)


### Making our data appear on the page

We're going to need a variable that is visible to both our app.js and index.js that we can store our movie data.

At first we'll just be grabbing one movie, but we'll hope to be able to grab an array. So we'll name our variable movies

**app.js**

```js
this.movies = [];

this.getMovies = () => {
  $http({
    method: 'GET',
    url: this.searchURL
  }).then( response => {
    this.movies = [response.data ];
  }, error => {
    console.error( error );
  }).catch( err => console.error('Catch: ' , err ));
}

```

**index.html**

```html
<div ng-repeat="movie in ctrl.movies">
  <h2>{{ movie.Title }} {{ movie.Year }}</h2>
  <img ng-src='{{ movie.Poster }}' />
  <h3> Starring: {{ movie.Actors }} </h3>
  <h4> Plot: {{ movie.Plot }} </h4>
  <h5> Genre: {{ movie.Genre }} </h5>
  <h6> Rated: {{ movie.Rated }} </h6?
</div>
```
## Making Specific Requests

Right now our movie app is hard coded to always return info about Eraserhead.

It would be great to look for any movie.

Let's make an input.

We'll use the directive `ng-model` that will grab whatever we put in our input field. The value of `ng-model` is they key, the property will be whatever is in the input field.

```html
<form ng-submit="ctrl.getMovies()">
  <input type="text" placeholder="movie title" ng-model="ctrl.movieTitle" />
	<button type="submit">SUBMIT</button>
</form>
```

Let's adjust our **app.js**

- change this.movieTitle to an empty string
- remove `this.movieTitle` from `this.searchURL`
- add `this.movieTitle` to the `url` inside the `$http` function

```js
this.movies =[];
this.movieTitle ='';
this.baseURL = 'http://www.omdbapi.com/?';
this.apikey = 'apikey=' + '9999999';
this.query = 't='
this.searchURL = this.baseURL + this.apikey + '&' + this.query;
```

Update our url inside our function

```js
this.getMovies = () => {
  $http({
    method: 'GET',
    url: this.searchURL + this.movieTitle;
```

Now we can search for whatever movie we want.

## Lab

Our app could still use some upgrades.

Right now it only shows one movie.

Research the OMDB docs on how to get a list (hint research `s=` we've been using `t=`)

Then, only display the movie titles. Allow the user to click on a movie and see the movie's details.  
