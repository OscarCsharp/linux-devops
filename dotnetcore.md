
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

#Install Nginx
sudo apt-get install nginx
sudo service nginx start
sudo service nginx status

#Configure Nginx
sudo nano /etc/nginx/sites-available/default
#post everything and paste this code:
server {
    listen 80;
    location / {
    proxy_pass http://localhost:5000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection keep-alive;
    proxy_set_header Host $http_host;
    proxy_cache_bypass $http_upgrade;
    }
}


