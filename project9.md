<h1>Tooling Website deployment automation with Continuous Integration. Introduction to Jenkins</h1>



Spin up an Ubuntu instance for the Jenkins server

![image](https://user-images.githubusercontent.com/22638955/115167868-e44ef480-a0b0-11eb-8aa2-94843da9c1b9.png)

Install JDK (since Jenkins is a Java-based application) using the below commands - 

<b>sudo apt update</b>

<b>sudo apt install default-jdk-headless</b>

![image](https://user-images.githubusercontent.com/22638955/115167797-ac47b180-a0b0-11eb-8847-7e7be446004a.png)

![image](https://user-images.githubusercontent.com/22638955/115168078-a7cfc880-a0b1-11eb-987d-a970ad9374c4.png)

Install Jenkins using the following commands -

<b>wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add - </b>

<b>sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'</b>

<b>sudo apt update</b>

<b>sudo apt-get install jenkins</b>

![image](https://user-images.githubusercontent.com/22638955/115168291-4eb46480-a0b2-11eb-80a9-9e21a2e46575.png)

Make sure Jenkins is up and running

![image](https://user-images.githubusercontent.com/22638955/115168365-87543e00-a0b2-11eb-9d9e-04733219e2bf.png)

By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound Rule in your EC2 Security Group

![image](https://user-images.githubusercontent.com/22638955/115168541-3a249c00-a0b3-11eb-85d2-582d1ad36101.png)

Perform initial Jenkins setup.

<b>From your browser access http://Jenkins-Server-Public-IP-Address-or-Public-DNS-Name:8080</b>

You will be prompted to provide a default admin password

![image](https://user-images.githubusercontent.com/22638955/115168686-b0290300-a0b3-11eb-9641-d722956c5de3.png)

Retrieve it from your server:

<b>sudo cat /var/lib/jenkins/secrets/initialAdminPassword</b>

Then you will be asked which plugings to install - choose suggested plugins.

Once plugins installation is done - create an admin user and you will get your Jenkins server address.

The installation is completed!

![image](https://user-images.githubusercontent.com/22638955/115169064-e5822080-a0b4-11eb-93b7-39d775898e7b.png)

<h2>Configure Jenkins to retrieve source codes from GitHub using Webhooks</h2>

Enable webhooks in your GitHub repository settings

![image](https://user-images.githubusercontent.com/22638955/115170595-e87f1000-a0b8-11eb-9cb7-e5e9584ada24.png)

Go to Jenkins web console, click “New Item” and create a “Freestyle project”

To connect your GitHub repository, you will need to provide its URL, you can copy from the repository itself

In configuration of your Jenkins freestyle project choose Git repository, provide there the link to your Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

![image](https://user-images.githubusercontent.com/22638955/115171745-a7d4c600-a0bb-11eb-918d-8ec98ddf2d53.png)


