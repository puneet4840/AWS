# LAB: Accessing S3 bucket through using VPC Gateway Endpoint

Is lab mein VPC Gateway endpoint ke through s3 bucket ko aws ke private network se hoke ec2 ke through access karenge.

<br>

### Steps

- Create VPC with two subnets – A public and a private subnet.
- Launch an EC2 instance (EC2-A) (bastion host) in the Public subnet (allow SSH for 0.0.0.0/0).
- Launch an EC2 instance (EC2-B) in the Private subnet (allow SSH from SG of bastion host).
- Create IAM role for EC2 instance to allow S3Full permissions. Attach it to EC2-B.
- Login to EC2-A over SSH and from there SSH into EC2-B (you will need .pem key file on EC2-A).
- Try accessing S3 bucket using ```aws s3 list```.
- Create VPC Gateway endpoint for S3 - Select Private subnet.
- Modify Private subnet route table to route traffic to S3 via VPC gateway endpoint.
- Test the connectivity to S3 by uploading/downloading some files from any of your S3 bucket.
- ```aws s3 ls```.
- ```aws s3 cp s3://bucket_name/file_name .```.

