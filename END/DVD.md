########################################################################

# GIT and GITHUB

## GIT
- $ sudo apt update 
- $ sudo apt install git -y
- $ mkdir demo
- $ cd demo
- $ git init

## GITHUB
--> Create an New Public Repository

## GIT
- $ nano index.html
    --> <h1> Welcome to IACSD </h1>
- $ git status
- $ git config user.email "your@example.com"
- $ git config user.name "example"

- $ ssh-keygen
- $ cat .ssh/id_rsa.pub
    --> Copy the public key and paste in GitHub 
    --> GitHub --> Settings --> Deploy Key --> SSH --> Tick Permission

- $ git add .
- $ git commit -m "First"
- $ git remote add origin "git@github.com:xyz/project.git" <"URL GITHUB REPO"> 
- $ git push origin master
    --> Now, Check in GitHub --> File is Present or Not 

##########################################################################################

# JENKINS Script through DOCKER

- $ docker pull jenkins/jenkins:lts
- $ docker volume create jenkins_home
- $ docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts
--> Go in Browser --> <http://localhost:8080>
- $ docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
--> Install suggested plugins

###################################################################################

# JENKINS 

--> Launch an new AWS EC2 Instance 
- $ sudo apt update 
- $ nano jenkins.sh
--> 
#!/bin/bash
sudo apt update
sudo apt upgrade -y
sudo apt install fontconfig git default-jre -y
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins -y
systemctl is-active jenkins

- $ sudo chmod u+x jenkins.sh
- $ ./jenkins.sh
- $ systemctl status jenkins  --> Running
    --> Copy the Public IP from AWS EC2 instance and Paste in Browser : 8080 
- $ sudo cat /var/lib/jenkins/secrets/---

### Open Browser 
--> Install Plugins --> Give Password --> Jenkins is Ready

- $ systemctl restart jenkins
- $ systemctl status jenkins  --> Running

--> Select New Item --> Give Name --> Freestyle Project --> Ok

--> General 
    --> Description :
    --> GitHub Repo URL : 
    --> Change Branch name --> master
    --> Triggers --> GitHub hook trigger for GitScm pooling
    --> Environment --> No changes
    --> Build Steps 
        --> Execute Shell --> whoami , pwd , ls -l , echo "Hello" --> Save It
    
--> Go in GitHub --> Settings --> Add Webhooks 
    --> Payload URL - http://public-ip:8080/github-webhook/
    --> Content Type - application/json
    --> Secret ----
    --> SSL Verification : Tick on it 

--> Jenkins Dashboard --> Build Now 
    --> Build Successful --> Console Output --> Green 

#################################################################################

# DOCKER

- $ sudo apt update
- $ sudo apt install docker.io -y 
- $ sudo usermod admin -aG docker 
    --> Note : Make a duplicate session of terminal 
- $ docker pull ubuntu/apache2
- $ docker run --name web -d -p 8888:80 ubuntu/apache2
    --> Search on Web Browser <Public IP:8888>

- $ docker pull nginx
- $ docker run --name nweb -d -p 8001:80 nginx
    --> Search on Web Browser <Public IP:8001>

## If we want to build our own image..!!

- $ mkdir demo
- $ cd demo 
- $ nano index.html
    --> <h1> This is my Web Server </h1>
- $ nano Dockerfile
    --> FROM ubuntu/apache2
        COPY * /var/www/html/

- $ docker build -t myapache .
- $ docker run --name web2 -d -p 8005:80 myapache
- $ docker ps 
    --> Run on Web Browser <Public IP:8005>

###################################################################################

# DOCKER SWARM

--> Note : Required 2 or 3 AWS EC2 instance (Do on all 3 machines)
- $ sudo apt update
- $ sudo apt install docker.io -y 
- $ sudo usermod admin -aG docker 
    --> Note : Make a duplicate session of terminal 

## AWS EC2 - 01 (Manager)
- $ mkdir myproject
- $ cd myproject
- $ nano index.html 
    --> <h1> Implement of Docker Swarm </h1>
- $ nano Dockerfile 
    --> FROM nginx
        COPY * /usr/share/nginx/html/

- [ OR ]

- $ nano Dockerfile
    --> FROM httpd
        COPY * /usr/local/apache2/htdocs/

