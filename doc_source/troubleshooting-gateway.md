# Troubleshooting an AWS IoT SiteWise gateway<a name="troubleshooting-gateway"></a>

AWS IoT SiteWise gateways run an AWS IoT Greengrass connector\. You can configure your gateway to log connector events to CloudWatch and to your gateway's local file system\. Then, you can view the log files associated with the connector to troubleshoot your gateway\.

You can also view CloudWatch metrics reported by your gateways to troubleshoot issues with connectivity or data streams\. For more information, see [Gateway metrics](monitor-cloudwatch-metrics.md#gateway-metrics)\.

**Topics**
+ [Configuring and accessing AWS IoT SiteWise gateway logs](#configure-gateway-logs)
+ [Troubleshooting gateway issues](#troubleshoot-gateway-issues)
+ [Troubleshooting AWS IoT Greengrass issues](#troubleshoot-greengrass-issues)

## Configuring and accessing AWS IoT SiteWise gateway logs<a name="configure-gateway-logs"></a>

Before you can view gateway logs, you must configure your gateway to send logs to Amazon CloudWatch Logs or store logs on the local file system\.
+ Use CloudWatch Logs if you want to use the AWS Management Console to view your gateway's log files\. For more information, see [Using Amazon CloudWatch Logs](monitor-gateway-logs.md#gateway-cloudwatch-logs)\.
+ Use local file system logs if you want to use the command line or local software to view your gateway's log files\. For more information, see [Using local file system logs](monitor-gateway-logs.md#gateway-local-logs)\.

## Troubleshooting gateway issues<a name="troubleshoot-gateway-issues"></a>

Use the following information to troubleshoot gateway issues\.

**Topics**
+ [Unable to deploy packs to AWS IoT SiteWise Edge gateways](#gateway-issue-ggv2-packs)
+ [Modbus TCP sources are out of sync](#gateway-issue-source)
+ [Unable to connect to stream manager](#gateway-issue-stream-manager)
+ [Unable to connect to an OPC\-UA source](#gateway-issue-opc-ua-connection)
+ [AWS IoT SiteWise doesn't receive data from OPC\-UA servers](#gateway-issue-data-streams)

### Unable to deploy packs to AWS IoT SiteWise Edge gateways<a name="gateway-issue-ggv2-packs"></a>

If the Greengrass nucleus component \(`aws.greengrass.Nucleus`\) is out of date, you might not be able to deploy packs to your AWS IoT SiteWise Edge gateway\. You can use the AWS IoT Greengrass V2 console to upgrade the Greengrass nucleus component\.

**Upgrade the Greengrass nucleus component \(console\)**

1. Navigate to the [AWS IoT Greengrass console](https://console.aws.amazon.com/greengrassIntro)\.

1. In the navigation pane, under **Greengrass**, choose **Deployments**\.

1. In the **Deployments** list, select the deployment that you want to revise\.

1. Choose **Revise**\.

1. On the **Specify target** page, choose **Next**\.

1. On the **Select components** page, under **Public components**, in the search box, enter **aws\.greengrass\.Nucleus**, and then select **aws\.greengrass\.Nucleus**\.

1. Choose **Next**\.

1. On the **Configure components** page, choose **Next**\.

1. On the **Configure advanced settings** page, choose **Next**\.

1. On the **Review** page, choose **Deploy**\.

### Modbus TCP sources are out of sync<a name="gateway-issue-source"></a>

Your Modbus TCP source might be out of sync if your source data type is ASCII, UTF8, or ISO8859 and you're running an old version of the Modbus\-TCP Protocol Adapter connector\. To upgrade the connector to the latest version, do the following:

1. Sign in to the [AWS IoT Greengrass V1 console](https://console.aws.amazon.com/greengrass/)\.

1. In the navigation pane, choose **Groups**\.

1. Under **Greengrass groups**, choose the target group\.

1. In the navigation pane, choose **Connectors**\.

1. In the **Upgrade** column, choose **Available**\.

1. On the **Upgrade connector** page, choose the latest version, and then choose **Upgrade**\.

For more information, see [Modbus\-TCP Protocol Adapter connector](https://docs.aws.amazon.com/greengrass/latest/developerguide/modbus-tcp-connector.html) in the *AWS IoT Greengrass Version 1 Developer Guide*\.

### Unable to connect to stream manager<a name="gateway-issue-stream-manager"></a>

You might see the following `swPublisher` error log message if stream manager isn't enabled on your gateway's AWS IoT Greengrass group\.

```
com.amazonaws.greengrass.streammanager.client.StreamManagerClientImpl: Connect failed
```

As of version 6, the AWS IoT SiteWise connector requires stream manager\. For more information about how to enable stream manager, see step 5 of [Configuring an AWS IoT Greengrass group](configure-gateway.md#attach-iam-role)\.

### Unable to connect to an OPC\-UA source<a name="gateway-issue-opc-ua-connection"></a>

You might see the following `OPCUACollector` error log message if the version of the installed OpenJDK isn't supported\.

```
java.security.KeyStoreException: Key protection  algorithm not found: java.security.UnrecoverableKeyException: Encrypt Private Key failed: unrecognized algorithm name: PBEWithSHA1AndDESede
          Failed to start OPC-UA Connection for Source 'Server 1': Failed to add key to store
```

To downgrade to the supported OpenJDK version, follow the steps in this section\. These steps assume that you use a device with Ubuntu\. If you use a different Linux distribution, consult the relevant documentation for your device\.

**To downgrade to the support Amazon Corretto 8**

1. To uninstall the current OpenJDK, run one of the following commands\.
   + 

     ```
     sudo apt purge -y openjdk-8-jre-headless
     ```
   + 

     ```
     sudo apt-get purge -y java-1.8.0-amazon-corretto-jdk
     ```

1. To download and install the supported [Amazon Corretto 8](https://github.com/corretto/corretto-8/releases/tag/8.282.08.1), run the following command\.

   ```
   curl -s https://corretto.aws/downloads/resources/8.282.08.1/java-1.8.0-amazon-corretto-jdk_8.282.08-1_amd64.deb --output /tmp/java-1.8.0-amazon-corretto-jdk_8.282.08-1_amd64.deb
   sudo apt-get update && sudo apt-get install java-common
   sudo dpkg --install /tmp/java-1.8.0-amazon-corretto-jdk_8.282.08-1_amd64.deb
   ```

1. To restart the AWS IoT Greengrass V1 Core software, run the following command\.

   ```
   sudo /greengrass/ggc/core/greengrassd restart
   ```

### AWS IoT SiteWise doesn't receive data from OPC\-UA servers<a name="gateway-issue-data-streams"></a>

If your AWS IoT SiteWise assets aren't receiving data sent by your OPC\-UA servers, you can search your gateway's logs to troubleshoot issues\. Look for info\-level `swPublisher` logs that contain the following message\.

```
Emitting diagnostic name=PublishError.SomeException
```

Based on the type of *SomeException* in the log, use the following exception types and corresponding issues to troubleshoot your gateway:
+ **ResourceNotFoundException** – Your OPC\-UA servers are sending data that doesn't match a property alias for any asset\. This exception can occur in two cases:
  + Your property aliases don't exactly match your OPC\-UA variables, including any source prefix you defined\. Check that your property aliases and source prefixes are correct\.
  + You haven't mapped your OPC\-UA variables to asset properties\. For more information, see [Mapping industrial data streams to asset properties](connect-data-streams.md)\.

    If you already mapped all of the OPC\-UA variables that you want in AWS IoT SiteWise, you can filter which OPC\-UA variables the gateway sends\. For more information, see [Using OPC\-UA node filters](opc-ua-node-filters.md)\.
+ **AccessDeniedException** – Your gateway's AWS IoT Greengrass group doesn't have sufficient permissions to use the [BatchPutAssetPropertyValue](https://docs.aws.amazon.com/iot-sitewise/latest/APIReference/API_BatchPutAssetPropertyValue.html) operation to send data to asset properties\. For more information, see the [AWS IoT SiteWise connector requirements](https://docs.aws.amazon.com/greengrass/latest/developerguide/iot-sitewise-connector.html#iot-sitewise-connector-req)\.
+ **InvalidRequestException** – Your OPC\-UA variables data types don't match your asset property data types\. For example, if an OPC\-UA variable has an integer data type, your corresponding asset property must be integer data type\. A double\-type asset property can't receive OPC\-UA integer values\. To fix this issue, define new properties with the correct data types\.
+ **TimestampOutOfRangeException** – Your gateway is sending data that is outside the range that AWS IoT SiteWise accepts\. AWS IoT SiteWise rejects any data points with timestamps earlier than 7 days in the past or newer than 5 minutes in the future\. If your gateway lost power or connection to the AWS Cloud, you might need to clear your gateway's cache\. 
+ **ThrottlingException** or **LimitExceededException** – Your request exceeded an AWS IoT SiteWise service quota, such as rate of data points ingested or request rate for asset property data API operations\. Check that your configuration doesn't exceed the [AWS IoT SiteWise quotas](quotas.md)\.

## Troubleshooting AWS IoT Greengrass issues<a name="troubleshoot-greengrass-issues"></a>

To find solutions to many issues configuring or deploying your gateway on AWS IoT Greengrass, see [Troubleshooting AWS IoT Greengrass](https://docs.aws.amazon.com/greengrass/latest/developerguide/gg-troubleshooting.html) in the *AWS IoT Greengrass Developer Guide*\.