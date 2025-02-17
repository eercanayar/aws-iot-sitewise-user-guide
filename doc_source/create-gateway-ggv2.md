# Creating a gateway<a name="create-gateway-ggv2"></a>

You can use the AWS IoT SiteWise console to create a gateway\. This topic contains the following steps:

**Topics**
+ [Step 1: Configure a gateway](#configure-gateway-console)
+ [Step 2: Configure edge capabilities](#configure-edge-capabilities-console)
+ [Step 3: Configure publisher \- optional](#configure-publisher)
+ [Step 4: Add data sources \- optional](#add-data-sources-console)
+ [Step 5: Review and generate an installer](#generate-installer-console)

## Step 1: Configure a gateway<a name="configure-gateway-console"></a>

1. <a name="sitewise-open-console"></a>Navigate to the [AWS IoT SiteWise console](https://console.aws.amazon.com/iotsitewise/)\.

1. In the navigation pane, choose **Gateways**\.

1. Choose **Create gateway**\.

1. Enter a name for your gateway or use the name generated by AWS IoT SiteWise\.

1. For **Greengrass core device**, choose one of the following options:
   + **Default setup** ‐ AWS automatically uses default settings to create a Greengrass core device in AWS IoT Greengrass V2\.

     1. Enter a name for the Greengrass core device or use the name generated by AWS IoT SiteWise\.
   + **Advanced setup** ‐ Choose this option if you want to use an existing Greengrass core device or to create one manually\.

     1. Choose a Greengrass core device or choose **Create Greengrass core device** to create one in the AWS IoT Greengrass V2 console\. For more information, see [Setting up AWS IoT Greengrass V2 core devices](https://docs.aws.amazon.com/greengrass/v2/developerguide/setting-up.html) in the *AWS IoT Greengrass Version 2 Developer Guide*\.

1. Choose **Next**\.

## Step 2: Configure edge capabilities<a name="configure-edge-capabilities-console"></a>

AWS IoT SiteWise provides the following packs that your gateway can use to determine how to collect and process your data\. Select packs to enable edge capabilities for your gateway\.
+ **Data collection pack** enables your gateway to collect data from multiple OPC\-UA servers, and then export the data from the edge to the AWS Cloud\. By default, this pack is automatically enabled for your gateway\. You can't disable this pack\.
+ **Data processing pack** enables your gateway to process your data at the edge\. For example, you can use asset models to compute metrics and transforms\. For more information about asset models and assets, see [Modeling industrial assets](industrial-asset-models.md)\.
**Note**  
AWS IoT SiteWise retains your edge data on your gateways up to 30 days\. The retention of your data is dependent on your device and the available disk space\.
30 days after your gateway has been disconnected from the AWS Cloud, the data processing pack is automatically disabled\.

1. \(Optional\) In the **Edge capabilities** pane, select **Data processing pack**\. 

1. \(Optional\) In the **Edge LDAP connection** pane, you can grant user groups in your corporate directory access to this gateway\. The user groups can use the Lightweight Directory Access Protocol \(LDAP\) credentials to access the gateway\. Then they can use the AWS OpsHub for AWS IoT SiteWise application, AWS IoT SiteWise APIs, or other tools to manage the gateway\. For more information, see [Managing gateways](manage-gateways-ggv2.md)
**Note**  
You can also use the Linux credentials to access the gateway\. For more information, see [Accessing your gateway using Linux credentials](manage-gateways-ggv2.md#linux-user-pool)\.

   1. Select **Enable**\.

   1. For **Provider name**, enter a name for your LDAP provider\.

   1. For **Hostname or IP address**, enter the hostname or IP address of your gateway device\.

   1. For **Port**, enter a port number\.

   1. For **Base distinguished name \(DN\)**, enter a distinguished name \(DN\) for the base\. 

      The following attribute types are supported: commonName \(CN\), localityName \(L\), stateOrProvinceName \(ST\), organizationName \(O\), organizationalUnitName \(OU\), countryName \(C\), streetAddress \(STREET\), domainComponent \(DC\), and userid \(UID\)\.

   1. For **Admin group DN**, enter a DN\.

   1. For **User group DN**, enter a DN\.

1. Choose **Next**\.

## Step 3: Configure publisher \- optional<a name="configure-publisher"></a>

You can configure the publisher to control what data is sent to the cloud and the order that it's sent to the cloud\. The AWS IoT SiteWise publisher is an AWS IoT Greengrass component that runs on your gateway device\. For more information, see [IoT SiteWise Publisher](https://docs.aws.amazon.com/greengrass/v2/developerguide/iotsitewise-publisher-component.html) in the *AWS IoT Greengrass Version 2 Developer Guide*\.

**To configure the publisher**

1. For **Publishing order**, choose one of the following\.
   + **Publish oldest data first** – The gateway publishes the oldest data to the cloud first by default\.
   + **Publish newest data first** – The gateway publishes the newest data to the cloud first\.

1. \(Optional\) Choose **Exclude expired data**\.

1. For **Cutoff period**, enter a number and choose a unit\. The cutoff period must be between five minutes and seven days\. For example, if the cutoff period is three days, data that's older than three days isn't published to the cloud\.

1. \(Optional\) Choose **Local storage settings**\.

1. For **Retention period**, enter a number and choose a unit\. The retention period must be between one minute and 30 days, and greater than or equal to the rotation period\. For example, if the retention period is 14 days, the gateway deletes any data at the edge that's older than the specified cutoff period after it's stored for 14 days\.

1. For **Rotation period**, enter a number and choose a unit\. The rotation period must be greater than one minute, and equal to or less than the retention period\. For example, if the rotation period is two days, the gateway batches up and saves data that is older than the cutoff period to a single file\. The gateway also transfers a batch of data to the following local directory once every two days: `/greengrass/v2/work/aws.iot.SiteWiseEdgePublisher/exports`\.

1. For **Storage capacity**, enter a number that is greater than or equal to 1\. If the storage capacity is 2 GB, the gateway starts deleting data when more than 2 GB of data is stored locally\. 

1. Choose **Next**\.

## Step 4: Add data sources \- optional<a name="add-data-sources-console"></a>

Data sources are local servers or industrial equipment that are connected to gateways\. You can add data sources so that your gateway can ingest data from the OPC\-UA servers to AWS IoT SiteWise\.

**Note**  
Gateways running on AWS IoT Greengrass V2 currently don't support Modbus TCP and Ethernet IP sources\.

**To add an OPC\-UA source**

1. Choose **Add data source**\.

1. Enter a name for the source\.

1. Enter the **Local endpoint** of the data source server\. The endpoint can be the IP address or hostname\. You may also add a port number to the local endpoint\. For example, your local endpoint might look like **opc\.tcp://203\.0\.113\.0:49320**\.

1. \(Optional\) For **Node ID for selection**, add node filters to limit which data streams are ingested to the AWS Cloud\. By default, gateways use the root node of a server to ingest all data streams\. To define node filters, you can use node IDs and the `*` and `**` wildcard characters\.

1. For **Destinations**, choose where the source data is sent\. 
   + **AWS IoT SiteWise** ‐ Send data to AWS IoT SiteWise\. Choose this option if you want to process data at the edge\.
   + **AWS IoT Greengrass stream manager** ‐ Use AWS IoT Greengrass steam manager to send data to the following AWS cloud destinations: channels in AWS IoT Analytics, streams in Amazon Kinesis Data Streams, asset properties in AWS IoT SiteWise, or objects in Amazon Simple Storage Service \(Amazon S3\)\. For more information, see [Manage data streams on the AWS IoT Greengrass Core](https://docs.aws.amazon.com/greengrass/v2/developerguide/manage-data-streams.html) in *AWS IoT Greengrass Version 2 Developer Guide*\.

     Enter a name for the AWS IoT Greengrass stream\.

1. In the **Advanced configuration** pane, do the following: 

   1. \(Optional\) Enter a **Data stream prefix**\. The gateway adds this prefix to all data streams from this source\. Use a data stream prefix to distinguish between data streams that have the same name from different sources\. Each data stream should have a unique name within your account\.

   1. Choose a **Message security mode** for connections and data in transit between your source server and your gateway\. This field is the combination of the OPC\-UA security policy and message security mode\. You must choose the same security policy and message security mode that you specified for your OPC\-UA server\.

   1. If your source requires authentication, choose an AWS Secrets Manager secret from the **Authentication configuration** list\. The gateway uses the authentication credentials in this secret when it connects to this source\. You must attach secrets to your gateway's IoT SiteWise connector to use them for source authentication\. For more information, see [Configuring source authentication](configure-source-authentication.md)\.
**Tip**  
Your data server might have an option named **Allow anonymous login**\. If this option is **Yes**, then your source doesn't require authentication\.

   1. For **Property groups**, choose **Add new group**\.

   1. enter a **Name** for the property group\.

   1. For **Properties**:

      1. \(Optional\) For **Node paths**, add OPC\-UA node filters to limit which OPC\-UA paths are uploaded to AWS IoT SiteWise\. You can use node filters to reduce your gateway's startup time and CPU usage by only including paths to data that you model in AWS IoT SiteWise\. By default, gateways upload all OPC\-UA paths except those that start with `/Server/`\. To define OPC\-UA node filters, you can use node paths and the `*` and `**` wildcard characters\. For more information, see [Using OPC\-UA node filters](opc-ua-node-filters.md)\.

   1. For **Group settings**, do the following:

      1. For **Scan mode**, choose the mode that you want AWS IoT SiteWise to use to collect your data\. For more information about scan mode, see [Filter data ingestion ranges with OPC\-UA](opcua-data-acquisition.md)\.

      1. For **Scan rate**, update the rate that want the gateway to read your registers\. AWS IoT SiteWise automatically calculates the minimum allowable scan rate for your gateway\.

      1.  \(Optional\) Configure a **Deadband setting** for your source\. This controls what data your source sends to your AWS IoT SiteWise, and what data it discards\. For more information about the deadband setting, see [Filter data ingestion ranges with OPC\-UA](opcua-data-acquisition.md)\. 

   1. Choose **Add**\.

1. Choose **Next**\.

## Step 5: Review and generate an installer<a name="generate-installer-console"></a>

In this step, you review the configuration of your gateway, and then do the following:

1. Choose one of the following operating system:
   + **Amazon Linux**
   + **Red hat**
   + **Ubuntu**

1. Choose **Generate**\.

1. In the dialog box, choose **Acknowledge**\.

AWS IoT SiteWise automatically generates an installer that you can use to configure your gateway device\. Make sure that you save the installer file in a secure location\. You will use the file later\. 