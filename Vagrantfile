# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "geerlingguy/centos7"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
  config.vm.network "forwarded_port", guest: 443, host: 8443, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  config.vm.synced_folder "data", "/vagrant"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    # Add user and group
    /usr/sbin/groupadd -g 1118 oriapp
    /usr/sbin/useradd -d /app001 -m -u 1118 -g oriapp oriapp
    echo "%oriapp ALL=(ALL) NOPASSWD: ALL" > /etc/sudoers.d/oriapp

    # https://github.com/CentOS/sig-cloud-instance-images/issues/15#issuecomment-252563831
    yum install -y yum-plugin-ovl

    # Minor versions (i.e. 6.7) don't receive updates, so need to do this manually
    yum -y update

    # Set timezone to central
    ln -s -f /usr/share/zoneinfo/America/Chicago /etc/localtime

    # Install software collections, epel and other utilities needed
    yum -y install centos-release-scl epel-release gcc libaio-devel wget unzip tar xz git mail telnet

    # Apparently the PHP 7.3 Software Collection is not yet included in centos-release-scl
    yum-config-manager --enable centos-sclo-rh-testing

    # Install apache 2.4 and apache modules
    yum -y install httpd24 httpd24-apr httpd24-apr-util httpd24-httpd httpd24-httpd-tools \
                    httpd24-libnghttp2 httpd24-mod_ssl httpd24-runtime

    # Install php and php modules
    yum -y install rh-php73 rh-php73-php-cli rh-php73-php-common rh-php73-php-devel \
                    rh-php73-php-gd rh-php73-php-gmp rh-php73-php-imap rh-php73-php-ldap rh-php73-php-mbstring \
                    rh-php73-php-mysqlnd rh-php73-php-odbc rh-php73-php-opcache rh-php73-php-pdo \
                    rh-php73-php-pear rh-php73-php-pecl-jsonc rh-php73-php-pecl-jsonc-devel \
                    rh-php73-php-pecl-memcache rh-php73-php-process rh-php73-php-soap rh-php73-php-tidy \
                    rh-php73-php-xml rh-php73-runtime libmcrypt libmcrypt-devel ssmtp rh-php73-php-fpm \
                    supervisor

    # Create directory structure for code
    mkdir /app001/{bin,apps,www,www-logs}

    # Install xdebug
    yes | /opt/rh/rh-php73/root/usr/bin/pecl install xdebug

    # Install mcrypt PHP extension
    echo "" | /opt/rh/rh-php73/root/usr/bin/pecl install mcrypt

    # Make the php-fpm "www" pool log errors to Apache error log
    perl -pi -e 's/^php_admin_value\[error_log\].*$/php_admin_value[error_log] = /' /etc/opt/rh/rh-php73/php-fpm.d/www.conf

    # Use UNIX socket for php-fpm "www" pool
    perl -pi -e 's/^listen\s*=.*$/listen = \/var\/opt\/rh\/rh-php73\/run\/php-fpm\/www.sock/' /etc/opt/rh/rh-php73/php-fpm.d/www.conf
    perl -pi -e 's/^;?listen.owner\s*=.*$/listen.owner = apache/' /etc/opt/rh/rh-php73/php-fpm.d/www.conf

    # Install composer
    wget -O /vagrant/composer-setup.php https://getcomposer.org/installer
    /opt/rh/rh-php73/root/usr/bin/php /vagrant/composer-setup.php --filename=composer --install-dir=/app001/bin
    rm -f /vagrant/composer-setup.php

    # Install node
    wget -O /vagrant/node.tar.xz https://nodejs.org/dist/v12.18.3/node-v12.18.3-linux-x64.tar.xz
    mkdir /app001/apps/node
    tar xf /vagrant/node.tar.xz -C /app001/apps/node --strip-components=1
    rm -f /vagrant/node.tar.xz

    # Install gulp globally
    npm install gulp-cli -g

    # Update root's PATH variable to include php and node
    echo -e "PATH=\"/app001/bin:/opt/rh/rh-php73/root/usr/bin:/opt/rh/rh-php73/root/usr/sbin:/app001/apps/node/bin:${PATH}\"" >> /etc/profile

    # Clean up
    yum clean all
    chown -R oriapp:oriapp /app001

    # Ansible
    yum -y install python3
    pip3 install ansible molecule docker testinfra yamllint flake8
  SHELL
end
