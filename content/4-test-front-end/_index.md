---
title : "Test with front-end"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
We will try registration and login from web application to test API Gateway, Lambda function and User pool working.
1. Open [API Gateway console](https://ap-southeast-1.console.aws.amazon.com/apigateway/main/apis?region=ap-southeast-1)
     - Click **API Gateway REST API to Lambda**

2. Select **Stage** on the left menu
     - Click **staging**
     - Record **InvokeURL**
![UpdateSource](/images/1/23.png?width=90pc)

3. Open **config.js** file in source code folder of application - **FCJ-Serverless-Workshop**
     - Replace **APP_API_URL** with **InvokeURL**
4. Run the below commands:
    ```
      yarn build
      aws s3 cp build s3://fcj-book-store --recursive
    ```
3. Open [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1). Click **fcj-book-store** bucket
![UpdateSource](/images/1/24.png?width=90pc)
5. Click **Properties** tab. Scroll down to bottom, click to web endpoint
![UpdateSource](/images/1/25.png?width=90pc)


7. Click **Register** on right corner
![UpdateSource](/images/1/26.png?width=90pc)

8. Enter registration information: email, password and re-enter password
      - Click **Register** button
9. A prompt will appear displaying the **Register fail**
      - The error due our API missing **Access-Control-Allow-Headers** in response headers
![UpdateSource](/images/1/27.png?width=90pc)
{{% notice note %}}
Register with the email you are using to get the account verification code
{{% /notice %}}

10. To resolve this error, open **template.yaml** file in source of **fcj-book-store-sam-ws3.zip** file
      - Add the below script for **BookApi**
      ```
        AllowMethods: "'GET,POST,OPTIONS,DELETE'"
        AllowHeaders: "'content-type'"
        AllowOrigin: "'*'"
      ```
      ![UpdateSource](/images/1/28.png?width=90pc)

      - Run the following commands:
      ```
       sam build
       sam deploy --guided
      ```

10. Go back to the registration screen and click **Register** button
![UpdateSource](/images/1/29.png?width=90pc)
11. Back to the Amazon Cognito console
       - At **Users** tab, display a user but still in **Unconfirmed** state
       ![UpdateSource](/images/1/30.png?width=90pc)
12. Open the email you just registered for an account, get the confirmation code sent from **no-reply@verificationemail.com**
13. Enter the confirmation code in the verification screen
       - Click **Submit**
![UpdateSource](/images/1/31.png?width=90pc)


14. Back to the Amazon Cognito console
       - State of user changed to **Confirmed**
![UpdateSource](/images/1/32.png?width=90pc)

15. Enter account information: email, password to login
       - Click **Submit**
![UpdateSource](/images/1/33.png?width=90pc)

16. After successful login, the features: **Create new book**, **Management**, **Order** appear allowing users to use
![UpdateSource](/images/1/34.png?width=90pc)