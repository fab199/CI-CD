<h2>Project Architecture.</h2>

<br/>
<img src="https://i.imgur.com/PYu2Lte.png" height="80%" width="80%" alt="the project architecture"/>
<br />


The project's architectural design revolves around a continuous delivery pipeline for Docker containers. Here's a breakdown of the architectural design.
1. **Code Repository (GitHub):**
   - Developers push code changes, including Dockerfile and Helm charts, to the GitHub repository.
2. **Continuous Integration Server (Jenkins):**
   - Jenkins is configured to monitor the GitHub repository for changes.
   - Upon detecting changes, Jenkins fetches the code and initiates the pipeline.
3. **Code Analysis (SonarQube):**
   - The fetched code undergoes code analysis using SonarQube to ensure code quality.
   - SonarQube evaluates the code against predefined quality gates.
4. **Artifact Building (Maven):**
   - If the code passes the quality gates, Jenkins builds the artifact using Maven.
   - This step involves compiling code, running tests, and generating the application artifact.
5. **Docker Image Building (Docker Engine):**
   - Once the artifact is built successfully, Jenkins initiates the Docker image building process.
   - Docker Engine builds the Docker image based on the Dockerfile provided in the code repository.
6. **Docker Image Registry (Docker Hub):**
   - The built Docker image is then pushed to Docker Hub, which serves as the Docker image registry.
   - Docker Hub hosts and manages the Docker images, making them accessible for deployment.
7. **Deployment to Kubernetes Cluster (Helm):**
   - Helm, a package manager for Kubernetes, is used to deploy the Docker image to a Kubernetes cluster.
   - Helm charts, containing the necessary configuration for deployment, are applied to the Kubernetes cluster.
   - Helm manages the deployment of the Docker image along with any associated resources like services, secrets, and volumes.
8. **Kubernetes Cluster:**
   - The Kubernetes cluster serves as the container orchestration platform where the Docker containers are deployed.
   - It manages the lifecycle of the containers, ensuring scalability, resilience, and efficient resource utilization.
9. **Jenkins Slave (Cops VM):**
   - Additionally, a Jenkins slave is configured on a VM provisioned with Cops.
   - This slave facilitates the execution of Helm commands for deploying Helm charts to the Kubernetes cluster.

This architectural design ensures a seamless flow from code changes to deployment, with automated processes for building, testing, and deploying Docker containers within a Kubernetes environment.

<h2>Project Execution Steps:</h2>
<br/>

   - Set up continuous integration with Jenkins, SonarQube and Nexus.
   - Created or use an existing Docker Hub account and store credentials in Jenkins.
   - Configured Docker Engine in Jenkins and install necessary plugins (docker pipeline, docker, pipeline utility).
   - Created a Kubernetes cluster using kops and install Helm on the kops VM.
   - Developed Helm charts with application image variables and test them in a designated Kubernetes namespace.
   - Created a declarative pipeline in Jenkins, defining build, test, Docker build, and Helm chart deployment processes.
   - Added the kops VM as a Jenkins slave.
   - Updated the Git repository with Dockerfiles, Helm charts, and Jenkinsfile.
   - Finally, created a Jenkins job for the pipeline and test its execution.

<h2>Set up continuous integration with Jenkins, SonarQube and Nexus</h2>

Logged into Jenkins and set up SonarQube integration and then create Kubernetes Cluster from Kops

<br/>
<img src="https://i.imgur.com/jNmjuKX.png" height="80%" width="80%" alt="jenkins, sonarqube integration"/>
<br />


<h2>Stored the details of Docker Hub logins into Jenkins credentials.</h2>

<br/>
<img src="https://i.imgur.com/bbhEF0c.png" height="80%" width="80%" alt="jenkins credentials"/>
<br />


<h2>Configured Docker Engine in Jenkins and install necessary plugins (docker pipeline, docker, pipeline utility).</h2>

Installing Docker engine in the Jenkins server because I'll be running docker build command from Jenkins.
<br/>
<img src="https://i.imgur.com/HdOCdcy.png" height="80%" width="80%" alt="jenkins credentials"/>
<br />

Installed pluggins such as docker, docker pipeline and pipeline utility.
<br/>
<img src="https://i.imgur.com/CDqsSoN.png" height="80%" width="80%" alt="pluggins"/>
<br />

<h2>Created a Kubernetes cluster using kops and install Helm on the kops VM.</h2>

<br/>
<img src="https://i.imgur.com/mwEshsG.png" height="80%" width="80%" alt="pluggins"/>
<br />

