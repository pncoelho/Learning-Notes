# AWS Essential

https://interactive.linuxacademy.com/diagrams/ProjectOmega2.html

OR

https://lucid.app/lucidchart/4a14d834-acd0-4b9f-8f0b-85b69d01b6ab

![Project Omega 2.0 Diagram](./media/aws_essentials_project_omega_diagram.png)



### Internet Gateway (IGW) rules:

 - **One VPC** can only have **one Internet Gateway** attached.

 - An IGW cannot be detached from a VPC while there are active AWS resources in the VPC (such as an EC2 instance or RDS Database).

Route Table (RT) rules:

 - **A VPC** can have **multiple active route tables**;

 - A RT cannot be deleter if it has dependancies (associated subnets);



### NetACLS in AWS:

 - They are **stateless**;

 - **A subnet** can only be associated with **one NACL** at a time;

 - An NACL allows or denies traffic from entering a subnet. Once inside the subnet other AWS resourcer (i.e. EC2 isntances) may have additional security layers (security groups);



### Subnet in AWS:

 - A **VPC** spans **all of the Availability Zones in the region**.

 - A **Subnet** must reside entirely within **one Availability Zone** and cannot span zones;

 - **Public Subnets** have a route to the Internet - Controled via RT;

 - **Private Subnets** don't have a route to the Internet - Controled via RT;

 - If subnets are note explicitly associated with a Route Table, they will be **implicitly** associated with the **default Route Table**;



### Elastic Block Store (EBS):

 - Provides block level storage volumes for use with EC2 instances;
 - Can be attached to any running instance that is in the **same Availability Zone**;
 - They persist independently from the life of the instance;
 - EC2 instances:
     - Every EC2 instance **must have a root volume**, which may or not be EBS;
     - By default, **EBS root volumes are set to be deleted when the instance is terminated**;
 - Snapshots:
		 - An image of an EBS volume that can be **stored as a backup** or used to **create a duplicate**;
	 - It's not an active EBS volume and cannot be attached to an EC2 instance;
	 - To restore the snapshot, a new EBS volume must be created using the snapshot as a template;



### Security Groups

Differences between **NACLs** and **SGs**:

- **Network ACLs** apply at the **subnet level**, while **Security Groups** apply at the **Instance level**;
- **NACLs** have rule numbers, **SGs** don't;
- **NACLs** can have deny statements, **SGs** cannot;
- **NACLs** are **stateless**, while **SGs** are **stateful**;

New Security Groups allow **all traffic out** and **no traffic in**.

![](./media/sg_new_security_group.png)



### EC2 to Internet Steps

![](./media/ip_ec2_internet_access.png)



### Simple Storage Service (S3)

This is AWS's primary storage service, where any type of file can be stored.

The root level "*folders*" in S3 are ***buckets***, which can have "*subfolders*" that are referred to as ***folders***.

Files stored in a bucket are referred to as ***objects***.

An S3 bucket is region specific, meaning all it's data and objects exist in a data center in that region. 

#### S3 Charging

Charging for S3 buckets is done in two ways:

1. **Storage Cost**
   - Applies to data at rest in S3;
   - Charged per GB used;
   - Price per GB varies based on region and storage class;
2. **Request Pricing** - moving data in/out of S3
   - PUT;
   - COPY;
   - POST;
   - LIST;
   - GET;
   - Lifecycle Transitions Request;
   - Data Retrieval;
   - Data Archival;
   - Data Restorartion;

#### S3 Properties

- **Bucket**
  - General Info
  - Permissions
  - Static Web Hosting
  - Logging
  - Events
  - Versioning
  - Lifecycle
  - Cross-Region Replication
  - Tags
  - Requester Pays
  - Transfer Acceleration
- **Folder**
  - General Info
  - Details
- **Object**
  - General Info
  - Details
  - Permissions
  - Metadata

#### S3 Storage Class

Represents the **classification** assigned to each object in S3.

This classification dictates things like:

- Storage cost
- Object availability
- Object durability
- Frequency of access

The default storage class is **standard**, but this can be changed at any time.

The available classes are:

- **Standard**
  - Designed for general, all-purpose storage;
  - Business critical objects and objects with frequent access;
  - Default storage option;
  - Most expensive class;
- **Intelligent-Tiering**:
  - Designed for objects with changing or uknown access patterns;
  - Access patterns of the objects are monitored, and those that haven't been accessed for some time, are moved to the infrequent access tier;
  - Less expensive than the standard storage class;
- **Standard-Infrequent Access**
  - Designed for objects that are not accessed frequently but must be immediately available when accessed;
  - S3 standard Infrequen Access;
  - Less expensive than the standard storage class;
  - But they do have a retrieval fee for acessing objects;
- **One Zone-Infrequent Access**
  - Designed for non-critical, reproducible objects;
  - Single Availability Zone;
