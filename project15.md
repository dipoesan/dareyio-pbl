# AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

#### **WARNING**: This infrastructure set up is NOT covered by the AWS free tier. Therefore, ensure to DELETE ALL resources created immediately after finishing this project. Monthly cost may be shockingly high if resources are not deleted. Also, it is strongly recommended to set up a budget and configure notifications when your spendings reach a predefined limit.

1. Configure your AWS account and Organization Unit
* Create an AWS Master account. (Also known as Root Account)
* Within the Root account, create a sub-account and name it *DevOps*. (You will need another email address to complete this)
* Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there)
* Move the DevOps account into the Dev OU.
* Login to the newly created AWS account using the new email address.

2. Create a free domain name for your fictitious company at Freenom domain registrar [here](https://www.freenom.com/en/index.html?lang=en).

3. Create a hosted zone in AWS, and map it to your free domain from Freenom. [Watch how to do that here.](https://www.youtube.com/watch?v=IjcHp94Hq8A&feature=youtu.be)

![image](https://user-images.githubusercontent.com/22638955/130644745-54c4be4b-7669-4dd6-b1ab-ff3d3c28f417.png)

The above diagram is what we want our infrastructure to look like. We are now going to begin setting this up.

### SET UP A VIRTUAL PRIVATE NETWORK (VPC)

* Create a VPC
![image](https://user-images.githubusercontent.com/22638955/130646248-b1893075-9bc7-480d-a289-4208f2a0e35d.png)

* Create subnets as shown in the architecture
![image](https://user-images.githubusercontent.com/22638955/130646690-b4550607-59a0-4b98-9280-a159dcaf7f9d.png)

* Create a route table and associate it with public subnets
* Create a route table and associate it with private subnets
![image](https://user-images.githubusercontent.com/22638955/130646918-a39353da-63ea-40f9-adff-85c36d7b5104.png)

* Create an Internet Gateway
![image](https://user-images.githubusercontent.com/22638955/130647027-6877995a-8f04-4150-9d80-7f9491e2b4d7.png)

* Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble from the Internet)
![image](https://user-images.githubusercontent.com/22638955/130647138-d90c3746-6a55-41d9-937b-853425c45dc8.png)

* Create 3 Elastic IPs
* Create a Nat Gateway and assign one of the Elastic IPs (*The other 2 will be used by the Bastion hosts)
![image](https://user-images.githubusercontent.com/22638955/130647331-b4ca0d65-896b-4fdf-aba0-83b8000b2fc5.png)

* Create a Security Group for:
  * Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a load balancer, therefore we will update the rules later. For now, just create it and put some dummy records as a place holder.
  * Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type curl www.canhazip.com
  * Application Load Balancer: ALB will be available from the Internet
  * Webservers: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a place holder, we will update it later.
  * Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.


### SET UP COMPUTE RESOURCES

* Create an EC2 Instance based on the RHEL Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region (it is recommended to use the Region that is closest to your customers). Use EC2 instance of T2 family (e.g. t2.micro or similar)
* Ensure that it has the following software installed - python, ntp, net-tools, vim, wget, telnet, epel-release, htop.
```
yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm 
yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm 
yum install wget vim python3 telnet htop git mysql net-tools chrony -y 
systemctl start chronyd
systemctl enable chronyd
```
* Create an AMI out of the EC2 instance
* Make use of the AMI to set up a launch template
* Ensure the Instances are launched into a public subnet
* Assign appropriate security group
* Configure Userdata to update yum package repository and install nginx






![image](https://user-images.githubusercontent.com/22638955/130638151-159d7553-8efb-4904-b72e-edaf4b807a74.png)
