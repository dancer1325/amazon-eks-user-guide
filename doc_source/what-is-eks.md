# What is Amazon EKS?<a name="what-is-eks"></a>

* Amazon EKS
  * := managed service
    * -> NO need to make by our own, about Kubernetes control plane
      * install,
      * operate,
      * maintain

## Features of Amazon EKS<a name="eks-features"></a>


**Secure networking and authentication**  
* your Kubernetes workloads -- are integrated with -- 
  * AWS [networking](eks-networking.md) &
  * security services
  * AWS IAM
    * -> provide [authentication](cluster-auth.md) -- for -- your Kubernetes clusters

**Easy cluster scaling**  
* types
  * [cluster autoscaling](autoscaling.md)
    * -- based on the -- demand of your workloads
  * [horizontal Pod autoscaling](horizontal-pod-autoscaler.md)
    * -- based on -- CPU or custom metrics

**Managed Kubernetes experience**  
* ways to make changes | your Kubernetes clusters
  * `[eksctl](https://eksctl.io/)`,
  * [AWS Management Console](https://console.aws.amazon.com/eks/),
  * [AWS CLI](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/eks/index.html),
  * [AWS EKS API](https://docs.aws.amazon.com/eks/latest/APIReference/Welcome.html),
  * [`kubectl`](install-kubectl.md),
  * [Terraform](https://tf-eks-workshop.workshop.aws/)

**High availability**  
* [high availability](disaster-recovery-resiliency.md) -- for your -- control plane / ACROSS MULTIPLE Availability Zones

**Integration with AWS services**  
* allowed [AWS services](eks-integrations.md) / -- integrate -- with
* Kubernetes workloads -- can be observed, via -- [observability](eks-observe.md) tools

* see [Amazon EKS features](https://aws.amazon.com/eks/features)

## Get started with Amazon EKS<a name="how-eks-works"></a>

* TODO:
To create your first cluster and its associated resources, see [Get started with Amazon EKS](getting-started.md)\. In general, getting started with Amazon EKS involves the following steps\.

1. **Create a cluster** – Start by creating your cluster using `eksctl`, AWS Management Console, AWS CLI, or one of the AWS SDKs\.

1. **Choose your approach to compute resources** – Decide between AWS Fargate, Karpenter, managed node groups, and self\-managed nodes\.

1. **Setup** – Set up the necessary controllers, drivers, and services\. 

1. **Deploy workloads** – Tailor your Kubernetes workloads to best utilize the resources and capabilities of your chosen node type\.

1. **Management** – Oversee your workloads, integrating AWS services to streamline operations and enhance workload performance\. You can view information about your workloads using the AWS Management Console\.

The following diagram shows a basic flow of running Amazon EKS in the cloud\. To learn about other Kubernetes deployment options, see [Deploy Amazon EKS clusters across cloud and on\-premises environments](eks-deployment-options.md)\.

![\[\]](http://docs.aws.amazon.com/eks/latest/userguide/images/what-is-eks.png)

## Pricing for Amazon EKS<a name="eks-pricing"></a>

An Amazon EKS cluster consists of a control plane and the [Amazon Elastic Compute Cloud](https://aws.amazon.com/ec2/) \(Amazon EC2\) or Fargate compute that you run Pods on\. For more information about pricing for the control plane, see [Amazon EKS pricing](https://aws.amazon.com/eks/pricing)\. Both Amazon EC2 and Fargate provide:

**On\-Demand Instances**  
Pay for the instances that you use by the second, with no long\-term commitments or upfront payments\. For more information, see [Amazon EC2 On\-Demand Pricing](https://aws.amazon.com/ec2/pricing/on-demand/) and [AWS Fargate Pricing](https://aws.amazon.com/fargate/pricing/)\.

**Savings Plans**  
You can reduce your costs by making a commitment to a consistent amount of usage, in USD per hour, for a term of one or three years\. For more information, see [Pricing with Savings Plans](https://aws.amazon.com/savingsplans/pricing/)\. You can also use a hybrid pricing model\. For example, you can use Savings Plans to serve your regular traffic and scale up your cluster nodes with Spot instances to serve the peak demands\.