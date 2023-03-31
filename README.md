# Multiple-Nodejs-with-Nginx
NGINX with multiple node apps into the same server for Node.js and development.

To use NGINX with multiple Node.js applications on the same server for development, you can follow these general steps:

1.Install Node.js on the server: If you haven't already done so, you'll need to install Node.js on the server. You can typically do this using the package manager for your operating system.

2. Install the Node.js apps: Install the Node.js apps you want to run on the server. You'll need to configure each app to listen on a different port.

3.Install and configure NGINX: Install NGINX on the server and configure it to act as a reverse proxy for the Node.js apps. In the NGINX configuration file, you'll define the different server blocks that will listen on different ports and forward requests to the appropriate Node.js app.

Example:-
Lets suppose we have Nodejs code in /var/www/html directory.
There are three services running on following ports and we need those service on mentioned domain :- 
1. http://127.0.0.1:7000  - admin -   http://admin.mydomain.com
2. http://127.0.0.1:7001 - api -  http://api.mydomain.com
3. http://127.0.0.1:7002 - web -   http://mydomain.com

So we have three services running on specific port and we are redirecting to a specific domain.
Here is nginx steps:
# cd /etc/nginx/sites-available
# vi default
server {
  listen 80;
  server_name admin.mydomain.com;
  location / {
    proxy_pass http://127.0.0.1:7000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

}

So In default file we mentioned one domain.
same we can define in domain conf file with following details:-
____________________________________________________________________
# vi api.mydomain.com.conf
server {
  root /var/www/html/;
  listen 80;
  server_name api.mydomain.com;
  location / {
    proxy_pass http://127.0.0.1:7001;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }

}
____________________________________________________________________
# vi mydomain.com/conf
server {
  root /var/www/html;
  listen 80;
  server_name mydomain.com www.mydomain.com;
  location / {
    proxy_pass http://127.0.0.1:7002;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
____________________________________________________________________
Test the configuration 
# nginx -t
Restart the service.
# service nginx restart or systemctl restart nginx 
