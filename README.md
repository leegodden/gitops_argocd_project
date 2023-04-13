# Grand Real-time End to End DevOps CI/CD Project | Git | Jenkins | Docker | K8's | ArgoCD | HandsOn

`https://www.youtube.com/watch?v=kuSdi8bDztk&list=PLLu1bCv5AByFWjZ6WNC10OKXTHDT62zCc&index=2&ab_channel=Mr.DevOps`

NB: Jenkins keys have been updated since tutorial so were using the below to install jenkins in our Ec2 instacne :
`https://varunmanik1.medium.com/devops-jenkins-aws-series-part-1-how-to-install-jenkins-on-aws-ubuntu-22-04-cb0c3cdb055`

# `SETUP For new session`

////////////////////////////////////////////////////////////////

1. start docker, then minikube
2. start `mr-devops-jenkins Ec2` instance in `AWS management console`
3. login to `jenkins` and run job from `Jenkinsfile` in vscode project.

///////////////////////////////////////////////////////////////

Because `AWS Ec2 Instances` generate a new `ip4 address` when re-starting we have to ammend in the `settings.json`
file, for now until a workaround hs been found

4. Create a `EC2` instance in AWS Management console
   use ubuntu with `t2/medium` and `20 gb` storage

5. make sure docker is running & start `minikube` on computer in terminal
6. select the `Ec2 instance` in AWS console and click `Connect`

Install jenkins on our AWS Ec2 instance by connecting to the insance console and following setps below

1. Install Java: Jenkins requires `Java` to run, so the first step is to install `Java on the Ubuntu instance`. You can
   do this by running the below command:

- sudo apt-get update
  sudo apt-get install openjdk-11-jdk

Next, you need to add the `Jenkins repository` to the instance by running the below commands:

- sudo curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
- sudo echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

Now you can install `Jenkins` by running the below command:

- sudo apt-get update
  sudo apt-get install jenkins

  Once Jenkins is installed, start & enable the Jenkins service using the below command:

  - sudo systemctl start jenkins
    sudo systemctl status jenkins
    sudo systemctl enable jenkins

# Create a security group for our AWS Ec2 instance

1. select our instance then click the `Security tab`
2. Next choose `Security groups` and click `Edit inbound rules`
3. For time being we will select `all traffic` from `anywhere`

# Connecting to Jenkins on our instance

1. run `http://35.178.170.148:8080/` in browser. This is the `Public IPv4 address` if the instance and the
   standard `jenkins port` number - !!! `remember after each restart the ip4 address changes`

# configuring jenkins

You should now see the login page for jenkins

1. run `sudo cat /var/lib/jenkins/secrets/initialAdminPassword` in the instance console to retrieve th password
2. copy the password: `a5136fbd844340f7995bbe54fe103f30` and paste into the `jenkins administrtor` password field
3. Now click the `continue` button, and then from the next screen choose `install suggested plugins`

4. once `jenkins plugins` have installed successfully create a new `username`, `password` etc
5. click the `Save and Finish` button, then click the `Start using jenkins` button

# Jenkins Runner configuration

3. in `jenkins` create a new job by clicking `New Item`
4. Enter `gitops-argocd_CI` for the item name, click on `Pipeline` and then on `OK`

5. in the next General jenkins screen, select `Discard old builds` with `3` and `1` for `Days to keep builds` and
   `max # of builds to keep`

6. Click `Save` button

7. create a new project named `gitops_argocd_project`, and open in vscode. Add the `app.py` file from
   the tutorial repo: `https://github.com/vikash-kumar01/gitops_argocd_project`

8. install vscode `jenkins runner` extension if not already

9. add .vscode folder and add `settings.json` with code for `jenkins-runner.jobs` & `jenkins-runner.hostConfigs`

10. now in our `Jenkinsfile`, add our first stage `Clean up workspace`

# Run the pipeline

IMPORTANT: have the `Jenkins` file open in the editor before following steps below

11. To trigger our jenkins job click in `command pallete` and then choose below"
    `Jenkins Runner: Run Pipeline Script On Default Job`

    You shoud see the output in vscode terminal of the pipeline, as well as in the `console` of the
    build in `jenkins`

# Add environment variables and credentials

we will create `env variable` to hide our `github` abd `dockerhub` secrets

1. in vscode in Jenkinsfile add the envoronment variables for `dockerhub`, `username`, `app name`, `image tag`,
   `image name` and `Registry credentials`

2. now in jenkins go to `Dashboard` > `Manage Jenkins` > `Manage Credentials`
3. then click `System` > `Global Credentials`, then click the `Add Credentials` button

4. under ``New credentials` enter `username:` - Your `docker` username, then `password` - your `docker`
   password, then type `dockerhub` in both `ID` and `Description` fields (the same as in
   our environemnt variables)

5. Click `Create`

6. now add your `Github credentials`
7. Click `Add credentials` and add your github `username`. We won't put our github password in here
   instead we will create a `token` in our GH account

8. go to your github account select `settings` from your icon dropdown, choose `Developer Settings` then
   `Personal Access Token` > `Tokens Classic` and click `Create personal access token`

9. add a note in the `note field` choose all options except `delete repo` then click `generate token`

10. copy the token and add to tehe `password field` in `jenkins`. Add `github `for the ID and Description fields
11. click `Create` button.

# Adding Checkout Stage to our Jenkisfile

1. in Jenkinsfile add another stoge called `Checkout SCM`, using our git credentialId: `github`, the url
   of our `github repository` & the `branch` name
