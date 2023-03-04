# Infrastructure

## AWS Zones
Identify your zones here:
- Primary: us-east-2a, us-east-2b, us-east-2c
- Secondary: us-west-1a, us-west-1c


## Servers and Clusters
- 3 EC2 instances with name Ubuntu-Web in every region
- AMI image used by Ubuntu-Web EC2 instances stored in every region
- 2 EC2 instances for EKS cluster in every region

### Table 1.1 Summary
| **Asset**                   | **Purpose**                                                                                                                                                | **Size**                                                               | **Qty**                                                         | **DR**                                                                                                                                               |
|-----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------|-----------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| Asset name                  | Brief description                                                                                                                                          | AWS size eg. T3.micro (if applicable, not all assets will have a size) | Number of nodes /replicas or jus how many of a particular asset | Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere                                         |
| S3 Bucket                   | It is an object storage. It is to storage terraform profile                                                                                                |                                                                        | 2                                                               | Created in two regions                                                                                                                               |
| IAM Roles                   | AWS identitity with permission policies that determine what the identity can and cannot do in AWS. Roles for EKS’ cluster and for worker nodes             |                                                                        | 2                                                               | DR- deployed in two regions                                                                                                                          |
| VPC                         | It is a virtual network dedicated in the AWS account. Here we can define the network space and the resources that we need for the application in a region. |                                                                        | 1                                                               | DR- deployed in two regions                                                                                                                          |
| Subnets                     | Subnet is a range of IP addresses in VPC. There are VPC subnets in every availability zone. In every subnet we can launch AWS resources.                   |                                                                        | 3                                                               | DR- deployed in every Availability of two regions                                                                                                    |
| AMI                         | AMI for EC2 instances used for web servers                                                                                                                 |                                                                        | 1                                                               | Created in two regions                                                                                                                               |
| Key pair                    | Set of security credentials that using to prove your identity when connecting to an EC2 instance                                                           |                                                                        | 2                                                               | Created in two regions                                                                                                                               |
| EC2 instances (web server)  | EC2 instances for Udacity-web server                                                                                                                       | t3.micro                                                               | 3                                                               | DR- deployed in two regions                                                                                                                          |
| EC2 instances (EKS cluster) | EC2 instances for the EKS cluster worker nodes                                                                                                             | t3.medium                                                              | 2                                                               | DR- deployed in two regions                                                                                                                          |
| Application Load Balancer   | Device to distribute the incoming traffic accross the EC2 instances and Grafana dashboard.                                                                 |                                                                        | 2                                                               | DR- deployed in two regions                                                                                                                          |
| Security Group              | It is a configurations virtual firewall rules for: EC2 instances, EKS nodes and RDS  nodes.                                                                |                                                                        | 3                                                               | DR- deployed in two regions                                                                                                                          |
| RDS cluster                 | RDS instances with one primary and another read replica                                                                                                    |                                                                        | 2                                                               | DR- deployed in two regions (every cluster have 2 nodes), one cluster in one region is writer, the another cluster in another region is read replica |
| Prometheus/Grafana          | Monitoring stack                                                                                                                                           |                                                                        | 1                                                               |                                                                                                                                                      |


### Descriptions
More detailed descriptions of each asset identified above.
- VPC: we have created subnets public and private in each availability zone of each region.
- AMI images are using to hold the application executable. You have to create and store these AMI images in both regions. Also, these AMI images are copied from us-east-1 region.
- EC2 instances for web server: we need to create EC2 in every availability zone in the region
- Keypairs: it is necessary creating a keypair in the 2 regions
- RDS cluster: we are deploying two RDS cluster. One RDS cluster as primary cluster is deployed in us-east-2 region. This RDS cluster has one write instance and one read instance. The another RDS cluster as secondary cluster is deployed in us-west-1 region with replication from the primary cluster in us-east.2. This secondary cluster has 2 read instances.  

## DR Plan
### Pre-Steps:
List steps you would perform to setup the infrastructure in the other region. It doesn't have to be super detailed, but high-level should suffice.
- Create and configure S3 bucket for terraform state. 
- Create AMI image.
- Create a private Keypair with name "udacity".
- Deploy: VPC, Application Load Balancer (ALB), Security groups, EC2 instances web servers and EKS cluster in another region.
- Configure a secondary RDS cluster in us-west-1 region with replication from the primary RDS cluster in us-east-2 region. 
- Code from monitoring stack like: prometheus configuration, Grafana dashboard and alerting; must be stored in a git repository so that in case of failure replicate the same monitoring configuration. 

## Steps:
You won't actually perform these steps, but write out what you would do to "fail-over" your application and database cluster to the other region. Think about all the pieces that were setup and how you would use those in the other region
- The first step is stop RDS instances individually in primary RDS cluster, we need to wait around 2 minutes to stop the two instances. While those instances are stopped. We are going to another cluster, in the secondary region we see that RDS cluster in secondary region with name promoting this to a Regional cluster which is no longer replicate.
- Enable a direct client connection point to directly access the RDS cluster promoted as primary and to access the new writer node and replica node.
- Enable a direct access point to the Flask application to ALB that balances traffic to the secondary region with replicate RDS cluster in case of failure.
- The application must be able to connect and be accessible to the database through DNS/Route 53.
