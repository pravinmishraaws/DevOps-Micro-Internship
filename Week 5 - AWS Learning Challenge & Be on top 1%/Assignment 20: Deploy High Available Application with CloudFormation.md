## Assignment 20: Deploy High Available Application with CloudFormation

### Objective

In this assignment, you will **deploy a highly available web application on AWS** using **AWS CloudFormation**. This will help you understand how Infrastructure as Code (IaC) can be used to provision complete environments reliably and repeatedly.

---

### Prerequisites (Must Do Before Starting)

Before you begin this assignment, **you must complete the following** to ensure you understand CloudFormation fundamentals:

* **Watch this Playlist** to understand AWS Basics:
  [👉 AWS Mastery](https://www.youtube.com/playlist?list=PLVOdqXbCs7bXTSCuuCmyck7ShTY5OevQn)

* **Watch this specific CloudFormation lecture** to understand IaC and CloudFormation concepts:
  [👉 Introduction to CloudFormation](https://youtu.be/bDAw5g5YaKY)

---

### Task Instructions

1. **Watch the Full Hands-On Video**

   * Go to the following video where I demonstrate the full process:
     [👉 Deploy High Available Application with CloudFormation](https://youtu.be/WuXjHkF31YQ)

2. **Replicate the Setup in Your AWS Account**

   * Create the required resources using the provided CloudFormation template as shown in the video.
   * Ensure the following resources are created and configured:

     * VPC with Public and Private Subnets across 2 Availability Zones
     * Internet Gateway & NAT Gateway
     * Security Groups
     * EC2 instances in an Auto Scaling Group (for high availability)
     * Application Load Balancer
     * Target Group and Health Checks
   * Verify your application is accessible via the Load Balancer DNS name.

3. **Test the High Availability Setup**

   * Stop/terminate one EC2 instance and confirm the Load Balancer routes traffic to the remaining healthy instance.

4. **Capture Evidence**

   * Take **3-5 screenshots** showing:

     * CloudFormation stack creation complete
     * Resources created (VPC, Subnets, EC2, ALB)
     * Application working from browser
     * Load Balancer DNS URL
     * EC2 Auto Recovery when one instance is stopped

---

### Submission Guidelines

* Upload this file using the [assignment submission form](https://docs.google.com/spreadsheets/d/1Y3d0Wvil2wDRQK3ANS9WqkQYMviQb0fHHfftEiPwtXQ/edit?gid=0#gid=0).

### Tips

* Make sure to delete all resources after completion to avoid unexpected AWS charges.
* Use the same resource naming as shown in the video for clarity.
* If you get stuck, re-watch the video carefully and pause at each step.
