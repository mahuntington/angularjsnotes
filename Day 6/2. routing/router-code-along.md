# Additional Angular Topics

## Lesson Objectives

1. Create partials
1. Create custom directives
1. Use dependencies
1. Use angular router

## Create partials

### Describe why we need includes

Includes allow us to take reusable content and move it into external files so that we don't copy and paste the same code over and over

### Include an external file

Use the following syntax to include an external html file. **Note the extra single quotes since it can take valid javascript**

```html
<div ng-include="'partials/included.html'"></div>
```

Inside this external file, you can do write normal html with angular directives.  You can even reference controllers outside of the file that are ancestors.

It does this via AJAX, but normally a browser cannot make an AJAX request to a file on a computer (insecure!).  Two ways around this:

- Start a basic http server from the command line
	- execute this command in the same directory as application: `python -m SimpleHTTPServer` and visit [http://localhost:8000](http://localhost:8000)
- Start up chrome with extra params
	- `open /Applications/Google\ Chrome.app --args --allow-file-access-from-files`

#### Example

file: index.html
```html
<!DOCTYPE html>
<html >
<head>
	<script src='https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.8/angular.min.js'></script>
</head>
<body ng-app>
	<div ng-include="'partials/included.html'"></div>
</body>
</html>
```

file: partials/included.html
```html
oh hai!
```

## Create custom directives

### Explain what a custom directive is

You can create your own directives, just like ng-click, ng-submit, etc.  This allows you to abstract away complicated/reusable code into an attribute, a class, a comment, or even your own HTML element!

### Create your own custom directive

Here's our custom HTML element:

```html
<product-title></product-title>
```

the full html:

file: index.html
```html
<!DOCTYPE html>
<html >
<head>
	<script src='https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.5.8/angular.min.js'></script>
	<script type="text/javascript" src="js/app.js"></script>
</head>
	<body ng-app="MyApp">
		<product-title></product-title>
	</body>
</html>
```

Here's how we define its behavior:

file: js/app.js
```javascript
const app = angular.module('MyApp', []);

app.directive('productTitle', function(){
	return {
		restrict: 'E', // E=element, A=attribute, C=class, M=comment, can be combined
		templateUrl: 'partials/product-title.html', //template to replace directive element
		controller: function(){ //controller constructor function
			this.name = "foo"
		},
		controllerAs: 'title' //how it should be instantiated (Controller as title)
	};
});
```

Here's the external template referenced above:

file: partials/product-title.html
```html
<h3>
	{{title.name}}
</h3>
```

## Use dependencies

### Explain what dependencies do

- We can group functionality together into different modules
- You can have multiple ng-apps on your page, each with specific functionality
- Modules can be dependent on other modules
- Very large modules can be broken out into smaller sub modules, grouped by functionality

### Create a module and inject it into another one

In this example, imagine we have a very large module for our "store" app.  Since we don't want one very large file, we can break it apart into many smaller files.

This first file will be module just for the store directives

file: js/store-directives.js
```javascript
const app = angular.module('store-directives', []);
app.directive('productTitle', function(){
	// directive stuff
});
app.directive('productGallery', function(){
	// directive stuff
});
```

We can now include the module for our directives in the store as a whole

file: js/store.js
```javascript
const app = angular.module('store', ['store-directives']);
```

## Use angular router

### Describe Push State

You can update the browser's url without refreshing the page using JS

- this is called Push State.  It's part of HTML5
- up until recently, the only way to update the url is make a new request
	- click a link
	- type into browser's url bar and hit enter
- now you can do that with javascript

What can Angular do with this?

- angular will listen for changes in the url
- next, angular will render a template into a specific part of the page that we define
- it will then attach a controller to this template and alias it `as` a variable which can be referenced in the HTML

### Set up your HTML with proper tags and directives

- We need to include Angular and ngRoute, which is a separate file
- This approach is similar to express, where functionality that is not crucial is moved to separate packages (ejs, method-override, etc...)

file: index.html
```html
<!DOCTYPE html>
<html>
	<head>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular.min.js"></script>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular-route.min.js"></script>
	</head>
	<body>

	</body>
</html>
```

Next, tell angular where to render the templates with the `ng-view` directive

```html
<!DOCTYPE html>
<html>
	<head>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular.min.js"></script>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular-route.min.js"></script>
	</head>
	<body>
		<main ng-view></main><!--add ng-view here-->
	</body>
</html>
```

We need to make sure all links are relative to a specific base url (/ in this case).  Add the following:

```html
<head>
	<!-- other head tag stuff here -->
	<base href="/">
</head>
```

<details><summary>file: index.html</summary>

```html
<!DOCTYPE html>
<html>
	<head>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular.min.js"></script>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular-route.min.js"></script>
		<base href="/"><!-- add base href here -->
	</head>
	<body>
		<main ng-view></main>
	</body>
</html>
```

</details>

Links that start with `http` will not use push state.  Everything else will.

file: index.html
```html
<!DOCTYPE html>
<html>
	<head>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular.min.js"></script>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular-route.min.js"></script>
		<base href="/">
	</head>
	<body>
		<nav>
			<ul>
				<li><a href="/">root</a></li>
				<li><a href="/url1">url1 with no param</a></li>
				<li><a href="http://www.google.com">google</a></li><!-- only this link goes outside of the app because it starts with http -->
			</ul>
		</nav>
		<main ng-view></main>
	</body>
</html>
```

### Write initial JS

We need one dependency for our module

file: js/app.js
```javascript
const myApp = angular.module('MyApp', ['ngRoute']);
```

Add `ng-app="MyApp"` to the `<body>` tag in your html

<details><summary>index.html</summary>

```html
<!DOCTYPE html>
<html>
	<head>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular.min.js"></script>
		<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.0/angular-route.min.js"></script>
		<script type="text/javascript" src="js/app.js"></script>
		<base href="/">
	</head>
	<body ng-app="MyApp">
		<nav>
			<ul>
				<li><a href="/">root</a></li>
				<li><a href="/url1">url1 with no param</a></li>
				<li><a href="http://www.google.com">google</a></li><!-- only this link goes outside of the app because it starts with http -->
			</ul>
		</nav>
		<main ng-view></main>
	</body>
</html>
```

</details>

We need to create a `config` for our module to make this work.  A module config runs only once at load time.

file: js/app.js
```javascript
const myApp = angular.module('MyApp', ['ngRoute']);

myApp.config(function() { //.config just runs once on load.  Looks like a controller, but without a name
});
```

Let's add two important services

- `$locationProvider` handles push state
	- push state
		- allows you to update the URL in your browser without reloading the page
		- also updates your history when URL changes via push state
- `$routeProvider` creates event listeners which render a template into ng-view when the browser URL matches its URL

file: js/app.js
```javascript
//include $routeProvider and $locationProvider
const myApp = angular.module('MyApp', ['ngRoute']);

myApp.config(['$routeProvider', '$locationProvider', function($routeProvider, $locationProvider) { //.config just runs once on load
    $locationProvider.html5Mode({ enabled: true }); // tell angular to use push state
}]);
```

### Write a basic route

Now let's add a basic route within the config function, after `$locationProvider.html5Mode({ enabled: true });`.

```javascript
$routeProvider.when('/url1', { //when http://localhost:3000/url1
	templateUrl: 'partials/partial1.html', // render http://localhost:3000/partials/partial1.html
	controller: function(){ // attach controller
		//controller code goes here
	},
	controllerAs: 'ctrl' // alias for controller (like ng-controller="Ctrl1 as ctrl")
});
```

<details><summary>file: js/app.js</summary>

```javascript
const myApp = angular.module('MyApp', ['ngRoute']);

myApp.controller('Ctrl1', function(){

});

myApp.config(['$routeProvider', '$locationProvider', function($routeProvider, $locationProvider) { //.config just runs once on load
	$locationProvider.html5Mode({ enabled: true }); // tell angular to use push state

	$routeProvider.when('/url1', { //when http://localhost:3000/url1
		templateUrl: 'partials/partial1.html', // render http://localhost:3000/partials/partial1.html
		controller: function(){ // attach controller
			//controller code goes here
			this.foo = 'bar'
		},
		controllerAs: 'ctrl' // alias for controller (like ng-controller="Ctrl1 as ctrl")
	});

}]);
```

</details>


add the file: partials/partial1.html

```html
Controller foo var: {{ctrl.foo}}
```

- Now when you click the "url1 with no param" text, you should see the partial appear in the appropriate place
- Note, if you refresh the page after clicking the link, you'll get a file not found
	- That's because there's no file called `url1`
	- You'll have to tell express to serve up your index.html file for that route

		```javascript
		app.get('/url1', (req, res)=>{
			res.render('/public/index.html') //note the initial / so it doesn't go into views
		})
		```
