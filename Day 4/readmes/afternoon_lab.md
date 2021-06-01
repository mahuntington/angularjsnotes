# Lab - Afternoon

Keep Building the Holiday App

## Warm Up


- Add a `td` element to the table that shows the number of likes
- Add a `td` element to the table that has an image of balloons, when you click it, the number of likes go up
  - In the assets folder is an image `two-balloon-icons-68911.png`

Hints:
- Create a new function with an `$http` call that specifically updates just the likes

![likes](https://i.imgur.com/mjIMg4N.png)


## More Challenging


- Add a `td` element to the table that has an image of a pencil, when you click it, a modal will appear with a form to edit
  - In the assets folder is an image `simpleiconDOTcom-pen-15-64x64.png`

![possible appearance of modal](https://i.imgur.com/ASwYY9W.png)

**Hints**
- Starter Form - this should give you a nice layout that plays well with the CSS already in place. Note when the class `edit` is present it shows the modal, when the class `edit` is removed the modal disappears.

- There is also another css class `.dim` which when added to the `body`, will dim the apperance of the `body`, which you can also toggle with angular `ng-class` - if you go this route it is recommended you set a variable to true/false and create a function that allows you to toggle this value.


```html
<div class="modal edit">
  <form>
    <div class="row">
        <label for="name">Name</label>
        <input type="text" placeholder="" id="name">
        <label for="celebrated">Celebrated</label>
        <input type="text" placeholder=""  id="celebrated">
        <label for="likes">Likes</label>
        <input type="number" placeholder="" id="likes">
        <label for="description">Description</label>
          <textarea class="u-full-width" placeholder="" id="description"></textarea>
          <input type="submit" value="Update Holiday" class="button-primary">
          <button class="button-red"> Don't Update </button>
      </div>
  </form>
</div>

```

- One way to solve it is to have, on click, the ability to grab the holiday object being clicked and store it in a global variable for reuse in other functions

- Review the create form  the edit route and show one functionality we created and mix and match the functionality you need



## Ultra
- Add date to the schema. Use the JavaScript date object, mongoose Date, and moment.js to enter dates properly and also to format them that displays them in a user friendly way.
