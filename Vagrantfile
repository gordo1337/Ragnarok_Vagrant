# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure

Vagrant.configure("2") do |config|
  config.vm.box ="centos/8"#"treehouses/buster64"  
  config.vm.synced_folder '../var', "/var/www/html/nextcloud/",  type: "nfs", disabled: true
  #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
  #config.vm.network "forwarded_port", guest: 5432, host: 54320 # nextcloud
  config.vm.network "private_network", ip: "192.168.33.4", type: "dhcp"
  config.vm.network "forwarded_port", guest: 111, host: 2049 #, type: "nfs", disabled: true 
  config.vm.network "forwarded_port", guest: 443, host: 8081 #, type: "nfs", disabled: true 
  config.vm.provider "virtualbox" do |v|
    v.memory = "1096"
    v.cpus = "1"
  end

  config.vm.define "dbmiljo" do |db|
    db.vm.box = "centos/8"
    db.vm.hostname = "dbmiljo.local"
    config.vm.provider "virtualbox" do |v| #v 
      v.memory = "4024"
      v.cpus =" 1"
    end
    #config.vm.network "private_network", type: "dhcp"
    #config.vm.network "private_network", guest: 5433, host: 80, type: "dhcp"
    #config.vm.network "private_network", ip:"192.168.33.20", type: "dhcp" 
    #sudo dnf install -y httpd
    #sudo dnf install http://rpms.remirepo.net/enterprise/remi-release-8.rpm
    #sudo dnf install yum-utils
    #php-redis php-imagick 
    #sudo dnf install -y php php-gd php-mbstring php-intl php-pecl-apcu\php-mysqlnd php-opcache php-json php-zip
    #sudo dnf install -y php-redis php-imagick
    #sudo dnf install -y mariadb mariadb-server
    #sudo dnf install -y redis
    #sudo dnf install firewalld 
    #systemctl start ("firewalld | mariadb.service | php.service") 
    #systemctl enable (firewalld | mariadb.service | php.service")
    #sudo firewall-cmd --reload
    #sudo mkdir /var/lib/mysql/dbnextmiljo
    #mount -t /var/lib/mysql/dbmiljo
    config.vm.network "private_network", ip:"192.168.33.20", type: "dhcp"
    config.vm.network "forwarded_port", guest: 111, host: 2049, type: "nfs", disabled: true
    config.vm.network "forwarded_port", guest: 443, host: 8081, type: "nfs", disabled: true 
    #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
    config.vm.synced_folder '../var', "/var/www/html/nextcloud/", type: "nfs", disabled: true
    config.vm.synced_folder '../etc', "/etc/", type: "nfs", disabled: true
    config.vm.provision "shell", inline: <<-SHELL
    sudo dnf makecache
    sudo dnf install -y bind bind-utils
    sudo dnf update
    sudo dnf install epel-release yum-utils unzip curl wget bash-completion policycoreutils-python-utils mlocate bzip2 httpd 
    sudo dnf -y update
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/data(/.*)?'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/config(/.*)?'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/apps(/.*)?'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.htaccess'      
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.user.ini'
    sudo wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm 
    sudo wget https://rpms.remirepo.net/enterprise/remi-release-8.rpm 
    sudo dnf module reset php
    sudo dnf update -y
    sudo dnf install php php-gd php-mbstring php-intl php-pecl-apcu php-mysqlnd php-opcache php-json php-zip mariadb mariadb-server redis firewalld 

    SHELL
  end
      config.vm.define "nextmiljo" do |server|
      config.vm.hostname = "nextmiljo.local"
      config.vm.network "private_network", type: "dhcp"
      config.vm.network :private_network, ip: "192.168.33.10", type: "dhcp" 
      config.vm.network "forwarded_port", guest: 111, host: 2049, type: "nfs", disabled: true
      config.vm.network "forwarded_port", guest: 443, host: 8081, type: "nfs", disabled: true 
      config.vm.synced_folder '../var', "/var/www/html/nextcloud/", type: "nfs", disabled: true
      config.vm.synced_folder '../etc', "/etc/", disabled: true  
     end    
      #sudo tar -xvjf nextcloud-17-latest.tar.bz2 
      #dnf install -y nextcloud-17-latest.tar.bz2 
      #config.vm.synced_folder "db/", "/var/lib/mysql/dbmiljo", disabled: false
       #line 83 maybe unzip instead of tar but then use this instead: 
      #curl https://Nextclouddownload.nextcloud.com/server/releases/nextcloud-19.0.0.zip -o /var/www/html/nextcloud-19.0.0.zip
      #sudo unzip /var/www/html/nextcloud-19.0.0.zip
      #sudo dnf install unzip -y
      #sudo mv /tmp/nextcloud /var/www/html/
      #sudo cd ../var
      #sudo mkdir /var/www/html/nextcloud/data
      #sudo chown -R apache:apache /var/www/html/nextcloud/
      #sudo chgrp -R apache /var/lib/php/{opcache,session,wsdlcache}
      config.vm.provision "shell", inline: <<-SHELL
      dnf makecache
      dnf update 
      dnf install -y yum 
      yum install mariadb-server php-mysqlnd
      cd /tmp
      curl https://Nextclouddownload.nextcloud.com/server/releases/nextcloud-19.0.0.zip -o /var/www/html/nextcloud-19.0.0.zip
      sudo unzip /var/www/html/nextcloud-19.0.0.zip
      sudo dnf install -y unzip 
      sudo yum install -y policycoreutils-python-utils 
      sudo mkdir /etc/httpd/conf.d/nextcloud.conf
      sudo vi /etc/httpd/conf.d/nextcloud.conf/ALIAS.D
      sudo mkdir /var/www/html/nextcloud/data
      sudo chown -R apache:apache nextcloud
      sudo mv /tmp/nextcloud /var/www/html/
      sudo cd .. /var/www/html/nextcloud
      sudo chown -R apache:apache /var/www/html/nextcloud/
      sudo chgrp -R apache /var/lib/php/{opcache,session,wsdlcache}
      sudo yum install -y policycoreutils-python-utils
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/data(/.*)?'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/config(/.*)?'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/apps(/.*)?'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.htaccess'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.user.ini'
      sudo restorecon -Rv '/var/www/html/nextcloud/'
      setsebool -P httpd_can_network_connect_db 1
      setsebool -P httpd_execmem 1
      sed -i '/^memory_limit =/s/=.*/= 512M/' /etc/php.ini
      sudo yum install php-xmlwriter 
      yum install -y php-opcache
      yum install -y redis
      #dnf install -y dhcp-server

      SHELL
    
end
