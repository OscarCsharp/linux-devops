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

# Install Jenkins
 sudo apt update
 sudo apt upgrade
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
