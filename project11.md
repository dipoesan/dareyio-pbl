# Ansible Configuration Management - Automate Project 7 to 10

In Projects 7 to 10 we had to perform a lot of manual operations to set up virtual servers, install and configure the required software, and also deploy our web application.

This Project will make us appreciate DevOps tools even more by automating most of the routine tasks with Ansible Configuration Management, at the same time you will become 
confident at writing code using declarative language such as `YAML`.

## Ansible Client as a Jump Server (Bastion Host)

A Jump Server (sometimes also referred as a Bastion Host) is an intermediary server through which access to an internal network can be provided. That means, even DevOps engineers 
cannot `SSH `into the Web servers directly, and can only access it through a Jump Server - this provides better security and also reduces the attack surface.

### Install and configure Ansible on EC2 Instance

Update the `Name` tag on your Jenkins EC2 Instance to `Jenkins-Ansible`. It's this server we will use to run our playbooks.

![image](https://user-images.githubusercontent.com/22638955/116822263-c9cb4f80-ab75-11eb-85c6-43f05dac6f38.png)

We would be creating a repository on our GitHub account and we would call it `ansible-config-mgt`.

![image](https://user-images.githubusercontent.com/22638955/116822502-0a779880-ab77-11eb-809b-8a008a272fa9.png)

Instal Ansible

```bash
sudo apt update

sudo apt install ansible
```

![image](https://user-images.githubusercontent.com/22638955/116823350-ebc7d080-ab7b-11eb-95fe-5599428b55c6.png)

![image](https://user-images.githubusercontent.com/22638955/116823390-27629a80-ab7c-11eb-8f38-5981b94e06f7.png)

Check your Ansible version by running `ansible --version`

![image](https://user-images.githubusercontent.com/22638955/116823439-76103480-ab7c-11eb-91cb-ac2750694012.png)

Configure Jenkins build job to save your repository content every time you change it 

* Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository

![image](https://user-images.githubusercontent.com/22638955/116833246-16cc1780-abb0-11eb-8723-c22a95ec23fc.png)

* Configure Webhook in GitHub and set webhook to trigger ansible build

![image](https://user-images.githubusercontent.com/22638955/116833451-1e3ff080-abb1-11eb-92a1-4fa605edf55f.png)

Go back to Jenkins and configure triggering a build job from GitHub webhook, and Configure “Post-build Actions” to archive all the files.

![image](https://user-images.githubusercontent.com/22638955/116833673-74616380-abb2-11eb-91ea-55e7271e98c7.png)

We are going to test our setup by making some change in the README.MD file in our main branch, and we would also make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

`/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

![image](https://user-images.githubusercontent.com/22638955/116835271-a924e900-abb9-11eb-982a-be2b2fcbde78.png)

![image](https://user-images.githubusercontent.com/22638955/116835315-d40f3d00-abb9-11eb-9305-afadbab3cac5.png)

From the above images, we can see that we have had 6 automatic builds in Jenkins, and they have also reflected in the folder as shown in the command below `/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/`

### Prepare the development environment using Visual Studio Code

I was stuck on connecting my VSCode IDE to my Jenkins-Ansible EC2 instance as I specified a private key (ppk) instead of a `.pem` key.

Follow this [link](https://medium.com/@christyjacob4/using-vscode-remotely-on-an-ec2-instance-7822c4032cff) to setup your VSCode to connect to your instance.

### Begin Ansible Development

In the `ansible-config-mgt` GitHub repository I created previously, we would be creating a new branch that will be used for development of a new feature.

We would checkout a newly created feature branch to our local machine and start building our code and directory structure

We first cloned the repository, then created a new feature using `git checkout -b feature name`

![image](https://user-images.githubusercontent.com/22638955/117086440-69d6d380-ad44-11eb-8bcb-9246dcd8012a.png)

I created a directory and named it `playbooks` - it will be used to store all our playbook files.

I created a directory and named it `inventory` - it will be used to keep our hosts organised.

Within the playbooks folder, I created our first playbook, and named it `common.yml`

Within the inventory folder, I created an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

![image](https://user-images.githubusercontent.com/22638955/117087667-14042a80-ad48-11eb-9b86-d81bbf7c0bb0.png)

### Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from the Jenkins-Ansible host - for this we need to copy our private (.pem) key and provide a path to it so Ansible can use it to connect. Lets not forget to change permissions to our private key chmod 400 key.pem, otherwise EC2 will not accept the key. Also notice, that our Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user.

I made use of mobaxterm to copy the `.pem` key from my PC to the linux instance.

We would update our inventory/dev.yml file with this snippet of code:

[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>

[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>

[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>

[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu' ansible_ssh_private_key_file=<path-to-.pem-private-key>

Below is what the `dev.yml` file looks like after inserting information into it

![image](https://user-images.githubusercontent.com/22638955/117090202-99d7a400-ad4f-11eb-8462-4afd40e987ac.png)

### Create a Common Playbook


