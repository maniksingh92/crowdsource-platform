---
layout: page
title: 2. Understanding the Front End
pos: 2
desc: Know how to develop the frontend of the website, and learn about the data flow in the frontend.
---

<p class="message">
This guide will allow you to learn the data model specifically related to the front end web development. It will give you both the technical overview and its implementation to get an idea about how the javascript frameworks handle data and rendering of the web document.
</p>

>This platform heavily depends upon AngularJS. If you wish to learn it, head [here](http://toddmotto.com/ultimate-guide-to-learning-angular-js-in-one-day/) to get a quick start with it. For more Angular recipes, follow [this](http://fdietz.github.io/recipes-with-angular-js/index.html). The [official tutorial](https://docs.angularjs.org/guide) and [API](https://docs.angularjs.org/api) will further allow you to deepen your understanding. Without an understanding of AngularJS, it will be difficult for you to grasp the following guide.

The front end folder structure is as follows:
{% highlight console %}
.
├── static
|   ├── bower_components
|   ├── css
|   ├── images
|   ├── js
|   └── templates
{% endhighlight %}
* **bower_components** : contains JS libraries (do not have to be touched)
* **css** : contains stylesheets for the html content
* **images** : contains images
* **js** : contains primarily AngularJS scripts and other js, which interact with the html files in the 'templates' folder
* **templates** : contains the html content files

The Angular javascript code inside the 'js' folder is resposible for manipulating how html files will finally output in the browser. Assuming that you have some knowledge of AngularJS, we will proceed with an actual example to show how actually the AngularJS framework works, and how you can leverage it to develop for the front end platform.

We will take the example of the user registration process. You will need to refer to the following files to understand how the process works:
{% highlight console %}
.
├── static
|   ├── js
|   |   ├── crowdsource.routes.js
|   |   ├── authentication
|   |   |   ├── authentication.module.js
|   |   |   ├── services
|   |   |   |   └── authentication.service.js
|   |   |   ├── controllers
|   |   |   |   └── register.controller.js
|   ├── templates
|   |   ├── authentication
|   |   |   └── register.html
{% endhighlight %}
There are 4 javascript files and 1 html file involved with this process. The CSS files, which determine the layout of the web design, have been deliberately not included to preserve simplicity of this guide. The HTML file `register.html` utilizes the AngularJS code in `crowdsource.routes.js`, `authentication.module.js`, `authentication.service.js`, and `register.controller.js`.

If you open up `crowdsource.routes.js`, you will find:
{% highlight js %}
.when('/register', {
      controller: 'RegisterController',
      controllerAs: 'register',
      templateUrl: '/static/templates/authentication/register.html'
    })
{% endhighlight %}
This piece of code defines the routes, or how AngularJS resolves URL requests from front end i.e. `/register` and maps it to the actual path in the backend templateUrl: `/static/templates/authentication/register.html`. So, whenever the user browser points to `http://daemo.stanford.edu/register`, it is actually fetching resources from `http://daemo.stanford.edu/static/templates/authentication/register.html` on the server.

The controller `RegisterController` is a middleman which fetches data from the server, and then modifies and displays it to the view on the user browser. The controller in simple terms is nothing but javascript code, with a little AngularJS syntax in it. We can find the `RegisterController` code inside the file `register.controller.js`.
The `controllerAs: 'register'` key-value pair simply is a way to refer the controller as `register` instead of `RegisterController` inside the HTML code of `register.html` for the sake of brevity. Another important thing to note here is that with the help of routing, whenever the browser sends a URL request for `/register`, AngularJS will automatically allow the controller `RegisterController` to interact with the front end HTML code, only within the file `register.html`. Now that we are clear about how an AngularJS controller is linked with the front end HTML code, let us discuss a HTML snippet from `register.html` before diving into the javascript:
{% highlight html %}
<div layout="row">
  <md-input-container>
    <label>Confirm Password</label>
    <input min="8" type="password" required ng-model="register.password2">
    <span ng-show="form.password2.$error.compareTo && form.password2.$dirty" class="authentication-error">Passwords do not match.</span>
  </md-input-container>
</div>
<div class="authentication-error" layout="row" ng-show="register.error && form.$pristine" ng-bind="register.error"></div>
<md-button ng-click="register.register()" class="md-raised">Sign Up</md-button>
{% endhighlight %}
There are 4 different types of AngularJS components in the above HTML code:

* ng-model="register.password2"
* ng-show="form.password2.$error.compareTo && form.password2.$dirty"
* ng-bind="register.error"
* ng-click="register.register()"

The `ng-model` directive links its HTML element to an AngularJS element from the controller, in this case the value of the input is directly stored inside the variable 'password2' of the Angular controller. `ng-show` is a directive which decides whether the HTML element will be visible in the browser view or not, depending on the value of the expression passed to it from the controller. `ng-bind` is an Angular directive that replaces the text content of the specified HTML element with the value of a given expression; it updates the text content as the expression value changes. `ng-click` directive performs the behavior specified in its expression whenever the HTML element, in this case the 'Sign Up' button, is clicked by the user.

Once a user has correctly input all details for registration and clicks the 'Sign Up' button, the function 'register()' from the controller `RegisterController` in `register.controller.js` gets executed:
{% highlight js %}
angular
        .module('crowdsource.authentication.controllers')
        .controller('RegisterController', ['$location', '$scope', 'Authentication', 'cfpLoadingBar',
            function RegisterController($location, $scope, Authentication, cfpLoadingBar) {
                activate();
                function activate() {
                    if (Authentication.isAuthenticated()) {
                        $location.url('/home');
                    }
                }
                var vm = this;
                vm.register = register;
                vm.errors = [];
                function register() {
                    cfpLoadingBar.start();
                    Authentication.register(vm.email, vm.firstname, vm.lastname,
                        vm.password1, vm.password2)
{% endhighlight %}
>We can clearly see that our controller `RegisterController` has two custom dependencies `Authentication` and `cfpLoadingBar`. `cfpLoadingBar` is just an AngularJS code which displays a loading bar, and we will not discuss much about it. `Authentication` contains AngularJS code which we will talk about later.

Whenever the `register.html` page is loaded in the browser, the function `activate()` is executed. Upon inspecting the code for the function, we can see that it executes `Authentication.isAuthenticated()` to redirect the browser to the home page if an already logged-in user tries to access the `register.html` page. We will discuss about the code of the `isAuthenticated()` function when we talk about the dependency `Authentication`. If a new user is trying to register and clicks the 'Sign Up' button from `register.html`, the `ng-click` directive on the button executes the function `register()`. Upon inspecting the code, we can see that this function calls another function `Authentication.register(vm.email, vm.firstname, vm.lastname, vm.password, vm.password2)` from the `Authentication` dependency. If you have been observing closely, the parameters 'vm.email, vm.firstname, vm.lastname, vm.password, vm.password2' are actually the values from the HTML form of `register.html` binded with the ng-model 'register.email, register.firstname, register.lastname, register.password, register.password2' respectively. There is more code succeeding the above snippet inside `register.controller.js` related to error handling, but for simplicity we will assume that there are no errors in the process and dive into the code of the dependency `Authentication` in `authentication.service.js` :
{% highlight js %}
function register(email, firstname, lastname, password1, password2) {
  return $http({
    url: '/api/user/',
    method: 'POST',
    data: {
      email: email,
      first_name: firstname,
      last_name: lastname,
      password1: password1,
      password2: password2
    }
  });
}

...
...
...

function isAuthenticated() {
  return !!$cookies.get('authenticatedAccount');
}
{% endhighlight %}
The dependency `Authentication` is a factory of AngularJS. Here, we can see that the function `isAuthenticated()` simply checks whether a browser cookie 'authenticatedAccount' is set, and returns its value. If you scroll up to the code in `register.controller.js`, you can now understand that if `isAuthenticated()` returns a 'TRUE' value, the browser is redirected to the home page since a user is already logged-in. The function `register(email, firstname, lastname, password1, password2)` is the one which we called from the controller `RegisterController` in `register.controller.js` as `Authentication.register(vm.email, vm.firstname, vm.lastname, vm.password, vm.password2)`. This function sends the user details data like email, first name, last name, password to the server via the POST method to the backend API url '/api/user/'. The server processes this data, attempts to register the user details, generates a response regarding the success of this attempt, and returns the response back to the browser so that AngularJS can use it. If you remember the error handling code which we skipped from `register.controller.js`, then that code deals with this response from the server; and if there are no errors with the registration, the browser is redirected to the login page.

>This was a basic guide of how the registration process works. With similar analogy, you can identify the front end data flow for other processes in the platform.

Also, we left out the code from `authentication.module.js`, so let's understand it quickly:
{% highlight js %}
angular
  .module('crowdsource.authentication', [
    'crowdsource.authentication.controllers',
    'crowdsource.authentication.services'
  ]);

angular
  .module('crowdsource.authentication.controllers', []);

angular
  .module('crowdsource.authentication.services', ['ngCookies']);
{% endhighlight %}
This code defines a parent module 'crowdsource.authentication' and declares dependencies on two other modules: 'crowdsource.authentication.controllers' from `register.controller.js`, and 'crowdsource.authentication.services' from `authentication.service.js`. In simple words, it glues together the controller and services under one module.