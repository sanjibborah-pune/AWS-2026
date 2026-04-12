# 🧪 AWS Lab:Attack Detection Setup (End-to-End)

<img width="1827" height="717" alt="image" src="https://github.com/user-attachments/assets/319aefa7-2383-44c1-ae51-0458b56bf3ac" />


## 🎯 Objective

Build a real-time attack detection system in AWS using:

* CloudTrail (API logging)
* CloudWatch (monitoring + alarms)
* SNS (notifications)
* GuardDuty (threat detection)

---

## 🏗️ Architecture Overview

1. CloudTrail logs API activity
2. Logs sent to CloudWatch Logs
3. Metric Filters detect suspicious patterns
4. CloudWatch Alarms trigger alerts
5. SNS sends notifications
6. GuardDuty provides intelligent threat detection

---

## 🔹 Step 1: Create S3 Bucket (CloudTrail Logs)

1. Go to **S3**
2. Click **Create bucket**
3. Name: `cloudtrail-logs-sanjib`
4. Keep default settings
5. Click **Create bucket**

---

## 🔹 Step 2: Enable CloudTrail

1. Go to **CloudTrail**
2. Click **Create trail**
3. Trail name: `sanjib-trail`
4. Apply trail to: **All regions**
5. Storage: Select created S3 bucket
6. Enable:

   * Management events (Read + Write)
7. Enable **CloudWatch Logs**

   * Create new IAM role (auto)

Click **Create trail**

---

## 🔹 Step 3: Verify Logs

1. Go to S3 → your bucket
2. Check for log files (may take a few minutes)

---

## 🔹 Step 4: Simulate Attack Activity

### Failed Login Simulation

1. Open AWS login in incognito mode
2. Enter wrong credentials 3–5 times

---

## 🔹 Step 5: Configure CloudWatch Logs

1. Go to CloudTrail → select trail
2. Ensure **CloudWatch Logs** is enabled
3. Log Group: `cloudtrail-log-group`

---

## 🔹 Step 6: Create Metric Filter

1. Go to **CloudWatch → Logs → Log groups**
2. Select: `cloudtrail-log-group`
3. Click **Create metric filter**

### Filter Pattern:

```
{ ($.eventName = ConsoleLogin) && ($.errorMessage = "Failed authentication") }
```

4. Metric name: `FailedLoginCount`
5. Namespace: `SecurityMetrics`
6. Create filter

---

## 🔹 Step 7: Create SNS Topic

1. Go to **SNS**
2. Click **Create topic**

   * Type: Standard
   * Name: `security-alerts`
3. Create topic

### Add Subscription:

* Protocol: Email
* Enter your email
* Confirm via inbox

---

## 🔹 Step 8: Create CloudWatch Alarm

1. Go to **CloudWatch → Alarms**
2. Click **Create alarm**
3. Select metric:

   * Namespace: `SecurityMetrics`
   * Metric: `FailedLoginCount`

### Configure:

* Threshold: ≥ 3
* Time: 5 minutes

### Actions:

* Send notification → SNS topic (`security-alerts`)

Name: `FailedLoginAlarm`

---

## 🔹 Step 9: Test Alarm

1. Perform 3–5 failed login attempts again
2. Wait 2–5 minutes
3. Check email for alert

---

## 🔹 Step 10: Enable GuardDuty

1. Go to **GuardDuty**
2. Click **Enable**

GuardDuty will now detect:

* Brute force attacks
* Suspicious IP activity
* Data exfiltration

---

## 🔹 Step 11 (Optional): Simulate Security Misconfiguration

1. Launch EC2 instance
2. Configure Security Group:

   * Allow SSH (22) from `0.0.0.0/0`

---

## 🔹 Step 12: Detect Security Group Changes

Create another metric filter:

```
{ ($.eventName = AuthorizeSecurityGroupIngress) }
```

Create alarm on this metric for detection.

---

## 🧠 Key Learnings

* CloudTrail captures API activity
* CloudWatch detects patterns using metric filters
* SNS sends real-time alerts
* GuardDuty provides intelligent threat detection

---

## 🚨 Real-World Scenario

If attacker performs brute-force login:

1. CloudTrail logs failed attempts
2. Metric filter detects pattern
3. Alarm triggers
4. SNS sends alert
5. GuardDuty raises finding

---

## ⚠️ Notes

* CloudWatch Agent is NOT required for this lab
* Logs may take a few minutes to appear
* Always validate alarms by simulating activity

---

## 🚀 Next Steps

* Automate response using Lambda
* Detect IAM privilege escalation
* Integrate Security Hub
* Monitor VPC Flow Logs

---

## ✅ Status

* [ ] CloudTrail Enabled
* [ ] Logs Verified
* [ ] Metric Filters Created
* [ ] Alarm Configured
* [ ] SNS Notification Working
* [ ] GuardDuty Enabled

---
## 🧠 When DO you need CloudWatch Agent?

 You only install it when you want inside-OS metrics/logs, like:

* Memory usage ❌ (not available by default)
* Disk usage ❌
* Application logs (e.g., /var/log/nginx/access.log)

* 👉 Example:

  If interviewer asks: “How do you monitor memory in EC2?”
  Then you say:
* “I install CloudWatch Agent to push OS-level metrics.”

## ⚠️ Brutal Reality Check

If you say in interview:

“We need CloudWatch Agent for alarms”

 ❌ Wrong → shows weak fundamentals

Correct understanding:

* Agent = OS-level monitoring
* CloudWatch = Service-level monitoring + alarms

🔥 This lab demonstrates real-world AWS security monitoring and is highly relevant for interviews.

