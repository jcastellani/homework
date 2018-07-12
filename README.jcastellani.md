### Overview

This Ansible playbook installs Apache Airflow and supporting services into a dedicated VPC on
AWS.  As currently configured, the playbook will:

- Create a VPC with CIDR 10.1.0.0/16 in the eu-west-1 region
- Create public and private subnets in 3 availability zones in the region, for a total of 6 subnets.
It also creates an Internet gateway, network ACLs, security groups, and routing tables.
- Creates a multi-az AWS RDS Postgres instance to store Airflow data.
- Creates a Redis 4.0.10 server based on Ubuntu 16.04 to handle the messaging used by the Airflow Celery
executor.
-  Creates a single instance that runs all Airflow services, including web server, scheduler, and workers.

## Requirements
To run the playbook, you will need the following:

1) A local AWS client installation including AWS credentials (access key and secret key).  For simplicity,
the account used to test this was granted administrator access.  In reality, the AWS account needs
rights to create and alter VPCs, create and modify RDS instances, and create and modify EC2 keys
and instances.

2) Python 2.6+ or 3.5+.  Ansible is based on Python, but can run under either Python2 or Python3.

3) Ansible uses the AWS `boto` and `boto3` SDKs to communicate with AWS.  Both must be installed locally.
 
4) Ansible uses SSH to communicate with the various hosts.  An SSH client needs to be installed locally.

5) Ansible 2.6 should be installed locally.  This playbook was developed using Ansible 2.6, which
was the latest available at the time.  It may run on earlier versions of Ansible, but it has not\
been tested with any of them.  

## Running the playbook

Once all of the requirements have been installed and the AWS account has been created (and the associated
credentials installed locally), the playbook can be run via:

`ansible-playbook <path-to-installation>/aws/ansible/site.yaml`

Note that the first run of the playbook will take some time, as it waits for the AWS RDS instance to be 
created, which alone takes about 10 minutes.  Subsequent runs are much faster. 


# TODOs

The following items still need to be addressed before this can be considered production-ready:

1) The Airflow web application needs to be secured with SSL and authentication.
2) A stable public DNS name needs to be configured for the web application.
3) In the interests of cost, all instances used were t2.micro (or db.t2.micro) instances. It
is likely that the instance types will need to be increased for production loads.  The Airflow
services may also need to be split up into separate instances.
4) Again in the interest of cost, no external volumes were used for the Airflow or Redis server instances.
This would obviosly need to change for production.
5) The playbook currently does not handle secret data.  Some mechanism for secret management needs to
be put in place, either via Ansible's Vault or some other means.
6) The playbook needs further refactoring to support multiple environments and regions.
7) There are currently no tests for this playbook.  That obviously needs attention.
8) There is some hard-coding of things like subnet placement that should be refactored.

