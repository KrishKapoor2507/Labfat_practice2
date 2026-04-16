SonarQube Installation on Ubuntu
PostgreSQL Setup
1. sudo apt install -y postgresql-common postgresql -y
2. sudo systemctl enable postgresql
3. sudo systemctl start postgresql
4. sudo -u postgres psql
5. CREATE ROLE sonaruser WITH LOGIN ENCRYPTED PASSWORD 'your_password';
6. CREATE DATABASE sonarqube;
7. GRANT ALL PRIVILEGES ON DATABASE sonarqube TO sonaruser;
8. GRANT ALL PRIVILEGES ON SCHEMA public TO sonaruser;
9. \q
Install SonarQube
1. sudo apt update
2. sudo apt install openjdk-17-jdk -y
3. sudo apt install unzip
4. wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-25.2.0.102705.zip
5. unzip sonarqube-25.2.0.102705.zip
6. sudo mv sonarqube-25.2.0.102705/ /opt/sonarqube
7. sudo adduser --system --no-create-home --group --disabled-login sonarqube
8. sudo chown -R sonarqube:sonarqube /opt/sonarqube
Install SonarScanner CLI
1. wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-7.0.1.4817-linux-x64.zip
2. unzip sonar-scanner-cli-7.0.1.4817-linux-x64.zip
3. sudo mv sonar-scanner-7.0.1.4817-linux-x64/ /opt/sonarscanner
4. sudo nano /opt/sonarscanner/conf/sonar-scanner.properties
5. Change: sonar.host.url=127.0.0.1
6. sudo chmod +x /opt/sonarscanner/bin/sonar-scanner
7. sudo ln -s /opt/sonarscanner/bin/sonar-scanner /usr/local/bin/sonar-scanner
8. sonar-scanner -v
Configure SonarQube
1. sudo nano /opt/sonarqube/conf/sonar.properties
2. Add:
sonar.jdbc.username=sonaruser
sonar.jdbc.password=your_password
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
sonar.web.host=0.0.0.0
sonar.web.port=9000
3. sudo nano /etc/sysctl.conf
Add:
vm.max_map_count=524288
fs.file-max=131072
4. sudo nano /etc/security/limits.d/99-sonarqube.conf
Add:
sonarqube - nofile 131072
sonarqube - nproc 8192
5. sudo ufw allow 9000/tcp
6. sudo ufw allow 22/tcp
7. sudo ufw reload
8. sudo ufw status
Set Up SonarQube Service
1. sudo nano /etc/systemd/system/sonarqube.service
2. Add:
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking
ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop
User=sonarqube
Group=sonarqube
Restart=always
LimitNOFILE=131072
LimitNPROC=8192

[Install]
WantedBy=multi-user.target
3. sudo systemctl daemon-reload
4. sudo systemctl enable sonarqube
5. sudo systemctl start sonarqube
6. sudo systemctl status sonarqube
7. sudo reboot now











Eclipse STS + Bugzilla Setup
Tools Overview
- Eclipse STS (Spring Tool Suite) → Development tool for building Spring Boot apps in Java
- Bugzilla → Bug tracking tool for reporting and managing software errors
- Workflow → Developer creates app → Tester finds bug → Bugzilla reports → Developer fixes
Installing Spring Tool Suite (STS)
Method 1: Add STS Plugin to Existing Eclipse
1. Open Eclipse
2. Help → Eclipse Marketplace
3. Search "Spring Tools"
4. Install Spring Tools 4 → Confirm → Accept license → Finish
5. Restart Eclipse
Method 2: Install STS Directly
1. Chrome → Search "Spring Tool Suite download"
2. Download Windows version
3. Extract ZIP → Extract Here/All
4. Run STS.exe or eclipse.exe
Creating Spring Boot Project
1. Launch STS → Select workspace
2. File → New → Spring Starter Project
3. Project Name: demo6
4. Type: Maven
5. Java Version: 17
6. Dependency: Spring Web
7. Finish
Create Controller
```
package com.example.demo;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class HomeController {
    @GetMapping("/")
    public String home() {
        return "Application Running Successfully";
    }
}
```
Run Application
1. Right-click Demo6Application.java → Run As → Spring Boot App
2. Browser → http://localhost:8080 → Output: Application Running Successfully
Bugzilla Setup
1. Open https://bugzilla.mozilla.org
2. Login/Create Account
3. File a Bug → Fill details (Summary, Description, Steps, Expected vs Actual)
4. Submit → Bug ID generated