- $ docker build -t <DockerHub Repo Name:latest> .
- $ docker login 
    --> Username and Password 
- $ docker push <DockerHub Repo Name:latest>
- $ docker swarm init
- $ docker swarm init --advertise-addr <Public IP Server>

## AWS EC2 - 02 and 03 (Worker)
--> Paste the above command in both machines
- $ docker swarm join --token --------- <Public IP Server:2377>

## AWS EC2 - 01 (Manager)
- $ docker service create --help
- $ docker service create --name myservice -p 80:80 -d --replicas=3 prajwal/xyz:latest
- $ docker service ls 
- $ docker service scale myservice=2
- $ docker service rm myservice 
- $ docker service create --name myservice -d -p 80:80 prajwal055/xyz:latest

- $ cd myproject
- $ nano index.html
    --> Update something in new file 
- $ docker build -t prajwal055/xyz:latest .
- $ docker push prajwal055/xyz:latest
- $ docker service update --image prajwal055/xyz:latest myservice 
- $ docker service --help
- $ docker node --help 

--> Note : Check in Browser of both worker machines , it will reflect the changes ...!!! 

########################################################################################

# ANSIBLE 

--> Note : Required 2 or 3 AWS EC2 instance (Do on all 3 machines)
- $ sudo apt update 
- $ sudo apt install ansible -y

## AWS EC2 - 01 

- $ mkdir demo 
- $ cd demo

- $ nano inventory.ini
--> [webservers]
    managed_node1 ansible_host=<Client1 Public IP> ansible_user=admin
    managed_node2 ansible_host=<Client2 Public IP> ansible_user=admin
        
- $ nano install.yml
---
- name: Install apache2 on Debian
  hosts: webservers
  become: yes
  tasks:
    - name: Update apt cache
      ansible.builtin.apt:
        update_cache: yes

    - name: Install apache2
      ansible.builtin.apt:
        name: apache2
        state: present

    - name: Ensure apache2 is running
      ansible.builtin.service:
        name: apache2
        state: started
        enabled: yes

- $ sudo nano /etc/default/locale
    --> LANG="     .UTF-8"
        LC_ALL="      .UTF-8"

- $ sudo locale-gen en_In.UTF-8
- $ sudo update-locale
- $ sudo reboot

--> Copy the id_rsa.pub key from Server and Paste into authorized_keys of Client

- $ cd demo
- $ ansible -i inventory.ini -m ping all
- $ ansible-playbook -i inventory.ini install.yml
    --> Wait for 3-5 mins 

## AWS EC2 - 02 and 03

- $ sudo systemctl status apache2
--> Search on Web Browser <Public IP>

##############################################################################################

# TERRAFORM WITH AWS IAM 

## Login to AWS 
--> Create an new IAM user and group  --> Give full permissions
--> After Creating IAM user --> Copy URL and Open in new alternative Browser 
--> Check Permissions --> Full Control 
--> Check Security Credentials --> Create Access Key --> Copy in Notepad

## Launch an new AWS EC2 instance 
- $ sudo apt update 

- $ nano terraform.sh 

#!/bin/bash
sudo apt install wget -y
sudo apt-get update && sudo apt-get install -y wget gnupg software-properties-common

wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null


gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update
sudo apt-get install terraform -y

sudo apt install -y awscli
aws --version


- $ chmod u+x terraform.sh
- $ ./terraform.sh

- $ aws configure 
    --> Key ID : -----------
        Secret key : -----------
        Default Region : us-east-1
        output : json 

- $ mkdir demo 
- $ cd demo

- $ nano main.tf

provider "aws" {
  region = var.region
}

resource "aws_instance" "debian_ec2" {
  ami                    = var.ami_id
  instance_type          = var.instance_type
  key_name               = var.key_name
  associate_public_ip_address = true
  vpc_security_group_ids = [aws_security_group.allow_ssh.id]

  tags = {
    Name = "Debian-Terraform-EC2"
  }
}

resource "aws_security_group" "allow_ssh" {
  name        = "allow_ssh"
  description = "Allow SSH inbound traffic"

  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]  # Use your IP/CIDR for better security
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}


- $ nano variables.tf

variable "region" {
  default = "us-east-1"
}

variable "ami_id" {
  description = "Debian 12 AMI ID"
  default     = "ami-0779caf41f9ba54f0"  # Debian 12 (Bookworm) in us-east-1
}

