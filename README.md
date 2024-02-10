# Install SonarQube in Ubuntu Linux 

### 1. Install OpenJDK 11
```bash
sudo apt-get update
java -version
sudo apt-get install openjdk-11-jdk -y
```
### 2. Install and Configure PostgreSQL
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
sudo apt-get update
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
sudo apt install postgresql postgresql-contrib -y
sudo systemctl enable postgresql
sudo systemctl start postgresql
sudo systemctl status postgresql
sudo passwd postgres
su - postgres
createuser sonar
psql
ALTER USER sonar WITH ENCRYPTED password 'my_strong_password';
CREATE DATABASE sonarqube OWNER sonar;
GRANT ALL PRIVILEGES ON DATABASE sonarqube to sonar;
\dl
\l
\q
exit
```

### 3. Download and Install SonarQube
```bash
sudo apt-get install zip -y
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.6.1.59531.zip
ls
sudo unzip sonarqube-9.6.1.59531.zip
sudo mv sonarqube-9.6.1.59531 sonarqube
sudo mv sonarqube /opt/
```

### 4. Add SonarQube Group and User
```bash
sudo groupadd sonar
sudo useradd -d /opt/sonarqube -g sonar sonar
sudo chown sonar:sonar /opt/sonarqube -R
cd /opt/
ls
ll
```

### 5. Configure SonarQube
```bash
sudo apt install vim -y
sudo vim /opt/sonarqube/conf/sonar.properties
     sonar.jdbc.username=sonar
     sonar.jdbc.password=my_strong_password
     sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
sudo vim /opt/sonarqube/bin/linux-x86-64/sonar.sh
     RUN_AS_USER=sonar
```


### 6. Setup Systemd service
```bash
sudo vim /etc/systemd/system/sonar.service
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target


sudo systemctl daemon-reload
sudo systemctl enable sonar
sudo systemctl start sonar
sudo systemctl status sonar
```

### 7. Modify Kernel System Limits
```bash
cd /opt/sonarqube/logs
cat sonar.log
sudo vim /etc/sysctl.conf

vm.max_map_count=262144
fs.file-max=65536
ulimit -n 65536
ulimit -u 4096

sudo reboot
```
### 8. Access SonarQube Web Interface
```bash
http://IP:9000
```
Log in with username admin and password admin. SonarQube will prompt you to change your password.


[Click to do it with video.](https://drive.google.com/file/d/1IsIoMI6sAIJkCgy3UcAf20k0h6-dQt2k/view?usp=sharing)

or

[Click to do it with video.](https://www.youtube.com/watch?v=GTCQdgtd9mo)