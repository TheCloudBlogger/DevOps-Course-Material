## Setting up Maven and running a Webapp

#### Update and Upgrade apt

sudo apt update
sudo apt upgrade -y

#### Install Java
sudo apt install openjdk-11-jdk -y

#### Set up an environment variable for Java

sudo nano /etc/environment.d/java.conf
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
PATH="$JAVA_HOME/cli:$PATH"
source /etc/environment.d/java.conf

#### install Maven
sudo apt install maven 

#### Set up an environment variable for Maven

sudo nano /etc/environment.d/maven.conf
M2_HOME=/usr/share/maven
M2=/usr/share/maven/bin
PATH=$M2:$PATH
source /etc/environment.d/maven.conf
or 
sudo nano /etc/profile.d/maven.sh
export M2_HOME=/usr/share/maven
export M2=$M2_HOME/bin
export PATH=$M2:$PATH
source /etc/profile.d/maven.sh

mvn --version

#### Create Project directory
mkdir -p /opt/Maven-Projects
cd /opt/Maven-Projects

#### Create sample maven  Webapp
Link - https://maven.apache.org/archetypes/maven-archetype-webapp/
mvn archetype:generate -DarchetypeGroupId=org.apache.maven.archetypes -DarchetypeArtifactId=maven-archetype-webapp -DarchetypeVersion=1.5

A folder with name Maven-webapp-project will be created with Src and POM.XML file

## Build the WAR Package
mvn clean package
Maven-webapp-project.war file will be created under the target

#### Maven Lifecycles
https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html

validate - validate the project is correct and all necessary information is available
compile - compile the source code of the project
test - test the compiled source code using a suitable unit testing framework. These tests should not require the code be packaged or deployed
package - take the compiled code and package it in its distributable format, such as a JAR.
verify - run any checks on results of integration tests to ensure quality criteria are met
install - install the package into the local repository, for use as a dependency in other projects locally
deploy - done in the build environment, copies the final package to the remote repository for sharing with other developers and projects.



