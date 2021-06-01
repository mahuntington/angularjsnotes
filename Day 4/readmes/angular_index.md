
## Lesson Objectives

1. Completed `angular_create.md`
1. Create a function to get all holidays
1. On page load, show all holidays
1. After creating a new holiday, refresh list of holidays



## Create a function to get all holidays

- We'll need to make a new request to get our list of all of our holidays
- Again, we'll make a new function that calls the `$http()` function
- We'll pass the `$http()` function an object with the following
  - `method` : `GET`
  - `url`    : `/holdays`
Unlike our `POST` request, we don't need to include `data` because we're not sending any data for this get request.

- Our `$http()` function again, will have a `.then()` function chained to it, that will wait for the response from the `http` request and then give us access to that data



**In `js/app.js`**

```javascript
this.getHolidays = () => {
  $http({
    method: 'GET',
    url: '/holidays'
  }).then(response => {
    this.holidays = response.data
  }, error => {
    console.log(error)
  })
}
```

If we make our changes and reload the page, we shouldn't see anything happen, no `console.log` of our data.

That's because we've only defined our function and not called it.

## Call the `getHolidays` function

We were able to call `createHoliday` because clicking on the `create new holiday` button listened for a click and then triggered this function.

In this case, we just want to immediately load our data on page load. To do this, we just invoke the function after the function has been defined.

```js
this.getHolidays = () => {
  $http({
    method: 'GET',
    url: '/holidays'
  }).then(response => {
    console.log(response.data)
  }, error => {
    console.log(error)
  })
}

// load immediately on page
this.getHolidays()
```

Ok, so we are able to `console.log` on page load, which is cool, but we'd really like to update the browser view.

![`console.log` data on page load](https://i.imgur.com/Zw9eblX.png)

## Render our Holidays in the Browser

With Angular's two-way data binding, we're going to have to declare a variable that is going to hold our data both in `index.html` and `app.js`


- both the `index.html` and the `app.js` will need to have this variable
- let's name our variable `holidays`

**In `index.html`**
- in our `index.html` the `holidays` variable will be accessible by adding `ctrl.` in front of it. We decided that it should be `ctrl` in our `body` tag right at the `ng-controller="MainController as ctrl"`
- so any time we want to access it in the html we need to follow two rules
 - We are accessing it in a tag that is nested **inside** the `body` tag.
 - We are putting `ctrl.` in front of it
 - therefore our variable in `index.html` is `ctrl.holidays`


**In `js/app.js`**
- We can give our `index.html` access to our `holidays` variable by doing the following
 - making sure we are in the correct controller. In our case, we just have one main controller: `MainController`, so we have to make sure we are accessing our variable inside of it
 - put `this.` in front of our variable, so our variable inside `app.js` inside the `MainController` is `this.holidays`

**Recap**
 - It HAS to be `holidays` in both the `index.html` and `app.js`
 - in `index.html` it is referenced by `ctrl.holidays`
 - in `app.js` it is referenced by `this.holidays`
 - changing the value in one 'place' will update the value in the other


- First we are going to set our response data to `this.holidays`

 **In `js/app.js`**

```javascript
this.getHolidays = () => {
  $http({
    method: 'GET',
    url: '/holidays'
  }).then(response => {
    this.holidays = response.data
  }, error => {
    console.log(error)
  })
}
```

- Next let's use `ng-repeat` to display our holiday names inside a table cell `td` tag
- `ctrl.holidays` is an array of objects
- we have to set a variable name for one holiday object, we do that with angular by writing `holiday in ctrl.holidays` - that sets a variable name of `holiday` for each object in the `holidays` array
- For now, all we want to do is see the holiday name. So we'll have to access that property out of our holiday objects by doing `holiday.name`

**In `index.html`**

```html
  </form><!-- closes our create form -->
  <table>
    <tr ng-repeat="holiday in ctrl.holidays">
      <td> {{ holiday.name }} Day</td>
    </tr>
  <table>
</div><!-- closes div with class container -->

```

Success looks like this
![index route displaying in browser](https://i.imgur.com/bJ3snQn.png)

## Update the List of Holidays When a New Holiday is Created

Let's add a new holiday
- We know it works because we are still `console.log`ging the response
![create holiday response](https://i.imgur.com/gru337F.png)
- But we don't see our page update it
- Let's refresh our page, ok we see it now, but that's not great user experience for a user to have to know to refresh the page to see their change.

- What we really want is that upon a successful response from the database, for the page to update accordingly

- We can approach it in two ways

- In the first one, we just call `getHolidays()` again.

Let's try it, add `this.getHolidays();` inside the response of the `$http` function inside `this.createHoliday()`

```js
this.createHoliday = () => {
  $http({
    method: 'POST',
    url: '/holidays',
    data: this.createForm
  }).then(response => {
    this.getHolidays()
  }, error => {
    console.log(error)
  })
}
```

Don't forget to save your file and refresh your browser

The nice thing about doing it this way is that it is super easy, just one line of code.
The downside is that it is another call to the database. For a small app that not many people use, it's not really a problem at all.

However, if you wanted to update the page without making a call to the database, you can do so by manipulating the holidays array.

Let's try it:
```js
this.createHoliday = () => {
  $http({
    method: 'POST',
    url: '/holidays',
    data: this.createForm
  }).then(response => {
  // let's use unshift to put our latest holiday at the top of our list, rather than push which would add it to the bottom
    this.holidays.unshift(response.data)
  }, error => {
    console.log(error)
  })
}
```

You can add as much functionality as you like inside the `.then()` function. Another really nice to have feature, is to empty out the form after creating a new holiday.

We can do that by setting `this.createForm` to an empty object

We can also have our new holiday appear at the top of our list by using `.unshift` instead of push

```js
this.createHoliday = () => {
  // console.log('submit button calls this function');
  $http({
    method: 'POST',
    url: '/holidays',
    data: this.createForm
  }).then(response => {
    this.holidays.unshift(response.data)
    this.createForm = {}
  }, error => {
    console.log(error)
  })
}
```

![End of create lesson app appearance](https://i.imgur.com/Il5Ksqf.png)

## Take aways
Even though Angular has some very specific rules and formatting, `app.js` is still Javascript and we can do as much or as little JavaScript as we like.

## Onwards
- go to the [angular_delete.md](angular_delete.md)
