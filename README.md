# linux-devops

# Install the OpenSSH Server
#check ssh is installed
sudo service ssh status
#if installed skip to generate keys
#By default, Ubuntu does not come with the OpenSSH server installed. You need to install it first.
sudo apt update
sudo apt install openssh-server
#Verify SSH Service Status
#Ensure that the SSH service is running and enabled to start on boot.
sudo systemctl status ssh
#You should see output indicating that the SSH service is active and running. If it is not running, start it with:
sudo systemctl start ssh
#To enable it to start on boot:
sudo systemctl enable ssh

#Gen keys
#check if exists 
ls ~/.ssh
#view public key
cat ~/.ssh/id_rsa.pub
#if does exist generate
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
#Add the Key to the SSH Agent:
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
cat ~/.ssh/id_rsa.pub
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
sudo systemctl restart ssh
#checkers used for authentication
ls -ld /home/*
ls -ld /home/*/.ssh
#use of the users to test ssh
ssh ubuntu@server_ip

#if  ssh test fails
#Configure SSH
#Manually Copy the Public Key
If ssh-copy-id doesn't work, you can manually copy the public key:
cat ~/.ssh/id_rsa.pub
#On the server, open or create the authorized_keys file in the .ssh directory for the user:
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
#Paste the public key into this file. Ensure each key is on its own line.
#Set Permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
sudo systemctl restart ssh
#Verify SSH Configuration
sudo nano /etc/ssh/sshd_config
#Ensure the following settings are correct: copy and paste then save
PubkeyAuthentication yes
AuthorizedKeysFile     %h/.ssh/authorized_keys

#Restart SSH Service
systemctl restart ssh
#Check File Ownership and Permissions
chown -R ubuntu:ubuntu ~/.ssh
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
#Replace ubuntu with the actual username on the server.

#Check SSH Agent
ssh-add ~/.ssh/id_rsa
#Check if the key is added:
ssh-add -l
#Test SSH Connection
After making these changes, try to connect again:
ssh username@serverip



# Install Docker
sudo su -
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
#to get password:
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword

#change number of excutors to 0 for the default node to remove the warning the dashboard profile  (set label to be unique)

# Configure Jenkins
sudo mkdir -p /var/lib/jenkins/.ssh
sudo chown -R jenkins:jenkins /var/lib/jenkins/.ssh
sudo chmod 700 /var/lib/jenkins/.ssh
sudo -u jenkins ssh-keyscan -H remote_host >> /var/lib/jenkins/.ssh/known_hosts
sudo -u jenkins cat /var/lib/jenkins/.ssh/known_hosts
sudo chown jenkins:jenkins /var/lib/jenkins/.ssh/known_hosts
sudo chmod 644 /var/lib/jenkins/.ssh/known_hosts
sudo -u jenkins ssh -i /var/lib/jenkins/.ssh/id_rsa jenkins@server_ip

# Configure Nodes:
#Click Build excutor status then then new node ,give it a name and  description then add number excutors not more no of CPUs available.
#Add ssh crendentials
2. Root Directorey : /home/ubuntu/agent
3. Add label eg : linux docker (plugins for that job)
4. Set Usage 
5. Launch Method : Use SSH(Select Known Host Verification) ,Set Availability

#Create test pipeline 
#Pipeline Script :
pipeline {
    agent any  // Run on any available agent

    stages {
        stage('Build') {
            steps {
                echo 'Hello, Jenkins!'  // Print a message to the console
            }
        }
        stage('Test') {
            steps {
                script {
                    // Run a test command (replace with your actual test command)
                    sh 'echo "Running tests..."'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
    }
}

   

