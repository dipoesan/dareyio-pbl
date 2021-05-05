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

![image](https://user-images.githubusercontent.com/22638955/117086440-69d6d380-ad44-11eb-8bcb-9246dcd8012a.png)

### Begin Ansible Development




