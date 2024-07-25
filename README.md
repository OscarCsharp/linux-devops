# linux-devops
# Install Docker
sudo apt update
sudo apt upgrade -y
sudo apt install apt-transport-https ca-certificates curl software-properties-common -y






$ sudo apt update
$ sudo apt upgrade
Install Java
$ sudo apt install default-jdk
$ java -version
Install Jenkins
$ cd /tmp && wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
$ echo 'deb https://pkg.jenkins.io/debian-stable binary/' | sudo tee -a /etc/apt/sources.list.d/jenkins.list
$ sudo apt update
$ sudo apt install jenkins
