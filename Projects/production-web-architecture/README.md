# Production-Style AWS Web Architecture

## Project Overview

This project demonstrates the design and deployment of a production-style web application architecture on AWS.

The architecture uses private EC2 instances, an Application Load Balancer, Auto Scaling, Amazon RDS, AWS Secrets Manager, Systems Manager Session Manager, CloudWatch, IAM, and VPC endpoints.

The goal of the project was to build a secure, highly available, scalable, and self-healing architecture while applying AWS networking, security, monitoring, database, and troubleshooting concepts.

## Architecture

Internet User
↓
Application Load Balancer
↓
Auto Scaling Group
↓
Private EC2 Instances
↓
Python Web Application
↓
Amazon RDS MySQL

Supporting AWS services:

- AWS Secrets Manager for database credentials
- AWS Systems Manager Session Manager for secure EC2 administration
- Amazon CloudWatch for monitoring and CPU metrics
- IAM roles for secure AWS service access
- VPC endpoints for private access to AWS services
- Security Groups for network traffic control
- Multi-AZ public and private subnet design

## Network Design

The architecture was deployed inside a custom VPC across two Availability Zones in the Frankfurt region.

- Public subnets were used for the internet-facing Application Load Balancer.
- Private subnets were used for the application EC2 instances.
- Amazon RDS was deployed inside private subnets and was not publicly accessible.
- Security Groups controlled communication between the ALB, EC2 application servers, and RDS database.
- VPC endpoints allowed private EC2 instances to communicate with AWS services without requiring direct internet access.
- Systems Manager Session Manager was used instead of exposing SSH port 22 to the internet.

This design reduced the public attack surface while keeping the application accessible through the load balancer.

## Security and IAM

Security was designed around the principle of least privilege.

- EC2 instances were placed in private subnets with no direct inbound access from the internet.
- SSH port 22 was removed after Systems Manager Session Manager access was configured.
- An IAM instance role allowed EC2 to access required AWS services without storing AWS access keys on the server.
- Database credentials were stored in AWS Secrets Manager instead of hard-coded inside the application.
- Security Groups restricted communication between architecture layers.
- The ALB accepted public HTTP traffic and forwarded requests to the application instances on port 8080.
- RDS accepted database traffic only from the application layer.
- VPC endpoints provided private connectivity to required AWS services.

## Application and Database

The application layer consisted of a Python web application running on private EC2 instances.

- The application listened on port 8080.
- The application ran automatically as a systemd service.
- systemd was configured to restart the application if the process failed.
- The application retrieved database credentials securely from AWS Secrets Manager.
- The application connected to an Amazon RDS MySQL database in private subnets.
- RDS was not publicly accessible.
- Database access was restricted using Security Groups.
- The application successfully retrieved data from MySQL and displayed it through the web interface.

This demonstrated communication across the full application path:

Internet User → ALB → Private EC2 → Python Application → RDS MySQL

## High Availability and Auto Scaling

The application infrastructure was designed for high availability and automatic recovery.

- An Application Load Balancer distributed HTTP traffic to the application instances.
- The load balancer used health checks to detect unhealthy application instances.
- An Auto Scaling Group managed the EC2 application instances.
- The Auto Scaling Group was configured across two Availability Zones.
- A Launch Template defined the configuration used to create replacement EC2 instances.
- Auto Scaling automatically replaced unhealthy or terminated instances.
- Instance refresh was used to deploy an updated Launch Template version.
- Target Group health checks confirmed that replacement instances were healthy before serving traffic.

This demonstrated self-healing infrastructure: if an application instance failed, Auto Scaling could replace it while the load balancer continued routing traffic to healthy targets.

## Troubleshooting

During the deployment, I encountered several infrastructure and application issues and used AWS monitoring and troubleshooting tools to identify the causes.

### Auto Scaling Instance Launch Failure

During an Instance Refresh, the Auto Scaling Group failed to launch replacement EC2 instances.

The Auto Scaling activity logs showed that the Security Group configured in the Launch Template belonged to a different VPC than the private subnets used by the Auto Scaling Group.

To resolve the issue:

- Reviewed the Auto Scaling Group activity history.
- Identified the Security Group and subnet VPC mismatch.
- Created a new Launch Template version using the correct application Security Group.
- Updated the Auto Scaling Group to use the new Launch Template version.
- Started a new Instance Refresh.
- Verified the replacement instance became healthy in the Target Group.

This demonstrated the importance of checking VPC relationships between subnets, Security Groups, Launch Templates, and Auto Scaling Groups.

### Private EC2 Internet Access and Package Installation

The application EC2 instances were deployed in private subnets without a NAT Gateway.

While configuring the Python application, installing the PyMySQL package with pip failed because the private EC2 instance could not reach the public Python package repository.

Investigation showed that the existing S3 VPC Gateway Endpoint allowed access to Amazon S3, but did not provide general internet connectivity.

Instead of creating a permanent NAT Gateway, I redesigned the application to avoid the external package dependency.

The application used:

- Python standard library for the web server.
- AWS CLI to retrieve credentials from AWS Secrets Manager.
- MySQL command-line client to communicate with Amazon RDS.

This allowed the application to operate from the private subnet without requiring general outbound internet access.
This demonstrated the difference between VPC endpoint connectivity and general internet access through a NAT Gateway.

## Monitoring and CloudWatch

Amazon CloudWatch was used to monitor the application infrastructure.

- The CloudWatch Agent was installed and configured on the private EC2 instance.
- An IAM instance role provided the permissions required to publish monitoring data.
- VPC endpoints allowed the private instance to communicate with CloudWatch without requiring a NAT Gateway.
- CPU and system metrics were published to the `CWAgent` namespace.
- CloudWatch metrics were used to observe the health and performance of the EC2 application layer.

This demonstrated how monitoring can be implemented for private workloads without exposing the instances to the public internet.

## Skills Demonstrated

This project demonstrates practical experience with:

- AWS VPC architecture and subnet design
- Amazon EC2 and private instance deployment
- Application Load Balancers and Target Groups
- Auto Scaling Groups and Launch Templates
- Amazon RDS MySQL
- AWS Secrets Manager
- AWS Systems Manager Session Manager
- Amazon CloudWatch monitoring
- IAM roles and least-privilege access
- VPC endpoints and private AWS connectivity
- Security Groups and network traffic control
- Linux system administration and systemd
- Python web application deployment
- AWS infrastructure troubleshooting
- Git and GitHub project documentation

## Conclusion

This project demonstrates the design, deployment, security, monitoring, and troubleshooting of a production-style AWS web architecture.

It provided hands-on experience connecting multiple AWS services into a complete architecture rather than working with each service independently.

The project also demonstrated practical troubleshooting skills by identifying and resolving networking, security group, application dependency, and Auto Scaling issues.

The architecture was designed with security, high availability, scalability, monitoring, and cost awareness in mind.s