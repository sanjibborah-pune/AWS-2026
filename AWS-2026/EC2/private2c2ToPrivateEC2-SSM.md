# Connect to a private EC2 instance from another private EC2(from our local machine) using SSM to list data in S3

## Steps


1. Create A VPC CIDR 10.0.0.0/16
2. create 2 subnets (private) for EC2 instances. CIDR 10.0.1.0/24 and 10.0.0/24.
3. create
   - private ec2
   - security group for ec2 and Interface endpoints
   - SSM
   - 3 Interface endpoints
4. IAM role
   - SSM
   - for first ec2
5. create second ec2 for ec2 1.
   - create sg for this ec2
   - add inbound rule to allow the sg of the first ec2 on port ssh 22
6. create s3 bucket for the second ec2 to access
   - public access needs to get block
   - s3 gateway enable
   - s3 gateway endpoint
   - add  instance profile (role) and give s3 access 
