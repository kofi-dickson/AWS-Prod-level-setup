# With exercise i am using the AWS console #
## Why this Project:
To teach and learn how to deploy WEBAPP in realtime production aws instance.
## Goal of the Project:
1.To show and learn the importance of how VPC and subnets when it comes to setting up a production instance

2.To know how ALB(Application Load Balancer) works and how to set up one

3.Setting up an Auto-Scaling Group and its importance.

4.Creating a Bastion Host and Private instance and how to SSH into the private instance using the bastion 



# Lets Start the project
First we create a VPC 
- we head to the aws UI and choose create VPC and choose VPC and more for creating VPC and Other networking resources
- with this project we utilize 2 availability zones,2 public subnets and 2 private subnet
- we choose 1 NATGateway per availability zone to enable the private subnets connect to the internet
- we choose none for vpc endpoint for this service
- we click on create VPC.this is how it will look like.
- <img width="1676" height="867" alt="Screenshot 2025-08-19 at 11 39 18" src="https://github.com/user-attachments/assets/7b2607be-ba1a-4fd2-afff-88169bc699eb" />


Secondly we create the instances using auto-scaling-group
- we cant create an auto-scaling group of we dont have a launch template so we first create that, you'd notice its just like creating an ec2 instance
  whiles launching the template, under the network settings only select create Security groups under that the vpc should be the one we just created
  <img width="1676" height="867" alt="Screenshot 2025-08-19 at 11 57 09" src="https://github.com/user-attachments/assets/ec1aa6df-3c92-4dbd-bca0-c28e5338ba3e" />
- then we launch it..
- Now lets go back to the auto-scaling page under the select template launch option we select the template we just created from the drop down
- After we click to head to the next page then select our instance type t2.micro(free tier for our project), vpc and select the two private subnets.
- with group size we select two and set min and max size to 1 and 4 respectively
- now we proceed to create the auto-scale instance after we click on view to confirm if our auto-scaling group is created
- <img width="1676" height="900" alt="Screenshot 2025-08-19 at 13 36 48" src="https://github.com/user-attachments/assets/c88cd0da-da38-4a79-8481-90df49dafbcd" />should look like this.
- now we check out EC2 instance if out private instances are up and running, you remember where we chose 2 at the group size option!! yeah this where we check if we really have two instances created..
  <img width="1676" height="862" alt="Screenshot 2025-08-19 at 14 07 01" src="https://github.com/user-attachments/assets/f1eb985b-0a2d-4d62-be9a-a3ca5f1cb09c" />notice how the running instance are only 2.

Furthermore, we cant access our 2 private instances cause its not opened to the internet so what we do is to use a Bastion Host to access that.
By using bastion host method we can improve the security and control of our infrastructure while providing authorized users with access to private resources/instance.

To do that we;

1.Create an EC2 instance in the same vpc as the two private subnets with the same keypair and enable public ip to be assigned to it with a t2.micro instance type and select same security group,now leave everything else as default then click on create instance
So with bastion host we cant SSH into a private instance if we are in a different vpcs unless both the bastion and private are in the same instance
<img width="1676" height="862" alt="Screenshot 2025-08-19 at 14 20 04" src="https://github.com/user-attachments/assets/65994325-ae91-4e32-babf-736e9734fcba" />we can see our host is created
Now we ssh into the bastion host by opening a terminal on our laptop and copying the public ip of the bastion.. but first we need to secure copy our key pair into our bastion host using this code
In our terminal we CD to where our keypair is stored then we check exactly where we are in the directory
```bash
  CD downloads
  PWD
```
i used downloads cause my keypair is at downloads after i typed PWD and copied the results print we gonna use is to secure copy our key pair..
Now knowing the location of the key pair and lets secure copy
```bash
scp -i /Users/mac/downloads/nu.pem /Users/mac/downloads/nu.pem ubuntu@3.193.127.248:/home/ubuntu
```
will delete this project so theres nothing wrong posting this here but in realtime production we keep all this secret and secure..
After ssh succesfully into the bastion host and checking ```ls``` is the keypair is in the instance now we shh from the bastion to into the private host
<img width="1066" height="862" alt="Screenshot 2025-08-19 at 15 15 38" src="https://github.com/user-attachments/assets/5f529b56-d38b-4c95-a548-491b6ede8c87" />this is the bastion terminal with its public ip
<img width="1066" height="862" alt="Screenshot 2025-08-19 at 15 17 50" src="https://github.com/user-attachments/assets/6053d8c5-8f8f-4b30-9ca8-f9e4b412c259" />now we are in the private host noticed how the ip changed?..YH
From here we will create a html using 
```bash
vi index.html
```
So we are about hosting our first file on a private subnet.. Now we run this python code
```bash
python3 -m http.server 8000
```
<img width="1184" height="693" alt="Screenshot 2025-08-19 at 16 27 05" src="https://github.com/user-attachments/assets/6d6e2fee-05fd-4461-bcb6-5cc849ff5d73" />


Now lets create our Load Balancer, with the load balancer you'd notice we only placed a application into one instance... so what the load balancer does its when traffic goes to the first instance it gives back an response but doesnt give a response when traffic goesto the othe load balancer.
So lets create a Load Balancer..
On the load balancer UI you add a name,select internet-facing,select the vpc and click on the two AZ(availability zone) we created and select the public subnets for both,at the security groups remember we created a security group with shh 22,http 80 and custom tcp 8000(my own descretion)
Now we create a target group to define which instance is accessible,at the available instance option here only select the two private instance and leave out the bastion..And we know we only put an app in one of the instance .
After toggle the create target option..

 Now on our LB page click on the default action drop down and select the target group we just created
 Next click on create LB and wait till the STATE changes from PROVISIONING to ACTIVE...
 <img width="1426" height="862" alt="Screenshot 2025-08-19 at 16 06 31" src="https://github.com/user-attachments/assets/07044325-7eb3-4159-a577-0328e433c200" />

 