- **Glacier**
  - Designed for long-term archival storage;
  - Useful for backup and compliance;
  - May take several hours for objects stored in Glacier to be retrieved;
  - Low-cost S3 storage class;
- **Glacier Deep Archive**
  - Designed for long-term archival storage;
  - Cheapest S3 storage class;

The change to Glacier may take one to two days to take effect

#### S3 Object Lifecycle

An object lifecycles is a set of rules that automate the migration of an object's to a different storage class, or its deletion, based on specified time intervals.

The lifecycle's functionality is located on the bucket level. However, it can be applied to:

- The entire bucket;
- One specific folder;
- One specific object;

#### S3 Permissions

S3 permissions grant granular control over who can view, access, and use specific buckets and objects.

They can be defined on the bucket and object level:

- **Bucket**
  - **List** - who can see the bucket name;
  - **Upload/Delete**
  - **Permissions**
- **Object**
  - **Open/Download**
  - **View**
  - **Edit**

Bucket level permissions are generally used for "internal" access control.

Objects can be shared via link.

#### S3 Versioning

Keeps track of and stores all versions of an object so that you can access and use an older version.

Versioning rules:

- It's either ON or OFF;
- Once it is turned ON, versioning **can only be suspended**, meaning it cannot be fully turned OFF;
- Suspending versioning **only prevents new versions**, while maintaining all previous versions in storage;
- Versioning can **only be set on the bucket level**, applying to all objects in the bucket;



### Databases

Two main categories of databases:

- Relational Databases, known as "*SQL*";
- Non-Relational Databases, known as "*NoSQL*";

AWS offers both:

- **RDS** for **SQL**;
- **DynamoDB** for **NoSQL**;

**RDS** is a **SQL database service** that provides a wide range of SQL database options.

**DynamoDB** is a **NoSQL database service**.

Differences between SQL and NoSQL:

- SQL
  - Stores related data in tables;
  - Typically used for very structured data, such as contact lists;
- NoSQL
  - Stores related data in JSON-like, name-value documents;
  - Typically used for non-structured data, such as cataloging documents;

### Pricing

Charging varies a bit between RDS and DynamoDB:

- RDS
  - The **engine choosen**;
    - **Amazon Aurora is not free**;
  - The **RDS Instance Class**;
    - Similar to EC2 instance type;
  - **Purchasing Terms**;
    - On Demand;
    - Reserved;
  - **Database Storage**;
  - **Data Transfer** in/out of RDS
- DynamoDB
  - **Provisioned Throughput Capacity**;
  - **Indexed Data Storage**;
  - **DynamoDB Streams**;
  - **Reserved Capacity**;
  - **Data Transfer** in/out of DynamoDB;



### Simple Notification Service (SNS)

An AWS service that allows you to **automate the sending of email or text message notifications**, **based on events** that happen in your AWS account.

In SNS there are two types of clients:

- **Publishers** (producers) - They **produce** and send **messages** to a **topic**, which is a communication channel;
- **Subscribers** (consumers) - Consume or receive the messages or notifications when they are subscribed to the topic;

The supported protocols for subscribers are:

- Amazon SQS;
- HTTP/S;
- Email;
- SMS;
- Lambda;

The SNS basic components are:

- **Topics**
  - How you label and group different **endpoints** that you send messages to;
- **Subscriptions**
  - The **endpoints** that a topic sends messages to;
    - I.E. email address, or a phone number;
- **Publishers**
  - The human/alarm/event that gives SNS the message that needs to be sent;

#### Pricing:

- **Publishes**
  - Number of SNS requests;
- **Notification deliveries**
  - Number of subscribers the message is sent to;
- **Data transfer in/out of SNS**

#### Limits:

- Topics - 100,000 per account
- Subscriptions - 12,500,000 per topic



### CloudWatch

A service that allows you to **monitor** various elements of your AWS account.

#### Pricing

- Per dashboard;
- Detailed monitoring for EC2 instances;
- Custom metrics;
- API Requests;
- Logs;
- Events/custom events;

#### Lab

```
Create an SNS Topic and Subscribe an Email Address
In the AWS Management Console, navigate to Simple Notification Service (SNS).
In the Create topic box, enter a Topic name of "EC2statechange".
Click Next step.
On the Create topic page, leave everything as default, and click Create topic.
In the Subscriptions section, click Create subscription.
On the Create subscription page, change the Protocol to Email.
For Endpoint, type your email address.
Click Create subscription.
In a new browser tab, navigate to your email inbox.
Open the AWS Notification - Subscription Confirmation email, and click the Confirm subscription link.
Go back to your AWS Management Console browser tab, and refresh the page. The subscription should now be confirmed.
Create an Amazon EventBridge Rule to Trigger the SNS Topic When There Is a State Change to an EC2 Instance
Go to Amazon EventBridge.
Click Create Rule.
Name the rule "EC2instancestatechange".
Choose Even Pattern.
Under Event matching pattern, choose Pre-defined pattern by service.
Choose the Service Provider AWS.
Choose the service name EC2.
For Even Type, choose EC2 Instance State-change Notification.
Leave the Any state and Any instance options selected.
Click into the field at the top of the Targets menu, and select SNS topic from the dropdown.
For Topic, select EC2statechange from the dropdown.
Click Create.

```



