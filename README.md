# E-Commerce Website 

![arch](https://github.com/user-attachments/assets/058a7865-787b-4a16-881d-6a4c69db2d35)


## Architecture | E-Commerce Website Using 10 Microservices on EKS Cluster

![archi](https://github.com/user-attachments/assets/5edf0d37-4e6d-44e7-866b-7fbb24d2adf7)

### Why Microservices?

Microservices is an architectural style where an application is built as a collection of small, independent services. Each service focuses on a specific business function and operates on its own. These services communicate with each other through well-defined APIs, making the system more modular and easier to manage.

#### Project Overview

- **Project Name**: 11 Microservice CI/CD Pipeline
- **Deployment Platform**: AWS EKS (Elastic Kubernetes Service)
- **Application Type**: E-Commerce Website

#### Microservices Implemented

1. **Ad Sense Service**: Manages advertisements and promotions displayed on the website.
2. **Cart Service**: Handles user shopping carts, including adding, removing, and updating items.
3. **Checkout Service**: Manages the checkout process, including order summary and confirmation.
4. **Currency Service**: Provides currency conversion and pricing in different currencies.
5. **Email Service**: Sends emails to users for order confirmations, promotions, and notifications.
6. **Frontend Service**: Manages the user interface and user experience of the website.
7. **External Frontend (for load balancing)**: Distributes incoming traffic across multiple frontend instances to ensure high availability and performance.
8. **Payment Service**: Processes payments and handles transactions securely.
9. **Product Catalogue Service**: Manages product listings, details, and inventory.
10. **Recommendation Service**: Suggests products to users based on their browsing and purchase history.

These microservices can be deployed on an EKS cluster, which provides a managed Kubernetes environment to run containerized applications. Each microservice runs in its own container, and Kubernetes manages the deployment, scaling, and operation of these containers.

#### Reasons for Choosing Microservices

1. **Scalability**: Microservices allow individual services to scale independently based on their specific demand. For instance, the Cart Service can be scaled during high traffic periods without affecting other services.
2. **Resilience**: In a microservices architecture, the failure of one service does not necessarily impact the entire system. Each service can be designed to handle failures gracefully, thereby improving the overall resilience of the application.
3. **Development Speed**: Development teams can work on different services simultaneously without waiting for other teams. This parallel development accelerates the overall development process and allows for quicker releases.
4. **Technology Diversity**: Each microservice can be developed using the most suitable technology stack for its specific needs. For example, the Product Catalogue Service can use a NoSQL database for better performance, while the Checkout Service might use a relational database for transaction consistency.
5. **Isolation and Maintenance**: Microservices encapsulate their own logic, making it easier to understand, develop, test, and maintain. Changes in one service do not directly affect others, reducing the risk and complexity of updates.

#### Project Components and Pipeline

- **CI/CD Pipeline Using Jenkins**:
  - **Jenkinsfiles**: Each microservice has its own Jenkinsfile defining the steps for building, testing, and deploying the service.
  - **Docker**: Microservices are containerized using Docker, allowing for consistent environments from development to production.
  - **AWS EKS**: The microservices are deployed on AWS EKS, a managed Kubernetes service, which orchestrates and manages the lifecycle of the containers.



### Hands-On Implementation Steps 

**Phase 1: Infrastructure Setup**

1. **Create a User in AWS IAM**: Create a new user with any name.
2. **Attach Policies**: Attach the following policies to the newly created user:
   - AmazonEC2FullAccess
   - AmazonEKS_CNI_Policy
   - AmazonEKSClusterPolicy
   - AmazonEKSWorkerNodePolicy
   - AWSCloudFormationFullAccess
   - IAMFullAccess
3. **Create an Inline Policy**: Create an inline policy with the following content and attach it to your user:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "VisualEditor0",
         "Effect": "Allow",
         "Action": "eks:*",
         "Resource": "*"
       }
     ]
   }
   ```
4. **Generate Secret Access Key**: Once the IAM user is created, generate its Secret Access Key and download the `credentials.csv` file.

**Launch Virtual Machine Using AWS EC2**

1. **EC2 Instance Requirements and Setup**:
   - **Instance Type**: `t2.large`
     - vCPUs: 2
     - Memory: 8 GB
     - Network Performance: Moderate
   - **Amazon Machine Image (AMI)**: Ubuntu Server 20.04 LTS (Focal Fossa)
   - **Security Groups**: Security groups act as a virtual firewall for your instance to control inbound and outbound traffic.
```
- Port 22 (SSH): Allows SSH access to the instance.
- Port 80 (HTTP): Allows web traffic to the instance.
- Port 465 (SMTP): Used for secure email transmission.
- Port 3000: Commonly used for development servers (e.g., Node.js).
- Port 8080: Often used for web servers and development.
- Port 8081: Another common port for web servers.
- Port 9090: Typically used for web-based management interfaces.
- Port 9000: Often used for custom applications.
- Port 32630: Specific to your application needs.
- Port 6443: Kubernetes API server.
- Port 9115: Prometheus metrics.

