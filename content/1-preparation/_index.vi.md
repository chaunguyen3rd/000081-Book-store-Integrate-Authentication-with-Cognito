---
title : "Chuẩn bị"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Trước khi thực hiện nội dung chính của workshop này, chúng ta cần thiết lập lại ứng dụng web.
1. Tải source code dưới đây

{{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

2. Chạy các câu lệnh dưới đây
```
sam build
sam deploy --guided
```

3. Nhập nội dung như sau:

- Stack Name []: `fcj-book-store`
- AWS Region []: `ap-southeast-1`
- Confirm changes before deploy [Y/n]: y
- Allow SAM CLI IAM role creation [Y/n]: y
- Disable rollback [y/N]: n
- BooksList may not have authorization defined, Is this okay? [y/N]: y
- BookCreate may not have authorization defined, Is this okay? [y/N]: y
- BookDelete may not have authorization defined, Is this okay? [y/N]: y
Save arguments to configuration file [Y/n]: y

4. Tải code **FCJ-Serverless-Workshop** về máy của bạn
- Mở terminal trên máy bạn tại thư mục bạn muốn lưu source code
- Sao chép câu lệnh dưới đây
```
git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
cd FCJ-Serverless-Workshop
yarn build
```
5. Chúng ta đã build xong front-end. Tiếp theo thực hiện câu lệnh sau để tải thư mục **build** lên S3
```
aws s3 cp build s3://fcj-book-store --recursive
```

Vậy là chúng ta đã tạo lại xong ứng dụng web.
