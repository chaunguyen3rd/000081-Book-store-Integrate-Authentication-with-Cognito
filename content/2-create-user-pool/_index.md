---
title : "Create User Pool"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2. </b> "
---
1. Open [Amazon Cognito console](https://ap-southeast-1.console.aws.amazon.com/cognito/v2/idp/user-pools?region=ap-southeast-1)
2. Select **User pools** on the left menu.
    - Click **Create user pool**
![CreateUserPool](/images/1/1.png?width=90pc)

3. Select **Email**
    - Click **Next**
![CreateUserPool](/images/1/2.png?width=90pc)

4. Select **Custom** for **Password policy** pattern to customize the password as you want
    - Uncheck **Contain at least 1 special character**
    - Set **30** for expiration date
![CreateUserPool](/images/1/3.png?width=90pc)

5. Scroll down, select **No MFA** for **Multi-factor authentication** pattern
    - Select **Email only** for **Delivery method** pattern
    - Click **Next**
![CreateUserPool](/images/1/4.png?width=90pc)

6. Leave the options as default
![CreateUserPool](/images/1/5.png?width=90pc)

7. Scroll down, click **Next**
![CreateUserPool](/images/1/6.png?width=90pc)

8. Select **Send email with Cognito**
    - Click **Next**
![CreateUserPool](/images/1/7.png?width=90pc)

9. Enter user pool name, such as: `cognito-fcj-book-shop`
![CreateUserPool](/images/1/8.png?width=90pc)

10. Select **Public client**
    - Enter app client name, such as: `fcj-book-shop`
![CreateUserPool](/images/1/9.png?width=90pc)

11. Expand the **Advanced app client settings** pattern
    - Select **ALLOW_USER_PASSWORD_AUTH**
![CreateUserPool](/images/1/10.png?width=90pc)

12. Scroll down to bottom and click **Next**
![CreateUserPool](/images/1/11.png?width=90pc)

13. Click on the user pool you just created
    - Click **Create user pool** tab
![CreateUserPool](/images/1/12.png?width=90pc)

14. Click the **App integration**
![CreateUserPool](/images/1/13.png?width=90pc)

15. Scroll down to the bottom, record the Client ID
 ![CreateUserPool](/images/1/14.png?width=90pc)