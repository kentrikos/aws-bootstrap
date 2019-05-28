# Description

Update guide notes for Kentrikos deployments.

## Steps

0. Check diff between your current version of [Kentrikos](https://github.com/kentrikos/aws-bootstrap) and desired 

    ````
    https://github.com/kentrikos/aws-bootstrap/compare/<YOUR VERSION>...<NEW VERSION>
    ````

    <https://github.com/kentrikos/aws-bootstrap/compare/0.3.0...0.4.1>
1. Check diff between your current version of config repo and template [repo](https://github.com/kentrikos/template-environment-configuration)

    ````
    https://github.com/kentrikos/template-environment-configuration/compare/<YOUR VERSION>...<NEW VERSION>
    ````

    <https://github.com/kentrikos/template-environment-configuration/compare/0.6.2...0.7.0>
2. Create all new files
3. Update existed .tf files
4. Update values in .tfvars files
5. Commit all updates and push to config repo
6. (OPTIONAL) If CloudFormation for operations account was changed, deploy it ([steps](#cfn-update) )
7. (OPTIONAL) If CloudFormation for application account was changed, deploy it([steps](#cfn-update) )
8. (OPTIONAL) If jenkins-core-infra was updated, deploy it ([steps](#jenkins-core-infra-update) )
9. (OPTIONAL) If operations folder was updated, deploy changes ([steps](#operations-update) )

## Updating procedures

### <a name="cfn-update"></a> CloudFormation update

1. Go to AWS Console to CloudFormation on your account and region
2. Select your stack and update select update 
3. Chose `Upload a template to Amazon S3` and select proper stack file from https://github.com/kentrikos/aws-bootstrap/cfn for your version
4. Update params if needed
5. Review changes before deployment
6. If Bastion Host is marked to recreate it means that during bootstrap will perform terraform update for jenkins master automatically. Prepare config repo before so [Jenkins core infra update](#jenkins-core-infra-update) could be skipped as will be performed automatically 

### <a name="jenkins-core-infra-update"></a> Jenkins core infra update

1. Update config repo.
2. Log in to bastion host
3. Go to terrafrom/\<reponame\>/operations/\<region\>/jenkins-core-infra
4. Pull newest code (git pull --rebase)
5. Remove .terraform dir
6. Init terraform

    ```bash
      terraform init -input=false \
                 -backend-config="region=${Region}" \
                 -backend-config="bucket=tf-${AccountId}-ops-${Region}-${ProductDomainName}-${EnvironmentType}" \
                 -backend-config="dynamodb_table=tf-state-lock-bootstrap-${ProductDomainName}-${EnvironmentType}" \
                 -backend-config="key=tf/tf-aws-product-domain-${ProductDomainName}-env-${EnvironmentType}/jenkins-core-infra/terraform.tfstate"
    ```

7. Plan terraform

    ```bash
      terraform plan -var-file="../terraform.tfvars" -out=tfplan -input=false
    ```

8. Review plan
9. Apply terraform planed

    ```bash
      terraform apply -input=false tfplan
    ```

### <a name="operations-update"></a> Operations Account update

1. Check change details to determine jobs needed to be run
2. Base on [job order](Jenkins-jobs-order.md) run operations account jobs

### <a name="operations-update"></a> Application Account update

1. Check change details to determine jobs needed to be run
2. Base on [job order](Jenkins-jobs-order.md) run application account jobs
