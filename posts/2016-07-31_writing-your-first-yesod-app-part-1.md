---
layout: post
title: Writing your first Yesod app part 1
description: Rewriting Yesod's wonderful polls tutorial in Haskell
category: 
tags: [Haskell]
---


Django's polls tutorial is awesome, this write-up aims at being exactly the same except using Haskell/Yesod instead of Yesod/Python. Starting
now, I'll copy Django's polls tutorial exactly except anywhere it might not make sense.

------

# Writing your first Yesod app, part 1

Let’s learn by example.

Throughout this tutorial, we’ll walk you through the creation of a basic poll application.

It’ll consist of two parts:

    A public site that lets people view polls and vote in them.
    An admin site that lets you add, change, and delete polls.

We’ll assume you have installed Yesod and yesod-bin using stack already. You can tell Yesod is installed and which version by running the following command:

```shell
$ stack exec yesod
```

If Yesod is installed, you should see:

TODO enter what user should see 

If it isn’t, you’ll get an error telling “TODO enter error”.

This tutorial is written using Stackage resolver 6.9 using [these Yesod/yesod-bin install instructions](TODO enter link to install instructions or #link to instructions on page).

Where to get help:

If you’re having trouble going through this tutorial, please post a message to beginners@haskell.org or drop by #haskell on irc.freenode.net to chat with other Yesod users who might be able to help.

## Creating a project

If this is your first time using Yesod, you’ll have to take care of some initial setup. Namely, you’ll need to auto-generate some code that establishes a Yesod project – a collection of settings for an instance of Yesod, including database configuration, Yesod-specific options and application-specific settings.

From the command line, cd into a directory where you’d like to store your code, then run the following command:

```shell
$ stack new mysite yesod-mysql
```

This will create a mysite directory in your current directory.

Let’s look at what startproject created:

```
├── app (the folder containing the entry point to the application and some live coding esque tools)
│   ├── devel.hs
│   ├── DevelMain.hs
│   └── main.hs (entry point)
├── Application.hs (Init, shutdown, db queries, ties together application plumbing)
├── config (routes, models, configuration files such as database settings, test settings)
│   ├── favicon.ico
│   ├── keter.yml
│   ├── models
│   ├── robots.txt
│   ├── routes
│   ├── settings.yml
│   └── test-settings.yml
├── Foundation.hs (Application instances, "Foundation" data type)
├── Handler (Where you'll put your handler's for urls)
│   ├── Comment.hs
│   ├── Common.hs
│   └── Home.hs
├── Import (referenced by Import.hs)
│   └── NoFoundation.hs
├── Import.hs (re-export useful libraries)
├── Model.hs (code to initialize and migrate models from config/models)
├── mysite.cabal
├── Settings  (self-explanatory)
│   └── StaticFiles.hs
├── Settings.hs (Application settings. If you want a custom project structure, modify this file.)
├── stack.yaml
├── static (static files)
│   ├── css
│   │   └── bootstrap.css
│   └── fonts
│       ├── glyphicons-halflings-regular.eot
│       ├── glyphicons-halflings-regular.svg
│       ├── glyphicons-halflings-regular.ttf
│       └── glyphicons-halflings-regular.woff
├── templates (Where you put templates referenced in your handlers)
│   ├── default-layout.hamlet
│   ├── default-layout-wrapper.hamlet
│   ├── homepage.hamlet
│   ├── homepage.julius
│   └── homepage.lucius
└── test (self-explanatory)
    ├── Handler
    │   ├── CommentSpec.hs
    │   ├── CommonSpec.hs
    │   └── HomeSpec.hs
    ├── Spec.hs
    └── TestImport.hs
```

The development server

Let’s verify your Yesod project works. Change into the outer mysite directory, if you haven’t already, and run the following commands:


```
$ stack exec yesod devel
```

You’ll see the following output on the command line:

TODO paste output



    WIP do not publish



Note

Ignore the warning about unapplied database migrations for now; we’ll deal with the database shortly.

You’ve started the Yesod development server, a lightweight Web server written purely in Python. We’ve included this with Yesod so you can develop things rapidly, without having to deal with configuring a production server – such as Apache – until you’re ready for production.

Now’s a good time to note: don’t use this server in anything resembling a production environment. It’s intended only for use while developing. (We’re in the business of making Web frameworks, not Web servers.)

Now that the server’s running, visit http://127.0.0.1:8000/ with your Web browser. You’ll see a “Welcome to Yesod” page, in pleasant, light-blue pastel. It worked!

Changing the port

By default, the runserver command starts the development server on the internal IP at port 8000.

If you want to change the server’s port, pass it as a command-line argument. For instance, this command starts the server on port 8080:

$ python manage.py runserver 8080

If you want to change the server’s IP, pass it along with the port. So to listen on all public IPs (useful if you want to show off your work on other computers on your network), use:

$ python manage.py runserver 0.0.0.0:8000

Full docs for the development server can be found in the runserver reference.

Automatic reloading of runserver

The development server automatically reloads Python code for each request as needed. You don’t need to restart the server for code changes to take effect. However, some actions like adding files don’t trigger a restart, so you’ll have to restart the server in these cases.
Creating the Polls app¶

Now that your environment – a “project” – is set up, you’re set to start doing work.

Each application you write in Yesod consists of a Python package that follows a certain convention. Yesod comes with a utility that automatically generates the basic directory structure of an app, so you can focus on writing code rather than creating directories.

Projects vs. apps

What’s the difference between a project and an app? An app is a Web application that does something – e.g., a Weblog system, a database of public records or a simple poll app. A project is a collection of configuration and apps for a particular website. A project can contain multiple apps. An app can be in multiple projects.

Your apps can live anywhere on your Python path. In this tutorial, we’ll create our poll app right next to your manage.py file so that it can be imported as its own top-level module, rather than a submodule of mysite.

To create your app, make sure you’re in the same directory as manage.py and type this command:

$ python manage.py startapp polls

That’ll create a directory polls, which is laid out like this:

polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py

This directory structure will house the poll application.
Write your first view¶

Let’s write the first view. Open the file polls/views.py and put the following Python code in it:
polls/views.py

from django.http import HttpResponse


def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")

This is the simplest view possible in Yesod. To call the view, we need to map it to a URL - and for this we need a URLconf.

To create a URLconf in the polls directory, create a file called urls.py. Your app directory should now look like:

polls/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    urls.py
    views.py

In the polls/urls.py file include the following code:
polls/urls.py

from django.conf.urls import url

from . import views

urlpatterns = [
    url(r'^$', views.index, name='index'),
]

The next step is to point the root URLconf at the polls.urls module. In mysite/urls.py, add an import for django.conf.urls.include and insert an include() in the urlpatterns list, so you have:
mysite/urls.py

from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
]

