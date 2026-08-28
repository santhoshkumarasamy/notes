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
### Default Host Management Configuration

- When enabled it automatically configure your ec2 instance as managed insthaces without use of the ec2 instance profile
- **instance identity role** - a type of iam role with no permission beyond identifying the ec2 instance to aws service
- ec2 instance must have IMDSv2 enables and ssm agent installed 
- automatically enables session manager,patch manager and inventory
- automatically keeps the ssm agent up to date
- must be enable dper aws region


### Inventory 
- Collect metadata from ec2 and on-prem
- store it in s3
- cross account data
- custom metadata

### State Manager

- automate the process of keeping your managed instances in a state that ou define
- use case: bootstrap instances with software
- state Manager Association
    * defines the state that you want to maintain to your managed instance.
    * specify a schedule when this configuration is applied
- uses ssm documents to create an assoication 
### Patch Manager

- Automated the process of patching managed instances
- os update, applications updates, security updates
- support ec2 and on-prem
- linux, mac os and windows
- patch on-demand or on a schedule using maintenance windows
- scan instances and generate patch compliance report

#### Patch Baseline
    - define which patch should and shouldn't be appliend on the instances
    - ability to create custom patch baselines
    - patched can be auto-approved
    - by default, install only critical and security related patches
#### Patch group
    - associate a set of instances with specific patch baseline
    - instances should be defined with the tag key **Patch Group**
    - an instance can only be in one patch group
    - patch group can only be registered with one baseline

* Pre-defined Patch Baseline : AWS-RunPatchBaseLine(SSM Document)
* Custom Patch baselines


#### Maintenance Windows

- defines a schedule for when to perform actions on your instaces
- Maintenance window contains
    * Schedule
    * duration
    * set of registered instances
    * set of tasks

### session manager

- does not need ssh access
- allows you to start a secure shell on your ec2 and on premise servers
- macos, linux and windows
- session log data can be sent to s3 ro cloudwatch logs
- cloudtrail can intercept startsession events
- Need IAM Permission: tags can be used to restrict the access

### SSM Distributer

- package and deploy software to your managed instances
- you create a **distributor package(ssm document)** and deploy to different platforms
- Distributor package
    * contents stored in s3
    * zip file per target os platform(install script, uninstall script and executable file)
    * json manifest file that describe the package content
- use aws-provided packages, 3rd party packages, or create your own package
- install the package:
    * one time - AWS Run Command
    * On a schedule - using state manager

### Ops Center

- allowd you to view, investigate and remediate issues in one place
- security issues(security hub), performance issues(DynamoDB throttle), failures(ASG failed instance)
- Reduce meatime to resolve issues
- OpsItems
    * Operation issue or interruption that needs investigation and remediation
    * event, resources, aws config changes, cloud trail logs, eventbridge
    * provides recommended runbook to resolve the issue
- support ec2 and on-prem

## High availability and scalability 

- Classic
- Application - HTTP, HTTPS , Web Socket - Application layer(7) - path, hostname, queary string & headers
- Network - TCP, TLS, UDP - Transport Layer (4)
- Gateway - Layer 3(network layer)


### NLB

- TCP and UDP Traffic : Millions of request per sec: ultra low latency
- One Static IP per AZ and supports assigning elastic IP
- layer 4
- target group
    - Ec2 instances
    - IP Address -> must be private ip (outpost)
    - Application Load Balancer
    - Health check supports tcp, http and https

### Gateway load Balancer
- layer 3 - network layer
- its like a middleware between the end user and the application
- you can use it to route the traffic to monitor it 
- uses GENEVE protocol on port 6081
- target group
    - EC2 instance
    - Private IP