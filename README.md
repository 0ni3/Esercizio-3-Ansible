# Esercizio-3-Ansible

https://www.miamammausalinux.org/2018/02/primi-passi-con-ansible-parte-1/


https://www.digitalocean.com/community/tutorials/initial-server-setup-with-centos-7
https://www.digitalocean.com/community/tutorials/how-to-install-and-configure-ansible-on-centos-7

controllo se la repository centos 7 epel è installata

```
sudo yum install epel-release
```

procedo a installare ansible

```
sudo yum install ansible
```


```
vagrant@localhost ~]$ ansible --version
ansible 2.9.18
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/home/vagrant/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Apr  2 2020, 13:16:51) [GCC 4.8.5 20150623 (Red Hat 4.8.5-39)]

```

in /etc/ansible/hosts

``` 
# This is the default ansible 'hosts' file.
#
# It should live in /etc/ansible/hosts
#
#   - Comments begin with the '#' character
#   - Blank lines are ignored
#   - Groups of hosts are delimited by [header] elements
#   - You can enter hostnames or ip addresses
#   - A hostname/ip can be a member of multiple groups

# Ex 1: Ungrouped hosts, specify before any group headers.

## green.example.com
## blue.example.com
## 192.168.100.1
## 192.168.100.10

# Ex 2: A collection of hosts belonging to the 'webservers' group

## [webservers]
## alpha.example.org
## beta.example.org
## 192.168.1.100
## 192.168.1.110

# If you have multiple hosts following a pattern you can specify
# them like this:

## www[001:006].example.com

[servers]
host1 ansible_ssh_host=192.168.1.215
host2 ansible_ssh_host=192.168.1.199

```

