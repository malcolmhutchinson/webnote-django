## How to deploy webnote

This document lists the steps necessary to deploy a webnote server
using Apache, and the WSGI (Web Server Gateway Interface) mod, on a
Debian GNU/Linux host.

The idea is to have a webnote server which presents a list of all the
user directories containing the www directory.


### Some standard locations

We are going to put the code in `/opt/localnote`, and we will go
through all the steps of cloning the repository into that directory,
and building a virtual environment for it to run on.


### Cloning and installing the webnote and localnote code.

We will be placing these in the `/opt` directory. In shell:

    sudo mkdir /opt/localnote /opt/webnote

    # Change the owner and permissions, so you can work on it.
    sudo chown root:malcolm /opt/localnote /opt/webnote
    sudo chmod 775 /opt/localnote /opt/webnote

    # Clone the webnote code.
    git clone git@github.com:malcolmhutchinson/webnote.git /opt/webnote

    # Clone the localnote code.
    git clone git@github.com:malcolmhutchinson/webnote-django.git /opt/localnote/code

    # Create the virtual environment
    virtualenv /opt/localnote/env
    source /opt/localnote/env/bin/activate
    pip install --upgrade pip
    pip install django markdown2 bs4 exifread Pillow smartypants

    # Make a link to webnote.
    ln -s /opt/webnote /opt/localnote/env/lib/python2.7/site-packages/webnote

You can test this, by running the Django development server on the
code in `/opt/localnote/code`. It might give a lot of warnings about
migrations. You can apply them if you like, but localnote doesn't yet
use the database for anything, and it should run without them.


### Installing Apache

First, update the software repositories, then download and install the
apache2 server with mod-wsgi. In shell:

    sudo apt-get update
    sudo apt-get install apache2 libapache2-mod-wsgi
    
You should have a webserver running, which you can test, by pointing a
browser at http://localhost/ or http://127.0.0.1.

Now we want to create a virtual host for the localnote webserver.

In Debian-based systems, Apache modules and sites are configured by
keeping configuration files in `sites-available` (or `conf-available`,
or `mods-available`). Configurations, modules and site specifications
can be enabled by linking them from `sides-enabled` as needed.

We will create a configuration file, and then enable it using a Debian
utility. In shell:

    cd /etc/apache2
    sudo emacs sites-available/localnote.conf

Into that file, place the following code:

    <VirtualHost *:80>
            # Adapted from Debian default script, with redundant stanzas
            # removed, and comments deleted.

            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/localnote
            ServerName localnote
            ServerAlias www.localnote

            ErrorLog ${APACHE_LOG_DIR}/error_localnote.log
            CustomLog ${APACHE_LOG_DIR}/access_localnote.log combined

            <Directory '/opt/localnote/code/home/'>
                    <Files 'wsgi.py'>
                            Require all granted
                    </Files>
            </Directory>

            # WSGI daemon mode, which will not interfere with other
            # virtual hosts.
            WSGIDaemonProcess localnote \
                python-path=/opt/localnote/env/lib/python2.7/site-packages:/opt/localnote/code \
                python-home=/opt/localnote/env

            WSGIProcessGroup localnote
            WSGIScriptAlias / /opt/localnote/code/home/wsgi.py

            Alias /static/ /var/www/localnote/

    </VirtualHost>    

Enable this with the Debian apache utility `a2ensite`, and disable the existing default.

    sudo a2ensite localnote
    sudo a2dissite 000-default


### Connecting everything up

There are directives in the apache config file which make the python
code and static files available to Apache:

    DocumentRoot                /var/www/localnote
    Installed Python code       /opt/localnote/code/
    Python path                 /opt/localnote/env/lib/python2.7/site-packages
                                /opt/localnote/code

So we make sure all the expected things are in the expected places.


Create a link from `/var/www/localnote` to the static files
directory. In shell:

    sudo ln -s /opt/localnote/code/static /var/www/localnote

Hopefully, that should run. You might want to make some changes to the
`ServerAlias` directive in the apache config file, and maybe to
ALLOWED_HOSTS in `home.settings`.

I create a branch called `deploy`, in which to make these changes.

    cd /opt/localnote/code
    git checkout -b deploy

Finally, modify your `/etc/hosts` file, adding a line like this:

    127.0.0.1    localnote

You should be able to see a list of user directories which contain a
www directory.


### Pulling changes

First, checkout master, then pull the changes down. Now checkout deploy
and merge those changes.

This will protect the deployed values in settings files.

In shell:

    cd /opt/localnote/code
    git checkout master
    git pull github master
    git checkout deploy
    git merge master

END