---
title : "Tạo API và Lambda function"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
Sau khi tạo User pool, chúng ta tạo một API và một Lambda function để xử lý các yêu cầu đăng ký và đăng nhập người dùng.
#### Chuẩn bị
1. Thêm các tham số cần thiết để hàm được thực thi.
  - Sao chép các khối mã dưới đây vào tệp **template.yaml** trong nguồn của tệp **fcj-book-shop-sam-ws3.zip** đã tải xuống trong phần chuẩn bị.
    ```
    cognitoClientID:
    Type: String
    Default: APP_CLIENT_ID

    cognitoClientSecret:
    Type: String
    Default: APP_CLIENT_SECRET
    ```
  - Thay đổi **APP_CLIENT_ID** và **APP_CLIENT_SECRET** thành giá trị của ứng dụng khách Cognito đã ghi lại trước đó.
  ![DeployFunction](/images/temp/1/14.png?width=90pc)

2. Tạo một bản triển khai ``sam`` mới.
  - Mở tệp **template.yaml** trong nguồn của tệp **fcj-book-shop-sam-ws3.zip** đã tải xuống trong phần chuẩn bị.
  - Chú thích các khối mã như dưới đây.
    ![DeployFunction](/images/temp/1/12.png?width=90pc)
  - Chạy các lệnh dưới đây.     
    ```
    sam build
    sam validate
    sam deploy --guided
    ```
    ![DeployFunction](/images/temp/1/13.png?width=90pc)

#### Tạo hàm Registration
Tại tệp **template.yaml** trong nguồn của tệp **fcj-book-shop-sam-ws3.zip** đã tải xuống trong phần chuẩn bị.
1. Tạo tham số **Registration**.
  - Sao chép và dán khối mã dưới đây như hình dưới.
    ```
    registerPathPart:
    Type: String
    Default: register
    ```
    ![DeployFunction](/images/temp/1/21.png?width=90pc)

2. Tạo hàm **Registration**.
  - Sao chép và dán các khối mã dưới đây vào cuối tệp.
    ```
    Register:
    Type: AWS::Serverless::Function
    Properties:
      CodeUri: fcj-book-shop/register
      Handler: register.lambda_handler
      Runtime: python3.11
      FunctionName: register
      Architectures:
      - x86_64
      Environment:
      Variables:
        CLIENT_ID: !Ref cognitoClientID
        CLIENT_SECRET: !Ref cognitoClientSecret

    RegisterApiResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      RestApiId: !Ref BookApi
      ParentId: !GetAtt BookApi.RootResourceId
      PathPart: !Ref registerPathPart

    RegisterApiOptions:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: OPTIONS
      RestApiId: !Ref BookApi
      ResourceId: !Ref RegisterApiResource
      AuthorizationType: NONE
      Integration:
      Type: MOCK
      IntegrationResponses:
        - StatusCode: "200"
        ResponseParameters:
          method.response.header.Access-Control-Allow-Origin: "'*'"
          method.response.header.Access-Control-Allow-Methods: "'OPTIONS,POST,GET,DELETE'"
          method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
      MethodResponses:
      - StatusCode: "200"
        ResponseParameters:
        method.response.header.Access-Control-Allow-Origin: true
        method.response.header.Access-Control-Allow-Methods: true
        method.response.header.Access-Control-Allow-Headers: true

    RegisterApi:
    Type: AWS::ApiGateway::Method
    Properties:
      HttpMethod: POST
          RestApiId: !Ref BookApi
          ResourceId: !Ref RegisterApiResource
          AuthorizationType: NONE
          Integration:
            Type: AWS_PROXY
            IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
            Uri: !Sub >-
              arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${Register.Arn}/invocations
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      RegisterApiInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          FunctionName: !Ref Register
          Action: lambda:InvokeFunction
          Principal: apigateway.amazonaws.com
          SourceAccount: !Ref "AWS::AccountId"
      ```
      ![DeployFunction](/images/temp/1/16.png?width=90pc)

