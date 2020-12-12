# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure

Vagrant.configure("2") do |config|
  config.vm.box = "centos/8"  
  config.vm.synced_folder '../html', "/var/www/html/nextcloud/", type: "nfs", disabled: true 
  #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
  #config.vm.network "forwarded_port", guest: 5432, host: 54320 # nextcloud
  config.vm.network "private_network", type: "dhcp"
  config.vm.usable_port_range = 8000..8999
  config.vm.provider "virtualbox" do |v|
    v.memory = "4096"
    v.cpus = "1"
  end

  config.vm.define "dbmiljo" do |db|
    db.vm.box = "centos/8"
    db.vm.hostname = "dbmiljo.local"
    config.vm.provider "virtualbox" do |v|
      v.memory = "4024"
      v.cpus =" 1"
    end
    #config.vm.network "private_network", type: "dhcp"
    #config.vm.network "private_network", guest: 5433, host: 80, type: "dhcp"
    #config.vm.network "private_network", ip:"192.168.33.20", type: "dhcp" 
    config.vm.network "private_network", ip:"192.168.33.20", type: "dhcp" 
    config.vm.usable_port_range = 8000..8999
    #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
    config.vm.synced_folder '../html', "/var/www/html/nextcloud/", type: "nfs", disabled: true 
    config.vm.provision "shell", inline: <<-SHELL
    sudo dnf makecache
    sudo dnf install -y bind bind-utils 
    sudo dnf update -y
    sudo dnf install -y epel-release yum-utils unzip curl wget bash-completion policycoreutils-python-utils mlocate bzip2 httpd
    sudo dnf update -y
    #sudo dnf install -y httpd
    #sudo dnf install http://rpms.remirepo.net/enterprise/remi-release-8.rpm
    #sudo dnf install yum-utils
    sudo dnf install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
    sudo dnf install -y https://rpms.remirepo.net/enterprise/remi-release-8.rpm
    sudo dnf module reset php
    sudo dnf update -y
    sudo dnf install -y php php-gd php-mbstring php-intl php-pecl-apcu php-mysqlnd php-opcache php-json php-zip mariadb mariadb-server redis firewalld 
#php-redis php-imagick 
 #   sudo dnf install -y php php-gd php-mbstring php-intl php-pecl-apcu\php-mysqlnd php-opcache php-json php-zip
 #   sudo dnf install -y php-redis php-imagick
 #   sudo dnf install -y mariadb mariadb-server
 #   sudo dnf install -y redis
 #   sudo dnf install firewalld 
    #systemctl start ("firewalld | mariadb.service | php.service") 
    #systemctl enable (firewalld | mariadb.service | php.service")
    #sudo firewall-cmd --reload
    #sudo mkdir /var/lib/mysql/dbnextmiljo
    #mount -t /var/lib/mysql/dbmiljo
    SHELL
    #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
  end
      config.vm.define "nextmiljo" do |server|
      config.vm.hostname = "nextmiljo.local"
      config.vm.network "private_network", type: "dhcp"
      config.vm.network :private_network, ip: "192.168.33.10", type: "dhcp" 
      config.vm.usable_port_range = 8000..8999
      config.vm.synced_folder '../html', "/var/www/html/nextcloud/", type: "nfs", disabled: true 
     end    
      #config.vm.synced_folder "db/", "/var/lib/mysql/dbmiljo", disabled: false
      config.vm.provision "shell", inline: <<-SHELL
      cd /var/www/html
      curl -o nextcloud-17-latest.tar.bz2 https://download.nextcloud.com/server/releases/latest-17.tar.bz2
      tar -xvjf nextcloud-17-latest.tar.bz2
      mkdir nextcloud/data
      chown -R apache:apache nextcloud
      rm nextcloud-17-latest.tar.bz2
      #curl https://Nextclouddownload.nextcloud.com/server/releases/nextcloud-19.0.0.zip -o /var/www/html/nextcloud-19.0.0.zip
      #sudo unzip /var/www/html/nextcloud-19.0.0.zip
      #sudo dnf install unzip -y
      #sudo mv /tmp/nextcloud /var/www/html/
      #sudo cd ../var
      #sudo mkdir /var/www/html/nextcloud/data
      #sudo chown -R apache:apache /var/www/html/nextcloud/
      #sudo chgrp -R apache /var/lib/php/{opcache,session,wsdlcache}
      sudo yum install -y policycoreutils-python-utils
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/data'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/config(/.*)?'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/apps(/.*)?'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/3rdparty(/.*)?'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.htaccess'
      semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.user.ini'
      #restorecon -Rv '/var/www/html/nextcloud/' 
      SHELL
end 
