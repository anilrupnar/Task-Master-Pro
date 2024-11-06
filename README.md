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

# Kubernetes Cluster Setup Documentation

## Setting Up the Master Node

### 1. Initialize Pod Network and Generate Token to Add Worker Node
Run the following command on the master node to initialize the Kubernetes master node with a specific pod network CIDR. This command also generates a token that will be used by the worker nodes to join the cluster.

- This command initializes the Kubernetes control plane on the master node and sets the pod network CIDR to `192.168.0.0/16`.

### 2. Note the kubeadm join Command
After running the master node initialization command, a `kubeadm join` command will be provided. This command is used on the worker node(s) to join them to the Kubernetes cluster. Ensure you save the join command generated, as it contains the token and certificate hash required for the worker node to join the cluster.

## Joining Worker Nodes to the Master Node

### 1. Run the kubeadm join Command on the Worker Node(s)
Use the `kubeadm join` command generated by the master node during initialization on the worker node. This command will look similar to the following:

- This step connects the worker node to the master node, allowing it to become part of the Kubernetes cluster.

## Set Up kubeconfig for kubectl Access

### 1. Create the .kube Directory
On the master node, create the `.kube` directory, which will be used to store the Kubernetes configuration file that `kubectl` requires to interact with the cluster.

### 2. Copy the admin.conf File to the Kubeconfig Directory
Copy the Kubernetes admin configuration file (`admin.conf`) from its default location to the `.kube` directory created in the previous step.

### 3. Change the Ownership of the Kubeconfig File
Change the ownership of the `config` file to the current user so that `kubectl` can use the configuration to interact with the cluster. This ensures that the current user has the necessary permissions to manage the Kubernetes cluster.

## Setting Up Calico for Networking

To configure networking within your Kubernetes cluster, Calico is set up as the network plugin. Run the following command to apply the Calico manifest, which provides networking and network policy capabilities for your cluster.

- This step sets up Calico as the networking solution for the Kubernetes cluster, enabling pod-to-pod communication and network policies.

## Setting Up Ingress-nginx Controller

To manage ingress resources and route external traffic to Kubernetes services, set up the Ingress-nginx controller. This step ensures that your cluster can expose services to external clients through HTTP or HTTPS.

- Run the necessary command to apply the Ingress-nginx controller manifest.

## Testing the Kubernetes Cluster

### 1. Check the Status of Nodes
To verify that your Kubernetes cluster is operational, check the status of the nodes in the cluster by running the appropriate command. This will list all the nodes in the Kubernetes cluster along with their status.

- **Master Node**: The master node should display a `Ready` status, indicating that it is properly initialized and operational.
- **Worker Nodes**: Worker nodes should also display a `Ready` status if they have successfully joined the cluster.

# Setting Up the Production EKS Cluster

We need to set up the Production cluster, which will be an Amazon EKS cluster. For creating the EKS cluster, we will use Terraform.

## 1. Create a Virtual Machine for Terraform

To run Terraform commands, we need a dedicated virtual machine. Although you can run Terraform from your local machine, using a separate virtual machine is often more convenient.

**Virtual Machine Details:**
- **Name**: terraform server
- **OS Version**: Ubuntu 24.04
- **Size**: T2 medium
- **Storage**: 25 GB
- **Security Group**: Use the existing security group.

Create and launch the virtual machine, and wait for it to come online.

Once the virtual machine is ready, you can proceed with running Terraform commands to set up the EKS cluster.

## 2. Connecting to the Virtual Machine and Setting Up AWS CLI

### 2.1. Connect to the Virtual Machine
- Connect to the virtual machine using SSH.
- Rename the machine as Terraform after connecting.

### 2.2. Update the System
Run the command to update the package list.

### 2.3. Install AWS CLI
To interact with AWS resources, we need to install the AWS CLI.

### 2.4. Configure AWS CLI
Run the command to configure the AWS CLI and provide your AWS credentials. You will be prompted to enter the following information:
1. **Access Key ID**: 
   - To get the Access Key ID, you can create an IAM user in the AWS Management Console with appropriate permissions and generate access keys for that user.
   - For demo purposes, you can use the root user credentials, although this is not recommended for production environments.
   - Navigate to the AWS Management Console, go to your profile, select Security Credentials, and create a new access key. Copy the Access Key ID.
2. **Secret Access Key**: 
   - This key is generated along with the Access Key ID. Copy the Secret Access Key from the same page where you obtained the Access Key ID.
3. **Default Region Name**: 
   - Enter the region closest to you. For example, use ap-south-1 for Mumbai.
