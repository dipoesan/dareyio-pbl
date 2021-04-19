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