- Outbound Rules: Allow all traffic to ensure the instance can communicate externally.
```
2. **SSH into the Server**: After launching your virtual machine, Open your terminal and use the following command to SSH into your instance:

```sh
ssh -i /path/to/your-key-pair.pem ubuntu@your-ec2-public-dns
```

Replace `/path/to/your-key-pair.pem` with the path to your key pair file and `your-ec2-public-dns` with the public DNS of your EC2 instance.

**Install AWS CLI and KUBECTL on VM Server**

1. **AWS CLI**:
   ```sh
   curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
   sudo apt install unzip
   unzip awscliv2.zip
   sudo ./aws/install
   ```

2. **KUBECTL**:
   ```sh
   curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.31.0/2021-01-05/bin/linux/amd64/kubectl
   chmod +x ./kubectl
   sudo mv ./kubectl /usr/local/bin
   kubectl version --short --client
   ```


3. **Save All Scripts**: Save all the scripts in a file, for example, `ctl.sh`, and make it executable using:
   ```sh
   chmod +x ctl.sh
   ```

4. **Run the Script**:
   ```sh
   ./ctl.sh
   ```

#### AWS Configure Steps

To configure AWS CLI, use the following command:
```sh
aws configure
```
This command will prompt you to enter your AWS Access Key ID, Secret Access Key, region, and output format. This setup allows the AWS CLI to interact with your AWS account.

### Installing Java, Jenkins, and Docker on Ubuntu

Execute these commands on the Jenkins server:

1. **Install OpenJDK 17 JRE Headless**:
   ```sh
   sudo apt install openjdk-17-jre-headless -y
   ```

2. **Download Jenkins GPG Key**:
   ```sh
   sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
   ```

3. **Add Jenkins Repository to Package Manager Sources**:
   ```sh
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   ```

4. **Update Package Manager Repositories**:
   ```sh
   sudo apt-get update
   ```

5. **Install Jenkins**:
   ```sh
   sudo apt-get install jenkins -y
   ```

Save this script in a file, for example, `install_jenkins.sh`, and make it executable using:
```sh
chmod +x install_jenkins.sh
```

Then, you can run the script using:
```sh
./install_jenkins.sh
```

This script will automate the installation process of OpenJDK 17 JRE Headless and Jenkins.

#### Install Docker

To install Docker, follow these steps:

1. **Update the Package List**:
   ```sh
   sudo apt-get update
   ```

2. **Install Required Packages**:
   ```sh
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common -y
   ```

3. **Add Docker’s Official GPG Key**:
   ```sh
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. **Add Docker Repository**:
   ```sh
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. **Update the Package List Again**:
   ```sh
   sudo apt-get update
   ```

6. **Install Docker**:
   ```sh
   sudo apt-get install docker-ce -y
   ```

7. **Verify Docker Installation**:
   ```sh
   sudo systemctl status docker
   ```

### Creating an EKS Cluster Using Terraform:

To set up an EKS cluster, follow these steps:

✅ Install terraform:

✅ Clone the git repository for the EKS cluster code:

✅ Provision Infrastructure using terraform go into the folder:


# Terraform initialization command

terraform init

# Terrafrom plan for planning resources

terraform plan


# Terraform apply command to apply changes to AWS

terraform apply --auto-approve


# ✅ Connecting to the cluster: aws eks --region ap-south-1 update-kubeconfig --name cluster-name

**Note**: Make sure to replace the cluster name, region, zones, and SSH public key with your specific details.



###################################################################################################################


### Phase 2: Multi-branch Pipeline Setup

#### Step 1: Install Jenkins Plugins

To get started, you need to install the required Jenkins plugins. Follow these steps:

1. **Access Jenkins Dashboard**:
   - Open a web browser and navigate to your Jenkins instance (e.g., `http://your-instance-public-dns:8080`).
   - Log in with your Jenkins credentials.

2. **Install Plugins**:
   - Go to **Manage Jenkins** > **Manage Plugins**.
   - Click on the **Available** tab.
   - Search for and install the following plugins:
     - **Docker**: Enables Jenkins to use Docker containers.
     - **Docker Pipeline**: Allows Jenkins to use Docker containers in pipeline jobs.
     - **Kubernetes**: Provides support for Kubernetes in Jenkins.
     - **Kubernetes CLI**: Allows Jenkins to interact with Kubernetes clusters.
     - **Multibranch Scan Webhook Trigger**: Adds webhook trigger functionality for multibranch projects.

