# EKS with AKO Demo
This CloudFormation template will deploy an EKS cluster on AWS along with a bastion host to manage the environment. This template is for VMware internal use, and is not considered baked enough for customer demonstrations or distribution.

## Assumptions
 - You already have an Avi Controller deployed in the target VPC. Avi Controller parameters are required to build out initialization parameters.
 - The VPC that this template is deployed into has internet access. EKS nodes require connectivity to pull down appropriate container images, and access to the bastion host is via the internet over SSH.
 - To demo the Hackazon URL, you must configure a DNS listener on the Avi Controller and delegate the example.local domain. The virtual service of the DNS listener is required as input to the CloudFormation template.

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
Once the CloudFormation Deployment is complete, the final step is to run the initiatlization script on the bastion host. This script is pre-populated with the required parameters to deploy AKO to the newly deployed EKS cluster.

1. SSH into the bastion host. The public IP of the bastion host can be found in the outputs section of the CloudFormation deployment. This deployment uses the Amazon Linux AMI, so you will authenticate with the ec2-user user account. 
`ssh -i <your EC2 private key> ec2-user@<public IP>`
    ![Outputs](/images/outputs.png)
2. Switch to the root user. 
   `sudo su -`
3. Run the init.sh script `./init.sh`. This script will complete the configuration of AKO.
    ![Init](/images/init_script.png)

4. Now that AKO is built, deploy the example application. `kubectl apply -f hacknp.yaml`. The values of this application are currently hard coded to use https://hackazon.example.local. 
    ![Deploy App](/images/deploy_app.png)

## Demo
*Assignment of an Elastic IP (Public IP) for virtual services created by AKO is currently not supported. A Windows demo host is provisioned so that you can access the URL internally via the demo networks.*

1. RDP into the demo host. The public IP of the demo host can be found in the outputs section of the Cloudformation deployment. Credentials are avidemo // Avi123#@!
2. Disable Internet Explorer: Enhanced Security Configuration (ESC). This is only required the first time you launch the demo.
  - Click Start, then select Server Manager
    ![Server Manager](/images/server_manager.png)
  - Click Local Server on the lefthand side of Server Manager
    ![Local Server](/images/local_server.png)
  - Click the hyperlinked button to the right of IE ESC.
    ![ESC](/images/esc.png)
  - Disable for Administrators and Users.
  - Restart Internet Explorer if it is currently open.
3. Open Internet Explorer and navigate to https://hackazon.example.local
    ![Hackzaon](/images/hackazon.png)
