# Install Jenkins on AWS EC2
Jenkins is a self-contained Java-based program, ready to run out-of-the-box, with packages for Windows, Mac OS X and other Unix-like operating systems. As an extensible automation server, Jenkins can be used as a simple CI server or turned into the continuous delivery hub for any project.


### Prerequisites
1. EC2 Instance 
   - With Internet Access
   - Security Group with Port `8080` open for internet
1. Java v1.8.x 

## Install Java
1. We will be using open java for our demo, Get the latest version from http://openjdk.java.net/install/
   ```sh
   yum install java-1.8*
   #yum -y install java-1.8.0-openjdk-devel
   ```

1. Confirm Java Version and set the java home
   ```sh
   java -version
   find /usr/lib/jvm/java-1.8* | head -n 3
   JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-<Java version which seen in the above output>
   export JAVA_HOME
   PATH=$PATH:$JAVA_HOME
    # To set it permanently update your .bash_profile
   vi ~/.bash_profile
   ```
   _The output should be something like this,_
    ```sh
   [root@~]# java -version
   openjdk version "1.8.0_151"
   OpenJDK Runtime Environment (build 1.8.0_151-b12)
   OpenJDK 64-Bit Server VM (build 25.151-b12, mixed mode)
   ```

## Install Jenkins
 You can install jenkins using the rpm or by setting up the repo. We will set up the repo so that we can update it easily in the future.
1. Get the latest version of jenkins from https://pkg.jenkins.io/redhat-stable/ and install
   ```sh
   yum -y install wget
   sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
   sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
   yum -y install jenkins
   ```

   ### Start Jenkins
   ```sh
   # Start jenkins service
   service jenkins start

   # Setup Jenkins to start at boot,
   chkconfig jenkins on
   ```

   ### Accessing Jenkins
   By default jenkins runs at port `8080`, You can access jenkins at
   ```sh
   http://YOUR-SERVER-PUBLIC-IP:8080
   ```
  #### Configure Jenkins
- The default Username is `admin`
- Grab the default password 
- Password Location:`/var/lib/jenkins/secrets/initialAdminPassword`
- `Skip` Plugin Installation; _We can do it later_
- Change admin password
   - `Admin` > `Configure` > `Password`
- Configure `java` path
  - `Manage Jenkins` > `Global Tool Configuration` > `JDK`  
- Create another admin user id

### Test Jenkins Jobs
1. Create “new item”
1. Enter an item name – `My-First-Project`
   - Chose `Freestyle` project
1. Under the Build section
	Execute shell: echo "Welcome to Jenkins Demo"
1. Save your job 
1. Build job
1. Check "console output"

**********************************************************************************************



#!/bin/bash
#
#Description: Jenkins Installation and Setup
#
#Author: Olufemi A.
#
#Date: June 2020


clear

echo
echo "Installation of Java in progress...please wait"
echo

sleep 5

yum install java-1.8.0-openjdk-devel -y

echo -e "\n make sure Java 8 is the default Java version\n"
echo -e "\n The next step is to enable the Jenkins repositor...please wait \n"
sleep 5

curl --silent --location http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo | sudo tee /etc/yum.repos.d/jenkins.repo

sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key

clear

echo
echo "now installing Jenkins, this may take a little while, please wait..."

sleep 7

yum -y install jenkins

#After the installation process is completed, start the Jenkins service

systemctl start jenkins

systemctl status jenkins

sudo systemctl enable Jenkins

#Adjust the Firewall
#If you are installing Jenkins on a remote CentOS server that is protected by a firewall you need to port 8080.

sudo firewall-cmd --permanent --zone=public --add-port=8080/tcp

sudo firewall-cmd --reload

#Setting Up Jenkins

echo -e "\nTo set up your new Jenkins installation, open your browser and type your domain or IP address followed by port 8080\n"
echo "http://your_ip_or_domain:8080"

echo "Use this link to access your jenkins server. http://$(ifconfig |grep Bcast |awk '{print $2}' |awk -F":" '{print $2}'):8080"
echo

sleep 5

echo
echo
echo "cat /var/lib/jenkins/secrets/initialAdminPassword"
echo
echo "You should see a 32-character long alphanumeric password as shown below:"
echo "2115173b548f4e99a203ee99a8732a32"
echo "Copy the password from your terminal, paste it into the Administrator password field and click Continue"
echo
echo "Thank you!"
echo
echo
echo "          Please use this link to access your jenkins server: http://$(ifconfig | head -2 | grep inet | awk '{print $2}'):8080"
echo
