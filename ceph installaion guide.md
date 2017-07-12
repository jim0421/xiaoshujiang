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

#### storage cluster on admin node
![ceph node architecture][1]
my-cluster will store all the keyring and log file, make sure you deploy node in the following directory.

> mkdir my-cluster
> cd my-cluster

Do not call ceph-deploy with sudo or run it as root.

> deploy monitor node
```
ceph-deploy new {monitor-node}
```
> install ceph package
```
ceph-deploy install node1 node2 node3 ...
```
> generate keyrings
```
ceph-deploy mon create-initial
```
> copy the configuration file and admin key to your admin node and Ceph Nodes
```
ceph-deploy admin node1 node2 node3
```
> deploy a manager daemon
```
ceph-deploy mgr create node1
```
> we assume you have an unused disk in each node called /dev/vdb, Be sure that the device is not currently in use and does not contain any important data.
```
ceph-deploy osd create node1:vdb node2:vdb node3:vdb
```
> check your cluster’s health
```
ssh node1 
sudo ceph health
sudo ceph -s
```


  [1]: ./images/Screen%20Shot%202017-07-12%20at%204.19.21%20PM_1.png "ceph node architecture"