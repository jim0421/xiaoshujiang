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
baseurl=https://download.ceph.com/rpm-jewel/el7/noarch
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

#### preflight on ceph node
> install and enable ntp on ceph node
```
sudo yum install ntp ntpdate ntp-doc
sudo service ntpd start
sudo chkconfig ntpd on
```

> install ssh server
```
sudo yum install openssh-server
```

> create new ceph user and enable password-less sudo
```
ssh user@ceph-server
sudo useradd -d /home/{username} -m {username}
sudo passwd {username}

echo "{username} ALL = (root) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/{username}
sudo chmod 0440 /etc/sudoers.d/{username}
```

#### preflight on admin node
> enable password-less ssh (do not use sudo)
```
ssh-keygen

ssh-copy-id {username}@node1
ssh-copy-id {username}@node2
ssh-copy-id {username}@node3
```

> modify ~/.ssh/config
```
Host node1
   Hostname node1
   User {username}
Host node2
   Hostname node2
   User {username}
Host node3
   Hostname node3
   User {username}
```

#### preflight on all node
> navigate to /etc/sysconfig/network-scripts and ensure that the ifcfg-{iface} file has ONBOOT set to yes.
> navigate to /etc/sudoers and locate the Defaults requiretty setting. if you find it, change it to Defaults:ceph !requiretty or comment it out.
> navigate to /etc/selinux/config and set SELINUX=permissive

> install yum plugin priorities
```
sudo yum install yum-plugin-priorities
```

#### firewall setting
> on monitor node
```
sudo firewall-cmd --zone=public --add-service=ceph-mon --permanent
sudo firewall-cmd --reload
sudo iptables -A INPUT -p tcp -s {ip-address}/{netmask} --dport 6789 -j ACCEPT
sudo yum install iptables-services
sudo service iptables save
```

> on OSDs and MDSs
```
sudo firewall-cmd --zone=public --add-service=ceph --permanent
sudo firewall-cmd --reload
sudo iptables -A INPUT -p tcp -s {ip-address}/{netmask} --dport 6800:7300 -j ACCEPT
sudo yum install iptables-services
sudo service iptables save
```