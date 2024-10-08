## Tomcat Server deploy
---

### 1. Install Java
```
sudo apt update
sudo apt upgrade -y
```
sudo apt install default–jdk
or 
sudo apt install openjdk-11-jdk -y

java -version

---

### 2. Create a file for the Java environment Variable

```
sudo nano /etc/environment.d/java.conf
JAVA_HOME="/usr/lib/jvm/java-11-openjdk-amd64"
PATH="$JAVA_HOME/bin:$PATH"
source /etc/environment.d/java.conf
echo $$JAVA_HOME

```
---

### 3. Create Tomcat User

```
sudo useradd -r -m -U -d /opt/tomcat -s /bin/false tomcat

```

---

### 4. Install Tomcat on Ubuntu

```

cd /tmp
wget https://downloads.apache.org/tomcat/tomcat-9/v9.0.93/bin/apache-tomcat-9.0.93.tar.gz 

sudo mkdir -p /opt/tomcat

cd /opt/tomcat
sudo tar xzvf /tmp/apache-tomcat-9.0.*tar.gz -C /opt/tomcat --strip-components=1


sudo chown -R tomcat: /opt/tomcat/
sudo chmod -R 755 /opt/tomcat

```

---

### 5. Create a Systemd file

```
sudo nano /etc/systemd/system/tomcat.service

[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking
User=tomcat
Group=tomcat
Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh
Restart=on-failure

[Install]
WantedBy=multi-user.target

```
---

### 6. Reload the systemd daemon to apply the changes:

```
sudo systemctl daemon-reload
sudo systemctl start tomcat
sudo systemctl enable tomcat

```
---

### 7. Check the status of Tomcat server

```
sudo systemctl status tomcat

```
---

### 8. Setup Tomcat users and Webapp whitelisting

```
sudo nano /opt/tomcat/conf/tomcat-users.xml

<?xml version="1.0" encoding="UTF-8"?>

<tomcat-users>
 <role rolename="manager-gui"/>
 <role rolename="admin-gui"/>
 <role rolename="manager-script"/>
    <user username="admin" password="admin@123" roles="manager-gui,admin-gui"/>
    <user username="deploy" password="deploy@123" roles="manager-script"/>
</tomcat-users>


sudo nano /opt/tomcat/webapps/manager/META-INF/context.xml
<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->

sudo nano /opt/tomcat/webapps/host-manager/META-INF/context.xml

<!--
  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />
-->

```
---

### 9. Restart tomcat

```
sudo systemctl restart tomcat

```
---

### 10. Access Online 

```
http://server_domain_or_IP:8080/host-manager/html/

```
---

### 11. Deploy WAR File from Maven to Tomcat Server

```
Edit POM.xml file

<build>
    <plugins>
        <plugin>
            <groupId>org.apache.tomcat.maven</groupId>
            <artifactId>tomcat7-maven-plugin</artifactId>
            <version>2.2</version>
            <configuration>
                <url>http://tomcat-server-ip:8080/manager/text</url>
                <server>TomcatServer</server>
                <path>/your-app-context</path>
            </configuration>
        </plugin>
    </plugins>
</build>

```
---

### 12. Create settings.xml file under .m2 file

```
sudo nano /root/.m2/settings.xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
                              http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <servers>
        <server>
            <id>TomcatServer</id>
            <username>admin</username>
            <password>password</password>
        </server>
    </servers>
</settings>

```
---

### 13. Deploy the WAR file to the Tomcat server

```
 mvn clean install tomcat7:deploy

```
----

----
