 README.md RAGNAROK 
 Demo : https://www.youtube.com/watch?v=VZkKTLSKNek&t=1s

# IMPORTANT # When you have cloned the repository you have to change UID (501) to you UID in
# And if you dont know you id, do the " $ vagrant up " first then it will show up. 
# But here is the path etherway : 
$  sudo nvim Ragnarok_Vagrant/.vagrant/machines/dbmiljo/virtualbox/creatoruid 
$  sudo nvim Ragnarok_Vagrant/.vagrant/machines/nextmiljo/virtualbox/creatoruid
#
##########################################################################################
##########################################################################################
#'########:::::'###:::::'######:::'##::: ##::::'###::::'########:::'#######::'##:::'##::::#
# ##.... ##:::'## ##:::'##... ##:: ###:: ##:::'## ##::: ##.... ##:'##.... ##: ##::'##:::::#
# ##:::: ##::'##:. ##:: ##:::..::: ####: ##::'##:. ##:: ##:::: ##: ##:::: ##: ##:'##::::::#
# ########::'##:::. ##: ##::'####: ## ## ##:'##:::. ##: ########:: ##:::: ##: #####:::::::#
# ##.. ##::: #########: ##::: ##:: ##. ####: #########: ##.. ##::: ##:::: ##: ##. ##::::::#
# ##::. ##:: ##.... ##: ##::: ##:: ##:. ###: ##.... ##: ##::. ##:: ##:::: ##: ##:. ##:::::#
# ##:::. ##: ##:::: ##:. ######::: ##::. ##: ##:::: ##: ##:::. ##:. #######:: ##::. ##::::#
###########################################################################################
###########################################################################################

# The manual guides you with: 
# - Setting up a Nextcloud Server 
# - Setting up a Database With Mysql Mariadb PHP Httpd 
# - Setting up and use mysqldump with cron 
#############################################################################
# The goal with this setup is to make it easy to do these processes.        #
# Due to some Systemctl tasks and so those will have to be executed manual. #
# so i don't recomend to automate ROOT tasks.                               #
# Feel free to grab a CLONE and config it how you would like it.            #
#############################################################################

# manual: 

# clone the repo. 
$ git fetch && pull
$ vagrant up
# Setting up the database
$ vagrant ssh dbmiljo
$ my_secure_sql
$ systemctl enable firewalld | mariadb.service | php.service |
$ systemctl start  firewalld | mariadb.service | php.service |
$ systemctl status  firewalld-cmd | mariadb.service | php.service |
$ mysql -u root -p

#  Backup dbmiljo: 

$ nvim home/example_user/.mylogin.cnf :

[client]
user = root
password = MySQL root user's password
 
wq 

$ chmod 600 /home/example_user/.mylogin.cnf

# Create the cron job file. Below is an example cron job to back up the entire database management system every day at 1am:
/etc/cron.daily/mysqldump :

0 1 * * * /usr/bin/mysqldump --defaults-extra-file=/home/example_user/.my.cnf -u root --single-transaction --quick --lock-tables=false --all-databases > full-backup-$(date +\%F).sql


# Restore Backup : 
#The restoration command’s general syntax is:
mysql -u [username] -p [databaseName] < [filename].sql

# Restore an entire DBMS backup. You will be prompted for the MySQL root user’s password:
This will overwrite all current data in the MySQL database system:
mysql -u root -p < full-backup.sql

# Restore a single database dump. An empty or old destination database must already exist to import the data into, and the MySQL user you’re running the command as must have write access to that database:
mysql -u [username] -p db1 < db1-backup.sql

# Restore a single table, you must have a destination database ready to receive the data:
mysql -u dbadmin -p db1 < db1-table1.sql


(not done with this one) check the one under this line instead )


# Setting up nextcloud

$ vagrant ssh nextmiljo

$ vim /etc/httpd/conf.d
# Copy and paste this in the vim conf file: 

