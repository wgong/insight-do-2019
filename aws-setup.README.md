<<<<<< Don't terminate instance until after insight workshop >>>>>>

[Pegasus](https://github.com/InsightDataScience/pegasus)

# install pegasus on virtualbox/ubuntu machine

$ cd  ~/aws-wen-at-insight
## ssh to ec2 instance
********************************************************************************************
$ ssh -i "wen-at-insight-IAM-keypair.pem" ec2-user@ec2-3-86-35-254.compute-1.amazonaws.com
********************************************************************************************

[ec2-user@ip-172-31-90-235 ~]$ pwd
/home/ec2-user

$ sudo yum install git
$ git clone https://github.com/InsightDataScience/pegasus.git
$ git clone https://github.com/InsightDataScience/aws-ops-insight.git
$ git clone https://github.com/InsightDataScience/engineering-project-template.git



$ mkdir bin
$ mkdir Downloads
$ mkdir Documents


install terraform
$wget https://releases.hashicorp.com/terraform/0.11.11/terraform_0.11.11_linux_amd64.zip

$ unzip terraform_0.11.11_linux_amd64.zip
$ mv terraform bin


install ansible
ansible is available in Amazon Linux Extra topic "ansible2"

To use, run
$ sudo amazon-linux-extras install ansible2

Learn more at
https://aws.amazon.com/amazon-linux-2/faqs/#Amazon_Linux_Extras
$ which ansible
/usr/bin/ansible



install pip

$ curl -O https://bootstrap.pypa.io/get-pip.py
$ python get-pip.py --user
$ pip --version
$ pip install awscli

 add the following to your ~/.bash_profile.
 
export AWS_ACCESS_KEY_ID=AKIAIN7YGFFBFCFIN7ZA
export AWS_SECRET_ACCESS_KEY=Pb2ZRKP62VSTTgmRw1NtfdtNu36HjwkTCuPETkAc
export AWS_DEFAULT_REGION=us-east-1
export REM_USER=ubuntu
export PEGASUS_HOME=$HOME/pegasus
export PATH=$PEGASUS_HOME:$PATH

source ~/.bash_profile

issue:
terminated my AWS instance yesterday,
relaunch and lost my work
use STOP not TERMINATE when leaving EC2 instance

********************************************************

devopsgong@osboxes:~/pegasus/examples/spark$ peg config
access key: AKIAIN7YGFFBFCFIN7ZA
secret key: Pb2ZRKP62VSTTgmRw1NtfdtNu36HjwkTCuPETkAc
    region: us-east-1
  SSH User: ubuntu


devopsgong@osboxes:~/pegasus/examples/spark$ aws ec2 --output json describe-regions --query Regions[].RegionName
[
    "ap-south-1",
    "eu-west-3",
    "eu-north-1",
    "eu-west-2",
    "eu-west-1",
    "ap-northeast-2",
    "ap-northeast-1",
    "sa-east-1",
    "ca-central-1",
    "ap-southeast-1",
    "ap-southeast-2",
    "eu-central-1",
    "us-east-1",
    "us-east-2",
    "us-west-1",
    "us-west-2"
]


$ peg aws vpcs
------------------------------
|        DescribeVpcs        |
+---------------+------------+
|    VPC_ID     | VPC_NAME   |
+---------------+------------+
|  vpc-4f2b9d35 |  default   |


devopsgong@osboxes:~/pegasus/examples/spark$ peg aws subnets
--------------------------------------------------------------------------
|                             DescribeSubnets                            |
+------------+-------+-------------------+--------------+----------------+
|     AZ     |  IPS  |     SUBNET_ID     | SUBNET_NAME  |    VPC_ID      |
+------------+-------+-------------------+--------------+----------------+
|  us-east-1e|  4091 |  subnet-f1aa03cf  |  None        |  vpc-4f2b9d35  |
|  us-east-1a|  4091 |  subnet-0d2e4851  |  None        |  vpc-4f2b9d35  |
|  us-east-1c|  4090 |  subnet-3a2b4b14  |  None        |  vpc-4f2b9d35  |
|  us-east-1d|  4091 |  subnet-2d1e2b67  |  None        |  vpc-4f2b9d35  |
|  us-east-1b|  4091 |  subnet-c037ada7  |  None        |  vpc-4f2b9d35  |
|  us-east-1f|  4091 |  subnet-8c336c83  |  None        |  vpc-4f2b9d35  |
+------------+-------+-------------------+--------------+----------------+

devopsgong@osboxes:~/pegasus/examples/spark$ peg aws security-groups
-------------------------------------------------------------
|                  DescribeSecurityGroups                   |
+----------------------+-------------------+----------------+
|         SG_ID        |      SG_NAME      |    VPC_ID      |
+----------------------+-------------------+----------------+
|  sg-0a0e51e0b61b7bdd4|  database         |  vpc-4f2b9d35  |
|  sg-0ac74d48f8c9199da|  control-machine  |  vpc-4f2b9d35  |
|  sg-0d867714df5d62a2a|  compute          |  vpc-4f2b9d35  |
|  sg-0ff0feef8705877bc|  launch-wizard-2  |  vpc-4f2b9d35  |
|  sg-664bc124         |  default          |  vpc-4f2b9d35  |
+----------------------+-------------------+----------------+



$cd ~/pegasus/examples/spark
prepare master.yml and workers.yml
$ more master.yml
purchase_type: on_demand
subnet_id: subnet-05f6a2c972aac4170
num_instances: 1
key_name: wen-at-insight-IAM-keypair
security_group_ids: sg-058f94ad726d59c3b
instance_type: m4.large
tag_name: wen-gong
vol_size: 100
role: master
use_eips: true


$ more workers.yml
purchase_type: on_demand
subnet_id: subnet-05f6a2c972aac4170
num_instances: 3
key_name: wen-at-insight-IAM-keypair
security_group_ids: sg-058f94ad726d59c3b
instance_type: m4.large
tag_name: wen-gong
vol_size: 100
role: worker
use_eips: true

devopsgong@osboxes:~/pegasus/examples/spark$ peg up master.yml
Template validated successfully.
[wen-gong] waiting for instances i-0fda9efa5e6af7cb3 to be in status ok state...
[wen-gong] i-0fda9efa5e6af7cb3 ready...
Elastic IP associated with the instance i-0fda9efa5e6af7cb3


devopsgong@osboxes:~/pegasus/examples/spark$ peg up workers.yml
Template validated successfully.
[wen-gong] waiting for instances i-0620cccd366ba71f3	i-00433ceb6fb111ab2	i-0f3ab44e477a74431 to be in status ok state...
[wen-gong] i-0620cccd366ba71f3	i-00433ceb6fb111ab2	i-0f3ab44e477a74431 ready...
Elastic IP associated with the instance i-0620cccd366ba71f3
Elastic IP associated with the instance i-00433ceb6fb111ab2
Elastic IP associated with the instance i-0f3ab44e477a74431




devopsgong@osboxes:~/pegasus/examples/spark$ peg fetch wen-gong
pem key wen-at-insight-IAM-keypair found locally
wen-at-insight-IAM-keypair.pem has been added to your ssh-agent
wen-gong cluster instance type histogram
      4 m4.large

MASTER NODE:
    Hostname:   ip-172-31-87-254
    Public DNS: ec2-52-70-192-93.compute-1.amazonaws.com

WORKER NODE:
    Hostname:   ip-172-31-95-128
    Public DNS: ec2-3-86-19-87.compute-1.amazonaws.com

WORKER NODE:
    Hostname:   ip-172-31-90-141
    Public DNS: ec2-52-203-95-80.compute-1.amazonaws.com

WORKER NODE:
    Hostname:   ip-172-31-90-128
    Public DNS: ec2-52-6-252-72.compute-1.amazonaws.com


cluster-name = wen-gong
$ peg install wen-gong ssh

devopsgong@osboxes:~/pegasus/examples/spark$ peg install wen-gong ssh
Setting up passwordless SSH on wen-gong
ssh: connect to host ec2-52-70-192-93.compute-1.amazonaws.com port 22: Connection timed out
ssh: connect to host ec2-52-70-192-93.compute-1.amazonaws.com port 22: Connection timed out
Warning: Permanently added 'ec2-52-70-192-93.compute-1.amazonaws.com,52.70.192.93' (ECDSA) to the list of known hosts.
# ec2-52-70-192-93.compute-1.amazonaws.com:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
Adding ip-172-31-95-128 to known hosts...
# ip-172-31-95-128:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
Adding ip-172-31-90-141 to known hosts...
# ip-172-31-90-141:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
Adding ip-172-31-90-128 to known hosts...
# ip-172-31-90-128:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
# 0.0.0.0:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
# localhost:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
# 127.0.0.1:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
** run it again
devopsgong@osboxes:~/pegasus/examples/spark$ peg install wen-gong ssh
Setting up passwordless SSH on wen-gong
Generating public/private rsa key pair.
Your identification has been saved in /home/ubuntu/.ssh/id_rsa.
Your public key has been saved in /home/ubuntu/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:efGDJX6rbFGhfGYSnrWfXZSq6Tj7Z7eATh7PEnDMK4M ubuntu@ip-172-31-87-254
The key's randomart image is:
+---[RSA 2048]----+
|                .|
|          . o  ..|
|         o==.o.. |
|         +*X*.  .|
|        S **B. o.|
|       E +.*.oo .|
|          ==o.   |
|         +=++o.. |
|         o*++o...|
+----[SHA256]-----+
Adding  to authorized keys...
Adding  to authorized keys...
Adding  to authorized keys...
Warning: Permanently added 'ec2-52-203-95-80.compute-1.amazonaws.com,172.31.90.141' (ECDSA) to the list of known hosts.
Warning: Permanently added 'ec2-3-86-19-87.compute-1.amazonaws.com,172.31.95.128' (ECDSA) to the list of known hosts.
Warning: Permanently added 'ec2-52-6-252-72.compute-1.amazonaws.com,172.31.90.128' (ECDSA) to the list of known hosts.
# ec2-52-70-192-93.compute-1.amazonaws.com:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
Adding ip-172-31-95-128 to known hosts...
# ip-172-31-95-128:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
Adding ip-172-31-90-141 to known hosts...
# ip-172-31-90-141:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
Adding ip-172-31-90-128 to known hosts...
# ip-172-31-90-128:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
# 0.0.0.0:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
# localhost:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
# 127.0.0.1:22 SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6




$ peg install wen-gong aws

devopsgong@osboxes:~/pegasus/examples/spark$ peg install wen-gong aws
Passing AWS credentials to wen-gong
ec2-52-70-192-93.compute-1.amazonaws.com
ec2-3-86-19-87.compute-1.amazonaws.com
ec2-52-203-95-80.compute-1.amazonaws.com
ec2-52-6-252-72.compute-1.amazonaws.com
Warning: Permanently added 'ec2-52-203-95-80.compute-1.amazonaws.com,52.203.95.80' (ECDSA) to the list of known hosts.
Warning: Permanently added 'ec2-52-6-252-72.compute-1.amazonaws.com,52.6.252.72' (ECDSA) to the list of known hosts.
Warning: Permanently added 'ec2-3-86-19-87.compute-1.amazonaws.com,3.86.19.87' (ECDSA) to the list of known hosts.


$ peg install wen-gong environment
	
Collecting ipykernel (from notebook; extra == "notebook"->ipython[notebook]==5.5.0)
  Downloading https://files.pythonhosted.org/packages/11/0b/95330660f8cc5d63428b9886c800ea8d68842fd866389cf579acca4915be/ipykernel-5.1.0.tar.gz (101kB)
    Complete output from command python setup.py egg_info:
    ERROR: ipykernel requires Python version 3.4 or above.
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-F96d99/ipykernel/
    Complete output from command python setup.py egg_info:
    ERROR: ipykernel requires Python version 3.4 or above.
    
    ----------------------------------------
Command "python setup.py egg_info" failed with error code 1 in /tmp/pip-build-YG_6mw/ipykernel/
You are using pip version 8.1.1, however version 18.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
export JAVA_HOME=/usr
You are using pip version 8.1.1, however version 18.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
export JAVA_HOME=/usr
Environment installed!

[Data Engineering EcoSystem](https://github.com/InsightDataScience/data-engineering-ecosystem/wiki)
$ peg install wen-gong <technology>
The technology tag can be any of the following (version set in configuration is bolded):

** 6 cassandra (v3.11.1, v3.11.2)
cassandra installed!
Cassandra configuration complete!


** 9 elasticsearch (v5.1.2, v6.2.4)
FAILED
gzip: stdin: not in gzip format
tar: This does not look like a tar archive
tar: Exiting with failure status due to previous errors
The software wasn't downloaded correctly from the URL (https://s3-us-west-2.amazonaws.com/insight-tech/elasticsearch/elasticsearch-.tar.gz). Check Pegasus GitHub README.


** 7 flink (v1.4.2 with hadoop v2.7 and scala v2.11)
flink installed!

** 1 hadoop (v2.7.6)
$ peg install wen-gong hadoop
** Hadoop configuration complete!

** 8 hbase (v1.2.6)
hbase installed!
HBase configuration complete!


hive (v2.3.3)

** 5 kafka (v1.0.0 and v1.1.0 with scala v2.11 and v2.12) (all combinations)
kafka installed!
Kafka configuration complete!


kibana (v6.2.4)
opscenter (v6.5.0)
pig (v0.17.0)
presto (v0.200)

** 3 redis (v4.0.9)
$ peg install wen-gong redis
** Redis configuration complete!


** 2 spark (v2.2.1, v2.3.0, v2.3.1 for hadoop v2.7)
$ peg install wen-gong spark
** spark installed!


** 10 storm (v1.2.1)
storm installed!
Storm configuration complete!


** 4 zookeeper (v3.4.10, v3.4.12, v3.4.13)
Zookeeper configuration complete!

SSH into a node

$ peg ssh wen-gong 1 # ssh to master node
ubuntu@ip-172-31-87-254:~$

$ peg ssh wen-gong 2 # ssh to work node 1 
ubuntu@ip-172-31-95-128:~$

$ peg ssh wen-gong 3 # ssh to work node 2
ubuntu@ip-172-31-90-141:~$

$ peg ssh wen-gong 4 # ssh to work node 3
ubuntu@ip-172-31-90-128:~$











