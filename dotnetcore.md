
# Install Docker
sudo apt install -y docker.io
# Start and enable Docker
sudo systemctl start docker
sudo systemctl enable docker
# Add your user to the docker group (optional)
sudo usermod -aG docker $USER

# Install Jenkins
sudo apt install openjdk-17-jdk
# Add Jenkins repository and key
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
echo deb http://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list
# Install Jenkins
sudo apt update
sudo apt install -y jenkins
# Start and enable jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
#add jenkins user to docker	
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins

#check ssh statussudo systemctl status ssh
sudo systemctl status ssh


sudo visudo
#add this line
jenkins ALL=(ALL) NOPASSWD:ALL


sudo su - jenkins
ssh-keygen -t rsa -b 4096 -C "admin@nivaltec.co.za"
chmod 700 ~/.ssh
mkdir -p ~/.ssh
chmod 700 ~/.ssh
touch ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
echo ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys


sudo apt update
sudo apt install nano
sudo nano /etc/ssh/sshd_config
#enable the below
#PubkeyAuthentication yes
#AuthorizedKeysFile %h/.ssh/authorized_keys 



ssh-keyscan -H 102.130.116.102 >> /var/lib/jenkins/.ssh/known_hosts
ssh -i ~/.ssh/id_rsa jenkins@102.130.116.102


# Install Ngix
sudo apt-get update
sudo apt-get install nginx
#apache running stop it
sudo systemctl stop apache2
sudo systemctl restart nginx
sudo systemctl status nginx.service
sudo systemctl status nginx
sudo visudo



# Install Curl
sudo apt update
sudo apt install curl

sudo apt-get update
sudo apt-get install firewalld

sudo systemctl start firewalld
sudo systemctl enable firewalld
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload
sudo firewall-cmd --list-all







# Configure Jenkins

#Open your web browser and go to http://<vm-ip-address>:8080.
#Follow the on-screen instructions to unlock Jenkins using the initial admin password found at 
cat /var/lib/jenkins/secrets/initialAdminPassword
#Configure jenkins


#Click Build excutor status then then new node ,give it a name and  description then add number excutors not more no of CPUs available.
#Create credentials for SSH (username with private key ) on jenkins
#Create a Node to run and use the crendetials
# copy cat ~/.ssh/id_rsa
cat ~/.ssh/id_rsa
# create a node and th root should be : /var/lib/jenkins/agent-name
#ls -ld /var/lib/jenkins/agent-name/workspace/projectname

#create pipeline to test
pipeline {
    agent any
    stages {
        stage('Check Docker') {
            steps {
                script {
                    // Print Docker version
                    sh 'docker --version'

                    // Test Docker container
                    sh 'docker run --rm busybox echo "Docker is working!"'
                }
            }
        }
    }
}





# Create a Pipeline for .NET Core 8 API
dotnet --version
wget https://packages.microsoft.com/keys/microsoft.asc
sudo apt-key add microsoft.asc
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/$(lsb_release -cs) stable main" > /etc/apt/sources.list.d/mssql-release.list'

wget https://packages.microsoft.com/config/ubuntu/20.04/prod.list
sudo mv prod.list /etc/apt/sources.list.d/

sudo apt-get update
sudo apt-get install -y dotnet-sdk-8.0  # Replace with the required version
#create bashrc
nano ~/.bashrc
# add basicconfig
export PATH=$PATH:/path/to/dotnet
source ~/.bashrc


#create a folder to hold deployment files : 
sudo mkdir -p /var/lib/jenkins/workspace
sudo chown -R jenkins:jenkins /var/lib/jenkins
sudo chmod -R 755 /var/lib/jenkins
sudo mkdir /var/lib/jenkins/workspace/staging
sudo mkdir /var/lib/jenkins/workspace/production

sudo chown -R jenkins:jenkins /var/lib/jenkins
sudo chmod -R 755 /var/lib/jenkins

