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
