# Experience Continous Integration With Jenkins | Ansible | Artifactory | Sonarqube | PHP

The `inventory` directory in our `ansible config management` repository should look like what we have below - 

![image](https://user-images.githubusercontent.com/22638955/118739747-1be0c600-b842-11eb-93db-ef0ea4b2d6a8.png)

`ci.yml` under the `inventory` directory should contain the following - 

```
[jenkins]
<Jenkins-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[sonarqube]
<SonarQube-Private-IP-Address>

[artifact_repository]
<Artifact_repository-Private-IP-Address>
```

![image](https://user-images.githubusercontent.com/22638955/118739970-a3c6d000-b842-11eb-9f6a-991bddbf610d.png)

`dev.yml` inventory file - 

```
[tooling]
<Tooling-Web-Server-Private-IP-Address>

[todo]
<Todo-Web-Server-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<DB-Server-Private-IP-Address>
```

![image](https://user-images.githubusercontent.com/22638955/118740175-1a63cd80-b843-11eb-9d6d-092afa50dec7.png)

`pentest.yml` inventory file - 

```
[pentest:children]
pentest-todo
pentest-tooling

[pentest-todo]
<Pentest-for-Todo-Private-IP-Address>

[pentest-tooling]
<Pentest-for-Tooling-Private-IP-Address>
```

![image](https://user-images.githubusercontent.com/22638955/118740462-d91fed80-b843-11eb-97f2-488bc453b45c.png)

* We will notice that in the `pentest` inventory file, we have introduced a new concept `pentest:children` This is because, we want to have a group called `pentest` which covers Ansible execution against both `pentest-todo` and `pentest-tooling` simultaneously. But at the same time, we want the flexibility to run specific Ansible tasks against an individual group.

* The `db` group has a slightly different configuration. It uses a RedHat/Centos Linux distro. Others are based on Ubuntu (in this case user is `ubuntu`). Therefore, the user required for connectivity and path to python interpreter are different. If all your environment is based on Ubuntu, you may not need this kind of set up.

This makes us to introduce another Ansible concept called `group_vars`. With group vars, we can declare and set variables for each group of servers created in the `inventory` file.

## Ansible Roles for CI Environment

We are going to add two more roles to ansible -

* [SonarQube](https://youtu.be/vE39Fg8pvZg)

* [Artifactory](https://youtu.be/upJS4R6SbgM)

```
cd roles
ansible-galaxy init sonarqube
ansible-galaxy init artifactory
```

![image](https://user-images.githubusercontent.com/22638955/118743048-79c4dc00-b849-11eb-8f20-aa15739e7d4b.png)

## Configuring Ansible For Jenkins Deployment

In previous projects, we have been launching Ansible commands manually from a CLI. Now, with Jenkins, we will start running Ansible from the Jenkins UI.

* Navigate to your Jenkins URL

* Install and open Blue Ocean Jenkins plugin

* Create a new pipeline

![image](https://user-images.githubusercontent.com/22638955/118744279-f3f66000-b84b-11eb-9507-9b0d3e6729cf.png)