#create file for the api
sudo nano /etc/nginx/sites-available/myskol-api
#add the below
server {
    listen 5003;
    server_name devops-nivaltec.co.za;

    location / {
        proxy_pass http://localhost:5003;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

sudo ln -s /etc/nginx/sites-available/myskol-api /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
sudo systemctl restart nginx
sudo systemctl status nginx



#open ports
sudo apt update
sudo apt install ufw
sudo ufw enable
sudo ufw status
sudo ufw allow 5003/tcp
sudo ufw status verbose
sudo netstat -tuln | grep 5003

#change apache port
sudo nano /etc/apache2/ports.conf
sudo systemctl restart apache2
#create service 
nano /etc/systemd/system/myskol-api-staging.service
#----------------------------------copy paste------------------------------------------------------------
[Unit]
Description=myskol-api-staging service

[Service]
WorkingDirectory=/var/www/app/staging/myskol-api
ExecStart=/usr/bin/dotnet /var/lib/jenkins/workspace/staging/myskol-api/testdeployy.dll    
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=myskol-api
User=jenkins
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
#------------------------------------------------------------------------------------------------
sudo systemctl enable myskol-api-staging.service
sudo systemctl start myskol-api-staging.service


nano /etc/systemd/system/myskol-api-production.service
#----------------------------------copy paste------------------------------------------------------------
[Unit]
Description=myskol-api-production service

[Service]
WorkingDirectory=/var/www/app/production/myskol-api
ExecStart=/usr/bin/dotnet /var/lib/jenkins/workspace/production/myskol-api/testdeployy.dll
Restart=always
# Restart service after 10 seconds if the dotnet service crashes:
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=myskol-api
User=jenkins
Environment=ASPNETCORE_ENVIRONMENT=Production

[Install]
WantedBy=multi-user.target
#------------------------------------------------------------------------------------------------
sudo systemctl enable myskol-api-production.service
sudo systemctl start myskol-api-production.service


#permissions
sudo mkdir -p /var/lib/jenkins/workspace/
sudo chown -R jenkins:jenkins /var/lib/jenkins/workspace/
sudo chmod -R 755 /var/www/app



#Pipeline Script
pipeline {
    agent any
    parameters {
        choice(name: 'BRANCH_NAME', choices: ['staging', 'production'], description: 'Select the branch to deploy')
    }
    environment {
        PATH = "/usr/local/share/dotnet:$PATH"  // Ensure dotnet is in the PATH
        STAGING_PORT = '5003'
        PRODUCTION_PORT = '5004'
        STAGING_SERVICE_NAME = 'myskol-api-staging'
        PRODUCTION_SERVICE_NAME = 'myskol-api-production'
        STAGING_PATH = '/var/lib/jenkins/workspace/staging/myskol-api'
        PRODUCTION_PATH = '/var/lib/jenkins/workspace/production/myskol-api'
        NGINX_CONF_PATH = '/etc/nginx/sites-available/myskol-api'
        STAGING_BRANCH = 'staging'   // Define the branch for staging
        PRODUCTION_BRANCH = 'main'    // Define the branch for production
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    def branch = params.BRANCH_NAME
                    if (branch == 'staging') {
                        echo "Checking out the ${STAGING_BRANCH} branch for staging."
                        checkout scm: [$class: 'GitSCM', branches: [[name: "*/${STAGING_BRANCH}"]], userRemoteConfigs: [[url: 'https://github.com/OscarCsharp/testdeployment.git']]]
                    } else if (branch == 'production') {
                        echo "Checking out the ${PRODUCTION_BRANCH} branch for production."
                        checkout scm: [$class: 'GitSCM', branches: [[name: "*/${PRODUCTION_BRANCH}"]], userRemoteConfigs: [[url: 'https://github.com/OscarCsharp/testdeployment.git']]]
                    } else {
                        error("Unsupported branch: ${branch}. Please specify either 'staging' or 'production'.")
                    }
                }
            }
        }

        stage('Build and Test') {
            parallel {
                stage('Build and Test Staging') {
                    when {
                        expression { params.BRANCH_NAME == 'staging' }
                    }
                    steps {
                        dir('testdeploy') {
                            sh 'dotnet build -c Release'
                            sh 'dotnet test'
                        }
                    }
                }
                stage('Build and Test Production') {
                    when {
                        expression { params.BRANCH_NAME == 'production' }
                    }
                    steps {
                        dir('testdeploy') {
                            sh 'dotnet build -c Release'
                            sh 'dotnet test'
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            parallel {
                stage('Deploy to Staging') {
                    when {
                        expression { params.BRANCH_NAME == 'staging' }
                    }
                    steps {
                        dir('testdeploy') {
                            sh 'echo "Deploying to Staging Environment"'
                            sh "dotnet publish /var/lib/jenkins/dot-net-agent/workspace/myskol-api/testdeploy/testdeploy/testdeploy.csproj -c Release -o ${env.STAGING_PATH}"
                        }
                        sh '''
                        #!/bin/bash
                        sudo sed -i "s/Listen 80/Listen ${STAGING_PORT}/" ${NGINX_CONF_PATH}
                        sudo nginx -t
                        sudo systemctl restart nginx

                        # Check if the service exists
                        if systemctl list-units --type=service | grep -q ${STAGING_SERVICE_NAME}.service; then
                            echo "Staging service exists."
                            if ! systemctl is-active --quiet ${STAGING_SERVICE_NAME}; then
                                echo "Staging service is not running. Starting it now."
                                sudo systemctl start ${STAGING_SERVICE_NAME}
                            else
                                echo "Staging service is already running."
                            fi
                        else
                            echo "Staging service does not exist."
                        fi
                        '''
                    }
                }
                stage('Deploy to Production') {
                    when {
                        expression { params.BRANCH_NAME == 'production' }
                    }
                    steps {
                        dir('testdeploy') {
                            sh 'echo "Deploying to Production Environment"'
                            sh "dotnet publish /var/lib/jenkins/dot-net-agent/workspace/myskol-api/testdeploy/testdeploy/testdeploy.csproj -c Release -o ${env.PRODUCTION_PATH}"
                        }
                        sh '''
                        #!/bin/bash
                        sudo sed -i "s/Listen 80/Listen ${PRODUCTION_PORT}/" ${NGINX_CONF_PATH}
                        sudo nginx -t
                        sudo systemctl restart nginx

                        # Check if the service exists
                        if systemctl list-units --type=service | grep -q ${PRODUCTION_SERVICE_NAME}.service; then
                            echo "Production service exists."
                            if ! systemctl is-active --quiet ${PRODUCTION_SERVICE_NAME}; then
                                echo "Production service is not running. Starting it now."
                                sudo systemctl start ${PRODUCTION_SERVICE_NAME}
                            else
                                echo "Production service is already running."
                            fi
                        else
                            echo "Production service does not exist."
                        fi
                        '''
                    }
                }
            }
        }

        stage('Post-Deployment Validation') {
            parallel {
                stage('Validate Staging') {
                    when {
                        expression { params.BRANCH_NAME == 'staging' }
                    }
                    steps {
                        script {
                            def result = sh(script: "curl -I http://102.130.116.102:${STAGING_PORT}/swagger/index.html || echo 'Staging validation failed.'", returnStatus: true)
                            if (result != 0) {
                                echo 'Staging validation failed.'
                            } else {
                                echo 'Staging validation succeeded.'
                            }
                        }
                    }
                }
                stage('Validate Production') {
                    when {
                        expression { params.BRANCH_NAME == 'production' }
                    }
                    steps {
                        script {
                            def result = sh(script: "curl -I http://102.130.116.102:${PRODUCTION_PORT}/swagger/index.html || echo 'Production validation failed.'", returnStatus: true)
                            if (result != 0) {
                                echo 'Production validation failed.'
                            } else {
                                echo 'Production validation succeeded.'
                            }
                        }
                    }
                }
            }
        }

        stage('Prompt for Production Deployment') {
            when {
                expression { params.BRANCH_NAME == 'staging' }
            }
            steps {
                script {
                    def userInput = input(
                        id: 'userInput',
                        message: 'Do you want to deploy to Production?',
                        parameters: [choice(name: 'Deploy to Production', choices: ['Yes', 'No'], description: 'Choose whether to deploy to Production')]
                    )

                    if (userInput == 'No') {
                        echo 'Skipping Production deployment.'
                        currentBuild.result = 'SUCCESS'
                        return
                    }
                }
            }
        }

        stage('Deploy to Production') {
            when {
                expression { params.BRANCH_NAME == 'production' }
            }
            steps {
                dir('testdeploy') {
                    sh 'echo "Deploying to Production Environment"'
                    sh "dotnet publish /var/lib/jenkins/dot-net-agent/workspace/myskol-api/testdeploy/testdeploy/testdeploy.csproj -c Release -o ${env.PRODUCTION_PATH}"
                }
                sh '''
                #!/bin/bash
                sudo sed -i "s/Listen 80/Listen ${PRODUCTION_PORT}/" ${NGINX_CONF_PATH}
                sudo nginx -t
                sudo systemctl restart nginx

                # Check if the service exists
                if systemctl list-units --type=service | grep -q ${PRODUCTION_SERVICE_NAME}.service; then
                    echo "Production service exists."
                    if ! systemctl is-active --quiet ${PRODUCTION_SERVICE_NAME}; then
                        echo "Production service is not running. Starting it now."
                        sudo systemctl start ${PRODUCTION_SERVICE_NAME}
                    else
                        echo "Production service is already running."
                    fi
                else
                    echo "Production service does not exist."
                fi
                '''
            }
        }
    }
    post {
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed.'
        }
        always {
            echo 'Cleaning up...'
        }
    }
}

