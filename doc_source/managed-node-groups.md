# Simplify node lifecycle with managed node groups<a name="managed-node-groups"></a>

* Amazon EKS managed node groups
  * 💡AUTOMATE the provisioning & lifecycle management of nodes \(== Amazon EC2 instances\) -- for -- Amazon EKS Kubernetes clusters 💡
    * ❌== NOT need to SEPARATELY provision or register Amazon EC2 instances / provide compute capacity -- to -- run your Kubernetes applications ❌
    * == enable -- via 1! operation -- 
      * create                      nodes -- for - your cluster
      * automatically update,       nodes -- for - your cluster /
        * BEFORE drain the nodes' workloads
      * terminate                   nodes -- for - your cluster /
        * BEFORE drain the nodes' workloads
    * 's nodes -- are provisioned as -- part of an Amazon EC2 Auto Scaling group /
      * managed for you -- by -- Amazon EKS
      * 👀ALL resources run | your AWS account 👀
  * 👀ALL nodes runs ACROSS MULTIPLE Availability Zones / you define👀

* | NEW or EXISTING clusters,
  * ways to add a managed node group
    * Amazon EKS console,
    * `eksctl`,
    * AWS CLI;
    * AWS API,
    * infrastructure as code tools 
    * AWS CloudFormation

* managed node group's nodes
  * 👀are AUTOMATICALLY tagged -- , by the [Kubernetes cluster autoscaler](autoscaling.md), for -- auto\-discovery👀

* costs
  * ❌NO additional costs❌
    * == ONLY pay -- for the -- AWS resources / you provision
  * ❌NO 
    * minimum fees
    * upfront commitments❌

## Managed node groups concepts<a name="managed-node-group-concepts"></a>

+ | create Managed node group
  + 's Auto Scaling group spans ALL subnet / you specify (TODO: ❓)

+ ⚠️if you are running a stateful application ACROSS MULTIPLE Availability Zones / backed by Amazon EBS volumes & using the Kubernetes [cluster Autoscaler](autoscaling.md) -> 
  + configure MULTIPLE node group / 1 / EACH Availability Zone
  + enable the `--balance-similar-node-groups` feature ⚠️
+ 👀| deploy managed nodes, recommended to use a [CUSTOM launch template](launch-templates.md) 👀
  + _Example:_ specify extra `kubelet` arguments & use a custom AMI
  + if | create a managed node group, you do NOT use a custom launch template -> EXIST auto\-generated launch template
    + NOT MANUALLY modify this auto\-generated template
      + Reason: 🧠 errors can occur🧠
