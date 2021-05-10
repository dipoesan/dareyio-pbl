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

Notice 3 things to notice here:

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
