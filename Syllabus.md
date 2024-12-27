# AWS Syllabus for a 5-Year Experienced Candidate

## **1. AWS Basics (Refresher)**
### Objective: Gain a solid foundation and fill knowledge gaps.
- **AWS Global Infrastructure:**
  - Regions, Availability Zones, Edge Locations
  - Understanding Shared Responsibility Model
- **Core Services Overview:**
  - Compute: EC2, Lambda
  - Storage: S3, EBS, EFS, Glacier
  - Networking: VPC, Route 53, Elastic Load Balancing (ELB), CloudFront
  - Database: RDS, DynamoDB, Redshift, Aurora
- **IAM and Security:**
  - IAM Policies, Roles, and Users
  - Best practices for least privilege access
  - Multi-Factor Authentication (MFA)

---

## **2. Compute and Scaling**
### Objective: Master compute resources and scalability.
- **EC2:**
  - Instance types, families, and pricing models (On-Demand, Reserved, Spot)
  - Elastic IPs and Security Groups
  - Auto Scaling and Load Balancing (ALB, NLB, ELB)
- **Lambda:**
  - Event-driven architecture with Lambda
  - Integration with other services (API Gateway, DynamoDB, S3)
  - Advanced configurations (Concurrency, Error handling, Monitoring)
- **Elastic Beanstalk:**
  - Deploying and managing applications
  - Customization with `.ebextensions`
- **Container Services:**
  - ECS (Amazon Elastic Container Service)
  - Fargate
  - EKS (Amazon Elastic Kubernetes Service)

---

## **3. Networking and Content Delivery**
### Objective: Build secure, high-performance network architectures.
- **VPC:**
  - Subnets, Route Tables, NAT Gateways, Internet Gateways
  - VPC Peering and Transit Gateway
  - Security Groups and Network ACLs
- **Elastic Load Balancer (ALB, NLB)**
- **Route 53:**
  - DNS management, failover routing, geolocation routing
  - Domain registration
- **CloudFront:**
  - Caching strategies for low latency
  - Securing distributions with OAC (Origin Access Control)
- **AWS Direct Connect and VPN**

---

## **4. Storage and Database Services**
### Objective: Design scalable storage and database solutions.
- **S3:**
  - Bucket policies, encryption, lifecycle policies
  - S3 Object Lock, Cross-Region Replication (CRR)
- **EBS and EFS:**
  - Use cases, snapshots, performance tuning
- **Glacier:** Deep storage archiving
- **Databases:**
  - RDS (MySQL, PostgreSQL, SQL Server)
  - DynamoDB (NoSQL, Global Tables, Streams)
  - Aurora (Serverless, Multi-master, Cloning)
  - Redshift: Data warehousing
- **Database Migration Service (DMS)**

---

## **5. Security and Identity Management**
### Objective: Secure AWS environments and applications.
- **IAM:**
  - Identity Federation (SSO, SAML)
  - Service Control Policies (SCPs)
- **AWS KMS and Secrets Manager:**
  - Managing and rotating secrets
  - Encryption at rest and in transit
- **AWS WAF and Shield:**
  - Protecting applications against DDoS attacks
- **AWS Config:**
  - Compliance monitoring and auditing
- **Security Hub, GuardDuty, and Inspector**

---

## **6. Monitoring and Observability**
### Objective: Maintain and optimize performance and costs.
- **CloudWatch:**
  - Metrics, Logs, Alarms, Dashboards
  - Log Insights
- **AWS X-Ray:**
  - Tracing for distributed applications
- **Trusted Advisor:**
  - Cost optimization and best practices
- **Cost Management Tools:**
  - AWS Cost Explorer, Budgets, and Billing

---

## **7. DevOps on AWS**
### Objective: Automate workflows and deployments.
- **AWS CodePipeline, CodeBuild, CodeDeploy**
- **Infrastructure as Code (IaC):**
  - CloudFormation and CDK (Cloud Development Kit)
  - Terraform with AWS
- **CI/CD with Jenkins, GitHub Actions, or AWS services**
- **Container Orchestration:**
  - ECS, EKS with CI/CD pipelines

---

## **8. Advanced Topics**
### Objective: Gain expertise in specialized domains.
- **Serverless Architectures:**
  - Building serverless applications
  - EventBridge, Step Functions, AppSync
- **Machine Learning:**
  - SageMaker basics
  - AI services like Rekognition, Polly, and Comprehend
- **Big Data and Analytics:**
  - EMR, Glue, Athena, Kinesis
- **Hybrid Cloud:**
  - Outposts, Snowball, and Storage Gateway
- **Disaster Recovery Strategies:**
  - Cross-Region Replication, Backups, Recovery Plans

---

## **9. Real-World Architectures and Use Cases**
### Objective: Apply AWS knowledge to real projects.
- Architecting multi-tier applications
- Migrating on-premise applications to AWS
- Building data pipelines
- High-availability architectures
- Designing for fault tolerance

---

## **10. Certification Preparation**
### Objective: Validate expertise with industry certifications.
- **AWS Certified Solutions Architect - Associate** (first step)
- **AWS Certified Developer - Associate** (if focused on DevOps or programming)
- **AWS Certified DevOps Engineer - Professional**
- **AWS Certified Solutions Architect - Professional**

---

## Suggested Resources
1. **Official Documentation:** [AWS Documentation](https://docs.aws.amazon.com/)
2. **Training and Labs:** [AWS Training & Certification](https://aws.amazon.com/training/)
3. **Hands-on Practice:** [AWS Free Tier](https://aws.amazon.com/free/)
4. **Books:**
   - *AWS Certified Solutions Architect Study Guide* by Ben Piper
   - *Mastering AWS Security* by Albert Anthony
5. **Community and Forums:** [AWS Developer Forums](https://forums.aws.amazon.com/)

---

## Practical Projects
1. Build a Serverless web application using API Gateway, Lambda, DynamoDB.
2. Create a CI/CD pipeline for a containerized application.
3. Migrate an on-prem database to RDS using DMS.
4. Set up an EKS cluster with monitoring using Prometheus and Grafana.
5. Implement a cost monitoring dashboard using AWS Budgets and CloudWatch.

---

### **Timeline:**
6–12 months of focused study, with periodic evaluations through hands-on projects and mock certification exams.

