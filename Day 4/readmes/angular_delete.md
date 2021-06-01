
## Lesson Objectives

1. Completed `angular_index.md`
1. Update our HTML, to make room for a delete button
1. Make form make AJAX request on x click
1. get the object id and pass it in
1. On successful delete, update the page

## Update our `index.html` to have a table

A table's vertical alignment is pretty darn nice, let's use it

We'll update our table to have a cell with x which will end up being our 'delete button'

**In `index.html`**

```html
<table>
  <tr ng-repeat="holiday in ctrl.holidays">
    <td> {{ holiday.name }} Day</td>
    <td>x</td>
  </tr>
<table>
```


Now our page will look like this:

![add x to table](https://i.imgur.com/3EP0FhL.png)

## Create a delete function inside `app.js`

**In `js/app.js`**

```js
this.deleteHoliday = ()=>{
  console.log ("I'm going to delete you")
}

```


## Connect the `html` element to the `app.js` functionality
Add an ng-click="deleteHoliday()" to the `td` element that has the `x` in the second `td`

**In `index.html`**

```html
<tr ng-repeat="holiday in ctrl.holidays">
  <td> {{ holiday.name }} Day
  <td ng-click="ctrl.deleteHoliday()">x</td>
</tr>
```

When we click the `x`, we should now get our `console.log` message

## Passing in the id to the `deleteHoliday` function

Our delete route is `holidays/mongo_id`

We need to be able to create that route in order to delete a holiday. Therefore we'll need access to the specific holiday's `_id`

Let's start by passing in that id to our `deleteHoliday` function

**In `index.html`**

```html
<tr ng-repeat="holiday in ctrl.holidays">
  <td> {{ holiday.name }} Day
  <td ng-click="ctrl.deleteHoliday( holiday._id )">x</td>
</tr>
```

**In `js/app.js`**
```js
this.deleteHoliday = ( id )=> {
  console.log ("I'm going to delete you", id)
}
```

![Success in getting the id](https://i.imgur.com/k7zdPcu.png)

## Calling our `$http` function for Delete Route

Since Angular is handling our routes and not the HTML, we don't need npm `method-override` to get to the correct route.

Inside our `this.deleteHoliday` function we're going to call `$http`, we'll pass it an object with the following:
 - `method` : `'DELETE'`
 - `url`    : `/holidays/` + `id`
We can then chain `.then()`, and we'll start by just console.logging the holiday we deleted.



In order to see our change, we'll have to refresh the page

```js
this.deleteHoliday = id => {
  $http({
    method: 'DELETE',
    url: '/holidays/' + id
  }).then (response => {
    console.log(response.data)
  }, error => {
    console.log(error)
  })
}
```

## Updating our Change to Reflect the Change in Our Database

Again, we can take one of two approaches, we can just call `this.getHolidays()` , inside the `.then()` function.

Or we can manipulate our `holidays` array of objects.

Let's go with the latter

We're going to want to find that holiday object by its id and splice it out. In order to splice it, we'll need to know it's index position.

```js
}).then(response => {
  const removeByIndex = this.holidays.findIndex(holiday => holiday._id === id)
  this.holidays.splice(removeByIndex, 1)
}, error => { 
```

- Let's save our file, refresh our browser and test it, let's remove a holiday.

## Takeaways
- Since Angular is handling our routes, we don't have to use method-override
- We can continue to build out the functionality we want, using good old JavaScript


## Onwards
- go to the [angular_update.md](angular_update.md)
