## Lesson Objectives

1. Completed `angular_delete.md`
1. change css with angular
1. simple update with double click
1. Make form make AJAX request on double click



## Changing css with angular

### Toggling a true/false value

We will want to change the appearance of the holidays we've celebrated and store this data in our database. Our Holiday object has a `celebrated` property that can be either true or false.

Let's make a new function `this.updateCelebrated` in our `app.js`

**In `js/app.js`**

```js
this.updateCelebrated = holiday => {
  console.log ("Let's celebrate", holiday.name, 'Day!')
}

```

**In `index.html`**

```html
<tr ng-repeat="holiday in ctrl.holidays">
  <td ng-dblclick="ctrl.updateCelebrated( holiday )"> {{ holiday.name }} Day</td>
  <td ng-click="ctrl.deleteHoliday( holiday._id )">x</td>
</tr>
```

So, now when we double click our holiday name, we should get a `console.log`

![Succes in console.logging](https://i.imgur.com/ex6sI7A.png)


Let's create some toggle functionality


**In `js/app.js`**

```js
this.updateCelebrated = holiday => {
  console.log("Let's celebrate", holiday.name, 'Day!')
  holiday.celebrated = !holiday.celebrated
  console.log(holiday.celebrated)
}
```

Now, every time we double click our holiday name, the `celebrated` property toggles between true and false

![true false toggle](https://i.imgur.com/Z7J9lWL.png)

### Using our true/false value to set a css class

We can base the css class on a boolean property we have in our holiday object. We have a property called `celebrated`, which by default, is false, but we can build in functionality to make it toggle between true and false.

We'll use a ternary operator. This fancy term is just a way to write a short-hand if-else statement.

A ternary operator is made up of 5 components

1. The statement to be evaluated. This statement will be evaluated if it is [truthy or falsey.](https://www.sitepoint.com/javascript-truthy-falsy/)
2. a question mark `?`
3. what happens if the statement is `truthy`
4. a `:` to separate the truthy evaluation from the falsey one
5. what happens if the statement is `falsey`

So if we have

```
(true) ? 'hooray' : 'boo'
```
This will evaluate to 'hooray'

or
```
(false) ? 'hooray' : 'boo'
```
This will evaluate to 'boo'


So, we can now dive into our `index.html` and change the `css` property based on the `celebrated` boolean value.

**In `index.html`**

```html
<tr ng-repeat="holiday in ctrl.holidays">
  <td ng-dblclick="ctrl.updateCelebrated( holiday )"
      ng-class="(holiday.celebrated ? 'celebrated' : '' )"
  > {{ holiday.name }} Day</td>
  <td ng-click="ctrl.deleteHoliday( holiday._id )">x</td>
</tr>
```

Now, when we refresh the page, we can double click on each holiday name and change its CSS every time, based on the `celebrated` value

### Updating our new `celebrated` value in our database

Every time we refresh our page, we lose our info of whether or not we celebrated our holiday.

We can use an `$http` call to update our database

**In `js/app.js`**

```js
this.updateCelebrated = holiday => {
  holiday.celebrated = !holiday.celebrated
  $http({
    method: 'PUT',
    url: '/holidays/' + holiday._id,
    data: { celebrated: holiday.celebrated }
  }).then(response => {
    console.log(response.data.celebrated)
  }, error => {
    console.log(error.message)
  })
}
```

Cool! Now we can update our database and when we reload our page it remembers if we have celebrated the holiday

## Take Aways
- Being able to manipulate css with angular adds an exciting level of interactivity
- Ternary operators are a neat way to keep our code short
- Update requires a couple extra steps, but when we make a plan and take it step by step it is not so bad

## Onwards
[Angular Show One Holiday](angular_show.md)
