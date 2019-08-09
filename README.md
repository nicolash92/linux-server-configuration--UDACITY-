# linux-server-configuration--UDACITY-

project for Udacity, AWS lightsail ubuntu server hosting the item-catalog web app

###### ip: 35.160.137.6

###### URL address: catalog.nicolas-hanout.com

###### ssh port: 2200

1. run

   - `sudo apt-get update`
   - `sudo apt-get upgrade`

2. create Grader with sudo permissions and rsa keys

   - `sudo adduser grader`
   - `sudo touch /etc/sudoers.d/grader`
   - `sudo nano /etc/sudoers.d/grader and add grader ALL=(ALL) NOPASSWD:ALL`
   - `sudo mkdir /home/grader/.ssh`
   - `sudo touch /home/grader/.ssh/authorized_keys`
   - On local machine `ssh-keygen`
     then copy public key to `authorized_keys`
     now we can ssh to grader aacount

3. Change SSH port from 22 to 2200 and disable root login and password login

   - Run `sudo nano /etc/ssh/sshd_config`
   - Change the port from 22 to 2200
   - set `PasswordAuthentication` to `no`
   - set `PermitRootLogin` to `no`
   - ran into `unable to resolve host`
     [followed these instructions to solve](https://forums.aws.amazon.com/thread.jspa?threadID=104765)
     Confirm by running `ssh -i /c/Users/shark/.ssh/udacity_key.rsa -p 2200 grader@35.160.137.6`

4) config ufw

   - `sudo ufw default deny incoming`
   - `sudo ufw default deny outgoing`
   - `sudo ufw allow 2200/tcp`
   - `sudo ufw allow 80/tcp`
   - `sudo ufw allow 123/udp`
   - `sudo ufw enable`
   - restart server for it to take effect
   - (had to add rule `sudo ufw allow out 53` because dns firewall blocked dns lookup)

5. set time to UTC

   - `sudo timedatectl set-timezone UTC`
   - `sudo timedatectl` shows that the timezone is now UTC

6) set up the server to serve the app:

   - `sudo apt-get install apache2`
   - `sudo apt-get install libapache2-mod-wsgi python-dev`
   - `sudo apt-get install git`

   - `sudo apt install python3-pip`
   - `sudo pip install flask` and other dependencies for catalog app: `google-auth`, `flask_auth`, `requests`, `psycopg2`, `libpq-dev`, `python-dev` etc..

   - `sudo apt-get install postgresql`
   - become postgresql account `sudo su - postgres` then `psql` then `create user catalog and alter user permisiions to CREATDB (allows user to create databases)`
   - `sudo mkdir /var/www/catalog`, then cloned my item-catalog repo to `/var/www/catalog/catalog`, then `sudo touch /var/www/catalog/catalog/flaskapp.wsgi` and set up the code to run my application.py

   - made and edited `/etc/apache2/sites-enabled/catalog.conf` to hold my wsgi app configuration.
   - [allowed apache to pass Authorization token to my app instead of intecepting them](https://modwsgi.readthedocs.io/en/develop/configuration-directives/WSGIPassAuthorization.html)
   - then `enable mod_wsgi`
   - `sudo a2enmod wsgi`

Resources used (There's more but I've lost some links :/):

- https://unix.stackexchange.com/questions/131332/ufw-is-blocking-dns

- deploy flask app on apache wsgi
  https://www.bogotobogo.com/python/Flask/Python_Flask_HelloWorld_App_with_Apache_WSGI_Ubuntu14.php

- https://modwsgi.readthedocs.io/en/develop/configuration-directives/WSGIPassAuthorization.html

- https://flask.palletsprojects.com/en/1.1.x/deploying/mod_wsgi/

- https://www.codementor.io/abhishake/minimal-apache-configuration-for-deploying-a-flask-app-ubuntu-18-04-phu50a7ft

- https://askubuntu.com/questions/138423/how-do-i-change-my-timezone-to-utc-gmt

- https://linuxize.com/post/how-to-set-or-change-timezone-on-ubuntu-18-04/

- https://askubuntu.com/questions/709843/how-to-configure-ufw-to-allow-ntp-to-work

- https://www.digitalocean.com/community/tutorials/how-to-use-ssh-to-connect-to-a-remote-server-in-ubuntu

- https://www.digitalocean.com/community/questions/how-to-i-add-a-second-key-to-the-authorized_keys-file

- disable remote root login https://askubuntu.com/questions/27559/how-do-i-disable-remote-ssh-login-as-root-from-a-server
