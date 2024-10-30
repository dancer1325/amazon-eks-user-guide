# Monitor your cluster performance and view logs<a name="eks-observe"></a>

* MANY available monitoring or logging tools / 
  * Amazon EKS log data -- can be streamed to --
    * AWS services or
    * partner tools
      * [AWS-supported open-source solution](https://docs.aws.amazon.com/grafana/latest/userguide/solution-eks.html)

* to view
  * cluster health and details
    * select **Clusters** | Amazon EKS console
  * details about any existing Kubernetes resources / deployed | cluster
    * see [View Kubernetes resources in the AWS Management Console](view-kubernetes-resources.md)

* Monitoring
  * == 👀 important part of maintaining the reliability, availability, and performance of Amazon EKS 👀
  * recommendations
    * collect monitoring data -- from -- ALL parts of your AWS solution
      * -> easily debug a multi-point failure
    * 👀questions to wonder 👀
      + What are your goals? if your clusters scale dramatically -> do you need real\-time notifications ?
      + What resources need to be observed?
      + How frequently do you need to observe these resources? Does your company want to respond quickly to risks?
      + What tools do you intend to use?
        + if you already run AWS Fargate -> use the built\-in [log router](fargate-logging.md)
      + Who do you intend to perform the monitoring tasks?
      + if something goes wrong -> whom do you want notifications to be sent to ?

## Logging and monitoring on Amazon EKS<a name="logging-monitoring"></a>

* built-in tools -- for -- logging & monitoring
  * 👀Amazon EKS Control plane logging 👀
    * -- records -- ALL API calls | your clusters
    * -- capture -- 
      * audit information
        * == actions / -- performed by the -- users
      * role-based information
    * forward the captured information -- to -- CloudWatch Logs | your account
      * see [Send control plane logs to CloudWatch Logs](control-plane-logs.md)
  * see [Logging and monitoring on Amazon EKS](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/amazon-eks-logging-monitoring.html)

* uses of the logs
  * make it easy to
    * secure your clusters
    * run your clusters

* if you want low-level, customizable logging -> enabled [Kubernetes logging](https://kubernetes.io/docs/concepts/cluster-administration/logging/)

* _Example:_ Amazon EKS authenticator logs | Amazon CloudWatch

    ```
    level=info msg="mapping IAM role" groups="[]"
    role="arn:aws:iam::111122223333:role/XXXXXXXXXXXXXXXXXX-NodeManagerRole-XXXXXXXX"
    username="eks:node-manager"
    ```

  * `username`
    * == Amazon EKS internal service role / -- performs -- specific operations -- for --
      * managed node groups
      * Fargate

* Amazon EKS -- is integrated with -- AWS CloudTrail
  * AWS CloudTrail
    * := 👀AWS service / captures ALL API calls for Amazon EKS -- as -- events 👀
      * included calls captured
        * calls -- from the -- Amazon EKS console 
        * code calls -- to the -- Amazon EKS API operations
        * see [Log API calls as AWS CloudTrail events](logging-using-cloudtrail.md)

* Kubernetes API server
  * -- exposes -- SOME metrics / -- are useful for --
    * monitoring
    * analysis
  * see [Monitor your cluster metrics with Prometheus](prometheus.md)

* if you want to configure Fluent Bit -- for -- custom Amazon CloudWatch logs -> see [Setting up Fluent Bit | CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-logs-FluentBit.html#Container-Insights-FluentBit-setup)

## Amazon EKS logging and monitoring tools<a name="eks-monitor-tools"></a>

* Amazon Web Services tools / can monitor Amazon EKS
  * types
    * tools / set up automatic monitoring
      * recommended to set up ALL you can 
    * tools / require manual calls

* logging tool options

| Areas | Tool | Description | Setup | 
| --- | --- | --- | --- | 
|  Applications  |  [Amazon CloudWatch Container Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights.html)  |  collects, from your containerized applications and microservices, <br/> &nbsp; aggregates,<br/> &nbsp; summarizes metrics <br/>  &nbsp; logs  |  [Setup procedure](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Container-Insights-setup-EKS-quickstart.html)  | 
|  Control plane  |  [AWS CloudTrail](logging-using-cloudtrail.md)  |  It logs API calls -- by a -- <br/> &nbsp; user, <br/> &nbsp; role, or <br/> &nbsp; service  |  [Setup procedure](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)  | 
|  Multiple areas for AWS Fargate instances  |  [AWS Fargate log router](fargate-logging.md)  |  AWS Fargate instances -- streams logs to -- <br/> &nbsp; AWS services or <br/> &nbsp; partner tools <br/> uses [AWS for Fluent Bit](https://github.com/aws/aws-for-fluent-bit) <br/> logs -- can be streamed to -- <br/> &nbsp; other AWS services or <br/> &nbsp; partner tools\.  |  [Setup procedure](fargate-logging.md)  | 

* monitoring tool options

| Areas | Tool | Description | Setup | 
| --- | --- | --- | --- | 
|  Applications  |  [CloudWatch Container Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/ContainerInsights.html)  | collects from your containerized applications and microservices <br/> &nbsp; aggregates <br/> &nbsp; summarizes metrics <br/> &nbsp; logs |  [Setup procedure](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html)  | 
|  Applications  |  [AWS Distro for OpenTelemetry \(ADOT\)](https://aws-otel.github.io/docs/introduction)  |  collects and sends correlated metrics, <br/> &nbsp; trace data, and metadata to AWS monitoring services or partners  |  [Setup procedure](opentelemetry.md)  | 
|  Applications  |  [Amazon DevOps Guru](https://aws.amazon.com/about-aws/whats-new/2021/11/amazon-devops-guru-coverage-amazon-eks-metrics-cluster/)  | detects <br/> &nbsp; node\-level operational performance <br/> &nbsp; availability\.  |  [Setup procedure](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/deploy-container-insights-EKS.html)  | 
|  Applications  |  [AWS X\-Ray](https://docs.aws.amazon.com/xray/latest/devguide/aws-xray.html)  |  receives trace data about your application / includes <br/> &nbsp; ingoing requests <br/> &nbsp; outgoing requests <br/> &nbsp; metadata about the requests <br/> requires the OpenTelemetry add-on  |  [Setup procedure](https://docs.aws.amazon.com/xray/latest/devguide/xray-instrumenting-your-app.html)  | 
|  Applications  | [Amazon CloudWatch Observability Operator](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) | collects <br/> &nbsp; metrics <br/> &nbsp; logs <br/> &nbsp; trace data <br/> send to Amazon CloudWatch & AWS X-Ray | [Setup procedure](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/install-CloudWatch-Observability-EKS-addon.html) | 
|  Applications / control plane  |  [https://docs.aws.amazon.com/prometheus/latest/userguide/what-is-Amazon-Managed-Service-Prometheus.html](https://docs.aws.amazon.com/prometheus/latest/userguide/what-is-Amazon-Managed-Service-Prometheus.html)  | monitor metrics & alerts for <br/> &nbsp; applications <br/> &nbsp; control plane  |  [Setup procedure](prometheus.md)  | 