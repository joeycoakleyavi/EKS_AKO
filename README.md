# EKS with AKO Demo
This CloudFormation template will deploy an EKS cluster on AWS along with a bastion host to manage the environment. This template is for VMware internal use, and is not considered baked enough for customer demonstrations or distribution.

## Assumptions
 - You already have an Avi Controller deployed in the target VPC. Avi Controller parameters are required to build out initialization parameters.
 - The VPC that this template is deployed into has internet access. EKS nodes require connectivity to pull down appropriate container images, and access to the bastion host is via the internet over SSH.

## Caveats
 - When deploying this template, you must delegate the CloudFormation deployment to the "cfn_deploy" role. This is required because when an EKS cluster is created, the creator principle is who is granted administrative access. This workaround was put in place to facilitate testing to folks that may be unfamiliar with AWS and allows a deployment without needing to configure the aws cli.


## How to Deploy a CloudFormation Template
1. Login to the AWS Console. Ensure that you are in the correct region which contains your Avi Controller.
    ![Region Select](/images/select_region.png)
2. In the search bar, search for CloudFormation. It can also be found under Services > Management & Governance > CloudFormation.
    ![CloudFormation Search](/images/cloudformation_search.png)
3. At the top right, Select Create stack > With new resources. Upload main.yml as the template file. Click next.
    ![Create Stack Image Upload](/images/create_stack_template_upload.png)
4. Give the stack a name, then complete the Parameter section. Parameters should provide context or examples of what the intended responses should be. Once completed, click next.
    ![Stack Details](/images/stack_details.png)
5. **Important** In the Permissions section, ensure cfn_deploy is selected. Click next.
    ![CFN Permissions](/images/cfn_permissions.png)
6. In the final step, you must acknowledge that this template will create IAM resources. Click create stack.
    ![IAM Ack](/images/iam_acknowledge.png)
*Note: The CloudFormation deployment can take up to 20 minutes to complete.*

## Post-Deployment Steps
