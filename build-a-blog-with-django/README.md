# How to build a blog with Django

This tutorial is a step-by-step guide on how to build a simple blog
using Django. Having a basic understanding of Django will be useful for
this tutorial but not essential as it is for beginners, written from a
beginners' perspective.

![](https://justdjango.com/_next/image?url=https%3A%2F%2Fassets.justdjango.com%2Fmedia%2Fpost_thumbnails%2Fdjango-blog-tutorial.webp&w=2048&q=75)

This tutorial is a step-by-step guide on how to build a simple blog
using Django. Having a basic understanding of Django will be useful for
this tutorial but not essential as it is for beginners, written from a
beginners' perspective.

To be able to do this tutorial you will need the latest version of
<a href="https://www.python.org/downloads/"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">Python</a> and
<a href="https://code.visualstudio.com/download"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">VS Code</a> installed. For
reference, I am working on a Mac but this shouldn’t be too different for
Windows and Linux. I will also be using
<a href="https://tailwindcss.com/docs/installation"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">Tailwind CSS</a> for the
styling of this project as well as a few other things that I’ll cover
throughout the tutorial. Let’s get started.

Live blog example: <a href="https://savfreire.com/"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank"
rel="noopener noreferrer">https://blog.savfreire.com/</a>

The final code for this tutorial is available on GitHub:
<a href="https://github.com/SavFreire/Blogpostproject1"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank"
rel="noopener noreferrer">https://github.com/SavFreire/Blogpostproject1</a>

## Starting the Project

1.  Create a folder for your project. I've called mine "blog".
2.  Open a terminal (or command prompt on Windows) and run the following
    commands one by one:

```bash
pip install virtualenv  
virtualenv env  
source env/bin/activate # this command might be different on windows  
pip install django  
pip freeze requirements.txt  
django-admin startproject blogpost .  
```

Here you can see the basic commands for starting a project.

-   Virtual environments are isolated Python environments for us to work
    in. So as you can see, we start by installing
    pip install virtualenv, then running the command
    virtualenv and then activating it
    source env/bin/activate.
-   Next we install Django
    pip install django, then
    pip freeze \> requirements.txt which records an environment's
    current package list into a requirements.txt file.
-   The last command
    django-admin startproject blogpost . creates a Django project for
    the given project name in the current directory. I called my project
    "blogpost" because the folder created earlier was also called blog,
    just to keep everything organised.

## Run Initial Migrations

Running migrations should be a familiar practice by now but if you’re
still not sure of why we run them; basically migrations are Django's way
of recording changes you make to your \*\*models. \*\*I'll explain more
about models soon.

So run these next commands in the terminal one by one:

```bash
python manage.py migrate  
```

Now you will be able to see a new file "dbsqlite3", this is the database
stored in a file.

Next run this command:

```bash
python manage.py runserver  
```

You should now be able to see the page load on
<a href="http://127.0.0.1:8000"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">http://127.0.0.1:8000</a>

## Create Apps

Creating an App will be essential as it holds files such as "views",
"admin" and "models". Apps are used to provide some functionality to
your project. We can create multiple apps within a single Django
project.

1.  Close the server (ctrl+C) and run this command in the terminal:

```bash
python manage.py startapp nameofapp  
```

For example mine was:

```bash
python manage.py startapp posts  
```

1.  Once this is done, go into the **blogpost** folder, into
    **settings.py**. Scroll down until you get to **INSTALLED_APPS** and
    add **posts** (or the name of your App) into the list.

It should look like this:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'posts' # this is where you add the app name
]
```

## Models

Models are how Django accesses, manages and stores data through Python
objects. Since we are creating a blog, our models will represent some of
the things a blog usually has, like authors, categories and blog posts.

Inside of the **models.py** file, import the `get_user_model which` is a helper function from Django that fetches the User model for the project. Also import Django’s `models`  module at the top of the file like this:

```bash
from django.contrib.auth import get_user_model
from django.db import models

User = get_user_model()
```

Start with the Author model, all we want is a name and a picture of the
author.

```python
class Author(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    profile_picture = models.ImageField()

    def __str__(self):
        return self.user.username
```

Next is the Category model. A category needs a title, a subtitle, a
thumbnail (picture) and lastly, a **slug.** A slug is a short label for
something, containing only letters, numbers, underscores or hyphens.
They're generally used in URLs. So instead of having
<a href="http://127.0.0.1:8000"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">http://127.0.0.1:8000</a>/posts/1
we can have <a href="http://127.0.0.1:8000"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">http://127.0.0.1:8000</a>/post/my-first-blog-post.
It just is much easier to read and nicer to look at from a user
perspective.

```python
class Category(models.Model):
    title = models.CharField(max_length=20)
    subtitle = models.CharField(max_length=20)
    slug = models.SlugField()
    thumbnail = models.ImageField()

    def __str__(self):
        return self.title
```

And last but not least the Post model. This has the most information so
it has a bit more included. Everything is quite self explanatory in the
names.

```python
class Post(models.Model):
    title = models.CharField(max_length=100)
    slug = models.SlugField()
    overview = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)
    content = models.TextField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE)
    thumbnail = models.ImageField()
    categories = models.ManyToManyField(Category)
    featured = models.BooleanField()

    def __str__(self):
        return self.title
