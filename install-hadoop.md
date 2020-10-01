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
### Create a hadoop user
``` bash
ubuntu@ip-172-31-19-118:~$ sudo addgroup hadoop

ubuntu@ip-172-31-19-118:~$ sudo adduser --ingroup hadoop hduser
Adding user `hduser' ...
Adding new user `hduser' (1002) with group `hadoop' ...
Creating home directory `/home/hduser' ...
Copying files from `/etc/skel' ...
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
Changing the user information for hduser
Enter the new value, or press ENTER for the default
	Full Name []: hadoop user
	Room Number []: 
	Work Phone []: 
	Home Phone []: 
	Other []: 
Is the information correct? [Y/n] y

ubuntu@ip-172-31-19-118:~$ sudo adduser  hduser sudo
Adding user `hduser' to group `sudo' ...
Adding user hduser to group sudo
Done.
```
### Login as hadoop user
``` bash
su - hduser
Password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
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
hduser@ip-172-31-19-118:~$ ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/home/hduser/.ssh/id_rsa): 
Created directory '/home/hduser/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/hduser/.ssh/id_rsa.
Your public key has been saved in /home/hduser/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:re2zzaBeUUs2PvTOXnXYBOfUHhFsx3JoMX3KoGH1wwE hduser@ip-172-31-19-118
The key's randomart image is:
+---[RSA 2048]----+
|           .E.*B*|
|          o .o+@B|
|         . o*+==*|
|         ..* +o=.|
|        S o + o +|
|         o . +  o|
|        . +   o .|
|         +.+ . . |
|       .o ooo .  |
+----[SHA256]-----+

hduser@ip-172-31-19-118:~$ ls .ssh/
id_rsa  id_rsa.pub

hduser@ip-172-31-19-118:~$ cat .ssh/id_rsa.pub >> .ssh/authorized_keys

hduser@ip-172-31-19-118:~$ ssh localhost
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:/aCK5yu5g2UgEAG2oNhjyMQ9ZW8AZ4uZJekVl/oAp5M.
Are you sure you want to continue connecting (yes/no)? yes


hduser@ip-172-31-19-118:~$ exit
logout
Connection to localhost closed.
```
### Update the isntance
``` bash
sudo apt install openjdk-8-jdk -y
```
### Install Java
* use the following command to install java
``` bash
sudo apt install openjdk-8-jdk -y
```
* check the verison of java
``` bash
ubuntu@ip-172-31-90-231:~$ java -version
openjdk version "1.8.0_265"
OpenJDK Runtime Environment (build 1.8.0_265-8u265-b01-0ubuntu2~18.04-b01)
OpenJDK 64-Bit Server VM (build 25.265-b01, mixed mode)
ubuntu@ip-172-31-90-231:~$ javac -version
javac 1.8.0_265
```
### Install hadoop



