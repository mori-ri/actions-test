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

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    environment: production
    # These permissions are needed to interact with GitHub's OIDC Token endpoint.
    permissions:
      id-token: write
      contents: read
    steps:
      - name: Configure AWS credentials from IAM Role
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ env.AWS_ROLE_ARN }}
          aws-region: ${{ env.AWS_REGION }}

      - name: Run AWS Amplify List Jobs
        id: amplify
        run: |
          echo "Development Deployment"
          branch=${{github.event.pull_request.head.ref}}
          status=$(aws amplify list-jobs --app-id ${{env.amplifyId}} --branch-name $branch --query jobSummaries[0].status)
          echo "status=$status" >> $GITHUB_ENV
        shell: bash