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

