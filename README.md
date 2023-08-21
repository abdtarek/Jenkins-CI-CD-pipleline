# This is Jenkins CI/CD pipeline with GitHub webhook integration for Deploying Docker application on EC2 instances using the declarative pipeline
### Setup CI/CD with:

-EC2

-jenkins

-Docker

-Github

### Steps:
1. First of all, go to the AWS portal, and create a new instance. As,

· Name: jenkins-server

· AMI: ubuntu.

· Instance type: t2.micro (free tier).

· Key pair login: Create > docker.pem.

· Allow HTTP.

· Allow HTTPS.

(Download the .pem file.)

Click on Launch Instance.

<img width="1648" alt="Screenshot 2023-08-19 at 11 08 25 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/03d1b793-bc8f-4856-8e61-7b332f205ecf">

2. Now, connect to the EC2 instance that you have created. Copy the SSH from server:
<img width="1518" alt="Screenshot 2023-08-19 at 11 15 47 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/6d95b780-0ab3-434c-b515-b77ec7d95c36">


3. Go to the download folder, where the .pem file is placed and open the terminal in the same location, and paste the SSH.

4. In the machine, run the command
`ssh-keygen`

This will generate public and private keys in the machine.

Id_rsa — Private Key.

Id_rsa.pub — Public Key.

<img width="586" alt="Screenshot 2023-08-19 at 11 18 42 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/f37eecd6-3861-4d88-8011-3506088a8009">

5. Now we will install Jenkins on the machine, by following this link

https://www.jenkins.io/doc/book/installing/linux/

This will automatically install java with Jenkins.

6. Install Docker as well to the machine by running,

`Sudo apt-install docker.i`

7. Now check if it got installed by running `jenkins —-version` and `docker —-version`

<img width="586" alt="Screenshot 2023-08-19 at 11 39 34 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/beeecdfa-5afc-4ba5-bc39-fa6a6d9fd224">

8. Now, we will allow ports 8080 and 8001 for this machine from a security group. We can find the security group in the VM description. Now, here we need to allow “Inbound Rule” as below:

<img width="1524" alt="Screenshot 2023-08-19 at 11 47 57 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/1ea601df-b650-46ae-bd57-b7a6e8bf0229">



9. Now, Copy the Public Ip of the machine and paste it to the browser to access the Jenkins portal. As,

“public ip of the EC2:8080”
----look for the ip from details part, because if you stopped and restart the instance, it changes.----

<img width="1524" alt="Screenshot 2023-08-19 at 11 50 11 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/ee029d4a-d3d4-4007-9bb9-f3bf840a4392">

10. We need an Administrator Password to unlock this. For that, go to the provided highlighted path in the upper screenshot.

`cat /var/lib/Jenkins/secrets/initialAdminPassword`
<img width="1524" alt="Screenshot 2023-08-19 at 11 57 11 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/8264bc9e-b34a-4550-b577-06627ae64388">

11. Now Click on, “Install Suggested Plugins”
 

<img width="1524" alt="Screenshot 2023-08-19 at 11 57 50 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/7331049a-b8bb-4c7c-9ab9-803fbc25af75">


12. Create a Freestyle project in Jenkins and configure it to fetch code from GitHub.


<img width="1524" alt="Screenshot 2023-08-20 at 7 29 06 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/eb675c3e-f84e-4326-a379-833e9a2ab966">


13.Add build steps to build Docker image and create a container


<img width="1496" alt="Screenshot 2023-08-22 at 1 43 52 AM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/5ec730f0-be3b-4390-80bc-d01cfe99d585">



14.Trigger a build and check for success in the console output

<img width="1050" alt="Screenshot 2023-08-21 at 7 18 37 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/487ac399-474f-43f4-bb09-447d34aca3e4">


### as you see the pipeline failed, so for troubleshooting, look in the console output.
as you see its jenkins permission problem


there i had to give jenkins user permission to connect to docker daemon socket by typing commands

`sudo usermod -aG docker jenkins`

`sudo systemctl restart jenkins`

and voila!!

<img width="1496" alt="Screenshot 2023-08-21 at 7 31 51 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/d437d70e-0c42-4b68-941b-e918d566bdeb">

15.now check if the container is running by opening the browser with the port we forward for it

<img width="1496" alt="Screenshot 2023-08-21 at 7 42 04 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/95f59e50-1113-4ab3-8849-87f0141d6567">

### the site couldnt be reached , i love troubleshooting

for troublshooting here it must a problem either with the container port or in the EC2 inbound rules
by opening the port assigned in the container from the inbound rules in the security rules, it should work

<img width="1536" alt="Screenshot 2023-08-21 at 7 51 35 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/c36bfa75-ad3c-49c9-b700-4c415fb197f2">


16.Now, our goal is,

· Whenever the developer commits their code in GitHub, after every commit, it should reflect in the live web app.

· For that, we will use “GitScm polling”.

· Every time, a developer made a commit, a trigger will run automatically, which will rebuild the image and run a container on your behalf as a part of automation that will run the pipeline automatically.

17. Now, configure the project again, and add

Build Trigger: GitHub hook trigger for GitScm polling.

Description: GitHub webhook integration


![integ](https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/0a6117fb-03b4-4461-b10d-0bc5a7b1f5cf)

18. Create an SSH and GPG key in GitHub and add a webhook with the necessary details.

<img width="1496" alt="Screenshot 2023-08-21 at 9 41 21 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/9395efad-ca44-4876-904c-bf33d4e1c656">


19. Add Payload URL: http://<public_ip_of_ec2>:8080/github-webhook/

    
<img width="1496" alt="Screenshot 2023-08-21 at 9 41 36 PM" src="https://github.com/abdtarek/Jenkins-CI-CD-pipleline/assets/137318449/3ccb3383-0c83-47e1-b0d9-9b1318899b68">


### Now if we make changes in the code and push to GitHub to automatically trigger the pipeline and deploy the new code.





