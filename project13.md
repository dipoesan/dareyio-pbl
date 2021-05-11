# Ansible Dynamic Assignments (Include) and Community Roles

We'll be continuing from our last project (Project 12)

In our `https://github.com/<your-name>/ansible-config-mgt` GitHub repository start a new branch and call it `dynamic-assignments`.

```
git checkout -b dynamic-assignments
```

Create a new folder, name it `dynamic-assignments`. Then inside this folder, create a new file and name it `env-vars.yml`. 

We will instruct `site.yml` to `include` this playbook later. For now, we would keep building up the structure.

![image](https://user-images.githubusercontent.com/22638955/117734550-99c22300-b1eb-11eb-952b-445803319e4f.png)

Our GitHub shall have following structure by now.

```
├── dynamic-assignments
│   └── env-vars.yml
├── inventory
│   └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
└── roles (optional folder)
    └──...(optional subfolders & files)
└── static-assignments
    └── common.yml
```

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as servername, ip-address etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment’s variables file. Therefore, create a new folder `env-vars`, then for each environment, create new YAML files which we will use to set variables.

Our layout should now look like this - 

```
├── dynamic-assignments
│   └── env-vars.yml
├── env-vars
    └── dev.yml
    └── stage.yml
    └── uat.yml
    └── prod.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
├── playbooks
    └── site.yml
└── static-assignments
    └── common.yml
    └── webservers.yml
```

![image](https://user-images.githubusercontent.com/22638955/117735328-23bebb80-b1ed-11eb-872f-786264a1bd33.png)

Now paste the instruction below into the `env-vars.yml` file.

```
---
- name: collate variables from env specific file, if it exists
  include_vars: "{{ item }}"
  with_first_found:
    - "{{ playbook_dir }}/../env_vars/{{ "{{ inventory_file }}.yml"
    - "{{ playbook_dir }}/../env_vars/default.yml"
  tags:
    - always
```

![image](https://user-images.githubusercontent.com/22638955/117735504-75ffdc80-b1ed-11eb-9018-14c92c81ccb1.png)

Notice 3 things to note here:

We used include_vars syntax instead of include, this is because Ansible developers decided to separate different features of the module. 

From Ansible version 2.8, the include module is deprecated and variants of include_* must be used. These are:

* [include_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html#include-role-module)
* [include_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html#include-tasks-module)
* [include_vars](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html#include-vars-module)

In the same version, variants of import were also introduces, such as:

* [import_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_role_module.html#import-role-module)
* [import_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_tasks_module.html#import-tasks-module)
* 
We made use of special variables `{{ playbook_dir }}` and `{{ inventory_file }}`. `{{ playbook_dir }}` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. 

`{{ inventory_file }}` on the other hand will dynamically resolve to the name of the inventory file being used, then append `.yml` so that it picks up the required file within the `env-vars` folder.

We are including the variables using a loop. with_first_found implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.

## Update site.yml with dynamic assignments

Update `site.yml` file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come)

`site.yml` should now look like this - 

```
---
- name: Include dynamic variables 
  hosts: all
  tasks:
    - import_playbook: ../static-assignments/common.yml 
    - include_playbook: ../dynamic-assignments/env-vars.yml
  tags:
    - always

- name: Webserver assignment
  hosts: webservers
    - import_playbook: ../static-assignments/webservers.yml
```

![image](https://user-images.githubusercontent.com/22638955/117736517-afd1e280-b1ef-11eb-9f74-eed6690418dc.png)

Now it is time to create a role for MySQL database - it should install the MySQL package, create a database and configure users. 

But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there. 

These roles are actually production ready, and dynamic to accomodate most of Linux flavours. 

With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

## Download Mysql Ansible Role

You can browse available community roles [here](https://galaxy.ansible.com/home)

We will be using a [MySQL role developed by](https://galaxy.ansible.com/geerlingguy/mysql) `geerlingguy`.

In order to preserve our GitHub in it's actual state after we install a new role - make a commit and push to master our ‘ansible-config-mgt’ directory. 

On `Jenkins-Ansible` server make sure that `git` is installed with `git --version`, then go to ‘ansible-configuration-management’ directory and run -

```
git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
```

Inside `roles` directory create your new MySQL role with `ansible-galaxy install geerlingguy.mysql` and rename the folder to `mysql`

```
mv geerlingguy.mysql/ mysql
```

![image](https://user-images.githubusercontent.com/22638955/117739471-57521380-b1f6-11eb-8cd0-b791cd1cd91b.png)

Read `README.md` file, and edit roles configuration to use correct credentials for MySQL required for the `tooling` website.

`ROLES -> MYSQL -> DEFAULTS -> MAIN.YML`

![image](https://user-images.githubusercontent.com/22638955/117741281-f9272f80-b1f9-11eb-99e6-92436ac63ee7.png)

Upload the changes into GitHub:

```
git add .
git commit -m "Commit new role files into GitHub"
git push --set-upstream origin roles-feature
```

Create a Pull Request and merge it to the `main` branch on GitHub.

## Load Balancer roles

We want to be able to choose which Load Balancer to use, `Nginx` or `Apache`, so we need to have two roles respectively:

* Nginx
* Apache

Install the Nginx Ansible Role and rename the folder to nginx

```
ansible-galaxy install geerlingguy.nginx
mv geerlingguy.nginx/ nginx
```

Install the Apache Ansible Role and rename the folder to apache

```
ansible-galaxy install geerlingguy.apache 
mv geerlingguy.apache/ apache
```

* Since we cannot use both Nginx and Apache load balancer, we need to add a condition to enable either one - this is where we can make use of variables.

* Declare a variable in `defaults/main.yml` file inside the Nginx and Apache roles. Name each variables `enable_nginx_lb` and `enable_apache_lb` respectively.

* Set both values to false like this `enable_nginx_lb: false` and `enable_apache_lb: false`.

* Declare another variable in both roles `load_balancer_is_required` and set its value to `false` as well

* Update both assignment and `site.yml` files respectively

`loadbalancers.yml` file is going to look like this (create this file in your `static-assignments` folder)

```
- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
```

Add this to the `site.yml` file

```
 - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 
```

Now we can make use of `env-vars\uat.yml` file to define which loadbalancer to use in UAT the environment by setting the respective environmental variable to `true`.

We will activate load balancer, and enable `nginx` by setting these in the respective environment’s `env-vars` file.

```
enable_nginx_lb: true
load_balancer_is_required: true
```

The same must work with `apache` LB, so we can switch it by setting respective environmental variable to `true` and other to `false`.

To test this, we can update inventory for each environment and run Ansible against each environment.
