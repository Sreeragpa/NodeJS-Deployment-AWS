# NodeJS-Deployment-AWS
# Node.js Deployment

> Steps to deploy a Node.js app using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Lauch an EC2 instance and SSH into machine


## 3. Install Node and NPM
```
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs

node --version
```

## 4. Clone your project from Github and Adding .env
```
git clone https://github.com/...repo
```

```
#To add env file to your folder in ec2 instance 
nano .env
```

## 5. Install dependencies and test app
```
sudo npm i pm2 -g   
pm2 start index

# Other pm2 commands
pm2 show app
pm2 status
pm2 restart app
pm2 stop app
pm2 logs (Show log stream)
pm2 flush (Clear logs)

# To make sure app starts when reboot
pm2 startup ubuntu
```

## 6. Setup Firewall
```
sudo ufw enable
sudo ufw status
sudo ufw allow ssh (Port 22)
sudo ufw allow http (Port 80)
sudo ufw allow https (Port 443)
```

## 7. Install NGINX and configure
```
sudo apt install nginx

sudo nano /etc/nginx/sites-available/default
```
Add the following to the location part of the server block
```
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:8001; #whatever port your app runs on
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
```
```
# Check NGINX config
sudo nginx -t

# Restart NGINX
sudo nginx -s reload
```
## 8. Manage your DNS Using Route 53 (Routing your domain to the instance ip) !!Before doing this you need to purchase a domain from hostinger or godaddy
Step 1 :Search for Route 53 in AWS 

Step 2 :Click Create Hosted Zones   ( Route 53>Hosted zones>Create hosted zone)

Step 3 :Enter your Domain Name in the Domain Name Field in Hosted zone configuration

Step 4 :Click Create Record

Step 5 :Copy the nameservers in the from the table field NS (There will be 4 nameservers. Discard the dot at the end)



![image](https://github.com/Sreeragpa/NodeJS-Deployment-AWS/assets/84066738/3baf928d-1cdb-49f9-a03f-2f188a496178)



## 9. Add SSL with LetsEncrypt
```
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
sudo apt-get install python3-certbot-nginx
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```
