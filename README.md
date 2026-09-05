Markdown

# Cloud Engineer Journey
## Skills
- AWS
- Linux
- Git
- Networking

## Coming soon 

- Terraform
- Docker
CI/CD

<Markdown>

# EC2 Linux Web Server

## Objective
Deploy a Linux web server on AWS EC2.

## Services Used
- EC2
- Security Groups
- Key Pair
- Amazon Linux 2023

## Tasks Completed
- Created EC2 instance
- Connected using SSH
- Installed Apache
- Configured web server
- Tested HTTP access

## Challenges
ssh authentication errors, after reaching the server i encountered:permission denied (publickey,gssapi-keyex,gssapi-keyex,gssapi-with-mic)

host fingerprint warning,during the first ssh connection ssh displayed a SHA256 fingerprint and asked  "Are you sure you want to continue connecting"


## Lessons Learned
initially i thought this was an error but i learned that it is normal security feature that verifies the identity of the server before adding it to the known_host file. these challenges helped me gain practical experience with EC2 provisioning, security groups, SSH key management and Linux command-line operations on Macos 

Markdown

# AWS Systems Manager - session manager 

##vobjective 
securely connect to an ec2 instance using AWS systems manager session manager istead of SSH

## AWS services used

- Amazon ec2 
- AWS Systems manager session manager
- AWS identity and access Management
- Securiry groups

## Security Improvement

- initially, ssh port22 allowed access from my public IP 
i configured systems manager session manager and attached AmazonSSMManagedInstanceCore to the EC2 IAM role. After veriffying session manager access, i removed the inbound SSH rule.

## Verification

- Connected through session manager 
- confirmed apache was running 
- tested the website with curl
- confirmed direct ssh no longer worked
- confirmed http remained available

## Lesson learned 

admistrative access should not be publicly exposed when a managed private -access method is available


## Application-load-balancing high availability-lab

i deployed two EC2 web serers across seperate availabilty zones and registered them with an application load balancer target group. i configured the ALB security group to accept http only from the ALB. i verified that direct access to the ec2 public ip was blocked, while the applicationremained available through the ALB. i then stopped one EC2 instance and confirmed that health checks  detected the failed target and trffic continued to the remaining healthy instance

# challenges and troubleshooting 

- second EC2 instance was not initially part of the Target group

# problem 

- the load balancer could not distribute traffic across both web servers because the second EC2 instance was not registered correctly
# Investigation

- i checked the target group and reviewed the registered targets and their health status

# Solution

- i registered the second EC2 instance with the target Group on HTTP port 80 and waited for the ALB health checks.


# Auto scaling project 

## objective 
- build a highly aailable web application that automatically increases or decreases the number of EC2 instances depending on cpu utilization

## testing
-i generated a high CPU utilitation on the EC2 instance. The target tracking policy detected the increased CPU utilisation and automatically scaled the autoscaling group from 3 to 4 instances 

# what i learned 
an auto scaling group maintains the required number of ec2 instances. target tracking can automatically change the desired capacity based on a matricsuch as average CPU utilisation. The application load balancer distributes traffic across the helthy instances registerd in the target group 


