```
## Setting up the project environment
#### Create a new virtual environment
python -m venv venv

#### Activate the new virtual environment
source venv/bin/activate  # On Windows use `venv\Scripts\activate`

#### Install the required packages
pip install -r requirements.txt

## AWS deployment
### 1. Login to the AWS account

### 2. Create an IAM user for deployment
1. Build a Docker image of the source code
2. Push the Docker image to ECR to save it on AWS
3. Launch an EC2 virtual machine
4. Pull the Docker image from ECR to EC2
5. Launch the Docker image on EC2

Policies required for an IAM user:
1. AmazonEC2ContainerRegistryFullAccess
2. AmazonEC2FullAccess

### 3. Create ECR repo to store/save docker image
URI for ECR repository to save Docker image:
405074955098.dkr.ecr.ap-south-1.amazonaws.com/clinical_summary

### 4. Launch an EC2 Ubuntu machine instance

### 5. Start EC2 machine and install Docker
```
# optional
sudo apt-get update -y

sudo apt-get upgrade

# required
curl -fsSL https://get.docker.com -o get-docker.sh

sudo sh get-docker.sh

sudo usermod -aG docker ubuntu

newgrp docker
```

### 6. Configure the EC2 machine as a self-hosted runner
On Github repository settings page > actions > runner > new self hosted runner > choose OS as Ubuntu > then run commands listed on the runner webpage line by line in the EC2 machine console to connect Github with EC2 machine. Enter name of runner as 'self-hosted'.

If restarted the machine, just change directory to 'actions-runner' and run the GitHub Actions self-hosted runner using the command './run.sh'. To stop the runner execution, press Ctrl+C. Whenever you push some changes in your GitHub repo, the actions runner will deploy the changes in AWS machine.

### 7. Save Github secrets
AWS_ACCESS_KEY_ID= (when IAM is created. It is saved on GitHub)

AWS_SECRET_ACCESS_KEY=

AWS_REGION = ap-south-1

AWS_ECR_LOGIN_URI = 405074955098.dkr.ecr.ap-south-1.amazonaws.com

ECR_REPOSITORY_NAME = clinical_summary

#### website hosting on AWS
1. Configure your security group (ssh port 22 for admin only) and (http port 80 for normal traffic i.e. 0.0.0.0/0).
2. Check Docker status using 'docker ps'. Copy the instance name and type 'docker logs clinical_summary'. This will show if uvicorn is running on http://0.0.0.0:8080.
3. Some lines like INFO: 180.244.214.131:60779 - "GET / HTTP/1.1" 200 OK show that your server is responding with 200 OK to requests at /. This means that requests to your server are reaching it successfully, both locally (from 172.17.0.1, which is Docker’s internal IP) and externally.
4. Open a web browser and access your EC2 public IPv4 address or DNS.