+ TODO: Amazon EKS follows the shared responsibility model for CVEs and security patches on managed node groups\. When managed nodes run an Amazon EKS optimized AMI, Amazon EKS is responsible for building patched versions of the AMI when bugs or issues are reported\. We can publish a fix\. However, you're responsible for deploying these patched AMI versions to your managed node groups\. When managed nodes run a custom AMI, you're responsible for building patched versions of the AMI when bugs or issues are reported and then deploying the AMI\. For more information, see [Update a managed node group for your cluster](update-managed-node-group.md)\. 
+ Amazon EKS managed node groups can be launched in both public and private subnets\. If you launch a managed node group in a public subnet on or after April 22, 2020, the subnet must have `MapPublicIpOnLaunch` set to true for the instances to successfully join a cluster\. If the public subnet was created using `eksctl` or the [Amazon EKS vended AWS CloudFormation templates](creating-a-vpc.md) on or after March 26, 2020, then this setting is already set to true\. If the public subnets were created before March 26, 2020, you must change the setting manually\. For more information, see [Modifying the public `IPv4` addressing attribute for your subnet](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-ip-addressing.html#subnet-public-ip)\.
+ When deploying a managed node group in private subnets, you must ensure that it can access Amazon ECR for pulling container images\. You can do this by connecting a NAT gateway to the route table of the subnet or by adding the following [AWS PrivateLink VPC endpoints](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html#ecr-setting-up-vpc-create):
  + Amazon ECR API endpoint interface – `com.amazonaws.region-code.ecr.api`
  + Amazon ECR Docker registry API endpoint interface – `com.amazonaws.region-code.ecr.dkr`
  + Amazon S3 gateway endpoint – `com.amazonaws.region-code.s3`

  For other commonly\-used services and endpoints, see [Deploy private clusters with limited internet access](private-clusters.md)\.
+ Managed node groups can't be deployed on [AWS Outposts](eks-outposts.md) or in AWS Wavelength or AWS Local Zones\.
+ You can create multiple managed node groups within a single cluster\. For example, you can create one node group with the standard Amazon EKS optimized Amazon Linux AMI for some workloads and another with the GPU variant for workloads that require GPU support\.
+ If your managed node group encounters an [Amazon EC2 instance status check](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/monitoring-system-instance-status-check.html) failure, Amazon EKS returns an error code to help you to diagnose the issue\. For more information, see [Managed node group error codes](troubleshooting.md#troubleshoot-managed-node-groups)\.
+ Amazon EKS adds Kubernetes labels to managed node group instances\. These Amazon EKS provided labels are prefixed with `eks.amazonaws.com`\.
+ Amazon EKS automatically drains nodes using the Kubernetes API during terminations or updates\.
+ Pod disruption budgets aren't respected when terminating a node with `AZRebalance` or reducing the desired node count\. These actions try to evict Pods on the node\. But if it takes more than 15 minutes, the node is terminated regardless of whether all Pods on the node are terminated\. To extend the period until the node is terminated, add a lifecycle hook to the Auto Scaling group\. For more information, see [Add lifecycle hooks](https://docs.aws.amazon.com/autoscaling/ec2/userguide/adding-lifecycle-hooks.html) in the *Amazon EC2 Auto Scaling User Guide*\.
+ In order to run the drain process correctly after receiving a Spot interruption notification or a capacity rebalance notification, `CapacityRebalance` must be set to `true`\.
+ Updating managed node groups respects the Pod disruption budgets that you set for your Pods\. For more information, see [Understand each phase of node updates](managed-node-update-behavior.md)\.
+ There are no additional costs to use Amazon EKS managed node groups\. You only pay for the AWS resources that you provision\.
+ If you want to encrypt Amazon EBS volumes for your nodes, you can deploy the nodes using a launch template\. To deploy managed nodes with encrypted Amazon EBS volumes without using a launch template, encrypt all new Amazon EBS volumes created in your account\. For more information, see [Encryption by default](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EBSEncryption.html#encryption-by-default) in the *Amazon EC2 User Guide*\.

## Managed node group capacity types<a name="managed-node-group-capacity-types"></a>

When creating a managed node group, you can choose either the On\-Demand or Spot capacity type\. Amazon EKS deploys a managed node group with an Amazon EC2 Auto Scaling group that either contains only On\-Demand or only Amazon EC2 Spot Instances\. You can schedule Pods for fault tolerant applications to Spot managed node groups, and fault intolerant applications to On\-Demand node groups within a single Kubernetes cluster\. By default, a managed node group deploys On\-Demand Amazon EC2 instances\.

### On\-Demand<a name="managed-node-group-capacity-types-on-demand"></a>

With On\-Demand Instances, you pay for compute capacity by the second, with no long\-term commitments\. 

**How it works**

By default, if you don't specify a **Capacity Type**, the managed node group is provisioned with On\-Demand Instances\. A managed node group configures an Amazon EC2 Auto Scaling group on your behalf with the following settings applied:
+ The allocation strategy to provision On\-Demand capacity is set to `prioritized`\. Managed node groups use the order of instance types passed in the API to determine which instance type to use first when fulfilling On\-Demand capacity\. For example, you might specify three instance types in the following order: `c5.large`, `c4.large`, and `c3.large`\. When your On\-Demand Instances are launched, the managed node group fulfills On\-Demand capacity by starting with `c5.large`, then `c4.large`, and then `c3.large`\. For more information, see [Amazon EC2 Auto Scaling group](https://docs.aws.amazon.com/autoscaling/ec2/userguide/asg-purchase-options.html#asg-allocation-strategies) in the *Amazon EC2 Auto Scaling User Guide*\.
+ Amazon EKS adds the following Kubernetes label to all nodes in your managed node group that specifies the capacity type: `eks.amazonaws.com/capacityType: ON_DEMAND`\. You can use this label to schedule stateful or fault intolerant applications on On\-Demand nodes\. 

### Spot<a name="managed-node-group-capacity-types-spot"></a>

Amazon EC2 Spot Instances are spare Amazon EC2 capacity that offers steep discounts off of On\-Demand prices\. Amazon EC2 Spot Instances can be interrupted with a two\-minute interruption notice when EC2 needs the capacity back\. For more information, see [Spot Instances](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html) in the *Amazon EC2 User Guide*\. You can configure a managed node group with Amazon EC2 Spot Instances to optimize costs for the compute nodes running in your Amazon EKS cluster\.

**How it works**

To use Spot Instances inside a managed node group, create a managed node group by setting the capacity type as `spot`\. A managed node group configures an Amazon EC2 Auto Scaling group on your behalf with the following Spot best practices applied:
+ To ensure that your Spot nodes are provisioned in the optimal Spot capacity pools, the allocation strategy is set to one of the following:
  + `price-capacity-optimized` \(PCO\) – When creating new node groups in a cluster with Kubernetes version `1.28` or higher, the allocation strategy is set to `price-capacity-optimized`\. However, the allocation strategy won't be changed for node groups already created with `capacity-optimized` before Amazon EKS managed node groups started to support PCO\.
  + `capacity-optimized` \(CO\) – When creating new node groups in a cluster with Kubernetes version `1.27` or lower, the allocation strategy is set to `capacity-optimized`\.

  To increase the number of Spot capacity pools available for allocating capacity from, configure a managed node group to use multiple instance types\.
+ Amazon EC2 Spot Capacity Rebalancing is enabled so that Amazon EKS can gracefully drain and rebalance your Spot nodes to minimize application disruption when a Spot node is at elevated risk of interruption\. For more information, see [Amazon EC2 Auto Scaling Capacity Rebalancing ](https://docs.aws.amazon.com/autoscaling/ec2/userguide/capacity-rebalance.html) in the *Amazon EC2 Auto Scaling User Guide*\.
  + When a Spot node receives a rebalance recommendation, Amazon EKS automatically attempts to launch a new replacement Spot node\.
  + If a Spot two\-minute interruption notice arrives before the replacement Spot node is in a `Ready` state, Amazon EKS starts draining the Spot node that received the rebalance recommendation\. Amazon EKS drains the node on a best\-effort basis\. As a result, there's no guarantee that Amazon EKS will wait for the replacement node to join the cluster before draining the existing node\.
  + When a replacement Spot node is bootstrapped and in the `Ready` state on Kubernetes, Amazon EKS cordons and drains the Spot node that received the rebalance recommendation\. Cordoning the Spot node ensures that the service controller doesn't send any new requests to this Spot node\. It also removes it from its list of healthy, active Spot nodes\. Draining the Spot node ensures that running Pods are evicted gracefully\.
+ Amazon EKS adds the following Kubernetes label to all nodes in your managed node group that specifies the capacity type: `eks.amazonaws.com/capacityType: SPOT`\. You can use this label to schedule fault tolerant applications on Spot nodes\.

**Considerations for selecting a capacity type**

When deciding whether to deploy a node group with On\-Demand or Spot capacity, you should consider the following conditions:
+ Spot Instances are a good fit for stateless, fault\-tolerant, flexible applications\. These include batch and machine learning training workloads, big data ETLs such as Apache Spark, queue processing applications, and stateless API endpoints\. Because Spot is spare Amazon EC2 capacity, which can change over time, we recommend that you use Spot capacity for interruption\-tolerant workloads\. More specifically, Spot capacity is suitable for workloads that can tolerate periods where the required capacity isn't available\. 
+ We recommend that you use On\-Demand for applications that are fault intolerant\. This includes cluster management tools such as monitoring and operational tools, deployments that require `StatefulSets`, and stateful applications, such as databases\.
+ To maximize the availability of your applications while using Spot Instances, we recommend that you configure a Spot managed node group to use multiple instance types\. We recommend applying the following rules when using multiple instance types:
  + Within a managed node group, if you're using the [Cluster Autoscaler](autoscaling.md), we recommend using a flexible set of instance types with the same amount of vCPU and memory resources\. This is to ensure that the nodes in your cluster scale as expected\. For example, if you need four vCPUs and eight GiB memory, use `c3.xlarge`, `c4.xlarge`, `c5.xlarge`, `c5d.xlarge`, `c5a.xlarge`, `c5n.xlarge`, or other similar instance types\.
  + To enhance application availability, we recommend deploying multiple Spot managed node groups\. For this, each group should use a flexible set of instance types that have the same vCPU and memory resources\. For example, if you need 4 vCPUs and 8 GiB memory, we recommend that you create one managed node group with `c3.xlarge`, `c4.xlarge`, `c5.xlarge`, `c5d.xlarge`, `c5a.xlarge`, `c5n.xlarge`, or other similar instance types, and a second managed node group with `m3.xlarge`, `m4.xlarge`, `m5.xlarge`, `m5d.xlarge`, `m5a.xlarge`, `m5n.xlarge` or other similar instance types\.
  + When deploying your node group with the Spot capacity type that's using a custom launch template, use the API to pass multiple instance types\. Don't pass a single instance type through the launch template\. For more information about deploying a node group using a launch template, see [Customize managed nodes with launch templates](launch-templates.md)\.