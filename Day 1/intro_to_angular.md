# Intro to AngularJS

## Learning Objectives
- Describe what are Single Page Apps and their pros and cons
- Describe what are front-end frameworks
- Describe AngularJS and what problems it tries to solve
- Set up first Angular app
- Learn some basic angular directives

### Important

Remember in your google searches we are learning Angular 1.7 or AngularJS - the newer angular is called Angular or Angular 2.x - 6.x

## AJAX and the Start of Single Page Apps

A major advancement in web development was AJAX (Asynchronous JavaScript And XML).

What if you didn't have to reload an entire web page every time you needed to make a request? What if you only needed to reload the parts of the page that needed to be updated?

Think about google maps that are embedded in a web page. When you click on the map, move around, zoom in or out, the whole page doesn't reload, just the map.

This allows for a better user experience, since it is faster (no whole page reload), and generally smoother, (user keeps their place and can keep moving around the map).

This technology opened the doors to Single Page Apps.

## Single Page App Pros and Cons

Pros:
- Fast, resources only loaded once and then only data is transmitted back and forth
- The client's browser renders the views, rather than the server, which usually allows for a faster experience
- Separate data from views. By making the back end have end points that are just data, the data can be resused for multiple apps - desktop vs android app, vs iOS app all can use the same data/database.

Cons:
- Initial load can be slow because there could be a lot of code to download
- Building large apps gets very tricky very fast (new frameworks try to solve this over and over again)

## Front End Frameworks
You can build a single page application with vanilla javascript or jQuery.

But, much as we favored jQuery over vanilla JavaScript in unit 1 for its useful functionality and thus let us `write less and do more`, Front-End Frameworks have been developed to try to help solve common problems in developing Single Page Applications.

There are many, some of the most popular are
- Backbone
- Ember
- Meteor
- Knockout
- AngularJS (Angular 1.x)
- Angular (Angular 2.x - 5.x and beyond)
- Vue
- React

Some are popular because everyone was excited about them at one point and companies continue to use them. Others are popular because they have a lot of buzz.

As of March 2018, the landscape continues to change. There is even 'framework fatigue' where people are tired of all the options and new things being released all the time!

Front End Frameworks generally try to solve the same problems:
 - Speed - all frameworks are pretty darn fast, but this is something that gets a lot of attention. Speed is an easy metric to understand for even a non-coding person. But should not be the only deciding factor
 - Code organization - some stick to MVC, some combine MVC in some way, and some choose a different way to organize code. This helps with scaling the scope of the project, having many devs working with the same patterns so it is easier for people to maintain consistency and join projects
 - Managing state - state has to do with the way the data is stored/represented in the database, on the web page and how the data is updated

## AngularJS
AngularJS (Angular 1.x) came out in 2009 and was the first blockbuster framework. Everyone was excited about it, everyone wanted to use it. AngularJS is built by Google. It continued to grow in popularity and features for years.

Its replacement Angular (versions 2 - 5+), came out in January 2016 and was a total rewrite of Angular and is completely incompatible with Angular 1.x. Despite this new version, many companies kept and keep using 1.x.

Angular's features include:
- Open source, free to use
- Handles the V in MVC (Models, Views, Controllers)* (not everyone agrees with this particular semantic, but it is close enough; the alternate you might see is MVVM (Model-View-View-Model))
- 2 way data binding - the data displayed on the web page is the same as the data in js, if one is updated, the other updates as well. This is really cool, but also saves you from having to dedicate code to traverse, manipulate and listen to the DOM for every change of data on your page.
- Fast start! Getting started with AngularJS is as simple as adding a link in the header, just like jQuery.
- HTML Template, write your HTML as you always would, and then add in AngularJS to provide functionality where needed.
- Directives - these mark DOM elements to interact with AngularJS, things like click events, showing/hiding elements, binding data and much more
- Services (Dependency Injection), you can use AJAX to make http requests and many other services are available that can help you build all the functionality you'd like for a Single Page Application. The fancy term 'dependency injection' - just means it is pretty easy to add services. We'll see this later in our code alongs

## Our First AngularJS App

