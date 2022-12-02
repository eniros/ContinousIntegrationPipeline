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

<img width="676" alt="Screenshot 2022-12-02 at 01 43 04" src="https://user-images.githubusercontent.com/61475969/205195665-d5c5fce4-28a3-4b60-9b45-76a51c5a49d4.png">


wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > \
    /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt-get install jenkins

sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins

<img width="676" alt="Screenshot 2022-12-02 at 01 43 58" src="https://user-images.githubusercontent.com/61475969/205195755-876ca190-2a06-4c4c-96db-da8b989a910b.png">


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

The created artifact can be found on local terminal too at this path /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/

<img width="786" alt="Screenshot 2022-12-02 at 01 23 45" src="https://user-images.githubusercontent.com/61475969/205193630-88734087-54a5-4c73-9c73-ad8be0a61c3f.png">

Configuring Jenkins To Copy Files(Artifact) to NFS Server

To achieve this, we install the Publish Via SSH pluging on Jenkins. The plugin allows one to send newly created packages to a remote server and install them, start and stop services that the build may depend on and many other use cases.

On main dashboard select "Manage Jenkins" and choose "Manage Plugins" menu item.

<img width="786" alt="Screenshot 2022-12-02 at 01 25 23" src="https://user-images.githubusercontent.com/61475969/205193786-a2301492-52a2-42e4-ab86-56c0666a96dc.png">


On "Available" tab search for "Publish Over SSH" plugin and install it

<img width="417" alt="Screenshot 2022-12-02 at 01 26 04" src="https://user-images.githubusercontent.com/61475969/205193861-3116c81a-5d9e-4ff1-becd-7c93befcffbe.png">

Configure the job to copy artifacts over to NFS server. On main dashboard select "Manage Jenkins" and choose "Configure System" menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to the NFS server:

Provide a private key (content of .pem file that you use to connect to NFS server via SSH/Putty)

Hostname – can be private IP address of NFS server 
Username – ec2-user (since NFS server is based on EC2 with RHEL 8) 
Remote directory – /mnt/apps since our Web Servers use it as a mointing point to retrieve files from the NFS server

Test the configuration and make sure the connection returns Success. Remember, that TCP port 22 on NFS server must be open to receive SSH connections.

<img width="1009" alt="Screenshot 2022-12-02 at 01 27 19" src="https://user-images.githubusercontent.com/61475969/205194009-079fcb63-07b3-4d01-ab64-3b16775cb7f5.png">

<img width="1009" alt="Screenshot 2022-12-02 at 01 27 57" src="https://user-images.githubusercontent.com/61475969/205194074-decd885b-3694-4ce0-b03f-760bee7a0065.png">

We specify ** on the send build artifacts tab meaning it sends all artifact to specified destination path(NFS Server).

<img width="821" alt="Screenshot 2022-12-02 at 01 29 19" src="https://user-images.githubusercontent.com/61475969/205194192-3649999d-bcc7-4a7e-a2a4-e1b114175927.png">


<img width="736" alt="Screenshot 2022-12-02 at 01 30 09" src="https://user-images.githubusercontent.com/61475969/205194292-599a1714-517c-4436-b037-7a825777e195.png">

Now make a new change on the source code and push to github, Jenkins builds an artifact by downloading the code into its workspace based on the latest commit and via SSH it publishes the artifact into the NFS Server to update the source code.

<img width="1393" alt="Screenshot 2022-12-02 at 01 50 23" src="https://user-images.githubusercontent.com/61475969/205196536-cb4d6e1c-9eca-402a-83d0-49c073acfef3.png">

Configure the job/project to copy artifacts over to NFS server. On main dashboard select “Manage Jenkins” and choose “Configure System” menu item.

<img width="930" alt="Screenshot 2022-12-02 at 01 51 39" src="https://user-images.githubusercontent.com/61475969/205196695-88c2799d-342c-4a0e-9b22-21bfe8efdab6.png">

Save the configuration, open your Jenkins job/project configuration page and add another one “Post-build Action”.

<img width="783" alt="Screenshot 2022-12-02 at 01 52 54" src="https://user-images.githubusercontent.com/61475969/205196825-8541db1c-e92c-4dbc-b3b5-e42d6d2b7e73.png">


Configure it to send all files produced by the build into our previouslys define remote directory. In our case we want to copy all files and directories - so we use "**".

If you want to apply some particular pattern to define which files to send - use this syntax.

<img width="765" alt="Screenshot 2022-12-02 at 01 54 27" src="https://user-images.githubusercontent.com/61475969/205197033-256abb45-087b-4aac-8378-c4110cf35460.png">

Make changes in the README.MD file in the git account and confirm if it will sync along with jenkins and show in the NFS server.













