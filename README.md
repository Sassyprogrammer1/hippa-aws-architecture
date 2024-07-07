# hippa-aws-architecture
This repo contains the architecture diagram and a readme file for explanation

# Features

1. Route53: when the user initiates the request it will land on the route53, which basically does the DNS resolution.
2. CloudFront: The request lands on cloudFront from the route53.
3. Certificate Manager: The certificate manager provides secure ssl/tls certificates to the cloudfront.
4. S3: S3 hosts all the static content which included Frontend assets. Here the S3 bucket will be considered as an origin server from which the cloudfront will fetch content.
5. Apigateway: This defines the stages for our API routes and an interface to access them. The cloudfront first interacts with the apigateway when any backend api is called.
6. NetworkLoadBalancer(NLB): The apigateway will call the NLB to access the api endpiont on the ec2 server. This communication takes place throug fix ports. For e.g an nginx web server deployed on ec2 in private subnet can be accessed through a NLB on port 80 
7. VPC: The VPC is the virtual private cloud that has the subnets. Subnets are nothing but some network ranges or CIDR blocks.
8. Public Subnet: These subnets will hold the components that will be accessed openly through the internet.
9. Private Subnet: In the private subnet we will keep the application ec2 and the databases so that these resources can't be accessed from the internet directly.
10. Bastion Host: This will be an ec2 server used to access the rds or the application ec2 for maintainance purposes. This is generally used by developers to do configurations on the application ec2 and rds. The benefit of the bastion host is that you don't expost the application ec2 directly to anyone. You can create an ssh tunnel to the bastion host and access the private resources.
11. Internet Gateway: Internet Gateway will give internet access to natgateway for outbound communication.
12. NatGateway: NatGateway will be used to allow all outbound traffic from the private ec2 instance to the internet.
13. Application EC2: This ec2 will be the main ec2 instance which will contain the backend api.
14. RDS: This will be the main database hosted inside the private subnet only accessable from the application ec2 or the bastion host for maintainance purposes.
15. Security Groups: The security groups will be used on instance level for the ec2 instances to control the inbound and outbound traffic for security reasons.
16. Route Table: Route table will ensure the inbound and outbound traffic at the VPC level.
17. VPC Link: This will be used to establish a communication between the apigateway and the NLB.
18. Lambda: This will be used as a serverless compute for the event driven architecture. For e.g payments. The lambda invoked for the payments will process payments which will in term invoke an sns topic. Also the messages will be polled from the sqs through a lambda.
19. SNS: This will generate messages either success or failure and will send it to the sqs queue for the payment process resolution. 
20. SQS: SQS will keep the messages of SNS for a longer period of time so that each payment request is completed successfully and each subscribed user is notified of either success or failure.
21. Dynamodb: Once the messages are polled from the sqs it can be stored in the dynamodb as transaction details for persistent storage.