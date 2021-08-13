#Packer to build an AMI on AWS

## Step 1) Downloading and installing packer

### 1: Install the version of Packer based on the OS of the machine from which you plan to build the image. ![Open this link](https://www.packer.io/downloads)
### 2: If you are in windows download the file and extract the packer.exe and save it in a folder named Packer in your C: Drive (C:\Packer\packer.exe)
### 3: Add it to your PATH Variable by going to your settings from Control Panel. Click on new in the Environment Variables and add the Absolute Path to the file there.
- Go to Control Panel -> System -> System settings -> Environment Variables.
- Scroll down in system variables until you find PATH.
- Click edit and change accordingly.
- BE SURE to include a semicolon at the end of the previous as that is the delimiter, i.e. c:\path;c:\path2
- Launch a new console for the settings to take effect.

## Step 2) Packer template creation
### Packer uses a JSON template that contains build instructions. The basic construct is one or more builders and multiple steps of provisioners and post-processors.
### example.json is the packer template :
```
{
    "variables": {
        "access_key": "{{env `ACCESS_KEY`}}",
        "secret_key": "{{env `SECRET_KEY`}}",
        "description": "eng89_prathima_packer_myWebServer"
    },
    "builders":[
        {
            "type":"amazon-ebs", 
            "access_key": "{{user `access_key`}}",
            "secret_key": "{{user `secret_key`}}",
            "region": "eu-west-1",
            "ami_name": "{{user `description`}}",
            "source_ami": "ami-038d7b856fe7557b3",
            "instance_type": "t2.micro",
            "ssh_username": "ubuntu"
        }
    ],
    "provisioners":[
        {
            "type": "shell",
            "script": "setup.sh"
        }
    ],
    "sensitive-variables": ["access_key", "secret_key"],
    "post-processors": [
        {
            "type": "manifest",
            "output":"{{user `description`}}.json"
        }
    ]
}
```
### setup.sh is the shell script that will install packages inside the machine
```
sudo apt update
sudo apt install nginx -y
systemctl enable nginx
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```
### We are using a file-based provisioner that copies the file into the build machine and executes it. We also have a manifest post-processor that enables us to output the AMI ID that was just built.

### In example.json, replace the variable wildcards (security group, subnet id, instance information etc.) with your desired values
### Run as from the terminal to build an AMI as below:
`packer build example.json`

### We can the console log of the steps in building an AMI and we can see the AMI created on AWS Console as well.

```
Name :eng89_prathima_packer_myWebServer

AMI Name: eng89_prathima_packer_myWebServer

AMI ID: ami-07f4c568b9ffc663d
Source: 135928476890/eng89_prathima_packer_myWebServer
Owner: 135928476890
Visibility: Private
Status: available
Creation Date: August 13, 2021 at 7:53:06 AM UTC+1
Platform: Other Linux
Root Device: ebs
Virtualization: hvm
Deprecation Time: -

```