```

Here's more information on the ManyToManyField: <a
href="https://docs.djangoproject.com/en/4.0/topics/db/examples/many_to_many/"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank"
rel="noopener noreferrer">https://docs.djangoproject.com/en/4.0/topics/db/examples/many_to_many/</a>

1.  At the end of this, run migrations after closing the server (*ctrl
    C*):

```bash
python manage.py makemigrations  
```

```bash
python manage.py migrate  
```

Every time you make changes to the models, you will run migrations to
apply the changes to the database.

## Admin

In the **admin.py** file, import models and admin at the top of the page
like this:

```python
from django.contrib import admin
from .models import Author, Category, Post

admin.site.register(Author)
admin.site.register(Category)
admin.site.register(Post)
```

## Create a Superuser

The next step is creating a Superuser, similar to migrations, this
should be a practice for every Django project. Superusers are the admin
users who can make changes in an easy and user-friendly way, this admin
page is provided by Django. Whatever we put in the models will reflect
in the admin page and you’ll be able to access this with the Superuser
login information throughout the project.

Close the server (ctrl+C) and run these commands in the terminal:

```bash
python manage.py createsuperuser  
```

Follow the prompts in the terminal, example username, password, email,
etc. These can be chosen to your liking. Note them down somewhere in
case but if you forget you can always create another Superuser.

Once you have successfully created the Superuser, run this command:

```bash
python manage.py runserver  
```

You should now be able to access the admin page by logging into
 <a href="http://127.0.0.1:8000/admin/login"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank"
rel="noopener noreferrer">http://127.0.0.1:8000/admin/login</a>. You
should be able to see all of the classes you’ve created in the
**models.py** file. You can start by adding some content to work with in
Author, Category and Posts.

![Django
Admin](https://assets.justdjango.com/media/blog-media/admin.png)

## Views

Next you’ll be working in the **views.py** file. This is where you will
be doing all of the logic and is the most important part of this
project.

In **views.py**, import render as well as the models that were just
created.

```python
from django.shortcuts import render
from .models import Post, Category, Author
```

Start with the homepage:

```python
def homepage(request):
    categories = Category.objects.all()[0:3]
    featured = Post.objects.filter(featured=True)
    latest = Post.objects.order_by('-timestamp')[0:3]
    context= {
        'object_list': featured,
        'latest': latest,
        'categories':categories,
    }
    return render(request, 'homepage.html', context)
```

Next is the post detail page:

```python
def post(request,slug):
    post = Post.objects.get(slug=slug)
    context = {
        'post': post,
    }
    return render(request, 'post.html', context)
```

The "about" page if you want to add it in, it’s your choice.

```python
def about (request):
    return render(request, 'about_page.html')
```

The category page:

```python
def category_post_list (request, slug):
    category = Category.objects.get(slug = slug)
    posts = Post.objects.filter(categories__in=[category])
    context = {
        'posts': posts,
    }
    return render(request, 'post_list.html', context)
``` 

Then lastly, the list of posts:

```python
def allposts(request):
    posts = Post.objects.order_by('-timestamp')
    context = {
        'posts': posts,
    }
    return render(request, 'all_posts.html', context)
```

## URLs

1.  Go into the **urls.py** file and import this at the top:

```python
from django.conf import settings
from django.contrib import admin
from posts.views import homepage, post, about, search, postlist, allposts
```

1.  Create **paths** to the views you just created. A path is used for
    routing URLs to the appropriate view functions within a Django
    application using the URL dispatcher. It should look like this in
    the end:

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', homepage, name = 'homepage'),
    path('post/<slug>/', post, name = 'post'),
    path('about/', about,name = 'about' ),
    path('postlist/<slug>/', postlist, name = 'postlist'),
    path('posts/', allposts, name = 'allposts'),
]
```

## Templates

This is where the "frontend" templates are stored.

1.  Create a folder called **templates**.
2.  Create the following files inside the templates folder. The links to
    the final code of each page is included.

