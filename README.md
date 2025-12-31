**Deploying Nginx on AWS EC2 with GitHub Actions and Docker**

This repository demonstrates automated deployment of an Nginx web server on AWS EC2 using Terraform, Docker, and GitHub Actions. All steps are documented below, and screenshots are included to verify successful deployment.


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



<img width="1366" height="768" alt="Screenshot (1726)" src="https://github.com/user-attachments/assets/6bdf07b7-dad6-4b76-b2a0-b6f6849ca789" />
<img width="1366" height="768" alt="Screenshot (1725)" src="https://github.com/user-attachments/assets/cf2e9de7-123f-4031-951d-51340efe7e35" />
<img width="1366" height="699" alt="Screenshot (1724)" src="https://github.com/user-attachments/assets/81e37150-86f5-49a6-8fc2-8a76bf6ee3c6" />
<img width="1366" height="768" alt="Screenshot (1715)" src="https://github.com/user-attachments/assets/22563c02-1864-4297-b698-9ec3c6f827c8" />
<img width="1366" height<img width="1366" height="768" alt="Screenshot (1722)" src="https://github.com/user-attachments/assets/7baae95f-ca07-46e1-beb1-e7031b8c62b8" />
="768" alt="Screenshot (1710)<img width="1366" height="699" alt="Screenshot (1724)" src="https://github.com/user-attachments/assets/0a00bfd2-ad88-49f4-97ff-5ae095d73c18" />
" src="https://github.com/user-attachments/assets/0b6bd339-9c6d-4ebc-bc6f-8191bee467c1" />
<img width="1366" height="768" alt="Screenshot (1716)" src="https://github.com/user-attachments/assets/8319264b-c3a3-4b0c-a68e-d2a28281c19c" />
<img width="1366" height="768" alt="Screenshot (1718)" src="https://github.com/user-attachments/assets/40bffb13-e367-4a1d-87d2-f0042925d654" />
<img width="1366" height="768" alt="Screenshot (1723)" src="https://github.com/user-attachments/assets/b8554743-7052-45b8-a451-6301e7500fd5" />
