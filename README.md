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

If you are unfamiliar with Django, I recommend following the
[Django tutorial](https://docs.djangoproject.com/en/dev/intro/tutorial01/).

First, you must download or clone a copy of webnote. We assume this
has been installed in `~/dev/webnote`.

To install the localnote code into a Django development environment,
follow these steps.

Create a development directory:

    $ mkdir ~/dev/webnote
    $ cd ~/dev/webnote

Make a directory to hold the code under version control:

    $ mkdir code

Clone the code from github:

    $ git clone https://github.com/malcolmhutchinson/webnote.git code/

Install a virtual environment. 

    $ virtualenv env

You will have to install Django, and a number of other dependencies,
into the environment:

    $ source env/bin/activate
    (env) $ pip install django markdown2 bs4 exifread Pillow smartypants

You will have to put the webnote package onto your path. I've done
this by placing a simlink in my virtual environment at

    $ ln -s ~/dev/webnote/ ~/dev/webnote/env/lib/python2.7/site-packages/webnote

Now, run the Django development server:

    (env) $ python code/djsrv/manage.py runserver

And point your browser at localhost, port 8000:

    http://localhost:8000/

You should see a page indexing pages in your own `~/www` folder, and a
list of other users (if any) on the system. This will index any folder
in `/home` which contains a folder called `www`.

In order to see your figures and image files displayed inline on pages
in the webserver, you will have to add a symlink in the Django
project's `static` folder:

    $ ln -s /home/malcolm/www ~/dev/webnote/code/djsrv/static/home/malcolm

The structure of folders and symlinks in `static/` should echo the url
structure. Since home folders are called with url's like :

    http://localhost:8000/home/malcolm/

There should be a folder `static/home/malcolm/`.

