# Preparing an EKS cluster in both Transit and Application accounts:

## Prerequirements (*very* specific for EKS):
* Both accounts need to adhere to the 'organizations' account structure
* a transit VPC with three AZ's (and three private subnets) in transit (3 is recommended, 1 is bare minimum)
* an advanced VPC with six AZ's; with six subnets: 3x private and 3x public (3 is bare minimum)
* Each subnet needs at least to be a /26 
* an accessible and functional proxy in the transit account to provide internet access
* Access to a PRIVATE repository system to host your own environment-configuration specific for your environment


## Known Limitations:  

* There are limited amount of IP's available in Transit accounts.  This may affect the creation/scaling of cluster, loadbalancers etc.  Therefore limited amount of infrastructure should be deployed in transit, with minimal resource reservations (e.g. when creating an EKS cluster with m4.large instances, some 18 IP addresses per instance are automatically reserved).  If there is an IP shortage, your cluster may not come on-line or will not scale up.).
* Each EKS cluster takes about 12 minutes to build in either account.  This is an Amazon restriction.  Please keep that in mind when starting a build.
* AWS resources are currently unable to resolve on-prem repo servers by name, please use IP addresses instead.


## Lets get started:  

1. Clone Kentrikos template-environment-configuration repository  
	> This repo needs to be modified and will contain the information needed to build your EKS clusters  

	Sample code:  
	
	```
	cd ~
	mkdir test  
	cd test
	git clone https://github.com/kentrikos/aws-bootstrap.git
	git clone https://github.com/kentrikos/template-environment-configuration.git
	```

2. Go to the cloned template-environment-configuration repository and perform the following tasks:  

	* for both application and operation, create your own 'region' folder (e.g. us-east-1).  

		> If eu-central-1 is your region, then skip the copy part and modify the provided files.  

		> copy and paste the provided eu-central-1 folder and rename it for your own region.  Do this for both Application and Operation folders.  

	* modify the following files:  
		* application/$REGION/terraform.tfvars ([example found here](application_terraform.tfvars.md))   
		* operations/$REGION/terraform.tfvars([example found here](operation_terraform.tfvars.md))  
		* operations/$REGION/env-eks/jenkins/parameters.yaml (config for Jenkins installation)
		* operations/$REGION/env-eks/grafana/parameters.yaml (config for grafana installation)

3. Login to your private Bitbucket
4. Create a new repo (and copy the URL to this repo)
5. Upload the template-environment-configuration to your new Bitbucket repo, making sure the folder structure stays intact.
    > NOTE: As this repo now contains sensitive information please make sure that it is PRIVATE
6. Create an SSH keypair (public/private) on your local machine `ssh-keygen -t rsa -b 4096`
7. Add your newly created public ssh key to the access configuration of your private configuration repository: this will allow the deployment to pull this repo allowing it to build your infrastructure.
8. Open a browser, login to your Default/Transit AWS account.
9. Start the CloudFormation workflow from here: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=kentrikos-ops&templateURL=https://kentrikos.s3.eu-central-1.amazonaws.com/aws-bootstrap/cfn/operations-account.yaml)
10. Fill out the questions to fit your infrastructure
11. Click OK / Next 
    > Recommended: Add a tag named "RunTill" with a value in format YYYY-MM-DD (This allows the EC2 resource to run uninterupted by cleanup-bots till the set date)
12. Acknowledge that additional AWS resources are created and click OK
13. Now resources will be created on behalf of you.  By clicking 'refresh' in the CloudFormation console you can stay informed of the progress.  When the process is successfully completed, you will have:
	* running bastion ec2 instance. 
	* running jenkins ec2 instance (may take up to 5 minutes to appear in the EC2 list, after the bastion host is created)
  * URL to jenkins instance (running on port 8080) and credentials required to log into web UI (all printed as outputs by terraform)
14. In the same browser, switch to the Advanced/Applications account
15. Start the CloudFormation workflow from here: [Launch Stack](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=kentrikos-ops&templateURL=https://kentrikos.s3.eu-central-1.amazonaws.com/aws-bootstrap/cfn/application-account.yaml)
16. Fill out the questions to fit your infrastructure
17. Click OK / Next (no tags or other info is needed)
18. Acknowledge that additional resources are created and click OK
    Now resources will be created on behalve of you.  When clicking 'refresh' you can stay informed of the progress.  When the process is successfully completed, you will have:
	* 'crossaccount role' in application account (OK: nothing much to show for here, as this account is created in the back ground) 
19. Retrieve the Jenkins URL from step 14, and login to jenkins.  
20. Modify SSH key (imported from step 7) to include a carriage return at the very end of the certificate 
    > (known issue: <https://github.com/jenkinsci/ssh-credentials-plugin/pull/33>)
21. Browse to the 'Infrastructure" map and build "Install EKS in Application account".  Wait for successful completion.
22. In this same folder, build "Install EKS in Operations account".  Wait for successful completion.

### Your EKS clusters are now ready for use.

#####Next steps:
Deploy Grafana/Prometheus for monitoring purposes.

