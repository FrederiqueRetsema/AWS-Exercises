AWS VPC Exercises
=================

This is a collection of "trouble shooting" exercises: in this directory there are CloudFormation (yaml) files, one per exercise. The goal is to be able to connect
to the virtual machine in the private subnet using SSH, then do a yum update and see that the virtual machine is able to do so. Please DON'T use diff or tools
like that (the goal is to learn you AWS, not to learn you use Unix).

The answers can be found in the Solutions subfolder.

You can use these exercises to learn for your Associate level certifications. I expect you to have seen videos or learned from a book about the folowing topics:
- VPC
- Public Subnet
- Private Subnet
- Route table
- Internet Gateway
- NAT Gateway
- NACL
- Security Group
- Bastion host

Example
-------

To help you understand the goal of these exercises, I created one example template to give you a working example and show you what you should be
able to do when you solved the issue in the exercise files. In general: when you cannot find the answer, just re-learn about all the topics that I put in the list above. The more you search, the more you will learn ;-)

1) Logon to the AWS GUI with a valid user-id and password. You need at least privileges for EC2, it would be great to use a test account where 
you have administrator privileges.
2) Go to EC2
3) Create a new key (PEM-format key), with for example the name EC2Key.pem . Please mind that the case of the characters ARE relevant within AWS. 
4) Go to CloudFormation
5) Create a new stack
6) Use the template "ExerciseGoal.yaml"
7) Use all the default parameters (assuming that you created a PEM key with the name EC2Key)
8) When the stack is created successfully, then go to the output tab of CloudFormation. You will see two IP addresses: one public IP address of the Bastion host, one private IP address of the server in the private network.
9) Start a command prompt and go to the directory where your EC2Key.pem is downloaded (f.e. cd c:\Users\Frederique\Downloads)
10) Use the ssh command to log on to the Bastion Host (in this example I'm using public IP address 1.2.3.4):

    `ssh -i EC2Key ec2-user@1.2.3.4`

11) When this is successful, then log off and use the following command to upload your SSH key to AWS [1] 

    `scp -i EC2Key .\EC2Key.pem ec2-user@1.2.3.4:/home/ec2-user/EC2Key.pem`

12) Log on to the Bastion Host and change the permissions on the uploaded key to 400:

    `ssh -i EC2Key ec2-user@1.2.3.4`  
    `chmod 400 EC2Key.pem`

13) Go from the Bastion Host to the virtual machine in the private subnet (will always start with 10.x.x.x, f.e. 10.11.12.13) and use `yum update -y` to install all updates (when you don't get timeouts and don't get errors, everything is correct):

    `ssh -i EC2Key ec2-user@10.11.12.13`  
    `sudo yum update -y`

[1] You should NEVER do this in a production environment. The only reason to do it here is that it saves me a lot of time explaining how you can set up the environment correctly without uploading that SSH key to AWS within Windows, Linux, Mac. You will enroll these examples only in your test environment anyhow, won't you?

When all goes well, the creation or deletion of a stack should take about 3 minutes. See also the section below on "possible issues".

Example output:
---------------

C:\Users\FRETSEM69\Downloads>`ssh -i EC2Key.pem ec2-user@54.78.163.165`  
The authenticity of host '54.78.163.165 (54.78.163.165)' can't be established.  
ECDSA key fingerprint is SHA256:NjczqJIJvLgIOa9NY+QlWloMtjpaR8q610D2NecnJK8.  
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes  
Warning: Permanently added '54.78.163.165' (ECDSA) to the list of known hosts.  
  
       __|  __|_  )  
       _|  (     /   Amazon Linux 2 AMI  
      ___|\___|___|  

https://aws.amazon.com/amazon-linux-2/  
[ec2-user@ip-10-0-0-58 ~]$ `exit`
logout  
Connection to 54.78.163.165 closed.  
  
C:\Users\FRETSEM69\Downloads>`scp -i EC2Key.pem .\EC2Key-fra-dev.pem ec2-user@54.78.163.165:/home/ec2-user/EC2Key.pem`   
EC2Key.pem                                    100% 1674    51.5KB/s   00:00  

C:\Users\FRETSEM69\Downloads>`ssh -i EC2Key.pem ec2-user@54.78.163.165`    
Last login: Fri Jan 14 12:55:58 2022 from 1.2.3.4  
  
       __|  __|_  )  
       _|  (     /   Amazon Linux 2 AMI  
      ___|\___|___|  
  
https://aws.amazon.com/amazon-linux-2/  
[ec2-user@ip-10-0-0-58 ~]$ `chmod 400 EC2Key.pem`  
[ec2-user@ip-10-0-0-58 ~]$ `ssh -i EC2Key.pem ec2-user@10.0.10.211`  
The authenticity of host '10.0.10.211 (10.0.10.211)' can't be established.  
ECDSA key fingerprint is SHA256:vjLUu7UJAoKkx4YnFFnIc5nNtKtTKynJtowW9gLVokQ.  
ECDSA key fingerprint is MD5:98:bf:3b:92:a6:ae:67:4c:5f:03:8b:39:99:cc:cc:e9.  
Are you sure you want to continue connecting (yes/no)? yes  
Warning: Permanently added '10.0.10.211' (ECDSA) to the list of known hosts.  
  
       __|  __|_  )  
       _|  (     /   Amazon Linux 2 AMI  
      ___|\___|___|  
  
https://aws.amazon.com/amazon-linux-2/  
[ec2-user@ip-10-0-10-211 ~]$ `sudo yum update -y`  
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd  
amzn2-core                                            | 3.7 kB  00:00:00  
amzn2extra-docker                                     | 3.0 kB  00:00:00  
(1/5): amzn2-core/2/x86_64/group_gz                   | 2.5 kB  00:00:00  
(2/5): amzn2-core/2/x86_64/updateinfo                 | 432 kB  00:00:00  
(3/5): amzn2extra-docker/2/x86_64/primary_db          |  86 kB  00:00:00  
(4/5): amzn2extra-docker/2/x86_64/updateinfo          | 4.7 kB  00:00:00  
(5/5): amzn2-core/2/x86_64/primary_db                 |  59 MB  00:00:01  
No packages marked for update  
[ec2-user@ip-10-0-10-211 ~]$  
  
  
Possible issues
---------------

All exercise templates should deploy without issues. When you delete a CloudFormation stack and it takes forever to delete the resources,
then go to the VPC section of AWS and delete the VPC manually. You might have changed settings or added other resources (f.e. a Security Group)
to a VPC, in that case CloudFormation will not delete your VPC.

You might also see this warning, you can ignore this. Acknowledge the warning by ticking the box on the left and then continue creating the stack.
![Warning](https://frpublic2.s3.eu-west-1.amazonaws.com/GitImages/TemplateHasChanged.png)