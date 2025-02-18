---
title : "Create API and Lambda function"
date :  2025-02-11
weight : 3
chapter : false
pre : " <b> 3. </b> "
---
After creating the User pool, we create an API and a Lambda function to handle user registration and login requests.

#### Preparation

1. Add needed parameters for function to be executed.
    - Copy the below code blocks to **template.yaml** file in source of **fcj-book-shop-sam-ws3.zip** file that downloaded in preparation.

      ```yml
      cognitoClientID:
        Type: String
        Default: APP_CLIENT_ID

      cognitoClientSecret:
        Type: String
        Default: APP_CLIENT_SECRET
      ```

    - Change **APP_CLIENT_ID** and **APP_CLIENT_SECRET** to the Cognito app clients recorded value before.
    ![DeployFunction](/images/temp/1/14.png?width=90pc)

2. Create a new ``sam`` deployment.
    - Open **template.yaml** file in source of **fcj-book-shop-sam-ws3.zip** file that downloaded in preparation.
    - Comment the code blocks as below.
      ![DeployFunction](/images/temp/1/12.png?width=90pc)
    - Run the below commands.

      ```bash
      sam build
      sam validate
      sam deploy
      ```

      ![DeployFunction](/images/temp/1/13.png?width=90pc)

#### Create Registration function

At **template.yaml** file in source of **fcj-book-shop-sam-ws3.zip** file that downloaded in preparation.

1. Create **Registration** parameter.
    - Copy and paste the below code block as image below.

      ```yml
      registerPathPart:
        Type: String
        Default: register
      ```

      ![DeployFunction](/images/temp/1/21.png?width=90pc)

2. Create **Registration** function.
    - Copy and paste the below code blocks to the bottom of the file.

      ```yml
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

3. The directory structure is as below.

      ```txt
      fcj-book-shop-sam-ws3
      ├── fcj-book-shop
      │   ├── register
      │   │   └── register.py
      │   ├── ...
      │
      └── template.yaml
      ```

    - Create **register** folder in **fcj-book-shop-sam-ws3/fcj-book-shop/** folder.
    - Create **register.py** file and copy the following code to it.

      ```py
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

#### Create Confirm function

At **template.yaml** file in source of **fcj-book-shop-sam-ws3.zip** file that downloaded in preparation.

1. Create **Confirm** parameter.
    - Copy and paste the below code block as image below.

      ```yml
      confirmPathPart:
        Type: String
        Default: confirm_user
      ```

      ![DeployFunction](/images/temp/1/18.png?width=90pc)

2. Create **Confirm** function.
    - Copy and paste the below code blocks to the bottom of the file.

      ```yml
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

3. The directory structure is as below.

      ```txt
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

    - Create **confirm_user** folder in **fcj-book-shop-sam-ws3/fcj-book-shop/** folder.
    - Create **confirm_user.py** file and copy the following code to it.

      ```py
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

#### Create Login function

At **template.yaml** file in source of **fcj-book-shop-sam-ws3.zip** file that downloaded in preparation.

1. Create **Login** parameter.
    - Copy and paste the below code block as image below.

      ```yml
      loginPathPart:
        Type: String
        Default: login
      ```

      ![DeployFunction](/images/temp/1/15.png?width=90pc)

2. Create **Login** function.
    - Copy and paste the below code blocks to the bottom of the file.

      ```yml
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

3. The directory structure is as below.

      ```txt
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

    - Create **login** folder in **fcj-book-shop-sam-ws3/fcj-book-shop/** folder.
    - Create **login.py** file and copy the following code to it.

      ```py
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

#### Update Stage resource and create new Deployment

1. Uncomment and edit this code block.

    ```yml
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

2. Run the below commands. Leave as default.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![DeployFunction](/images/temp/1/25.png?width=90pc)

We have completed the implementation of the APIs and Lambda functions.
