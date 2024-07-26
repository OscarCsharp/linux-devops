# linux-devops
#change hostname
hostnamectl set-hostname new-hostname
# Install Docker
sudo apt update

sudo apt install apt-transport-https curl gnupg-agent ca-certificates software-properties-common -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable"

sudo apt install docker-ce docker-ce-cli containerd.io -y
sudo usermod -aG docker $USER
docker version
sudo systemctl status docker
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl restart docker
docker run hello-world

sudo apt install docker-compose
docker compose version

# Setup SSH

# Install Jenkins
 sudo apt update
#Install Java
sudo apt install openjdk-17-jdk 
java -version
#Install Jenkins
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins

sudo systemctl stop jenkins.service
sudo systemctl start jenkins.service
sudo systemctl enable jenkins.service
service jenkins status

#check if user jenkins exists
usermod -aG docker jenkins

ufw allow 8080
ufw allow ssh
ufw enable
ufw status

#to show jenkins :
http://{ip or localhost}:8080
to get password:
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword

#change number of excutors to 0 for the default node to remove the warning the dashboard profile  (set label to be unique)

# Configure Jenkins to Use Docker
#Grant Jenkins User Docker Access:

#Install Docker Plugin for Jenkins:
#Go to the Available tab and search for "Docker".Install the "Docker" plugin.

#Configure Jenkins Docker Settings:
#In Jenkins, navigate to Manage Jenkins > Manage Nodes and Clouds > Configure Clouds. Click on Add a new cloud and select Docker.
#Configure the Docker cloud settings:
#Docker Host URI: Use unix:///var/run/docker.sock if Docker is running locally.
#Credentials: Leave this empty if connecting to a local Docker daemon.
#Save the configuration.

#Generate new ssh keys 
ssh-keygen
ssh-copy-id username@server-ip

# Create Freestyle project to test connection



# Configure Nodes:
1. Click Build excutor status then then new node ,give it a name and  description then add number excutors not more no of CPUs available.
2. Root Directorey : /home/ubuntu/agent
3. Add label eg : linux docker (plugins for that job)
4. Set Usage 
5. Launch Method : Use SSH(Select Known Host Verification) ,Set Availability
   
# Configure SSH For Jenkins
cd /var/lib
ls
cd jenkins
ls
mkdir -p /var/lib/jenkins/.ssh
ssh-keyscan -H hostname >> /var/lib/jenkins/.ssh/known_hosts
pwd
cat .ssh/known_hosts
clear 
ls -last (checks what owns ssh)
chown -R jenkins:jenkins /var/lib/jenkins/.ssh
ls -last .ssh

