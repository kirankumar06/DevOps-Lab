Welcome to DevOps Cloud:
------------------------

Demo Project:
-------------
Create server and deploy Java application on DigitalOcean and AWS EC2 cloud server

Technologies used:
------------------

* Intellij IDE, DigitalOcean, Linux, Java, Gradle

Project Description:
--------------------

* Setup and Configure a server on DigitalOcean and EC2 Instance
* Create and configure a new LinuxUser on the Droplet (Security best Prctice)
* Deploy and run a Java Gradle application on Drloplet and EC2 Instance

 
SetUp Server on DigitalOcean
-----------------------------

* Create a Droplet (Server on DigitalOcean)
* Based on Linux Ubuntu
 
Pre-Requisites:
---------------

* Create a DigitalOcean accout (Free Tier)


Steps to be consider while creating a Free tier 
-----------------------------------------------
* Before creating a Free tier server, we must have the ssh public key to authenticate the cloud server

* To generate ssh key open a terminal and run the following command
  -> ssh-keygen

* To check the ssh key " cat ~/.ssh/id_rsa.pub"

* We need to add ssh public key into the cloud free tier server to authenticate

* We have to open SSH Port = 22 Inbound Rules to communicate the server

* Final we can ssh into the server " ssh root@<public_ip> "


Steps to be consider while installing the software:
---------------------------------------------------
* Once the ssh into the server, there is no java application installed to check just type java and hit the enter

* Before installing the java, we must update the package manager 
  -> apt get update

* Command for installing Java " apt install openjdk-8-jre-headless -y
* After installing Java to check the version "java --version"


Steps to be consider while Deploy Application on Droplet
--------------------------------------------------------
* Build Jar File
* Copy to remote Server
* Run App on the remote Server

* Open Intelij Ide, open the Java project, while opening the java project automatically install all the dependencies file like Gradle

* We must install Java locally to run the application locally and build the java app jar file

* Once all the dependent files installed then run the build command

  -> ./gradlew build
 
* Once build command executed, build folder automatically generated

  -> Inside build folder, libs, java-react-example.jar is available

* After jar file is generated, we must copy the jar file from locally to remote server for that we have write scp command

  -> scp /bild/libs/java-react-example.jar root@<public_ip>:/root
  -> For coping the jar file locally to AWS EC2 instance
  -> scp -i <path of the .pem file> <path of the jar file> ec2-user@<public_ip>:/ec2-user

* Once jar file is copied into the remote server, we have cross verify the jarfile is avilable or not

* Once the Jar file is avilable we have run that jar file

  -> command for : java -jar java-react-example.jar or java -jar java-react-example.jar &

* Once the application is running the tomcat server is running on port number 7071 for that we have to open 7071 port on Inbound rules

* Check from the browser whether the application is running or not

  -> remote server public ip:port numbe -> <public_ip>:7071


To check the processid tomcat server port:
------------------------------------------

* We have to install netstat: apt install net-tools
* To check : netstat -lnpt

Create and configure a Linux user on a cloud server:
----------------------------------------------------
* Create a separate Linux User (Not use Root)

Security Best practice:
----------------------
-> Create separate User for every application

-> Give it only the permission it needs to run that Application

-> Don't work with the Root user 

Creating a new user in remote server or (droplet):
-------------------------------------------------
* adduser kiran

  It will ask adding user kiran to group, Adding new user kiran with group kiran, Creating home directory '/home/kiran'
  It will ask for password for new user kiran and retype the password then yes and hit the enter

* After adding new user kiran, then give sudo privileages to that user

  -> usermod -aG sudo kiran
  -> su -kiran
  -> $ = standard Linux User
  -> # = Root User

* When we ssh into the new user kirn it got permission denied because we haven't updated the local ssh public key to the new user 'kiran' in the remote server

* ssh into the root user then go to the new user kiran

  -> su -kiran

  -> Inside /home/kiran 

  -> ls -a , Here there are couple of files are avilable

  -> Create folder .ssh inside .ssh create file called authorized_keys

  -> sudo vim .ssh/authorized_key and past the public ssh key into this location and save it

  -> Now exit the kiran user and root user

  -> Try to login as new user 'kiran' into ssh like ssh kiran@<public_ip> now it will login as a new user kiran

