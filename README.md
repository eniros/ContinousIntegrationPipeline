# ContinousIntegrationPipeline
CI-CD-on-DevOps-Website-Solution

Introduction

Jenkins is an open-source Continuous Integration server written in Java for orchestrating a chain of actions to achieve the Continuous Integration process in an automated fashion. Jenkins supports the complete development life cycle of software from building, testing, documenting the software, deploying, and other stages of the software development life cycle.

<img width="811" alt="Screenshot 2022-12-02 at 00 57 54" src="https://user-images.githubusercontent.com/61475969/205190787-31fee821-323e-42bc-8606-7f0d13047d80.png">

1 - Installing Jenkins Server

Spun up a web server on AWS cloud and SSH into it.

Installing JDK which is an important Java based package required for Jenkins to run.

sudo apt update
sudo apt install default-jdk-headless

*********add image********

wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

**********add image**********

Since Jenkins runs on default port 8080, open this port on the Security Group inbound rule of the jenkins server on AWS

<img width="1180" alt="Screenshot 2022-12-02 at 00 54 00" src="https://user-images.githubusercontent.com/61475969/205191068-6e559681-996d-4450-87d2-5a21ea84603b.png">

Jenkins is up and running, copy and paste jenkins server public ip address appended with port 8080 on a web server to gain access to the interactive console. <jenkins_server_public_ip_address>:8080

<img width="1118" alt="Screenshot 2022-12-02 at 01 02 44" src="https://user-images.githubusercontent.com/61475969/205191843-812e901d-aa72-4081-a2c4-a37eb9515fa0.png">

<img width="860" alt="Screenshot 2022-12-02 at 01 07 49" src="https://user-images.githubusercontent.com/61475969/205191957-ac695708-ceb7-4190-8919-a8c7d6643c4c.png">

<img width="1362" alt="Screenshot 2022-12-02 at 01 09 40" src="https://user-images.githubusercontent.com/61475969/205192177-6d5b1406-eeb1-47a6-8e16-dcf450f18a55.png">

Attaching WebHook to Jenkins Server

On the github repository that contains application code, create a webhook to connect to the jenkins job. To create webhook, go to the settings tab on the github repo and click on webhooks. Webhook should look like this <public_ip_of_jenkins_server>:8080/github-webhook/

<img width="997" alt="Screenshot 2022-12-02 at 01 11 10" src="https://user-images.githubusercontent.com/61475969/205192339-509e7ee7-e612-48f3-aa56-ab9d87f37576.png">

2 - Creating Job and Configuring GIT Based Push Trigger

On the jenkins server, create a new freestyle project by clicking on create a new job and then click 'freestyle project'

<img width="997" alt="Screenshot 2022-12-02 at 01 13 33" src="https://user-images.githubusercontent.com/61475969/205192554-6d799aee-dc02-419c-9ad4-247bcc6e6277.png">

In configuration of the Jenkins freestyle job choose Git repository, provide there the link to the GitHub repository and credentials (user/password) so Jenkins could access files in the repository. Also specify the branch containing code

<img width="997" alt="Screenshot 2022-12-02 at 01 15 22" src="https://user-images.githubusercontent.com/61475969/205192689-f4557818-0358-48da-b72d-66006efb3160.png">

<img width="882" alt="Screenshot 2022-12-02 at 01 15 54" src="https://user-images.githubusercontent.com/61475969/205192759-ad66aa51-d9f2-48e2-8a20-93b8c64e637f.png">

<img width="830" alt="Screenshot 2022-12-02 at 01 16 46" src="https://user-images.githubusercontent.com/61475969/205192837-a38456d6-ab88-47de-96d8-edf04b5bd1a7.png">

Specify the particular trigger to use for triggering the job. Click "Configure" on the jenkins job and add these two configurations

1. Configure triggering the job from GitHub webhook:

<img width="735" alt="Screenshot 2022-12-02 at 01 17 54" src="https://user-images.githubusercontent.com/61475969/205192954-a2216b23-d699-41f6-ab73-9bc4ff0ac7c2.png">

2. Configure "Post-build Actions" to archive all the files – files resulted from a build are called "artifacts".

<img width="709" alt="Screenshot 2022-12-02 at 01 18 46" src="https://user-images.githubusercontent.com/61475969/205193043-4393844a-473f-42b6-8e20-4cb5975a0387.png">

The architecture has pretty much been built, lets test it by making a change on any file on the Github repository and then push it to see the triggered job

<img width="1013" alt="Screenshot 2022-12-02 at 01 20 09" src="https://user-images.githubusercontent.com/61475969/205193175-dcd8142b-a630-493d-8709-627b8bddcf1e.png">

The console output shows the created job and the successful build. In this case the code on Github was built into an artifact on our Jenkins server workspace. Find the artificat by checking the status tab of the completed job .

<img width="1000" alt="Screenshot 2022-12-02 at 01 21 01" src="https://user-images.githubusercontent.com/61475969/205193291-48e461fe-9e99-42c6-8e51-c3f7bc498565.png">

<img width="786" alt="Screenshot 2022-12-02 at 01 21 39" src="https://user-images.githubusercontent.com/61475969/205193371-f8ef4a6e-fdf6-4278-90f6-12daafbc66e5.png">






