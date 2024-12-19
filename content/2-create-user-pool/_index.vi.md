---
title : "Tạo User Pool"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2. </b> "
---
1. Mở bảng điều khiển của [Amazon Cognito](https://ap-southeast-1.console.aws.amazon.com/cognito/v2/idp/user-pools?region=ap-southeast-1)
2. Chọn **User pools** ở phía menu phía bên trái
    - Ấn **Create user pool**
![CreateUserPool](/images/1/1.png?width=90pc)
3. Chọn **Email**
    - Ấn **Next**
![CreateUserPool](/images/1/2.png?width=90pc)

4. Chọn **Custom** cho mục **Password policy** để chỉnh password theo ý bạn muốn
    - Bỏ chọn **Contain at least 1 special character**
    - Để **30** ngày hết hạn
![CreateUserPool](/images/1/3.png?width=90pc)

5. Kéo xuống, chọn **No MFA** cho mục **Multi-factor authentication**
    - Chọn **Email only** cho mục **Delivery method**
    - Ấn **Next**
![CreateUserPool](/images/1/4.png?width=90pc)
6. Để mặc định các tuỳ chọn
![CreateUserPool](/images/1/5.png?width=90pc)
7. Kéo xuống dưới, ấn **Next**
![CreateUserPool](/images/1/6.png?width=90pc)
8. Chọn **Send email with Cognito**
    - Ấn **Next**
![CreateUserPool](/images/1/7.png?width=90pc)

9. Nhập tên cho user pool, ví dụ: `cognito-fcj-book-shop`
![CreateUserPool](/images/1/8.png?width=90pc)

10. Chọn **Public client**
    - Nhập tên cho app client, ví dụ: `fcj-book-shop`
![CreateUserPool](/images/1/9.png?width=90pc)

11. Mở rộng phần **Advanced app client settings**
    - Chọn **ALLOW_USER_PASSWORD_AUTH**
![CreateUserPool](/images/1/10.png?width=90pc)

12. Kéo xuống cuối trang và ấn **Create user pool**
![CreateUserPool](/images/1/11.png?width=90pc)

13. Ấn vào user pool vừa tạo
    - Ấn chọn **App intergation**
![CreateUserPool](/images/1/12.png?width=90pc)
14. Nhấn vào **App integration**
![CreateUserPool](/images/1/13.png?width=90pc)

15. Kéo xuống cuối trang, ghi lại **Client ID**
 ![CreateUserPool](/images/1/14.png?width=90pc)
