# AWS Auto Scaling Project: CPU Stress Test and Scaling

## Overview

This project demonstrates how to configure AWS Auto Scaling for an EC2 instance with CPU stress testing. We will simulate high CPU utilization using the `stress` tool, monitor the instance's performance via CloudWatch, and configure Auto Scaling to trigger actions based on the CPU utilization.

## Steps Followed

### 1. **Launch EC2 Instance**

- **Launch an EC2 instance** using an appropriate instance type (e.g., `t2.micro` for testing or `t2.medium` for more robust performance).
- Install the **Apache HTTP Server (httpd)** and configure your web page to be served on the instance.

  ```bash
  sudo yum install -y httpd
  sudo systemctl start httpd
  sudo systemctl enable httpd
  echo "Your Web Page Content" > /var/www/html/index.html
  ```

- Confirm that the web server is running by accessing the public IP or DNS of the instance in a browser.

### 2. **Create an Auto Scaling Group (ASG)**

- Navigate to the **EC2 > Auto Scaling Groups** section in the AWS Management Console.
- Create a new **Auto Scaling Group** and associate it with an existing **Launch Configuration** or **Launch Template**.
- **Set health check type** to **EC2** (or **ELB** if using a load balancer).
- **Set Health Check Grace Period** to 300 seconds (or 60 seconds if you need faster scaling reactions).
- Choose an appropriate **VPC**, **Subnets**, and **Availability Zones** for redundancy.

### 3. **Install and Run Stress Test on EC2 Instance**

- SSH into your EC2 instance and install the **stress** tool.

  ```bash
  sudo yum install -y stress
  ```

- **Run a stress test** to simulate high CPU usage. We used 8 CPU workers to put a significant load on the instance for 5 minutes (300 seconds):

  ```bash
  stress --cpu 8 --timeout 300
  ```

  This command initiates CPU stress by launching 8 worker threads that simulate high CPU load.

### 4. **Set Up CloudWatch Monitoring**

- Create **CloudWatch Alarms** to monitor the `CPUUtilization` metric for your EC2 instance.
- **Configure the CloudWatch Alarm**:
  - Set the alarm to trigger if **CPUUtilization** exceeds a threshold (e.g., 80% or higher) for a specified period (e.g., 5 minutes).
  - Attach the alarm to your **Auto Scaling Group** to trigger scaling actions when the CPU utilization exceeds the set threshold.

  Example Alarm Condition:
  - **Metric**: CPUUtilization
  - **Threshold**: Greater than 80%
  - **Period**: 5 minutes
  - **Statistic**: Average

### 5. **Configure Auto Scaling Actions**

- **Set scaling policies** to define how your Auto Scaling Group should behave under high CPU usage.
  - **Scale Up Policy**: Automatically add EC2 instances if CPU usage exceeds 80% for 5 minutes.
  - **Scale Down Policy**: Automatically remove EC2 instances if CPU usage drops below 40% for 5 minutes.

  Example Auto Scaling Policies:
  - **Scale Up**: Add 1 instance if CPU exceeds 80% for 5 minutes.
  - **Scale Down**: Remove 1 instance if CPU falls below 40% for 5 minutes.

### 6. **Health Check Grace Period Adjustment**

- Set the **Health Check Grace Period** to 300 seconds to ensure the instance is allowed time to initialize properly after launch.
- After the grace period ends, Auto Scaling starts considering the instance’s health and will trigger scaling actions based on the defined policies.

### 7. **Monitor CloudWatch Metrics and Test Scaling**

- **Run the stress test** again on your EC2 instance.
  - Use the `top` or `htop` command to check CPU utilization on the instance.
  - Monitor the **CloudWatch metrics** to see if the CPU utilization spikes during the stress test.

- **Check Auto Scaling Reaction**:
  - After the grace period, if the CPU utilization exceeds the threshold (e.g., 80%), the Auto Scaling Group should automatically **launch new EC2 instances** to handle the load.
  - Similarly, if CPU utilization falls below the threshold, the Auto Scaling Group will **terminate excess EC2 instances**.

### 8. **Adjust Scaling Parameters**

- If necessary, adjust the **Health Check Grace Period** or modify the **scaling policies** to fine-tune the response time.
- **Re-run the stress test** to verify that scaling actions are triggered correctly based on the new parameters.

### 9. **Verify Instance Health**

- **Verify EC2 instance health** after Auto Scaling triggers any scaling actions. Instances that are deemed "unhealthy" based on the health checks will be replaced by new instances.
- Use the **CloudWatch Alarm** to verify that the alarm conditions are being met and actions are being triggered.

---

## Conclusion

In this project, we successfully configured an AWS Auto Scaling Group to handle high CPU utilization using the `stress` tool to simulate load. We set up CloudWatch monitoring, adjusted the Health Check Grace Period, and configured scaling policies to add or remove EC2 instances based on CPU usage. 

This approach ensures that the infrastructure can scale up or down dynamically based on demand, improving cost efficiency and application performance.

---

This **README.md** should cover everything you've done in a step-by-step manner. Feel free to adjust or add any details as necessary. Let me know if you'd like me to clarify any part! 😊
