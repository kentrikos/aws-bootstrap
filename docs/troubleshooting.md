# Description
Troubleshooting notes for Kentrikos deployments.

# Troubleshooting
Most common problems fall into 4 categories:

1. Wrong parameters to deployment scripts:

  * please double-check you've correctly passed required arguments
  * ensure you have clearly planned on which AWS account K8s clusters will be deployed and into which VPCs and subnets

2. Misconfigure AWS accounts/VPCs:

  * ensure all "PREREQUISITIES" in `how-to-start.md` are met

3. Wrong DNS/HTTP proxy configuration on AWS accounts (especially "operations" types):

  * this can prevent Internet connectivity which is required to download necessary K8s components from public repositories
  * to investigate connectivity on cluster instances ssh to private IPs (can be found in AWS console), username is `admin`, key can be found in `.ssh/` directory on Jenkins.
  Then test with `wget`, `docker ps` (there should be K8s services running) and also `docker run hello-world`
  * debugging K8s deployment on application is trickier as jobs are run in pods from jx (try `kubectl -n jx exec -it JENKINS_POD_NAME -- /bin/bash` from core-infra Jenkins)
  * ensure public DNS resolution works on "application" account (e.g. launch EC2 instance and make simple test such as `host github.com`)
  * similarly, ensure Internet connectivity on "operations" account (public DNS resolution is not required since HTTP proxy is used)
  * check connectivity between your target VPCs in both accounts (e.g. run `ping` between 2 EC2 instances)

4. Re-deploying over existing resources:
  * ensure your old deployments are completely removed (consult `decommissioning.md`)

# Other problems

1. Not sufficient permissions for AWS accounts:

  * check if you can switch management roles in aws console or with awscli and if they provide admin-level permissions

2. Misconfigured IAM policies:

  * for "legacy" mode (IAM policies created manually) - please double check that policy documents were generated for correct  cluster name and that correct policy names and json files were used when creating these policies
  Also ensure that lenght limits were not reached for IAM entities (policies, roles) due to too long cluster name prefix.
  Another reason for no K8s containers running on masters/nodes are missing awslogs permissions (indicated by messages in   `/var/log/daemon.log`) - please double check IAM policies for masters and nodes.
  * for "AutoIAMMode", ensure your management role has permissions to create IAM Policies

3. Bootstrap Jenkins not available immediately after deployment:

  * wait few minutes and retry (last step of deployment is restart which may take some time)
  * check Terraform output
  * ssh to jenkins EC2 instance and investigate status information and logs:
  
    ```shell
    sudo -i
    systemctl status jenkins
    less /var/log/cloud-init-output.log
    less /var/log/jenkins/jenkins.log
    ```

4. Jenkins job hang on "Should we continue" in "Console Output" mode:

   * switch to "Stage View", hover your mouse over blocking stage and click confirm button

5. After redeployment of core-infra Jenkins, kubectl's configuration file is missing and helm client is not initialized:

  * this may happen when only Jenkins was redeployed and can be recovered from if rest of the environment is intact
  * ssh to Jenkins instance and use kops and helm to restore missing configuration:
  
    ```
    sudo -i
    usermod -s /bin/bash jenkins
    su -l jenkins
    kops --state s3://KOPS_STATE_S3_BUCKET_HERE export kubecfg K8S_CLUSTER_NAME_HERE.k8s.local
    helm init --client-only
    ```

# How to request help  

When requesting help, please prepare/provide the following information:

* AWS account numbers used and names of corresponding management roles
* copy of your configuration repository (e.g. tarball/gzip/URL)
* full outputs from commands/Jenkins jobs run
