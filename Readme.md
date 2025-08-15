<img src="https://skillicons.dev/icons?i=jenkins" />

## 🤖 What is Jenkins?

Jenkins is an open-source automation server that enables developers to `build`, `test`, and `deploy` their software applications more efficiently. It provides a wide range of plugins and integrations, allowing teams to automate various stages of the software development lifecycle (SDLC), from code integration to delivery.

## 🏗️ Jenkins Infrastructure

- **🎯 Master Server**

  - Controls Pipeline
  - Schedules Builds

- **⚙️ Agent/Minions**
  - Executes Build Tasks
  - Reports Status to Master

## 🤖 Jenkins Agents

Agents, also known as Minions, are the worker nodes in a Jenkins setup. They are responsible for executing the build tasks assigned by the Jenkins Master.

- **🏢 Permanent Agents**

  - Dedicated servers for running jobs
  - Always online and connected to the Jenkins Master
  - Ideal for long-running tasks and builds

- **☁️ Cloud Agents**
  - Ephemeral/Dynamic agents that can be provisioned on-demand
  - Useful for short-lived tasks and dynamic workloads
  - e.g. Docker containers, Kubernetes pods, AWS EC2 Fleet Manager

## 🔨 Build Types

Jenkins supports various build types to accommodate different development workflows and requirements. The main build types include:

- **🎯 Freestyle Projects**

  - Basic and easy-to-configure jobs
  - Suitable for simple build tasks
  - Limited in terms of complex workflows

- **🔄 Pipeline Projects**
  - Define entire build processes as code
  - Supports complex workflows and stages
  - Highly customizable and maintainable

## 🐳 Setting Up Jenkins using Docker

### 🔨 Build the Jenkins BlueOcean Docker Image

```bash
docker build -t myjenkins .
```

### 🌐 Create the network 'jenkins'

Docker network is used to allow communication between the Jenkins master and other containers.

```bash
docker network create jenkins
```

### 🚀 Run the Container (windows)

```bash
docker run --name jenkins --restart=on-failure --detach `
  --network jenkins --env DOCKER_HOST=tcp://docker:2376 `
  --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 `
  --volume jenkins-data:/var/jenkins_home `
  --volume jenkins-docker-certs:/certs/client:ro `
  --publish 8080:8080 --publish 50000:50000 myjenkins:latest
```

### 🔑 Get the Password

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### 🌐 Connect to Jenkins

```bash
http://localhost:8080
```

### ⚙️ Configure Jenkins

- Install recommended plugins
- Set up admin user
- Configure system settings

## 🎯 Creating a Simple Freestyle Project

1. 🏠 Open Jenkins Dashboard
2. ➕ Click on "New Item"
3. 📝 Enter a name for your job
4. 🎯 Select "Freestyle project"
5. 🏷️ Name the project
6. ✅ Click "OK"
7. ⚙️ Configure your project
   - **📂 Source Code Management:** Select your version control system (e.g., Git) and provide the repository URL
   - **⏰ Build Triggers:** Set up triggers for when the build should be initiated (e.g., on code commits)
   - **🌍 Build Environment:** Configure any necessary build environment settings
   - **🔨 Build Steps:** Define the steps to build your project (e.g., invoking a build tool)
8. 💾 Click "Save"

### ⚡ Build Triggers

- `🔗 Trigger builds remotely (e.g., from scripts)`
  - You can trigger builds remotely by sending a request to the Jenkins server with the appropriate job name and parameters
- `🔗 Build after other projects are built`
  - This trigger allows you to specify that a build should be initiated after the successful completion of other specified projects
- `⏰ Build periodically`
  - This trigger allows you to schedule builds to run at specific intervals (e.g., daily, weekly)
- `🐙 GitHub hook trigger for GITScm polling`
  - This trigger allows you to initiate a build when a change is pushed to the GitHub repository
- `🔍 Poll SCM`
  - This trigger allows you to poll the source code management system for changes at regular intervals
  - Example: Check for changes every 5 minutes (`*/5 * * * *`)

### 📝 Example Project

- Clone a python script from a Git repository and run it

1. ➕ Create a new freestyle project - "CloneAndRunPythonScript"
2. ⚙️ Configure the project
   - **📂 Source Code Management:**
     - Select "Git" and provide the repository URL (https://github.com/devopsjourney1/jenkins-101)
     - If the repository is private, add credentials to access it
     - Branch Specifier: Set to `*/main` or the appropriate branch
   - **🔨 Build Steps:**
     - Add a build step to execute a shell command to run the script
     - Command: `python3 helloworld.py`
3. 💾 Click "Save"

## ☁️ Setting Up Cloud Agents

1. 🏠 Open Jenkins Dashboard
2. ⚙️ Click on "Manage Jenkins"
3. 🖥️ Click on "Manage Nodes and Clouds"
4. ☁️ Click on "Configure Clouds"
5. ➕ Click on "Add a new cloud" and select the cloud type (e.g., Docker)
6. ⚙️ Configure the cloud settings
7. 💾 Click "Save"

### 🐳 Example Docker Agent Configuration

1. 🐳 Select "Docker" as the cloud type
2. 🏷️ Name the Docker cloud - "My Docker Cloud"
3. 🔧 Setup the Docker host container

   - Create alpine/socat container to forward traffic from Jenkins to Docker Desktop on Host Machine

   ```bash
   docker run -d --restart=always -p 127.0.0.1:2376:2375 --network jenkins -v /var/run/docker.sock:/var/run/docker.sock alpine/socat tcp-listen:2375,fork,reuseaddr unix-connect:/var/run/docker.sock

   docker inspect <container_id> # Inspect the container
   ```

   - Set the Docker Host URI to `tcp://<jenkins-host-ip>:2375`
   - Enable Docker connection
   - Test the connection to the Docker host

4. ☁️ Configure the Cloud
   - Setup Agent template
     - Labels and the Name - "docker-agent-alpine"
     - Set the Docker image - "jenkins/agent:alpine-jdk11"
     - Instance Capacity - 2
     - Remote File System Root - `/home/jenkins`
5. 💾 Save the configuration
6. ✅ Verify the cloud agent is connected and online in the Jenkins dashboard
7. ➕ Add a job that uses the Docker agent for building and testing applications

## 🚀 Creating a CI/CD Pipeline

1. 🏠 Open Jenkins Dashboard
2. ➕ Click on "New Item"
3. 📝 Enter a name for your pipeline
4. 🔄 Select "Pipeline"
5. ✅ Click "OK"
6. ⚙️ Configure your pipeline
   - **📋 Definition:** Choose "Pipeline script" or "Pipeline script from SCM"
   - **📝 Script:** Define your pipeline stages and steps using the Jenkins Pipeline DSL
7. 💾 Click "Save"

- We also can use a Jenkinsfile for defining our pipeline as code and triggering builds with version control

### 📋 Example Pipeline

```groovy
pipeline {
    agent {
      node {
        label 'agent-name'
      }
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```
