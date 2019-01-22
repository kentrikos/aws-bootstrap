# Naming convention:

## Top level names:
* if possible names for all other resources (e.g. s3 buckets or K8s cluster) should be computed from PRODUCT_DOMAIN_NAME and ENV_TYPE, this will allow
  for very simple input from user and also keep all resulting names predictable
* for S3 buckets operations and application account are abbreviated into ops and aps to save on bucket name's length, use full names in all other places

## S3 buckets/keys for Terraform state:
* bucket name: `tf-AWS_ACCOUNT_NUMBER-AWS_ACCOUNT_TYPE-AWS_REGION-PRODUCT_DOMAIN_NAME-ENVIRONMENT_TYPE` - e.g. `tf-123456789012-ops-eu-central-1-demo-test` (S3 bucket names are global across all AWS accounts/regions)
* key: `tf/tf-aws-product-domain-PRODUCT_DOMAIN_NAME-env-ENV_TYPE/SUBCOMPONENT/terraform.tfstate` - e.g. `tf/tf-aws-product-domain-demo-env-test/jenkins/terraform.tfstate` (for main set of subcomponents deployed by infrastructure module use "env" for SUBCOMPONENT)
* please keep in mind S3 bucket name length limit (63 characters) and that multiple TF states (for different infra components) are kept under the same bucket (separated by keys)

## S3 buckets/keys for kops state:
* bucket name: `kops-AWS_ACCOUNT_NUMBER-AWS_REGION-PRODUCT_DOMAIN_NAME-ENVIRONMENT_TYPE[-ops]` (e.g. `kops-123456789012-eu-central-1-demo-test` or `kops-123456789012-eu-central-1-demo-test-ops')

## DynamoDB state lock table:
* `tf-state-lock-bootstrap-PRODUCT_DOMAIN_NAME-ENVIRONMENT_TYPE` (since table names are not global)
* multiple TF states will be automatically locked using 1 table by using different items (named after S3 keys), so no need for separate tables for single account/region
* DynamoDB table name length limit = 255 characters which should be plenty

## AWS CLI PROFILES (optional):
* `project-AWS_ACCOUNT_NAME-AWS_ACCOUNT_NUMBER` - e.g.  `project-main-test-234567890123`
* awscli profiles are expected to be pre-configured on machine used interact with AWS using awscli
