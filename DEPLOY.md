## How to deploy webnote

This document lists the steps necessary to deploy a webnote server
using Apache, and the WSGI mod, on a Debian GNU/Linux host.

The idea is to have a webnote server which presents a list of all the
user directories containing the www directory.

The most important part of the process is configuring the Apache
server to run a copy of the Django code for the webnote server.



### Some standard locations

We are going to put the code in `/opt/localnote`, and we will go
through all the steps of cloning the repository into that directory,
and building a virtual environment for it to run on.


### Installing Apache


### Installing the webnote and localnote code

Webnote server is dependant on webnote. You should follow all the
instructions in the README, except that you are installing them in a
different location, at `/opt/localnote/`, instead of
`~/dev/localnote`.

You will need to create a virtual environemt, and clone the code from
[Webnote](https://github.com/malcolmhutchinson/webnote),
and [webnote-django](https://github.com/malcolmhutchinson/webnote-django).


### Make the VirtualHost file

Apache is configured using text files in `/etc/apache2/` Create a file
called `/etc/apache2/sites-available/localnote.conf', and put this in
it:

    <VirtualHost *:80>
            # Adapted from Debian default script, with redundant stanzas
            # removed, and comments deleted.

            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/localnote
            ServerName localnote
            ServerAlias localnote.localhost www.localnote.localhost

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

    # vim: syntax=apache ts=4 sw=4 sts=4 sr noet

### Connecting everything up

You will need to makde a few adjustments to the ettings fle...