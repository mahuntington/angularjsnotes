# MEAN Stack Auth

## Create toggle to show app vs login/signup

`index.html`:

```html
<section ng-if="ctrl.loggedInUser === false">
    <h2>Sign Up</h2>
    <form>
        Username: <input type="text" /><br/>
        Password: <input type="password" /><br/>
        <input type="submit" value="Sign Up">
    </form>
    <h2>Log In</h2>
    <form>
        Username: <input type="text" /><br/>
        Password: <input type="password" /><br/>
        <input type="submit" value="Log In">
    </form>
</section>
<section ng-if="ctrl.loggedInUser !== false">
    <!-- previous code: ul and form -->
</section>
```

`app.js` inside controller:

```javascript
this.loggedInUser = false;
```

## Make fake signup/login forms call angular methods

`app.js`:

```javascript
this.signup = function(){
    this.loggedInUser = {
        username: 'Matthew'
    }
}

this.login = function(){
    this.loggedInUser = {
        username: 'matt'
    }
}
```

`index.html`:

```html
<section ng-if="ctrl.loggedInUser === false">
    <form ng-submit="ctrl.signup()"><!-- add ng-submit -->
        Username: <input type="text" /><br/>
        Password: <input type="password" /><br/>
        <input type="submit" value="Sign Up">
    </form>
    <h2>Log In</h2>
    <form ng-submit="ctrl.login()"><!-- add ng-submit -->
        Username: <input type="text" /><br/>
        Password: <input type="password" /><br/>
        <input type="submit" value="Log In">
    </form>
</section>
<section ng-if="ctrl.loggedInUser !== false">
    <h2>Welcome {{ctrl.loggedInUser.username}}</h2>
    <!-- rest of code -->
</section>
```

## Create user create route for api:

create `controllers/users.js`:

```javascript
const express = require('express');
const router = express.Router();

router.post('/', (req, res) => {
    res.json(req.body);
});

module.exports = router;
```

`server.js`:

```javascript
const usersController = require('./controllers/users.js');
app.use('/users', usersController);
```

## Create User model

create `models/users.js`:

```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    username: String,
    password: String
});

const User = mongoose.model('User', userSchema);

module.exports = User;
```

`controllers/users.js`:

```javascript
const User = require('../models/users.js');

router.post('/', (req, res) => {
    User.create(req.body, (error, createdUser) => {
        res.json(createdUser);
    })
});
```

## Add bcrypt to sign up

```
npm install bcrypt
```

`controllers/users.js`:

```javascript
const bcrypt = require('bcrypt');
//...
router.post('/', (req, res) => {
    req.body.password = bcrypt.hashSync(req.body.password, bcrypt.genSaltSync(10));
    User.create(req.body, (error, createdUser) => {
        res.json(createdUser);
    })
});
```

## Make request to create user api route in angular

`index.html`:

```html
Username: <input type="text" ng-model="ctrl.signupUsername" /><br/>
Password: <input type="password" ng-model="ctrl.signupPassword"/><br/>
```

`app.js`:

```javascript
this.signup = function(){
    $http({
        url:'/users',
        method:'POST',
        data: {
            username: this.signupUsername,
            password: this.signupPassword
        }
    }).then(function(response){
        controller.loggedInUser = response.data;
    })
}
```

## Create session create route

create `controllers/session.js`:

```javascript
const express = require('express');
const router = express.Router();
const User = require('../models/users.js');

router.post('/', (req, res) => {
    User.findOne({username:req.body.username}, (error, foundUser) => {
        res.json(foundUser)
    });
});

module.exports = router;
```

`server.js`:

```javascript
const sessionController = require('./controllers/session.js');
app.use('/session', sessionController);
```

## Check password

`controllers/session.js`:

```javascript
const bcrypt = require('bcrypt');
//...
router.post('/', (req, res) => {
    User.findOne({username:req.body.username}, (error, foundUser) => {
        if(foundUser === null){
            res.json({
                message:'user not found',
            });
        } else {
            const doesPasswordMatch = bcrypt.compareSync(req.body.password, foundUser.password);
            if(doesPasswordMatch){
                res.json(foundUser)
            } else {
                res.json({
                    message:'user not found'
                });
            }
        }
    });
});
```

## Integrate with Angular

```html
<h2>Log In</h2>
<form ng-submit="ctrl.login()">
    Username: <input type="text" ng-model="ctrl.loginUsername"/><br/>
    Password: <input type="password" ng-model="ctrl.loginPassword"/><br/>
    <input type="submit" value="Log In">
</form>
```

```javascript
this.login = function(){
    $http({
        url:'/session',
        method:'POST',
        data: {
            username: this.loginUsername,
            password: this.loginPassword
        }
    }).then(function(response){
        if(response.data.username){
            controller.loggedInUser = response.data;
        } else {
            controller.loginUsername = null;
            controller.loginPassword = null;
        }
    })
}
```

## Set up sessions

```
npm install express-session
```

`app.js`:

```javascript
const session = require('express-session');
//...
app.use(session({
    secret:'feedmeseymour',
    resave:false,
    saveUninitialized:false
}))
```


## Set session on login

`controllers/session.js`:

```javascript
const doesPasswordMatch = bcrypt.compareSync(req.body.password, foundUser.password);
if(doesPasswordMatch){
    req.session.user = foundUser; //add this line
    res.json(foundUser)
} else {
    res.json({
        message:'user not found'
    });
}
```

## Set session on sign up

`controllers/users.js`:

```javascript
User.create(req.body, (error, createdUser) => {
    req.session.user = createdUser; //add this line
    res.json(createdUser);
})
```

## Test to see if user is logged in on page load

`app.js`:

```javascript
// bottom of controller
this.getTodos();

$http({
    method:'GET',
    url:'/session'
}).then(function(response){
    console.log(response);
});
```

`controllers/session.js`:

```javascript
router.get('/', (req, res) => {
    res.json(req.session.user);
})
```

## Set session data in angular

`app.js`:

```javascript
$http({
    method:'GET',
    url:'/session'
}).then(function(response){
    if(response.data.username){
        controller.loggedInUser = response.data;
    }
});
```

## Log out functionality

`controllers/session.js`:

```javascript
router.delete('/', (req, res) => {
    req.session.destroy(() => {
        res.json({
            destroyed:true
        });
    })
});
```

`index.html` (after welcome `h2`):

```html
<button ng-click="ctrl.logout()">Log Out</button>
```

`app.js`:

```javascript
this.logout = function(){
    $http({
        url:'/session',
        method:'DELETE'
    }).then(function(){
        controller.loggedInUser = false;
    })
}
```
