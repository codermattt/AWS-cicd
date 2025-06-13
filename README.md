## AWS-cicd
- The architecture inluded Bitbucket, AWS CodeBuild, AWS CodePipeline, AWS Elastic Beanstalk and AWS RDS
- A project where I pulled source code from Bit Bucket, used AWS CodeBuild to build source code into an artifact, then deploy onto Elastic Beanstalk which was          connected to RDS
 
## Overview
- I migrated source code from GitHub to a Bitbucket repo.
- 3 stages in the pipeline -> fetch source code from Bitbucket repo, trigger CodeBuild to build into artifact, then deploy onto Elastic Beanstalk
- The web app was running on Beanstalk, which accessed RDS
- The application connects to RDS through the application.properties file
- The build details came from the buildspec.yml file

## Source Code
- https://github.com/hkhcoder/vprofile-project/tree/aws-ci

