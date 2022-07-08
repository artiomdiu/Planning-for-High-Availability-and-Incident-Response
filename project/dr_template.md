# Infrastructure

## AWS Zones
us-east-2

## Servers and Clusters

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Asset name | Brief description | AWS size eg. t3.micro (if applicable, not all assets will have a size) | Number of nodes/replicas or just how many of a particular asset | Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere |
| EC2 Ubuntu-Web | A web server using Flask | t3.micro | 1 | The application is running on 1 instance in us-est-2 region and 2 instances in us-west-1 region. |
| Custom Ubuntu image | Image of Flask web application | - | - |  |
| RDS for Ubuntu-web | Aurora MySQL database is used by web service | Aurora MySQL | 1 | DB is run on 1 instance, has no replication and no snapshots. |
| NLB for Monitoring platform | Disctribute traffic between monitoring stack kubernetes cluster nodes | - | 1 | Configured to run on 2 AZs. |
| udacity-cluster | Kubernetes cluster for monitoring stack | t3.medium | 1 node | Cluster has one node running, but can provision 2 nodes in total. Configured to run in 2 AZs |
| Monitoring platform for web application| Prometheus and Grafana used for monitoring and alerting | - | - | Configuration is not backed up. Grafana web available publicly |
| S3 bucket for storing Terraform code locally | Store and execute Terraform code | S3 bucket | 1 | Available only from private network |
| GitHub repo storing Terraform code | Infrastructure automation (IaaC) | - | - | Github publicly available. All sensitive data shall be stored as environment variables |
| SSH keys | - | - | - | - |
| AWS credentials | Access Amazon web console for resource management | - | - | Use strong password. Use MFA. Limit access to others |

### Descriptions
There is a Flask web service running on 1 instance, using Aurora MySQL database that runs on 1 instance and Monitoring stack (Prometheus + Grafana) running in Kubernetes cluster on 1-2 nodes. The IT assets are configured to run in 2 availability zones (us-east-2b, us-east-2c), but in one region. Grafana LB is accessible publicly. The Terraform code is stored in public GitHub repo and in Amazon S3 bucket, from which it can be run. 

## DR Plan
### Pre-Steps:
In order to make existing IT assets more resilent to disaster following changes shall be made:
* the whole IT infrastructure shall be deployed to one more region (us-west-1)
* web service shall be deployed to 3 instances in region us-west-1. 
* geo-replication for RDS in us-east-2 and us-west-1 shall be made.

Following steps shall be done:
1. Uncomment rds-s code in zone1/rds.tf and push changes to GitHub.
2. From Amazon CloudShell pull changes and update terraform infrastructure.
3. Switch to Amazon CloudShell in region us-west-1 and clone GitHub repo to udacity-ad-west S3 bucket.
4. Follow the steps written in README.md for us-west-1.
5. Deploy infrastructure with terraform from zone2 folder.
6. Set-up monitoring and alerting.

## Steps:
In case of failover in one region, following steps shall be done:
* Promote RDS-s in us-west-1 region to primary DB in AWS console.
* Check whether traffic to Udacity-web is redirected to us-west-1 region.