3. **Configure Docker Tool**:
   - After installing the Docker plugin, configure the Docker tool in Jenkins:
     - Go to **Manage Jenkins** > **Global Tool Configuration**.
     - Under **Docker**, set the tool name to `docker` (same as in the Jenkinsfile) and the version to `latest`.

4. **Add Docker Hub Credentials**:
   - Go to **Manage Jenkins** > **Manage Credentials** > **(global)** > **Add Credentials**.
   - Choose **Username with password** as the kind.
   - Set the ID to `docker-cred`.
   - Enter your Docker Hub username and password.
   - Click **OK**.

5. **Add GitHub Credentials**:
   - Go to **Manage Jenkins** > **Manage Credentials** > **(global)** > **Add Credentials**.
   - Choose **Secret text** as the kind.
   - Set the ID to `git-cred`.
   - Enter your GitHub Personal Access Token as the secret.
   - Click **OK**.

#### Step 2: Create Credentials

You need to create credentials for Docker and GitHub access.

1. **Create Docker Credentials**:
   - Go to **Manage Jenkins** > **Manage Credentials** > **(global)** > **Add Credentials**.
   - Choose **Username with password** as the kind.
   - Set the ID to `docker-cred`.
   - Enter your Docker Hub username and password.
   - Click **OK**.

2. **Create GitHub Credentials**:
   - Go to **Manage Jenkins** > **Manage Credentials** > **(global)** > **Add Credentials**.
   - Choose **Secret text** as the kind.
   - Set the ID to `git-cred`.
   - Enter your GitHub Personal Access Token as the secret.
   - Click **OK**.

#### Step 3: Configure Multibranch Pipeline

1. **Create a New Multibranch Pipeline**:
   - Go to **New Item**.
   - Enter a name for your project (e.g., `microservice-pipeline`).
   - Select **Multibranch Pipeline** and click **OK**.

2. **Configure Branch Sources**:
   - Under **Branch Sources**, click **Add source**.
   - Choose **Git**.
   - Enter the repository URL: `https://github.com/RajPractiseRepo/terraform-eks-microservices-multibranch-cicd-project.git`.
   - Select the `git-cred` credentials.

3. **Configure Build Configuration**:
   - Under **Build Configuration**, ensure **by Jenkinsfile** is selected.
   - Leave the **Script Path** as the default (`Jenkinsfile`).

4. **Configure Webhook Trigger**:
   - Under **Scan Repository Triggers**, click **Add**.
   - Select **Multibranch Scan Webhook Trigger**.
   - Enter a token name (e.g., `swapnil`).
   - Note the webhook URL: `http://your-jenkins-instance:8080/multibranch-webhook-trigger/invoke?token=swapnil`.
     - Replace `your-jenkins-instance` with the public DNS or IP address of your Jenkins instance.
     - Replace `swapnil` with the token name you provided.

#### Step 4: Set Up GitHub Webhook

1. **Go to GitHub Repository Settings**:
   - Navigate to your repository on GitHub.
   - Go to **Settings** > **Webhooks**.

2. **Add Webhook**:
   - Click **Add webhook**.
   - **Payload URL**: Enter the webhook URL you noted earlier (e.g., `http://your-jenkins-instance:8080/multibranch-webhook-trigger/invoke?token=swapnil`).
   - **Content type**: Select `application/json`.
   - **Secret**: Leave it blank.
   - Select **Let me select individual events** and choose **Push events**.
   - Click **Add webhook**.

After completing these steps, your Jenkins multi-branch pipeline will start building automatically.

### Phase 3: Continuous Deployment

Run these commands on the server to create a Service Account, Role, and Role Binding, and to generate a token for the Service Account. This setup will allow you to deploy your application on the main branch.

- Create a namespace for isolation:
  ```sh
  kubectl create namespace webapps
  ```
#### Service Account
  
A **Service Account** in Kubernetes is used to provide an identity for processes that run in a Pod. This identity allows the processes to interact with the Kubernetes API server. Service accounts are typically used for applications running within the cluster that need to communicate with the Kubernetes API.

1. **Create a Service Account**:
   - Create a file named `svc.yml` with the following content:
     ```yaml
     apiVersion: v1
     kind: ServiceAccount
     metadata:
       name: jenkins
       namespace: webapps
     ```
   - Apply the configuration:
     ```sh
     kubectl apply -f svc.yml
     ```

