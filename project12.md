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





