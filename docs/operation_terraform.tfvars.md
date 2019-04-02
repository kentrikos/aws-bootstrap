### Example of terraform.tfvars for *operations* account:


<br></br>
operations\_aws\_account\_number = "[your application aws account number]"

<br></br>
application\_aws\_account\_number = "[your operations aws account number]"

<br></br>
auto\_IAM\_mode = true  
> leave this set to 'true';  
> needed for backwards compatibility with KOPS.

<br></br>
auto\_IAM\_path = "/"
> do not change this;  
> needed for backwards compatibility with KOPS.

<br></br>
logs\_not\_resource = ["arn:aws:logs:*:*:ccc-*"]
> do not change this

<br></br>
environment\_type = "dev"
> reflects the environment [dev,test,int,prod];  
> used in the name of your infrastructure.

<br></br>
product\_domain\_name = "[Your unique name]"
> unique name for your environment;  
> used in the name of your infrastructure.

<br></br>
region = "[your region]"
> where the infrastructure is deployed;  
> used in the name of your infrastructure.

<br></br>
azs = ["az1", "az2", "az3"]
> Availability zones for your environment.  See your VPC settings in the AWS console for the specifics in your account. 

<br></br>
vpc\_id = "[your VPC ID]"  
> The VPC where the EKS will be deployed

<br></br>
http\_proxy = "[Your Proxy URL]"
> Proxy information

<br></br>
http\_proxy\_port = 8080
> proxy port for your environment

<br></br>
no\_proxy = "localhost,127.0.0.1,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16,.elb.us-east-1.amazonaws.com,.elb.amazonaws.com,169.254.169.254,.internal,.local"
> Destinations bypassing the Proxy

<br></br>
jenkins\_config\_repo\_url = "[your git repo]"  
> The URL to your cloned private repo for the environment;  
> e.g.: ssh:/git@git.bmwgroup.net:7999/[q#]/[git repo name].git;  
> bmwgroup.net may need to be substitutes by xxx.xxx.xxx.xxx. 

<br></br>
jenkins\_iam\_policy\_names\_prefix = "/"
> do not change this;  
> needed for backwards compatibility with KOPS.

<br></br>
jenkins\_subnet\_id = "[subnet]"  
> pick one of the private subnets to place your Jenkins instance

<br></br>
jenkins\_http\_allowed\_cidrs = ["10.0.0.0/8"]  
> allowing http access to your Jenkins

<br></br>
jenkins\_ssh\_allowed\_cidrs = ["10.0.0.0/8"]  
> allowing ssh access to your Jenkins

<br></br>
jenkins\_ec2\_instance\_type = "t3.medium"  
> Instance type Jenkins will be running on

<br></br>
jenkins\_dns\_domain\_hosted\_zone\_ID = "[Route53 hosted zone ID]"  
> Your route53 hosted zone ID to create an ALIAS to reach your Jenkins installation on

<br></br>
jenkins\_dns\_hostname = "[Jenkins DNS name]"  
> The DNS name your Jenkins will receive

<br></br>
k8s\_cluster\_name\_postfix = "k8s.local"  
> do not change this;  
> needed for backwards compatibility with KOPS.

<br></br>
k8s\_private\_subnets = ["subnet-for-az1", "subnet-for-az2", "subnet-for-az3"]
> **Private** subnets for your environment.  See your VPC settings in the AWS console for the specifics in your account.  
> **Subnets need to be listed in order of the AZ's above**

<br></br>
k8s\_master\_instance\_type = "m4.large"
> Not important for EKS, needed for backwards compatibility with KOPS

<br></br>
k8s\_node\_instance\_type = "t3.medium"
> Type of instance to be used for your nodes in the workergroup

<br></br>
k8s\_node\_count = "3"
> how many nodes will be spun up in your workergroup

<br></br>
k8s\_aws\_ssh\_keypair\_name = "[your ssh key]"
> Use an existing key-pair that you have access to.  
> Do not include '.pem'

<br></br>
k8s\_linux\_distro = "debian"
> Leave as is: solution has been tested with this distribution

<br></br>
k8s\_enable\_pod\_autoscaling = true
> Preparation for pod autoscaling (horizontal scaling): Toggle [ true | false ]   
> Recommend to set to true

<br></br>
k8s\_enable\_cluster\_autoscaling = true
> Preparation for EKS cluster autoscaling (vertical scaling): Toggle [ true | false ]  
> Recommend to set to true

<br></br>
k8s\_protect\_cluster\_from\_scale\_in = false
> Toggle [ true | false ] to protect the workergroup to scale in.  
> Recommend to set to 'false'

<br></br>
k8s\_install\_helm = true
> Prepare cluster for Helm  
> Recommend to set to true

<br></br>
k8s\_allowed\_worker\_ssh\_cidrs = []
> Additional CIDR's to allow ssh into the worker nodes