-   homepage.html (see the full code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/homepage.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)
-   footer.html (see the code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/footer.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)
-   base.html
-   navbar.html (see the code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/navbar.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)
-   post_list.html (see the code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/post_list.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)
-   all_posts.html (see the code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/all_posts.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)
-   about_page.html (see the code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/about_page.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)
-   post.html (see the code <a
    href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/post.html"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank" rel="noopener noreferrer">here</a>)

In **settings.py,** under TEMPLATES add this in by DIRS:

```python
'DIRS': BASE_DIR / “templates”,
```

## Tailwind CSS

For this project I am using Tailwind CSS for the frontend.

1.  To install Tailwind go to the following page and follow the prompts
    : <a href="https://tailwindcss.com/docs/installation/play-cdn"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank"
    rel="noopener noreferrer">https://tailwindcss.com/docs/installation/play-cdn</a>
2.  In the **base.html** file, put this in:

```html
<!DOCTYPE html>

<html>
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <script src="<https://cdn.tailwindcss.com>"></script>
  </head>
  <body>
    <h1 class="text-3xl font-bold underline">Hello world!</h1>
  </body>
</html>
```

The *base.html* file is very important, it will be added into most of
the templates so that we don’t have to repeat this code into each
template, but instead just extend the contents from the *base.html*
file.

## Template syntax

The Django documentation has more information on
<a href="https://docs.djangoproject.com/en/4.0/topics/templates/"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">template syntax</a>.

1.  In the body of **base.html**, remove “Hello World” and replace it
    with this:

```html
{% include "navbar.html" %} {% block content %} {% endblock content %} {% include "footer.html" %}
```

As I mentioned earlier, whatever is in the *base.html* file will be
extended into the other templates. So here you can see it will include
the *navbar* and *footer* on each page.

1.  In the following templates add
    {% extends 'base.html' %} at the top and then wrap all of the
    content in a block content form.

-   post.html
-   about_page.html
-   homepage.html
-   post_list.html

So each page should look like this:

```html
{% extends 'base.html' %} {% block content %}

<section>........</section>

{% endblock %}
```

## Alpine JS

We are going to use <a href="https://alpinejs.dev/start-here"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">AlpineJS</a> because it's a
simple JS library that helps with making a website more interactive.

Instead of a normal button in the *navbar*, I decided on adding a
dropdown menu for the categories. You can find the template for the
dropdown menu from <a
href="https://tailwindui.com/components/application-ui/elements/dropdowns"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">TailwindUI</a> (free).

1.  Paste the template in **navbar.html** - I did it below my "home" and
    "about" links.
2.  Add the AlpineJS script in **base.html** underneath the tailwind
    script.

```html
<script defer src="https://unpkg.com/alpinejs@3.x.x/dist/cdn.min.js"></script>
```

Then inside \*\*navbar.html \*\*add the following snippet below for a
dropdown menu using AlpineJS to toggle the dropdown:

```html
<div x-data="{ open: false }" class="relative inline-block text-left">
  <div>
    <button
      @click="open = ! open"
      type="button"
      class="inline-flex justify-center w-full rounded-md border border-gray-300 shadow-sm px-4 py-2 bg-white text-sm font-medium text-gray-700 hover:bg-gray-50 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-offset-gray-100 focus:ring-indigo-500"
      id="menu-button"
      aria-expanded="true"
      aria-haspopup="true"
    >
      Categories
      <!-- drop-down -->
      <svg
        class="-mr-1 ml-2 h-5 w-5"
        xmlns="http://www.w3.org/2000/svg"
        viewBox="0 0 20 20"
        fill="currentColor"
        aria-hidden="true"
      >
        <path
          fill-rule="evenodd"
          d="M5.293 7.293a1 1 0 011.414 0L10 10.586l3.293-3.293a1 1 0 111.414 1.414l-4 4a1 1 0 01-1.414 0l-4-4a1 1 0 010-1.414z"
          clip-rule="evenodd"
        />
      </svg>
    </button>
  </div>

  <div
    x-show="open"
    x-transition:enter="transition ease-out duration-100"
    x-transition:enter-start="transform opacity-0 scale-95"
    x-transition:enter-end="transform opacity-100 scale-100"
    x-transition:leave="transition ease-in duration-75"
    x-transition:leave-start="transform opacity-100 scale-100"
    x-transition:leave-end="transform opacity-0 scale-95"
    class="origin-top-right absolute right-0 mt-2 w-56 rounded-md shadow-lg bg-white ring-1 ring-black ring-opacity-5 focus:outline-none"
    role="menu"
    aria-orientation="vertical"
    aria-labelledby="menu-button"
    tabindex="-1"
  >
    <div class="py-1" role="none">
      <!-- Active: "bg-gray-100 text-gray-900", Not Active: "text-gray-700" -->
      {% for category in category_list %}
      <a
        href="{% url 'postlist' category.slug %}"
        class="text-gray-700 block px-4 py-2 text-sm rounded-lg hover:bg-pink-200 "
        role="menuitem"
        tabindex="-1"
        id="menu-item-0"
      >
        {{ category.title }}
      </a>
      {% endfor %}
      <a
        href="{% url 'allposts' %}"
        type="submit"
        class="text-gray-700 block w-full text-left px-4 py-2 text-sm rounded-lg hover:bg-pink-200"
        role="menuitem"
        tabindex="-1"
        id="menu-item-3"
      >
        See all posts</a
      >
    </div>
  </div>
</div>
```

