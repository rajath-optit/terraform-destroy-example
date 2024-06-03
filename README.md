---

# Terraform Destroy Example

This repository demonstrates how to use Terraform with GitHub Actions to destroy AWS resources. The provided Terraform configuration creates an EC2 instance, and the GitHub Actions workflow allows for the destruction of these resources.

## Contents

- [Prerequisites](#prerequisites)
- [Terraform Configuration](#terraform-configuration)
- [GitHub Actions Workflow](#github-actions-workflow)
- [Usage](#usage)

## Prerequisites

Before using this example, ensure you have the following prerequisites installed:

- [Terraform](https://www.terraform.io/downloads.html)
- [AWS CLI](https://aws.amazon.com/cli/)

You'll also need an AWS account and appropriate IAM permissions to create and destroy resources.

## Terraform Configuration

### `main.tf`

The `main.tf` file contains the Terraform configuration for creating an EC2 instance in the AWS region specified.

### `variables.tf`

The `variables.tf` file defines input variables used in the Terraform configuration. Update these variables as needed.

### `terraform.tfvars`

The `terraform.tfvars` file contains values for the input variables. Update these values with your desired configuration.

## GitHub Actions Workflow

### `destroy.yml`

The `.github/workflows/destroy.yml` file defines a GitHub Actions workflow named "Destroy Terraform Resources." This workflow is triggered manually and prompts for input values (`application_name` and `aws_resource`). It then initializes Terraform and destroys the specified AWS resources using Terraform destroy.

## Usage

1. Clone this repository to your local machine.
2. Update the Terraform configuration files (`main.tf`, `variables.tf`, `terraform.tfvars`) as needed.
3. Commit your changes and push them to your GitHub repository.
4. Navigate to the Actions tab in your GitHub repository.
5. Select the "Destroy Terraform Resources" workflow.
6. Click on "Run workflow" and provide the required inputs (`application_name` and `aws_resource`).
7. Confirm the destruction of resources when prompted.

---

##main.tf

provider "aws" {
  region = "us-west-2"   # Change the region if needed
}

resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"   # Change the AMI ID as per your requirement
  instance_type = "t2.micro"

  tags = {
    Name = var.application_name   # Use the application_name variable for tagging
  }
}

##variables.tf
variable "application_name" {
  description = "The name of the application"   # Add a description for clarity
  type        = string   # Update the variable type if needed
}

variable "aws_resource" {
  description = "The AWS resource to manage"   # Add a description for clarity
  type        = string   # Update the variable type if needed
}

##terraform.tfvars
application_name = "example-app"   # Change this to the desired application name
aws_resource     = "example-resource"   # Change this to the desired AWS resource name

##.github/workflows/destroy.yml

name: Destroy Terraform Resources

on:
  workflow_dispatch:
    inputs:
      application_name:
        description: 'Application Name'
        required: true
        default: ''
      aws_resource:
        description: 'AWS Resource'
        required: true
        default: ''

jobs:
  destroy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v1
      with:
        terraform_version: 1.0.0

    - name: Terraform Init
      run: terraform init

    - name: Terraform Destroy
      env:
        TF_VAR_application_name: ${{ github.event.inputs.application_name }}
        TF_VAR_aws_resource: ${{ github.event.inputs.aws_resource }}
      run: terraform destroy -auto-approve


Navigate to the Actions tab in your GitHub repository.
Select the "Destroy Terraform Resources" workflow.
Click on "Run workflow".
Provide the required inputs (application_name and aws_resource) and click "Run workflow" to trigger the destruction of resources.
With these steps completed, your Terraform resources will be destroyed automatically when the workflow is triggered via GitHub Actions.
