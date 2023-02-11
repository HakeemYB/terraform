#Create AWS VPC using Terraform¶
Let's start with terraform. First, we need to create an AWS provider. It allows to interact with the many resources supported by AWS, such as VPC, EC2, EKS, and many others. You must configure the provider with the proper credentials before using it. The most common authentications methods:
AWS shared credentials/configuration files
Environment variables
Static credentials
EC2 instance metadata
Create AWS provider and give it a name terraform/0-provider.tf.

The next step is to create a virtual private cloud in AWS using the aws_vpc resource. There is one required field that you need to provide, which is the size of your network. 10.0.0.0/16 will give you approximately 65 thousand IP addresses. For your convenience, you can also give it a tag, for example, main. Let's name it terraform/1-vpc.tf.

# Create Internet Gateway AWS using Terraform¶
To provide internet access for your services, we need to have an internet gateway in our VPC. You need to attach it to the VPC that we just created. It will be used as a default route in public subnets. Give it a name terraform/2-igw.tf.

# Create private and public subnets in AWS using Terraform¶
Now, we need to create four subnets. To meet EKS requirements, we need to have two public and two private subnets in different availability zones. File name is terraform/3-subnets.tf.

# Create NAT Gateway in AWS using Terraform¶
It's time to create a NAT gateway. It is used in private subnets to allow services to connect to the internet. For NAT, we need to allocate public IP address first. Then we can use it in the aws_nat_gateway resource. The important part here, you need to place it in the public subnet. That subnet must have an internet gateway as a default route. Give it a name terraform/4-nat.tf.

By now, we have created subnets, internet gateway, and nat gateway. It's time to create routing tables and associate subnets with them. File name is terraform/5-routes.tf.

# Create EKS cluster using Terraform¶
Finally, we got to the EKS cluster. Kubernetes clusters managed by Amazon EKS make calls to other AWS services on your behalf to manage the resources that you use with the service. For example, EKS will create an autoscaling group for each instance group if you use managed nodes. Before you can create Amazon EKS clusters, you must create an IAM role with the AmazonEKSClusterPolicy. Let's name it terraform/6-eks.tf.

Next, we are going to create a single instance group for Kubernetes. Similar to the EKS cluster, it requires an IAM role as well.

# Create IAM OIDC provider EKS using Terraform¶
To manage permissions for your applications that you deploy in Kubernetes. You can either attach policies to Kubernetes nodes directly. In that case, every pod will get the same access to AWS resources. Or you can create OpenID connect provider, which will allow granting IAM permissions based on the service account used by the pod. File name is terraform/8-iam-oidc.tf.

I highly recommend testing the provider first before deploying the autoscaller. It can save you a lot of time. File name is terraform/9-iam-test.tf.

Now we can run terraform.


# terraform apply

To export Kubernetes context you can use aws eks ... command; just replace region and name of the cluster.


# aws eks --region us-east-1 update-kubeconfig --name demo

 To check connection to EKS cluster run the following command:


# kubectl get svc

