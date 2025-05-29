# Quickstart: Create & Deploy a New Lambda Repo

This guide shows you how to spin up a new repository for an AWS Lambda function in our GitHub organization and get code auto‑deployed on every push to `main`.

---

## 1️⃣ From the Organization Home

1. Go to **github.com/acs-lambda** and click **Create New Repository** → **Choose a template**.
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
       uses: acs-lambda/.github/.github/workflows/deploy-lambda.yml@main
       with:
         function-name: user-auth-function    # ← YOUR LAMBDA NAME
         region: us-east-2                    # ← (optional) AWS region
   ```
3. Save your changes.

---

## 3️⃣ Add Your Code

1. In the repo root, add or edit your lambda files and edit:

   * **Node.js:** `index.js`
   * **Python:** `handler.py`

---

## 4️⃣ Commit & Push to a Branch

1. Stage all changes:

   ```bash
   git add .
   ```
2. Commit:

   ```bash
   git commit -m "feat: initial implementation of user-auth function"
   ```
3. Push to a non-main branch in GitHub:

   ```bash
   git push
   ```

---

## 5️⃣ Merge with Main (PR)

1. You will need to create a pull request to do so.

---

⚡ **That’s it!** Now every push to `main` automatically zips your code and updates the specified AWS Lambda function. If you run into any issues, check the workflow logs or reach out on Slack. Good luck! 🎉