Alias /nextcloud "/var/www/html/nextcloud/"
<Directory /var/www/html/nextcloud/>
  Options +FollowSymlinks
  AllowOverride All
 <IfModule mod_dav.c>
  Dav off
 </IfModule>
 SetEnv HOME /var/www/html/nextcloud
 SetEnv HTTP_HOME /var/www/html/nextcloud
</Directory> 

 # Setting up SELinux :

 $ semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/data(/.*)?'
 $ semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/config(/.*)?'
 $ semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/apps(/.*)?'
 $ semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.htaccess'
 $ semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.user.ini'
 $ restorecon -Rv '/var/www/html/nextcloud/'
 
 $ setsebool -P httpd_can_network_connect_db 1
 
 $ setsebool -P httpd_execmem 1
 
 $ sed -i '/^memory_limit =/s/=.*/= 512M/' /etc/php.ini
 
 $ systemctl enable --now httpd
 
 $ Firewalld setup: 
 $ firewall-cmd --add-service http --permanent
 $ firewall-cmd --add-service https --permanent
 $ firewall-cmd --reload

 # when that is done, you type in to your url:  "http://YOUR_IP_ADDRESS/nextcloud/"
 # then click storage & database
 # then selectt datafolder
 # !!!![if you dont know what you're doing, do not touch anything on "DATABASE" it will be autoconfiged]!!!
 # if you know how to handle it, then go to database. 
$ Enable OPcache
$ vim  /etc/php.d/10-opcache.ini
# Enable Zend OPcache extension module
$ zend_extension=opcache.so
$ opcache.enable=1
$ opcache.enable_cli=1
$ opcache.interned_strings_buffer=8
$ opcache.max_accelerated_files=10000
$ opcache.memory_consumption=128
$ opcache.save_comments=1
$ opcache.revalidate_f
# once youre done filling in restart  apache
$ systemctl restart httpd php-fpm

# now we have to config nextcloud for redis. 
$ vim /var/www/html/nextcloud/config/config.php
# copy paste this code below: 

<?php
$CONFIG = array (
  'instanceid' => '',
  'passwordsalt' => '',
  'secret' => '',
  'trusted_domains' =>
  array (
    0 => 'YOUR_IP',
  ),
  'datadirectory' => '/var/www/html/nextcloud/data',
  'dbtype' => 'mysql',
  'version' => '15.0.0.10',
  'overwrite.cli.url' => 'http://YOUR_IP/nextcloud',
  'dbname' => 'nextcloud',
  'dbhost' => 'localhost',
  'dbport' => '',
  'dbtableprefix' => 'oc_',
  'dbuser' => 'nc_user',
  'dbpassword' => 'YOUR_PASSWORD_HERE',
  'installed' => true,
  'memcache.locking' => '\OC\Memcache\Redis',
  'memcache.distributed' => '\OC\Memcache\Redis',
  'memcache.local' => '\OC\Memcache\Redis',
  'redis' => [
    'host' => 'localhost',
    'port' => 6379,
    'timeout' => 3,
  ],
);
# now enable and restart
systemctl enable --now redis
systemctl restart httpd php-fpm
#edit this conf file with vim 
vim /etc/httpd/conf.d/nextcloud.conf
# Copy and paste this below 
<VirtualHost *:80>
  ServerName YOURDOMAIN.TLD
  ServerAdmin YOUR@EMAIL.TLD
  DocumentRoot /var/www/html/nextcloud
  <directory /var/www/html/nextcloud>
    Require all granted
    AllowOverride All
    Options FollowSymLinks MultiViews
    SetEnv HOME /var/www/html/nextcloud
    SetEnv HTTP_HOME /var/www/html/nextcloud
  </directory>
</VirtualHost>

# setting a servername YOURDOMAIN.TLD 
$ sudo -u apache php /var/www/html/nextcloud/occ config:system:set trusted_domains 2 --value=YOURDOMAIN.TLD

$ systemctl restart httpd

# Ragnarok_Vagrant
