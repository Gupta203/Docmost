Steps to Implementation:

1. AWS Account and Login.
•	Log in to the AWS Management Console.
2. Launch an EC2 Instance
•	Navigate to EC2 Dashboard:
•	Launch AMI: ubuntu and instance type : t2-large

3. Install Jenkins, Docker and Trivy:

#!/bin/bash
sudo apt update -y
wget -O - https://packages.adoptium.net/artifactory/api/gpg/key/public | sudo tee /etc/apt/keyrings/adoptium.asc
echo "deb [signed-by=/etc/apt/keyrings/adoptium.asc] https://packages.adoptium.net/artifactory/deb $(awk -F= '/^VERSION_CODENAME/{print$2}' /etc/os-release) main" | sudo tee /etc/apt/sources.list.d/adoptium.list
sudo apt update -y
sudo apt install temurin-17-jdk -y
/usr/bin/java --version
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc &gt; /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list &gt; /dev/null
sudo apt-get update -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status Jenkins

EC2 Public IP Address:8080
# In Jenkins server
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
 
 

4.Install Docker
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
After the docker installation, we create a sonarqube container (Remember to add 9000 ports in the security group).
sudo chmod 777 /var/run/docker.sock
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
 
5.Install Trivy
sudo apt-get install wget apt-transport-https gnupg lsb-release
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -
echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y

6.Install Plugins like JDK, Sonarqube Scanner, NodeJs, OWASP Dependency Check
Install Plugin
Goto Manage Jenkins →Plugins → Available Plugins →
Install below plugins
1 → Eclipse Temurin Installer
2 → SonarQube Scanner
3 → NodeJs Plugin
4 → Docker
5 → Docker commons
6 → Docker pipeline

 

6. Configure Java and Nodejs in Global Tool Configuration
Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(18)→ Click on Apply and Save
Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000, so <Public IP>:9000. Goto your Sonarqube Server.
Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token
Go to Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text.
 


7.Create an IAM user
 
 
8.Create an S3 bucket with simple basic settings
 
9.SMTP Email Credentials
Go to your Gmail and click on your profile
Then click on Manage Your Google Account –> click on the security tab on the left side panel you will get this page(provide mail password).
2-step verification should be enabled.
Search for the app in the search bar you will get app passwords 
Update the Required values into a env_file.txt

Sample ENV File

Please this command for APP_SECRET in .env
 
Go to Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret file.
 
Create a new pipeline
Goto Dashboard on Jenkins and Click on + New item
Docmost –> Pipeline and Click on Ok
 
 
