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
### Update the instance
``` bash
sudo apt-get update -y
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
	* bashrc
	* hadoop-env.sh
	* core-site.xml
	* hdfs-site.xml
	* mapred-site-xml
	* yarn-site.xml
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
* Find the java path
``` bash
hduser@ip-172-31-19-118:~$ whereis jvm
jvm: /usr/lib/jvm

hduser@ip-172-31-19-118:~$ cd  /usr/lib/jvm
hduser@ip-172-31-19-118:/usr/lib/jvm$ ls
java-1.8.0-openjdk-amd64  java-8-openjdk-amd64
```
* copy : ``` /usr/lib/jvm/java-8-openjdk-amd64 ```
* open the following file in the vi editor
``` bash
hduser@ip-172-31-19-118:~$ vi $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
* seach the file : /export JAVA
* locate the line 
``` bash
# export JAVA_HOME=
```
* uncomment the line and paste the path
``` bash 
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```
* Save the file and quit

### Edit core-site.xml File
* The core-site.xml file defines HDFS and Hadoop core properties.
* To set up Hadoop in a pseudo-distributed mode, you need to specify the URL for your NameNode, and the temporary directory Hadoop uses for the map and reduce process.
* Open the core-site.xml
``` bash
vi $HADOOP_HOME/etc/hadoop/core-site.xml
```
* Add the following configuration to override the default values for the temporary directory and add your HDFS URL to replace the default local file system setting
```
<configuration>
<property>
  <name>hadoop.tmp.dir</name>
  <value>/home/hduser/tmpdata</value>
</property>
<property>
  <name>fs.default.name</name>
  <value>hdfs://127.0.0.1:9000</value>
</property>
</configuration>
```
### Edit hdfs-site.xml File
* The properties in the hdfs-site.xml file govern the location for storing node metadata, fsimage file, and edit log file. Configure the file by defining the NameNode and DataNode storage directories.
* Additionally, the default dfs.replication value of 3 needs to be changed to 1 to match the single node setup.
* open hdfs-site.xml file 
``` bash
vi $HADOOP_HOME/etc/hadoop/hdfs-site.xml
```
* Add the following configuration to the file and, if needed, adjust the NameNode and DataNode directories to your custom locations
``` 
<configuration>
<property>
  <name>dfs.data.dir</name>
  <value>/home/hduser/dfsdata/namenode</value>
</property>
<property>
  <name>dfs.data.dir</name>
  <value>/home/hduser/dfsdata/datanode</value>
</property>
<property>
  <name>dfs.replication</name>
  <value>1</value>
</property>
</configuration>
```
### Edit mapred-site.xml File
* Use the following command to access the mapred-site.xml file and define MapReduce values
* open $HADOOP_HOME/etc/hadoop/mapred-site.xml in a editor
``` bash
vi $HADOOP_HOME/etc/hadoop/mapred-site.xml
```
* Add the following configuration to change the default MapReduce framework name value to yarn
* Add the following configuration to the file
```
<configuration> 
<property> 
  <name>mapreduce.framework.name</name> 
  <value>yarn</value> 
</property> 
</configuration>
```
### Edit yarn-site.xml File
* The yarn-site.xml file is used to define settings relevant to YARN. It contains configurations for the Node Manager, Resource Manager, Containers, and Application Master
* Open the yarn-site.xml file
``` bash
vi $HADOOP_HOME/etc/hadoop/yarn-site.xml
```
* Add the following configuration to the file
``` 
<configuration>
<property>
  <name>yarn.nodemanager.aux-services</name>
  <value>mapreduce_shuffle</value>
</property>
<property>
  <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
  <value>org.apache.hadoop.mapred.ShuffleHandler</value>
</property>
<property>
  <name>yarn.resourcemanager.hostname</name>
  <value>127.0.0.1</value>
</property>
<property>
  <name>yarn.acl.enable</name>
  <value>0</value>
</property>
<property>
  <name>yarn.nodemanager.env-whitelist</name>   
  <value>JAVA_HOME,HADOOP_COMMON_HOME,HADOOP_HDFS_HOME,HADOOP_CONF_DIR,CLASSPATH_PERPEND_DISTCACHE,HADOOP_YARN_HOME,HADOOP_MAPRED_HOME</value>
</property>
</configuration>
```
### Format HDFS NameNode
* It is important to format the NameNode before starting Hadoop services for the first time
``` bash
hdfs namenode -format
```
* You can see the following output at the end of the scrolling messages
```
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at ip-172-31-19-118/172.31.19.118
************************************************************/
```
* shutdown notification signifies the end of the NameNode format process
### Start Hadoop Cluster
* Navigate to the following location
``` bash
hduser@ip-172-31-19-118:~$ cd hadoop-3.2.1/sbin/
```
* list the files
``` bash
hduser@ip-172-31-19-118:~/hadoop-3.2.1/sbin$ ls
FederationStateStore     start-all.sh         stop-balancer.sh
distribute-exclude.sh    start-balancer.sh    stop-dfs.cmd
hadoop-daemon.sh         start-dfs.cmd        stop-dfs.sh
hadoop-daemons.sh        start-dfs.sh         stop-secure-dns.sh
httpfs.sh                start-secure-dns.sh  stop-yarn.cmd
kms.sh                   start-yarn.cmd       stop-yarn.sh
mr-jobhistory-daemon.sh  start-yarn.sh        workers.sh
refresh-namenodes.sh     stop-all.cmd         yarn-daemon.sh
start-all.cmd            stop-all.sh          yarn-daemons.sh
```
* Run the file start-dfs.sh 
``` bash
hduser@ip-172-31-19-118:~/hadoop-3.2.1/sbin$ ./start-dfs.sh
Starting namenodes on [localhost]
Starting datanodes
Starting secondary namenodes [ip-172-31-19-118]
ip-172-31-19-118: Warning: Permanently added 'ip-172-31-19-118,172.31.19.118' (ECDSA) to the list of known hosts.
```
* The system takes a few moments to initiate the necessary nodes
* Once the namenode, datanodes, and secondary namenode are up and running, start the YARN resource and nodemanagers
``` bash
hduser@ip-172-31-19-118:~/hadoop-3.2.1/sbin$ ./start-yarn.sh
Starting resourcemanager
Starting nodemanagers
```
* check if all the daemons are active and running as Java processes* 
``` bash
hduser@ip-172-31-19-118:~/hadoop-3.2.1/sbin$ jps
27315 ResourceManager
26806 DataNode
27511 NodeManager
26617 NameNode
27083 SecondaryNameNode
27838 Jps
```
### END of Installation 
* Hurray !You have successfully installed Hadoop on Ubuntu and deployed it in a pseudo-distributed mode.
### Access Hadoop UI from the command line
``` bash
hduser@ip-172-31-19-118:~$ curl http://localhost:9870
```
### Access Hadoop UI from Browser
* In your browser URl type the following address : http://<public-ip-of-your-instance>:9870








