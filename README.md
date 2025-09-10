# MySQL HeatWave integration with EBS

This GitHub repository explains and demonstrates how to set up a simple environment that showcases the integration between Oracle E-Business Suite (EBS) and 
MySQL HeatWave through the Lakehouse architecture. It provides step-by-step guidance, examples, and configurations to help users understand how data can seamlessly flow from EBS into HeatWave, enabling advanced analytics, 
reporting, and insights without complex ETL processes. The goal is to make it easier to learn and replicate the integration in a controlled, simplified setup.

## Create OCI environment

### Create Virtual Cloud Network (VCN)

Launch the OCI Console, go to Networking → Virtual Cloud Networks, and use the VCN with Internet Connectivity wizard. 
[Oracle Docs](https://docs.oracle.com/en/learn/lab_virtual_network/index.html?utm_source=chatgpt.com)

Provides sample values for fields like “VCN Name” and IPv4 CIDR blocks, complete with visual guidance and review steps.
[Oracle Docs](https://docs.oracle.com/en/learn/lab_virtual_network/index.html?utm_source=chatgpt.com)

**VCN Name** = EBS_workshop<br>
Click **Next**, click **Create.**, and Click **View VCN** <br><br>
Go to **Subnet** , click on **private subnet-EBS_workshop**, click **security**, click **security list for private subnet-EBS_workshop**, and click **Security Rules** <br>
* Click **Add Ingress Rules**, and: Source CIDR: 0.0.0.0/0, Source port range: All, Destination port range: 3306, 33060, Description: mysql/mysqlx ports <br>
Click **Add Ingress Rules** <br><br>

Go back to **EBS_workshop** , click on **Subnets**, click **public subnet-EBS_workshop**, click **security**, click **Default security for EBS_workshop**, and click **Security Rules** <br>
* Click **Add Ingress Rules**, and: Source CIDR: 0.0.0.0/0, Source port range: All, Destination port range: 3306, 33060, Description: mysql/mysqlx ports <br>
Click **Add Ingress Rules**
* Click **Add Ingress Rules**, and: Source CIDR: 0.0.0.0/0, Source port range: All, Destination port range: 5901, Description: vnc server ports <br>
Click **Add Ingress Rules**
* Click **Add Ingress Rules**, and: Source CIDR: 0.0.0.0/0, Source port range: All, Destination port range: 8000,9000, Description: EBS ports <br>
Click **Add Ingress Rules**
* Click **Add Ingress Rules**, and: Source CIDR: 0.0.0.0/0, Source port range: All, Destination port range: 1521, Description: Oracle port <br>
Click **Add Ingress Rules**

### Create MySQL HeatWave 

Provisioning MySQL HeatWave: This guide outlines the steps to create a MySQL HeatWave DB System and enable the HeatWave cluster. It covers prerequisites, including setting up a Virtual Cloud Network (VCN) and configuring networking between OCI and Azure. 
[Oracle Docs](https://docs.oracle.com/en-us/iaas/odsaz/odsa-provisioning-mysql-heatwave.html?utm_source=chatgpt.com)

Provisioning HeatWave Nodes: After setting up the DB System, this document explains how to add and manage HeatWave nodes, including resizing the cluster and utilizing MySQL HeatWave Autopilot for node estimation. 
[Oracle Docs](https://docs.oracle.com/en-us/iaas/Content/database-for-azure-provision/odsa-provisioning-heatwave-nodes.html?utm_source=chatgpt.com)

* Name: EBS_workshop
* Username: admin
* Password: <you define>
* Setup: standalone
* VCN: EBS_workshop
* Subnet: private subnet-EBS_workshop
* Shape: MySQL.4 + HeatWave.512GB (make sure Lakehouse is enabled)
* Initial data storage: 50GB
* Disable backup plan

### Create Object Storage Bucket

To create an Object Storage bucket in Oracle Cloud Infrastructure (OCI), you can follow the official documentation provided by Oracle. [Oracle Documentation](https://docs.oracle.com/iaas/Content/Object/Tasks/managingbuckets_topic-To_create_a_bucket.htm?utm_source=chatgpt.com)

Object Storage Bucket name: EBS_workshop

### Create Compute Node Running EBS Vision Instance

Creating a Compute Instance: [Oracle Docs](https://docs.oracle.com/en-us/iaas/compute-cloud-at-customer/topics/compute/compute-instances.htm?utm_source=chatgpt.com)
* Name: EBS_workshop
* Change Image: marketplace, E-Business Suite Demo Install Image
* Shape: 8 OCPU, 128 GB RAM
* VCN: EBS_workshop
* Subnet: public subnet-EBS_workshop

## Configure Compute Node

### Install S3FS
Login to the Compute node and install:
```
sudo dnf install -y epel-release
sudo dnf update -y
sudo dnf install -y gcc libstdc++-devel fuse-devel curl-devel libxml2-devel mailcap automake autoconf
sudo dnf install -y git
git clone https://github.com/s3fs-fuse/s3fs-fuse.git
cd s3fs-fuse
./autogen.sh
./configure
make
sudo make install
s3fs --version
```
















