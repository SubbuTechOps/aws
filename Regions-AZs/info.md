AWS Global Infrastructure ensures high availability, fault tolerance, and low latency for applications. Here's a quick overview:

### AWS Regions
AWS has regions all around the world, like us-east-1 or eu-west-3. A region is a cluster of data centers that enables deploying resources close to users. Most AWS services are region-scoped, meaning they operate within a specific region.

### How to Choose an AWS Region
When launching a new application, consider the following:
- **Compliance**: Ensure the region meets data governance and legal requirements. Data stays within a region unless explicitly allowed.
- **Proximity to Customers**: Choose a region close to your users to reduce latency.
- **Available Services**: Not all regions offer the same services or features. Verify service availability.
- **Pricing**: AWS pricing varies by region, so check for cost transparency.

### AWS Availability Zones
Each region has multiple Availability Zones (AZs), usually 3, but the number can range from 3 to 6. Examples include:
- ap-southeast-2a
- ap-southeast-2b
- ap-southeast-2c

Each AZ is one or more data centers with redundant power, networking, and connectivity. They are:
- **Isolated**: AZs are separated to prevent disasters in one AZ from affecting others.
- **Connected**: High bandwidth and ultra-low latency networks connect AZs within a region.

#### Text-Based Structural Diagram of AWS Regions and AZs:
```
AWS Region (e.g., ap-southeast-2)
  |
  |-- Availability Zone 1 (e.g., ap-southeast-2a)
  |      |-- Data Center 1
  |      |-- Data Center 2
  |
  |-- Availability Zone 2 (e.g., ap-southeast-2b)
  |      |-- Data Center 1
  |      |-- Data Center 2
  |
  |-- Availability Zone 3 (e.g., ap-southeast-2c)
         |-- Data Center 1
         |-- Data Center 2
```

![AWS Availability Zones Diagram](attachment:image)

### AWS Data Centers
Physical facilities housing servers and networking equipment, ensuring secure, 24/7 operation for AWS services.

### AWS Edge Locations/Points of Presence
AWS has over **450 Points of Presence** worldwide, including **440+ Edge Locations** and **13 Regional Caches**, spanning **90+ cities** across **48+ countries**. These help deliver content to end users with lower latency and high reliability through services like Amazon CloudFront.

### Tour of the AWS Console
AWS offers both global and region-scoped services to support a wide range of use cases:

#### Global Services
- **Identity and Access Management (IAM)**: Manage user permissions securely.
- **Route 53**: AWS's DNS service for domain routing.
- **CloudFront**: A Content Delivery Network (CDN) for faster content delivery.
- **WAF (Web Application Firewall)**: Protect web applications from malicious traffic.

#### Region-Scoped Services
- **Amazon EC2**: Infrastructure as a Service (IaaS) for virtual server hosting.
- **Elastic Beanstalk**: Platform as a Service (PaaS) for application deployment.
- **Lambda**: Function as a Service (FaaS) for serverless computing.
- **Rekognition**: Software as a Service (SaaS) for image and video analysis.
- **Amazon S3**: Scalable storage service for objects and files.
- **DynamoDB**: Fully managed NoSQL database service.
- **RDS (Relational Database Service)**: Managed SQL database platform.
- **Elastic Kubernetes Service (EKS)**: Managed Kubernetes for containerized applications.

AWS offers over **200+ region-scoped services** designed for various needs such as compute, storage, databases, analytics, and machine learning.

Learn more at [AWS Global Infrastructure](https://infrastructure.aws/).

