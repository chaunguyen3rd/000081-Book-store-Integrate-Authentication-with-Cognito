---
title : "Clean up"
date :  2025-02-11
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
1. Empty S3 bucket.
    - Open [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
    - Select **fcj-book-shop-by-myself**.
    - Click **Empty**.
    - Enter **permanently delete**.
    - Click **Empty**.
    - Do the same for bucket starting with **aws-sam-cli-managed-default-**.

2. Delete CloudFormation stacks.
    - Execute the below command to delete the AWS SAM application.

      ```bash
      sam delete --stack-name fcj-book-store
      sam delete --stack-name aws-sam-cli-managed-default
      ```

    - If you have issues when deleting with command. Open [AWS Cloudformation console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/getting-started). Then, delete all stacks related to this workshop.

3. Open [Amazon Cognito console](https://us-east-1.console.aws.amazon.com/cognito/v2/home?region=us-east-1).
    - Select **User pools** on the left menu.
    - Choose **User pool - ...** that just created before.
    - Click the **Delete** button.
      ![CreateUserPool](/images/temp/1/38.png?width=90pc)
    - At the **Delete user pool "User pool - ..."?** popup.
      - Check on the **Delete Cognito domain ...** and **Deactivate deletion protection**.
      - Enter the `User pool - ...` at the **2. To confirm deletion** field.
      - Click the **Delete** button.
        ![CreateUserPool](/images/temp/1/39.png?width=90pc)
