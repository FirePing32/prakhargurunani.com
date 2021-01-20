---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Django - The future of web development"
subtitle: "Django is a free and opensource framework written in python. It is maintained by the Django Software Foundation (DSF)."
summary: "Django is a free and opensource framework written in python. It is maintained by the Django Software Foundation (DSF)."
authors: ["Prakhar Gurunani"]
tags: ["Django", "Web", "Web Development", "Web App", "Cloud", "Website", "Python", "Framework"]
categories: ["Tech"]
date: 2018-08-15
lastmod: 2018-08-15
featured: true
draft: false

# Featured image
# To use, add an image named `featured.jpg/png` to your page's folder.
# Focal points: Smart, Center, TopLeft, Top, TopRight, Left, Right, BottomLeft, Bottom, BottomRight.
image:
  caption: ""
  focal_point: ""
  preview_only: false

# Projects (optional).
#   Associate this post with one or more of your projects.
#   Simply enter your project's folder or file name without extension.
#   E.g. `projects = ["internal-project"]` references `content/project/deep-learning/index.md`.
#   Otherwise, set `projects = []`.
projects: []
---

Django’s primary goal is to ease the creation of complex database driven websites through small syntaxes. It mainly emphasizes on reusability of code and rapid development. Many websites have been built using Django. For ex — YouTube, Instagram, Mozilla, The Washington Times, etc. Django is useful for creating apps and websites that have a specific target and have a common database architecture. It is useful in interlinking web pages to their templates and returning specific responses according to user input. Its main advantage is that it helps to display dynamic content in a very few lines of code and can be modified easily.

· The installation of the Django module is as follows –

Windows — pip install django

Linux — sudo apt-get install python-django or git clone [https://github.com/django/django.git](https://github.com/django/django.git)

· After the above installation completes, follow these steps to initiate the project –

1. Cd into the directory where you want to start a Django project. Then enter command-

2. django-admin startproject <your project name>

· This creates a simple django project with default functions and settings. It has mainly four files in the <your project name> folder –

§ **urls.py — **The URL file telling python which URLs exist in the project.

§ **settings.py — **Holds the overall settings of the project like installing apps, static files directory, etc.

§ **wsgi.py — **Web server gateway of python.

§ **__init__.py — **To declare the folder as a python module to make it accessible by other python files.

· To start creating your website, you need to create an app first which will hold all your database information, templates, and the basic functioning of your app. Follow these steps to create an app –

1. Cd into your project directory and type-

2. python manage.py startapp <your app name>

· This creates an app in your project which will be displayed to user. This app directory has the following files –

§ **__init__.py — **To declare the folder as a python module to make it accessible by other python files.

§ **admin.py — **the file providing web interface to administrators to make changes to the project users and to the database.

§ **apps.py — **Specifies the app information

§ **models.py — **The core of the database which specifies different fields in the database file.

§ **views.py — **Specifies the web responses for different user requests.

· To start your project and host it on your local computer, type the command-

python manage.py runserver 80

· This starts a server on port 80 and makes your website accessible. If you go to [http://localhost](http://localhost):80, you will see the default Django startup page saying that the server setup was successful.

· The entire language is based on a model-view-template architecture –

§ **Model — **A model is a single definitive source of information about the data. It contains the fields of data which would be included in the database. It usually connects to one database file.

§ **View **— A view in a class or method which takes a web request and returns a certain web response in the form of HTML templates or HTML forms.

§ **Template **— It contains the basic static parts of the HTML output as well as some special syntax describing how the dynamic content will be displayed.

· Useful links – <br>
[**Django documentation**](https://docs.djangoproject.com/en/2.1/)
<br>
[**Django (web framework) - Wikipedia**](https://en.wikipedia.org/wiki/Django_(web_framework))