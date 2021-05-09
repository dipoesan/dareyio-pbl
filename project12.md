# Ansible Refactoring & Static Assignments (Imports and Roles)

In this project we will continue working with `ansible-config-mgt` repository and make some improvements to our code. Now we need to refactor our Ansible code, create assignments, and learn how to use the imports functionality. Imports allow us to effectively re-use previously created playbooks in a new playbook - it allows us to organize our tasks and reuse them when needed.

## Code Refactoring

Refactoring is a general term in computer programming. It means making changes to the source code without changins expected behaviour of the software. The main idea of refactoring is to enhance code readability, increase maintainability and extensibility, reduce complexity, add proper comments without affecting the logic.

In our case, we will move things around a little bit in the code, but the overal state of the infrastructure remains the same.

## Step 1 - Jenkins job enhancement

Go to your `Jenkins-Ansible` server and create a new directory called `ansible-config-mgt` - we will store in there all our artifacts after each build.

![image](https://user-images.githubusercontent.com/22638955/117581047-b0388380-b0f2-11eb-825b-97a2d92c3d42.png)

```
mkdir /home/ubuntu/ansible-config-mgt
```

Change permissions to this directory, so Jenkins could save files there -

```
chmod -R 0777 /home/ubuntu/ansible-config-mgt
```

Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on `Available` tab search for `Copy Artifact` and install this plugin without restarting Jenkins

![image](https://user-images.githubusercontent.com/22638955/117581283-f6daad80-b0f3-11eb-944f-253ecab91f67.png)

Create a new Freestyle project and name it save_artifacts.

This project will be triggered by completion of the existing ansible project. Configure it accordingly:

![image](https://user-images.githubusercontent.com/22638955/117581777-76697c00-b0f6-11eb-9751-35ccbb1b192d.png)

![image](https://user-images.githubusercontent.com/22638955/117581976-b3823e00-b0f7-11eb-9fdb-8a6492d98f35.png)

Note: You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. 
You can also make this change to the `ansible` job.

The main idea of `save_artifacts` project is to save artifacts into `/home/ubuntu/ansible-config-mgt directory`. 

To achieve this, create a `Build` step and choose `Copy artifacts from other project`, specify `ansible` as a source project and `/home/ubuntu/ansible-config-mgt` as a target directory.

![image](https://user-images.githubusercontent.com/22638955/117581995-d44a9380-b0f7-11eb-96a6-5256245c5168.png)

We would test our set up by making some changes in README.MD file inside the ansible-config-mgt repository (right inside the main branch).

![image](https://user-images.githubusercontent.com/22638955/117582704-38bb2200-b0fb-11eb-81d2-3e1e3e2241d4.png)

If both Jenkins jobs have completed one after another - you shall see your files inside `/home/ubuntu/ansible-config-mgt` directory and it will be updated with every commit to your main branch.

![image](https://user-images.githubusercontent.com/22638955/117582808-bb43e180-b0fb-11eb-9476-00c0e7bd9249.png)

Now our Jenkins pipeline is more neat and clean.

## Step 2 - Refactor Ansible code by importing other playbooks into `site.yml`

Before we start refactoring the codes, we have to ensure that we have pulled down the latest code from the `master` (main) branch, and created a new branch, and name it `refactor`.

```
cd ansible-configuration-management
git checkout main 
git pull
git checkout -b refactor
```

Within playbooks folder, create a new file and name it `site.yml` - This file will now be considered as an entry point into the entire infrastructure configuration. 
Other playbooks will be included here as a reference. In other words, `site.yml` will become a parent to all other playbooks that will be developed, including `common.yml` that we created previously.

Create a new folder in root of the repository and name it `static-assignments`. The static-assignments folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. 

![image](https://user-images.githubusercontent.com/22638955/117587982-9447d880-b118-11eb-9dbe-3f325ace04e5.png)

Move the `common.yml` file into the newly created `static-assignments`folder.

```
sudo mv playbooks/common.yml static-assignments/
```

![image](https://user-images.githubusercontent.com/22638955/117588189-d02f6d80-b119-11eb-8661-9b35571a93c4.png)

Inside the `site.yml`file, import the `common.yml` playbook using the code below -

```
---
- hosts: all
- import_playbook: ../static-assignments/common.yml
```

![image](https://user-images.githubusercontent.com/22638955/117588275-4633d480-b11a-11eb-97e1-1b7c4346cb6c.png)

The code above uses the built in import_playbook Ansible module.

Our folder structure should look like below 

```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── staging
    └── uat
    └── prod
└── playbooks
    └── site.yml
```

Run the `ansible-playbook` command against the dev environment

Since we need to apply some tasks to our `dev` servers and `wireshark` is already installed - we can go ahead and create another playbook under `static-assignments` and name it `common-del.yml`. In this playbook, we would configure the deletion of the `wireshark` utility.

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```

![image](https://user-images.githubusercontent.com/22638955/117589357-dd9c2600-b120-11eb-9e32-60680ef99286.png)

update `site.yml` with `- import_playbook: ../static-assignments/common-del.yml` instead of `common.yml` and run it against `dev` servers:

![image](https://user-images.githubusercontent.com/22638955/117589470-9e220980-b121-11eb-804e-d26e0f033c89.png)

```
sudo ansible-playbook -i /home/ubuntu/ansible-config-mgt/inventory/dev.yml /home/ubuntu/ansible-config-mgt/playbooks/site.yml
```

![image](https://user-images.githubusercontent.com/22638955/117589622-90b94f00-b122-11eb-8c67-7f8b6d028366.png)

Make sure that `wireshark` is deleted on all the servers by running `wireshark --version`

## Step 3 - Configure UAT Webservers with a role ‘Webserver’

We would be launching 2 new webs servers to use as `UAT` environments.

Launch 2 fresh EC2 instances using RHEL 8 image, we will use them as our uat servers, so we would give them names accordingly - `Web1-UAT` and `Web2-UAT`.

![image](https://user-images.githubusercontent.com/22638955/117590012-73d24b00-b125-11eb-837c-35b5f04faa6b.png)

To create a role, we must create a directory called roles/, relative to the playbook file or in /etc/ansible/ directory.

There are two ways we can create this folder structure:

We can use an Ansible utility called `ansible-galaxy` inside `ansible-config-mgt/roles` directory (you need to create roles directory upfront)

```
mkdir roles
cd roles
ansible-galaxy init webserver
```

![image](https://user-images.githubusercontent.com/22638955/117590395-2951ce00-b127-11eb-9ebe-b90be541453b.png)

Or we can create the directory/files structure manually

Note: We can choose either way, but since we store all our codes in GitHub, it is recommended to create folders and files there rather than locally on the `Jenkins-Ansible` server.

The entire folder structure should look like below, but if you create it manually - you can skip creating `tests`, `files`, and `vars` or remove them if you used `ansible-galaxy`

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```

After removing unnecessary directories and files, the roles structure should look like this

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates
```

We would be updating our inventory `ansible-config-mgt/inventory/uat.yml` file with IP addresses of our 2 UAT Web servers

```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>
<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user' ansible_ssh_private_key_file=<path-to-.pem-private-key>
```

In `/etc/ansible/ansible.cfg` file uncomment `roles_path` string and provide a full path to your roles directory `roles_path = /home/ubuntu/ansible-config-mgt/roles`, so Ansible could know where to find configured roles.

![image](https://user-images.githubusercontent.com/22638955/117590767-d0833500-b128-11eb-8669-0a7e372feb17.png)

Go into `tasks` (`ansible-config-mgt/roles/webserver/tasks`) directory, and within the `main.yml` file, start writing configuration tasks to do the following:

* Install and configure Apache (httpd service)
* Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
* Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
* Make sure httpd service is started

```
---
- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```

## Step 4 - Reference ‘Webserver’ role

Within the `static-assignments` folder, we would create a new assignment for uat-webservers `uat-webservers.yml`. This is where we will reference the role.





