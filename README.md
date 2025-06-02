# MyMvcApp-Contact-Database-Application

## Overview

This project is an ASP.NET Core MVC CRUD application for managing contacts.  
This guide explains how to deploy the app to Azure using an ARM template and automate deployments with a GitHub Actions workflow.

---

## 1. Deploying to Azure with ARM Template

### Prerequisites

- An Azure subscription
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) installed
- A resource group (e.g., `GitHub-Copilot-Challenges`)
- This repository cloned locally

### Files

- `deploy.json` — ARM template defining Azure resources (App Service Plan, Web App)
- `deploy.parameters.json` — Parameters for the ARM template

### Steps

1. **Login to Azure:**
   ```sh
   az login
   ```

2. **Set your subscription (if needed):**
   ```sh
   az account set --subscription "<your-subscription-id>"
   ```

3. **Create the resource group (if it doesn't exist):**
   ```sh
   az group create --name GitHub-Copilot-Challenges --location westus2
   ```

4. **Deploy the ARM template:**
   ```sh
   az deployment group create \
     --resource-group GitHub-Copilot-Challenges \
     --template-file deploy.json \
     --parameters deploy.parameters.json
   ```

5. **Note the output URL** from the deployment.  
   You can now deploy your app code to the created Azure Web App.

---

## 2. GitHub Actions Workflow for CI/CD

You can automate build and deployment to Azure App Service using GitHub Actions.

### Example Workflow: `.github/workflows/azure-webapp.yml`

```yaml
name: Build and Deploy ASP.NET Core app to Azure Web App

on:
  push:
    branches:
      - master

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Set up .NET
      uses: actions/setup-dotnet@v4
      with:
        dotnet-version: '8.0.x'

    - name: Restore dependencies
      run: dotnet restore

    - name: Build
      run: dotnet build --configuration Release --no-restore

    - name: Publish
      run: dotnet publish