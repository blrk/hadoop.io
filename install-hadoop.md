### Hadoop: Setting up a Single Node Cluster
* Create an AWS EC2 Instance
* Image : Ubuntu Server 18.04 LTS (HVM)
* Instance Type : t2.micro
* Add Storage : size 10 GB
### Login into the instance 
* Note : Windows users can use [MobaXterm](https://mobaxterm.mobatek.net/)
* (No need to convert the pem key file)
* Linux users do the following steps
``` bash
chmod 400 hadoop.pem
ssh -i hadoop.pem ubuntu@<public-ip-of-instance>
```
* switch to root user mode
``` bash
sudo su -
```
### Create a SSH keypair
* run the following commands in the terminal of hadoop node
``` bash
ubuntu@ip-172-31-90-231:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ubuntu/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/ubuntu/.ssh/id_rsa.
Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:ixxSdlhSB+TFIiAZuSUKieQDBUA1coJkuugthfU+8vk ubuntu@ip-172-31-90-231
The key's randomart image is:
+---[RSA 2048]----+
|@@=B...o=oo      |
|X B.o .=.o.      |
|o+ =  +.o.       |
|o.= .o .         |
|o. .... S        |
|. o .o o .       |
| o o oo .        |
|  . o o          |
|     o.E         |
+----[SHA256]-----+

ubuntu@ip-172-31-90-231:~$ ls .ssh/
authorized_keys  id_rsa  id_rsa.pub
```
### Add the public key to the authorized_keys file and verify 
``` bash
ubuntu@ip-172-31-90-231:~$ cat .ssh/id_rsa.pub >> .ssh/authorized_keys 
ubuntu@ip-172-31-90-231:~$ ssh localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:vL7CqrCG+NOa1R8u71z495qIsor33rIGGcUf/uibups.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'localhost' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 5.3.0-1035-aws x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Thu Oct  1 02:03:50 UTC 2020

  System load:  0.0               Processes:           95
  Usage of /:   11.7% of 9.63GB   Users logged in:     1
  Memory usage: 17%               IP address for eth0: 172.31.90.231
  Swap usage:   0%


0 packages can be updated.
0 updates are security updates.

New release '20.04.1 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


Last login: Thu Oct  1 01:58:30 2020 from 157.46.114.116
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

ubuntu@ip-172-31-90-231:~$ exit
logout
Connection to localhost closed.
```

