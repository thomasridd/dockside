# Dockside
This tutorial  is written for devs who can write code and run it locally but get lost when it comes to deployment. 

I'm going to walk through how to take some locally running code and deploy it as a containerized web app to the cloud.  

We kick off by doing a walkthrough of a walkthrough of this really good blog post from Chris Barclay on the AWS blog
[Set up a build pipeline with Jenkins and Amazon ECS](https://blogs.aws.amazon.com/application-management/post/Tx32RHFZHXY6ME1/Set-up-a-build-pipeline-with-Jenkins-and-Amazon-ECS)

> Requirements
> - Github account
> - AWS account

## Getting started

####1. Github
Chris begins by getting us to [fork a repo](https://help.github.com/articles/fork-a-repo/) for an example project he has up on GitHub [here](https://github.com/awslabs/py-flask-signup-docker). It's Python but language isn't important so fork the project and continue.

> You are likely to already have your app but we know this works so lets roll with it for now.

####2. Setup a server
Chris's next instructions are to set an AWS instance running

> For our build environment we’ll launch an Amazon EC2 instance using the Amazon Linux AMI and install and configure the required packages. Make sure that the security group you select for your instance allows traffic on ports TCP/22 and TCP/80.

Simple stuff if you are an old hand but I got lost on this bit so lets go slow.

 Sign in to AWS and go to the [main console screen](https://eu-west-1.console.aws.amazon.com/console/home?region=eu-west-1#)

#####Virtual Private Cloud setup (VPC)

 1. Click **Create New VPC**
 2. Give it a name and the default CIDR block (10.0.0.0/16)
 3. Select your VPC and pick Actions->Edit DNS Hostnames
 4. Select Yes and Save
 5. Click Internet Gateways
 6. Click **Create Internet Gateway**
 7. Give it a name and Save
 8. Select and click Attach to VPC
 9. Select your VPC
 5. Click Subnets
 6. Click **Create New Subnet**
 7. Give it a name and the default CIDR block (10.0.0.0/24)
 8. Select your Subnet and pick Actions->Modify Auto-Assign public IP
 9. Check Yes and Save
 10. Select the **Route Table** tab
 11. Click the link to the current route table
 12. Add a link 0.0.0.0/0 to your Internet Gateway

So to catch up we've created a new virtual network, added a subnet that our server is going to sit on, and hooked it up to the internet.

##### EC2 Server setup

 2. Click EC2 to go to your virtual servers
 3. Click Launch Instance
 4. Click Select next to the  default Amazon Linux AMI
 5. Pick t2.small as the Instance Type  
 12. Click Security
 13. Click Add rule - Fill in protocol TCP, port 80, source 0.0.0.0
 6. Click Launch
 7. Select new keypair, think of a sensible name, and download

####3. Connect to AWS
[Connect to your instance](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-connect-to-instance-linux.html) is the next request from Chris. Again it is really simple stuff for people used to AWS. I had problems. 

This is the terminal command


    $  ssh -i /path/my-key-pair.pem ec2-user@public_dns_name

/path/my-key-pair.pem is your full local path to the keypair downloaded above

public_dns_name can be a problem

1. Go to your EC2 server list 
2. Select your server
3. The description tab at the bottom should list public_dns

If public dns is blank something dodge has happened with the VPC setup. Try [the troubleshooting page from AWS](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/TroubleshootingInstancesConnecting.html#TroubleshootingInstancesConnectionTimeout) or [this answer from Stack Overflow](http://stackoverflow.com/questions/20941704/ec2-instance-has-no-public-dns) or perhaps just begin again