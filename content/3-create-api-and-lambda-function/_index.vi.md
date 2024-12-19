---
title : "Tạo API và Lambda function"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
Sau khi tạo xong User pool, chúng ta tạo API và Lambda function để xử lý yêu cầu đăng ký và đăng nhập của người dùng.

1. Mở tệp **template.yaml** trong source của tệp **fcj-book-shop-sam-ws3.zip** bạn đã tải ở phần chuẩn bị
    - Thêm đoạn script sau dưới **LambdaInvokePermission**
    - Thay tất cả **APP_INTERGATION** bằng **CLIENT_ID** đã ghi lại từ bước trước
      ```
        Login:
          Type: AWS::Serverless::Function
          Properties:
            FunctionName: login
            CodeUri: fcj-book-shop/login
            Handler: login.lambda_handler
            Runtime: python3.9
            Architectures:
              - x86_64
            Environment:
              Variables:
                CLIENT_ID: "APP_INTERGATION"

        Register:
          Type: AWS::Serverless::Function
          Properties:
            FunctionName: register
            CodeUri: fcj-book-shop/register
            Handler: register.lambda_handler
            Runtime: python3.9
            Architectures:
              - x86_64
            Environment:
              Variables:
                CLIENT_ID: "APP_INTERGATION"

        ConfirmUser:
          Type: AWS::Serverless::Function
          Properties:
            FunctionName: confirm_user
            CodeUri: fcj-book-shop/confirm_user
            Handler: confirm_user.lambda_handler
            Runtime: python3.9
            Architectures:
              - x86_64
            Environment:
              Variables:
                CLIENT_ID: "APP_INTERGATION"
      ```
      ![DeployFunction](/images/1/15.png?width=90pc)

