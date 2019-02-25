# HOW TO START

This guide describes steps necessary to deploy basic infrastructure for Kubernetes-based environment
on a pair of AWS accounts (operations and application).

## PREREQUISITES

1. AWS accounts created (e.g. by requesting through internal IT procedures):

    * operations account (with VPC and at least 1 private subnet per Availability Zone)
    * application account (with VPC including 3 public and 3 private subnets and NAT Gateway(s))
    * please ensure at least /27 subnets everywhere (/26 recommended)
    * please use short domain-name in DHCP options set for your VPC (Kubernetes hostnames "ip-NNN-NNN-NNN-NNN.DOMAIN_NAME" cannot exceed 63 characters)
    * VPC peering between the 2 accounts (operations/advanced, including DNS resolution)
    * currently only scenario where operations account uses HTTP proxy is supported
    * access to both accounts with AWS console or aws-cli with highly-administrative roles

2. git and ssh clients on your laptop/workstation

3. Access to Kentrikos project's public git repositories:

    ```shell
    git clone https://github.com/kentrikos/aws-bootstrap.git
    ```

    (altrernatively use web browser to download repo from GitHub)

4. Configuration repository (typically private as it contains environment-specific values such as AWS account numbers, VPC IDs, etc.):
    * e.g. located in your private GitHub repo or Bitbucket, please contact your admin
    * __private__ SSH key allowing read-only access

5. Route53 private hosted zone for a domain that will be used to access services (e.g. Jenkins-X) via convenient names.
    * currently, only zones maintained within "operations" account are supported
    * please note Hosted Zone ID of your domain (using AWS console or awscli)

## NOTES

* some Jenkins jobs may require manual confirmation (e.g. for 'terraform apply' stage), please hover your mouse over the paused stage to see confirmation button
* steps marked "IAM_LIMITED_PERMISSIONS" are in general optional and may be required only for environments with limited IAM permissions where creating IAM Policies using AWS API is not allowed.

## STEPS

1. (IAM_LIMITED_PERMISSIONS) If necessary, manually create base IAM Policies on both your operations and application account:

    * these policies define required permissions for Terraform bastion host, core-infra jenkins and cross-account role
    * use jsons from `aws-bootstrap/iam/kops/` to create the same set of policies on both AWs accounts (using either you internal procedures or directly with AWS console/IAM)
    * for policy names use filenames without `.json` (e.g. `KOPS_MANAGEMENT_NODE_ec2`) - please don't change policy names

2. Create SSH key pair on your "operations" account:

    * using AWS console (EC2 services/Key Pairs) create (or import existing) new SSH key pair

        > This key can be optionally used to ssh to the Terraform bastion host. Please note its name and make sure you have private key (e.g. ".pem" file) stored securely on your laptop.

3. Prepare you private configuration repository:

    * clone generic configuration repository from github.com/kentrikos/template-environment-configuration into your private repository
    * update Terraform configuration accordingly to top-level README.md file and comments in files listed

4. Create TF state resources, Terraform Bastion Host (TF BH) and (optionally) IAM Policies on operations account:

    * go to AWS console/CloudFormation service in AWS web console
    * use `aws-bootstrap/cfn/operations-account.yaml` template
    * name your stack in meaningful way (e.g. `demo-test-operations`, note that it will become prefix for EC2 instance name as well)
    * refer to field descriptions and set them accordingly
    * for "IAM_LIMITED_PERMISSIONS" environments set "AutoIAMMode" to false and adjust "IAMExistingManagedPoliciesPath" if necessary
    * to deploy Jenkins with TF automatically leave "AutoDeployCoreInfraJenkins" set to "true" and skip next step (5.)

