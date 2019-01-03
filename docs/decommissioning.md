# HOW TO REMOVE ENVIRONMENT?

This guide describes steps necessary to destoy basic infrastructure for Kubernetes-based environment
on a pair of AWS accounts (operations and application).


## STEPS:

These steps should be done in the given order to ensure proper decomissioning.

In case you clean-up incomplete deployment please skip irrelevant steps.


1. Remove Kubernetes (K8s) cluster on application account:

* [JenkinsX] run job for cluster destroy
* FIXME: currently there is no "destroy" job so "Kubernetes_Install_On_Application" job must be edited manually (see commented lines marked 'DESTROY')

2. Remove K8s cluster from operations account:

* [Jenkins Core-Infra] run job: "Kubernetes_Destroy"

3. Remove bootstrap (core-infra) Jenkins from operations account:

* SSH to TF BH as ec2-user and run the following commands:

  ```
  cd ~/terraform/NAME_OF_YOUR_CONFIGURATION_REPOSITORY/operations/AWS_REGION/jenkins-core-infra
  terraform plan -destroy -input=false -var-file=../terraform.tfvars -out=tfplan
  terraform apply -input=false tfplan
  ```

4. Destroy "bootstrap" CF stack on operations account:

* use AWS console/CloudFormation

5. Destroy "bootstrap" CF stack on application account:

* use AWS console/CloudFormation


## TROUBLESHOOTING:

1. Problem with state lock for Terraform resources:

* sample error message:

  ```
  Error: Error locking state: Error acquiring the state lock: ConditionalCheckFailedException: The conditional request failed
      status code: 400, request id: RBN5ECNCCTNB0JE4FR96LJNTS7VV4KQNSO5AEMVJF66Q9ASUAAJG
  Lock Info:
    ID:        cb43d95b-8e14-0d9d-9368-d937ac30de3b
    Path:      tf-state-bootstrap-/.../jenkins-core-infra/terraform.tfstate
    Operation: OperationTypeApply
    Who:       ec2-user@localhost.eu-central-1.compute.internal
    Version:   0.11.10
    Created:   2018-12-06 10:48:25.822692885 +0000 UTC
    Info:


  Terraform acquires a state lock to protect the state from being written
  by multiple users at the same time. Please resolve the issue above and try
  again. For most commands, you can disable locking with the "-lock=false"
  flag, but this is not recommended.
  ```

  * workaround: add `-lock=false` to your terraform command.

2. CloudFormation refuses to remove "bootstrap" stacks due to S3 bucket not being empty:

* workaround: manually remove all objects from S3 `tf-state-YOUR_BUCKET` (inluding all versions - e.g. switch to 'Versions/Show')

3. Failed `terraform destroy`: remove all related AWS resources manually (via AWS console/cli), remove corresponding TF and kops state buckets on S3
  (this assumes new installation must be started from scratch)
