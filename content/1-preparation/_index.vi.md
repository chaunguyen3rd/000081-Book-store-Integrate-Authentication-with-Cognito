---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Trước khi chúng ta đi vào nội dung chính của workshop này, chúng ta cần đặt lại ứng dụng web.

1. Tải xuống mã nguồn dưới đây.

{{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

2. Chạy các lệnh dưới đây.
{{% notice note %}}
Đảm bảo rằng bạn đã cài đặt AWS CLI và SAM CLI trên máy của mình, cấu hình thông tin xác thực AWS trước khi chạy các lệnh.
{{% /notice %}}
    ```
    sam build
    sam validate
    sam deploy --guided
    ```

3. Nhập nội dung sau. Để mặc định.
    - Stack Name []: `fcj-book-store`
    - AWS Region []: `us-east-1`
    - Confirm changes before deploy [Y/n]: y
    - Allow SAM CLI IAM role creation [Y/n]: y
    - Disable rollback [y/N]: n
    - Save arguments to configuration file [Y/n]: y
      ![Preparation](/images/temp/1/1.png?width=90pc)

4. Tải xuống mã nguồn **FCJ-Serverless-Workshop** về thiết bị của bạn.
    - Mở terminal trên máy tính của bạn trong thư mục nơi bạn muốn lưu mã nguồn.
    - Sao chép lệnh dưới đây.
      ```
      git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
      cd FCJ-Serverless-Workshop
      ```
    - Mở **FCJ-Serverless-Workshop** bằng VSCode và chỉnh sửa.
      - Mở **src/component/Authen/Login.js** và chỉnh sửa như dưới đây.
        ```
        data: JSON.stringify({
            username: email,
            password: password
        })
        ```
        ![Preparation](/images/temp/1/2.png?width=90pc)
      - Tiếp theo, mở **src/component/Authen/Register.js** và chỉnh sửa như dưới đây.     
        ```
        data: JSON.stringify({
          username: email,
          password: password
        })
        ```

        ```
        data: JSON.stringify({
            username: email,
            confirmation_code: code
        })
        ```
        ![Preparation](/images/temp/1/3.png?width=90pc)
    - Quay lại thư mục gốc của **FCJ-Serverless-Workshop** và chạy các lệnh dưới đây.     
      ```
      yarn
      yarn build
      ```

5. Chúng ta đã hoàn thành việc xây dựng front-end. Tiếp theo, thực hiện lệnh sau để tải thư mục **build** lên S3.
    ```
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```

Vậy là chúng ta đã xây dựng lại ứng dụng web.
