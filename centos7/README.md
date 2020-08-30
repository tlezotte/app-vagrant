## How to run

[:cloud:](https://app.vagrantup.com/tlezotte/boxes/app-centos7) App-CentOS 7 box contains may common developement and devops tools. 

1. Create project directory

```
mkdir -p app-centos7/data
cd app-centos7
```

2. Add this content to `Vagrantfile`

```
Vagrant.configure("2") do |config|
  config.vm.box = "tlezotte/app-centos7"
  config.vm.box_version = "1.0.1"
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

- centos-release-scl
- epel-release
- httpd24
- rh-php73
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
vagrant package --output app-centos7-v1.0.1.box
```

2. Create a new version in [Vagrant Cloud](https://app.vagrantup.com/tlezotte/boxes/app-centos7/versions/new)

3. Add a provider, and upload the new package __(app-centos7-v1.0.1.box)__

4. In order for you or others to discover this box, you need to release it.
