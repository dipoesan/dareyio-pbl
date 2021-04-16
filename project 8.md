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
