#README.md RAGNARÖK 
Vagrant automation making a database and a nextcloud server vm. 
manual: 
-----------------------------------------------------------------
clone the repo. 
git fetch && pull
cd testmiljo
vagrant up
#Setting up the database
vagrant ssh dbmiljo
my_secure_sql
systemctl enable firewalld | mariadb.service | php.service |
systemctl start  firewalld | mariadb.service | php.service |
systemctl status  firewalld-cmd | mariadb.service | php.service |


(not done with this one) check the one under this line instead )




____________________________________________________________________
#Setting up nextcloud
vagrant ssh nextmiljo
vim /etc/httpd/conf.d
and the following lines : 
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

Setting up SELinux :

 semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/data(/.*)?'
 semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/config(/.*)?'
 semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/apps(/.*)?'
 semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.htaccess'
 semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.user.ini'
 restorecon -Rv '/var/www/html/nextcloud/'
 
 setsebool -P httpd_can_network_connect_db 1
 
 setsebool -P httpd_execmem 1
 
 sed -i '/^memory_limit =/s/=.*/= 512M/' /etc/php.ini
 
 systemctl enable --now httpd
 
 Firewalld setup: 
 firewall-cmd --add-service http --permanent
 firewall-cmd --add-service https --permanent
 firewall-cmd --reload
 # when that is done, you type in to your url "http://YOUR_IP_ADDRESS/nextcloud/"
 then click storage & database
 then selectt datafolder
 if you dont know what you're doing, do not touch anything!!. 
 if you know, then go to database. 
 Enable OPcache
 vim  /etc/php.d/10-opcache.ini
 ; Enable Zend OPcache extension module
zend_extension=opcache.so
opcache.enable=1
opcache.enable_cli=1
opcache.interned_strings_buffer=8
opcache.max_accelerated_files=10000
opcache.memory_consumption=128
opcache.save_comments=1
opcache.revalidate_freq=1
#once youre done filling in restart  apache
systemctl restart httpd php-fpm
#now we have to config nextcloud for redis. 
vim /var/www/html/nextcloud/config/config.php
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
sudo -u apache php /var/www/html/nextcloud/occ config:system:set trusted_domains 2 --value=YOURDOMAIN.TLD

systemctl restart httpd
# Ragnarok_Vagrant
