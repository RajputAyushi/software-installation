# software-installation
✅ 🔧 Prerequisites
Ubuntu 22.04 EC2 instance

Instance Type: t3.large or higher (SonarQube needs at least 2 vCPU & 4GB RAM)

Open ports: 9000 (SonarQube UI), 5432 (if using external DB)

✅ 1. Install Java 17 (SonarQube requirement)
sudo apt update -y
sudo apt install openjdk-17-jdk -y
java -version
✅ 2. Create a new user for SonarQube
sudo adduser --system --no-create-home --group --disabled-login sonar
✅ 3. Install and Configure PostgreSQL
sudo apt install postgresql postgresql-contrib -y
sudo -u postgres psql
Inside PostgreSQL shell:
CREATE USER sonar WITH ENCRYPTED PASSWORD 'sonar@123';
CREATE DATABASE sonarqube OWNER sonar;
\q
✅ 4. Download and Install SonarQube
cd /opt
sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-10.4.1.88267.zip
sudo apt install unzip -y
sudo unzip sonarqube-10.4.1.88267.zip
sudo mv sonarqube-10.4.1.88267 sonarqube
sudo chown -R sonar:sonar /opt/sonarqube
✅ 5. Configure SonarQube
Edit config file:
sudo nano /opt/sonarqube/conf/sonar.properties
Uncomment & edit these lines:
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar@123
sonar.jdbc.url=jdbc:postgresql://localhost/sonarqube
✅ 6. Create Systemd Service for SonarQube
sudo nano /etc/systemd/system/sonarqube.service
Paste this:
[Unit]
Description=SonarQube service
After=network.target

[Service]
Type=simple
User=sonar
Group=sonar
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
Restart=always

[Install]
WantedBy=multi-user.target
Then:
sudo systemctl daemon-reexec
sudo systemctl daemon-reload
sudo systemctl enable sonarqube
sudo systemctl start sonarqube
✅ 7. Access SonarQube
Open your browser and go to:

http://<your-ec2-public-ip>:9000
Default login:

Username: admin

Password: admin
