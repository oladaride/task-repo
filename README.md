**Deploying Nginx on AWS EC2 with GitHub Actions and Docker**

Using Terraform, launched a T3 Micro instance with an Amazon linux image


```hcl
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
```
Ran `Terraform Apply` to execute the EC2 instance

I SSHed into the EC2 
`ssh -i ~/.ssh/fresh-key.pem ec2-user@51.21.181.24`

}