You can also look at the <a
href="https://github.com/SavFreire/Blogpostproject1/blob/master/templates/navbar.html"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">final code</a> to see the
whole template.

## Search Bar

This is part of the styling. I’ve chosen to add in a search bar to
search for specific blog posts.

1.  Create a file in the **templates** folder called
    **\*search_results.html**. \*You can find the final code here.
2.  Then we will do the search bar. The original html for the search bar
    can be found here:
    <a href="https://tailwindcomponents.com/component/search-bar"
    class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
    target="_blank"
    rel="noopener noreferrer">https://tailwindcomponents.com/component/search-bar</a>
3.  Copy and paste the component into **navbar.html -** I pasted it
    below the
    \<nav\> tag because I want it on the far right of the navbar.
4.  In **navbar.html,** wrap the template in a
    \<form\> tag.
5.  In the form add the action property:

```html
<form action="{% url 'search' %}" class="search-form">...</form>
```

In **views.py**, import this at the top:

```python
from django.db.models import Q
```

Q is for making AND or OR queries. AND queries are for filtering records
that meet more than one condition. OR queries are for filtering records
that meet one of the queries.

Add this view function into **views.py** for the search logic.

```python
def search(request):
    queryset = Post.objects.all()
    query = request.GET.get('q')
    if query:
        queryset = queryset.filter(
            Q(title__icontains=query) |
            Q(overview__icontains=query)
        ).distinct()
    context = {
        'queryset': queryset
    }
    return render(request, 'search_bar.html', context)
```

Then add it to **urls.py**:

```python
path('search/', search, name = 'search'),
```

Change the name and button type in the input and class:

```html
<input
  class="border-2 border-gray-300 bg-white h-10 px-5 pr-16 rounded-lg text-sm focus:outline-none"
  type="text"
  name="q"
  #here
  placeholder="Search"
/>
<button type="submit" #here class="absolute right-0 top-0 mt-5 mr-4"></button>
```

It should look like this:

```html
<form action="{% url 'search' %}" class="search-form">
  <div class="pt-2 relative mx-auto text-gray-600">
    <input
      class="border-2 border-gray-300 bg-white h-10 px-5 pr-16 rounded-lg text-sm focus:outline-none"
      type="text"
      name="q"
      placeholder="Search"
    />
    <button type="submit" class="absolute right-0 top-0 mt-5 mr-4">
      <svg
        class="text-gray-600 h-4 w-4 fill-current"
        xmlns="[<http://www.w3.org/2000/svg>](<http://www.w3.org/2000/svg>)"
        xmlns:xlink="[<http://www.w3.org/1999/xlink>](<http://www.w3.org/1999/xlink>)"
        version="1.1"
        id="Capa_1"
        x="0px"
        y="0px"
        viewBox="0 0 56.966 56.966"
        style="enable-background:new 0 0 56.966 56.966;"
        xml:space="preserve"
        width="512px"
        height="512px"
      >
        <path
          d="M55.146,51.887L41.588,37.786c3.486-4.144,5.396-9.358,5.396-14.786c0-12.682-10.318-23-23-23s-23,10.318-23,23  s10.318,23,23,23c4.761,0,9.298-1.436,13.177-4.162l13.661,14.208c0.571,0.593,1.339,0.92,2.162,0.92  c0.779,0,1.518-0.297,2.079-0.837C56.255,54.982,56.293,53.08,55.146,51.887z M23.984,6c9.374,0,17,7.626,17,17s-7.626,17-17,17  s-17-7.626-17-17S14.61,6,23.984,6z"
        />
      </svg>
    </button>
  </div>
</form>
```

Here is an example of how it should look:

