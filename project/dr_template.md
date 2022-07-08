# Infrastructure

## AWS Zones
us-east-2, us-west-1

## Servers and Clusters

### Table 1.1 Summary
| Asset      | Purpose           | Size                                                                   | Qty                                                             | DR                                                                                                           |
|------------|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Asset name | Brief description | AWS size eg. t3.micro (if applicable, not all assets will have a size) | Number of nodes/replicas or just how many of a particular asset | Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere |
| EC2 Ubuntu-Web | A web server using Flask | t3.micro | 3 | The application is running on 3 instances. Multi AZ. |
| ALB | Aplication Load Balancer is used for traffic distribution between EC2 Ubuntu-web assets | - | 1 | Multi AZ. |
| VPC | Virtual Private Network for EC2 instances and EKS clusters | - | 1 | Multi AZ.  |
| Custom Ubuntu image | Image of Flask web application | - | - | Image source code is stored in publicly available Github repository. |
| RDS for Ubuntu-web | Aurora MySQL database is used by web service | Aurora MySQL | 2 | Primary RDS is running on 2 instances in us-east-2 region and replicates to secondary RDS that is running on 2 instances in us-west-1 region. Both RDS has a 5 day backup window. |
| NLB for Monitoring platform | Distribute traffic between monitoring stack kubernetes cluster nodes | - | 1 | - |
| udacity-cluster | Kubernetes cluster for monitoring stack | t3.medium | 2 nodes | 2 nodes. Multi AZ. |
| Monitoring platform for web application| Prometheus and Grafana used for monitoring and alerting | - | 1 | Configuration is not backed up. Grafana web available publicly |
| S3 bucket for storing Terraform code locally | Store and execute Terraform code | S3 bucket | 1 | Available only from private network. |
| GitHub repo storing Terraform code | Infrastructure automation (IaaC) | - | 1 | Github publicly available. All sensitive data shall be stored as environment variables |
| SSH keys | Used to get access to EC2 instances | - | 1 | Shall be stored securely. |
| AWS credentials | Access Amazon web console for resource management | - | 1 | Use strong password. Use MFA. Limit access to others |

### Descriptions
There is a Flask web service running on EC2 instances using Aurora MySQL database that replicates to another region and Monitoring stack (Prometheus + Grafana) running in Kubernetes cluster on 2 nodes. The IT assets are configured to run in multi availability zones in us-east-2 and us-west-1 regions. Grafana LB is accessible publicly. The Terraform code is stored in public GitHub repo and in Amazon S3 bucket, from which it can be run. 

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