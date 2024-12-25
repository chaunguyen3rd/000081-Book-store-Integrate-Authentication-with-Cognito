---
title : "Kiểm tra hoạt động"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
Chúng ta sẽ thử đăng ký và đăng nhập từ ứng dụng web để kiểm tra API Gateway, hàm Lambda và User pool hoạt động.

1. Mở [API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Nhấp vào **APIs** trên menu bên trái.
    - Chọn **fcj-serverless-api**.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/26.png?width=90pc)
    - Nhấp vào **Stages** trên menu bên trái.
    - Chọn **Staging**.
    - Ghi lại **Invoke URL**.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/27.png?width=90pc)

2. Mở tệp **config.js** trong thư mục mã nguồn của ứng dụng - **FCJ-Serverless-Workshop**.
    - Thay thế **APP_API_URL** bằng **InvokeURL**.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/28.png?width=90pc)

3. Mở terminal của bạn và chạy các lệnh dưới đây.
    ```
    yarn build
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```

4. Mở [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=us-east-1). 
    - Nhấp vào **fcj-book-shop-by-myself** bucket.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/29.png?width=90pc)
    - Tại trang **fcj-book-shop-by-myself**.
      - Nhấp vào tab **Properties**.
        ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/30.png?width=90pc)
      - Cuộn xuống dưới cùng và ghi lại url **Bucket website endpoint**.
        ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/31.png?width=90pc)

5. Mở trình duyệt của bạn với url **Bucket website endpoint** đã ghi lại.
    - Nhấp vào **Đăng ký**.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/32.png?width=90pc)
    - Tại trang **FCJ Book Store - Đăng ký**.
      - Nhập email, mật khẩu và nhập lại mật khẩu của bạn.
      - Nhấp vào nút **Đăng ký**.
        ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/33.png?width=90pc)
    - Mở hộp thư **Email** của bạn và ghi lại mã xác nhận.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/34.png?width=90pc)
    - Quay lại trang **Xác minh Email**.
      - Nhập mã **Xác nhận** mà bạn đã ghi lại.
      - Nhấp vào nút **Gửi**.
        ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/35.png?width=90pc)
    - Sau khi xác minh email thành công, bạn sẽ được chuyển hướng đến trang **FCJ Book Store - Đăng nhập**.
      - Nhập **Email** và **Mật khẩu** của bạn.
      - Nhấp vào nút **Gửi**.
        ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/36.png?width=90pc)
    - Sau khi đăng nhập thành công, các tính năng: **Tạo sách mới**, **Quản lý**, **Đặt hàng** sẽ xuất hiện cho phép người dùng sử dụng.
      ![TestFrontEnd](/000081-Book-store-Integrate-Authentication-with-Cognito/images/temp/1/37.png?width=90pc)
