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

Select GitHub

![image](https://user-images.githubusercontent.com/22638955/118744881-04f3a100-b84d-11eb-9f55-7ca81db56eac.png)

Connect Jenkins with Github by logging onto Github and generating an access token

![image](https://user-images.githubusercontent.com/22638955/118746289-e216bc00-b84f-11eb-9302-8c6653ef2be5.png)

![image](https://user-images.githubusercontent.com/22638955/118746393-1d18ef80-b850-11eb-9425-ccefd2b8ff2e.png)

![image](https://user-images.githubusercontent.com/22638955/118746425-2904b180-b850-11eb-9c70-51604554a769.png)

Then generate the token. Copy the access token, paste it on the blue ocean page and connect.

![image](https://user-images.githubusercontent.com/22638955/118746588-6b2df300-b850-11eb-8672-a07195aa74da.png)

Select the name of the organisation that holds your `ansible-config-management` repository.

Then select the `ansible-config-management` repository, and then create.

Below is our newly created pipeline - 

![image](https://user-images.githubusercontent.com/22638955/118897602-89542b80-b902-11eb-91ff-167de80d4b1c.png)

Next step is to create a `Jenkins` file by ourselves.

Go into the `ansible-config-mgt` directory and create another directory called `deploy`, and then start a new file called `Jenkinsfile` inside the directory.

![image](https://user-images.githubusercontent.com/22638955/118897917-3af35c80-b903-11eb-839d-9c779960194d.png)

Add the code snippet below to start building the `Jenkinsfile` gradually. This pipeline currently has just one stage called `Build` and the only thing we are doing is using the `shell script` module to echo `Building Stage`

```
pipeline {
    agent any


  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```

Now we would go back into the Ansible pipeline in Jenkins and select configure -

![image](https://user-images.githubusercontent.com/22638955/118898110-b35a1d80-b903-11eb-97da-ed1ba90929bd.png)

Scroll down to `Build Configuration` section and specify the location of the Jenkinsfile (`deploy/Jenkinsfile`).

![image](https://user-images.githubusercontent.com/22638955/118898258-1350c400-b904-11eb-86b6-eb395efce753.png)

Save the above.

Go to the Blue Ocean dashboard, select the `ansible-config-mgt` project

Hover over any of the branches, and you should see a play button. Click on it, and it should build it

![image](https://user-images.githubusercontent.com/22638955/118900302-86f4d000-b908-11eb-8f79-33ae396fa72f.png)

This pipeline is a multibranch one. This means that, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all and we would have been able to trigger a build for each branch.

Let us create another branch to see the multibranching feature in action - 

* Create a new git branch and name it `feature/jenkinspipeline-stages`

```
git checkout -b feature/jenkinspipeline-stages
```

![image](https://user-images.githubusercontent.com/22638955/118900814-c243ce80-b909-11eb-853e-bfa270f7f881.png)

* Currently we only have the `Build` stage. Let us add another stage called `Test`. Paste the code snippet below and push the new changes to GitHub.

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```

![image](https://user-images.githubusercontent.com/22638955/118900986-1fd81b00-b90a-11eb-8389-51d3e90157b4.png)

Push these changes to Github - 

```
git add .
git commit -m "Commit new stage into Github"
git push --set-upstream origin feature/jenkinspipeline-stages
```

To make the new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

* On the Blue Ocean Dashboard, Click on the “Administration” button

![image](https://user-images.githubusercontent.com/22638955/118902333-5b281900-b90d-11eb-99cb-a8e1d22c51fc.png)

* Navigate to the Ansible project and click on “Scan repository now”

![image](https://user-images.githubusercontent.com/22638955/118902457-97f41000-b90d-11eb-8735-471ba1e593b2.png)

* Refresh the page and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.

![image](https://user-images.githubusercontent.com/22638955/118903179-143b2300-b90f-11eb-8d84-1d0385ba7b20.png)

* In Blue Ocean, we can now see how the Jenkinsfile has caused a new step in the pipeline launch build for the new branch.

![image](https://user-images.githubusercontent.com/22638955/119066380-91ca6600-b9d7-11eb-80ca-f205d470c592.png)

Let us go a step further by doing the following below - 
* Create a pull request to merge the latest code into the `main branch`
* After merging the `PR`, go back into the terminal and switch into the `main` branch.
* Pull the latest change.
* Create a new branch, add more stages into the Jenkins file to simulate below phases. (Just add an `echo` command like we have in `build` and `test` stages)
    * Package 
    * Deploy 
    * Clean up
* Verify in Blue Ocean that all the stages are working, then merge the feature branch to the main branch
* Eventually, the main branch should have a successful pipeline like this in blue ocean

![image](https://user-images.githubusercontent.com/22638955/119069342-e4a71c00-b9dd-11eb-90b1-f4c90029a7dc.png)

## Running Ansible Playbook from Jenkins

Install Ansible on the Jenkins server

```
sudo apt install ansible -y
```

Install the ansible plugin from the Jenkins UI

* Go to the manage Jenkins tab on the Jenkins homepage
* Click on Manage Plugins
* Click on the Available tab
* Go to the search bar and type in Ansible
* Click the check box next to the plugin
* Then Install without restart

![image](https://user-images.githubusercontent.com/22638955/119278471-66d15380-bc1d-11eb-8390-3d904c95a9c3.png)

Go to the manage Jenkins tab -> global tool connfiguration

![image](https://user-images.githubusercontent.com/22638955/119279256-6dae9500-bc22-11eb-9d66-45a5e66c63f6.png)

Click on Ansible -> add whatever value into name -> find your ansible executable directory by typing `which ansible`

![image](https://user-images.githubusercontent.com/22638955/119279355-1957e500-bc23-11eb-9e02-ca1dc8228b16.png)

Create the `jenkinsfile` from the scratch and delete all it's contents

* First we would create a stage that would clone our `ansible-config-mgt` repo
```
stages {
        stage('Clone Repo') {
          steps {
            git branch: 'main', url: 'https://github.com/dipoesan/ansible-config-mgt.git'
          }
        }
```
Decided to clone from the `main` branch because I was having issues cloning from other branches I had created previously

* Next stage is to prepare Ansible for execution
```
stage('Prepare Ansible For Execution') {
          steps {
            sh 'echo ${WORKSPACE}'
            }
        }
```

* Next stage is to run the Ansible playbook
```
stage('Run playbook') {
          steps {
            ansiblePlaybook credentialsId: 'private-key', disableHostKeyChecking: true, installation: 'Ansible', inventory: 'inventory/${params.inventory}', playbook: 'playbooks/site.yml'            }
        }
```

* Final stage is the clean up stage (This step is to delete a previous workspace before running a new one. This is because sometimes, changes we push to git do not show up as a result of an old worksapce still being present)
```
stage('Clean up') {
          steps {
            cleanWs cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenUnstable: true, deleteDirs: true
          }
        }
```

Once all of the above have been completed and pushed, we can go and trigger the build and we should get the below -

![image](https://user-images.githubusercontent.com/22638955/119589521-df294780-bdca-11eb-9a4e-c2de7b1380eb.png)

## Parameterizing Jenkinsfile For Ansible Deployment

To deploy to other environments, we will need to use parameters.

* Update sit inventory with new servers
```
[tooling]
<SIT-Tooling-Web-Server-Private-IP-Address>

[todo]
<SIT-Todo-Web-Server-Private-IP-Address>

[nginx]
<SIT-Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<SIT-DB-Server-Private-IP-Address>
```

* Update Jenkinsfile to introduce parameterization. Below is just one parameter. It has a default value in case if no value is specified at execution. It also has a description so that everyone is aware of its purpose.
```
pipeline {
    agent any

    parameters {
      string(name: 'inventory', defaultValue: 'dev',  description: 'This is the inventory file for the environment to deploy configuration')
    }
```

* In the Ansible execution section, remove the hardcoded inventory/dev and replace with `${inventory}. With this, each time we click on execute, it would expect an input

![image](https://user-images.githubusercontent.com/22638955/119591588-c9b61c80-bdce-11eb-8eda-ff0bd45a9beb.png)

* Add a tagging parameter 
```
string(name: 'tag', defaultValue: 'all',  description: 'Tags to specify which Ansible plays to run')
```

## CI/CD Pipeline for TODO application

We would have to update Ansible with an Artifactory role. We can use this [guide](https://www.howtoforge.com/tutorial/ubuntu-jfrog/) to create the role.

After successful installation of artifactory, we can check to see the staus of artifactfory as shown below - 

![image](https://user-images.githubusercontent.com/22638955/119910678-8e8f2700-bf4f-11eb-989f-242df8e7e35a.png)

### Phase 1 - Prepare Jenkins

* We would be forking the repository below into our GitHub account

```
https://github.com/darey-devops/php-todo.git
```

* On the Jenkins server, install PHP, its dependencies and the Composer tool

```
sudo apt install -y zip libapache2-mod-php phploc php-{xml,bcmath,bz2,intl,gd,mbstring,mysql,zip}
```

* Install Jenkins plugins
    * Plot Plugin
    * Artifactory Plugin
We will use `plot` plugin to display tests reports, and code coverage information.
The `Artifactory` plugin will be used to easily upload code artifacts into an Artifactory server.

* In Jenkins UI configure Artifactory

Manage Jenkins -> Configure System -> Scroll down to Jfrog and input values as shown below

![image](https://user-images.githubusercontent.com/22638955/119919149-b4bdc280-bf61-11eb-8811-8e252a313c4d.png)

Please remember to open port 8082 in your EC2 instance.

If everything is configured properly, you should be able to access Jfrog as shown below - 

![image](https://user-images.githubusercontent.com/22638955/119918981-5ee91a80-bf61-11eb-8071-a989796e4a00.png)

### Phase 2 - Integrate Artifactory repository with Jenkins

Create a dummy `Jenkinsfile` in the repository

Create a multibranch Jenkins pipeline within Blue Ocean

On the database server, create a database and a user

```
Create database homestead;
CREATE USER 'homestead'@'%' IDENTIFIED BY 'sePret^i';
GRANT ALL PRIVILEGES ON * . * TO 'homestead'@'%';
```

Update the database connectivity requirements in the file `.env.sample`

Update `Jenkinsfile` with proper pipeline configuration

```
pipeline {
    agent any

  stages {

     stage("Initial cleanup") {
          steps {
            dir("${WORKSPACE}") {
              deleteDir()
            }
          }
        }
  
    stage('Checkout SCM') {
      steps {
            git branch: 'main', url: 'https://github.com/darey-devops/php-todo.git'
      }
    }

    stage('Prepare Dependencies') {
      steps {
             sh 'mv .env.sample .env'
             sh 'composer install'
             sh 'php artisan migrate'
             sh 'php artisan db:seed'
             sh 'php artisan key:generate'
      }
    }
  }
}
```

Update the Jenkinsfile to include Unit tests step

```
stage('Execute Unit Tests') {
      steps {
             sh './vendor/bin/phpunit'
      } 
```

### Phase 3 - Code Quality Analysis

This is one of the areas where developers, architects and many stakeholders are mostly interested in as far as product development is concerned. As a DevOps engineer, we also have a role to play. Especially when it comes to setting up the tools.

For PHP the most commonly tool used for code quality analysis is phploc. The data produced by phploc can be ploted onto graphs in Jenkins.

Add the code analysis step in `Jenkinsfile`. The output of the data will be saved in `build/logs/phploc.csv` file.

```
 stage('Code Analysis') {
      steps {
            sh 'phploc app/ --log-csv build/logs/phploc.csv'

      }
    }
```

Plot the data using plot Jenkins plugin.

```
    stage('Plot Code Coverage Report') {
      steps {

            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Lines of Code (LOC),Comment Lines of Code (CLOC),Non-Comment Lines of Code (NCLOC),Logical Lines of Code (LLOC)                          ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'A - Lines of code', yaxis: 'Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Directories,Files,Namespaces', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'B - Structures Containers', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Average Class Length (LLOC),Average Method Length (LLOC),Average Function Length (LLOC)', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'C - Average Length', yaxis: 'Average Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Cyclomatic Complexity / Lines of Code,Cyclomatic Complexity / Number of Methods ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'D - Relative Cyclomatic Complexity', yaxis: 'Cyclomatic Complexity by Structure'      
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Classes,Abstract Classes,Concrete Classes', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'E - Types of Classes', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Methods,Non-Static Methods,Static Methods,Public Methods,Non-Public Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'F - Types of Methods', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Constants,Global Constants,Class Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'G - Types of Constants', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Test Classes,Test Methods', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'I - Testing', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Logical Lines of Code (LLOC),Classes Length (LLOC),Functions Length (LLOC),LLOC outside functions or classes ', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'AB - Code Structure by Logical Lines of Code', yaxis: 'Logical Lines of Code'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Functions,Named Functions,Anonymous Functions', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'H - Types of Functions', yaxis: 'Count'
            plot csvFileName: 'plot-396c4a6b-b573-41e5-85d8-73613b2ffffb.csv', csvSeries: [[displayTableFlag: false, exclusionValues: 'Interfaces,Traits,Classes,Methods,Functions,Constants', file: 'build/logs/phploc.csv', inclusionFlag: 'INCLUDE_BY_STRING', url: '']], group: 'phploc', numBuilds: '100', style: 'line', title: 'BB - Structure Objects', yaxis: 'Count'


      }
    }
```

We should now see a Plot menu item on the left menu. Click on it to see the charts.

![image](https://user-images.githubusercontent.com/22638955/120128431-dadd9f80-c1b9-11eb-82e3-b9705811d9fa.png)

Bundle the application code into an artifact (archived package) to upload to Artifactory

```
stage ('Package Artifact') {
    steps {
            sh 'zip -qr ${WORKSPACE}/php-todo.zip ${WORKSPACE}/*'
}
}
```

Publish the resulted artifact into Artifactory

```
stage ('Deploy Artifact') {
    steps {
            script { 
                 def server = Artifactory.server 'artifactory-server'
                 def uploadSpec = """{
                    "files": [{
                       "pattern": "php-todo.zip",
                       "target": "php-todo"
                    }]
                 }"""

                 server.upload(uploadSpec) 
               }
    }
  
}
```

Deploy the application to the `dev` environment by launching Ansible pipeline

```
stage ('Deploy to Dev Environment') {
    steps {
    build job: 'ansible-project/main', parameters: [[$class: 'StringParameterValue', name: 'env', value: 'dev']], propagate: false, wait: true
    }
  }
```

![image](https://user-images.githubusercontent.com/22638955/120391687-4ee98600-c327-11eb-9ace-c358ff169df0.png)

From the above, we can see that the "deploy artifact" stage is failing. In the error message, it said we can retry after 55 seconds.

![image](https://user-images.githubusercontent.com/22638955/120392471-73922d80-c328-11eb-9ba6-a641635de64a.png)

Restarted the "deploy to artifact" stage and it completed successfully -

## Configure SonarQube

We will make some Linux Kernel configuration changes to ensure optimal performance of the tool - we will increase `vm.max_map_count`, `file discriptor` and `ulimit`.

### Tune Linux Kernel

This can be achieved by making session changes which does not persist beyond the current session terminal.

```
sudo sysctl -w vm.max_map_count=262144
sudo sysctl -w fs.file-max=65536
ulimit -n 65536
ulimit -u 4096
```

![image](https://user-images.githubusercontent.com/22638955/120394923-0f716880-c32c-11eb-9a45-c0272fc038d5.png)

To make a permanent change, edit the file `/etc/security/limits.conf` and append the below -

```
sonarqube   -   nofile   65536
sonarqube   -   nproc    4096
```

Before installing, let us update and upgrade system packages:

```
sudo apt-get update
sudo apt-get upgrade
```

Install wget and unzip packages

```
sudo apt-get install wget unzip -y
```

Install OpenJDK and Java Runtime Environment (JRE) 11

```
sudo apt-get install openjdk-11-jdk -y
sudo apt-get install openjdk-11-jre -y
```

Set default JDK - To set default JDK or switch to OpenJDK enter below command:

```
sudo update-alternatives --config java
```

If you have multiple versions of Java installed, you should see a list like below:

```
Selection    Path                                            Priority   Status

------------------------------------------------------------

  0            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      auto mode

  1            /usr/lib/jvm/java-11-openjdk-amd64/bin/java      1111      manual mode

  2            /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java   1081      manual mode

* 3            /usr/lib/jvm/java-8-oracle/jre/bin/java          1081      manual mode
```

Type “1” to switch OpenJDK 11

Verify the set JAVA Version:

```
java -version
```

### Install and Setup PostgreSQL 10 Database for SonarQube

The command below will add PostgreSQL repo to the repo list:

```
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main" >> /etc/apt/sources.list.d/pgdg.list'
```

Download PostgreSQL software

```
wget -q https://www.postgresql.org/media/keys/ACCC4CF8.asc -O - | sudo apt-key add -
```

Install PostgreSQL Database Server

```
sudo apt-get -y install postgresql postgresql-contrib

```

Start PostgreSQL Database Server

```
sudo systemctl start postgresql
```

Enable it to start automatically at boot time

```
sudo systemctl enable postgresql
```

Change the password for default postgres user

```
sudo passwd postgres
```

Switch to the postgres user

```
su - postgres
```

Create a new user by typing the below -

```
createuser sonar
```

Switch to the PostgreSQL shell

```
psql
```

Set a password for the newly created user for SonarQube database

```
ALTER USER sonar WITH ENCRYPTED password 'sonar';
```

Create a new database for PostgreSQL database by running:

```
CREATE DATABASE sonarqube OWNER sonar;
```

Grant all privileges to sonar user on sonarqube Database

```
grant all privileges on DATABASE sonarqube to sonar;
```

Exit from the psql shell:

```
\q
```

Switch back to the `sudo` user by running the exit command.

```
exit
```

### Install SonarQube on Ubuntu 20.04 LTS

Navigate to the tmp directory to temporarily download the installation files

```
cd /tmp && sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.9.3.zip
```

Unzip the archive setup to `/opt directory`

```
sudo unzip sonarqube-7.9.3.zip -d /opt
```

Move extracted setup to /opt/sonarqube directory

```
sudo mv /opt/sonarqube-7.9.3 /opt/sonarqube
```

## Configure SonarQube

We cannot run SonarQube as a root user, if you run using root user it will stop automatically. The ideal approach will be to create a separate group and a user to run SonarQube

Create a group `sonar`

```
sudo groupadd sonar
```

Now add a user with control over the `/opt/sonarqube` directory

```
sudo useradd -c "user to run SonarQube" -d /opt/sonarqube -g sonar sonar 
sudo chown sonar:sonar /opt/sonarqube -R
```

Open SonarQube configuration file using your favourite text editor (e.g., nano or vim)

```
sudo vi /opt/sonarqube/conf/sonar.properties
```

Find the following lines:

```
#sonar.jdbc.username=
#sonar.jdbc.password=
```

Uncomment them and provide the values of PostgreSQL Database username and password:

```
sonar.jdbc.username=sonar
sonar.jdbc.password=sonar
sonar.jdbc.url=jdbc:postgresql://localhost:5432/sonarqube
```

![image](https://user-images.githubusercontent.com/22638955/120400930-ca066880-c336-11eb-850a-8f6185852aa4.png)

Edit the sonar script file and set RUN_AS_USER

```
sudo vi /opt/sonarqube/bin/linux-x86-64/sonar.sh
```

![image](https://user-images.githubusercontent.com/22638955/120401391-c45d5280-c337-11eb-90bf-225176f8a25e.png)

Now, to start SonarQube we need to do following:

Switch to sonar user

```
sudo su sonar
```

Move to the script directory

```
cd /opt/sonarqube/bin/linux-x86-64/
```

Run the script to start SonarQube

```
./sonar.sh start
```

Check SonarQube running status:

![image](https://user-images.githubusercontent.com/22638955/120404428-405a9900-c33e-11eb-863f-cb32eb2d525e.png)

To check SonarQube logs, navigate to `/opt/sonarqube/logs/sonar.log` directory

```
tail /opt/sonarqube/logs/sonar.log
```

### Configure SonarQube to run as a systemd service

Stop the currently running SonarQube service

```
 cd /opt/sonarqube/bin/linux-x86-64/
```

Run the script to stop SonarQube

```
./sonar.sh stop
```

Create a systemd service file for SonarQube to run as System Startup.

```
 sudo vi /etc/systemd/system/sonar.service
```

Add the configuration below for systemd to determine how to start, stop, check status, or restart the SonarQube service.

```
[Unit]
Description=SonarQube service
After=syslog.target network.target

[Service]
Type=forking

ExecStart=/opt/sonarqube/bin/linux-x86-64/sonar.sh start
ExecStop=/opt/sonarqube/bin/linux-x86-64/sonar.sh stop

User=sonar
Group=sonar
Restart=always

LimitNOFILE=65536
LimitNPROC=4096

[Install]
WantedBy=multi-user.target
```

![image](https://user-images.githubusercontent.com/22638955/120405802-528a0680-c341-11eb-9a15-7566e2ee1974.png)

Save the file and control the service with `systemctl`

```
sudo systemctl start sonar
sudo systemctl enable sonar
sudo systemctl status sonar
```

![image](https://user-images.githubusercontent.com/22638955/120405923-9715a200-c341-11eb-83cb-2bae285cc3b1.png)

### Access SonarQube

To access SonarQube using browser, type server’s IP address followed by port `9000`

vvv

# BLOCKER 
Could not test or access JFrog after setting it up because I had not enabled the port 8082 in my inbound rules.

My instance was inaccessible at some point in time. After reaching out to some colleagues in Darey.io, it was suggested I change the instance type to a higher one (t2 nano [2GB] to t2 medium [4GB]). I changed it, and the instance was accessible again. What I noticed is that artifactory requires a minimum of 4GB ram to run, and I was using t2 nano which had only 2GB. Saw that after switching to t2 medium, my instance was using about 3GB of RAM. Hence, the system ran out of memory when I installed artifactory.

https://michalwegrzyn.wordpress.com/2016/07/14/do-not-run-sonar-as-root/
