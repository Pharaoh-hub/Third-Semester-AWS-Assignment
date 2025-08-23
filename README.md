CloudLaunch – AltSchool Cloud Engineering Project

This project, called CloudLaunch, was completed as part of my AltSchool Africa Cloud Engineering assignment. It demonstrates knowledge of AWS services such as S3, IAM, and VPC networking. The work is divided into two major tasks:

Hosting a static website using Amazon S3 and applying IAM restrictions.

Designing and configuring a Virtual Private Cloud (VPC) with subnets, route tables, and security groups.

All steps were done inside my AWS Free Tier account.

Task 1 – Static Website Hosting with S3 and IAM
Buckets Created

cloudlaunch-site-bucket-pharaoh

Purpose: To host a public static website.

Configuration: Static website hosting enabled with an index.html file uploaded.

Permissions: Public read access to the objects.

cloudlaunch-private-bucket-pharaoh

Purpose: To store private files.

Configuration: Block public access enabled.

Permissions: Accessible only to a specific IAM user, not to the public.

cloudlaunch-visible-only-bucket-pharaoh

Purpose: To demonstrate a bucket that can be listed but not read.




Website

A very simple HTML file was uploaded to the site bucket with the following content:

<!DOCTYPE html>
<html>
<head>
  <title>CloudLaunch</title>
</head>
<body>
  <h1>Hello, Welcome to CloudLaunch</h1>
  <p>This is a test static site hosted on Amazon S3.</p>
  <p>By Oladoye Toyeeb Olaoluwa</p>
</body>
</html>


Website URL:
http://cloudlaunch-site-bucket-pharaoh.s3-website-us-east-1.amazonaws.com

When opened in a browser, this displays a welcome page with my name at the bottom.





IAM User and Policy

User created: cloudlaunch-user

Access: The user is restricted with an inline custom policy.

Password: Console login enabled with a custom password.

Permissions Summary:

Can ListBucket on all three buckets.

Can GetObject/PutObject inside cloudlaunch-private-bucket-pharaoh.

Can only GetObject from cloudlaunch-site-bucket-pharaoh.

Cannot access objects in cloudlaunch-visible-only-bucket-pharaoh.

Denied permission to delete objects from any bucket.

Given ec2:DescribeVpcs for read-only visibility of VPC details.
This ensures least privilege access and meets the project requirement of restricting the user.



Task 2 – VPC Design
VPC

Name: cloudlaunch-vpc

CIDR Block: 10.0.0.0/16

Purpose: To provide a secure network foundation for the application.

Subnets
Name	CIDR Block	Type	Purpose
cloudlaunch-public-subnet	10.0.1.0/24	Public	For load balancers or other resources that need internet access
cloudlaunch-app-subnet	10.0.2.0/24	Private	For application servers that process business logic
cloudlaunch-db-subnet	10.0.3.0/28	Private	For databases, isolated from the internet

Each subnet was mapped to its own Availability Zone to support high availability.

Route Tables

cloudlaunch-public-rt

Routes internet traffic (0.0.0.0/0) to the Internet Gateway.

Associated with the public subnet.

cloudlaunch-app-rt

Private routing, no internet gateway.

Associated with the app subnet.

cloudlaunch-db-rt

Private routing, no internet gateway.

Associated with the database subnet.

Internet Gateway

Name: cloudlaunch-igw

Attached to the VPC.

Provides internet access for the public subnet only.

Security Groups

cloudlaunch-app-sg

Allows inbound HTTP (port 80) only from within the VPC.

Used by application servers.

cloudlaunch-db-sg

Allows inbound MySQL (port 3306) only from the App Subnet.

Protects the database from external exposure.

This separation follows best practices of keeping the database private and only reachable by the application.

IAM Policy JSON

Here is the full inline policy used for the IAM user:

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:ListAllMyBuckets",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": [
        "arn:aws:s3:::cloudlaunch-site-bucket-pharaoh",
        "arn:aws:s3:::cloudlaunch-private-bucket-pharaoh",
        "arn:aws:s3:::cloudlaunch-visible-only-bucket-pharaoh"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::cloudlaunch-site-bucket-pharaoh/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::cloudlaunch-private-bucket-pharaoh/*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::cloudlaunch-visible-only-bucket-pharaoh/*"
    },
    {
      "Effect": "Deny",
      "Action": "s3:DeleteObject",
      "Resource": [
        "arn:aws:s3:::cloudlaunch-site-bucket-pharaoh/*",
        "arn:aws:s3:::cloudlaunch-private-bucket-pharaoh/*",
        "arn:aws:s3:::cloudlaunch-visible-only-bucket-pharaoh/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": "ec2:DescribeVpcs",
      "Resource": "*"
    }
  ]
}

Conclusion

This project covered:

Hosting a static site on S3 with public access.

Creating three buckets with different visibility rules.

Designing and attaching an inline IAM policy with least privilege.

Building a VPC with subnets, route tables, internet gateway, and security groups.

It demonstrates the ability to use AWS core services securely and effectively.





Configuration: User can see the bucket exists but cannot open or download objects inside.
