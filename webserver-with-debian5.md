# Webserver with Debian 5 (Lenny)
This guide describes, how to install a web server with Apache, Phusion Passenger, Mysql, RVM, Ruby Enterprise, Ruby on Rails, git.

## Prerequisites
This guide assumes, that you use a minimal Debian 5 installation. If you setup from scratch, download and install the minimal netinst debian image (http://www.debian.org/CD/netinst/).

After installing the base system, uncheck **everything** in the "Software Selection". Repeat: Uncheck "Desktop environment", "Standard System" and **don't** select "Web server", etc. We will install everything needed later on!

## Install SSH-Server
With the minimal base system installed, log into the fresh server and become super user with `su`. Then install the openssh-server with 
    aptitude install openssh-server

Now you can log into the server from your own terminal over ssh.

# Get the party started
After the base system installed (either on a home server, a virtual machine or a rented server in the net) we can start setting up the web server.

## Essentials
The essentials for RVM:

* Git core
* curl

for ruby enterprise:

* C compiler
* C++ compiler
* The 'make' tool
* The 'patch' tool
* Zlib development headers
* OpenSSL development headers
* GNU Readline development headers

Install everything with

    aptitude install git-core curl build-essential patch zlib1g-dev libssl-dev libreadline5-dev
    
as root (`su`).

## RVM
The ruby version manager.

    bash < <( curl -L http://bit.ly/rvm-install-system-wide )

## Ruby Enterprise (via RVM)
    rvm install ree
    rvm --default ree
Load the rvm environment
    . /usr/local/lib/rvm
    
## Ruby on Rails
    gem install rails

## Apache & MySQL
Last package is important to be able to run `gem install mysql` properly.
    aptitude install apache2 mysql-server libmysqlclient15-dev
Note: the mysql installer will ask you for a mysql root user password.
    
## Phusion Passenger
As Rails (or more precisely Rack) application server.
    aptitude install libcurl4-openssl-dev apache2-prefork-dev libapr1-dev libaprutil1-dev
    gem install passenger
    passenger-install-apache2-module
    
The setup guides you through different steps. At the end you get configuration information like:

"Please edit your Apache configuration file, and add these lines:"
    LoadModule passenger_module /usr/local/rvm/gems/ree-1.8.7-2010.02/gems/passenger-3.0.0/ext/apache2/mod_passenger.so
    PassengerRoot /usr/local/rvm/gems/ree-1.8.7-2010.02/gems/passenger-3.0.0
    PassengerRuby /usr/local/rvm/wrappers/ree-1.8.7-2010.02/ruby

Restart Apache `apache2ctl graceful`

And for vhosts:

    <VirtualHost *:80>
       ServerName www.yourhost.com
       DocumentRoot /somewhere/public    # <-- be sure to point to 'public'!
       <Directory /somewhere/public>
          AllowOverride all              # <-- relax Apache security settings
          Options -MultiViews            # <-- MultiViews must be turned off
       </Directory>
    </VirtualHost>