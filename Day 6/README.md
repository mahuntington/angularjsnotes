# AngularJS Potpourri

Our last day of AngularJS will go over
- Filters (in custom_directives)
- Includes (in custom_directives)

The above two are the two lessons that will likely be the most useful for student projects.

Rather than learn technologies and try to build an app that shoehorns in everything you've learned, a better approach is to figure out what you want to build, apply your fundamental skills and then teach yourself what you need to know in order for your project to function.

**Brief Summary of other topics/notes:**

- **Custom Directives:** you have `ng-click`, `ng-repeat` and a lot of directives available to you, but perhaps you are writing a lot of repetitive code that doesn't quite have a directive that meets your specific needs? You can write your own!

- **Dependencies:** Is your app getting massive? Are there many parts to it? Break it down into modules and add them in as dependencies

- **Routing:** wish you could use the forward and back buttons on your browser? Wish you could send someone a link to a specific Angular view? Make these wishes come true but using AngularJS routing

- **Scope:** Did you end up with multiple controllers? Do they need to share information/pass it around? Check out AngularJS's scope

- **Watch/Apply:** AngularJS is listening for events that you've added. Once you've clicked on something with an event listener, AngularJS will go through and update all the values needed (AngularJS digest loop). But perhaps you need to _watch_ for another event and _apply_ changes. (A simple example is a stopwatch, you want the value to increase automatically, without waiting for someone to keep clicking something/typing on the keyboard etc). This might be what you need to get it done

- **Events:** More than one controller? Do these controllers need to be aware of certain actions happening in the other controller(s)? Check out Events, to see if this can solve the problems you are looking for

**Final Caveat**
- AngularJS is great! However, it grew into something much bigger and solving many more problems that ever was intended. Therefore some of these more advanced things can be cumbersome to work with. Newer frameworks like the new Angular, React, Vue etc. took into consideration a lot of the problems that AngularJS wasn't able to solve elegantly as a first generation framework. So if you find yourself frustrated applying any of the above, don't assume every framework will be just as frustrating with their solutions to things like routing.
