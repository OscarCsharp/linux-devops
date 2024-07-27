
#install microsoft package and github
msBuild
msTest 
msTest Runner
GitHub Integration 


sudo apt-get update
sudo apt-get update && \ sudo apt-get install -y dotnet-sdk-8.0
sudo apt-get update && \sudo apt-get install -y aspnetcore-runtime-8.0
sudo apt-get install -y dotnet-runtime-8.0
sudo apt install zlib1g

#Add Github Credentials , make it global


# Prepare Your .NET Core API
#Create a Dockerfile
#In your .NET Core API project, create a Dockerfile in the root directory. This file defines how your .NET Core application is built into a Docker image.
#Example Dockerfile:

#Use the official .NET Core SDK image to build the app
FROM mcr.microsoft.com/dotnet/sdk:6.0 AS build
WORKDIR /app
COPY *.csproj ./
RUN dotnet restore
COPY . ./
RUN dotnet publish -c Release -o out
#Use the official .NET Core runtime image to run the app
FROM mcr.microsoft.com/dotnet/aspnet:6.0 AS runtime
WORKDIR /app
COPY --from=build /app/out ./
ENTRYPOINT ["dotnet", "YourApp.dll"]


# Create a New Jenkins Pipeline Job
#In the job configuration page, scroll to Pipeline.In the Definition dropdown, select Pipeline script from SCM.
#Set SCM to Git. Enter your GitHub repository URL and branch.
#Configure Pipeline Script In the Script Path field, enter Jenkinsfile (you will create this file next).

pipeline {
    agent any  // Use any available agent

    environment {
        DOCKER_IMAGE_NAME = 'dotnet-core-api'
        DOCKER_TAG = 'latest'
        API_PORT = '5000'  // Port to expose for the API
    }

    stages {
        stage('Checkout') {
            steps {
                // Clone the repository using Git credentials from Jenkins credentials store
                git credentialsId: 'github-jenkins', url: 'https://github.com/OscarCsharp/testdeployment.git', branch: 'main'
            }
        }
        stage('Restore') {
            steps {
                // Restore dependencies
                sh 'dotnet restore'
            }
        }
        stage('Build') {
            steps {
                // Build the application
                sh 'dotnet build -c Release'
            }
        }
        stage('Test') {
            steps {
                // Run tests
                sh 'dotnet test'
            }
        }
        stage('Publish') {
            steps {
                // Publish the application
                sh 'dotnet publish -c Release -o build'
            }
        }
        stage('Build Docker Image') {
            steps {
                // Build the Docker image
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_TAG}", '.')
                }
            }
        }
        stage('Deploy') {
            steps {
                // Run the Docker container with port mapping
                script {
                    sh """
                    docker stop dotnet-core-api || true
                    docker rm dotnet-core-api || true
                    docker run -d --name dotnet-core-api -p ${API_PORT}:80 ${DOCKER_IMAGE_NAME}:${DOCKER_TAG}
                    """
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



