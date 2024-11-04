# aws-learner

# Word Frequency app Architecture

Overview
--------
Word Frequency is a sample service built with AWS SDK for Go.
The service takes advantage of Amazon EC2 service, Amazon Simple Storage service, Amazon Simple Queue Service and Amazon DynamoDB to collect and report the top 10 most common words of a text file.
The original sample app released by AWS is here: https://github.com/aws-samples/aws-go-wordfreq-sample

A - Launching the EC2 Development Instance

B - Create the S3 Buckets : 
The uploading S3 Bucket is used for uploading text files from your local machine and storing those files.
The files will be copied to your processing S3 bucket. The bucket will have upload notifications enabled, such that when a file is uploaded, a message notification is   automatically added to a wordfreq SQS queue and an email will be sent to you.
  
C - Create the SQS Queues : JSON policy to allow respective AWS entity to write to the queue, not just the queue owner
"Principal": {
        "AWS": "<12 digits>"
      },
Create a second SQS queue (for file processing results)

D - Create the Amazon SNS Topic :
Create a subscription for the Jobs queue, Create a second subscription to send an email

E - Configure the File Copied notification from Bucket to SNS
You will receive an email if you've successfully created event notification.

F - Log in to the Dev Instance and Setup the Environments
Install AWS CLI, Install and Configure go language

G - Copy the Application Code zip onto the Dev Instance
H - Set up and Configure the WordFreq App : Edit the 'run_worker.sh' and 'run_upload.sh' scripts to refer to the correct SQS queue URLs
I - Test the Worker and Upload functionality
J - Setting up the Worker Service
K - Upload files to your S3 uploading bucket
L - Copy files to your S3 processing bucket to start processing them : Observe the processing log as the application begins to process the files:
sudo journalctl -f -u wordfreq



# Architecture
--------
High level overview of components used: 
• In each region a VPC for a private network and multiple AZ to increase availability of our 
application 
• multiple regions for Disaster Recovery and improved resilience 
• private subnets within each AZ for more secured access to Ec2’s within 
• Backup DB for Dynamo DB where our results reside. 
• Cloud Trail with backup to S3 bucket for log monitoring 
• Cloud Watch for metric value polling and triggering alarm which further invokes autoscaling 
activity. 
• Amazon KMS for encrypt and decrypt keys to protect our data at rest and in transit. 
• IAM for access control to users and AWS resources and AWS Organisations for organised 
access policies for multiple accounts 
•  S3 multi- region access point to support multi-region architecture and able upload files from 
any region 
• AWS Cost Explorer and AWS Budgets to monitor and control costs. 
• AWS auditing and reporting for compliance and reporting requirements.


# Main Features

1. Availability and Fault Tolerance
  1.1 Multi-AZ in a Region: Autoscaling across multiple Availability Zones (AZs) with distributed EC2 instances ensures application availability, even if one AZ experiences issues.
  1.2 Multi-Region Disaster Recovery: Standby region enabled with S3 multi-region access points and DynamoDB global tables for seamless failover. Regular EBS snapshots and AMI replication across regions support rapid DR.


2. Long-Term Data Backup
  2.1 DynamoDB Global Tables: Automated backups and cross-region syncing allow consistent data access.
  2.2 S3 Versioning and Backup: S3 versioning ensures data recovery during crises, with intelligent tiering and S3 Glacier options for efficient long-term storage.

3. Cost-Efficiency for Occasional Use
  3.1 Lambda Serverless: Event-driven Lambda minimizes costs with per-second billing, avoiding the need for always-on EC2 instances.
  3.2 On-Demand & Spot Instances: On-demand instances provide flexibility for unpredictable workloads, while discounted Spot instances offer cost savings when immediate processing is not critical.

4. Security and Access Control
  4.1 IAM & AWS Organizations: Centralized access management with granular control, supporting multi-account setups and MFA for secure user access.
  4.2 VPC with Private Subnets: Each region includes a VPC with private subnets, restricting public access and securing EC2 instances.
  4.3 Security Groups: Configured per subnet, controlling inbound/outbound traffic. SSH access allowed per security policy.
  4.4 AWS KMS: Centralized key management for encryption in transit and at rest, with cross-region key replication for secure multi-region functionality.



