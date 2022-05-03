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
