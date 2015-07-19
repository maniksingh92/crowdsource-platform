---
layout: page
title: 1. Getting Started
pos: 1
desc: First time setting up of the code on your computer.
---

<p class="message">
This guide will allow you to run a local copy of the {{ site.title }} on your computer. It will also teach you the necessary git commands to upload/fetch the code from GitHub and keep your local copy updated with the <a href="{{ site.github.repo }}">main GitHub repository</a>.
</p>

### Dependencies


The following tools are required in order to run the web server. It is advised, to web search how to install the tools through an executable installer or through your package manager, to avoid minimum issues with the setup. The given download links are only for your easy reference, and may not provide the advised method as mentioned above. These pre-requirements are written for UNIX-like machines. For Windows users, there is an easier setup method available [here](https://github.com/crowdresearch/crowdsource-platform#setup-with-vagrant).

* [git](https://git-scm.com/downloads)
* [postgres](http://www.enterprisedb.com/products-services-training/pgdownload)
* [python 2.7](https://www.python.org/downloads/release/python-2710/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/installation.html)
* [Node.js](https://nodejs.org/download/)


### Setup

#### 1. Setting up the code on your machine with Git

Create an account on [GitHub](https://github.com/) if you haven't already. This is where the code repository is present online. Once logged in, go to the main official online [repository]({{ site.github.repo }}) and click the fork button. 'Forking' will create an online copy of the repository of the main code, under your GitHub account. This way, you can tinker with your copy of the code without breaking the official code. You should be redirected to a URL similar to `https://github.com/<github-user-name>/crowdsource-platform`. Next, we will download this copy on your machine so that you can actual modify the code. In order to do that, open up your console and navigate to a preferred directory. The following command will download the code locally in a sub-directory 'crowdsource-platform':
{% highlight console %}
$ git clone https://github.com/<github-user-name>/crowdsource-platform
{% endhighlight %}
However, you must remember that this is a local copy from YOUR online repository and does not keep itself updated with any independent changes in the main official online repository, as of this step. In order to allow it to read/download changes from the main official online repository, we need to add an 'upstream' which is basically a link which allows git to communicate with the main official repository:
{% highlight console %}
$ cd crowdsource-platform
$ git remote add upstream https://github.com/crowdresearch/crowdsource-platform.git
{% endhighlight %}
Now our local repository has the potential to fetch any changes made in the main official online repository by other contributors. The default branch being worked upon is called 'develop2'. You can check your current branch with:
{% highlight console %}
$ git branch
{% endhighlight %}
Since 'develop2' is the main branch, we would like to keep it as stable as possible and only introduce new code into it once we're sure that our code changes are final. To maintain this stability in your local copy, it will be advisable to branch out and work on a copy of 'develop2':
{% highlight console %}
$ git checkout develop2
$ git checkout -b your-branch-name
{% endhighlight %}
Now you can work on the branch 'your-branch-name' and be assured that the original branch 'develop2' is untouched on your local machine. You can branch out as many times as you like from 'develop2' with the above two steps, and switch between different branches with:
{% highlight console %}
$ git checkout any-branch-name
{% endhighlight %}
Always remember to name a branch out according to the feature you wish to implement, rather than your name, to make the purpose of the branch clear for you and other developers. At this point you are safe to make any changes you want to the code in these new branches.

Before committing any large code changes, make sure to keep your 'develop2' branch updated with the latest changes from the main online repository. In order to do so, follow these commands:
{% highlight console %}
$ git fetch upstream
$ git checkout develop2
$ git merge upstream/develop2
{% endhighlight %}
The above commands are self explanatory. You download changes from the online main repository, checkout into the 'develop2' branch and merge the downloaded changes into it. However, a specific feature branch you created is still with the old code. In order to update it with the downloaded changes, follow these commands:
{% highlight console %}
$ git checkout feature-branch-name 
$ git merge develop2
{% endhighlight %}
After you think that your feature branch is ready, you can upload it to YOUR online repository. You can execute `git status` in your console to review your changes. You can manually choose files to stage by:
{% highlight console %}
$ git add file1 file2 path/to/file3
{% endhighlight %}
or stage all the changed files with:
{% highlight console %}
$ git add .
{% endhighlight %}
Finally, commit this stage with:
{% highlight console %}
$ git commit -m "The commit message describing the change"
{% endhighlight %}
Now, the changes have been finalised and can be uploaded to your online repository with:
{% highlight console %}
$ git push origin feature-branch-name
{% endhighlight %}
Follow the prompt in the console and once the upload is successful you can view these changes online at `https://github.com/<github-user-name>/crowdsource-platform`.

#### 2. Setting Up a Local Server
First we will set up the postgreSQL database. Make sure to create the user exactly as your computer's user account name. Execute the following step by step in your console:
{% highlight console %}
$ sudo -i -u postgres
$ initdb --locale en_US.UTF-8 -E UTF8 -D '/var/lib/postgres/data'
$ createuser --interactive
$ psql
CREATE DATABASE crowdsource_dev ENCODING 'UTF8';
\q
$ exit
{% endhighlight %}
Create a `local_settings.py` file in the project root folder and configure it to connect to the Postgres database:
{% highlight python %}
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.postgresql_psycopg2",
        "NAME": "crowdsource_dev"
    }
}
{% endhighlight %}

While inside your project root folder, execute:
{% highlight console %}
$ sudo npm install -g bower
$ bower install
$ virtualenv venv -p python2
$ source venv/bin/activate
$ pip install -r requirements.txt
$ python manage.py makemigrations
$ python manage.py migrate
{% endhighlight %}
Load data into the database so that we have an initial data to work with:
{% highlight console %}
$ python manage.py loaddata fixtures/neilCrowdsourcingRankingData.consoleon
{% endhighlight %}
If there are no errors, you are ready to run the app from your local server:
{% highlight console %}
$ source venv/bin/activate //if you have not sourced into the virtual environment
$ python manage.py runserver
{% endhighlight %}

Congratulations on successfully running your server.