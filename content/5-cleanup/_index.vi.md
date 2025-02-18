---
title : "Dọn dẹp"
date :  2025-02-11
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
1. Làm trống S3 bucket.
    - Mở [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
    - Chọn **fcj-book-shop-by-myself**.
    - Nhấp vào **Empty**.
    - Nhập **permanently delete**.
    - Nhấp vào **Empty**.
    - Làm tương tự cho bucket bắt đầu bằng **aws-sam-cli-managed-default-**.

2. Xóa các stack CloudFormation.
    - Thực hiện lệnh dưới đây để xóa ứng dụng AWS SAM.

      ```bash
      sam delete --stack-name fcj-book-store
      sam delete --stack-name aws-sam-cli-managed-default
      ```

    - Nếu bạn gặp vấn đề khi xóa bằng lệnh. Mở [AWS Cloudformation console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/getting-started). Sau đó, xóa tất cả các stack liên quan đến workshop này.

3. Mở [Amazon Cognito console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
    - Chọn **User pools** trên menu bên trái.
    - Chọn **User pool - ...** vừa tạo trước đó.
    - Nhấp vào nút **Delete**.
      ![CreateUserPool](/images/temp/1/38.png?width=90pc)
    - Tại popup **Delete user pool "User pool - ..."?**.
      - Chọn **Delete Cognito domain ...** và **Deactivate deletion protection**.
      - Nhập `User pool - ...` vào trường **2. To confirm deletion**.
      - Nhấp vào nút **Delete**.
        ![CreateUserPool](/images/temp/1/39.png?width=90pc)
