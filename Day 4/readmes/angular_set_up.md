

## Lesson Objectives

1. Complete `install_test_api.md`
1. Set up Angular
1. Create App/Controller


## Describe the elements of the MEAN stack

MEAN stack is just a collection of tech that work well together

- Mongo
- Express
- Angular
- Node.js

## Move into working with static files

1. in the `public` directory, we'll be working with `index.html` and `/js/app.js`



## Set up Angular

**In `index.html`:**
1. Include Angular cdn link in the `head` tag
1. Set up app, by including `ng-app` inside the `html` tag
1. Test {{}}, inside the `body` tag, but doing something simple like summing `2 + 2`
1. Open `localhost:3004` to test

```html
<!DOCTYPE html>
<html ng-app>
    <head>
      <!-- ...  -->
      <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js" charset="utf-8"></script>
      <!-- ...  -->
    </head>
    <body>
     {{2+2}}
    </body>
</html>
```

## Create App/Controller

**In `js/app.js`**

```javascript
app.controller('MainController', ['$http', function ($http) {
  this.h5 = 'Holidays! Celebrate!'
}])

```

**Note**: We may be tempted to write `()=>{}` as the callback in `app.controller`, but it won't work as expected, we have to use `function(){}` for this particular callback

**Note 2**: We are not using the $http service yet, but we will!

**In `index.html`:**
1. set `ng-app` value to `HolidaysApp` in the `html` tag
1. add script tag for `/js/app.js` inside the `head` tag, below the angular cdn
1. set `ng-controller` to `MainController` in the `body` tag in `index.html`
1. set `MainController` to have an alias of `ctrl
1. create a `div` with the class of `container`
1. remove: `{{ 2 + 2 }}`
1. Insde the `container div`, add `<h5> {{ ctrl.h5 }} </h5>` (h1 is a bit too big and distracting, despite being more semantically accurate - we can polish html/css later)

public/index.html:

```html
<!DOCTYPE html>
<html ng-app="HolidaysApp">
  <head>
    <meta charset="utf-8">
    <title>Holiday Celebrations</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js" charset="utf-8"></script>
    <script src="/js/app.js" charset="utf-8"></script>
    <link rel="stylesheet" href="/css/normalize.css">
    <link rel="stylesheet" href="/css/skeleton.css">
    <link rel="stylesheet" href="/css/main.css">
  </head>
  <body ng-controller="MainController as ctrl">
    <div class="container">
      <h5> {{ ctrl.h5 }} </h5>
    </div>
  </body>
</html>
```
**Note**: We are using an tag for speed, not sematics, best practice would be to use an h1 and use css to properly resize it.  But we still have a lot of code to write, so let's see if we have time at the end to fix this. 

We now should have our browser look like this:

![Angular set up successfully](https://i.imgur.com/KyaWJjM.png)

## Take Aways
Setting up angular takes a few steps, but going slowly and testing one step at a time can make us sure we're ready for success

## Onwards
Continue to [angular_create.md](angular_create.md)