### Setup
In your student examples folder
- `mkdir first_app`
- `cd first_app`
- `touch index.html app.js`
- `atom .`

**index.html**
- add `html` boilerplate
- in the head tag add

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
```
and below that link our `app.js`
```html
<script src="app.js" charset="utf-8"></script>
```

### Module
We will only have one module in our apps. Our modules can have many controllers.

To make sure our module encompasses all our html, we're going to put the `directive` in our opening `html` tag.

Remember a directive is just extra text in an html element tag that will let us do cool angular stuff. It always starts with `ng-`

This one is to say 'hey this is our angular app'

```html
<html ng-app="MyApp">
```

Let's head on over to our `app.js` to configure our app.

**app.js**
```js
const app = angular.module('MyApp', []);
```
We're saying let's make a variable `app` that is equal to an angular.module, which is a function that takes two arguments.

The first argument **MUST** match what we named our app in our html, in this case, we named it `MyApp`, we could have named it `KarolinApp` or whatever we want, but it must match in the html and the `app.js` file.

The second argument is an empty array. Right now our app is going to be really simple and we'll just go over some fundamental AngularJS. We'll be filling in that array soon enough.

### Controller
Controllers control a certain area of the page. A controller can control it's own HTML element and the children inside of it. By default, it doesn't know anything about sibling or parent elements.

Let's write our first controller inside our

**app.js**

```js
app.controller('MainController', function(){
  this.hello = 'oh hai!';
});
```

**GOTCHA** - we have to use the ES5 `function(){}` as our second argument. If we use an arrow function, it won't work as expected.

Super! Now let's bind our controller to our html

We'll just have one controller, so let's bind it to the body.

`MainController` is a lot of typing. We can give it an alias `ctrl` so we can type less

**index.html**

```html
<body ng-controller = "MainController as ctrl">
```
Last step, let's get our data stored in `this.hello` to render in our HTML

Our data will match in our HTML and in our `app.js` and update whenever one is changed.

However, we access our data a little differently depending if we are in our html or js file.

When in our html file, we access the data by starting with `ctrl` which is the alias we set for `MainController`.

When in our js file, we acccess the data by starting with `this`

so in this case our data is the text string `oh hai!`

To access it in the js it is
```js
this.hello = 'oh hai!';
```

and to access it in the html:

```html
<body ng-controller = "MainController as ctrl">
  <h1>{{ ctrl.hello }}</h1>
</body>
```

Let's test it out and see if our data renders!

All our `index.html`

```html
<!DOCTYPE html>
<html ng-app="MyApp">
  <head>
    <meta charset="utf-8">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
    <script src="app.js" charset="utf-8"></script>
    <title>My First App</title>
  </head>
  <body ng-controller='MainController as ctrl'>
    <h1>{{ ctrl.hello }}</h1>
  </body>
</html>
```

All our `app.js`

```js
const app = angular.module('MyApp', []);

app.controller('MainController', function(){
  this.hello = 'oh hai!';
});
```

### Directives

We'll go over a few directives beyond what we needed to set up our app
- `ng-repeat`
- `ng-src`
- `ng-if`
- `ng-show`/ `ng-hide`
- `ng-click`

Let's make a page for our contacts.

First let's add a little css
```html
<style>
img {
  max-width: 100px;
  display: inline-block;
}
dl {
  display: inline-block;
  vertical-align: top;
}
</style>
```

### ng-repeat
Here is some data of our contacts. Let's copy paste it into our `app.js` INSIDE our `app.controller` function

```js
this.contacts = [
    {
      name: 'Jenny',
      phone: 8675309,
      state: 'California',
      image: 'http://old.unsquare.com/dance/wp-content/uploads/2008/11/jennylewis01.jpg'
    },
    {
      name: 'Claire',
      phone: 6060842,
      state: 'DC',
      image: 'http://www1.pictures.zimbio.com/mp/yOE-jyqpuoql.jpg'
    },
    {
      name: 'Morris',
      phone: 7779311,
      state: 'Minnesota',
      image: 'https://s1.ticketm.net/tm/en-us/dbimages/111807a.jpg'
    },
    {
      name: 'Alicia',
      phone: 4894608,
      state: 'New York',
      image: 'https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/AliciaKeys2013.jpg/220px-AliciaKeys2013.jpg'
    },
    {
      name: 'Etta',
      phone: '842-3089',
      state: 'California',
      image: 'http://images5.fanpop.com/image/photos/30600000/SWEET-ETTA-JAMES-etta-james-30694011-584-519.gif'
    }
  ];

