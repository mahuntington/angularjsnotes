
## Lesson Objectives

1. Completed `angular_set_up.md`
1. Create new holiday form
1. Hook form up to controller properties
1. Make form make AJAX request on submit
1. Check that the request was successful


## Upgrade our `MainController` in `app.js` to use the `$http` module
- You'll need the `$http` request module in order to interact with our express server
- We'll need to carefully upgrade our `app.Controller` to have a second argument as an array, and then pass in the value as a parameter in the callback function (still inside the array!)


**In `js/app.js`**
```js
const app = angular.module('HolidaysApp', [])

app.controller('MainController', ['$http', function ($http) {
  this.h5 = 'Holidays! Celebrate!'
}])
```

- Let's test it to make sure it didn't break our code. Our `Holidays! Celebrate!` message should still appear in the browser

## Create new holiday form

**In `index.html`:**

```html
<body ng-controller="MainController as ctrl">
  <div class="container">
    <h5> {{ ctrl.h5 }} </h5>
    <form>
      <input type="text" placeholder="Holiday name" />
      <input type="submit" value="Create New Holiday" />
    </form>
  </div>
</body>
```

## Hook Form Up to Controller Properties

**Remember**

- use `ng-model` inside the `input` tags
- For now, we're only going to create holiday names
- the value of each `ng-model` needs to match the schema key (you can check in the `models` folder)


**In `index.html`:**

```html
<form>
    <input type="text" ng-model="ctrl.createForm.name" placeholder="Holiday name"/>
    <input type="submit" value="Create New Holiday"/>
</form>
```

- This is a good time to open Chrome Dev tools in your browser

## Make form make a function call

**In `js/app.js`**

```javascript
app.controller('MainController', ['$http', function ($http) {
  this.h5 = 'Holidays! Celebrate!'

  this.createHoliday = () => {
    console.log ('submit button calls this function');
    }
}])
```

We should be able to click our `create new holiday` button ... and ... nothing should happen!

We have to add the function to our `html`
- Upgrade our opening `form` tag to the following:

**In `index.html`:**

```html
<form ng-submit="ctrl.createHoliday()">
```

- Don't forget to refresh your browser

- Now, when we click `create new holiday`, we should see a `console.log` in our browser that reads `submit button calls this function`

- We have successfully connected our form to our `app.js` and we are able to call the right function



- Now we have to make that code do something more useful

## On form submit make AJAX request to our API/server


**In `js/app.js`**

1. add a new empty object called `this.createForm`, outside of our `this.createHoliday` function
1. add an `$http` function
  - Include an object as the argument that includes the following
    - `method` :`'POST'`
    - `url`    : `/holidays`
    - `data`   : `this.createForm`
  - Chain a `.then()` after the `$http` function, with an argument of `response`
    - The `.then()` function waits for the `http` request to go through and give a response
    -  `.then()` function can then use what the server has responded with
    - `.then()` takes two arguments, the first is a successful response from the server, the second is the error response **if** there is an error
    - for now, let's just `console.log` the response

```javascript
app.controller('MainController', ['$http', function ($http) {
  this.h5 = 'Holidays! Celebrate!'
  this.createForm = {}


    this.createHoliday = function(){
        $http({
            method:'POST',
            url: '/holidays',
            data: this.createForm
        }).then(response => {
            console.log(response)
        },  error => {
            console.log(error)
        })
    }
}])
```

- Successful POST request `console.log`:
![POST request response success](https://i.imgur.com/OlNhgU9.png)

## Get the data from the response

The response has a lot of stuff in it. We just want the data. Let's update our `console.log` to just show our data

```js
this.createHoliday = () => {
  $http({
    method: 'POST',
    url: '/holidays',
    data: this.createForm
  }).then(response => {
    console.log(response.data)
  }, error => {
    console.log(error)
  })
}
```

Success!
![just response data](https://i.imgur.com/NJykBmW.png)

- We can tell this has been successfully added to our database because the `console.log` is coming from the response, we also see a mongo `id`, and also the other default values have been added in
- Nice work!


## Onwards
- go to the [angular_index.md](angular_index.md)
