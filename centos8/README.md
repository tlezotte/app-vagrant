## How to run

[:cloud:](https://app.vagrantup.com/tlezotte/boxes/app-centos8) App-CentOS 8 box contains may common developement and devops tools. 

1. Create project directory

```
mkdir -p app-centos8/data
cd app-centos8
```

2. Add this content to `Vagrantfile`

```
Vagrant.configure("2") do |config|
  config.vm.box = "tlezotte/app-centos8"
  config.vm.box_version = "1.0.0"
  config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
  config.vm.network "forwarded_port", guest: 443, host: 8443, host_ip: "127.0.0.1"
  config.vm.synced_folder "../data", "/vagrant-data"
end
```

3. Start Vagrant Box

```
vagrant up
```

### List of Software Installed

- PowerTools
- epel-release
- httpd
- php
  - xdebug
  - mcrypt
- composer
- node
  - gulp
- ansible
  - molecule

---

## Package for Cloud

1. Create a new version
```
vagrant package --output app-centos8-v1.0.0.box
```

2. Create a new version in [Vagrant Cloud](https://app.vagrantup.com/tlezotte/boxes/app-centos8/versions/new)

3. Add a provider, and upload the new package __(app-centos8-v1.0.0.box)__

4. In order for you or others to discover this box, you need to release it.
