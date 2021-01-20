---
# Documentation: https://wowchemy.com/docs/managing-content/

title: "Social networking site in Django (Cloudinary + Sendgrid)"
subtitle: "Did you know that almost every person on our planet is a user of at least five types of social networking sites?"
summary: "Did you know that almost every person on our planet is a user of at least five types of social networking sites?"
authors: ["Prakhar Gurunani"]
tags: ["Python", "Django", "Cloudinary", "Sendgrid", "Social Network", "Website", "Web App", "PWA", "Framework", "Web"]
categories: ["Tech"]
date: 2020-07-17
lastmod: 2020-07-17
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

<blockquote>
*Facebook alone, the most popular social network website, has 2.375 billion users. That is why developing a social network website often comes to the minds of entrepreneurs when they look at these astounding numbers.*
</blockquote>

Building and managing your own social networking website comes to many when they are fed up with those bunch of features and content, which they may don't even want to use, making the platform over populated with posts not relevant to them.

A simple example explaning this, is Instagram. After the initial public launch, it was mainly designed for artists for sharing photos. Then IGTV Videos were introduced, followed by Direct Messaging. Now, they have introduced Reels, which brings all features of TikTok and similar apps (as TikTok is banned in India and some other countries). This leaves the platform much similar to Facebook.

This is a detailed tutorial explaining how to build a social networking website from scratch in Django along with [Cloudinary](https://cloudinary.com) CDN integration for storing media files and [Sendgrid](https://sendgrid.com) for crash reporting.

The user can write articles, upload profile picture, display bio in profile, view other user’s profile and their articles.

*The full source code of the project can be found in this repo → [VellXR](https://github.com/prakhargurunani/VellXR)*

## Initialisation

Create a Django project and an app for the same.

```bash
django-admin startproject VellXR
cd VellXR && python manage.py startapp user
```

Setup virtualenv and install requirements.

```bash
virtualenv env

.\env\Scripts\activate - For Windows users
source env/bin/activate - For Linux/MacOS users

pip install -r requirements.txt
```

## Configure project settings

The existing `settings.py` uses production settings. Create a `local_settings.py` in same directory to run the project in development mode.

```python
DEBUG = True
SECURE_SSL_REDIRECT = False
SECURE_PROXY_SSL_HEADER = None
```

## Coding time !

Create your models in `VellXR/user/models.py` and make cloudinary CDN configurations.

```python
from django.db import models
from django.contrib.auth.models import User
from django.utils import timezone 
import cloudinary
import cloudinary.uploader
import cloudinary.api
from cloudinary.models import CloudinaryField
from django.template.defaultfilters import slugify
import datetime
import os

User._meta.get_field('email')._unique = True
User._meta.get_field('username')._unique = True

cloudinary.config( 
  secure=True,
  cloud_name = 'CLOUDINARY_CLOUD_NAME', 
  api_key = 'CLOUDINARY_API_KEY', 
  api_secret = 'CLOUDINARY_API_SECRET' 
)

class UserDetail(models.Model):

    user = models.OneToOneField(User,on_delete=models.CASCADE)
    bio = models.CharField(max_length=50, blank=True)
    portfolio_site = models.URLField(blank=True)
    profile_picture = CloudinaryField('image', null=True, blank=True, default="logo.png")
    def __str__(self):
        return self.user.username

class Post(models.Model): 

    author = models.ForeignKey(User, on_delete=models.CASCADE) 
    slug = models.SlugField(max_length=50)
    title = models.CharField(max_length=50) 
    post_image = CloudinaryField('image', blank=True, null=True, default="logo.png")
    content = models.TextField() 
    published_date = models.DateTimeField(default=timezone.now) 

    def publish(self): 
        self.published_date = timezone.now() 
        self.save() 

    def __str__(self): 
        return self.title 

    def save(self):
        super(Post, self).save()
        cur_time = datetime.datetime.now()
        self.slug = '%s-%d%d%d%d%d%d' % (
            slugify(self.title), cur_time.hour, cur_time.minute, cur_time.second, cur_time.day, cur_time.month, cur_time.year
        )
        super(Post, self).save()
```

Replace the environment variables with your Cloudinary `CLOUD_NAME`, `API_KEY`, `API_SECRET`.

In `VellXR/user/admin.py` register your models.

```python
from django.contrib import admin
from user.models import UserDetail
from user.models import Post
# Register your models here.

admin.site.register(UserDetail)
admin.site.register(Post)
```

Setup urls for the project.

```python
from django.contrib import admin
from django.urls import path
from django.conf.urls import url,include
from django.conf.urls.static import static
from user import views
from . import settings

urlpatterns = [
    path('admin/', admin.site.urls),
    url(r'^$',views.index, name='index'),
    url(r'^search/(?P<search_query>[-\w.]+)/$', views.search, name='search'),
    url(r'^logout/$', views.user_logout, name='logout'),
    url(r'^register/$',views.register, name='register'),
    url(r'^login/$',views.user_login, name='login'),
    url(r'^about/$', views.about, name='about'),
    url(r'^write/$', views.write, name='write'),
    url(r'^profile/(?P<username>[-\w.]+)/$', views.user_profile, name='profile'),
    url(r'^profile/(?P<username>[-\w.]+)/posts/$', views.profile_posts, name='profile_posts'),
    url(r'^profile/(?P<username>[-\w.]+)/posts/(?P<slug>[-\w.]+)/$', views.profile_posts_detail, name='profile_posts_detail'),
] + static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

### Building views

Here, views will mainly consist of the following components -

* `index`

* `about`

* `user_login`

* `user_logout`

* `register`

* `user_profile`

* `write`

* `profile_posts`

* `profile_posts_detail`

* `search`

```python
from django.shortcuts import render
from user.forms import UserForm, UserDetailForm, PostForm
from django.contrib.auth import authenticate, login, logout
from django.http import HttpResponseRedirect, HttpResponse
from django.urls import reverse
from django.contrib.auth.decorators import login_required
from cloudinary.forms import cl_init_js_callbacks
from django.utils import timezone
from django.contrib.auth.models import User
from .models import UserDetail, Post

def index(request):
    user_posts_on_home = Post.objects.all().order_by('-published_date')[:20]
    return render(request,'user/index.html', {'user_posts_on_home':user_posts_on_home})

def about(request):
    return render(request,'user/about.html')

@login_required
def user_logout(request):
    logout(request)
    return HttpResponseRedirect(reverse('index'))

def register(request):
    registered = False
    if request.method == 'POST':
        user_form = UserForm(request.POST)
        user_detail_form = UserDetailForm(request.POST, request.FILES)
        if user_form.is_valid() and user_detail_form.is_valid():
            user = user_form.save()
            user.set_password(user.password)
            user.save()
            profile = user_detail_form.save(commit=False)
            profile.user = user
            if 'profile_picture' in request.FILES:
                print('found profile picture')
                profile.profile_picture = request.FILES['profile_picture']
            profile.save()
            registered = True
        else:
            print(user_form.errors, user_detail_form.errors)
    else:
        user_form = UserForm()
        user_detail_form = UserDetailForm()
    return render(request,'user/registration.html',
                          {'user_form':user_form,
                           'user_detail_form':user_detail_form,
                           'registered':registered})

def user_login(request):
    if request.method == 'POST':
        username = request.POST.get('username')
        password = request.POST.get('password')
        user = authenticate(username=username, password=password)
        if user:
            if user.is_active:
                login(request,user)
                return HttpResponseRedirect(reverse('index'))
            else:
                return HttpResponse("Account not found !")
        else:
            print("Someone tried to login and failed.")
            print("They used username: {} and password: {}".format(username,password))
            return HttpResponse("Invalid login details given")
    else:
        return render(request, 'user/login.html', {})

@login_required
def write(request):
    written = False
    if request.method == 'POST':
        post_form = PostForm(request.POST, request.FILES)
        if post_form.is_valid():
            my_post = post_form.save(commit=False)
            my_post.author = request.user
            my_post.published_date = timezone.now()
            if 'post_image' in request.FILES:
                print('found post image')
                my_post.post_image = request.FILES['post_image']
            my_post.save()
            written = True
        else:
            print(post_form.errors)
    else:
        post_form = PostForm()
    return render(request,'user/write.html',
                          {'post_form':post_form, 'written':written})

def user_profile(request, username):
    user_username = User.objects.get(username=username)
    user_image = user_username.userdetail.profile_picture
    user_bio = user_username.userdetail.bio
    user_portfolio_site = user_username.userdetail.portfolio_site
    return render(request, 'user/profile.html', {'user_username':user_username,
                                                 'user_image':user_image,
                                                 'user_bio':user_bio,
                                                 'user_portfolio_site':user_portfolio_site,
                                                 })

def profile_posts(request, username):
    user_posts = Post.objects.filter(author__username=username).order_by('-published_date')
    return render(request, 'user/posts.html', {'user_posts':user_posts})

def profile_posts_detail(request, username, slug):
    user_posts_detail = Post.objects.filter(slug=slug)
    cur_user_username = User.objects.get(username=username)
    cur_user_image = cur_user_username.userdetail.profile_picture
    return render(request, 'user/posts_detail.html', {'user_posts_details':user_posts_detail, 
                                                      'cur_user_username':cur_user_username,
                                                      'cur_user_image':cur_user_image})

def search(request, search_query):
    if request.method == 'GET':
        cur_search_query = search_query
        search_query_users = User.objects.filter(username=search_query)
        search_query_posts = Post.objects.filter(title=search_query)
        return render(request, 'user/search.html', {'cur_search_query':cur_search_query,
                                                    'search_query_users':search_query_users,
                                                    'search_query_posts':search_query_posts})
```

### Building forms

Forms will be of 3 types -

* `UserForm`

* `UserDetailForm`

* `PostForm`

```python
from django import forms
from user.models import UserDetail
from user.models import Post
from django.contrib.auth.models import User
from django.forms import widgets

class UserForm(forms.ModelForm):
    first_name = forms.CharField(widget=forms.TextInput(attrs={'class':'form-control text-center'}))
    last_name = forms.CharField(widget=forms.TextInput(attrs={'class':'form-control text-center'}))
    email = forms.CharField(widget=forms.TextInput(attrs={'class':'form-control text-center'}))
    username = forms.CharField(widget=forms.PasswordInput(attrs={'class':'form-control text-center', 'type':'username', 'id':'exampleInputEmail1'}))
    password = forms.CharField(widget=forms.PasswordInput(attrs={'class':'form-control text-center', 'type':'password', 'id':'exampleInputPassword1'}))

    class Meta():
        model = User
        fields = ('first_name', 'last_name', 'email', 'username', 'password')

class UserDetailForm(forms.ModelForm):
    bio = forms.CharField(required=False, widget=forms.TextInput(attrs={'class':'form-control text-center'}))
    portfolio_site = forms.CharField(required=False, widget=forms.URLInput(attrs={'class':'form-control text-center'}))
    profile_picture = forms.ImageField(required=False, widget=forms.FileInput(attrs={'class':'form-control text-center custom-file custom-file-input btn btn-default btn-file input-group-text', 'id':'inputGroupFile01', 'aria-describedby':'inputGroupFileAddon01', 'style':'text-align: center; vertical-align: center; width: 100%; height: 100%;'}))

    class Meta():
        model = UserDetail
        fields = ('bio', 'portfolio_site', 'profile_picture')

class PostForm(forms.ModelForm):
    title = forms.CharField(widget=forms.TextInput(attrs={'class':'form-control text-center'}))
    post_image = forms.ImageField(required=False, widget=forms.FileInput(attrs={'class':'form-control text-center custom-file custom-file-input btn btn-default btn-file input-group-text', 'id':'inputGroupFile01', 'aria-describedby':'inputGroupFileAddon01', 'style':'text-align: center; vertical-align: center; width: 100%; height: 100%;'}))
    content = forms.CharField(widget=forms.Textarea(attrs={'class':'form-control'}), help_text='HTML, CSS, and JavaScript are supported')

    class Meta():
        model = Post
        fields = ('title', 'post_image', 'content')
```

## Configuring Sendgrid

The config is pretty easy and straightforward.

* `ADMIN_FULL_NAME` - full name on Sendgrid console

* `ADMIN_EMAIL_ID` - E-mail ID used to signup on Sendgrid

* `SENDGRID_API_KEY` - Sendgrid API key

* `MAILER_LIST` - list of users to send e-mail

* `DEFAULT_FROM_EMAIL` - E-mail ID used to signup on Sendgrid

* `EMAIL_HOST_USER` - Username on Sendgrid console

* `EMAIL_HOST_PASSWORD` - Password of Sendgrid account

## Running the app

The app is now ready for development use. Running the following necessary commands will fire up our app for use.

```bash
python manage.py makemigrations
python manage.py migrate
python manage.py collectstatic
python manage.py runserver 80
```

***Your thoughts are valuable for me. Express your views and suggestions in comments and give a clap if you like the project.***