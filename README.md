# Week 12 Lab
###### Erica and Cloue
---
# Setting up
1. In Digital Ocean, Create a new droplet.
2. In web-one server. Make sure to create a new user.
   - To create a new user ssh to new web-one server as root.
   - ``` ssh -i ~/.ssh/server-key root@(server ip address) ```
   - "Are you sure you want to continue" type: ``` yes ```
   - Add user: ``` useradd -ms /bin/bash user ``` ("user" name of user)
   - Add Password:``` passwd user ```
   - Add sudo permission to the user: ``` usermod -aG sudo user ```
   - ``` rsync --archive --chown=user:user ~/.ssh /home/user ```
   ---
   #### Disabling Root Login
   - ``` sudo vi /etc/ssh/sshd_config ```
   - Change configuration "PermitRootLogin" from "yes" to "no".
   ---
   #### Updating and Upgrading
   - Update:``` sudo apt update ```
   - Upgrade:``` sudo apt upgrade ```
   ---
   #### Restart SSH
   - ``` sudo systemctl restart ssh ```
   
   #### SSH to your server
   -  ``` ssh -i ~/.ssh/server-key user@123.123.12.123 ``` user@(server address)
   
   #### "You're Ready to Start!"
---
# 1. NGINX
---
## Installing NGINX in your server
1. To Install nginx in you server:  ``` sudo apt install nginx ```
---
## Creating HTML document in server
1. Create directory for the html: ``` sudo mkdir -p /var/www/(server ip address)/html ```
2. Create and Edit the html file: ``` sudo vim /var/www/(server ip address)/html/index.html ```
3. Inside undex.html:
    ``` 
    <!DOCTYPE html>
    <html lang="en">
    <html>
        <head>
            <meta charset="UTF-8" />
            <title> HTML TITLE </title>
        </head>
        <body>
            <h1> Header 1 </h1>
            <h2 style="color:red;"> Success! - NGINX </h2>
        </body>
     </html> ```
---
## Writing NGINX server block
1. Edit  ``` /etc/nginx/sites-available/(server ip address) ```
2. Content:
```
server {
        listen 80;
        listen [::]:80;

        root /var/www/(server ip address)/html;
        index index.html;

        server_name (server ip address);

        location / {
                try_files $uri $uri/ =404;
        }
}
```
---
## Uploading NGINX files to the Server
1. ``` sudo ln -s /etc/nginx/sites-available/(server ip address) /etc/nginx/sites-enabled/ ```
2. To test configuration: ``` sudo nginx -t ``` 'There should be no error'.
3. To check the contents of /sites-enabled, ``` cd ``` to ```/etc/nginx/sites-enabled ``` then: ``` ls -l ```
--- step 1
## Start/Restart NGINX service
1. To Check Status of NGINX: ``` systemctl status nginx ```
2. To Restart the NGINX: ``` sudo systemctl restart nginx.service ```
3. To Start the NGINX: ``` sudo systemctl start nginx.service ```
---
#### To see if this works, visit you server ip address.
---
# 2. Caddy
---
## Installing Caddy in you server
1. Copy and paste this to your command line: 
``` wget https://github.com/caddyserver/caddy/releases/download/v2.6.2/caddy_2.6.2_linux_amd64.tar.gz ```
2. To see if you have successfully downloaded the file: ``` ls ```. You should see the caddy tar.gz file.
---
## Creating HTML document in server
1. Create directory for the html: ``` sudo mkdir -p /var/www/(server ip address)/html ```
2. Create and Edit the html file: ``` sudo vim /var/www/(server ip address)/html/index.html ```
3. Inside undex.html:
    ``` 
    <!DOCTYPE html>
    <html lang="en">
    <html>
        <head>
            <meta charset="UTF-8" />
            <title> HTML TITLE </title>
        </head>
        <body>
            <h1> Header 1 </h1>
            <h2 style="color:red;"> Success! - Caddy </h2>
        </body>
     </html> ```
---
## Adding Caddy Configuration File
1. Edit CaddyFile: ``` sudo vim /etc/caddy/Caddyfile ```
2. Caddyfile content: 
    ```
    http://143.244.181.177 {
      root * /var/www
      file_server
    }
    ```
---
## Uploading Caddy files to the Server
1. ``` cd ``` to your home directory where Caddy.tar.gz
2. To unarchive the tar.gz: ``` tar xvf (The name of the Caddy File) ```
3. Copy to the caddy file to bin directory: ``` sudo cp caddy /usr/bin/ ```
4. Change the Caddy file's owner: ``` sudo chown root:root /usr/bin/caddy ```
---
## Start/Restart Caddy Service
1. Edit caddy.service file: ``` sudo vim /etc/systemd/system/caddy.service ```
2. Add Caddy.service content: 
    ```
    [Unit]
    Description=Serve HTML in /var/www using caddy
    After=network.target

    [Service]
    Type=notify
    ExecStart=/usr/bin/caddy run --config /etc/caddy/Caddyfile
    ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile
    TimeoutStopSec=5
    KillMode=mixed

    [Install]
    WantedBy=multi-user.target
    ```
3. Reload the service to save: ``` sudo systemctl daemon-reload ```
4. Start Caddy service: ``` sudo systemctl start caddy.service ```
5. To check Caddy service status: ``` sudo systemctl status caddy.service ```
---
#### To see if this works, visit you server ip address.
---

# Setting up Firewall
1. To show the list of application configurations: ``` sudo ufw app list ```
2. To allow all incoming HTTP and SSH connections:
    ``` sudo ufw allow http ``` or ``` sudo ufw allow 22 ```
3. And enable ufw: ``` sudo ufw enable ```
4. To check firewall status: ``` sudo ufw status ```
---
To see if this works, exit to your server ssh: ``` exit ```
and visit you server ip address in the browser.

