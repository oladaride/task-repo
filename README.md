**Deploying Nginx on AWS EC2 with GitHub Actions and Docker**

1. Using Terraform, launched a T3 Micro instance with an Amazon Linux image


```hcl
# Make changes to variable.tf to select security group, instance type, and image.
variable "security_group_id" {
  description = "The ID of the existing security group"
  type        = string
  default     = "sg-057f0c1db37208ad3"
}


variable "instance_type" {
  description = "EC2 instance type to use"
  type        = string
  default     = "t3.micro"
}
variable "ami_id" {
  description = "AMI ID to use for the instance"
  type        = string
  default     = "ami-0b46816ffa1234887"
}
```

2. Run `Terraform plan` to preview resources and then  `Terraform Apply` to execute the EC2 instance
   
3. SSH into EC2 server from the local machine with key
`ssh -i ~/.ssh/fresh-key.pem ec2-user@51.21.181.24`

4. Install git in the server
`sudo yum update -y`
`sudo yum install git -y`

5. Install Docker in the server for containerization
`sudo yum install docker -y`

6. Run `docker --version` to verify that Docker is installed on the server

7. Go to GitHub, create a repo for your project and clone the repo
`git clone https://github.com/oladaride/task-repo.git`

8. Create a .github\workflows folder in the root of your repo and create a YAML file to define your CI/CD pipeline

9. For GitHub Action, go to Github, in the repo, select 'Settings', select Secret and Variables', select "Actions", select 'Repository Secret' and "New repository secret."
EC2_HOST = EC2 IP ADDRESS
EC2_SSH_KEY = YOUR KEY
EC2_USER = ec2-user

10. In your server, open the YAML file with `vim exec-docker.yml` and write your workflow structure:
    
```
name: Run Nginx using Docker on EC2 by GitHub Actions
on:
  push:
    branches: ["main"]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Connect to EC2 and run Docker commands
        uses: appleboy/ssh-action@v0.1.10
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ${{ secrets.EC2_USER }}
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            echo "Connected to EC2"

            sudo systemctl start docker
            sudo systemctl enable docker

            sudo docker stop nginx-app 2>/dev/null || true
            sudo docker rm nginx-app 2>/dev/null || true

            sudo docker pull nginx:latest
            sudo docker run -d \
              --name nginx-app \
              -p 80:80 \
              -p 443:443 \
              --restart unless-stopped \
              nginx:latest

            echo "Deployment complete! Running containers:"
            sudo docker ps
```


<img width="1366" height="768" alt="Screenshot (1723)" src="https://github.com/user-attachments/assets/eba0fc89-7b1c-4206-8290-edd128db113e" />
<img width="1366" height="768" alt="Screenshot (1722)" src="https://github.com/user-attachments/assets/e235f3e2-1ef4-4a0d-966b-f9bb6d6ebd94" />
<img width="1366" height="768" alt="Screenshot (1721)" src="https://github.com/user-attachments/assets/f81dcd9a-6661-49a0-9479-6d04aaf0cbe5" />
<img width="1366" height="768" alt="Screenshot (1720)" src="https://github.com/user-attachments/assets/7dd1d433-068c-4647-ba1b-769c6381a974" />
<img width="1366" height="768" alt="Screenshot (1719)" src="https://github.com/user-attachments/assets/ea1faace-821e-43b9-8ba5-8951d1907b4e" />
<img width="1366" height="768" alt="Screenshot (1718)" src="https://github.com/user-attachments/assets/f8391019-f486-4fdf-8b85-bda322d2634a" />
<img width="1366" height="768" alt="Screenshot (1716)" src="https://github.com/user-attachments/assets/c0b318a2-5b80-4890-b6e3-3eed7e9d24ed" />
<img width="1366" height="768" alt="Screenshot (1715)" src="https://github.com/user-attachments/assets/aec5cc0c-0447-4b30-8802-2fcb24fadb5f" />
<img width="1366" height="768" alt="Screenshot (1710)" src="https://github.com/user-attachments/assets/266b4756-9f27-4af3-9d9c-5f08526aab4d" />
