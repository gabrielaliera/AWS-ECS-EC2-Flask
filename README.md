
# AWS Compute Final Project
## Summary
The aim of this project is to build the infrastructure associated with continous deployment in a CI/CD pipeline. Using a simple Flask application, the code is packaged into Docker image, and automatic deployment is push into to AWS. AWS technologies used in this project include IAM, ECR, ECS, and Route53. Elastic Container Registery(ECR) holds the Docker image. When the code is push into AWS, the Flask app is launch into EC2 instances by pulling the image from ECR. An application load balancer is used to direct user requests to the appropriate EC2 instance, and a domain name purchased from Route53 is utilized to customize the URL. 

The steps of the project are outlined in the rest of the document with screenshots provided in the appendix.

## Identity Access & Management (IAM) User
The first thing I did was to create an IAM user with the roels necessary to run the AWS Flask app. I did not want to use my IAM user with full administrator access because in industry users will be limited to what access is necessary for them to complete their project. I create a new user name AWS-ECS-FINAL with the following permission policies : 
<ul>
  <li>AmazonEC2FullAccess</li>
  <li>AmazonEC2ContainerRegistryFullAccess</li>
  <li>AmazonECS_FullAccess</li>
  <li>EC2InstanceProfileForImageBuilderECRContainerBuilds</li>
 </ul>

## Elastic Container Registry
The next step I did was create a repository called “ecs-flask-repo”  that will hold my docker image when I push it to AWS. 

## Elastic Container Service
I set up the task definition, service, and cluster in Elastic Container Service. 

### Task
The task definition is the blueprint describing what is contained in an EC2 instance like the docker image, memory, and open ports. I created a EC2 task definition to point to the ecs-flask-repo. I use the default settings except added port mapping to the host at 0 and the container at 5000. 

### Cluster
Next, I created a EC2 Linux + Networking Cluster. The instance type is tt2.nano because the application is small and doesn’t need a lot of computing power. The EC2 AMI ID used was the default Amazon Linux 2. I created a new key pair to be able to ssh into the web server. I used default settings to create a new VPC and security group for this cluster.

### Service
To complete the cluster, I added a service using the previously created task definition and cluster. In addition, I created and added an application load balancer using the VPC from the previously created step. Within the application load balancer, I also created a target group to run on port 5000.

## Security Groups
Next, I configure the security group for the ECS EC2 instance to allow incoming traffic. I allowed http, ssh from my personal ip-address, and all traffic from the security group

## Route53
To help me build my future personal website, I registered a domain name on Route53- gabrielaliera.com. I created a new record called ecsflask that is connected to my application load balancer. Hence, when a user types in ecsflask.gabrielaliera.com they will be routed to the ECS EC2 instance that holds my flask app.

## Docker/Github
The next step was to create the necessary files to Dockersize my flask application. I created a simple flask application on app.py. At a later stage, I can update this file to add more pages. I created a Dockerfile that installs python, adds a user, sets the variables for docker commands, and starts the web server. From the AWS task definition, I copied over the json file to Github. I also created a requirements.txt file. In addition I set environmental variables in github that will hold the following variables: AWS_ACESS_KEY_ID, AWS_DEFAULT_REGION, AWS_SECRET_ACCESS_KEY, FLASK_SECRET_KEY. This will hide the values so I can call them on the aws.yml file need for the CI/CD pipeline.

## Create CI/CD pipeline in Github
The last step was to create a workflow that will deploy to AWS. I used a custom template from Github to create an aws.ym file. I set different variables to the previous made repository, service, cluster, task definition, and container name. 
	
## Appendix
### 1. IAM User Creation with permission policies
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/1.jpg" width="" heigth="" alt="step 1"/>

### 2. Create ECS repository
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/2.jpg" width="" heigth="" alt="step 1"/>

### 3. Create Task definition on ECS
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/3.jpg" width="" heigth="" alt="step 1"/>

### 4. Create cluster ssh key pair
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/4.jpg" width="" heigth="" alt="step 1"/>

### 5. Create a cluster 
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/5.jpg" width="" heigth="" alt="step 1"/> 

### 6. Begin creating  a service to cluster
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/6.jpg" width="" heigth="" alt="step 1"/>  

### 7.Create application load balancer to add to cluster ( Also create target group)
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/7a.jpg" width="" heigth="" alt="step 7a"/> 
  <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/7b.jpg" width="" heigth="" alt="step 7b"/> 
### 8. Add application load balancer to cluster
  <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/8.jpg" width="" heigth="" alt="step 1"/>

Newly created service in cluster with load balancer
<img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/8b.jpg" width="" heigth="" alt="step 1"/> 

Showing active cluster
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/8c.jpg" width="" heigth="" alt="step 1"/>  

### 9. Edit security group of EC2 instance to allow http, ssh from my personal ip address, and all traffic from the security group
  <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/9.jpg" width="" heigth="" alt="step 1"/>

### 10. Register domain name on Route53 and connect load balancer
  <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/10.jpg" width="" heigth="" alt="step 1"/> 

### 11.	Create app.py, Dockerfile,aws-task-definition.json, and requirements.txt. Upload to Github
  <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/11.png" width="" heigth="" alt="step 1"/> 


### 12.	Create CI/CD pipeline in workflow with aws.yml file
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/12.png" width="" heigth="" alt="step 1"/>  

### 13.	Deploy files to AWS 
  <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/13.jpg" width="" heigth="" alt="step 1"/>

### 14. Accessing Flask app on register URL ecsflask.gabrielaliera.com
 <img src="https://github.com/gabrielaliera/ecs_flask_final/blob/main/images/14.png" width="" heigth="" alt="step 1"/>
