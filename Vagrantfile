# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure

Vagrant.configure("2") do |config|
  config.vm.box ="centos/8"#"treehouses/buster64"  
  config.vm.synced_folder '../var', "/var/www/html/nextcloud/",  type: "nfs", disabled: true
  #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
  #config.vm.network "forwarded_port", guest: 5432, host: 54320 # nextcloud
  #config.vm.network "public_network", ip: "192.168.33.4"
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
      v.memory = "1024"
      v.cpus =" 1"
    end
    #config.vm.network "private_network", type: "dhcp"
    #config.vm.network "private_network", guest: 5433, host: 80, type: "dhcp"
    #config.vm.network "private_network", ip:"192.168.33.20", type: "dhcp" 
    #systemctl start ("firewalld | mariadb.service | php.service") 
    #systemctl enable (firewalld | mariadb.service | php.service")
    #sudo firewall-cmd --reload
    #sudo mkdir /var/lib/mysql/dbnextmiljo
    #mount -t /var/lib/mysql/dbmiljo
     #config.vm.synced_folder "dbmiljo/", "/var/lib/mysql/dbmiljo", disabled: false
    #config.vm.network "public_network", ip:"192.168.33.20"
    config.vm.network "private_network", ip:"192.168.33.20", type: "dhcp"
    config.vm.network "forwarded_port", guest: 111, host: 2049, type: "nfs", disabled: true
    config.vm.network "forwarded_port", guest: 443, host: 8081, type: "nfs", disabled: true 
    config.vm.synced_folder '../var', "/var/www/html/nextcloud/", type: "nfs", disabled: true
    config.vm.synced_folder '../etc', "/etc/", type: "nfs", disabled: true
    config.vm.provision "shell", inline: <<-SHELL
    sudo dnf makecache
    sudo dnf install -y bind bind-utils
    sudo dnf update
    sudo dnf install -y epel-release yum-utils unzip curl wget bash-completion policycoreutils-python-utils mlocate bzip2 httpd 
    sudo dnf -y update
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/data(/.*)?'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/config(/.*)?'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/apps(/.*)?'
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.htaccess'      
    semanage fcontext -a -t httpd_sys_rw_content_t '/var/www/html/nextcloud/.user.ini'
    sudo curl -o https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm 
    sudo curl -o https://rpms.remirepo.net/enterprise/remi-release-8.rpm 
    sudo dnf module reset php
    sudo dnf update -y
    sudo dnf install -y php php-gd php-mbstring php-intl php-pecl-apcu php-mysqlnd php-opcache php-json php-zip mariadb mariadb-server redis firewalld
    SHELL
  end
      config.vm.define "nextmiljo" do |server|
      config.vm.hostname = "nextmiljo.local"
      #config.vm.network "public_network", ip:"192.168.33.20"
      config.vm.network "private_network", type: "dhcp"
      config.vm.network :private_network, ip: "192.168.33.10", type: "dhcp" 
      config.vm.network "forwarded_port", guest: 111, host: 2049, type: "nfs", disabled: true
      config.vm.network "forwarded_port", guest: 443, host: 8081, type: "nfs", disabled: true 
      config.vm.synced_folder '../var', "/var/www/html/nextcloud/", type: "nfs", disabled: true
      config.vm.synced_folder '../etc', "/etc/", disabled: true  
     end    
     
      config.vm.provision "shell", inline: <<-SHELL
      
      sudo dnf install -y php-mysqlnd php-xml php-zip  php-curl php-gd php-intl php-json php-ldap php-mbstring php-opcache httpd firewalld mariadb-server
      sudo dnf install -y --nogpgcheck https://download1.rpmfusion.org/free/el/rpmfusion-free-release-8.noarch.rpm
      sudo dnf install -y yum 
      sudo yum install -y wget
      sudo yum install -y unzip
      sudo wget -c https://download.nextcloud.com/server/releases/nextcloud-18.0.1.zip
      sudo unzip nextcloud-18.0.1 -d /var/www/html/
      sudo mkdir -p /var/www/html/nextcloud/data
      dnf update 
      sudo chown -R apache:apache /var/www/html/nextcloud/
      dnf install -y yum 
      sudo yum install -y policycoreutils-python-utils
      sudo yum install -y policycoreutils-python-utils
      setsebool -P httpd_can_network_connect_db 1
      setsebool -P httpd_execmem 
      sed -i '/^memory_limit =/s/=.*/= 512M/' /etc/php.ini
      sudo yum install -y php-xmlwriter 
      yum install -y redis
      
      SHELL
    
end
