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
* visit this link : https://hadoop.apache.org/releases.html
* Chosee the version you want to install and click the link
* I have selected 3.2.1 binary : https://www.apache.org/dyn/closer.cgi/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
* Copy the link address of tar.gz file and download it
``` bash
hduser@ip-172-31-19-118:~$ wget https://mirrors.estointernet.in/apache/hadoop/common/hadoop-3.2.1/hadoop-3.2.1.tar.gz
```
* extract the file
``` bash
hduser@ip-172-31-19-118:~$ ls
hadoop-3.2.1.tar.gz

tar -zxvf hadoop-3.2.1.tar.gz

hduser@ip-172-31-19-118:~$ ls
hadoop-3.2.1  hadoop-3.2.1.tar.gz
```
### Setup hadoop
* This setup, also called pseudo-distributed mode, allows each Hadoop daemon to run as a single Java process. A Hadoop environment is configured by editing a set of configuration files
** bashrc
** hadoop-env.sh
** core-site.xml
** hdfs-site.xml
** mapred-site-xml
** yarn-site.xml
### Configure Hadoop Environment Variables in .bashrc
* open the .bashrc file
``` bash
nano .bashrc
```
* add the following configuration at the end of the file
```
#Hadoop Related Options
export HADOOP_HOME=/home/hduser/hadoop-3.2.1
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib/native"
```
* apply the changes to the current running environment 
``` bash
hduser@ip-172-31-19-118:~$ vi .bashrc
hduser@ip-172-31-19-118:~$ source .bashrc
hduser@ip-172-31-19-118:~$ echo $HADOOP_HOME 
/home/hdoop/hadoop-3.2.1
```
### Edit hadoop-env.sh File
* The hadoop-env.sh file serves as a master file to configure YARN, HDFS, MapReduce, and Hadoop-related project settings.
* When setting up a single node Hadoop cluster, you need to define which Java implementation is to be utilized. Use the previously created $HADOOP_HOME variable to access the hadoop-env.sh file:
* 



