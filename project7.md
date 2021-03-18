## PROJECT 7 - DEVOPS TOOLING WEBSITE SOLUTION

Launch 4 EC2 instances that will serve as the 3 Web Servers, and 1 NFS server needed for this project.


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

















![home page of tooling website](https://user-images.githubusercontent.com/22638955/111553654-1f45cb80-8785-11eb-8b5e-4bb35fbcb6ef.png)