### CloudTrail

A service that allows you to track various actions taken while using your AWS account.

Actions taken by a user, role, or an AWS Service are recorded as events in CloudTrail.

Events include actions taken in the AWS Management console, CLI, SDKs and APIs.

#### Pricing

- Management events;
  - Launching an EC2 instance or creating an S3 Bucket;
- Data events;
  - Adding or removing objects from an S3 bucket;
- Usage Charges;
  - S3, SNS or encryption;



### Elastic Load Balancer (ELB)

Evenly distributes traffic between EC2 instances that are associated with it.

There are two LB types:

- **Application LB**
  - HTTP and HTTPS traffic only
  - L7
- **Network LB**
  - Any traffic
  - L4

#### Pricing

- No Free Tier
- For each hour or partial hour the LB is running;
- For each GB of data transfered through the load balancer;



### Autoscaling

Automates the process of adding (**scaling in**) or removing (**scaling out**) EC2 instances based on traffic demand for your application.

*Note: scaling up or scaling down is adding or removing resources from an EC2 instance*

There are two main components part of Auto Scaling:

- **Launch Configuration**
  - The EC2 template used when Auto Scaling needs to add an additional server to the group;
- **Auto Scaling Group**
  - All the rules and settings that govern:
    - When an EC2 instance is added or removed;
    - The minimum and maximum number of EC2 instances;

#### Pricing

- Auto Scaling is free to use;
- You will be charged for the resources that Auto Scaling provisions;



### Route 53

Configure and manage web domains for websites or applications you host on AWS.

It performs:

- **Domain registration**
- **Domain Name System (DNS) Service**
- **Health Checking**

#### Supported Record Types

- [A record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#AFormat)
- [AAAA record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#AAAAFormat)
- [CAA record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#CAAFormat)
- [CNAME record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#CNAMEFormat)
- [DS record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#DSFormat)
- [MX record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#MXFormat)
- [NAPTR record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#NAPTRFormat)
- [NS record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#NSFormat)
- [PTR record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#PTRFormat)
- [SOA record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#SOAFormat)
- [SPF record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#SPFFormat)
- [SRV record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#SRVFormat)
- [TXT record type](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/ResourceRecordTypes.html#TXTFormat)

#### Pricing:

- No Free Tier usage
- Number of hosted zones;
- Traffic Flow (per policy)
- Standard queries;
- Latency based routing;
- Geo DNS queries;
- Health checks;
- Register/transfer a domain;



### CloudFront

An AWS service that **replicates data, video, and applications** around the world **to reduce latency**, so that access to these resources is fast for the people who will use them.

It speeds up the distribution of **static and dynamic web content**, such as .**html**, .**css**, .**js** and **image files**.

CloudFront delivers your content through a worldwide network of datacenters called **edge locations**.

When a user requests content that is being server with CloudFront, **the user is routed to the edge location that provides the lowest latency**.

If the content is:

- Already in the edge location
  - It's delivered immediately;
- Not in the edge location
  - It's retrieved from the origin and copied to the edge location;

To create a basic CloudFront Distribution for S3 objects the steps are as follows:

- Upload the data for distribution to an S3 bucket;
  - For CloudFront URLs to work, **Public Read permissions must be explicitly granted to each object**;
- From the **CloudFront Console**, choose **Distribution**;
- On the Delivery Method, select the **Get Started** on **Web**;
- On the **Create Distribution**, under **Origin Settings** choose the Amazon S3 Bucket that was created earlier;
- Under **Default Cache Behavior Settings** accept the default values, and CloudFront will:
  - Forward requests to the S3 bucket;
  - Allow HTTP(S) access to the objects;
  - Cache the objects at the Edge locations for 24H;
- Under **Distribution Settings**, set the desired options including the following key configurations:
  - **Price Class** - also sets the regions where CloudFront will serve from;
  - **Alternate Domain Names**
  - **Distribution State**
- Choose **Create Distribution**;
- Ensure that the alternate domain name(s) are configured in **Route 53** if they are set in CloudFront;



### Lambda

It's **serverless** computing, allowing for **code execution without provisioning or managing a server**.

Code is **only run when needed and scales automatically**.

You **only pay for the compute time you consume**, so there is no charge when the code is not running.

When creating a function, it can be one of the following:

- **Author from scratch**
  - Start with a simple Hello World example;
- **Use a blueprint**
  - Use sample code and configuration presets for common use cases;
- **Container image**
- **Browse serverless app repository**

#### Pricing

- **Requests** (to execude code);
- **Duration** (the lenght of time it takes the code to execute);
- **Accessing data from other AWS services/resources**;

