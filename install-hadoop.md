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
