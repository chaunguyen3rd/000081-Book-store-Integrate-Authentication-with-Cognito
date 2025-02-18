---
title : "Create User Pool"
date :  2025-02-11
weight : 2
chapter : false
pre : " <b> 2. </b> "
---
1. Open [Amazon Cognito console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
    - Select **User pools** on the left menu.
    - Click **Create user pool**.
      ![CreateUserPool](/images/temp/1/4.png?width=90pc)

2. At **Set up resources for your application** page.
    - Select **Traditional web application**.
    - Enter `cognito-fcj-book-shop` at **Name your application** field.
      ![CreateUserPool](/images/temp/1/5.png?width=90pc)
    - Scroll down, select **Email** at **Configure options** box.
    - Click **Create user directory** button.
      ![CreateUserPool](/images/temp/1/6.png?width=90pc)

3. Back to **Amazon Cognito** management console.
    - Select **User pools** on the left menu.
    - Choose **User pool - ...** that just created before.
      ![CreateUserPool](/images/temp/1/7.png?width=90pc)

4. At **User pool - ...** page.
    - Click **App clients** on the left menu.
    - Click **cognito-fcj-book-shop** App client name.
      ![CreateUserPool](/images/temp/1/8.png?width=90pc)

5. At **App client: cognito-fcj-book-shop** page.
    - Record **Client ID** and **Client secret** value for later use.
    - Click **Edit** button.
      ![CreateUserPool](/images/temp/1/9.png?width=90pc)

6. At **Edit app client information** page.
    - Select **Sign in with username and password: ALLOW_USER_PASSWORD_AUTH** at **Authentication flows**.
      ![CreateUserPool](/images/temp/1/10.png?width=90pc)
    - Scroll down to the bottom and click **Save changes** button.
      ![CreateUserPool](/images/temp/1/11.png?width=90pc)
