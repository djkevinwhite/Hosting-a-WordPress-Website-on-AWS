![Alt text](/host_worldpress.pdf)

# Host a Static Website on AWS


This repository provides all the resources and scripts required to deploy a static HTML web application on AWS. The project leverages AWS services like EC2, VPC, Route 53, Auto Scaling, Application Load Balancer, and more to ensure high availability, fault tolerance, and scalability for the website.

The main objective of this project was to host a static website in a secure, scalable, and highly available environment on AWS. The following is a detailed walkthrough of the architecture and steps involved in the deployment.

## Architecture Overview

1. **Virtual Private Cloud (VPC)**:
   - Configured a VPC with both public and private subnets across two different availability zones (AZs).
   - Ensured secure communication within the infrastructure by utilizing a network firewall mechanism (Security Groups).

2. **Internet Gateway**:
   - Deployed an Internet Gateway to allow communication between the VPC instances and the broader internet.

3. **Public Subnets**:
   - Hosted infrastructure components such as the NAT Gateway and Application Load Balancer in the public subnets.
   - Managed access between internal resources and the internet securely.

4. **Private Subnets**:
   - Web servers (EC2 instances) were positioned within the private subnets to enhance security.
   - Instances in both the private Application and Data subnets accessed the internet via the NAT Gateway.

5. **Security Groups**:
   - Configured security groups to serve as a network firewall for controlling access to EC2 instances.

6. **EC2 Instance Connect Endpoint**:
   - Implemented EC2 Instance Connect for secure and seamless access to assets within the public and private subnets.

7. **Auto Scaling Group (ASG)**:
   - Created an Auto Scaling Group that automatically manages EC2 instances, ensuring scalability, fault tolerance, and elasticity for the website.

8. **Application Load Balancer**:
   - Deployed an Application Load Balancer (ALB) to evenly distribute web traffic to EC2 instances.
   - The ALB was linked with a target group, ensuring proper routing of requests to healthy instances.

9. **Website Hosting**:
   - Hosted the static website on EC2 instances using Apache HTTP server.

10. **SSL/TLS Encryption**:
    - Secured the application’s communication using AWS Certificate Manager (ACM) for SSL/TLS encryption.

11. **Simple Notification Service (SNS)**:
    - Configured Amazon SNS to notify about activities in the Auto Scaling Group, such as scaling events.

12. **Route 53**:
    - Registered the domain name and set up DNS records in Amazon Route 53 to route traffic to the Load Balancer.

## AWS Services Used

- **Amazon EC2**: For deploying web servers (static website hosting).
- **Amazon VPC**: For creating a custom network and security configuration.
- **Amazon Route 53**: For domain registration and DNS management.
- **Amazon Certificate Manager (ACM)**: For SSL/TLS certificate management.
- **Amazon SNS**: For notifications and alerting.
- **Amazon Auto Scaling**: For automatically managing EC2 instances.
- **Elastic Load Balancing (ELB)**: For distributing web traffic.
- **Amazon S3 (optional)**: For version control and file storage (Web files can also be stored in S3 for enhanced scalability).

## Deployment Steps

1. **Launch EC2 Instances**:
   - Create EC2 instances and ensure they are within private subnets for better security.
   
2. **Install Apache HTTP Server**:
   - SSH into the EC2 instance and install the Apache HTTP server.
   - Use the provided Bash script to install and configure Apache on your EC2 instances.

3. **Clone GitHub Repository**:
   - Clone the GitHub repository where the static HTML website files are stored.
   - Copy all the contents of the repository to Apache’s web root directory.

4. **Configure Auto Scaling**:
   - Set up an Auto Scaling Group to automatically adjust the number of EC2 instances based on traffic patterns.

5. **Configure Application Load Balancer**:
   - Set up an Application Load Balancer (ALB) to balance incoming traffic across your EC2 instances.
   - Configure health checks for each EC2 instance to ensure traffic is only routed to healthy instances.

6. **Set Up SSL/TLS**:
   - Obtain and configure an SSL/TLS certificate using AWS Certificate Manager (ACM) for securing your website's traffic.

7. **Domain Setup**:
   - Register a domain name via Amazon Route 53 and configure DNS settings to point to the Application Load Balancer.

8. **Enable Auto Scaling Notifications**:
   - Use Amazon SNS to send notifications for any scaling activities in the Auto Scaling Group.

## Bash Script for EC2 Instance Setup

The following script is used to set up the Apache server and deploy the website on the EC2 instance:

```bash
#!/bin/bash
# Switch to the root user to gain full administrative privileges
sudo su
# Update all installed packages to their latest versions
yum update -y
# Install Apache HTTP Server
yum install -y httpd
# Change the current working directory to the Apache web root
cd /var/www/html
# Install Git
yum install git -y
# Clone the project GitHub repository to the current directory
git clone https://github.com/aosnotes77/host-a-static-website-on-aws.git
# Copy all files, including hidden ones, from the cloned repository to the Apache web root
cp -R host-a-static-website-on-aws/. /var/www/html/
# Remove the cloned repository directory to clean up unnecessary files
rm -rf host-a-static-website-on-aws
# Enable the Apache HTTP Server to start automatically at system boot
systemctl enable httpd
# Start the Apache HTTP Server to serve web content
systemctl start httpd
```

## Prerequisites

- AWS Account with sufficient permissions to create and manage EC2 instances, VPCs, Route 53 records, and other services.
- Domain name (optional but recommended) for easier access to the website.
- GitHub account to store and manage web application files (for version control).
  
## Additional Notes

- Ensure that security groups and NACLs (Network Access Control Lists) are properly configured to allow necessary traffic (HTTP, HTTPS, SSH).
- Use AWS CloudWatch or other monitoring tools to track resource utilization and instance health.
- SSL/TLS certificates should be periodically renewed if using a third-party certificate.

## Conclusion

This project demonstrates how to host a static website on AWS while ensuring scalability, security, and high availability using AWS services. By leveraging EC2, Auto Scaling, Application Load Balancer, and other AWS services, this solution provides a robust infrastructure for hosting a static website in the cloud.