4. **Default Output Format**: 
   - You can press Enter to accept the default format or specify your preferred format.

After entering the required details, the AWS CLI will be configured, and you will be connected to your AWS account.

## 3. Setting Up Terraform for EKS Cluster Creation

### 3.1. Install Terraform
Before creating an EKS cluster on AWS, ensure that Terraform is installed on your machine. Run the command to check if Terraform is already installed.

If Terraform is not present, you will need to install it. Use the command to install Terraform via Snap.

### 3.2. Verify Installation
After installation, verify that Terraform is installed correctly by running:

### 3.3. Prepare Terraform Files
To execute Terraform commands, you need the appropriate Terraform configuration files. Make sure you have these files prepared before running Terraform commands to create the EKS cluster. Here is the generalized Terraform configuration files (please update according to your environment) for setting up an EKS cluster on AWS, including both the VPC and the necessary resources for the cluster and nodes.

Feel free to adjust the names, regions, and any other parameters according to your specific requirements.

- **Create the `main.tf` file**:
   - First, create a directory for your EKS configuration.
   - Create the `main.tf` file.
   - Paste the configuration contents into `main.tf` and save the file.

- **Create the `output.tf` file**:
   - Create the `output.tf` file.
   - Paste the configuration contents into `output.tf` and save the file.

- **Create the `variables.tf` file**:
   - Create the `variables.tf` file.
   - Paste the configuration contents into `variables.tf` and save the file.

### 3.4. Initialize Terraform
Run the command to initialize the working directory, download necessary plugins, and prepare Terraform to execute commands.

### 3.5. Apply Terraform Configuration
Execute the command to apply the Terraform configuration. Terraform will prompt you to confirm the actions it plans to take. You will see a message asking if you want to perform these actions. Type yes to proceed.

Alternatively, you can use the command to automatically approve the execution without prompting for confirmation.

Terraform will then start creating resources such as virtual machines, clusters, VPCs, subnets, etc. This process typically takes 5 to 10 minutes.

# GitHub Actions Runner Setup and Pipeline Configuration

## 1. Create a New Branch
- Name the new branch `dev` (or any name of your choice).
- Create this branch from `main`.

## 2. Set Up GitHub Actions Runner
- Go to your repository’s settings.
- Navigate to **Actions > Runners**.
- Remove any existing runner (you may need to provide MFA if enabled).
- To add a new runner:
  - Click **New Runner**.
  - Choose the appropriate runner type (e.g., **Linux**).
  - Follow the provided instructions to configure the runner on a Linux machine.

## 3. Create a Virtual Machine for the Runner
- Launch a new EC2 instance.
  - Name the instance **Runner**.
  - Use the latest **Ubuntu 24.04 LTS** version.
  - Select instance size (e.g., `t2.large` or `t2.medium`).
  - Assign the existing key pair and configure the security group.
  - Set the storage size (e.g., 30 GB).
  - Click **Launch Instance** and wait for it to start.

## 4. Connect to the VM
- Once the VM is running, connect to it via SSH.
- Rename the instance to **Runner** and adjust any settings as needed.

## 5. Download and Configure the GitHub Actions Runner
1. **Create a Folder for the Runner**:  
   `mkdir actions-runner && cd actions-runner`

2. **Download the Latest Runner Package**:  
   `curl -o actions-runner-linux-x64-2.317.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-linux-x64-2.317.0.tar.gz`

3. **Optional: Validate the Package Hash**:  
   `echo "9e883d210df8c6028aff475475a457d380353f9d01877d51cc01a17b2a91161d actions-runner-linux-x64-2.317.0.tar.gz" | shasum -a 256 -c`

4. **Extract the Installer**:  
   `tar xzf actions-runner-linux-x64-2.317.0.tar.gz`

5. **Configure the Runner**:  
   `./config.sh --url https://github.com/xxxxxxxxxxx/Repo-2 --token xxxxxxxxxxxxxxx`  
   - **Runner Group**: By default, no runner group is created. You can use the default group.
   - **Runner Name**: Enter a name for the runner (e.g., **Runner-1**).
   - **Labels**: Optionally, add labels to help identify where the runner should execute jobs.
   - **Work Folder**: The default work folder is `_work`. You can change this if desired.

## 6. Setting Up Maven, Trivy, and GitHub Actions Runner
1. **Install Maven**: Ensure Maven is installed on the machine.  
   `sudo apt install maven -y`

