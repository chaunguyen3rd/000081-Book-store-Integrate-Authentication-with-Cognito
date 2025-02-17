---
title : "Serverless - Authentication with Amazon Cognito"
date :  2025-02-11
weight : 1 
chapter : false
---
# Serverless - Authentication with Amazon Cognito

#### Overview

We went through 3 workshops and built a simple web application with the serverless model in the cloud. For our web application to authenticate, the functions are only used when the user is logged in. So in this workshop, we'll learn about AWS Cognito - which provides authentication, authorization, and user management for web and mobile applications.

The architecture of the web application will look like this.
![ServerlessExample](/images/serverless-diagram.png?featherlight=false&width=50pc)

#### AWS Cognito

AWS Cognito allows us to easily build a flow of sign-in, sign-up, verify email, change password, reset password, etc.,  instead of having to build DB for users and do many things yourself like JWT, hash password, send mail verify,... This helps you focus on developing other features of the application. Users can log in directly with a username and password or through a third party like Facebook, Amazon, Google, or Apple.

The two main components of Amazon Cognito are User pools and Identity pools:

- **User pools**: user directories provide registration and login options for your web and mobile app users. After logging in with the user pool, the application users can access the resources that the application allows
- **Identity pools**: grant your users access to other AWS services

An Amazon Cognito user pool and identity pool used together:

![ScenarioCognito](/images/0001.jpeg?featherlight=false&width=60pc)

- In the first step your app user signs in through a user pool and receives user pool tokens after a successful authentication.
- Next, your app exchanges the user pool tokens for AWS credentials through an identity pool.
- Finally, your app user can then use those AWS credentials to access other AWS services such as Amazon S3 or DynamoDB.

#### Content

1. [Preparation](1-preparation/)
2. [Create User Pool](2-create-user-pool/)
3. [Create API and Lambda function](3-create-api-and-lambda-function/)
4. [Test with front-end](4-test-front-end)
5. [Cleanup](5-cleanup)
