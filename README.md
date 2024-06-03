replace:
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
