 # Install and Run Nexus on Droplet
		  

Demo Project:
------------
* Run Nexus on Droplet and Publish Artifact to Nexus

Technologies used:
------------------
* Nexus, DigitalOcean, Linux, Java, Gradle, Maven

Project Description:
--------------------
* Install and configure Nexus from scratch on a cloud server

* Create New User on Nexus with relevant permissions

* Java Gradle Project: Build Jar & Upload to Nexus

* Java Maven Project: Build jar & Upload to Nexus

====================================================================================

        ----------------Nexus Installation----------------------------

1. Prerequisites:


	1. Create a Droplet on Digital Ocean
	2. Install Java : java-1.8.0-openjdk -y
	3. Open port Inboundrule 8081,Nexus run on port number 8081

====================================================================================

2. Implementation steps:


        1. cd /opt

        2. wget https://download.sonatype.com/nexus/3/nexus-3.41.1-01-unix.tar.gz (Nexus downloadble link)

        3. tar -zxvf nexus-3.41.1-01-unix.tar.gz (Unzim the file)

        4. mv /opt/nexus-3.13.0-01 / /opt/nexus ( Change the name as nexus)

====================================================================================

3. As a good security practice, it is not advised to run nexus service as root. So create new user called nexus also sudo access

         -> sudo adduser nexus 
         -> visudo : Give root permission to nexus user
         -> sudo chown -R nexus:nexus nexus
         -> sudo chown -R nexus:nexus sonartype-work

===================================================================================

4. Open /opt/nexus/bin/nexus.rc file, uncomment run_as-user parameter and set it as following

         -> vim /opt/nexus/bin/nexus.rc

====================================================================================

5. Add nexus as a service


        -> sudo ln -s /opt/nexus/bin/nexus /etc/init.d/nexus
	
======================================================================================

6. Login as a nexus user add start service


        -> su - nexus
        -> service nexus start
	
==========================================================================================

7. Login nexus server from browser on port 8081


        -> http://<nexus_server-public-ip>8081
	
=============================================================================================

8. Use default credentials to login


username: admin
password: admin123

===============================================================================================

Commands used:
-------------

apt update

apt install openjdk-8-jre-headless

apt install net-tools

cd /opt

wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz

tar -zxvf latest-unix.tar.gz

adduser nexus

chown -R nexus:nexus nexus-3.28.1-01

chown -R nexus:nexus sonatype-work

vim nexus-3.28.1-01/bin/nexus.rc

run_as_user="nexus"

su - nexus

/opt/nexus-3.28.1-01/bin/nexus start

ps aux | grep nexus

netstat -lnpt

===============================================================================================

Publish Artifact to Nexus Repository
====================================

1. Here i'm going to upload "Maven" and "Gradle" artifact jar files to Nexus Repository
	  
	-> Upload Jar File to existing hosted repository on Nexus
	
        -> Maven/Gradle command for pushing to remote repository
	
        -> Configure both tools to connect to Nexus (Nexus Repo URL + Credentials)
	
        -> Nexus User with permission to upload
	
2. Create Nexus User: 

![image](https://user-images.githubusercontent.com/91151185/196757006-b9fa6fc2-e4a2-4ddb-ba5e-252dcccb6238.png)

![image](https://user-images.githubusercontent.com/91151185/196757751-6022d7d6-d88e-4c90-bc2b-560137ae8b0e.png)

![image](https://user-images.githubusercontent.com/91151185/196757854-daa3c56d-3f33-47cc-a23c-235215020687.png)

3. Let's Create Role for the User Kiran

![image](https://user-images.githubusercontent.com/91151185/196759412-e2fabd53-9ed6-48aa-bc75-f50891ddb438.png)

![image](https://user-images.githubusercontent.com/91151185/196759626-561acef1-008f-43f8-9ee0-fcea0014ac42.png)

Add Privileges and save it

4. Assign the Role to the Kiran user and save it:

![image](https://user-images.githubusercontent.com/91151185/196760016-078abee9-3aea-4805-8f07-5b32e4cf05e8.png)

=========================================================================================================

Gradle Project configure with Nexus
===================================

![image](https://user-images.githubusercontent.com/91151185/196774366-fa1f6e16-94c9-4467-9e1c-9c3d88c1bd9b.png)

apply plugin: 'maven-publish'

publishing
        {
        publications {
            maven(MavenPublication) {
                    artifact("build/libs/my-app-$version"+".jar") {
                                extension'jar'
                    }
            }
        }
}
repositories {
    maven {
        name 'nexus'
        url "http://http://139.59.84.211/:8081/repository/maven-snapshots/"
        allowInsecureProtocol = true
        credentials {
            username = project.repoUser
            password = project.repoPassword
        }
    }
}

Here We have to create gradle.properties file and add the creatdentials
-----------------------------------------------------------------------
repoUser= ***
repoPassword= ***

setting.properties file : file name

![image](https://user-images.githubusercontent.com/91151185/196978968-bb6b366e-8220-406c-8e7e-6fa7df6b6fb3.png)

![image](https://user-images.githubusercontent.com/91151185/196979085-2c6769d9-fe4e-4ace-b88c-408f0137c04c.png)

![image](https://user-images.githubusercontent.com/91151185/196979394-7fd24833-b59d-4041-b1e9-1dff28fe9c23.png)

![image](https://user-images.githubusercontent.com/91151185/196979777-91b78618-c2ee-4e25-864d-6b3c909fdf2d.png)

Once the set up has been updated, Let's build the gradlew project
-----------------------------------------------------------------
./gradlew build   --> This command for build the artifact

./gradlew publish  --> This command for pushing the artifact into the Nexus repo

![image](https://user-images.githubusercontent.com/91151185/196980606-8effb331-8c43-4177-835f-1ab0d6e055d7.png)

Maven project configure with Nexus
-----------------------------------

We have to add plugin into the pom.xml file:
--------------------------------------------
<build>
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-deploy-plugin</artifactId>
                    <version>2.8.2</version>
                </plugin>
            </plugins>
        </pluginManagement>

        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-deploy-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
    
    <distributionManagement>
        <snapshotRepository>
            <id>nexus-snapshots</id>
            <url>http://139.59.84.211:8081/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
    
To set the Nexus login credentials into the maven project
---------------------------------------------------------
Search for .m2 folder in local system

cd ~

cd ls -a |grep .m2

cd .m2

# Create a file called settings.xml file inside .m2 folder and past the below script and save it

	<settings>
	  <servers>
	    <server>
		<id>nexus-snapshots</id>
		<username>nexus username</username>
		<password>password</password>
	     </server>
	   </servers>
	</settings>


Once the setup is done, Let's build the maven project and push into the Nexus repo
----------------------------------------------------------------------------------

mvn package -> This command is used for artifacting the project

mvn deploy -> This command is used for pushing the artifact into nexus repo

![image](https://user-images.githubusercontent.com/91151185/196989985-c13a695c-2146-4daf-8236-ac5ec93821ad.png)



























