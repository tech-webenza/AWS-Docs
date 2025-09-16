# Steps to Set AWS Alerts (Disk, CPU, Memory)

## 1. Prerequisites
- AWS Account with appropriate IAM permissions (`CloudWatchFullAccess`, `AmazonSNSFullAccess`, `EC2ReadOnlyAccess`).
- EC2 instances with **CloudWatch Agent** installed and configured (for memory and disk metrics).
  - By default, EC2 reports only **CPUUtilization**.  
  - For memory and disk usage, you must install and configure the **CloudWatch Agent**.

---

## 2. Install and Configure CloudWatch Agent (for Disk & Memory)
1. Connect to your EC2 instance via SSH.
2. Install CloudWatch Agent:
   ```bash
   sudo yum install amazon-cloudwatch-agent -y     # Amazon Linux
   sudo apt-get install amazon-cloudwatch-agent -y # Ubuntu/Debian
   ```
3. Configure agent:
   ```bash
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
   ```
   - Select options for **Memory** and **Disk (used_percent)**.
   - Save the configuration file.
4. Start the agent:
   ```bash
   sudo systemctl enable amazon-cloudwatch-agent
   sudo systemctl start amazon-cloudwatch-agent
   ```

---

## 3. Create an SNS Topic (for email notifications)
1. Go to **AWS Console → SNS → Topics → Create topic**.
2. Choose type **Standard**.
3. Enter a **Topic name** (e.g., `EC2_Alerts`).
4. Click **Create topic**.
5. Under **Subscriptions**, create a subscription:
   - Protocol: **Email**  
   - Endpoint: your email (e.g., `yourname@example.com`)  
6. Check your email and **Confirm subscription**.

---

## 4. Create CloudWatch Alarms

### a) CPU Utilization
1. Go to **AWS Console → CloudWatch → Alarms → All alarms → Create alarm**.
2. Select **EC2 metrics → Per-Instance Metrics → CPUUtilization**.
3. Set condition:
   - Threshold type: **Static**  
   - Condition: `Greater than 80%` for 5 minutes  
4. Set action:
   - Send notification to **SNS topic (EC2_Alerts)**.
5. Name the alarm: `EC2_CPU_Usage_Alarm`.

---

### b) Memory Utilization
1. Go to **CloudWatch → Alarms → Create alarm**.
2. Select **CWAgent → InstanceId, ImageId, InstanceType → mem_used_percent**.
3. Set condition:
   - Threshold: `Greater than 80%` for 5 minutes  
4. Set action:
   - Send notification to **SNS topic (EC2_Alerts)**.
5. Name the alarm: `EC2_Memory_Usage_Alarm`.

---

### c) Disk Space Usage
1. Go to **CloudWatch → Alarms → Create alarm**.
2. Select **CWAgent → InstanceId, ImageId, InstanceType → disk_used_percent**.
   - Example resources: `/`, `/home`, `/var`  
3. Set condition:
   - Threshold: `Greater than 80%` for 5 minutes  
4. Set action:
   - Send notification to **SNS topic (EC2_Alerts)**.
5. Name the alarm: `EC2_Disk_Usage_Alarm`.

---

## 5. Verify Alarms
- Go to **CloudWatch → Alarms → All alarms**.  
- Check the status:
  - **OK** → usage is below threshold.  
  - **ALARM** → usage is above threshold → you’ll receive an email alert.  

---

## 6. Best Practices
- Use **different SNS topics** for different teams (DB, DevOps, App).  
- Use **Composite Alarms** if you want a single alarm for multiple conditions.  
- Use **AWS Budgets** to monitor cost alongside resource usage.  

---

✅ Now your EC2 will send alerts for **CPU, Memory, and Disk usage** directly to your email.  
