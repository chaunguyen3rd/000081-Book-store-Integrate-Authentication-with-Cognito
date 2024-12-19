---
title : "Kiểm tra hoạt động"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
Chúng ta sẽ thử đăng ký và đăng nhập từ ứng dụng web để kiểm tra hoạt động của API Gateway, Lambda function và User pool
1. Mở bảng điều khiển của [API Gateway](https://ap-southeast-1.console.aws.amazon.com/apigateway/main/apis?region=ap-southeast-1)
      - Ấn vào **API Gateway REST API to Lambda**

2. Chọn **Stage** ở menu phía bên trái
      - Ấn **staging**
      - Ghi lại **InvokeURL**
![UpdateSource](/images/1/23.png?width=90pc)

3. Mở tệp **config.js** trong thư mục source code của ứng dụng - **FCJ-Serverless-Workshop**
      - Thay **APP_API_URL** bằng **InvokeURL**

4. Chạy các dòng lệnh dưới đây:
   ```
    yarn build
    aws s3 cp build s3://fcj-book-store --recursive
    ```
3. Mở bảng điều khiển [Amazon S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1). Ấn chọn bucket **fcj-book-store**
![UpdateSource](/images/1/24.png?width=90pc)

5. Chọn tab **Properties**. Kéo xuống cuối tramg, nhấn vào endpoint của web
![UpdateSource](/images/1/25.png?width=90pc)

7. Ấn **Register** góc bên phải
![UpdateSource](/images/1/26.png?width=90pc)

8. Nhập thông tin đăng ký email, mật khẩu và nhập lại mật khẩu
     - Ấn nút **Register**
9. Bạn sẽ gặp thông báo **Register fail**
     - Lỗi do API của chúng ta thiếu **Access-Control-Allow-Headers** trong headers của phản hồi
![UpdateSource](/images/1/27.png?width=90pc)
{{% notice note %}}
Đăng ký bằng email mà bạn đang dùng để có thể lấy được code xác thực tài khoản
{{% /notice %}}


10. Để giải quyết lỗi này, mở tệp **template.yaml** trong source của tệp **fcj-book-store-sam-ws3.zip**
     - Thêm đoạn script sau cho **BookApi**
     ```
        AllowMethods: "'GET,POST,OPTIONS,DELETE'"
        AllowHeaders: "'content-type'"
        AllowOrigin: "'*'"
     ```
      ![UpdateSource](/images/1/28.png?width=90pc)

      - Chạy các câu lệnh sau:
      ```
      sam build
      sam deploy --guided
      ```

10. Quay lại màn hình đăng ký và ấn **Register**
![UpdateSource](/images/1/29.png?width=90pc)
11. Trở lại với bảng điều khiển của Amazon Cognito
     - Tại tab **Users**, xuất hiên một người dùng nhưng vẫn ở trạng thái **Unconfirmed**
       ![UpdateSource](/images/1/30.png?width=90pc)

12. Mở email mà bạn vừa đăng ký tài khoản, lấy mã xác nhận được gửi từ **no-reply@verificationemail.com**
13. Nhập mã xác nhận vào màn hình xác thực
      - Ấn **Submit**
![UpdateSource](/images/1/31.png?width=90pc)

14. Trở lại với bảng điều khiển của Amazon Cognito
      - Người dùng đã chuyển sang trạng thái **Confirmed**
![UpdateSource](/images/1/32.png?width=90pc)

15. Nhập thông tin tài khoản: email, mật khẩu để đăng nhập
     - Ấn **Submit**
![UpdateSource](/images/1/33.png?width=90pc)

16. Sau khi đăng nhập thành công, các tính năng: **Create new book**, **Management**, **Order** xuất hiện cho phép người dùng sử dụng
![UpdateSource](/images/1/34.png?width=90pc)