Helm is a packaging system for definition files, Where you can package all the definition files for your project stack and it can also be deployed to Kubernetes cluster. So instead of managinging and maintaining all the definition files seperately, you can use Helm to package all of them and run them or update them on Kubernetes cluster.

<br/>
<img src="https://i.imgur.com/btDOk5J.png" height="80%" width="80%" alt="pluggins"/>
<br />

<h2>Developed Helm charts with application image variables</h2>

 **Helm Chart Creation**:
   - Navigated into the `helm` directory.
   - Run the command `helm create vprofile-charts` to create a Helm chart named `vprofile-charts`.
   - Navigated into the `vprofile-charts/templates` directory.
   - Removed all sample template files.
   - Copied all contents from the `kubernetes/app` directory of the source code repository to the `vprofile-charts/templates` directory in the new repository.
   - Opened the `vprofile-application-deployment.yaml` file and replace the static image reference with a variable reference (`{{ .Values.appImage }}`) for dynamic image deployment.

<br/>
<img src="https://i.imgur.com/SQnmiZB.png" height="80%" width="80%" alt="helm setup"/>
<br />

<br/>
<img src="https://i.imgur.com/iAlzbKf.png" height="80%" width="80%" alt="helm"/>
<br />

`See everything getting created, which includes the pods, service deployment, replica sets. So that's the complete stack deployment by using one command.`

<br/>
<img src="https://i.imgur.com/s3LhhsG.png" height="80%" width="80%" alt="helm charts"/>
<br />

<h2>Created a declarative pipeline in Jenkins, defining build, test, Docker build, and Helm chart deployment processes.</h2>

Steps Taken:

1. **Jenkinsfile Creation**:
   - Created a new project from version control using the repository containing the source code.
   - Created a new file named `Jenkinsfile` in the `helm/charts` directory.
   - Defined new variables for the Docker Hub registry, image name, and Jenkins credentials.
   - Added new stages for building and uploading Docker images, pushing images to Docker Hub, and removing unused Docker images.
   - Added a stage for deploying the application to Kubernetes cluster using Helm.
   - Updated the stage for SonarQube analysis to use the correct tool name.

2. **Kops EC2 Instance Setup**:
   - Installed OpenJDK 8 JDK on the Kops EC2 instance.
   - Created a directory named `Jenkins` in the home directory of the Ubuntu user.
   - Assigned ownership of the `Jenkins` directory to the Ubuntu user.
   - Updated the security group of the Kops EC2 instance to allow SSH connections from Jenkins.

3. **Helm Deployment**:
   - Wrote a stage in the Jenkinsfile to deploy the application to the Kubernetes cluster using Helm.
   - Ensured that the Helm commands are executed on the Kops EC2 instance.
   - Set the namespace for deployment to `prod`.


<h2>Add the kops VM as a Jenkins slave</h2>

Added a new node named `Kops` and Configure the node to connect via SSH using the private IP address of the Kops EC2 instance, Ubuntu username, and SSH private key.

<br/>
<img src="https://i.imgur.com/mGMb2Uj.png" height="80%" width="80%" alt="Kops as jenkins slave"/>
<br />

<h2>Creating a Jenkins job for the pipeline and test its execution</h2>

Steps taken:

   - Create a new job in Jenkins named `kube-cicd`.
   - Configured the job to clone the source code from Git repository.
   - Used the project repository and the branch.

<br/>
<img src="https://i.imgur.com/QhJxZsH.png" height="80%" width="80%" alt="Kops as jenkins slave"/>
<br />

Run the job and monitored the progress.
<br/>
<img src="https://i.imgur.com/mGEiuGX.png" height="80%" width="80%" alt="Run jobs"/>
<br />

The project completed successfully.
<br/>
<img src="https://i.imgur.com/xkuc0cY.png" height="80%" width="80%" alt="Run jobs"/>
<br />

<h2>Verification</h2>


   - Verified the deployment on the Kubernetes cluster.
   - Checked the status of pods and confirm that the latest image is deployed.
   
<br/>
<img src="https://i.imgur.com/b8FA602.png" height="80%" width="80%" alt="Run jobs"/>
<br />

**Got the load balancer URL to check the application on the browser.**

<br/>
<img src="https://i.imgur.com/qkSYzcs.png" height="80%" width="80%" alt="Run jobs"/>
<br />

So the App is up and running.
<br/>
<img src="https://i.imgur.com/bUc8nWa.png" height="80%" width="80%" alt="Run jobs"/>
<br />
<br/>
<img src="https://i.imgur.com/naLlSpy.png" height="80%" width="80%" alt="Run jobs"/>
<br />

`The entire application stack is deployed successfully by using Helm from Jenkins and fetching the latest image for the application.`