The include() function allows referencing other URLconfs. Note that the regular expressions for the include() function doesn’t have a $ (end-of-string match character) but rather a trailing slash. Whenever Yesod encounters include(), it chops off whatever part of the URL matched up to that point and sends the remaining string to the included URLconf for further processing.

The idea behind include() is to make it easy to plug-and-play URLs. Since polls are in their own URLconf (polls/urls.py), they can be placed under “/polls/”, or under “/fun_polls/”, or under “/content/polls/”, or any other path root, and the app will still work.

When to use include()

You should always use include() when you include other URL patterns. admin.site.urls is the only exception to this.

Doesn’t match what you see?

If you’re seeing include(admin.site.urls) instead of just admin.site.urls, you’re probably using a version of Yesod that doesn’t match this tutorial version. You’ll want to either switch to the older tutorial or the newer Yesod version.

You have now wired an index view into the URLconf. Lets verify it’s working, run the following command:

$ python manage.py runserver

Go to http://localhost:8000/polls/ in your browser, and you should see the text “Hello, world. You’re at the polls index.”, which you defined in the index view.

The url() function is passed four arguments, two required: regex and view, and two optional: kwargs, and name. At this point, it’s worth reviewing what these arguments are for.
url() argument: regex¶

The term “regex” is a commonly used short form meaning “regular expression”, which is a syntax for matching patterns in strings, or in this case, url patterns. Yesod starts at the first regular expression and makes its way down the list, comparing the requested URL against each regular expression until it finds one that matches.

Note that these regular expressions do not search GET and POST parameters, or the domain name. For example, in a request to https://www.example.com/myapp/, the URLconf will look for myapp/. In a request to https://www.example.com/myapp/?page=3, the URLconf will also look for myapp/.

If you need help with regular expressions, see Wikipedia’s entry and the documentation of the re module. Also, the O’Reilly book “Mastering Regular Expressions” by Jeffrey Friedl is fantastic. In practice, however, you don’t need to be an expert on regular expressions, as you really only need to know how to capture simple patterns. In fact, complex regexes can have poor lookup performance, so you probably shouldn’t rely on the full power of regexes.

Finally, a performance note: these regular expressions are compiled the first time the URLconf module is loaded. They’re super fast (as long as the lookups aren’t too complex as noted above).
url() argument: view¶

When Yesod finds a regular expression match, Yesod calls the specified view function, with an HttpRequest object as the first argument and any “captured” values from the regular expression as other arguments. If the regex uses simple captures, values are passed as positional arguments; if it uses named captures, values are passed as keyword arguments. We’ll give an example of this in a bit.
url() argument: kwargs¶

Arbitrary keyword arguments can be passed in a dictionary to the target view. We aren’t going to use this feature of Yesod in the tutorial.
url() argument: name¶

Naming your URL lets you refer to it unambiguously from elsewhere in Yesod, especially from within templates. This powerful feature allows you to make global changes to the URL patterns of your project while only touching a single file.

When you’re comfortable with the basic request and response flow, read part 2 of this tutorial to start working with the database.
