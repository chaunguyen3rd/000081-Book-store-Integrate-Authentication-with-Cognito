---
title : "Tạo User Pool"
date :  2025-02-11
weight : 2
chapter : false
pre : " <b> 2. </b> "
---
1. Mở [Amazon Cognito console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
    - Chọn **User pools** trên menu bên trái.
    - Nhấp vào **Create user pool**.
      ![CreateUserPool](/images/temp/1/4.png?width=90pc)

2. Tại trang **Set up resources for your application**.
    - Chọn **Traditional web application**.
    - Nhập `cognito-fcj-book-shop` vào trường **Name your application**.
      ![CreateUserPool](/images/temp/1/5.png?width=90pc)
    - Cuộn xuống, chọn **Email** tại hộp **Configure options**.
    - Nhấp vào nút **Create user directory**.
      ![CreateUserPool](/images/temp/1/6.png?width=90pc)

3. Quay lại **Amazon Cognito** management console.
    - Chọn **User pools** trên menu bên trái.
    - Chọn **User pool - ...** vừa tạo trước đó.
      ![CreateUserPool](/images/temp/1/7.png?width=90pc)

4. Tại trang **User pool - ...**.
    - Nhấp vào **App clients** trên menu bên trái.
    - Nhấp vào tên App client **cognito-fcj-book-shop**.
      ![CreateUserPool](/images/temp/1/8.png?width=90pc)

5. Tại trang **App client: cognito-fcj-book-shop**.
    - Ghi lại giá trị **Client ID** và **Client secret** để sử dụng sau này.
    - Nhấp vào nút **Edit**.
      ![CreateUserPool](/images/temp/1/9.png?width=90pc)

6. Tại trang **Edit app client information**.
    - Chọn **Sign in with username and password: ALLOW_USER_PASSWORD_AUTH** tại **Authentication flows**.
      ![CreateUserPool](/images/temp/1/10.png?width=90pc)
    - Cuộn xuống cuối trang và nhấp vào nút **Save changes**.
      ![CreateUserPool](/images/temp/1/11.png?width=90pc)
