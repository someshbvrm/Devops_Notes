
---

# Terraform Apply Workflow

This repository contains a GitHub Actions workflow for automatically applying Terraform configurations to provision and manage infrastructure on AWS.

## Overview

The `terraform-apply.yaml` workflow provides a secure, automated way to execute `terraform apply` commands. It is manually triggered and handles AWS authentication, Terraform initialization, and infrastructure deployment.

## Workflow Details

### Trigger
- **Manual Execution**: The workflow must be manually triggered from the GitHub Actions tab using the `workflow_dispatch` event.

### Jobs and Steps

1.  **Checkout Repository**: Clones your code into the runner environment.
2.  **Configure AWS Credentials**: Securely authenticates with AWS using secrets stored in GitHub.
3.  **Terraform Init**: Initializes the Terraform working directory, downloading providers and modules.
4.  **Terraform Apply**: Applies the Terraform configuration to create or modify infrastructure automatically.

## Prerequisites

Before using this workflow, ensure you have the following:

1.  **Terraform Code**: Your Terraform configuration files (`.tf`) must be present in the repository.
2.  **AWS Account**: An AWS account where the resources will be provisioned.
3.  **AWS IAM User**: An IAM user with programmatic access and the necessary permissions to create the resources defined in your Terraform code.
4.  **GitHub Secrets**: The required secrets must be stored in your GitHub repository settings.

## Setup Instructions

### 1. Configure AWS IAM User
Create an IAM user in your AWS account with the necessary permissions. Attach a policy (e.g., `AdministratorAccess` for full access, or a more granular custom policy) and generate an Access Key (Access Key ID and Secret Access Key).

### 2. Configure GitHub Secrets
Add the following secrets to your GitHub repository under **Settings > Secrets and variables > Actions**:

| Secret Name | Description | Example Value |
| :--- | :--- | :--- |
| `AWS_ACCESS_KEY_ID` | The Access Key ID of your IAM user. | `AKIAIOSFODNN7EXAMPLE` |
| `AWS_SECRET_ACCESS_KEY`| The Secret Access Key of your IAM user. | `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY` |
| `AWS_REGION` | (Optional) The AWS region to deploy to. Defaults to `eu-north-1`. | `us-east-1` |

### 3. Configure the Self-Hosted Runner
This workflow is configured to run on a self-hosted runner labeled `somesh`. Ensure your runner is installed, connected to this repository, and has the following software installed:

- **Terraform**: The CLI must be available on the runner's PATH.
- **AWS CLI** (Optional, but recommended for troubleshooting).
- **unzip**: Required by the `setup-terraform` action to extract the Terraform binary.

## Usage

### How to Run the Workflow

1.  Navigate to the **"Actions"** tab in your GitHub repository.
2.  Select the **"Terraform Apply"** workflow from the list on the left.
3.  Click the **"Run workflow"** button on the right side of the screen.
4.  (Optional) You can provide inputs if the workflow is configured to accept them.
5.  Click the green **"Run workflow"** button to start the execution.

### Monitoring Execution

- Click on the running workflow to see real-time logs for each step.
- The **"Terraform Apply"** step will show a detailed plan of what resources will be created, modified, or destroyed.

## Security Notes

- **Secrets**: AWS credentials are handled securely via GitHub Secrets and are not exposed in logs.
- **Permissions**: The IAM user should follow the principle of least privilege. Grant only the permissions necessary to create the required resources.
- **Runner Security**: Since this uses a self-hosted runner, ensure the runner's underlying machine is secure.

## Troubleshooting

Common issues and solutions:

- **"Error: Unable to locate executable file: unzip"**: Install `unzip` on the self-hosted runner: `sudo apt-get install -y unzip` (Ubuntu/Debian).
- **"Error: Failed to configure AWS credentials"**: Double-check the values of `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` secrets in GitHub.
- **Terraform Authentication Errors**: Ensure the IAM user associated with the credentials has the correct permissions.

---

**Disclaimer**: Use `terraform apply -auto-approve` with caution. Always verify the plan output in the workflow log before triggering the run, as it will automatically make changes to your infrastructure.
