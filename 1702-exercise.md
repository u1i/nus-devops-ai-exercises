# Hands-On Exercise: AWS Basic Services and CLI

**Course:** DevOps and Cloud with AI   
**Module:** AWS DevOps - AWS Fundamentals

## Introduction

This exercise gives you hands-on experience with the three foundational AWS services we discussed: IAM (Identity and Access Management), EC2 (Elastic Compute Cloud), and S3 (Simple Storage Service). You'll start by using the AWS Console (web interface) to create resources, then use the AWS CLI to interact with the same services programmatically. By the end, you'll understand how the GUI and command-line are two different ways to interact with the same underlying AWS APIs.

## Prerequisites

* **AWS Account:** You'll need a free AWS account. If you don't have one, visit [aws.amazon.com](https://aws.amazon.com) and click "Create AWS Account." AWS offers a generous free tier for new accounts.
* **Computer:** Any computer with internet access and the ability to install software
* **Email:** A valid email address for AWS account verification

**Cost Note:** Everything in this exercise uses AWS Free Tier resources. However, always clean up resources when finished to avoid any potential charges.

## Part 0: AWS Account Setup

1. **Create Your AWS Account:**
   - Visit [aws.amazon.com](https://aws.amazon.com) and click "Create AWS Account"
   - You'll need to provide payment information (credit card), but we'll only use free tier resources
   - Complete the verification process (phone verification, etc.)

2. **Sign into AWS Console:**
   - Go to [console.aws.amazon.com](https://console.aws.amazon.com)
   - Sign in with your new account credentials
   - You should see the AWS Management Console homepage

**What you're seeing:** The AWS Console is the web-based interface for managing all AWS services. It's the graphical way to interact with the same APIs we'll use from the command line later.

## Part 1: Creating an EC2 Instance (Your Computer in the Cloud)

### Using the AWS Console

3. **Navigate to EC2:**
   - In the AWS Console, find "EC2" in the services menu or search for "EC2"
   - Click on EC2 to open the EC2 Dashboard

**What you're seeing:** The EC2 Dashboard shows you all your virtual machines (instances) across all regions.

4. **Launch an Instance:**
   - Click the orange "Launch Instance" button
   - **Instance Name:** Enter `my-first-ec2`
   - **Application and OS Images:** Select "Amazon Linux 2023 AMI" (or newer, should be marked as "Free tier eligible")
   - **Instance Type:** Select `t2.micro` (also marked as "Free tier eligible")
   - **Key Pair:** Click "Create new key pair"
     - Name: `my-keypair`
     - Key pair type: RSA
     - Private key file format: .pem
     - Click "Create key pair" (this downloads a file - keep it safe!)
   - **Network Settings:** Leave default settings (it will create a security group)
   - **Configure Storage:** Leave default
   - Click "Launch instance"

**What just happened:** You've created a virtual Linux server running in AWS's data center. The key pair is how you'll securely connect to it later (though we won't actually SSH into it in this exercise).

5. **Verify Your Instance:**
   - You should see a success message
   - Click "View all instances" 
   - You'll see your instance with status "Pending" then "Running"

**What you're seeing:** Your virtual machine is starting up. The "Instance State" shows its current status, and "Instance ID" (like `i-0abcd1234efgh5678`) is its unique identifier.

## Part 2: Creating an S3 Bucket (Your Storage in the Cloud)

6. **Navigate to S3:**
   - In the AWS Console, search for "S3" or find it in the services menu
   - Click on S3 to open the S3 Console

**What you're seeing:** S3 shows all your "buckets" (containers for files). If this is a new account, you'll see "You don't have any buckets yet."

7. **Create a Bucket:**
   - Click "Create bucket"
   - **Bucket name:** Enter something unique like `my-devops-bucket-[your-initials]-20250927` (using today's date as an example, bucket names must be globally unique)
   - **AWS Region:** Leave as default (should match your EC2 region)
   - **Block Public Access:** Leave all boxes checked (this keeps your bucket private)
   - **Bucket Versioning:** Leave as "Disable"
   - **Default encryption:** Leave as "Server-side encryption with Amazon S3 managed keys (SSE-S3)"
   - Click "Create bucket"

**What just happened:** You've created a storage container in AWS's object storage system. This bucket can store files of any type and scale to petabytes if needed.

8. **Upload a Test File:**
   - Create a simple text file on your computer called `hello.txt` with content: "Hello from AWS S3!"
   - Click on your bucket name to open it
   - Click "Upload"
   - Drag your `hello.txt` file or click "Add files" to select it
   - Click "Upload"
   - You should see "Upload succeeded"

**What you're seeing:** Your file is now stored in AWS's global storage system and could be accessed from anywhere (though it's private by default).

## Part 3: Installing and Using AWS CLI

### Installing AWS CLI

9. **Install AWS CLI:**
   - **Windows:** Download installer from [AWS CLI Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)
   - **macOS:** Run `brew install awscli` (if you have Homebrew) or download installer
   - **Linux:** Follow the installation guide for your distribution
   - **Alternative for any OS:** Use [AWS CloudShell](https://console.aws.amazon.com/cloudshell) (browser-based terminal with CLI pre-installed)

10. **Verify Installation:**

    ```
    aws --version
    ```
    
    **What you should see:** Something like
    
    `aws-cli/2.15.0 Python/3.11.6 ...`

### Configuring AWS CLI

11. **Create Access Keys:**
    - In AWS Console, click on your account name (top right)
    - Select "Security credentials"
    - Scroll to "Access keys" section
    - Click "Create access key"
    - Select "Command Line Interface (CLI)"
    - Check the acknowledgment box and click "Next"
    - Add a description like "My first CLI access" and click "Create access key"
    - **Important:** Copy both the "Access key" and "Secret access key" - you won't see the secret again!

12. **Configure AWS CLI:**
    
    ```
    aws configure
    ```
    
    **What you'll be prompted for:**
    - **AWS Access Key ID:** Paste your access key
    - **AWS Secret Access Key:** Paste your secret access key  
    - **Default region name:** Enter your region (like `us-east-1` or whatever region you've been using)
    - **Default output format:** Enter `json`

**What just happened:** You've given your command line tools the credentials to access your AWS account programmatically.

## Part 4: CLI Interaction with Your Resources

### Working with S3 via CLI

13. **List your buckets:**
    ```bash
    aws s3 ls
    ```
    
    **What you'll see:** Something like:
    ```
    2024-01-15 10:30:45 my-devops-bucket-abc-2025
    ```
    
    **What this shows:** The CLI is seeing the same bucket you created in the GUI!

14. **List files in your bucket:**
    ```bash
    aws s3 ls s3://your-bucket-name/
    ```
    
    **What you'll see:**
    ```
    2024-01-15 10:35:22         20 hello.txt
    ```
    
    **Amazing!** The CLI can see the file you uploaded through the web interface.

15. **Upload a new file via CLI:**
    ```bash
    echo "Hello from the command line!" > cli-test.txt
    aws s3 cp cli-test.txt s3://your-bucket-name/
    ```
    
    **What you'll see:** 
    ```
    upload: ./cli-test.txt to s3://your-bucket-name/cli-test.txt
    ```

16. **Verify in the GUI:**
    - Go back to your S3 console in the browser
    - Refresh your bucket view
    - You should see both `hello.txt` (uploaded via GUI) and `cli-test.txt` (uploaded via CLI)

**Key Insight:** The GUI and CLI are just different ways to interact with the same AWS services!

### Working with EC2 via CLI

17. **List your EC2 instances:**
    
    ```
    aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name,InstanceType]' --output table
    ```
    
    **What you'll see:**
    
    ```
    |  DescribeInstances |
    +-----------+--------+-----------+
    |i-0abc123  |running |t2.micro   |
    +-----------+--------+-----------+
    ```
    
    **What this shows:** The CLI can see the EC2 instance you created in the GUI, with its Instance ID, current state, and type.

18. **Get detailed information:**
    
    ```
    aws ec2 describe-instances --instance-ids YOUR_INSTANCE_ID
    ```
    
    **What you'll see:** A large JSON response with detailed information about your instance - IP addresses, security groups, launch time, etc. This is the raw data that the GUI uses to display information in a user-friendly format.

## Part 5: Understanding the Connection

**What you've discovered:**

- The AWS Console (web interface) and AWS CLI are two different ways to interact with the same underlying APIs
- When you create resources in the GUI, they're immediately visible via CLI and vice versa
- The CLI shows you the raw JSON data that powers everything you see in the web interface
- This is exactly what we meant when we said "everything in AWS is API-driven"

## Part 6: Cleanup (Important!)

To avoid any potential charges, let's clean up everything we created:

19. **Terminate EC2 Instance:**
    - In EC2 Console, select your instance
    - Click "Instance state" → "Terminate instance"
    - Confirm the termination
    
    **Or via CLI:**
    
    ```
    aws ec2 terminate-instances --instance-ids YOUR_INSTANCE_ID
    ```

20. **Delete S3 Objects and Bucket:**
    - In S3 Console, select all files in your bucket and delete them
    - Then delete the empty bucket
    
    **Or via CLI:**
    
    ```
    aws s3 rm s3://your-bucket-name/ --recursive
    aws s3 rb s3://your-bucket-name/
    ```

21. **Delete Access Keys (Good Security Practice):**
    - Go to AWS Console → Your account → Security credentials
    - Find the access key you created and delete it

**What you've learned:** Proper cleanup is essential in cloud computing to avoid unexpected charges.

## Summary

Congratulations! You've just experienced the fundamental AWS workflow:

- **EC2** provided you with compute power (a virtual server)
- **S3** provided you with storage (a place to keep files)  
- **IAM** handled the security (access keys that let CLI authenticate)
- **AWS CLI** showed you how to programmatically manage the same resources you created in the GUI

This is the foundation that all AWS DevOps practices build upon. Every advanced AWS service ultimately uses these same building blocks, and every DevOps automation tool relies on the same APIs you just used through the CLI.

You now understand how cloud infrastructure can be managed both through point-and-click interfaces and programmatic automation - the first step toward Infrastructure as Code!
