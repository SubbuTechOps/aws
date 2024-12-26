## 1. There are two VPCs, and I want to privately communicate with one EC2 from another VPC?

**Private Communication Between VPCs**: To enable private communication between EC2 instances in different VPCs, you can use **AWS Transit Gateway**. This service acts as a central hub that connects multiple VPCs and on-premises networks, allowing them to communicate privately over AWS's infrastructure. You will need to create a Transit Gateway and attach your VPCs to it, then configure route tables to facilitate the communication.

## **2. What is the Transit Gateway attachment? What is that configuration in the attachment?**

**Transit Gateway Attachment**: A Transit Gateway attachment is a connection between the Transit Gateway and a VPC or VPN. When configuring an attachment, you specify the VPC ID and select the appropriate route table for the attachment. This setup allows traffic to flow between the Transit Gateway and the attached VPC.

## 3. What are the Inline Policy and Managed Policy?

**Inline Policy vs. Managed Policy**:

- **Inline Policy**: This is a policy that is embedded directly within a single IAM role or user. It is tightly coupled with that entity and is deleted if the entity is deleted.
- **Managed Policy**: This is a standalone policy that can be attached to multiple users, groups, or roles. Managed policies can exist independently of any specific IAM entity.

## 4. I have created an Inline Policy and added it to a role, or you can say any of the services; if I delete that service, then that inline policy gets deleted or remains?

**Deleting Inline Policies**: If you delete a service (like an IAM role) to which an inline policy is attached, that inline policy will also be deleted automatically, as it is inherently linked to that specific role or user.

## 5. Have you worked on Route 53? What are the different types of policies there in Route53?

**Route 53 Policies**: In Amazon Route 53, different types of routing policies include:

- **Simple Routing Policy**: Routes traffic to a single resource.
- **Weighted Routing Policy**: Distributes traffic across multiple resources based on assigned weights.
- **Latency Routing Policy**: Routes traffic based on the lowest latency.
- **Failover Routing Policy**: Routes traffic to a primary resource and fails over to a secondary resource if the primary is unavailable.
- **Geolocation Routing Policy**: Routes traffic based on geographic location[6].

## **6. What are the different Load Balancers available in AWS?**

**Types of Load Balancers in AWS**:

- **Application Load Balancer (ALB)**: Operates at the application layer (Layer 7) and routes requests based on content.
- **Network Load Balancer (NLB)**: Operates at the transport layer (Layer 4) and is capable of handling millions of requests per second while maintaining ultra-low latencies.
- **Classic Load Balancer (CLB)**: Supports both Layer 4 and Layer 7 but is being phased out in favor of ALB and NLB.

## 7. In the S3 Bucket, I have an Object; let's say I want to provide public access to a specific object.
In that case, what are the different ways to provide access to that object?

**Providing Public Access to S3 Objects**: To provide public access to a specific object in an S3 bucket, you can:

- Set the object's ACL (Access Control List) to public-read.
- Use bucket policies to allow public access.
- Generate a pre-signed URL for temporary access.

## 8. What mulit-stage Dockerfile?

**Multi-Stage Dockerfile**: A multi-stage Dockerfile allows you to use multiple `FROM` statements in your Dockerfile, enabling you to build your application in stages. This helps reduce image size by allowing you to copy only the necessary artifacts from one stage to another, discarding unnecessary build dependencies in the final image.

## 9. What is the difference between CMD and ENTRYPOINT?

**CMD vs ENTRYPOINT in Dockerfile**:

- **CMD**: Provides default arguments for the ENTRYPOINT command or can be used as a standalone command if ENTRYPOINT is not specified.
- **ENTRYPOINT**: Specifies the command that will always run when the container starts, allowing you to configure your container as an executable.

## 10. In the Dockerfile, I Exposed the application at port 8080, but at the time of running the containers using the docker image, I gave port 8000; then, at what port is it going to run?

**Port Configuration in Docker**: If you expose your application on port 8080 in your Dockerfile but run the container using port 8000, the application will run on port 8080 inside the container. However, it will be accessible externally via port 8000 due to how Docker maps ports during container execution.

## 11. In Kubernetes, let me have two pods, and I want to run them on the same node where I have 20 Nodes on my cluster. So is there any way you can think of to run these pods on the same node?