![Search
bar](https://assets.justdjango.com/media/blog-media/searchbar2gif-1.gif)

## Installing Markdown

The reason for using markdown is for the post content to be more
readable. If we just left it like how Django has provided, it would use
basic text, however markdown helps us format text into styling it
similar to sites like <a href="http://notion.so"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">notion.so</a>. This will be
extremely useful to the authors/ admin putting content into the admin
page and displaying it on the frontend to how they’ve written it instead
of having to use html in the backend.

Follow the tutorial
<a href="https://learndjango.com/tutorials/django-markdown-tutorial"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank"
rel="noopener noreferrer">https://learndjango.com/tutorials/django-markdown-tutorial</a>
for extra info if needed.

1.  Close the server and install this is in the terminal:
    pip install markdown==3.2.1
2.  In the **posts** folder, create a folder inside called
    **templatetags**.
3.  Inside the **templatetags** folder create a file called
    **markdown_extras.py.**
4.  In the **markdown_extras.py** file, paste this in:

```python
from django import template
from django.template.defaultfilters import stringfilter
import markdown as md

register = template.Library()

@register.filter()
@stringfilter
def markdown(value):
    return md.markdown(value, extensions=['markdown.extensions.fenced_code'])
```

1.  To continue with this go into **post.html** and below
    {% extends 'base.html' %} put
    {% load markdown_extras %}.
2.  In **post.html**, in the post content section, paste
    {{ post.content \| markdown \| safe }} . This will change how the
    content is displayed.

It will look like this:

```html
{% extends 'base.html' %} {% load markdown_extras %} {% block content %}

<div class="py-6">{{ post.content | markdown | safe }}</div>

{% endblock content %}
```

## Dropdown Menu Logic

In the **markdown_extras.py** file, import the Category model at the top
of the file. This is because the dropdown menu will contain the
different categories created, eg: food, review, learn.

```python
from posts.models import Category
```

Below the filter field, add this:

```python
@register.simple_tag
def get_categories():
    return Category.objects.all()[0:3]
```

This is being put in the navbar.html file because the dropdown menu is
in the actual navbar. In **navbar.html,**  add this to the top of the
page above everything else:

In **navbar.html** load the template tags:

```html
{% load markdown_extras %}
```

And then call the template tag to store the result in a template
variable:

```html
{% get_categories as category_list %}  
```

Then in the dropdown you can loop through all the categories to create
options in the dropdown:

```html
{% for category in category_list %}
<a
  href="{% url 'postlist' category.slug %}"
  class="text-gray-700 block px-4 py-2 text-sm rounded-lg hover:bg-pink-200 "
  role="menuitem"
  tabindex="-1"
  id="menu-item-0"
>
  {{ category.title }}
</a>
{% endfor %}
```

Here is an example of how it should look in the end:

![Dropdown
menu](https://assets.justdjango.com/media/blog-media/drop-dwn-menugif.gif)

## Installing Pillow for Images

For images to be displayed we need to install pillow and setup static
files.

1.  Close the server (Ctrl + C).
2.  Installing Pillow, in the terminal run these commands one by one:

```bash
pip install pillow
pip freeze > requirements.txt
python manage.py runserver
``` 

Then setup static files in the **settings.py**:

I’ll be using this link
<a href="https://docs.djangoproject.com/en/4.0/howto/static-files/"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank"
rel="noopener noreferrer">https://docs.djangoproject.com/en/4.0/howto/static-files/</a>
as a guide if you need it for reference.

Paste this in **settings.py** below STATIC_URL:

```python
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    BASE_DIR / "static",
]
MEDIA_URL = '/media/'
STATIC_ROOT = BASE_DIR / "static_cdn"
MEDIA_ROOT = BASE_DIR / "media_cdn"
```

In **urls**, import this:

```python
from django.confimport settings
from django.conf.urls.static import static
```

Also in **urls** at the bottom of the page:

```python
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
    urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
```

1.  Create a folder “**static_cdn**” and another folder “**media_cdn**”.
2.  Close the server and run :
    python manage.py collectstatic
3.  Now in any of the templates use this line to load the functionality
    for static files:

```html
{% load static %}  
```

Now you should see when you create a blog post and select the thumbnail,
it will save the image in your project's "media" folder.

## Conclusion

Try create a few blog posts. You should be able to see them listed out,
view the details of each post.

And that brings us to the end of this tutorial, I hope you were able to
follow along until the very end. Stay tuned for future tutorials with a
few more complex features to be added to this, as well as many more
things we can do with Django.

If this tutorial was helpful and you want to learn more about Django,
you can find a comprehensive roadmap of courses on
<a href="https://learn.justdjango.com"
class="text-lightBlue hover:text-lightBlueHover ease-in-out transition duration-150"
target="_blank" rel="noopener noreferrer">JustDjango Learn</a>.