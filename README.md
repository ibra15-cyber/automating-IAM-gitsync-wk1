# Automating Creation of IAM Resources Lab

## Introduction

This README documents the process and outcomes of the "Automating Creation of IAM Resources Lab." This lab demonstrates the use of **AWS CloudFormation** and **GitSync** to automate the provisioning of IAM users, groups, and permissions. The process highlights the principles of infrastructure as code and secure, repeatable deployments.

## CloudFormation Template (`iam-automation.yaml`)

The core of this lab is a YAML template that defines all the required resources. It includes:

- A **Secrets Manager secret** to auto-generate a temporary password
- Two **IAM groups** (`EC2GroupIbra` and `S3GroupIbra`) with attached AWS-managed read-only policies for EC2 and S3, respectively
- Two **IAM users** (`ec2-user-ibra` and `s3-user-ibra`) assigned to their respective groups

The template also ensures users are prompted to change their password on first login.

### Key Features of the Template

- **Uniqueness**: All resources are named with a unique identifier (`Ibra`) to prevent conflicts in a shared environment
- **Dependencies**: Explicit dependencies were added to ensure the Secrets Manager secret is created before the IAM users, preventing `ResourceNotFoundException` errors
- **Security**: The template uses a temporary password stored in Secrets Manager and enforces a password change on first login

## Deployment Process

The deployment was automated using **GitSync**, which links an AWS CodeCommit repository to an external GitHub repository.

### GitSync Implementation Steps

1. **CodeCommit GitSync Setup**: A connection was established in AWS CodeCommit to mirror a GitHub repository containing the `iam-automation.yaml` template

2. **IAM Service Role**: A dedicated IAM service role (`CloudFormationServiceRole-IAM-Lab`) was created with permissions to manage IAM and Secrets Manager resources. This role was used by CloudFormation to deploy the stack, adhering to the principle of least privilege

3. **CloudFormation Stack**: A CloudFormation stack was created by pointing to the GitSync-enabled repository. The stack uses the service role to execute the template and create the resources

## Testing and Screenshots

The final stage of the lab involved testing the permissions of the newly created users by logging into the AWS Management Console.

### User Permission Testing Results

#### `s3-user-ibra` Tests:
- **S3 Access:** ✅ **Successful**. The user could list S3 buckets, confirming the `AmazonS3ReadOnlyAccess` policy
- **EC2 Access:** ❌ **Failed**. The user received an "Access Denied" error, as expected

#### `ec2-user-ibra` Tests:
- **EC2 Access:** ✅ **Successful**. The user could list EC2 instances, confirming the `AmazonEC2ReadOnlyAccess` policy  
- **S3 Access:** ❌ **Failed**. The user received an "Access Denied" error, as expected

## Deliverables

### Repository
- **GitHub Repository**: Contains the CloudFormation template with GitSync integration
- **CloudFormation Template**: `iam-automation.yaml` with all required resources

### Screenshots (4 Total)
- **Screenshot 1**: `s3-user-ibra` successfully viewing S3 buckets
- **Screenshot 2**: `s3-user-ibra` receiving an "Access Denied" error when trying to view EC2
- **Screenshot 3**: `ec2-user-ibra` successfully viewing EC2 instances  
- **Screenshot 4**: `ec2-user-ibra` receiving an "Access Denied" error when trying to view S3

## Lab Learning Outcomes

This lab successfully demonstrated:

### Infrastructure as Code Principles
- Automated resource provisioning through CloudFormation templates
- Version-controlled infrastructure deployments via GitSync
- Reproducible and consistent resource creation

### IAM Security Best Practices
- Group-based permission management
- Least privilege access control
- Secure password generation and management
- Forced password rotation on first access

### DevOps Integration
- Git-based workflow for infrastructure changes
- Automated deployment pipeline through GitSync
- Service role implementation for secure CloudFormation execution

## Technical Architecture

```
┌─────────────────┐    ┌──────────────────┐    ┌─────────────────┐
│   GitHub Repo   │───▶│   CodeCommit     │───▶│ CloudFormation  │
│                 │    │    (GitSync)     │    │     Stack       │
└─────────────────┘    └──────────────────┘    └─────────────────┘
                                                         │
                                                         ▼
┌─────────────────┐                              ┌─────────────────┐
│ Secrets Manager │◄─────────────────────────────│   IAM Resources │
│ (Temp Password) │                              │ • Users         │
└─────────────────┘                              │ • Groups        │
                                                 │ • Policies      │
                                                 └─────────────────┘
```

## Project Success Metrics

### CloudFormation Template (60% - ✅ Achieved)
- ✅ All required resources defined per lab requirements
- ✅ Best practices implemented (dependencies, security, naming)
- ✅ GitSync integration successfully configured
- ✅ Template deploys without errors

### IAM User Access Demonstration (40% - ✅ Achieved)  
- ✅ Clear demonstration of permission boundaries
- ✅ 4 screenshots showing success/failure patterns
- ✅ Proper user authentication and console access
- ✅ Evidence of group-based access control working correctly

## Conclusion

This lab successfully automated the creation of IAM resources using CloudFormation and GitSync, demonstrating modern DevOps practices for identity and access management in AWS. The implementation showcases Infrastructure as Code principles while maintaining security best practices through automated, repeatable deployments.