**Running Pods on the Same Node in Kubernetes**: To ensure two pods run on the same node within a Kubernetes cluster, you can use `nodeSelector` or `affinity` rules in your pod specifications. This allows you to specify node labels that match your desired nodes for pod placement.

## 12. What are the preferred and required rules?

**Preferred vs Required Rules in Kubernetes**:

- **Preferred Rules**: These are soft constraints; Kubernetes tries to schedule pods according to these rules but will still schedule them if they cannot be met.
- **Required Rules**: These are hard constraints; Kubernetes must meet these rules for scheduling; otherwise, it won't place the pod.

## 13. If I want to delete my Pod PV and PVC when the pod is deleted, what Policy do we need to select? What are the different PV policies are there?

**Deleting Pod PV and PVC Upon Pod Deletion**: To ensure that Persistent Volumes (PV) and Persistent Volume Claims (PVC) are deleted when their associated pod is deleted, select the `Delete` reclaim policy for your PV configuration[6]. Different PV policies include `Retain`, `Recycle`, and `Delete`.

**Use Case of Recycle Policy**: The Recycle policy allows for automatic recycling of PVs when they are released from their claims by deleting data on them and making them available for new claims again.

## 14. What is the use case of Recycle Policy?

**Startup Probes vs Liveness Probes in Kubernetes**:

- **Startup Probes**: Used to check if an application within a pod has started successfully; if this probe fails, Kubernetes will restart the pod.
- **Liveness Probes**: Used to check if an application is still running; if this probe fails, Kubernetes will restart the container.

## 15. What are the start-up probes and liveness probes?

**Ingress Controller Implementation**: An Ingress Controller manages external access to services within a Kubernetes cluster, typically via HTTP/HTTPS routing rules defined in Ingress resources. It provides features like SSL termination and path-based routing.

## 16. Have you implemented the Ingress controller? So why do we use this Ingress Controller?

**Deployment Methods**: Deployment can be managed through various methods such as rolling updates using `kubectl apply`, Helm charts for package management, or CI/CD tools like Jenkins or GitLab CI for automated deployments.

## 17. How were you doing the deployment part?

**CI/CD Tools Experience**: Common CI/CD tools include Jenkins, GitLab CI/CD, CircleCI, and AWS CodePipeline, which automate testing and deployment processes across environments.

## 18. How can you roll back to a previous version for the deployment in Jenkins Pipeline?

**Rollback in Jenkins Pipeline**: To roll back to a previous version in Jenkins Pipeline, you can use the `rollback` command within your deployment scripts or revert changes in your version control system and trigger redeployment from there.

## 19. What is the command you use for this rollback using Helm Charts?

**Helm Chart Rollback Command**: The command used for rollback using Helm Charts is:

```bash
helm rollback <release-name> <revision>
```

This command reverts your release back to a specified revision number.

## 20. How did you do the Monitoring Part?

**Monitoring Implementation**: Monitoring can be implemented using tools like Prometheus for metrics collection and Grafana for visualization of those metrics across applications and infrastructure components.

## 22. Which Exporter you have used in Prometheus and Grafana?

**Prometheus Exporters Used**: Common exporters include Node Exporter for system metrics, Kube-state-metrics for Kubernetes state metrics, and custom application exporters tailored for specific applications.

## 23. Using Kubestate Exporter, what metrics will you get?

**Metrics from Kubestate Exporter**: Using Kubestate Exporter provides metrics related to Kubernetes objects such as deployments, replicasets, pods status, resource usage metrics like CPU and memory utilization among others.

## 24. I want to create Infrastructure on AWS for multiple environments by using the same terraform script. How can we do that?

**Creating Infrastructure with Terraform for Multiple Environments**: To create infrastructure on AWS for multiple environments using the same Terraform script, utilize variables or workspaces that allow parameterization of environment-specific configurations within your Terraform codebase.

## 25. Are you aware of terraform workspaces?

**Terraform Workspaces Awareness**: Terraform workspaces allow you to manage different states within the same configuration directory by creating isolated environments (e.g., development vs production) without duplicating code.

## 26. What is the Terraform Backend?

**Terraform Backend Configuration**: The Terraform backend defines where Terraform's state data is stored (e.g., local disk vs remote storage solutions like S3). It enables collaboration among teams by maintaining consistent state across different environments[6].
