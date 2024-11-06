# Task Master Pro

Welcome to Task Master Pro, a comprehensive Java application designed to manage tasks efficiently. This project is developed and maintained by DevOps Shack, a YouTube channel dedicated to DevOps tutorials and best practices.

# Project Name: Application Deployment Workflow

## Introduction
This document outlines the process for building and deploying an application to multiple environments, including development and production clusters.

## Workflow Overview

### Client Request
The client creates a Jira ticket for any updates or new features.

### Developer Process

#### Local Development
- The developer clones the repository to their local machine.
- They implement the new feature or update.
- The code is tested locally to ensure functionality.

#### Code Push
- The developer pushes the code to the dev branch on GitHub.

### Continuous Integration and Deployment (CI/CD)

#### GitHub Actions Pipeline
- **Compilation**
  - The pipeline triggers automatically on code push.
  - The code is compiled to check for syntax errors.
- **Unit Testing**
  - Unit tests are executed to verify the functionality of the application.
- **Dependency Check**
  - Aqua Trivy scans the application dependencies for vulnerabilities and outdated packages.
- **Code Quality Check**
  - SonarQube analyzes the code for bugs, vulnerabilities, and code smells.
  - Code coverage is assessed to ensure a minimum quality threshold.
- **Build Artifacts**
  - The application is built into an executable file (e.g., JAR for Java applications).
  - A Docker image is created and scanned with Aqua Trivy for vulnerabilities.
- **Deployment to Development Cluster**
  - The Docker image is pushed to Docker Hub.
  - YAML manifest files are used to deploy the application to the development cluster.

### Production Deployment

#### Pre-Deployment
- After successful deployment and verification in the development cluster, the Docker image is tagged (e.g., prod-latest).

#### Production Deployment
- The tagged Docker image is used to create new YAML manifest files for the production cluster.
- The application is deployed to the production cluster.

## Cluster Setup

### Production Cluster
We will set up an Amazon EKS cluster managed by AWS.

### Development Cluster
We will set up a self-hosted Kubernetes cluster.

### Open Required Ports
Ensure that the necessary ports are open in your security group's inbound rules.

### Creating a Self-hosted Kubernetes Cluster (Development Cluster)

#### Create Virtual Machines

- **Master Node**
  - Create a virtual machine with the following specifications:
    - OS: Ubuntu 20.04 LTS
    - Size: T2 medium
    - Key Pair: Use an existing key pair or create a new one. If creating a new key, name it and ensure the format is PEM.

- **Worker Node**
  - Create another virtual machine with the same specifications as the Master node.

#### Network Settings
Select the security group configured with the necessary open ports.

#### Storage
Set the storage size to 25 GB.

#### Launch VMs
Launch the virtual machines and wait for them to be in the running state. Name one as the Master node and the other as the Worker node.

#### Connect via SSH
Connect to each machine via SSH (e.g., MobaXterm) to proceed with the Kubernetes setup.


## Setting Up Master and Worker Nodes

### 1. Update the Package List
This command updates the list of available packages and their versions, ensuring you have the latest information on the newest versions and their dependencies.

### 2. Install Docker
This command installs Docker, a containerization platform, which is essential for running Kubernetes containers. The `-y` flag automatically confirms the installation.

### 3. Set Permissions for Docker
This command changes the permissions of the Docker socket file, allowing non-root users to run Docker commands.

### 4. Install Transport Packages and Certificates
This command installs packages necessary for allowing the `apt` package manager to use HTTPS for retrieving packages. It also installs `curl` for downloading files and `gnupg` for handling encryption and signing.

### 5. Create a Directory for the Kubernetes Keyring
This command creates the `/etc/apt/keyrings` directory with permissions set to `755`, which is required for storing the Kubernetes signing key.

### 6. Download and Store the Kubernetes Signing Key
This command downloads the Kubernetes signing key and converts it to the GPG format, saving it in the `/etc/apt/keyrings/kubernetes-apt-keyring.gpg` file. This key is used to verify the authenticity of Kubernetes packages.

### 7. Add the Kubernetes Repository to the Package List
This command adds the Kubernetes repository to the list of sources from which `apt` can install packages. It specifies that packages from this repository should be verified using the previously downloaded GPG key.

### 8. Update the Package List Again
This command refreshes the package list to include the newly added Kubernetes repository.

### 9. Install Kubernetes Components
This command installs specific versions of `kubeadm`, `kubelet`, and `kubectl`, which are essential components for setting up and managing a Kubernetes cluster. The `-y` flag confirms the installation automatically.



This version includes only the essential information without any extra details.


## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

