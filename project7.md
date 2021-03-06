## PROJECT 7 - DEVOPS TOOLING WEBSITE SOLUTION

Launch 4 EC2 instances that will serve as the 3 Web Servers, and 1 NFS server needed for this project (Use RHEL for the 4 servers).


Create a 15GB volume in the same AZ as your EC2 Web Server.
![image](https://user-images.githubusercontent.com/22638955/111555270-52d62500-8788-11eb-92b7-2a2eddc05c8d.png)


Attach the volume to your EC2 Web Server instance.
![image](https://user-images.githubusercontent.com/22638955/111555625-1525cc00-8789-11eb-98ae-a2589362fdb6.png)


Use the "lsblk" command to inspect what block devices are attached to the server (the new block device is xvdf).

![lsblk](https://user-images.githubusercontent.com/22638955/111555005-bf045900-8787-11eb-8e61-3b2fc43f1d62.png)


Use the "gdisk" utility to create a partition on the disk
![image](https://user-images.githubusercontent.com/22638955/111556595-42737980-878b-11eb-9976-7bd190da457f.png)


Install “LVM2”, then run the “sudo lvmdiskscan” command to check for available partitions
![image](https://user-images.githubusercontent.com/22638955/111556965-0987d480-878c-11eb-9cb4-3ad59eef6c68.png)


Use the "pvcreate" utility to mark the disk as a physical volume (PV) to be used by LVM and use "sudo pvs" to check

![image](https://user-images.githubusercontent.com/22638955/111557736-8a939b80-878d-11eb-8b43-2a56695173a6.png)

![image](https://user-images.githubusercontent.com/22638955/111558235-b1060680-878e-11eb-9ad3-e4d1fe898dcf.png)



Create a volume group and then check to see if the volume group was created "sudo vgs"
![image](https://user-images.githubusercontent.com/22638955/111558717-9b451100-878f-11eb-80d4-871b3101c562.png)


Verify that everything has been created by running "sudo vgdisplay -v"
![image](https://user-images.githubusercontent.com/22638955/111559471-e01d7780-8790-11eb-97b6-727ee9538e31.png)


Setup the NFS server

![image](https://user-images.githubusercontent.com/22638955/111560253-68e8e300-8792-11eb-9156-ff1b8d358863.png)


Set up permissions that will allow the Web servers read, write and execute files on the NFS server by running the codes below

![image](https://user-images.githubusercontent.com/22638955/111560543-fe847280-8792-11eb-9808-a1a1ce57dcb4.png)


Configure access to the NFS for clients within the same subnet by running "sudo vi /etc/exports" and put in your subnet as shown below

![image](https://user-images.githubusercontent.com/22638955/111560649-312e6b00-8793-11eb-817e-e90926ab4286.png)


Since this is a test, we would be opening all ports to the NFS server (not recommended)


Create a new Ubuntu instance which would be used for our DB server (follow all steps outlined for creating an instance)

Create a database and name it tooling

![image](https://user-images.githubusercontent.com/22638955/111562709-f595a000-8796-11eb-8f90-235c46807d95.png)


Create a database user and name it "webaccess"

![image](https://user-images.githubusercontent.com/22638955/111563042-82d8f480-8797-11eb-88a5-0cd4f8bcb283.png)

Grant permission to "webaccess" user on "tooling" database to do anything only from the webservers "subnet cidr"


Install the NFS client

![image](https://user-images.githubusercontent.com/22638955/111563824-faf3ea00-8798-11eb-9ba4-090dcbf95995.png)



Mount /var/www/ and target the NFS server’s export for apps and verify that NFS was mounted successfully by running df -h.

![image](https://user-images.githubusercontent.com/22638955/111564158-92593d00-8799-11eb-9dfe-b283feb3ff24.png)


Edit the /etc/fstab file so that changes will persist on  the Web Server after reboot
![image](https://user-images.githubusercontent.com/22638955/111564961-e87ab000-879a-11eb-9999-cec83ef81118.png)


Install Apache

![image](https://user-images.githubusercontent.com/22638955/111565288-62129e00-879b-11eb-8c2d-6c289c652fe3.png)

Repeat all these steps for the other 2 web servers


![home page of tooling website](https://user-images.githubusercontent.com/22638955/111553654-1f45cb80-8785-11eb-8b5e-4bb35fbcb6ef.png)
