---
title: ceph installaion guide
tags: ceph
grammar_cjkRuby: true
---


欢迎使用 **{小书匠}(xiaoshujiang)编辑器**

#### preflight on admin node
> Install and enable the Extra Packages for Enterprise Linux (EPEL) repository:
```
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
```

> Add the Ceph repository to your yum configuration file at /etc/yum.repos.d/ceph.repo with the following command:
```
cat >/etc/yum.repos.d/ceph.repro
[ceph-noarch]
name=Ceph noarch packages
baseurl=https://download.ceph.com/rpm/el7/noarch
enabled=1
gpgcheck=1
type=rpm-md
gpgkey=https://download.ceph.com/keys/release.asc
```

> Update your repository and install ceph-deploy:
```
sudo yum update
sudo yum install ceph-deploy
```

#### preflight on 