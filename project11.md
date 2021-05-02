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