2. **Create a Role**:

#### Role

A **Role** in Kubernetes defines a set of permissions within a specific namespace. It specifies what actions can be performed on which resources. Roles are used to grant access to resources like Pods, Services, and ConfigMaps within a namespace.

   - Create a file named `role.yml` with the following content:
     ```yaml
     apiVersion: rbac.authorization.k8s.io/v1
     kind: Role
     metadata:
       name: app-role
       namespace: webapps
     rules:
     - apiGroups: ["", "apps", "autoscaling", "batch", "extensions", "policy", "rbac.authorization.k8s.io"]
       resources: ["pods", "componentstatuses", "configmaps", "daemonsets", "deployments", "events", "endpoints", "horizontalpodautoscalers", "ingress", "jobs", "limitranges", "namespaces", "nodes", "persistentvolumes", "persistentvolumeclaims", "resourcequotas", "replicasets", "replicationcontrollers", "serviceaccounts", "services"]
       verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
     ```
   - Apply the configuration:
     ```sh
     kubectl apply -f role.yml
     ```

3. **Bind the Role to the Service Account**:

#### Role Binding

A **Role Binding** grants the permissions defined in a Role to a user or a set of users, including service accounts. It binds the Role to the Service Account, allowing the Service Account to perform the actions specified in the Role. 

   - Create a file named `bind.yml` with the following content:
     ```yaml
     apiVersion: rbac.authorization.k8s.io/v1
     kind: RoleBinding
     metadata:
       name: app-rolebinding
       namespace: webapps
     roleRef:
       apiGroup: rbac.authorization.k8s.io
       kind: Role
       name: app-role
     subjects:
     - kind: ServiceAccount
       name: jenkins
       namespace: webapps
     ```
   - Apply the configuration:
     ```sh
     kubectl apply -f bind.yml
     ```

### Creating a Secret Token for the Service Account

To create a secret token for the Service Account, follow these steps:

1. **Create a Secret**:
   - Create a file named `secret.yml` with the following content:
     ```yaml
     apiVersion: v1
     kind: Secret
     metadata:
       name: jenkins-token
       namespace: webapps
     type: kubernetes.io/service-account-token
     annotations:
       kubernetes.io/service-account.name: "jenkins"
     ```
   - Apply the configuration:
     ```sh
     kubectl apply -f secret.yml
     ```

2. **Retrieve the Token**:
   - Get the name of the secret created for the service account:
     ```sh
     kubectl get secrets -n webapps
     ```
   - Describe the secret to retrieve the token:
     ```sh
     kubectl describe secret <secret-name> -n webapps
     ```

Here, You will see the Service Account Token. Save the token for later use and This token can be used by Jenkins to authenticate with the Kubernetes API server.


### Configure Jenkins to use the secret token for authenticating with your Kubernetes cluster

#### Jenkins Configuration Steps

1. **Install the Kubernetes Plugin**:
   - Go to **Manage Jenkins** > **Manage Plugins**.
   - In the **Available** tab, search for the **Kubernetes** plugin.
   - Install the plugin and restart Jenkins if required.

2. **Add the Token to Jenkins Credentials**:
   - Go to **Manage Jenkins** > **Manage Credentials**.
   - Select the appropriate domain (e.g., Global).
   - Click **Add Credentials**.
   - Choose **Secret text** as the kind.
   - Enter the token in the **Secret** field and give it an **ID** (e.g., `k8-token`).
   - Kubernates Endpoint API - You can find it in your AWS EKS cluster. 
   - Cluster name- Provide any name. 
   - NameSpace – webapps  

3. **Configure Kubernetes in Jenkins**:
   - Create a new Dummy Jenkins pipeline job for Generating Pipeline Script.
   
   - In the pipeline script, use the `withKubeCredentials` block to configure Kubernetes credentials:
     ```groovy
     withKubeCredentials(kubectlCredentials: [[
       caCertificate: '', 
       clusterName: 'demo_cluster_eks', 
       contextName: '', 
       credentialsId: 'k8-token', 
       namespace: 'webapps', 
       serverUrl: 'https://38C1EFF4B3CDDD74AB2C211080031265.gr7.ap-south-1.eks.amazonaws.com'
     ]]) { 
       // Your pipeline code here
     }
     ```

### Explanation of Server URL

The **serverURL** is the endpoint of your Kubernetes API server. This is the address Jenkins will use to communicate with your Kubernetes cluster. You can find this URL in your AWS EKS cluster details. Here’s how to get it:

1. **AWS Management Console**:
   - Go to the **EKS** section in the AWS Management Console.
   - Select your cluster.
   - In the **Cluster** details, you will find the **API server endpoint**. This is the URL you need.

