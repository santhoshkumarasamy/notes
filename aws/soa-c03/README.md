```
This docs on details for CloudOps Associate Exam
```

# Content Outline
- Monitoring, Logging and remediation and performance optimization
- reliability and business continuity
- deployment, provisioning and automation
- security and compliance
- networrking and content delivery

## EC2

### Cloud Instance Initialization
the process of preparing an instance with configuration data for the opeartion system and runtime environment

`user-data`: is a script that you want to run when a instance *`first boots up`*

### Placement groups

- Cluster : Clusters instances into a low-latency group in a single az - 10gps - its in the same  az so if the az fails all instance go down
- Spread: spreads instances across underlying hardware(max 7 instances per group per az)
- Partition: spreads instance across many different partitions(which rely on different set of racks) within a AZ. scales to 100s of ec2 instance per group - 7 partitions per az

### Ec2 instance connest endpoint


### Status Check
- System Status Check - Aws Hardware issues
- Instance status check - os level issues
- EBS Status check

```
You can trigger `Recover EC2 Instace` from cloud watch alarm which will bring the ec2 instance with same private ip, public ip and metadata & same placement group
```
#### EC2 AMI

- Specific to a region but can be copied accross
- Public AMI(AWS Provided), Your own ami, Marketplace AMI
- start and customize your ec2,stop it for data integrity, build a ami(EBS Snapshot) and finally create a ec2 instnace

- AMI No reboot option is used to create an emi when the ec2 is running without this aws will first stop the instance, create a ebs snapshot then create a ami from the ebs snapshot
- when you want to share a AMI with other aws account you either share a unencrypted ami or you need ot share the customer managed key along with the encryted ami : it dosnt allow aws owned keys
- you can also copy the ami from one account to another
- you can force users to use only a handful of ami for the instance -apply tag to the ami and put a condition that no instance should be created that doesnt have the tag

## SSM

### Documents
 Basically a steps by steps details which needs to be performed on a instance

### Run Command

- Execute a document(=script) or just run a command on multiple instances
- No need for ssh
- command output can be shown in console, sent to s3 or cloud watch
- notification to sns
- can be invoked by event bridge