2. **Set Up Trivy**: Install Trivy by adding its repository and then installing the package.  
   - Install dependencies:  
     `sudo apt-get install wget apt-transport-https gnupg lsb-release`
   - Add the Trivy repository key:  
     `wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -`
   - Add the Trivy repository:  
     `echo deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main | sudo tee -a /etc/apt/sources.list.d/trivy.list`
   - Update the package list and install Trivy:  
     `sudo apt-get update`  
     `sudo apt-get install trivy`

3. **Start the Runner**:  
   `./run.sh`

4. **Check the Runner Status**: After starting the runner, check the GitHub Actions settings. Initially, the runner may appear as **offline**. To bring it online, ensure the `run.sh` script is running.

# Meanwhile on the machine used to create EKS cluster check the creation of the EKS cluster
- To ensure we can access it, we need to update the kubeconfig file. Use the following command:
```bash
aws eks --region <region> update-kubeconfig --name <cluster_name>
```
- Replace <region> with your AWS region (e.g., ap-south-1) and <cluster_name> with your EKS cluster name. This command updates the kubeconfig file used for authentication.
- Next, verify that **kubectl** is installed. If it's not installed, you can install it using Snap:
```bash
sudo snap install kubectl --classic
```
- After installation, check the **cluster status** with:
```bash
kubectl get nodes
```
- This will show the nodes in your cluster and their status. If the nodes are in the "Ready" state, your cluster is up and running correctly.

# CI/CD Pipeline Setup

## Step-by-Step Instructions

### 1. Navigate to GitHub Actions:
- Go to the **Actions** tab in your GitHub repository.

### 2. Choose a Template:
- You'll find many templates to help you get started. For this example, select **"Java with Maven"** and click on **"Configure"**.

### 3. Edit the Workflow File:
- The default configuration will create a pipeline for the main branch. We want to set it up for the **Dev** branch instead.
- Copy the provided configuration.
- Navigate to the **Dev** branch and create a new file at `.github/workflows/cicd.yaml`.

### 4. Paste and Edit Configuration:
- Paste the copied configuration into the new file. Here is the configuration:
```bash
name: CICD

on:
  push:
    branches: ["Dev"]
  pull_request:
    branches: ["Dev"]

jobs:
  build:
    runs-on: self-hosted
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      - name: Build with Maven
        run: mvn package --file pom.xml
```

# Explanation of the Configuration:

## Workflow Name:
- **name: CICD** specifies the name of the workflow.

## Triggers:
- The workflow triggers on **pushes** and **pull requests** to the **Dev** branch.

## Jobs:
- The **build** job runs on a **self-hosted runner**.

## Steps:
- **Checkout Repository**: Uses `actions/checkout@v4` to check out the repository code to the runner.
- **Set up JDK 17**: Uses `actions/setup-java@v4` to set up JDK 17 with the **Temurin** distribution and enables caching for Maven dependencies.
- **Build with Maven**: Runs the `mvn package` command, specifying the `pom.xml` file.

## Additional Note:
One interesting part of GitHub Actions is that you don't need to specifically install multiple tools on the virtual machine. You can directly call the action in your pipeline and utilize it. For example, we haven't installed Java on our virtual machine, but using the `setup-java` action, we can directly use the required Java version in our pipeline.

# Commit and Push Changes:
- **Save and commit the changes.**
- **Push the changes** to the **Dev** branch.

# Verify the Pipeline:

### 1. Go to Actions:
- Navigate to the **Actions** tab in your GitHub repository.

### 2. Monitor the Pipeline:
- You should see the pipeline has started. Click on it to monitor the stages and verify that they are running correctly.

### 3. Check Success:
- Once complete, verify that the application has been built successfully.

# Step-by-Step Instructions to Extend the CI/CD Pipeline

Now, we need to add multiple stages in our pipeline. We'll add a **file system scan** using **Trivy** and a **SonarQube** analysis.

1. Navigate to the Dev Branch and Edit Workflow File:
    - Go to the Dev branch and edit the .github/workflows/cicd.yaml file.
2. Add Trivy File System Scan Stage:
    - Copy the existing build stage configuration.
    - Rename the new stage to Trivy FS Scan.
    - Configure the Trivy scan command.

## Here is the updated configuration:
```bash
name: CICD

on:
  push:
    branches:
      - Dev
  pull_request:
    branches:
      - Dev

jobs:
  build:
    runs-on: self-hosted

    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: "17"
          distribution: temurin
          cache: maven

      - name: Build with Maven
        run: mvn package --file pom.xml

      - name: Trivy FS Scan
        run: trivy fs --format table -o fs.html .

```

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

