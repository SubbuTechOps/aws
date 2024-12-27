# AWS Compute: EC2 and Lambda

## **EC2 (Elastic Compute Cloud):**
### **What is it?**
Amazon EC2 is like renting a virtual computer in the cloud. You get to choose the size, capacity, and operating system of your computer, and it runs your applications just like a regular machine.

### **Key Concepts in Simple Terms:**
1. **Scalability**: Need more power? Add more instances (machines). Don’t need them anymore? Shut them down.
2. **Customizability**: Choose exactly how much RAM, CPU, and storage you want for your application.
3. **Pricing Models**:
   - **On-Demand**: Pay only for what you use. Ideal for short-term or unpredictable workloads.
   - **Reserved Instances**: Pay upfront for long-term usage, saving money.
   - **Spot Instances**: Bid for unused capacity at reduced prices—great for flexible, non-critical tasks.
4. **Security**: Secure your instances using key pairs, security groups (firewalls), and encryption.
5. **Elastic Load Balancer (ELB)**: Distribute traffic between multiple instances for high availability.
6. **Auto Scaling**: Automatically increase or decrease the number of EC2 instances based on traffic or application needs.
7. **Instance Types**: Choose based on your workload:
   - General-purpose (e.g., t2.micro)
   - Compute-optimized (e.g., c5.large)
   - Memory-optimized (e.g., r5.large)

### **Real-World Example:**
Imagine you’re running an online store. During sales, your website traffic spikes. EC2 allows you to spin up extra instances to handle the traffic. After the sale ends, you can scale back down to save costs.

---

## **Lambda:**
### **What is it?**
AWS Lambda is the magic of computing without needing to manage servers. Just write your code, tell AWS when to run it, and AWS does the rest.

### **Key Concepts in Simple Terms:**
1. **Serverless**: You don’t worry about servers at all. AWS takes care of everything.
2. **Event-Driven**: Lambda runs your code in response to events. Examples:
   - An image is uploaded to S3.
   - A message arrives in an SQS queue.
3. **Pay-Per-Use**: You only pay for the time your code runs (measured in milliseconds).
4. **Scalability**: Automatically handles thousands of events at once without you lifting a finger.
5. **Runtime Options**: Supports various programming languages like Python, Node.js, Java, and Go.
6. **Integration**: Works seamlessly with AWS services like DynamoDB, S3, and API Gateway.

### **Real-World Example:**
Imagine you’re running a photo-sharing app. Every time a user uploads a picture, you want to resize it. Instead of running a server 24/7 to handle uploads, use Lambda. When the photo is uploaded, Lambda resizes it instantly and shuts down when done, saving costs.

---

## **Comparison of EC2 and Lambda:**
| Feature                | EC2                               | Lambda                           |
|------------------------|------------------------------------|----------------------------------|
| **Control**            | Full control over the server      | Focus only on code              |
| **Scalability**        | Manual or Auto Scaling            | Automatic, event-based          |
| **Pricing**            | Pay for running instances         | Pay per execution               |
| **Use Case**           | Long-running, customizable tasks  | Short, event-driven tasks       |
| **Infrastructure**     | Requires maintenance              | Fully managed                   |

---

## **Use Cases and Practical Scenarios:**
### **Using EC2:**
1. Deploy a web application with full control over configurations.
2. Run machine learning models needing high compute power.
3. Host a traditional backend application with a database and APIs.

### **Using Lambda:**
1. Automatically resize images uploaded to S3.
2. Process payments when a customer checks out in an e-commerce app.
3. Respond to real-time changes, like triggering notifications when database updates occur.

---

### Let me know if you’d like additional details or examples!

