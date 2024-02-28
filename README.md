# 🚀 Node.js Deployment on AWS 🛠️

> Steps to deploy a Node.js app using PM2, NGINX as a reverse proxy and an SSL from LetsEncrypt

## 1. Create Free AWS Account
Create free AWS Account at https://aws.amazon.com/

## 2. Create and Launch an EC2 instance and SSH into machine


## 3. Install Node and NPM
```
sudo apt update
sudo apt upgrade

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
Clear the Default file And Add the following 
```
  server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html;
        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
            proxy_pass http://localhost:3000;  #whatever port your app runs on
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
```
```
# Check NGINX config
sudo nginx -t

# Restart NGINX
sudo nginx -s reload
```
## 8. Set up DNS with Route 53 🌐 (Before you begin, purchase a domain from Hostinger or GoDaddy)

Step 1 :Search for Route 53 in AWS 

Step 2 :Click Create Hosted Zones   ( Route 53>Hosted zones>Create hosted zone)

Step 3 :Enter your Domain Name in the Domain Name Field in Hosted zone configuration

Step 4 :Click Create Record

Step 5 :Copy the nameservers in the from the table field NS (There will be 4 nameservers. Discard the dot at the end)

Step 6 : Paste it in your (Hostinger/GodDaddy) nameserver .

Step 7 : Now you can add your subdomin by clicking add record in your Route 53 and specify the IP address of your instance (A Record).

Step 8 : Hola, Your DNS is configured , It might take sometime to update the nameserver 




![image](https://github.com/Sreeragpa/NodeJS-Deployment-AWS/assets/84066738/3baf928d-1cdb-49f9-a03f-2f188a496178)



## 9. Add SSL with LetsEncrypt
```
sudo apt update
sudo apt install snapd
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Only valid for 90 days, test the renewal process with
certbot renew --dry-run
```




## EXTRAS
```
Stop: sudo systemctl stop nginx
Start: sudo systemctl start nginx
Restart: sudo systemctl restart nginx
Reload: sudo systemctl reload nginx
Disable: sudo systemctl disable nginx
Enable: sudo systemctl enable nginx
Check status: sudo systemctl status nginx
```
