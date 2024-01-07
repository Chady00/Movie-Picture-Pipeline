## **Movie Picture Pipeline**

<sup>Exclusively composed by Chady Achraf (Chady00), this README bears the sole authorship of Chady himself. Only Chady00 contributed to the project development steps.</sup>

## Project Overview

**This project aims to showcase expertise in DevOps tools and CI/CD practices by automating the development workflows for a web application that serves as a catalog of Movie Picture movies**. The application consists of a frontend UI built with TypeScript and React, and a backend API built with Python and Flask. The goal is to accelerate the release cycle by implementing Continuous Integration (CI) and Continuous Deployment (CD) pipelines using GitHub Actions and deploying the applications to an existing Kubernetes cluster.

Tools used in this project:

*   **GitHub Actions**: Used for Continuous Integration and Continuous Deployment workflows.
*   **Tarraform**: IaC tool to develop the infrastructure, including eks clusters, vpc, and more.
*   **Kubernetes/EKS**: Container orchestration platform for deploying, managing, and scaling containerized applications.
*   **Docker**: Containerization tool for building and running application images.
*   **kubectl**: Kubernetes command-line tool for applying manifests.
*   **esLint**: adding static code analysis rules for identifying and fixing problems in Typescript/Javascript code.
*   **PyTest**: Python testing framework that simplifies the process of writing and executing unit tests.
*   **pipenv**: Python dependency management tool.
*   **nvm**: Node Version Manager for managing NodeJS versions.
*   **tfswitch**: Terraform version management tool.
*   **kustomize**: Kubernetes manifest customization tool.
*   **jq**: Command-line JSON processor.

The project is comprised of 2 application.

1\. A frontend UI built written in Typescript, using the React framework  
2\. A backend API written in Python using the Flask framework.

### Frontend

1.  A Continuous Integration workflow that:
    1.  Runs on `pull_requests` against the `main` branch,only when code in the frontend application changes.
    2.  Is able to be run on-demand (i.e. manually without needing to push code)
    3.  Runs the following jobs in parallel:
        1.  Runs a linting job that fails if the code doesn't adhere to eslint rules
        2.  Runs a test job that fails if the test suite doesn't pass
    4.  Runs a build job only if the lint and test jobs pass and successfully builds the application
2.  A Continuous Deployment workflow that:
    1.  Runs on `push` against the `main` branch, only when code in the frontend application changes.
    2.  Is able to be run on-demand (i.e. manually without needing to push code)
    3.  Runs the same lint/test jobs as the Continuous Integration workflow
    4.  Runs a build job only when the lint and test jobs pass
        1.  The built docker image should be tagged with the git sha
    5.  Runs a deploy job that applies the Kubernetes manifests to the provided cluster.
        1.  The manifest should deploy the newly created tagged image
        2.  The tag applied to the image should be the git SHA of the commit that triggered the build

### Backend

1.  A Continuous Integration workflow that:
    1.  Runs on `pull_requests` against the `main` branch,only when code in the frontend application changes.
    2.  Is able to be run on-demand (i.e. manually without needing to push code)
    3.  Runs the following jobs in parallel:
        1.  Runs a linting job that fails if the code doesn't adhere to eslint rules
        2.  Runs a test job that fails if the test suite doesn't pass
    4.  Runs a build job only if the lint and test jobs pass and successfully builds the application
2.  A Continuous Deployment workflow that:
    1.  Runs on `push` against the `main` branch, only when code in the frontend application changes.
    2.  Is able to be run on-demand (i.e. manually without needing to push code)
    3.  Runs the same lint/test jobs as the Continuous Integration workflow
    4.  Runs a build job only when the lint and test jobs pass
        1.  The built docker image should be tagged with the git sha
    5.  Runs a deploy job that applies the Kubernetes manifests to the provided cluster.
        1.  The manifest should deploy the newly created tagged image
        2.  The tag applied to the image should be the git SHA of the commit that triggered the build.

## Setting up Continuous Deployment environment

These steps are only applied after validating the CI process.

#### Step 1: Export AWS Credentials

I exported my AWS credentials from the Cloud Gateway. This is necessary for Terraform to interact with AWS on my behalf.

#### Step 2: Run Terraform Commands

I navigated to the **setup/terraform** directory and ran the following commands:

`cd setup/terraform` then `terraform apply`

![creating resources from terraform](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/d34ea228-557b-4b16-b6ea-ce6171d61496)


![done reousrces](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/17e8ea3f-87be-4b7c-bd37-d0ec58ec4b24)



#### Step 3: Generate AWS Access Keys for Github Actions

After the infrastructure was created, I generated AWS credentials for the IAM user account that Github Actions will use. Here are the steps:

