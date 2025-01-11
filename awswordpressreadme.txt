![Alt text](/host_worldpress.pdf)

# Hosting a WordPress Website on AWS

This repository contains the resources and scripts to deploy a WordPress website on Amazon Web Services (AWS). The project utilizes AWS services to ensure high availability, scalability, and security for the WordPress application.

## Architecture Overview

The WordPress website is hosted on EC2 instances within a highly available and secure architecture, including:

- **VPC**: Virtual Private Cloud with public and private subnets across two Availability Zones (AZs) for fault tolerance.
- **Internet Gateway**: Enables communication between the VPC and the internet.
- **Security Groups**: Controls inbound and outbound traffic.
- **Public Subnets**: For NAT Gateway and Application Load Balancer, facilitating external access and load balancing.
- **Private Subnets**: Secures web servers by restricting direct internet access.
- **EC2 Instance Connect Endpoint**: Provides secure SSH access.
- **Application Load Balancer**: Distributes incoming traffic across EC2 instances.
- **Auto Scaling Group**: Adjusts the number of EC2 instances based on traffic.
- **Amazon RDS**: Managed relational database service for WordPress data.
- **Amazon EFS**: Elastic File System for scalable storage.
- **AWS Certificate Manager**: Manages SSL/TLS certificates.
- **SNS**: Simple Notification Service for Auto Scaling notifications.
- **Amazon Route 53**: For domain registration and DNS management.

## Deployment Scripts

### WordPress Installation Script

This script installs and configures WordPress on an EC2 instance, including Apache, PHP, MySQL, and Amazon EFS mounting.

```bash
# Switch to root user
sudo su

# Update software packages
sudo yum update -y

# Create the HTML directory
sudo mkdir -p /var/www/html

# Set environment variable for EFS DNS name
EFS_DNS_NAME=fs-064e9505819af10a4.efs.us-east-1.amazonaws.com

# Mount EFS to HTML directory
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html

# Install Apache web server and enable it on boot
sudo yum install -y httpd
sudo systemctl enable httpd 
sudo systemctl start httpd

# Install PHP 8 and necessary extensions for WordPress
sudo dnf install -y \
php php-cli php-cgi php-curl php-mbstring php-gd php-mysqlnd php-gettext php-json \
php-xml php-fpm php-intl php-zip php-bcmath php-ctype php-fileinfo php-openssl \
php-pdo php-tokenizer

# Install MySQL 8 community repository
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm 
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf install -y mysql-community-server

# Start and enable MySQL server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Set permissions
sudo usermod -a -G apache ec2-user
sudo chown -R ec2-user:apache /var/www
sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
sudo find /var/www -type f -exec sudo chmod 0664 {} \;
chown apache:apache -R /var/www/html 

# Download WordPress files
wget https://wordpress.org/latest.tar.gz
tar -xzf latest.tar.gz
sudo cp -r wordpress/* /var/www/html/

# Create the wp-config.php file
sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php

# Edit the wp-config.php file
sudo vi /var/www/html/wp-config.php

# Restart Apache server
sudo service httpd restart
```

### Auto Scaling Group Launch Template Script

This script is included in the launch template for the Auto Scaling Group, ensuring that new instances are configured with the required software and settings.

```bash
#!/bin/bash
# Update software packages
sudo yum update -y

# Install Apache and enable it on boot
sudo yum install -y httpd
sudo systemctl enable httpd 
sudo systemctl start httpd

# Install PHP 8 and necessary extensions for WordPress
sudo dnf install -y \
php php-cli php-cgi php-curl php-mbstring php-gd php-mysqlnd php-gettext php-json \
php-xml php-fpm php-intl php-zip php-bcmath php-ctype php-fileinfo php-openssl \
php-pdo php-tokenizer

# Install MySQL 8 community repository
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm 
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm 
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf install -y mysql-community-server

# Start and enable MySQL server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Mount EFS to HTML directory
EFS_DNS_NAME=fs-02d3268559aa2a318.efs.us-east-1.amazonaws.com
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a

# Set permissions
chown apache:apache -R /var/www/html

# Restart Apache server
sudo service httpd restart
```

## How to Use

1. **Clone the Repository**: Clone this repository to your local machine.
2. **Create AWS Resources**: Follow AWS documentation to set up VPC, subnets, Internet Gateway, and other required resources.
3. **Deploy Scripts**: Use the provided scripts to install and configure WordPress on EC2 instances.
4. **Configure Services**: Set up the Auto Scaling Group, Load Balancer, and additional services as per the architecture.
5. **Access the Website**: Access your WordPress site through the Load Balancer's DNS name.

## Contributing

Contributions are welcome! Please fork the repository and submit a pull request with your changes or improvements.

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.

---

Let me know if you'd like further customization!