2. Cấu trúc thư mục như sau:
      ```
      fcj-book-shop-sam-ws3
      ├── fcj-book-shop
      │   ├── login
      │   │   └── login.py
      │   ├── register
      │   │   └── register.py
      │   ├── confirm_user
      │   │   └── confirm_user.py
      │   ├── ...
      │
      └── template.yaml

      ```
    - Tạo thư mục tên **login** trong thư mục **fcj-book-shop-sam-ws3/fcj-book-shop/**
    - Tạo tệp **login.py** và sao chép đoạn code sau vào nó
    ```
    import json
    import boto3
    import botocore.exceptions
    import os


    client = boto3.client('cognito-idp')
    def initiate_auth(client_id, username, password):
        try:
            response = client.initiate_auth(
                AuthFlow='USER_PASSWORD_AUTH',
                ClientId=client_id,
                AuthParameters={
                    "USERNAME": username,
                    "PASSWORD": password,
                }
            )
            print(response)
            
        except Exception as e:
            return None, e.__str__()
            
        return response, None

    def lambda_handler(event, context):
        user_infor = json.loads(event['body'])
        error = ""
        message = ""
        client_id = os.getenv("CLIENT_ID")
        
            
        resp, msg = initiate_auth(client_id, user_infor['username'], user_infor['password'])
        print(resp)
        
        if msg is not None:
            message = "Login fail!"
            statusCode = 400
        elif resp["AuthenticationResult"]:
            message = "Login sccessful!"
            statusCode = 200
        else:
            message = "Login fail!"
            statusCode = 400


        # TODO implement
        return {
                'statusCode': statusCode,
                'body': message,
                'headers': {
                    'Content-Type': 'application/json',
                    "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
                    "Access-Control-Allow-Origin": "*",
                    "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
                }
        }
    ```
    - Tạo thư mục tên **register** trong thư mục **fcj-book-shop-sam-ws3/fcj-book-shop/**
    - Tạo tệp **register.py** và sao chép đoạn code sau vào nó
    ```
    import json
    import boto3
    import os

    client = boto3.client('cognito-idp')

    def lambda_handler(event, context):
        user_infor = json.loads(event['body'])
        client_id = os.getenv("CLIENT_ID")
        error = None
        try:
            resp = client.sign_up(
                ClientId=client_id,
                Username=user_infor['username'],
                Password=user_infor['password']
                )
        
        except Exception as e:
            error = e.__str__()
            
        if error is None:
            message = "Register successful!"
            statusCode = 200
        else:
            message = "Register fail!"
            statusCode = 400
            print(error)
            
        # TODO implement
        return {
                'statusCode': statusCode,
                'body': message,
                'headers': {
                    'Content-Type': 'application/json',
                    "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
                    "Access-Control-Allow-Origin": "*",
                    "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
                },
        }
    ```
    - Tạo thư mục tên **confirm_user** trong thư mục **fcj-book-shop-sam-ws3/fcj-book-shop/**
    - Tạo tệp **confirm_user.py** và sao chép đoạn code sau vào nó
    ```
    import json
    import boto3
    import os

    client = boto3.client('cognito-idp')

    def lambda_handler(event, context):
        user_infor = json.loads(event['body'])
        client_id = os.getenv("CLIENT_ID")
        user_pool_id = os.getenv("USER_POOL_ID")
        error = None
        try:
            resp = client.confirm_sign_up(
                ClientId=client_id,
                Username=user_infor['username'],
                ConfirmationCode=user_infor['code']
                )
        except Exception as e:
            error = e.__str__()
            
        if error is None:
            message = "Confirm successful!"
            statusCode = 200
        else:
            message = "Confirm fail!"
            statusCode = 400
            print(error)
            
        # TODO implement
        return {
                'statusCode': statusCode,
                'body': message,
                'headers': {
                    'Content-Type': 'application/json',
                    "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
                    "Access-Control-Allow-Origin": "*",
                    "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
                },
        }
    ```

3. Chạy các dòng lệnh dưới đây:
    ```
    sam build
    sam deploy --guided
    ```
      ![DeployFunction](/images/1/16.png?width=90pc)

4. Thêm đoạn script sau vào sau method **delete** của **BookApi**
    ```
              /login:
                post:
                  responses:
                    "200":
                      description: 200 response
                      headers:
                        Access-Control-Allow-Origin:
                          type: string
                  x-amazon-apigateway-integration:
                    uri:
                      Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${Login.Arn}/invocations"
                    responses:
                      default:
                        statusCode: 200
                        responseParameters:
                          method.response.header.Access-Control-Allow-Origin: "'*'"
                    passthroughBehavior: when_no_match
                    httpMethod: POST #always POST
                    type: aws_proxy
              /register:
                post:
                  responses:
                    "200":
                      description: 200 response
                      headers:
                        Access-Control-Allow-Origin:
                          type: string
                  x-amazon-apigateway-integration:
                    uri:
                      Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${Register.Arn}/invocations"
                    responses:
                      default:
                        statusCode: 200
                        responseParameters:
                          method.response.header.Access-Control-Allow-Origin: "'*'"
                    passthroughBehavior: when_no_match
                    httpMethod: POST #always POST
                    type: aws_proxy
              /confirm_user:
                post:
                  responses:
                    "200":
                      description: 200 response
                      headers:
                        Access-Control-Allow-Origin:
                          type: string
                  x-amazon-apigateway-integration:
                    uri:
                      Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${ConfirmUser.Arn}/invocations"
                    responses:
                      default:
                        statusCode: 200
                        responseParameters:
                          method.response.header.Access-Control-Allow-Origin: "'*'"
                    passthroughBehavior: when_no_match
                    httpMethod: POST #always POST
                    type: aws_proxy
    ```
      ![DeployFunction](/images/1/17.png?width=90pc)
      ![DeployFunction](/images/1/18.png?width=90pc)

5. Thêm đoạn script sau vào function **Login** 
    ```
          Events:
            Login:
              Type: Api
              Properties:
                Path: /login/
                Method: post
                RestApiId:
                  Ref: BookApi
    ```
      ![DeployFunction](/images/1/19.png?width=90pc)

- Thêm đoạn script sau vào function **Register** 
    ```
          Events:
            Register:
              Type: Api
              Properties:
                Path: /register/
                Method: post
                RestApiId:
                  Ref: BookApi
    ```
      ![DeployFunction](/images/1/20.png?width=90pc)

  - Thêm đoạn script sau vào function **ConfirmUser** 
    ```
          Events:
            ConfirmUser:
              Type: Api
              Properties:
                Path: /confirm_user/
                Method: post
                RestApiId:
                  Ref: BookApi
    ```
      ![DeployFunction](/images/1/21.png?width=90pc)

6. Chạy các dòng lệnh dưới đây:
    ```
    sam build
    sam deploy --guided
    ```
    - Nhập "y" nếu được hỏi "Login may not have authorization defined, Is this okay? [y/N]: "
    - Nhập "y" nếu được hỏi "Register may not have authorization defined, Is this okay? [y/N]: "
    - Nhập "y" nếu được hỏi "ConfirmUser may not have authorization defined, Is this okay? [y/N]: "
      ![DeployFunction](/images/1/22.png?width=90pc)
Chúng ta đã hoàn thành việc triển khai các API và Lambda function.