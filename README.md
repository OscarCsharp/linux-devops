# linux-devops
# Install Docker
sudo apt update
sudo apt upgrade -y
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt update
sudo apt install docker-ce -y
sudo systemctl status docker
sudo systemctl enable docker
sudo usermod -aG docker ${USER}
docker run hello-world

# Install Jenkins
 sudo apt update
 sudo apt upgrade
#Install Java
 sudo apt install default-jdk$ java -version
Install Jenkins
$ cd /tmp && wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
$ echo 'deb https://pkg.jenkins.io/debian-stable binary/' | sudo tee -a /etc/apt/sources.list.d/jenkins.list
$ sudo apt update
$ sudo apt install jenkins
