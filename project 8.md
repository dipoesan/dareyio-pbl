<h1>Load Balancer Solution With Apache</h1>

This is a continuation from project 7, as we are building upon the infrastructure we had already setup in that project.

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

<b>Configure the Load Balancer</b>

Edit the 000-default.conf file using - <b>sudo vi /etc/apache2/sites-available/000-default.conf</b>

Add this configuration into the section <VirtualHost *:80>  </VirtualHost>

<Proxy "balancer://mycluster">
               BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
               BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
               ProxySet lbmethod=bytraffic
               # ProxySet lbmethod=byrequests
        </Proxy>

        ProxyPreserveHost On
        ProxyPass / balancer://mycluster/
        ProxyPassReverse / balancer://mycluster/
        
![image](https://user-images.githubusercontent.com/22638955/114968899-2e489800-9e6f-11eb-90c4-a94be71f19a2.png)

Then restart the apache server

<b>sudo systemctl restart apache2</b>

Verify that the configuration works - we try accessing the load balancers public IP address or Public DNS name from our browser
![image](https://user-images.githubusercontent.com/22638955/114969701-db6fe000-9e70-11eb-91cd-6f0816fd8bef.png)
![image](https://user-images.githubusercontent.com/22638955/114969759-f3476400-9e70-11eb-894b-b1c42e139d0e.png)

Open two ssh/Putty consoles for both Web Servers and run following command:

<b>sudo tail -f /var/log/httpd/access_log</b>

Try to refresh your browser page http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php several times and make sure that both servers receive HTTP GET requests from your LB - new records must appear in each server’s log file. The number of requests to each server will be approximately the same since we set loadfactor to the same value for both servers - it means that traffic will be disctributed evenly between them.

If you have configured everything correctly - your users will not even notice that their requests are served by more than one server.

![image](https://user-images.githubusercontent.com/22638955/114971455-3eaf4180-9e74-11eb-8598-ffdab24ef91c.png)
![image](https://user-images.githubusercontent.com/22638955/114971506-5e466a00-9e74-11eb-855d-d4ff4a73658c.png)

<h2>Configure Local DNS Names Resolution</h2>
Sometimes it is tedious to remember and switch between IP addresses, especially if you have a lot of servers under your management. What we can do, is to configure local domain name resolution. The easiest way is to use /etc/hosts file, although this approach is not very scalable, but it is very easy to configure and shows the concept well. So let us configure IP address to domain name mapping for our LB.

Open this file on your LB server

sudo vi /etc/hosts
Add 2 records into this file with Local IP address and arbitrary name for both of your Web Servers

WebServer1-Private-IP-Address Web1

WebServer2-Private-IP-Address Web2

![image](https://user-images.githubusercontent.com/22638955/114972046-969a7800-9e75-11eb-8e4d-529f0fdb5f0f.png)

Now you can update your LB config file with those names instead of IP addresses.

<b>sudo vi /etc/apache2/sites-available/000-default.conf</b>

![image](https://user-images.githubusercontent.com/22638955/114972245-11fc2980-9e76-11eb-8ac6-97afd6fcb8f8.png)

You can try to curl your Web Servers from LB locally curl http://web1 or curl http://web2 - it shall work.

![image](https://user-images.githubusercontent.com/22638955/114973068-94391d80-9e77-11eb-9702-3e9e25bd77a6.png)

Remember, this is only internal configuration and it is also local to your LB server, these names will neither be ‘resolvable’ from other servers internally nor from the Internet.
