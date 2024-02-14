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
   - Create or use an existing Docker Hub account and store credentials in Jenkins.
   - Configure Docker Engine in Jenkins and install necessary plugins (docker pipeline, docker, pipeline utility).
   - Create a Kubernetes cluster using Cops and install Helm on the Cops VM.
   - Develop Helm charts with application image variables and test them in a designated Kubernetes namespace.
   - Add the Cops VM as a Jenkins slave.
   - Create a declarative pipeline in Jenkins, defining build, test, Docker build, and Helm chart deployment processes.
   - Update the Git repository with Dockerfiles, Helm charts, and Jenkinsfile.
   - Finally, create a Jenkins job for the pipeline and test its execution.

<h2>Set up continuous integration with Jenkins, SonarQube and Nexus</h2>

Log into Jenkins and set up SonarQube integration and then create Kubernetes Cluster from Kops

<br/>
<img src="https://i.imgur.com/jNmjuKX.png" height="80%" width="80%" alt="jenkins, sonarqube integration"/>
<br />


<h2>Store the details of Docker Hub logins into Jenkins credentials.</h2>

<br/>
<img src="https://i.imgur.com/bbhEF0c.png" height="80%" width="80%" alt="jenkins credentials"/>
<br />
