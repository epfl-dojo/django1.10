# Ubuntu 16.04 vm setup to deploy our application


## Server basics

~~~ bash
sudo apt update && sudo apt dist-upgrade; /usr/lib/update-notifier/update-motd-reboot-required
sudo apt install vim
sudo apt install tree
sudo apt install multitail
sudo pip3 install Glances
sudo apt install build-essential
sudo apt install meld
sudo apt install git
sudo apt install python3-dev python3-pip
sudo apt install virtualenv
sudo apt install mailutils ssmtp
sudo vi /etc/ssmtp/ssmtp.conf
sudo vi /etc/ssmtp/revaliases
echo test | sudo mail -s test1 Samuel.Bancal@epfl.ch
sudo apt install ntp
sudo vi /etc/ntp.conf
date; sudo service ntp restart; date;
sudo pip3 install Glances
~~~


## Apache & MySQL

~~~ bash
sudo apt install apache2
sudo apt install apache2-dev
sudo apt install mysql-server
sudo apt install libmysqlclient-dev
sudo mysql -u root -p
~~~

~~~ sql
CREATE DATABASE shortme;
CREATE USER 'shortme'@'localhost' IDENTIFIED BY '<KeepassX>';
GRANT ALL PRIVILEGES ON shortme.* TO 'shortme'@'localhost';
FLUSH PRIVILEGES;
~~~


## Our application requirements

~~~ bash
sudo mkdir /django_app
sudo chown sbancal\: /django_app/
sudo vi /etc/hosts
~~~

~~~
128.178.7.114   enacit1sbtest1.epfl.ch  enacit1sbtest1 vas-y.epfl.ch vas-y
~~~


## Tequila install

~~~ bash
sudo apt install libssl-dev apache2-dev
wget http://tequila.epfl.ch/download/2.0/tequila-apache-C-2.0.16.tgz
tar -xvzf tequila-apache-C-2.0.16.tgz
cd tequila-2.0.16/Apache/C/
ed - Makefile <<EOFMakefile
g/httpd /s:httpd :apache2 :
g/apxs/s:apxs :apxs2 :
wq
EOFMakefile
make
sudo make install

sudo vi /etc/apache2/mods-available/tequila.load /etc/apache2/mods-available/tequila.conf
~~~

~~~ snip
LoadModule tequila_module /usr/lib/apache2/modules/mod_tequila.so
~~~

~~~ snip
<IfModule mod_tequila.c>
  TequilaLogLevel        2
  TequilaLog             /var/log/apache2/tequila.log
  TequilaServer          tequila.epfl.ch
  # TequilaSessionDir      /var/www/Tequila/Sessions
  TequilaSessionDir      /var/tequila
  TequilaSessionMax      3600
</IfModule>
~~~

~~~ bash
sudo mkdir /var/tequila
sudo chown www-data: /var/tequila

sudo a2enmod tequila
sudo service apache2 restart

sudo apache2ctl -M  | grep tequila
~~~

~~~ out
tequila_module (shared)
~~~
