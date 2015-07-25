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

We will take the example of the authentication process. You will need to refer to the following files to understand how the process works:
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
|   |   |   |   ├── register.controller.js
|   |   |   |   └── login.controller.js
|   ├── templates
|   |   ├── authentication
|   |   |   ├── register.html
|   |   |   └── login.html
{% endhighlight %}
There are 5 javascript files and 2 html files involved with this process. The CSS files, which determine the layout of the web design, have been deliberately not included to preserve simplicity of this guide. The HTML files: `register.html` and `login.html` utilize the AngularJS code in `crowdsource.routes.js`, `authentication.module.js`, `authentication.service.js`, `register.controller.js` and `login.controller.js`.

If you open up `crowdsource.routes.js`, you will find:
{% highlight js %}
.when('/register', {
      controller: 'RegisterController',
      controllerAs: 'register',
      templateUrl: '/static/templates/authentication/register.html'
    })

    .when('/login', {
      controller: 'LoginController',
      controllerAs: 'login',
      templateUrl: '/static/templates/authentication/login.html'
    })
{% endhighlight %}
This piece of code defines the routes, or how AngularJS resolves URL requests from front end i.e. `/register` and maps it to the actual path in the backend templateUrl: `/static/templates/authentication/register.html`. So, whenever the user browser points to `http://daemo.stanford.edu/register`, it is actually fetching resources from `http://daemo.stanford.edu/static/templates/authentication/register.html` on the server.

The controller `RegisterController` is a middleman which fetches data from the server, and then modifies and displays it to the view on the user browser. The controller in simple terms is nothing but javascript code, with a little AngularJS syntax in it. We can find the `RegisterController` code inside the file `register.controller.js`.
The controllerAs `register` key-value pair simply is a way to refer the controller as `register` instead of `RegisterController` inside the HTML code of `register.html` for brevity. Another important thing to note here is that with the help of routing, whenever the browser sends a URL request for `/register`, AngularJS will automatically allow the controller `RegisterController` to interact with the front end HTML code, only within the file `register.html`. Now that we are clear about how an AngularJS controller is linked with the front end HTML code, let us discuss a HTML snippet from `register.html` before diving into the javascript:
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
