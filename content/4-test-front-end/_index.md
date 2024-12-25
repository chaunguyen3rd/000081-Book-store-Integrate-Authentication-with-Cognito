---
title : "Test with front-end"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
We will try registration and login from web application to test API Gateway, Lambda function and User pool working.

1. Open [API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Click **APIs** on the left menu.
    - Choose **fcj-serverless-api**.
      ![TestFrontEnd](/images/temp/1/26.png?width=90pc)
    - Click **Stages** on the left menu.
    - Choose **Staging**.
    - Record **Invoke URL**.
      ![TestFrontEnd](/images/temp/1/27.png?width=90pc)

2. Open **config.js** file in source code folder of application - **FCJ-Serverless-Workshop**.
    - Replace **APP_API_URL** with **InvokeURL**.
      ![TestFrontEnd](/images/temp/1/28.png?width=90pc)

3. Open your terminal and run the below commands.
    ```
    yarn build
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```

4. Open [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=us-east-1). 
    - Click **fcj-book-shop-by-myself** bucket.
      ![TestFrontEnd](/images/temp/1/29.png?width=90pc)
    - At **fcj-book-shop-by-myself** page.
      - Click **Properties** tab.
        ![TestFrontEnd](/images/temp/1/30.png?width=90pc)
      - Scroll down to the bottom and record **Bucket website endpoint** url.
        ![TestFrontEnd](/images/temp/1/31.png?width=90pc)

7. Open your browser with recorded **Bucket website endpoint** url.
    - Click **Register**.
      ![TestFrontEnd](/images/temp/1/32.png?width=90pc)
    - At **FCJ Book Store - Register** page.
      - Enter your email, password and re-enter password.
      - Click **Register** button.
        ![TestFrontEnd](/images/temp/1/33.png?width=90pc)
    - Open your **Mail** box and record the confirmation code.
        ![TestFrontEnd](/images/temp/1/34.png?width=90pc)
    - Back to **Verify Email** page.
      - Enter the **Confirmation** code you recorded.
      - Click **Submit** button.
        ![TestFrontEnd](/images/temp/1/35.png?width=90pc)
    - After successfully verified email, it will redirect you to **FCJ Book Store - Login** page.
      - Enter your **Email** and **Password**.
      - Click **Submit** button.
        ![TestFrontEnd](/images/temp/1/36.png?width=90pc)
    - After successful login, the features: **Create new book**, **Management**, **Order** appear allowing users to use.
      ![TestFrontEnd](/images/temp/1/37.png?width=90pc)
