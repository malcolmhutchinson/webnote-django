Django project for webnote server
=================================

This is a Django webserver project to impliment Malcolm Hutchinson's
webnote application.

Webnote is a collection of Python classes designed to analyse the
contents of a filesystem for text documents, and return structures
such that the documents can be easily displayed by a web server, and
linked with other documents within the filesystem.



Installation
------------

These instructions list the steps necessary to install into a django
development environment. You will need to clone the
[webnote](https://github.com/malcolmhutchinson/webnote) repository,
and then the [webnote-django]() repo. I assume installation into a
directory at `~/dev/`. If this directory doesn't exist, create it
with:

    mkdir ~/dev

Create workspaces for both repositories. In shell:

    cd ~/dev/
    mkdir localnote webnote
    
Clone a copy of webnote into `~/dev/webnote`.

    git clone https://github.com/malcolmhutchinson/webnote.git webnote/

Now set up the development environment for the Django server.

If you are unfamiliar with Django, I recommend following the
[Django tutorial](https://docs.djangoproject.com/en/dev/intro/tutorial01/).

In shell:

    cd ~/dev/localnote/
    mkdir code
    git clone https://github.com/malcolmhutchinson/webnote-django.git code/


Install a Python virtual environment. 

    virtualenv env

You will have to install Django, and a number of other dependencies,
into the environment:

    source env/bin/activate
    pip install --upgrade pip
    pip install django markdown2 bs4 exifread Pillow smartypants

You will have to put the webnote package onto your path. I've done
this by placing a simlink in my virtual environment at

    ln -s ~/dev/webnote/ ~/dev/localnote/env/lib/python2.7/site-packages/webnote

Now, run the Django development server:

    (env) $ python code/djsrv/manage.py runserver

And point your browser at localhost, port 8000:

    http://localhost:8000/

The first time you run this, it will probably give you a migrations
warning. If so, quit out of the server with CTL-C, and type

    ./manage.py migrate

Then run the Django development server again:

    python code/djsrv/manage.py runserver

You should see a page indexing pages in your own `~/www` folder, and a
list of other users (if any) on the system. This will index any folder
in `/home` which contains a folder called `www`.

In order to see your figures and image files displayed inline on pages
in the webserver, you will have to add a symlink in the Django
project's `static` folder:

    cd ~/dev/localnote/code/static
    mkdir home
    ln -s /home/malcolm/www ~/dev/localnote/code/static/home/malcolm

The structure of folders and symlinks in `static/` should echo the url
structure. Since home folders are called with url's like :

    http://localhost:8000/home/malcolm/

There should be a folder `static/home/malcolm/`.

