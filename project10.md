## Load Balancer Solution With Nginx and SSL/TLS

We would create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 - this port is used for secured HTTPS connections)

![image](https://user-images.githubusercontent.com/22638955/115248577-b8b72300-a11f-11eb-8711-8c0f41ed7386.png)

![image](https://user-images.githubusercontent.com/22638955/115248912-05026300-a120-11eb-984b-36753d92e4db.png)

Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses

![image](https://user-images.githubusercontent.com/22638955/115249951-06805b00-a121-11eb-8f32-cf67825420a3.png)

Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers by running the commands below - 

<b>sudo apt update</b>

<b>sudo apt install nginx</b>

![image](https://user-images.githubusercontent.com/22638955/115251201-38de8800-a122-11eb-9320-7f73ee5a6be9.png)

![image](https://user-images.githubusercontent.com/22638955/115251303-527fcf80-a122-11eb-8ef2-4f2ad624ff9b.png)

Configure Nginx LB using Web Servers’ names defined in /etc/hosts

<b>sudo vi /etc/nginx/nginx.conf</b>

#insert following configuration into http section

 upstream myproject {
    server Web1 weight=5;
    server Web2 weight=5;
  }

server {
    listen 80;
    server_name www.domain.com;
    location / {
      proxy_pass http://myproject;
    }
  }

#comment out this line
#       include /etc/nginx/sites-enabled/*;

![image](https://user-images.githubusercontent.com/22638955/115251851-d89c1600-a122-11eb-9490-bd80adf4956c.png)

Restart Nginx and make sure the service is up and running using the commands below -

<b>sudo systemctl restart nginx</b>

<b>sudo systemctl status nginx</b>

## Register a new domain name and configure secured connection using SSL/TLS certificates

Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)

Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP. When you want to associate your domain name - it is better to have a static IP address that does not change after reboot. 

![image](https://user-images.githubusercontent.com/22638955/115253499-57de1980-a124-11eb-96f0-fe2054d71280.png)

Associated the elastic IP with the domain i bought by changing the DNS settings of my domain as shown below -

![image](https://user-images.githubusercontent.com/22638955/116260876-027eb980-a76f-11eb-931b-0fa2d717010d.png)

Check that your Web Servers can be reached from your browser using new domain name using HTTP protocol - http://<your-domain-name.com>

![image](https://user-images.githubusercontent.com/22638955/116340192-a64f8000-a7d6-11eb-885b-1926585709c6.png)

Configure Nginx to recognize your new domain name
Update your "nginx.conf" with "server_name www.<your-domain-name.com>" instead of "server_name www.domain.com"

![image](https://user-images.githubusercontent.com/22638955/116340377-fcbcbe80-a7d6-11eb-83c4-e27fb1384c69.png)

**When I was setting up my domain, I forgot to add the "@" symbol as my hostname, so I was having issues accessing my domain.**

Install certbot and request for an SSL/TLS certificate, also make sure snapd service is active and running

<b> systemctl status snapd</b>

![image](https://user-images.githubusercontent.com/22638955/116340677-75bc1600-a7d7-11eb-9425-d82c2eac23cc.png)

Install certbot

<b> sudo snap install --classic certbot</b>

![image](https://user-images.githubusercontent.com/22638955/116340866-c764a080-a7d7-11eb-8c95-27d64866d947.png)

Request your certificate (just follow the certbot instructions - you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it

sudo ln -s /snap/bin/certbot /usr/bin/certbot

sudo certbot --nginx

![image](https://user-images.githubusercontent.com/22638955/116341257-7acd9500-a7d8-11eb-8787-6bca2629062a.png)

Test secured access to your Web Solution by trying to reach

You shall be able to access your website by using HTTPS protocol (that uses TCP port 443) and see a padlock pictogram in your browser’s search string. Click on the padlock icon and you can see the details of the certificate issued for your website.

![image](https://user-images.githubusercontent.com/22638955/116341436-d4ce5a80-a7d8-11eb-9c8d-44003485b199.png)

Set up periodical renewal of your SSL/TLS certificate. By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

You can test renewal command in dry-run mode

<b>sudo certbot renew --dry-run</b>

![image](https://user-images.githubusercontent.com/22638955/116341840-8b323f80-a7d9-11eb-9cf7-6c1dd68eb9c5.png)

Best pracice is to have a scheduled job that would run a renew command periodically. We would configure a cronjob to run the command twice a day. To do so, lets edit the crontab file with the following command:

<b>crontab -e</b>

and add the following line - 

<b>* */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1</b>

![image](https://user-images.githubusercontent.com/22638955/116342582-c84b0180-a7da-11eb-9328-9aad5bfe1513.png)

You can always change the interval of this cronjob if twice a day is too often by adjusting schedule expression.

