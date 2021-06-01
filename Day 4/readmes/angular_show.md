## Lesson Objectives

1. Completed `angular_update.md`
1. show a detailed view of one holiday on hover

## Get One Holiday Object

We'll need to set a global variable inside our `MainController`, so that we can just grab the value and then be able to update and reuse it on the functions. There are many ways to do this, but we'll just go with a simple way.

**In `js/app.js`**

add `this.holiday = '';`

```js
app.controller('MainController', ['$http', function ($http) {
  this.h5 = 'Holidays! Celebrate!'
  this.createForm = {}
  this.holiday = ''

```

Now, let's write a function that lets us grab this value when we hover over a holiday in the browser. We'll add a console.log just to check that it works

```js

this.chooseOneHoliday = holiday => {
  this.holiday = holiday
  console.log(this.holiday.name)
}
```

Super! Now we just need to call this function. Let's do it on a mouseover on the table cell that has our holiday name in it

```html

<tr ng-repeat="holiday in ctrl.holidays">
  <td ng-dblclick="ctrl.updateCelebrated( holiday )"
      ng-class="(holiday.celebrated ? 'celebrated' : '' )"
      ng-mouseover="ctrl.chooseOneHoliday( holiday )"
  > {{ holiday.name }} Day</td>
  <td ng-click="ctrl.deleteHoliday( holiday._id )">x</td>
</tr>

```

Great! Now when we mouse over our holiday names, we should see them in our Chrome Dev tools:

![mouseover console log](https://i.imgur.com/xfIbkFW.png)

This HTML will play nice with our CSS

```html

<div class="details">
  <h3>Holiday Info:</h3>
  <hr>
  <h4> {{ ctrl.holiday.name }} Day </h4>
  <h6><span> Celebrated: </span>  {{ ctrl.holiday.celebrated }} </h6>
  <h6> <span> Likes:</span> {{ ctrl.holiday.likes }}</h6>
  <p><span> Description: </span> {{ ctrl.holiday.description }} </p>
</div>

```

Success!

![holiday details showing up](https://i.imgur.com/FrkZe0u.png)

## Display a Default Holiday

When we load our page, there is no default holiday, so our holiday details is empty, we can load it with the first holiday in our list.

![empty details](https://i.imgur.com/66AZqZO.png)

We may be tempted to just set `this.holiday = this.holidays[0];`

But it breaks!

![Broken Angular page](https://i.imgur.com/LXj7MBY.png)

This is because we are trying to load the value **before** we get a response from the server that populates our holidays array.

Let's set back to an empty string `this.holiday =''` and let's set the value inside the response of `this.getHolidays`

```js
this.getHolidays = () => {
  $http({
    method: 'GET',
    url: '/holidays'
  }).then(response => {
    this.holidays = response.data
    this.holiday = this.holidays[0]
  }, error => {
    console.log(error)
  })
}
```

That should now load the first holiday on page load

## Take Aways
- Sometimes setting a global variable makes things easier, it isn't truly global, because it is still in our Main controller.

## Onwards

Y'all did it! You made it through the class build. Let's go work on a lab to refine our holidays app some more