variable "instance_type" {
  default = "t2.micro"
}

variable "key_name" {
  description = "Enter a key pair"
}

- $ nano output.tf

output "instance_ip" {
  value = aws_instance.debian_ec2.public_ip
  description = "The public IP address of the instance"
}

output "instance_id" {
  value = aws_instance.debian_ec2.id
  description = "The ID of the instance"
}

- $ terraform --help

- $ terraform init

- $ terraform plan
    --> Enter a key : mykey

- $ terraform apply 
    --> Enter a key : mykey 
        Enter a value : yes

--> Example :- 
    Output: instance_id = "i-03a511---"
            instance_ip = "54.161.250.54"

--> Check in AWS EC2 Instance Dashboard , Instance is created or not ..!!

################################################################################################

# AWS S3 

- Login AWS Console --> Search for S3 --> Create bucket
- General Configuration 
	--> Bucket type : General Purpose
      Bucket Name : ---
      AWS Region : us-east-1
      Choose Bucket : ---

  --> Default Permissions --> Tick on Block all public access
  --> Create bucket (Save) --> Select bucket (Upload)
  --> Copy the URL of bucket and paste in Browser (Access Denied)
  --> Now Select Bucket --> Go in Permission Tab
  --> Edit Block Public Access (Remove Tick) 
  --> Go in ACL --> Edit give public access --> ON --> Save
  --> Click on Actions --> Make Public --> Go in Browser --> Refresh the page
  --> We will see our page..!!

##############################################################################################

# AWS VPC 

--> Go to VPC
	- Create a VPC
		- VPC Settings
			- VPC and more (not for manually) --> Select This		
		- Name tag auto-generation (tick on it) --> iacsd
		- IPv4 CIDR block --> 192.168.100.0/24
		- No IPv6 CIDR block (select)
		- Tenancy --> Default
		- Number of Availabilty Zones (AZs) ---> 1
		- Number of public subnets  --->  1
		- Number of private subnets  --->  1
		- Customize subnets CIDR blocks
			- Public Subnets ---> 192.168.100.0/25
			- Private Subnets  --->  192.168.100.128/25
		- NAT gateways   --->  In 1 AZ
		- VPC endpoints   --->  None
		- DNS Options (Both enable)  -->  Enable DNS hostnames , Enable DNS resolution --- (tick on it)
		- Click on ---> Create VPC
		- View VPC 


-->  Go in EC2
	-  Launch an instance
		-  Name : public
		-  Debian
		-  t2.micro
		-  key pair (new)  - name : public  ,  .ppk - download
		-  Network Settings
			- Select our created VPC (iacsd-vpc)
			- Subnet - Public
			- Auto-assign public IP - Enable 
			-  SSH and HTTP (Anywhere) - Allowed
		- Done .. Launch an instance

--> Go in Putty
		- Connect with given IP and provide Private Key 
		- sudo apt install apache2 -y
		- sudo rm /var/www/html/
		- sudo nano /var/www/html/index.html
			- <h1> PUBLIC SERVER </h1>
		- which curl
		- curl localhost
		- curl http://192.168.100.116

-->  Go in EC2 (new instance)
	-  Launch an instance
		-  Name : private
		-  Debian
		-  t2.micro
		-  key pair (new)  - name : private  ,  .pem - download
		-  Network Settings
			- Select our created VPC (iacsd-vpc)
			- Subnet - Private
			- Auto-assign public IP - Disable
			-  SSH and HTTP (Anywhere) - Allowed
		- Done .. Launch an instance

--> Open already created EC2 - terminal
	- nano mykey
		- copy the private.pem in that
		- save it and exit
	- chmod 400 mykey
	- ls -l
	- ssh -i mykey admin@192.168.100.198 <Private IP of EC2 Private Instance>

--> It will Open Private EC2 terminal 
	- sudo apt update
	- sudo apt install apache2 -y
	- sudo rm /var/www/html/
	- sudo nano /var/www/html/index.html
		- <h1> PRIVATE SERVER </h1>
	- curl localhost
	- curl http://192.168.100.116
	- exit

- Old Machine (EC2)
	- curl 192.168.100.198 <Private IP of EC2 Private Instance>

########################################################################