# Configure Jenkins Slaves on AWS EC2
Jenkins is a self-contained Java-based program, ready to run out-of-the-box, with packages for Windows, Mac OS X and other Unix-like operating systems. As an extensible automation server, Jenkins can be used as a simple CI server or turned into the continuous delivery hub for any project.


### Prerequisites
1. Jenkins Master Running 
1. EC2 Instance - for slave 
   - With Internet Access
   - Security Group with Port `8080` open for internet
   - Java v1.8.x 

## Install Java
```sh
sudo yum -y install java-1.8.0-openjdk
```
## Setup Jenkins Slave
```sh
# Create user and add the user to wheel group
useradd jenkins-slave
# Create SSH Keys
sudo su - jenkins-slave
ssh-keygen -t rsa -N "" -f /home/jenkins-slave/.ssh/id_rsa
# The private and public keys will be created at these locations `/home/jenkins-slave/.ssh/id_rsa` and `/home/jenkins-slave-01/.ssh/id_rsa.pub`
cd .ssh
cat id_rsa.pub > authorized_keys
chmod 400 authorized_keys
exit
sudo su -
cd /etc/sudoers.d/
chmod 777 90-cloud-init-users
echo "jenkins-slave ALL=(ALL) NOPASSWD:ALL" >> 90-cloud-init-users
chmod 400 90-cloud-init-users
```

### Configuration on Master

```sh
sudo mkdir -p /var/lib/jenkins/.ssh
sudo cd /var/lib/jenkins/.ssh
sudo ssh-keyscan -H SLAVE-NODE-IP-OR-HOSTNAME >>/var/lib/jenkins/.ssh/known_hosts
sudo chown -R jenkins:jenkins /var/lib/jenkins/.ssh
sudo chmod 700 /var/lib/jenkins/.ssh/known_hosts
```

#### Configure the Slave using `Manage Jenkins`
Configure the node as shown here
`Manage Jenkins` > `Manage Nodes` > `New Node`
![Jenkins Master and Slave Configuration](https://raw.githubusercontent.com/miztiik/DevOps-Demos/master/setup-jenkins-slave/images/Slave-Node-Configuration-01.png)
### Test Jenkins Jobs
1. Create “new item”
1. Enter an item name – `My-First-Project`
   - Chose `Freestyle` project
1. Under `General` Section
   - Choose `Restrict where this project can be run`
     - Update your _jenkins slave label_ `Java` 
1. Under Build section
   Execute shell
   ```sh
   #!/bin/bash
   echo "_______________________________"
   echo "|                             |"
   echo "|           _nnnn_            |"
   echo "|          dGGGGMMb           |"
   echo "|         @p~qp~~qMb          |"
   echo "|         M|@||@) M|          |"
   echo "|         @,----.JM|          |"
   echo "|        JS^\__/  qKL         |"
   echo "|       dZP        qKRb       |"
   echo "|      dZP          qKKb      |"
   echo "|     fZP            SMMb     |"
   echo "|     HZM            MMMM     |"
   echo "|     FqM            MMMM     |"
   echo "|   __| '.        |\dS'qML    |"
   echo "|   |    '.       | ' \Zq     |"
   echo "|  _)      \.___.,|     .'    |"
   echo "|  \____   )MMMMMP|   .'      |"
   echo "|       '-'       '--' hjm    |"
   echo "_______________________________"
   ```
1. Save your job 
1. Build job
1. Check "console output"