2. **AWS CLI**:
   - You can also retrieve the server URL using the AWS CLI:
     ```sh
     aws eks describe-cluster --name <cluster-name> --query "cluster.endpoint" --output text
     ```
This setup ensures that Jenkins uses the specified token to authenticate with the Kubernetes API server securely. 

### Create A Jenkinfile on Main Branch

Here’s the Jenkinsfile with the provided script for deploying to Kubernetes and verifying the deployment. This file should be placed in the main branch, which is your deployment branch

```
pipeline { 
    agent any 
    stages { 
        stage('Deploy To Kubernetes') { 
            steps { 
                withKubeCredentials(kubectlCredentials: [[
                    caCertificate: '', 
                    clusterName: 'demo_cluster_eks', 
                    contextName: '', 
                    credentialsId: 'k8-token', 
                    namespace: 'webapps', 
                    serverUrl: 'https://38C1EFF4B3CDDD74AB2C211080031265.gr7.ap-south-1.eks.amazonaws.com'
                ]]) { 
                    sh "kubectl apply -f deployment-service.yml" 
                } 
            } 
        } 
        stage('Verify Deployment') { 
            steps { 
                withKubeCredentials(kubectlCredentials: [[
                    caCertificate: '', 
                    clusterName: 'demo_cluster_eks', 
                    contextName: '', 
                    credentialsId: 'k8-token', 
                    namespace: 'webapps', 
                    serverUrl: 'https://38C1EFF4B3CDDD74AB2C211080031265.gr7.ap-south-1.eks.amazonaws.com'
                ]]) { 
                    sh "kubectl get svc -n webapps" 
                } 
            } 
        } 
    } 
}

```

Once you commit this file to the main branch, your pipeline will automatically start the build and deployment process.

### Results:

**Pipeline**

![jenkins-CI-pipeline](https://github.com/user-attachments/assets/1429d632-6878-4738-bfc7-30ca92edf2a2)

**Application**

![arch](https://github.com/user-attachments/assets/70f04e9e-8ce6-4d80-ac3b-b50d46b01605)

**Console Output**

![output](https://github.com/user-attachments/assets/a93ffed9-9625-4e04-a609-ded7914003c9)

**Main Branch Pipeline**

![jenkins-CD-pipeline](https://github.com/user-attachments/assets/23cf60f3-d5bf-4d39-b10d-0c38f21a50ea)

**EKS Cluster**

![eks-cluster](https://github.com/user-attachments/assets/50c4f82b-891a-4ff0-b3dd-ce923f32d2d4)



# Configuration of DNS Server through ROUTE 53:



# Setup the record for the domain name and route traffic to load balancer:


![route53-1](https://github.com/user-attachments/assets/6fcbad81-00af-4367-9837-ec3af47a3980)





# Status got synced:


![route53-2](https://github.com/user-attachments/assets/46e2eb30-ab23-41f6-9f40-368d3097f271)






#  Record got created:


![route53-3](https://github.com/user-attachments/assets/6fffb0e4-c6b5-426a-bcce-2ff3c5425c01)





# Lets test the app with domain name configured:



![dns-app](https://github.com/user-attachments/assets/05c8ec3c-753f-4a5f-908c-ed25ed118aee)







### Delete EKS Cluster and whole setup:

To delete your EKS cluster and avoid any unnecessary billing, you can use the following command:

```sh
terraform destroy --auto-approve
```


![resources-destroyed](https://github.com/user-attachments/assets/feb5e657-a68c-4c8d-a791-9b718cbe65c4)





### Project Impact

The implementation of the 10 Microservice CI/CD Pipeline project provides several significant benefits:

1. **Improved Efficiency**: Automation of the build and deployment process reduces the time and effort required for manual deployments, leading to faster delivery cycles.
2. **Enhanced Reliability**: Automated testing and deployment help in identifying and resolving issues early, improving the overall reliability and quality of the application.
3. **Scalability**: The microservices architecture and Kubernetes orchestration enable the application to handle varying loads efficiently, ensuring consistent performance and availability.
4. **Maintainability**: Independent development and deployment of services simplify maintenance and updates, reducing the complexity and risk of making changes.



### Conclusion

The successful deployment of the e-commerce website using a microservices architecture and an automated CI/CD pipeline on AWS EKS exemplifies the advantages of modern software development practices. This project not only achieves the goals of scalability, reliability, and efficiency but also sets a strong foundation for future enhancements and growth. The methodologies and technologies employed in this project can serve as a blueprint for similar projects aiming to leverage microservices and CI/CD pipelines for continuous innovation and delivery.





