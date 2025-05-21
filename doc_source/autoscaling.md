# Scale cluster compute with Karpenter and Cluster Autoscaler<a name="autoscaling"></a>

* Autoscaling
  * == function /
    * AUTOMATICALLY scale -- , to meet changing demands, -- your resources out & in

* SUPPORTED autoscaling products
  * **Karpenter**
    * improve
      * application availability
      * cluster efficiency
    * if application load changes -> launches right-sized compute resources
      * == provision just\-in\-time NEW compute resources / meet workload's requirements 
      * _Example of workload's requirements:_ compute, storage, acceleration, & scheduling
    * valid | any Kubernetes cluster
    * see [https://karpenter.sh/docs/](https://karpenter.sh/docs/)
  * **Cluster Autoscaler**
    * if pods fail or are rescheduled | OTHER nodes -> AUTOMATICALLY adjusts the number of cluster's nodes
    * uses Auto Scaling groups
    * see [Cluster Autoscaler on AWS](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/cloudprovider/aws/README.md)