*   Launched the Cloud Gateway and navigated to the IAM service.
*   Under users, I found the **github-action-user** user account.
*   Clicked the account and went to **Security Credentials**.
*   Under **Access keys**, I selected **Create access key**.
*   Selected **Application running outside AWS** and clicked **Next**, then **Create access key** to finish creating the keys.
*   On the last page, I made sure to copy/paste these keys for storing in Github Secrets.

#### Step 5: Add Github Action User to Kubernetes

Now that the cluster and AWS resources are created, I added the **github-action-user** IAM user ARN to the Kubernetes configuration. This allows the user to execute **kubectl** commands against the cluster. Here's how:

`cd setup` then  `./init.sh`

![init sh running ](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/85b0aa67-1c84-4a80-b5f9-1d827d95eed8)


This should add the necessary permissions to the IAM user information (github-action-user) allowing the user to access the cluster.

## Modifications:

### **Frontend package.json Modification**

In the **package.json** file of the Frontend directory, the following modification was made within the "scripts" object:

`"lint": "eslint --fix ."`

This addition to the "lint" script ensures that ESLint performs automatic fixes, such as removing extra spaces, when checking the code. **\*\*\* By default, ESLint would now fail if any linting issues are encountered**, and the script will attempt to fix them.

![After adding --fix to esLint](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/ff075126-d430-4d17-89be-1effc7408596)


### **Backend requirements.txt Addition**

In the Backend directory, a new file named **requirements.txt** was added. This file includes the necessary Python dependencies required for the project. The dependencies added are:

*   flake8
*   pytest
*   flask
*   flask\_cors

_**This file will be used to install these dependencies during the project's setup and configuration process.**_

### **GitHub Custom Actions Workflow (action.yml) Modifications**

In the GitHub Actions workflow file (**action.yml**), specific modifications were made to the properties of the **pyDepend** and **NodeJs** jobs:

For the **NodeJs and pyDepend** job, the following property was added:

`shell: bash`  
_**This ensures that the bash shell is used when executing any subsequent bash scripts in both workflows custom actions.**_

_**Note : both actions were added in a separate directory within the repository:**_ `_**actions/****_`

## **Additional Notes**

**Setting Git Config:**

`git config --global user.email "chadyachraf@outlook.com"`  
`git config --global user.name "Chady00"`  
`git remote set-url origin` [`<githubRepo>.git`](https://github.com/Chady00/Movie-Picture-Pipeline.git)  
**Exporting AWS Credentials:**

`export AWS_ACCESS_KEY_ID=<your_access_key>`  
`export AWS_SECRET_ACCESS_KEY=<your_secret_key>`  
`export AWS_SESSION_TOKEN=<your_session_token>`

**AWS EKS Cluster and VPN with Terraform:**

Run Terraform commands in **setup/terraform** directory, EKS cluster creation may take up to 9 minutes.

`cd setup/terraform`  
`terraform init`  
`terraform apply`

**Exported Terraform Outputs:**

`cd setup/terraform` then `terraform output`

**Updating Kubeconfig:**

`aws eks update-kubeconfig --region us-east-1 --name cluster`  
**Executing init.sh Script:**

`cd setup` then `./init.sh`

**Getting ECR Username/Password:**

`ecr_auth=$(aws ecr get-login-password --region us-east-1)  # However it's outdated, I used an ECR login action from the marketplace.`

 **GitHub Actions Workflow Considerations:**

*   Set triggering event to "workspace\_dispatch" to avoid unnecessary runs during testing.
*   Create a GitHub composite **action.yml** in the root to perform the initial steps for all workflows.

**React App Environment Variable (REACT\_APP\_MOVIE\_API\_URL):**

*   Used inside the React code with Axios to fetch from the backend.
*   Passed as a building argument to the Dockerfile.
*   Referenced in the code using **process.env.REACT\_APP\_MOVIE\_API\_URL**.

**Important Notes:**

*   Destroy infrastructure before stopping work to avoid unnecessary costs.
*   Make sure to export AWS credentials and secrets for GitHub Actions.
*   IAM user keys are used for GitHub Actions instead of the usual launch gateway secret and access keys.

Additional Screenshots of the development workflow are referenced:

![Lint and test jobs success](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/28d1244c-aab7-47a4-98e2-6c52200a802f)


![pipeliine ci backend](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/d77c268f-efff-4e17-94cf-3cf2bb7d83bc)


![pipeliine ci frontend](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/88b63a85-be93-4f84-a0d7-b4ebdf3c9e58)


![two ecr repositories-registries were created successfully ](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/9d8a053d-89cd-40ee-a132-e056e7196166)


![make sure to destroy all resources when done ](https://github.com/Chady00/Movie-Picture-Pipeline/assets/84717550/1f898bfd-b6dc-4db6-b0a8-bcf587548658)



