---
title : "Preparation"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Before we get to the main content of this workshop, we need to reset the web application.
1. Download the below source code

{{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

2. Run the below commands
```
sam build
sam deploy --guided
```

3. Enter the following content:

- Stack Name []: `fcj-book-store`
- AWS Region []: `ap-southeast-1`
- Confirm changes before deploy [Y/n]: y
- Allow SAM CLI IAM role creation [Y/n]: y
- Disable rollback [y/N]: n
- BooksList may not have authorization defined, Is this okay? [y/N]: y
- BookCreate may not have authorization defined, Is this okay? [y/N]: y
- BookDelete may not have authorization defined, Is this okay? [y/N]: y
- Save arguments to configuration file [Y/n]: y

4. Download the **FCJ-Serverless-Workshop** code to your device
- Open a terminal on your computer in the folder where you want to save the source code
- Copy the below command
```
git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
cd FCJ-Serverless-Workshop
yarn build
```
5. We have finished building the front-end. Next execute the following command to upload the **build** folder to S3
```
aws s3 cp build s3://fcj-book-store --recursive
```

So we have rebuilt the web application.