3. Cấu trúc thư mục như dưới đây.
      ```
      fcj-book-shop-sam-ws3
      ├── fcj-book-shop
      │   ├── register
      │   │   └── register.py
      │   ├── ...
      │
      └── template.yaml
      ```
    - Tạo thư mục **register** trong thư mục **fcj-book-shop-sam-ws3/fcj-book-shop/**.
    - Tạo tệp **register.py** và sao chép đoạn code sau vào nó.
      ```
      import json
      import boto3
      import os
      import hmac
      import hashlib
      import base64

      # Initialize the Cognito client
      client = boto3.client("cognito-idp")

      headers = {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
          "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token"
      }


      def lambda_handler(event, context):
          # Get the body from the event and parse it as JSON
          body = json.loads(event["body"])

          # Get the username and password from the body
          username = body["username"]
          password = body["password"]

          # Get Client ID and Client Secret from environment variables
          client_id = os.environ["CLIENT_ID"]
          client_secret = os.environ["CLIENT_SECRET"]

          # Generate the secret hash
          message = bytes(username + client_id, 'utf-8')
          key = bytes(client_secret, 'utf-8')
          secret_hash = base64.b64encode(
              hmac.new(key, message, digestmod=hashlib.sha256).digest()).decode()

          try:
              # Sign up the user
              client.sign_up(
                  ClientId=client_id,
                  SecretHash=secret_hash,
                  Username=username,
                  Password=password
              )

              return {
                  "statusCode": 200,
                  "headers": headers,
                  "body": json.dumps("User registration successful")
              }

          except Exception as e:
              print(f"Error registering user: {e}")
              raise Exception(f"Error registering user: {e}")
      ```
      ![DeployFunction](/images/temp/1/17.png?width=90pc)

#### Tạo hàm Confirm
Tại tệp **template.yaml** trong nguồn của tệp **fcj-book-shop-sam-ws3.zip** đã tải xuống trong phần chuẩn bị.
1. Tạo tham số **Confirm**.
    - Sao chép và dán khối mã dưới đây như hình dưới.
      ```
      confirmPathPart:
        Type: String
        Default: confirm_user
      ```
      ![DeployFunction](/images/temp/1/18.png?width=90pc)

2. Tạo hàm **Confirm**.
    - Sao chép và dán các khối mã dưới đây vào cuối tệp.
      ```
      Confirm:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/confirm_user
          Handler: confirm_user.lambda_handler
          Runtime: python3.11
          FunctionName: confirm
          Architectures:
            - x86_64
          Environment:
            Variables:
              CLIENT_ID: !Ref cognitoClientID
              CLIENT_SECRET: !Ref cognitoClientSecret

      ConfirmApiResource:
        Type: AWS::ApiGateway::Resource
        Properties:
          RestApiId: !Ref BookApi
          ParentId: !GetAtt BookApi.RootResourceId
          PathPart: !Ref confirmPathPart

      ConfirmApiOptions:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: OPTIONS
          RestApiId: !Ref BookApi
          ResourceId: !Ref ConfirmApiResource
          AuthorizationType: NONE
          Integration:
            Type: MOCK
            IntegrationResponses:
              - StatusCode: "200"
                ResponseParameters:
                  method.response.header.Access-Control-Allow-Origin: "'*'"
                  method.response.header.Access-Control-Allow-Methods: "'OPTIONS,POST,GET,DELETE'"
                  method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      ConfirmApi:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: POST
          RestApiId: !Ref BookApi
          ResourceId: !Ref ConfirmApiResource
          AuthorizationType: NONE
          Integration:
            Type: AWS_PROXY
            IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
            Uri: !Sub >-
              arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${Confirm.Arn}/invocations
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      ConfirmApiInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          FunctionName: !Ref Confirm
          Action: lambda:InvokeFunction
          Principal: apigateway.amazonaws.com
          SourceAccount: !Ref "AWS::AccountId"
      ```
      ![DeployFunction](/images/temp/1/19.png?width=90pc)

3. Cấu trúc thư mục như dưới đây.
      ```
      fcj-book-shop-sam-ws3
      ├── fcj-book-shop
      │   ├── register
      │   │   └── register.py
      │   ├── confirm_user
      │   │   └── confirm_user.py
      │   ├── ...
      │
      └── template.yaml
      ```
    - Tạo thư mục **confirm_user** trong thư mục **fcj-book-shop-sam-ws3/fcj-book-shop/**.
    - Tạo tệp **confirm_user.py** và sao chép đoạn code sau vào nó.
      ```
      import json
      import boto3
      import os
      import hmac
      import hashlib
      import base64

      # Initialize the Cognito client
      client = boto3.client("cognito-idp")

      headers = {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
          "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token"
      }


      def lambda_handler(event, context):
          # Get the body from the event and parse it as JSON
          body = json.loads(event["body"])

          # Extract the necessary information from the event
          username = body["username"]
          confirmation_code = body["confirmation_code"]

          # Get Client ID and Client Secret from environment variables
          client_id = os.environ["CLIENT_ID"]
          client_secret = os.environ["CLIENT_SECRET"]

          # Generate the secret hash
          message = bytes(username + client_id, "utf-8")
          key = bytes(client_secret, "utf-8")
          secret_hash = base64.b64encode(
              hmac.new(key, message, digestmod=hashlib.sha256).digest()).decode()

          try:
              # Confirm the user
              response = client.confirm_sign_up(
                  ClientId=client_id,
                  SecretHash=secret_hash,
                  Username=username,
                  ConfirmationCode=confirmation_code
              )

              return {
                  "statusCode": 200,
                  "headers": headers,
                  "body": json.dumps("User confirmed successfully")
              }

          except Exception as e:
              print(f"Error confirming user: {e}")
              raise Exception(f"Error confirming user: {e}")
      ```
      ![DeployFunction](/images/temp/1/20.png?width=90pc)

#### Tạo hàm Login
Tại tệp **template.yaml** trong nguồn của tệp **fcj-book-shop-sam-ws3.zip** đã tải xuống trong phần chuẩn bị.
1. Tạo tham số **Login**.
    - Sao chép và dán khối mã dưới đây như hình dưới.
      ```
      loginPathPart:
        Type: String
        Default: login
      ```
      ![DeployFunction](/images/temp/1/15.png?width=90pc)

2. Tạo hàm **Login**.
    - Sao chép và dán các khối mã dưới đây vào cuối tệp.
      ```
      Login:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/login
          Handler: login.lambda_handler
          Runtime: python3.11
          FunctionName: login
          Architectures:
            - x86_64
          Environment:
            Variables:
              CLIENT_ID: !Ref cognitoClientID
              CLIENT_SECRET: !Ref cognitoClientSecret

      LoginApiResource:
        Type: AWS::ApiGateway::Resource
        Properties:
          RestApiId: !Ref BookApi
          ParentId: !GetAtt BookApi.RootResourceId
          PathPart: !Ref loginPathPart

      LoginApiOptions:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: OPTIONS
          RestApiId: !Ref BookApi
          ResourceId: !Ref LoginApiResource
          AuthorizationType: NONE
          Integration:
            Type: MOCK
            IntegrationResponses:
              - StatusCode: "200"
                ResponseParameters:
                  method.response.header.Access-Control-Allow-Origin: "'*'"
                  method.response.header.Access-Control-Allow-Methods: "'OPTIONS,POST,GET,DELETE'"
                  method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      LoginApi:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: POST
          RestApiId: !Ref BookApi
          ResourceId: !Ref LoginApiResource
          AuthorizationType: NONE
          Integration:
            Type: AWS_PROXY
            IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
            Uri: !Sub >-
              arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${Login.Arn}/invocations
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      LoginApiInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          FunctionName: !Ref Login
          Action: lambda:InvokeFunction
          Principal: apigateway.amazonaws.com
          SourceAccount: !Ref "AWS::AccountId"
      ```
      ![DeployFunction](/images/temp/1/22.png?width=90pc)

3. Cấu trúc thư mục như dưới đây.
      ```
      fcj-book-shop-sam-ws3
      ├── fcj-book-shop
      │   ├── register
      │   │   └── register.py
      │   ├── confirm_user
      │   │   └── confirm_user.py
      │   ├── login
      │   │   └── login.py
      │   ├── ...
      │
      └── template.yaml
      ```
    - Tạo thư mục **login** trong thư mục **fcj-book-shop-sam-ws3/fcj-book-shop/**.
    - Tạo tệp **login.py** và sao chép đoạn code sau vào nó.
      ```
      import json
      import boto3
      import os
      import hmac
      import hashlib
      import base64

      client = boto3.client("cognito-idp")

      headers = {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
          "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token"
      }


      def lambda_handler(event, context):
          # Get the body from the event and parse it as JSON
          body = json.loads(event["body"])

          # Get the username and password from the body
          username = body["username"]
          password = body["password"]

          # Get Client ID and Client Secret from environment variables
          client_id = os.environ["CLIENT_ID"]
          client_secret = os.environ["CLIENT_SECRET"]

          # Generate the secret hash
          message = bytes(username + client_id, 'utf-8')
          key = bytes(client_secret, 'utf-8')
          secret_hash = base64.b64encode(
              hmac.new(key, message, digestmod=hashlib.sha256).digest()).decode()

          try:
              response = client.initiate_auth(
                  AuthFlow="USER_PASSWORD_AUTH",
                  AuthParameters={
                      "USERNAME": username,
                      "PASSWORD": password,
                      "SECRET_HASH": secret_hash
                  },
                  ClientId=client_id,
              )

              return {
                  "statusCode": 200,
                  "headers": headers,
                  "body": json.dumps({
                      "message": "Login successful",
                      "id_token": response["AuthenticationResult"]["IdToken"],
                      "access_token": response["AuthenticationResult"]["AccessToken"],
                      "refresh_token": response["AuthenticationResult"]["RefreshToken"]
                  })
              }

          except Exception as e:
              print(f"Error login: {e}")
              raise Exception(f"Error login: {e}")
      ```
      ![DeployFunction](/images/temp/1/23.png?width=90pc)

#### Cập nhật tài nguyên Stage và tạo bản Deployment mới
1. Bỏ chú thích và chỉnh sửa khối mã này.
    ```
    BookApiDeployment:
      Type: AWS::ApiGateway::Deployment
      Properties:
        RestApiId: !Ref BookApi
      DependsOn:
        - BookApiGet
        - BookApiCreate
        - BookApiDelete
        - RegisterApi
        - ConfirmApi
        - LoginApi

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```
    ![DeployFunction](/images/temp/1/24.png?width=90pc)

4. Chạy các lệnh dưới đây. Để mặc định.
    ```
    sam build
    sam validate
    sam deploy --guided
    ```
    ![DeployFunction](/images/temp/1/25.png?width=90pc)

Chúng tôi đã hoàn thành việc triển khai các API và chức năng Lambda.