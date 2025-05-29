# AWS Lambda CI/CD Setup Guide

This document walks you through creating a new Git repository for an AWS Lambda function and configuring GitHub Actions to deploy your `main` branch automatically on every push.

---

## 📋 Prerequisites

* An AWS account with permissions to create IAM users, policies, and Lambda functions.
* A GitHub account and organization/repo where you’ll host your Lambda code.
* AWS CLI installed locally (for testing deployments).

---

## 1. Create your Git repository

1. On GitHub, create a new **empty** repository named e.g. `my-service-lambda`.
2. Clone it locally:

   ```bash
   git clone git@github.com:YOUR_ORG/my-service-lambda.git
   cd my-service-lambda
   ```
3. Add your Lambda code (e.g. `index.js` or `handler.py`) and `package.json` / `requirements.txt` as appropriate.
4. Push your initial commit:

   ```bash
   git add .
   git commit -m "chore: initial Lambda handler"
   git push origin main
   ```

---

## 2. Create an IAM user for CI/CD

1. In the AWS Console, go to **IAM → Users → Create user**.
2. Name the user `lambda-deployer` and select **Programmatic access**.
3. Attach an inline policy granting just enough permissions:

   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "lambda:UpdateFunctionCode",
           "lambda:UpdateFunctionConfiguration"
         ],
         "Resource": "arn:aws:lambda:*:ACCOUNT_ID:function:*"
       }
     ]
   }
   ```
4. Complete creation and **save** the **Access Key ID** & **Secret Access Key**.

---

## 3. Store AWS credentials in GitHub

1. In your repo on GitHub, go to **Settings → Secrets and variables → Actions**.
2. Click **New repository secret** and add:

   * `AWS_ACCESS_KEY_ID` → *your* Access Key ID
   * `AWS_SECRET_ACCESS_KEY` → *your* Secret Access Key

---

## 4. Add the GitHub Actions workflow

Create the directory and file:

```bash
mkdir -p .github/workflows
```

Create `.github/workflows/deploy-lambda.yml` with the following contents:

```yaml
name: Deploy Lambda on push to main

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      # 1) Checkout your code
      - uses: actions/checkout@v4

      # 2) Configure AWS credentials
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v3
        with:
          aws-region: us-east-2                # ← change to your region
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}

      # 3) Install dependencies
      - name: Install dependencies
        run: |
          # Node.js example:
          npm ci
          # Python example:
          # pip install -r requirements.txt

      # 4) Package your function
      - name: Zip Lambda function
        run: |
          zip -r function.zip .

      # 5) Deploy to AWS Lambda
      - name: Deploy to AWS Lambda
        run: |
          aws lambda update-function-code \
            --function-name YOUR_FUNCTION_NAME \
            --zip-file fileb://function.zip
```

> **Tip:** If you need to update environment variables, memory, or timeouts, add:
>
> ```bash
> aws lambda update-function-configuration \
>   --function-name YOUR_FUNCTION_NAME \
>   --environment "Variables={KEY1=VALUE1,KEY2=VALUE2}" \
>   --memory-size 512 \
>   --timeout 30
> ```

---

## 5. Verify & Use

1. Commit and push the workflow:

   ```bash
   git add .github/workflows/deploy-lambda.yml
   git commit -m "ci: add Lambda deploy workflow"
   git push origin main
   ```
2. In GitHub, under **Actions**, watch the **Deploy Lambda** workflow run.
3. On success, your Lambda function’s code is updated automatically.

---

## 6. Best Practices & Tips

* **Keep config as code**: Consider managing your function (aliases, versions, env vars) via CloudFormation or SAM.
* **Use OIDC**: For tighter security, replace static secrets with GitHub’s OIDC-based roles:
  [`aws-actions/configure-aws-credentials` OIDC docs](https://github.com/aws-actions/configure-aws-credentials#permissions)
* **Lock down permissions**: Grant the deployer user only the actions & resources it truly needs.
* **Test locally**: Use `aws lambda invoke --function-name ...` to sanity-check before pushing.

---

🎉 You’re all set! Now every push to `main` will automatically deploy your Lambda function—no more manual edits in the console.
