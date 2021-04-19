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
