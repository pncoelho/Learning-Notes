# AWS Essential

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

#### Bucket, Folder and Object Properties

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

