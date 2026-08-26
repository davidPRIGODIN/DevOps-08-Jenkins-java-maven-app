# DevOps-08-Jenkins-java-maven-app


This project demonstrates how to create and configure Jenkins Freestyle Jobs.

Freestyle Jobs are used for very simple workflows.

## 1. Install Jenkins

On DigitalOcean:

* Create a droplet (Basic, regular, 2 CPU, 4 GB).
* Rename the droplet as `jenkins-server`
* Networking → Firewalls → Create Firewall.
* Name: `jenkins-firewall`
* Add an inbound rule:

  * Type: Custom
  * Protocol: TCP
  * Port: 8080
* Create the firewall.
* Apply it to the `jenkins-server` droplet.

### Install Docker
```bash
apt update && apt install docker.io
```

### Run Jenkins Server
```bash
docker run -p 8080:8080 -p 50000:50000 -d \
> -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
```

### Jenkins UI
* Go to the browser an type `<server-ip>:8080`.

### Install Build Tools on Jenkins
* Clic on the gear icon to Administer Jenkins.

#### Install Maven using Tools section

* System Configuration → Tools → Maven → Add Maven
* Install from Apache.
* Select latest version.
* Save

#### Install Node.js and npm directly in the container
Installing a tool directly on the server is much more flexible and convenient than installing it through a plugin.

```bash
docker ps
```
```bash
docker exec -u 0 -it <container-id> bash
```

##### Download a script that will have all the commands to install Node.js and npm
```bash
curl -sL https://deb.nodesource.com/setup_20.x -o nodesource_setup.sh
```
```bash
bash nodesource_setup.sh
```
```bash
apt install nodejs
```
```bash
node -v && npm -v
```

#### Install the Stage View plugin

The **Stage View** plugin allows you to visualize the progress of each stage of your pipeline directly in the Jenkins UI.

* Go to **Plugins → Available plugins** and search for **Stage View**.
* Select the plugin, click **Install**, then click **Restart Jenkins** at the bottom of the page.
* If Jenkins does not restart automatically, find the container with:
  
```bash
docker ps -a
```

* Start the Jenkins container with:

```bash
docker start <container-id>
```

* Verify that the plugin is installed under **Plugins → Installed plugins**.
<br>

## 2. Create simple Freestyle Job & Plugin Configuration

### Create Freestyle Job
* New Item → Freestyle project
* Create a Job → Freestyle project
* Enter the job name: `my-job`

### Write npm commands
* my-job → Configure → Build Steps → Add build step → Execute script shell
```bash
npm --version
```

### Write mvn commands
* my-job → Configure → Build Steps → Add build step → Invoke top-level Maven targets
* Maven version: Select the installed Maven version.
* Goals: `--version` (`mvn --version`).
* Save

### Build the project and check that there are no errors
* my-job → Build Now
* my-job → Builds → Down Arrow → Console Output: Finished: SUCCESS
<br>

## 3. Configure Git Repository

Connect Jenkins Job to your Git repository.

### Set Git URL Repository
* my-job → Configure → Source Code Management → Git
* Repository URL : https://github.com/davidPRIGODIN/DevOps-08-Jenkins-java-maven-app.git

### Allow your Jenkins Server to connect to your Git Repository
* Credentials → Add → Global credentials
* Kind : Username with password
* Enter your Username and Password from GitLab or GitHub.
* ID: github-credentials
* Add
* Select the credentials you created.
* Save

### Build the project and check that there are no errors
* my-job → Build Now
* my-job → Builds → Down Arrow → Console Output: Finished: SUCCESS
<br>

## 4. Complete Task from Git Repo in Jenkins Job

Run code from Git repository.

### Select the Branch with the code to be executed
* my-job → Configure → Source Code Management
* Branches to build →  Branch Specifier: */jenkins-jobs

### Remove previous command
* Build Steps →  Execute shell →  Command: `npm --version`

### Run Bash file instead
```bash
chmod +x freestyle-build.sh
```
```bash
./freestyle-build.sh
```
* Save

### Build the project and check that there are no errors
* my-job → Build Now
* my-job → Builds → Down Arrow → Console Output: Finished: SUCCESS
<br>

## 5. Run tests and build Java Application

We are gonna take a Java Maven application.

We are gonna run tests from the project.

And then we are gonna build a Jar file of that application.

* New Item → Freestyle project
* Name: `java-maven-build`
* java-maven-build → Configure → Source Code Management → Git
  * Repository URL: https://github.com/davidPRIGODIN/DevOps-08-Jenkins-java-maven-app.git
  * Credentials: Select the credentials you created.
  * Branches to build/Branch Specifier: */jenkins-jobs
* java-maven-build → Configure →  Build Steps
* Add build step → Invoke top-level Maven targets
* Maven Version: Select your maven version
* Goals: `test`
* Add build step (Same)
* Goals: `package`
* Save

### Build the project and check that there are no errors
* java-maven-build → Build Now
* java-maven-build → Builds → Down Arrow → Console Output: Finished: SUCCESS
<br>

## Acknowledgements

This demo project was created as part of the DevOps Bootcamp by **TechWorld with Nana**.<br>
Many thanks to Nana for creating such a comprehensive and practical learning experience.