5. (alternative, advanced step) Deploy core-infra Jenkins manually from TF BH:

    * login to BH via SSH
    * ensure your private configuration repository is cloned there already
    * execute terraform (use values appopriate for your deployment in `export` command):

      ```shell
      export Region=AWS_REGION AccountId=AWS_ACCOUNT_OPERATIONS_NUMERICAL_ID ProductDomainName=YOUR_PRODUCT_DOMAIN EnvironmentType=YOUR_ENVIRONMENT_TYPE

      cd terraform/YOUR_REPO/operations/${Region}/jenkins-core-infra

      terraform init -input=false \
      -backend-config="region=${Region}" \
      -backend-config="bucket=tf-${AccountId}-ops-${Region}-${ProductDomainName}-${EnvironmentType}" \
      -backend-config="key=tf/tf-aws-product-domain-${ProductDomainName}-env-${EnvironmentType}/jenkins-core-infra/terraform.tfstate"
      -backend-config="dynamodb_table=tf-state-lock-bootstrap-${ProductDomainName}-${EnvironmentType}" \
      
      terraform plan -var-file="../terraform.tfvars" -out=tfplan -input=false
      terraform apply -input=false tfplan
      ```

6. Note TF outputs with URL, login and password for Jenkins dashboard (to be open in web browser):

    * if Jenkins was deployed automatically (step 5. skipped), currently you need to check system logs from TF BH in EC2 console (Actions/Instance Settings/Get System Log) or by ssh-ing to TF BH and running `terraform output`
    * otherwise look into TF outputs after running TF manually
    * in default configurations, Jenkins URL should be reachable via: <http://jenkins.YOUR_DOMAIN:8080>
    * FIXME: this should be improved/automated

7. Create TF state resources, cross-account role and (optionally) IAM Policies on application account

    * open CloudFormation service in AWS web console
    * use `aws-bootstrap/cfn/application-account.yaml` template
    * name your stack in meaningful way (e.g. `demo-test-application`)
    * provide operations account as a parameter (required to create trust relationship)
    * for "IAM_LIMITED_PERMISSIONS" environments set "AutoIAMMode" to false and adjust "IAMExistingManagedPoliciesPath" if necessary
    * refer to other field descriptions and set them accordingly
    * ensure ARN of cross-account role created matches variable(s) in your configuration repository

8. Create IAM Policies for Kubernetes cluster instances (masters and nodes) on both accounts:

    * run 2 jobs on core-infra Jenkins ("Generate_IAM_Policies_Operations" and "Generate_IAM_Policies_Application")
    * if you are not allowed to create policies directly it will just create json files for you that you can use in your internal procedures to have policies created (requires proper configuration entry in configuration repository)

9. Deploy Kubernetes cluster on operations account using core-infra Jenkins:

    * open web dashboard
    * FIXME: due to <https://github.com/jenkinsci/ssh-credentials-plugin/pull/33> you need to update credentials manually (go to Manage Jekins/Configure Credentials/Credentials/git/Update and enter new line at the end of private key - just hit Enter and press Save)
    * run "Kubernetes_Install" job

10. Deploy jx on K8s cluster in operations account (using core-infra Jenkins):

    * run "Generate_JX_Docker_Image" job 
    * run "Install_JX" job
    * note that it will create a DNS entry in Route53 automatically (wildcard alias to be used by all Jenkins-X components)

11. Manually configure jx after it is installed (using URL and credentials for web dashboard from previous step, FIXME: this should be automated):

    * configure HTTP proxy settings for plugins (Manage Jenkins/Manage Plugins/Advanced - please note `Server` is without `http://` and port and `No Proxy` is one entry per line)
    * go to `Available` plugins tab, update the list (`Check now`) and install (without restart) the following plugins: `SSH Agent`, `AWS Parameter Store Build Wrapper`
    * add private SSH key for accessing your configuration repository (from Jenkins main dashboard: 'Credentials/System/Global/Add Credentials/SSH Username with private key', Username: `git`, ID: `bitbucket-key`, enter key directly with 1 empty line at the end)

12. Deploy Kubernetes cluster on "application" account:

    * run jx job  "Install_Kubernetes"
