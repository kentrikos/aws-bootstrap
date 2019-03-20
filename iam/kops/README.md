# Static IAM policies for kops

Please create these policies on both operation and application AWS accounts. For policy names use filenames without `.json` (e.g. `KENTRIKOS_ec2_AWSregion_ProductDomain_Env`) and replace parts `AWSregion`, `ProductDomain` and `Env` with your values. Please see example below:
`KENTRIKOS_ec2_eu-central-1_demo_test`

# Notes:
* please skip creating "vpc" policy on operations account
