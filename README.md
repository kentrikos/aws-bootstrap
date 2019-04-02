# Bootstrap for CloudHub-based environments

This repo servers as an entry-point for your Kubernetes cluster in AWS cloud.  It contains all needed instructions to give you a solid foundation to deploy your application.


_Two flavors are offered:_  
* EKS  
* KOPS

<br>
**EKS** is a kubernetes cluster where the 'management masters' are completly managed and operated by AWS.   
**KOPS** is a kubernetes cluster where all master and worker nodes are under your control and administration.

Both solutions offer the same compute power for your app on the workernodes.  EKS will take the operational part of your master nodes out of your hands.


Please follow one of the following instruction to set up your environment:  

####For EKS:    https://github.com/kentrikos/aws-bootstrap/blob/master/docs/EKS_README.md  

####For KOPS:   https://github.com/kentrikos/aws-bootstrap/blob/master/docs/KOPS_README.md  

