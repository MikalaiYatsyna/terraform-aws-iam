# Cloud Formation stacks for account bootstrapping <!-- {docsify-ignore} -->

In order to enable GitHub Actions to create resources in AWS, it's essential to set up AWS Authentication. The preferred method involves configuring an OIDC provider, which leverages OIDC to acquire the short-lived credentials necessary for performing actions.

To establish the OIDC provider, IAM Role, and IAM Policy, CloudFormation stacks have been defined.

## Templates

The following CloudFormation templates are available:

1. `templates/terraform-state.yaml`: This template sets up a S3 Bucket and DynamoDB table for terraform state storing and locking.
2. `templates/github.yaml`: This template sets up an OIDC provider, IAM Role and IAM Policy with Required policy set of permissions for GitHub authentication.
3. `templates/ecr-registry.yaml`: This template sets up a private ECR registry for storing Docker images and Helm charts.

## Usage

Ensure that you review and customize the templates according to your specific requirements before deployment.

To use these templates, follow these steps:

1. Create and configure Cloud Formation stacks with Git Sync([AWS docs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/git-sync.html)). 
2. Use Following policy to create Cloud Formation Git Sync role.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "cloudformation:CreateChangeSet",
        "cloudformation:DeleteChangeSet",
        "cloudformation:DescribeChangeSet",
        "cloudformation:DescribeStackEvents",
        "cloudformation:ExecuteChangeSet",
        "cloudformation:ListChangeSets",
        "cloudformation:DescribeStacks"
      ],
      "Resource": "arn:aws:cloudformation:*:<account-id>:stack/*/*"
    },
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": [
        "cloudformation:CreateChangeSet"
      ],
      "Resource": "arn:aws:cloudformation:*:aws:transform/Include"
    },
    {
      "Effect": "Allow",
      "Action": "cloudformation:ValidateTemplate",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "events:PutRule",
        "events:PutTargets",
        "Events:DescribeRule"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "iam:CreateOpenIDConnectProvider",
        "iam:GetOpenIDConnectProvider",
        "iam:DeleteOpenIDConnectProvider",
        "iam:CreateRole",
        "iam:GetRole",
        "iam:DeleteRole",
        "iam:CreatePolicy",
        "iam:GetPolicy",
        "iam:DeletePolicy",
        "iam:DeleteRolePolicy",
        "iam:PutRolePolicy",
        "iam:GetRolePolicy"
      ],
      "Resource": "*"
    },
    {
      "Action": [
        "dynamodb:CreateTable",
        "dynamodb:DescribeTable",
        "dynamodb:DescribeContinuousBackups",
        "dynamodb:DescribeTimeToLive",
        "dynamodb:ListTagsOfResource",
        "dynamodb:DeleteTable",
        "dynamodb:TagResource",
        "dynamodb:UpdateContinuousBackups",
        "dynamodb:UpdateTimeToLive"
      ],
      "Resource": "arn:aws:dynamodb:*:<account-id>:table/*",
      "Effect": "Allow"
    },
    {
      "Action": [
        "kms:ListAliases",
        "kms:TagResource",
        "kms:CreateKey",
        "kms:EnableKeyRotation",
        "kms:DescribeKey",
        "kms:GetKeyPolicy",
        "kms:GetKeyRotationStatus",
        "kms:ListResourceTags",
        "kms:ScheduleKeyDeletion",
        "kms:CreateAlias",
        "kms:DeleteAlias"
      ],
      "Resource": "*",
      "Effect": "Allow"
    },
    {
      "Action": [
        "s3:CreateBucket",
        "s3:PutBucketPolicy",
        "s3:ListBucket",
        "s3:GetBucketPolicy",
        "s3:GetBucketAcl",
        "s3:GetBucketCors",
        "s3:GetBucketWebsite",
        "s3:GetBucketVersioning",
        "s3:GetAccelerateConfiguration",
        "s3:GetBucketRequestPayment",
        "s3:GetBucketLogging",
        "s3:GetLifecycleConfiguration",
        "s3:GetReplicationConfiguration",
        "s3:GetEncryptionConfiguration",
        "s3:GetBucketObjectLockConfiguration",
        "s3:GetBucketTagging",
        "s3:GetBucketPublicAccessBlock",
        "s3:PutEncryptionConfiguration",
        "s3:PutBucketVersioning",
        "s3:DeleteBucketPolicy",
        "s3:PutLifecycleConfiguration",
        "s3:PutBucketPublicAccessBlock",
        "s3:DeleteBucket",
        "s3:PutBucketTagging",
        "s3:PutBucketAcl",
        "s3:PutBucketOwnershipControls",
        "s3:GetBucketOwnershipControls"
      ],
      "Resource": "arn:aws:s3:::*",
      "Effect": "Allow"
    },
    {
      "Action": [
        "iam:GetPolicy",
        "iam:GetPolicyVersion",
        "iam:ListPolicyVersions",
        "iam:CreatePolicyVersion",
        "iam:DeletePolicyVersion",
        "iam:DeletePolicy",
        "iam:CreatePolicy",
        "iam:TagPolicy"
      ],
      "Resource": "arn:aws:iam::<account-id>:policy/*",
      "Effect": "Allow"
    },
    {
      "Effect": "Allow",
      "Action": [
        "ecr:CreateRepository",
        "ecr:DeleteRepository",
        "ecr:DescribeRepositories",
        "ecr:TagResource",
        "ecr:GetRepositoryPolicy",
        "ecr:SetRepositoryPolicy",
        "ecr:DeleteRepositoryPolicy",
        "ecr:ListTagsForResource",
        "ecr:PutLifecyclePolicy"
      ],
      "Resource": "arn:aws:ecr:*:<account-id>:repository/*"
    }
  ]
}
```
