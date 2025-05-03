# DevOps Automation for Apache HTTPD Deployment using AWS CodePipeline and CodeDeploy

This project automates the deployment of a simple Apache HTTPD web server using AWS DevOps services. It uses CodePipeline, CodeBuild, and CodeDeploy to provision and deploy code on an EC2 instance.

## 📝 Table of Contents

1. [Fork or Clone this Repository](#1-fork-or-clone-this-repository)
2. [Prepare the EC2 Target Server](#2-prepare-the-ec2-target-server)
3. [Install CodeDeploy Agent on EC2](#3-install-codedeploy-agent-on-ec2)
4. [IAM Role Setup](#4-iam-role-setup)
5. [Create CodeDeploy Application & Deployment Group](#5-create-codedeploy-application--deployment-group)
6. [Create CodePipeline](#7-create-codepipeline)
7. [Project Structure](#8-project-structure)

---

## 1. Fork or Clone this Repository

```bash
# Clone this repo
git clone https://github.com/your-username/your-repo.git
cd your-repo

# OR Fork it from GitHub
Click on the top-right 'Fork' button on GitHub and choose your account.

## 2. Prepare the EC2 Target Server
Launch an Amazon Linux 2 EC2 instance.

Make sure it is in a public subnet with internet access.

Install Apache HTTPD and start the service:

sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd

3. Install CodeDeploy Agent on EC2

sudo yum update -y
sudo yum install ruby -y
sudo yum install wget -y
cd /home/ec2-user
wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto

# Start and enable the service
sudo systemctl start codedeploy-agent
sudo systemctl enable codedeploy-agent

# Check the status
sudo systemctl status codedeploy-agent

4. IAM Role Setup
 EC2 Instance Role (Attach to EC2 Target)
Name: CodeDeployEC2Role
Policy: Attach the AWS managed policy: AmazonEC2RoleforAWSCodeDeploy
Trust relationship should allow CodeDeploy to assume the role:

{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Service": "ec2.amazonaws.com"
    },
    "Action": "sts:AssumeRole"
  }]
}

 CodeBuild Role
Name: CodeBuildServiceRole
Policies:
AmazonEC2ReadOnlyAccess
AmazonS3FullAccess
AWSCodeBuildDeveloperAccess
CodeDeployFullAccess

Trust relationship:

{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": {
      "Service": "codebuild.amazonaws.com"
    },
    "Action": "sts:AssumeRole"
  }]
}

5. Create CodeDeploy Application & Deployment Group
Application
Name: HTTPDApp

Compute Platform: EC2/On-Premises

Deployment Group
Name: HTTPDApp-DeploymentGroup

Deployment type: In-place

Environment Configuration: EC2 Instances

Key Configuration:

Select EC2 instance by Tag (e.g., Name=HTTPDTarget)

Choose service role with AWSCodeDeployRole permissions

Enable load balancer if needed (optional)

6. Create CodePipeline

