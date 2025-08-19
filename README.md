<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-monitoring)

**Author:** gazzok@gmail.com  
**Email:** gazzok@gmail.com

---

![Image](http://learn.nextwork.org/happy_green_gentle_spider/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!

In this project I will be setting up a monitoring system with AWS CloudTrail, CloudWatch and SNS! 

I'm doing this project to showcase the essential skill for roles like Security Engineer, DevOps Engineer, and Systems or Cloud Administrator.

### Tools and concepts

Services I used were: 
AWS Secrets Manager
AWS CloudTrail
Amazon CloudWatch
Amazon SNS
Amazon S3
AWS CLI



### Project reflection

This project took me approximately 2 hours. 

---

## Create a Secret

AWS Secrets Manager helps you protect secrets, which are passwords, API keys, credentials and sensitive information. Instead of storing important credentials in your code (yikes!) or sharing them via email (double yikes!), you can tuck them safely away in Secrets Manager.

In my project, I'm just storing a dummy secret, but in real life, this is where you'd keep database passwords, API keys, and other sensitive information that would cause a major headache if they leaked.

To set up for my project, I created a secret called 'TopSecretInfo' that contains information that should stay secret and have the access to it monitored.

![Image](http://learn.nextwork.org/happy_green_gentle_spider/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

AWS CloudTrail is a monitoring service. It documents every action taken, like who did what, when they did it, and where they did it from.

This continuous recording is valuable for security (spotting unusual activity), troubleshooting (figuring out what changed when something breaks), and meeting compliance requirements (proving you're following the rules).

I set this up in order to monitor all access to my secret. 

CloudTrail events include types like Management Events, Data Events, Insights Evenets & Network Activity Events. 

### Read vs Write Activity

Read API activity happens when someone views but doesn't change anything. For example, listing your S3 buckets, describing your EC2 instances, or in my project, viewing (but not changing) metadata about a secret.

Write API activity occurs when changes happen - creating, deleting, modifying resources, or even retrieving the value of a secret (which is what I want to monitor).

---

## Verifying CloudTrail

I retrieved the secret in two ways: First through the AWS Secrets Manager console and second using the CLI. 

To analyse my CloudTrail events, I visited the CloudTrail console. I found under events history that my secret has been accessed. I can tell this as there is a GetSecretValue event name. 

![Image](http://learn.nextwork.org/happy_green_gentle_spider/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

Amazon CloudWatch Logs is a service that helps you bring together your logs from different AWS services, including CloudTrail, for visibility, troubleshooting, and analysis.

It's especially powerful because once your logs are in CloudWatch, you can create alerts based on specific patterns (such as someone accessing your secret), visualize trends, or trigger automated responses.

For this project, I'm sending my CloudTrail logs to CloudWatch Logs so I can set up alerts when someone accesses my secret.

CloudTrail's Event History is useful for recent events while CloudWatch Logs are better for 1) Setting up alerts and automated responses when specific events happen 2) CloudTrail Event History only keeps events for 90 days, while CloudWatch Logs can store them for as long as you'd like 3) CloudWatch Logs has powerful filtering tools that let us focus on exactly the events we care about.

Metric value is what gets recorded when our filter spots a match in the logs. I'm setting it to 1 so that each time someone accesses my secret, the counter increases by exactly one.

Default value is what gets recorded when my filter doesn't find any matches during a given time period. I'm setting it to 0 so that time periods with no secret access show up as zero on my charts, rather than not showing up at all. This gives me a complete picture - I can see both when access happened AND when it didn't.

![Image](http://learn.nextwork.org/happy_green_gentle_spider/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

A CloudWatch alarm is trigger that alerts us when the threshold is triggered. I set my threshold to 1 so the alarm will trigger the first time the secret is accessed.

I created a SNS topic along the way. A SNS (Simple Notification Service) topic is like a broadcast channel for your notifications. First, you create the channel (topic), then you invite subscribers (such as your email), and finally, you send messages to the topic. SNS automatically delivers that message to all subscribers.

My SNS topic is set to email my personal email address when the secret is accessed. 

AWS requires email confirmation because otherwise the owner of the email address could potentially not have asked for this data but get it sen to them anyway. This email ensures that the recipient of the notification did actually request it. 

![Image](http://learn.nextwork.org/happy_green_gentle_spider/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

To test my monitoring system, I retrieved my secret from the Secrets Manager console. The results were that I did not receive an email notification...

When troubleshooting the notification issues I did the below steps:
1) Checked if CloudTrail recorded the event - it had.
2) Checked if CloudTrail was sending logs to CloudWatch - it was. 
3) Checked if CloudWatch was filtering logs properly - it was. 
4) Check if CloudWatch's alarm was triggering an action - it wasn't! 
 

I changed the configuration setting to sum the number of times the alarm was triggered and set it to send a notification after triggering just one time. 

I initially didn't receive an email as the alarm configuration was set to send the notification if the average of times the secret was accessed was 5 times per minute. The solution was to change this to sum and set it to send the notification if the alarm is triggered just one time. 

---

## Success!

To validate that the monitoring system now works I accessed the secret through the Secrets Manager console again. I received an email notification telling me the secret was accessed! 

![Image](http://learn.nextwork.org/happy_green_gentle_spider/uploads/aws-security-monitoring_ageraergearge)

---

## Comparing CloudWatch with CloudTrail Notifications

---

---
