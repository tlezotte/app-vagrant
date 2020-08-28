## How to run

[:cloud:](https://app.vagrantup.com/tlezotte/boxes/app-centos7) App-CentOS 7 box contains may common developement and devops tools. 

```
mkdir app-centos7
cd app-centos7
vagrant init tlezotte/app-centos7 --box-version 1.0.0
vagrant up
```

## Package for Cloud

1. Create a new version
```
vagrant package --output app-centos7-v1.0.0.box
```

2. Create a new version in [Vagrant Cloud](https://app.vagrantup.com/tlezotte/boxes/app-centos7/versions/new)

3. Add a provider, and upload the new package __(app-centos7-v1.0.0.box)__

4. In order for you or others to discover this box, you need to release it.
