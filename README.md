# Restrict EC2 Launch Only through Clouformation

Let us say you want users the privilege to launch stack, but only through cloudformation. How do you achieve it. We can provide this type of authorization using the `aws:CalledVia` condition key. 

1. ## ⚙️ Prerequisites

    This demo, instructions, scripts and cloudformation template is designed to be run in `us-east-1`. With few modifications you can try it out in other regions as well(_Not covered here_).

    - AWS CLI pre-configured - [Get help here](https://youtu.be/TPyyfmQte0U)


1. ## 🚀 How does this work?

  The following policy does the trick for us. The `condition` key in the statement checks, how the service is being called and only `ALLOW`s the request if it is made _via_ cloudformation.


```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCFNConsoleActions",
            "Effect": "Allow",
            "Action": [
                "cloudformation:CreateUploadBucket",
                "s3:PutObject",
                "s3:ListBucket",
                "s3:GetObject",
                "s3:CreateBucket"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowCFNActions",
            "Effect": "Allow",
            "Action": [
                "cloudformation:ListStack*",
                "cloudformation:GetTemplateSummary",
                "cloudformation:CreateStack",
                "cloudformation:DescribeStacks",
                "cloudformation:DescribeStackEvents",
                "cloudformation:DescribeStackResources",
                "cloudformation:GetTemplate",
                "cloudformation:ValidateTemplate",
                "cloudformation:GetStackPolicy",
                "cloudformation:CreateChangeSet",
                "cloudformation:DeleteStack"
            ],
            "Resource": "*"
        },
        {
            "Sid": "AllowEC2ActionsFromCfn1",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateVolume"
            ],
            "Resource": [
                "arn:aws:ec2:*::image/*",
                "arn:aws:ec2:*::snapshot/*",
                "arn:aws:ec2:*:*:subnet/*",
                "arn:aws:ec2:*:*:network-interface/*",
                "arn:aws:ec2:*:*:security-group/*",
                "arn:aws:ec2:*:*:key-pair/*",
                "arn:aws:ec2:*:*:volume/*"
            ],
            "Condition": {
                "ForAnyValue:StringEquals": {
                    "aws:CalledVia": [
                        "cloudformation.amazonaws.com"
                    ]
                }
            }
        },
        {
            "Sid": "AllowEC2ActionsFromCfn2",
            "Effect": "Allow",
            "Action": [
                "ec2:CreateSecurityGroup",
                "ec2:DescribeSecurityGroups",
                "ec2:AuthorizeSecurityGroupIngress",
                "ec2:DeleteSecurityGroup",
                "ec2:DescribeInstances",
                "ec2:RunInstances",
                "ec2:TerminateInstances"
            ],
            "Resource": "*",
            "Condition": {
                "ForAnyValue:StringEquals": {
                    "aws:CalledVia": [
                        "cloudformation.amazonaws.com"
                    ]
                }
            }
        }
    ]
}
```

1. ## 🔬 Test the app

    - Create a new IAM User
    - Create a permission with the policy provided here `least-privileged-permissions.json`
    - Attach the policy IAM User
    - Launch the clouformation template
      - Should launch successfully
    - Try to launch an instance with GUI/CLI it should fail
    

1. ## 🧹 CleanUp

    If you want to destroy all the resources created by the stack, Execute the below command to delete the stack, or _you can delete the stack from console as well_. This is not an exhaustive list, please carry out other necessary steps as maybe applicable to your needs.

## 👋 Buy me a coffee

Buy me a coffee ☕ through [Paypal](https://paypal.me/valaxy), _or_ You can reach out to get more details through [here](https://youtube.com/c/valaxytechnologies/about).

### 📚 References

1. [Least Privilege Permissions](https://aws.amazon.com/blogs/security/how-to-define-least-privileged-permissions-for-actions-called-by-aws-services/)



### ℹ️ Metadata

**Level**: 200
