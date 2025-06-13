## AWS Elastic Beanstalk Deployment with RDS, CodeBuild, and CodePipeline
- This project briefly outlines the deployment of a Java web application using AWS Elastic Beanstalk, RDS, CodeBuild, CodePipeline, and Bitbucket as the source control provider.

## Elastic Beanstalk Setup
Platform & Configuration:
- Platform: Tomcat 10 with Corretto 21 (Amazon Linux 2023 AMI)
= Instance Type: Public IP enabled, all subnets activated (for instance distribution)
- Load Balancer:
  - Minimum Instances: 2
  - Maximum Instances: 4
- Stickiness: Enabled (users stay connected to the same instance)
- Deployment Strategy: Rolling deployment with 50% update rate (1 instance upgraded at a time)

## RDS (MySQL)
- Database Configuration:
  - Used Amazon RDS with MySQL (Free Tier)
  - Auto-generated password for root access
  - RDS security group modified to allow inbound traffic from Elastic Beanstalk's security group on port 3306
- Database Initialization:
  - SSH into one of the EC2 instances created by Elastic Beanstalk
  - Install MySQL client
  - Connect to RDS
    - mysql -h <RDS-ENDPOINT> -u <USERNAME> -p
  - Initialize database
    - wget https://raw.githubusercontent.com/hkhcoder/vprofile-project/refs/heads/aws-ci/src/main/resources/db_backup.sql
    - mysql -h <RDS-ENDPOINT> -u <USERNAME> -p < db_backup.sql
## Code Commit Migration (GitHub → Bitbucket)
- Steps:
  - Created a Bitbucket account
  - Generated SSH keys using Git Bash
  - Configured SSH in ~/.ssh/config to use the Bitbucket keys
  - Cloned original GitHub repo:
    - git clone https://github.com/hkhcoder/vprofile-project
  - Checked out all branches
  - Removed GitHub remote and added Bitbucket remote

## Build Phases (CodeBuild)
- I first created an s3 bucket to store the artifacts
- Install Phase
  - Specify runtime: java -> corretto17
- Pre-build Phase
  - Update packages and install dependencies:
    - jq for JSON parsing
    - Apache Maven 3.9.8 (manually downloaded and extracted)
- Modify application properties:
  - Set correct RDS password
  - Confirm RDS username
  - Replace default DB host with actual RDS endpoin
- Build Phase
  - Compile the Java application
    - mvn install 
- Post-build Phase
  - Package the build for deployment:  
    - mvn package
- Artifacts
  - The compiled artifact is located in: target/vprofile-v2
    - All files inside this directory (**/*) are bundled and sent to the S3 bucket
- Logging
  - CloudWatch logs was used to track and debug build steps and failures in real-time.
## AWS CodePipeline
- Created a custom pipeline with a new IAM service role
- Manually added Elastic Beanstalk permissions to the role
- Pipeline Workflow:
  - Source: Bitbucket (triggered on commit)
  - Build: AWS CodeBuild project
  - Deploy: AWS Elastic Beanstalk environment





