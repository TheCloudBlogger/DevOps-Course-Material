## Jenkins Master Slave Setup using SSH

- First go to Jenkins Master Node GUI
- Go to Nodes
- Click on New Node to add a new node
##
  ![image](https://github.com/user-attachments/assets/044212b1-0663-454b-82b6-d00979bfae7f)
##
![image](https://github.com/user-attachments/assets/86f6272a-110a-4712-83d1-48557244bead)
##
```
 > **Note:** Refer Configure Private Key on Master section below to configure credentials
```
![image](https://github.com/user-attachments/assets/21c02e7e-95a2-4c11-b36d-64da18eed2ba)
##
![image](https://github.com/user-attachments/assets/0d21fc86-fda1-4ae9-b853-d20ff81a38d0)

## Configuration on Agent

- Create an Ubuntu Linux VM
- Install java
  
  ``` apt update
      apt upgrade -y
      apt install openjdk-11-jdk -y
  ```
- set up Java environment variable

  
```
nano /etc/environment.d/java.conf
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
PATH="$JAVA_HOME/bin:$PATH"
source /etc/environment.d/java.conf

```
- Setting up ssh

```
  apt install ssh -y
  /etc/init.d/ssh start
```
- Create a user on Agent
  
```
  useradd -m -d /home/jenkins jenkins
  sudo chown -R jenkins /home/jenkins/
  sudo chmod -R 755 /home/jenkins/

 ```
- Create ssh key for added jenkins user

``` su jenkins
    ssh-keygen
```
- you will notice 2 files created under .ssh folder as id_rsa and id_rsa.pub
- id_rsa contains the private key while id_rsa.pub contains public key
- the public key is placed in the ~/.ssh/authorized_keys file under the user's home directory. This file contains a list of public keys authorized to log into the server.
```
cd ~/.ssh
cat id_rsa.pub > authorized_keys

```

- We will copy the private key by supplying cat id_rsa

## Configure Private Key on Master 

- Need to put the private key into Master Jenkins node
- Click on Add to add the jenkins credetials
 ![image](https://github.com/user-attachments/assets/359efbf9-8ea5-4f6a-a2fd-7b2c594e29c7)
- Choose ssh user name with private key option
 ![image](https://github.com/user-attachments/assets/49547997-3fd0-4449-8540-f8805cf04ddf)
- Enter username as Jenkins and click add to add private key
- ![image](https://github.com/user-attachments/assets/b4044207-883c-4fc1-834b-256a0de49a89)


## This will ensure the agent is connected 
  


