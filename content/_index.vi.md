---
title : "Serverless - Xác thực với Amazon Cognito"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Serverless - Xác thực với Amazon Cognito

#### Tổng quan

Chúng ta đã đi qua 3 bài và xây dựng một ứng dụng web đơn giản với mô hình serverless trên cloud. Để ứng dụng web của chúng ta xác thực, các chức năng chỉ được sử dụng khi người dùng đã đăng nhập. Do đó bài này chúng ta sẽ tìm hiểu về AWS Cognito - cung cấp xác thực, ủy quyền và quản lý người dùng cho ứng dụng web và di động.


Kiến trúc của ứng dụng web sẽ như sau:
![SeverlessExample](/000081-Book-store-Integrate-Authentication-with-Cognito/images/serverless-diagram.png?featherlight=false&width=50pc)



#### AWS Cognito
AWS Cognito cho phép chúng ta trong việc xây dựng luồng sign-in, sign-up, verify email, thay đổi password, đặt lại password,... một cách dễ dàng hơn, thay vì ta phải tự xây dựng DB cho user và tự làm nhiều thứ như JWT, hash password, send mail verify,... Điều này giúp bạn tập chung vào phát triển tính năng khác của ứng dụng. Người dùng có thể đăng nhập trực tiếp bằng tên người dùng và mật khẩu hoặc thông qua bên thứ ba như Facebook, Amazon, Google hoặc Apple. 

Hai thành phần chính của Amazon Cognito là User pools and Identity pools:

- User pools: các thư mục người dùng cung cấp tùy chọn đăng ký và đăng nhập cho người dùng ứng dụng web và thiết bị di động của bạn. Sau khi đăng nhập với user pool, người dùng ứng dụng có thể truy cập các tài nguyên mà ứng dụng cho phép
- Identity pools: cung cấp thông tin xác thực AWS để cấp cho người dùng của bạn quyền truy cập vào các dịch vụ AWS khác.

Ví dụ sử dụng user pool và identity pool cùng nhau

![ScenarioCognito](/000081-Book-store-Integrate-Authentication-with-Cognito/images/0001.jpeg?featherlight=false&width=60pc)

- Trong bước đầu tiên, người dùng ứng dụng của bạn đăng nhập thông qua user pool và nhận mã thông báo user pool sdùng sau khi xác thực thành công.
- Tiếp theo, ứng dụng của bạn trao đổi mã thông báo user pool dùng lấy thông tin đăng nhập AWS thông qua nhóm nhận dạng.
- Cuối cùng, người dùng ứng dụng của bạn sau đó có thể sử dụng các thông tin đăng nhập AWS đó để truy cập các dịch vụ AWS khác như Amazon S3 hoặc DynamoDB.

#### Nội dung

 1. [Chuẩn bị](1-preparation/)
 2. [Tạo User Pool](2-create-user-pool/)
 3. [Tạo API và Lambda function](3-create-api-and-lambda-function/)
 4. [Kiểm tra với front-end](4-test-front-end)
 5. [Dọn dẹp tài nguyên](5-cleanup)
