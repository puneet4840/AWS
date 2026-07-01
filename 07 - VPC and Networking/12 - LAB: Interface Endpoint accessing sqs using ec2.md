# LAB: Interface Endpoint, accessing SQS using ec2

Using existing setup used for gateway endpoint demo.

### Steps:

- Create SQS queue in the same AWS region (say myqueue).
- Enable “Enable DNS hostnames” and “Enable DNS Support” for the VPC.
- Create Security group for VPC interface endpoint ENI – Inbound HTTPS (443) from EC2-B instance SG.
- Create or modify IAM role for EC2 to allow Amazon SQS permissions and attach it to EC2-B.
- Login (SSH) into EC2-A and from there SSH into EC2-B and try accessing SQS service using CLI (command below. No connectivity).
- Create VPC interface endpoint for SQS - Select Private Subnet and Enable Private DNS.
- From EC2-B, now test the connectivity to SQS by sending some messages.

```aws sqs send-message --queue-url https://sqs.ap-south1.amazonaws.com/<account id>/myqueue --message-body "Hello from AWS nerd"```
