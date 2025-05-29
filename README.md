# Quickstart: Create & Deploy a New Lambda Repo

Welcome! This guide shows you how to spin up a new repository for an AWS Lambda function in our GitHub organization and get code auto‑deployed on every push to `main`.

---

## 1️⃣ From the Organization Home

1. Go to **github.com/YOUR\_ORG** and click **New** → **Choose a template**.
2. Select **`lambda-template`**.
3. Give your repo a name, e.g. `user-auth-function`, and click **Create repository from template**.

---

## 2️⃣ Update Your Deployment Workflow

1. In your new repo, open:

   ```
   .github/workflows/deploy.yml
   ```
2. Change the `function-name` input to match the **exact** AWS Lambda name you created:

   ```yaml
   jobs:
     deploy:
       uses: YOUR_ORG/.github/.github/workflows/deploy-lambda.yml@main
       with:
         function-name: user-auth-function    # ← YOUR LAMBDA NAME
         region: us-east-2                    # ← (optional) AWS region
   ```
3. Save your changes.

---

## 3️⃣ Add Your Code

1. In the repo root, add or edit your handler file:

   * **Node.js:** `index.js`
   * **Python:** `handler.py`
2. Install dependencies locally to verify (optional):

   ```bash
   npm ci           # or: pip install -r requirements.txt
   ```

---

## 4️⃣ Commit & Push

1. Stage all changes:

   ```bash
   git add .
   ```
2. Commit:

   ```bash
   git commit -m "feat: initial implementation of user-auth function"
   ```
3. Push to GitHub:

   ```bash
   git push origin main
   ```

---

## 5️⃣ Watch It Deploy

1. Go to **Actions** in your repo.
2. Click the **Deploy** workflow.
3. Confirm it ran successfully and updated your Lambda.

---

⚡ **That’s it!** Now every push to `main` automatically zips your code and updates the specified AWS Lambda function. If you run into any issues, check the workflow logs or reach out on Slack. Good luck! 🎉
