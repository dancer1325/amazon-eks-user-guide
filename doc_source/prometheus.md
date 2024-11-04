# Monitor your cluster metrics with Prometheus<a name="prometheus"></a>

* goal
  * set up Prometheus as
    * managed option or
    * open source option
  * monitor Amazon EKS control plane metrics

* [https://prometheus.io/](https://prometheus.io/)
  * := monitoring & time series database / scrapes endpoints
  * allows
    * query,
    * aggregate,
    * store collected data
    * alerting & alert aggregation

* Amazon Managed Service for Prometheus
  * allows
    * making it easy to monitor
      * containerized applications
      * infrastructure at scale
    * querying your metrics and alert -- via -- open-source PromQL query language
    * using alert manager
      * == set up alerting rules / critical alerts
        * these critical alerts -- can be sent as -- notifications | an Amazon SNS topic
  * == fully-managed service / automatically scales the
    * ingestion,
    * storage,
    * querying,
    * alerting of your metrics
  * -- integrates with -- AWS security services
  * see [https://docs.aws.amazon.com/prometheus/latest/userguide/what-is-Amazon-Managed-Service-Prometheus.html](https://docs.aws.amazon.com/prometheus/latest/userguide/what-is-Amazon-Managed-Service-Prometheus.html)

* options for using Prometheus -- with -- Amazon EKS
  + when first creating an Amazon EKS cluster -> turn on Prometheus metrics 
  + if you ALREADY have an existing Amazon EKS cluster -> you can create your own Prometheus scraper
    + see [Create a scraper](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-collector-how-to.html#AMP-collector-create)
  + deploy Prometheus -- via -- Helm
    + see [Deploy Prometheus using Helm](deploy-prometheus.md)
  + view control plane raw metrics | Prometheus format
    + see [View control plane raw metrics in Prometheus format](view-raw-metrics.md)

## Step 1: Turn on Prometheus metrics when creating a cluster<a name="turn-on-prometheus-metrics"></a>

* Amazon Managed Service for Prometheus resources
  * 👀 outside of the cluster lifecycle 👀
    * == need to be maintained independent of the cluster
    * if you delete your cluster -> delete manually ANY applicable scrapers
      * see [Find and delete scrapers](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-collector-how-to.html#AMP-collector-list-delete)

* if you create a new cluster -> you can turn on the option to send metrics to Prometheus
  * 👀way to turn on 👀
    * **Configure observability** step of creating a new cluster | AWS Management Console
      * see [Create an Amazon EKS cluster](create-cluster.md)

* TODO:
Prometheus discovers and collects metrics from your cluster through a pull\-based model called scraping\. Scrapers are set up to gather data from your cluster infrastructure and containerized applications\. 

When you turn on the option to send Prometheus metrics, Amazon Managed Service for Prometheus provides a fully managed agentless scraper\. Use the following **Advanced configuration** options to customize the default scraper as needed\.

Scraper alias  
\(Optional\) Enter a unique alias for the scraper\.

Destination  
Choose an Amazon Managed Service for Prometheus workspace\. A workspace is a logical space dedicated to the storage and querying of Prometheus metrics\. With this workspace, you will be able to view Prometheus metrics across the accounts that have access to it\. The **Create new workspace** option tells Amazon EKS to create a workspace on your behalf using the **Workspace alias** you provide\. With the **Select existing workspace** option, you can select an existing workspace from a dropdown list\. For more information about workspaces, see [Managing workspaces](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-manage-ingest-query.html) in the *Amazon Managed Service for Prometheus User Guide*\.

Service access  
This section summarizes the permissions you grant when sending Prometheus metrics:  
+ Allow Amazon Managed Service for Prometheus to describe the scraped Amazon EKS cluster
+ Allow remote writing to the Amazon Managed Prometheus workspace
If the `AmazonManagedScraperRole` already exists, the scraper uses it\. Choose the `AmazonManagedScraperRole` link to see the **Permission details**\. If the `AmazonManagedScraperRole` doesn't exist already, choose the **View permission** details link to see the specific permissions you are granting by sending Prometheus metrics\.

Subnets  
View the subnets that the scraper will inherit\. If you need to change them, go back to the create cluster **Specify networking** step\.

Security groups  
View the security groups that the scraper will inherit\. If you need to change them, go back to the create cluster **Specify networking** step\.

Scraper configuration  
Modify the scraper configuration in YAML format as needed\. To do so, use the form or upload a replacement YAML file\. For more information, see [Scraper configuration](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-collector-how-to.html#AMP-collector-configuration) in the *Amazon Managed Service for Prometheus User Guide*\.

Amazon Managed Service for Prometheus refers to the agentless scraper that is created alongside the cluster as an AWS managed collector\. For more information about AWS managed collectors, see [AWS managed collectors](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-collector.html) in the *Amazon Managed Service for Prometheus User Guide*\.

**Important**  
You must set up your `aws-auth` `ConfigMap` to give the scraper in\-cluster permissions\. For more information, see [Configuring your Amazon EKS cluster](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-collector-how-to.html#AMP-collector-eks-setup) in the *Amazon Managed Service for Prometheus User Guide*\.

## Step 2: View Prometheus scraper details<a name="viewing-prometheus-scraper-details"></a>

* view your cluster | AWS Management Console, & choose the **Observability ** tab
* shows a list of scrapers / cluster
  * information detailed
    * scraper configuration,
    * ARN,
    * remote write URL,
    * networking information

* scraper ID
  * uses
    * -- input to -- Amazon Managed Service for Prometheus API operations (_Example:_ `DescribeScraper` and `DeleteScraper`)

* see the [Amazon Managed Service -- for -- Prometheus API Reference](https://docs.aws.amazon.com/prometheus/latest/userguide/AMP-APIReference.html)