```

Let's make cards for each of our contacts. First let's just see if we can get their names to display

**Index.html**

```html
<body ng-controller='MainController as ctrl'>
  <h1>{{ ctrl.hello }}</h1>
  <div ng-repeat="contact in ctrl.contacts">
    <dt>{{ contact.name }}<dt>
  </div>
</body>
```


Expected Appearance:

![angular contacts](https://i.imgur.com/uWUEkae.png)

### ng-src
Let's build out some more of our contact list.
We'll use another directive `ng-src` to get our images to show up correctly

```html
<div ng-repeat="contact in ctrl.contacts">
  <img ng-src="{{ contact.image }}" alt="an image of {{ contact.name }}">
  <dt>{{ contact.name }}<dt>
</div>
```
Expected Apperance:

![angular contacts](https://i.imgur.com/bUcIntN.png)

Let's add some more

```html
<div ng-repeat="contact in ctrl.contacts">
  <img ng-src="{{ contact.image }}" alt="an image of {{ contact.name }}">
  <dl>
    <dt> {{ contact.name }} </dt>
    <dd> {{ contact.state }} </dd>
    <dd> {{ contact.phone }} </dd>
  </dl>
</div>
```

Cool! We can see that `ng-repeat` let's us loop over our array of objects and make a new element for each contact

### ng-if
We may not want everything to appear. We can control what appears on the page using `ng-if`

in **app.js**

```js
this.showImages = false;
```

in **index.html**
```html
  <img ng-src="{{ contact.image }}" alt="an image of {{ contact.name }}" ng-if="ctrl.showImages">
```

Now, all of our images should disappear.

Expected Appearance:

![ng if hide images](https://i.imgur.com/l5vpQ8w.png)

We can look into our Chrome console elements tab for more info

![chrome console elements](https://i.imgur.com/LeHbImP.png)

**app.js**

Let's change that false to true

```js
this.showImages = true;
```

Our images should reappear and in chrome console we should now see our img element. Before we just had a comment about the `ng-if` and our element was gone. Now it should be back

![ng if true](https://i.imgur.com/Etn8m0R.png)

## ng-show / ng-hide

In **app.js**

```js
this.showInfo = false;
```

In **index.html**
```html
<dl>
  <dt> {{ contact.name }} </dt>
  <dd ng-show="ctrl.showInfo"> {{ contact.state}} </dd>
  <dd ng-show="ctrl.showInfo"> {{ contact.phone}} </dd>
</dl>
```
In contrast to `ng-if` `ng-show` changes to `ng-hide` and the element is still on the page

![ng-show](https://i.imgur.com/VyPVJy4.png)

## ng-click

We need a way to be able to interact with our page.

Let's write a function that will toggle our `this.showInfo` value

**app.js**

```js
this.toggleInfo = () =>{
  this.showInfo = !this.showInfo;
}
```

Now let's add a click event to a button that will call this function


```html
<body ng-controller='MainController as ctrl'>
  <h1>{{ ctrl.hello }}</h1>
  <button ng-click="ctrl.toggleInfo()"> Toggle Info </button>
```

Now, as we update the value of `this.showInfo` our page changes to reflect that.


Resources
[SPA vs MPA](https://medium.com/@NeotericEU/single-page-application-vs-multiple-page-application-2591588efe58)

[AngularJS](https://www.tutorialspoint.com/angularjs/angularjs_overview.htm)
[5 Awesome AngularJS Features](https://code.tutsplus.com/tutorials/5-awesome-angularjs-features--net-25651)

[How it feels to learn JavaScript in 2016](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f)

[How it feels to learn JavaScript in 2017](https://medium.com/front-end-hacking/how-it-feels-to-learn-javascript-in-2017-a934b801fbe)
