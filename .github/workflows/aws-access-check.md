# AWS access check

## on:
  push:
  workflow_dispatch:

## env:

- common
  - AWS_REGION: ap-northeast-1 # set this to your preferred AWS region, e.g. us-west-1
- development
- staging
- production
  - AWS_ROLE_ARN: arn:aws:iam::${{ secrets.AWS_ACCOUNT_ID }}:github-actions-amplify/ # GitHub　Actions用に作成したIAMロールのARN
  - AMPLIFY_APP_ID: ${{ secrets.AMPLIFY_APP_ID }} # AmplifyのアプリケーションID
