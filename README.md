# Containers Workshop


`OpenSCAP Workshop` is a ansible playbook to provision servers in AWS. This playbook uses Ansible to wrap Terraform, for provisioning AWS infrastructure and nodes. To find more info about Terraform [check here](https://www.terraform.io/docs/providers/aws/index.html)

These modules all require that you have AWS API keys available to use to provision AWS resources. You also need to have IAM permissions set to allow you to create resources within AWS. There are several methods for setting up your AWS environment on you local machine. 

Export the `AWS API Keys` by hand;

```
export AWS_ACCESS_KEY_ID='****************WFQ'
export AWS_SECRET_ACCESS_KEY='****************TFHJw'
```

This repo also requires that you have Ansible installed on your local machine. For the most upto date methods of installing Ansible for your operating system [check here](http://docs.ansible.com/ansible/intro_installation.html).

This repo also requires that Terraform be installed if you are using the `aws.infra.terraform` role. For the most upto data methods of installing Terraform for your operating system [check here](https://www.terraform.io/downloads.html)


## AWS Infrastructure Roles

### role/aws.infra.terraform

Configure the workshop with your [api keys](https://aws.amazon.com/developers/access-keys/) and domain & [zone id](http://docs.aws.amazon.com/Route53/latest/DeveloperGuide/CreatingHostedZone.html) name for the workshop. Also fill in the `num_nodes` for the amount of students in the class. Also ensure that you have a `~/.ssh` folder created.  

`groups_var/all`

```
# file: group_vars/all

#####################################################
# container.workshop  |  RHEL Subscription Info
#####################################################
username: ""
password: ""
pool_id: ""


# If you plan on protecting your web server with 
# a password, set it here:
workshop_password: "openscap!@#" 

#####################################################
# aws.infra.terraform |  Domain Name you own
#####################################################
# NOTE: some commonly used domains/zone combos:
#
# For openscap.io:
#domain_name: "openscap.io"
#zone_id: "ZH2VK6MJFH9R4"
#
# For redhatgov.io (https://redhatgov.signin.aws.amazon.com/console)
#domain_name: "redhatgov.io"
#zone_id: "Z24HHVIM122OC"

domain_name: ""
zone_id: ""

#####################################################
# aws.infra.terraform |  AWS API Keys terraform.tfvars
#####################################################
# NOTE: did you remember to expose these as shell vars?
# $ export AWS_ACCESS_KEY_ID=''
# $ export AWS_SECRET_ACCESS_KEY=''
#
aws_access_key: ""
aws_secret_key: ""

# NOTE: If you change this, you will need to update
#		the AMI IDs used elsewhere!
region: "us-east-1"


#####################################################
# aws.infra.terraform |  Number of Students
#####################################################
# Pro tip: edit the "forks" option in ansible.cfg,
# which reflects how many parralel connections
# ansible will make. If you have 30-50+ student VMs,
# there's no reason "forks" shouldn't match.
#
# Setting "forks" will dramatically speed up your
# build!
num_nodes: 40
```

To create infrastructure and a instance via Terraform 

```
cd workshops
ansible-playbook -i inventory 1_aws_infra.yml --tags "tf_create" 
```

To destroy

```
cd /tmp/terraform
terraform destroy
```

## Configure Container Workshop

### role/container.workshop

To target the newly created EC2 instance use the `inventory` folder. The [ec2.py](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) is a dynamic script that queries Amazon for your instances. The `container.workshop` role sets up the student environment.

### Use `aws tags` to query AWS for your instance by AWS Tags


```
ansible-playbook -i inventory 2_aws_ec2.yml
```


## Students connect to their instance

Students should use a web browser to connect to their instance and download the ssh key from the newly created instance. 


```
student#.labs.openscap.io
```

![Student Login](img/student-login.png)

## Changing the student webpage

If you'd like to change the student webpage, e.g. add more tabs, edit `roles/container.workshop/files/web_content/index.html`
