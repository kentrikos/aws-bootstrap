### Example of terraform.tfvars for *application* account:


<br></br>
application\_aws\_account\_number = "[your application aws account number]"

<br></br>
operations\_aws\_account\_number = "[your operations aws account number]"

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
> reflects the environment [dev,test,int,prod]  
> used in the name of your infrastructure

<br></br>
product\_domain\_name = "[Your unique name]"
> unique name for your environment  
> used in the name of your infrastructure

<br></br>
region = "[your region]"
> where the infrastructure is deployed  
> used in the name of your infrastructure

<br></br>
azs = ["az1", "az2", "az3"]
> Availability zones for your environment.  See your VPC settings in the AWS console for the specifics in your account. 

<br></br>
vpc\_id = "[your VPC ID]"  
> The VPC where the EKS will be deployed

<br></br>
http\_proxy = ""
> Proxy information: **leave blank for application account**

<br></br>
http\_proxy\_port = 8080
> proxy port for your environment

<br></br>
k8s\_private\_subnets = ["subnet-for-az1", "subnet-for-az2", "subnet-for-az3"]
> **Private** subnets for your environment.  See your VPC settings in the AWS console for the specifics in your account.  
> **Subnets need to be listed in order of the AZ's above**

<br></br>
k8s\_public\_subnets = ["subnet-for-az1", "subnet-for-az2", "subnet-for-az3"]
> **Public** subnets for your environment.  See your VPC settings in the AWS console for the specifics in your account.  
> **Subnets need to be listed in order of the AZ's above**

<br></br>
k8s\_master\_instance\_type = "m4.large"
> Not important for EKS;  
> needed for backwards compatibility with KOPS.

<br></br>
k8s\_node\_instance\_type = "m4.large"
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

<br></br>
iam\_cross\_account\_role\_arn = "[your cross account arn:]"
> CrossAccount to be used / created to control Application EKS cluster from Transit accounts  
> arn:aws:iam::[application account]:role/KENTRIKOS\_[region]\_[domain name]\_[environment]\_CrossAccount