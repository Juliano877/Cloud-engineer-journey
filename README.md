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