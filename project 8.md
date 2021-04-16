<h1>Load Balancer Solution With Apache</h1>

Check to see that Apache is running on both web servers
![image](https://user-images.githubusercontent.com/22638955/114963983-ca6da180-9e65-11eb-8b11-5007b0d860ae.png)
![image](https://user-images.githubusercontent.com/22638955/114964110-fc7f0380-9e65-11eb-88f6-111aca4b087a.png)


Verify that the contents of the /mnt/apps directory on the NFS server are the contents present in /var/www directory of the web servers

<b>NFS</b>

![image](https://user-images.githubusercontent.com/22638955/114964633-06553680-9e67-11eb-8af9-07346c38089e.png)

<B>Web Server 1</b>

![image](https://user-images.githubusercontent.com/22638955/114964686-1d942400-9e67-11eb-8bed-463d9a98832f.png)

<b>Web Server 2</b>

![image](https://user-images.githubusercontent.com/22638955/114964728-300e5d80-9e67-11eb-8f7b-b00a71088640.png)

All the necessary TCP/UDP ports have also been configured on the AWS console for the NFS server, all web servers, and the DB server.

Both web servers can be accessed by clients using their public IP addresses.


For the Load Balancer, we would be spinning up an Ubuntu Server 20.04 EC2 instance
We would open TCP port 80, Install Apache Load Balancer on The server and configure it to point traffic coming to Load Balancer to go to both Web Servers

<b>As shown below, we have spun up a load balancing instance</b>

![image](https://user-images.githubusercontent.com/22638955/114966997-91383000-9e6b-11eb-9147-c14fd0e2d304.png)

Install Apache Load Balancer on the load balancer instance, and configure it to point traffic coming to LB to both Web Servers by running the below commands:
<b>
sudo apt update
  
sudo apt install apache2 -y

sudo apt-get install libxml2-dev

#Enable following modules:

sudo a2enmod rewrite

sudo a2enmod proxy

sudo a2enmod proxy_balancer

sudo a2enmod proxy_http

sudo a2enmod headers

sudo a2enmod lbmethod_bytraffic

#Restart apache2 service

sudo systemctl restart apache2  
</b>

![image](https://user-images.githubusercontent.com/22638955/114967734-e6287600-9e6c-11eb-85ff-0e00ef38cb2f.png)
![image](https://user-images.githubusercontent.com/22638955/114968007-6353eb00-9e6d-11eb-9507-8e5965dca8b2.png)

