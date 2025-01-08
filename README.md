# Detailed POC for Java Application CI/CD with Blue-Green Deployment Using Jenkins

### 1. Jenkins Environment Setup
#### Jenkins Installation
###### Install Jenkins on a server (local or cloud) using the appropriate installer for your OS.
###### Recommended Java version: JDK 11 or above for Jenkins.
###### Use the following plugins:
###### Maven Integration: For building Java applications.
###### Pipeline: To define CI/CD pipelines.
###### Blue Ocean: For a modern Jenkins UI.
###### Docker Pipeline: To integrate Docker commands into the pipeline.
###### Kubernetes CLI: To manage Kubernetes resources.
###### SonarQube Scanner: For code quality and vulnerability analysis.
###### Trivy Scanner: For security scanning of images and filesystem.

### 2. Configure Tools and Credentials
#### Maven:
Add Maven to Jenkins under Global Tool Configuration.
Name: maven3, Installation Path: Specify the Maven directory or auto-install.
#### Docker:
Install Docker on the Jenkins host and configure Docker CLI.
Provide credentials for Docker Hub or any other container registry.
#### SonarQube:
Integrate SonarQube with Jenkins by adding the server details and credentials under Manage Jenkins > Configure System.
#### Kubernetes:
Add Kubernetes credentials (Service Account token) to Jenkins.
Install kubectl on the Jenkins server for executing Kubernetes commands.

### 3. Jenkins Pipeline Design
#### Pipeline Stages
The Jenkins pipeline will include the following stages:

#### Git Checkout:
###### Clone the repository from GitHub.
###### Ensure the pipeline always works with the latest code from the main branch.

#### Code Compilation:
###### Use Maven to compile the Java application code.

#### Testing:
###### Run unit tests using Maven with the mvn test command.
###### Skip tests in build stages if explicitly required.

#### Static Code Analysis (SonarQube):
###### Analyze the code using SonarQube.
###### Upload results and set a quality gate.

#### Filesystem Vulnerability Scanning (Trivy):
###### Scan the project files for known vulnerabilities.

#### Build Application Artifact:
###### Use Maven to create the JAR file using mvn package.

#### Publish Artifact to Nexus:
###### Push the artifact (JAR) to a Nexus repository for versioned storage.

#### Docker Image Build:
###### Build a Docker image of the application using the JAR artifact.

#### Docker Image Vulnerability Scanning (Trivy):
###### Scan the built image for security vulnerabilities.

#### Push Docker Image:
###### Push the Docker image to a container registry (e.g., Docker Hub).

#### Kubernetes Deployment:
###### Deploy MySQL to the Kubernetes cluster (if not already running).
###### Deploy the application to either the Blue or Green environment based on the pipeline parameters.

#### Switch Traffic:
###### Update the Kubernetes service to route traffic to the selected environment.

#### Post-Deployment Verification:
###### Verify pod status and service availability.

### 4. Blue-Green Deployment Strategy
#### Deployment Files
###### app-deployment-blue.yml:
###### Contains Kubernetes deployment for the Blue environment.
###### Labels: version: blue.
###### app-deployment-green.yml:
###### Contains Kubernetes deployment for the Green environment.
###### Labels: version: green.
###### bankapp-service.yml:
###### Kubernetes service manifest.
######Default selector points to the Blue deployment initially.
#### Traffic Switching
###### Modify the service's selector to switch between version: blue and version: green.
###### Use a parameterized Jenkins pipeline to control traffic switching.
