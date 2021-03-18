## PROJECT 7 - DEVOPS TOOLING WEBSITE SOLUTION

Launch 3 EC2 instances that will serve as the 3 Web Servers needed for this project. 


Create a 15GB volume in the same AZ as your EC2 Web Server.
![image](https://user-images.githubusercontent.com/22638955/111555270-52d62500-8788-11eb-92b7-2a2eddc05c8d.png)


Attach the volume to your EC2 Web Server instance.
![image](https://user-images.githubusercontent.com/22638955/111555625-1525cc00-8789-11eb-98ae-a2589362fdb6.png)


Use the "lsblk" command to inspect what block devices are attached to the server (the new block device is xvdf).

![lsblk](https://user-images.githubusercontent.com/22638955/111555005-bf045900-8787-11eb-8e61-3b2fc43f1d62.png)


Use the "gdisk" utility to create a partition on the disk
![image](https://user-images.githubusercontent.com/22638955/111556595-42737980-878b-11eb-9976-7bd190da457f.png)



![image](https://user-images.githubusercontent.com/22638955/111556965-0987d480-878c-11eb-9cb4-3ad59eef6c68.png)








![home page of tooling website](https://user-images.githubusercontent.com/22638955/111553654-1f45cb80-8785-11eb-8b5e-4bb35fbcb6ef.png)
