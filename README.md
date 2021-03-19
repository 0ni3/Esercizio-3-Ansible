# Esercizio-3-Ansible

- Eseguire da zero tutte le azioni eseguite nei punti 1 e 2 tramite Ansible. 


articoli consultati:

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

creo la chiave rsa sulla macchina dove gira ansible

```
[vagrant@localhost ~]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/vagrant/.ssh/id_rsa):     
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/vagrant/.ssh/id_rsa.
Your public key has been saved in /home/vagrant/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:qYWR19Yp/6/2tmM84D4KtdEZb561kkdR6nlrgAOjwvc vagrant@localhost.localdomain
The key's randomart image is:
+---[RSA 2048]----+
|                .|
|       . . . . ..|
|      o .o+ o... |
|    .  +.oooo.+..|
|     o.oS  =.+o+o|
|      oo. . +o*.=|
|      .  E ..o+B |
|          .  o=*.|
|           .oo+=*|
+----[SHA256]-----+
```

ho copiato le chiavi su i nodi interessati e ho pingato i server, ma non riesco a connettere ansible!
```
[vagrant@localhost .ssh]$ scp /home/vagrant/.ssh/id_rsa.pub vagrant@192.168.1.199:/home/vagrant/.ssh/authorized_keys
vagrant@192.168.1.199's password: 
id_rsa.pub                                                                                                                            100%  411   521.8KB/s   00:00    
[vagrant@localhost .ssh]$ scp /home/vagrant/.ssh/id_rsa.pub vagrant@192.168.1.215:/home/vagrant/.ssh/authorized_keys
id_rsa.pub                                                                                                                            100%  411   404.5KB/s   00:00    
[vagrant@localhost .ssh]$ ansible -m ping all
host1 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).", 
    "unreachable": true
}
host2 | UNREACHABLE! => {
    "changed": false, 
    "msg": "Failed to connect to the host via ssh: Permission denied (publickey,gssapi-keyex,gssapi-with-mic,password).", 
    "unreachable": true
}

```

se provo a connettermi con ssh a una delle due macchine va a buon fine

```
[vagrant@localhost .ssh]$ ssh vagrant@192.168.1.199
Last login: Fri Mar 19 16:12:16 2021 from 192.168.1.181
[vagrant@localhost ~]$ ls -la
total 16
drwx------. 3 vagrant vagrant   95 Mar 11 14:38 .
drwxr-xr-x. 3 root    root      21 Apr 30  2020 ..
-rw-------. 1 vagrant vagrant 3697 Mar 19 16:12 .bash_history
-rw-r--r--. 1 vagrant vagrant   18 Apr  1  2020 .bash_logout
-rw-r--r--. 1 vagrant vagrant  193 Apr  1  2020 .bash_profile
-rw-r--r--. 1 vagrant vagrant  231 Apr  1  2020 .bashrc
drwx------. 2 vagrant vagrant   61 Mar 19 16:01 .ssh
```


```
[vagrant@localhost .ssh]$ cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.1.199 haproxy
192.168.1.215 jenkins

[vagrant@localhost .ssh]$ ping haproxy
PING haproxy (192.168.1.199) 56(84) bytes of data.
64 bytes from haproxy (192.168.1.199): icmp_seq=1 ttl=64 time=0.249 ms
64 bytes from haproxy (192.168.1.199): icmp_seq=2 ttl=64 time=0.357 ms
64 bytes from haproxy (192.168.1.199): icmp_seq=3 ttl=64 time=0.371 ms
--- haproxy ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1999ms
rtt min/avg/max/mdev = 0.249/0.325/0.371/0.058 ms

```

ho risolto creando la directory group_vars dentro /etc/ansible e settando l'utente vagrant
```
[vagrant@localhost ~]$ cd /etc/ansible/
[vagrant@localhost ansible]$ ls
ansible.cfg  group_vars  hosts  roles
[vagrant@localhost ansible]$ cd group_vars/
[vagrant@localhost group_vars]$ ls
servers
[vagrant@localhost group_vars]$ cat servers 
---
ansible_ssh_user: vagrant

```

ho pingato i server

```
[vagrant@localhost group_vars]$ ansible -m ping all
192.168.1.199 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
192.168.1.215 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    }, 
    "changed": false, 
    "ping": "pong"
}
```

ho scoperto un comando per aggiornare i server :)
```
ansible servers -m "shell" -a "yum update -y" -b

```




