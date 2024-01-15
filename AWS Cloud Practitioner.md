# AWS Cloud Practitioner

## Overview

cloud computing: on-demand availability of computer system resources, especially data storage and compute power, without direct active management by the user

Infrastructure as a service

2002 internal launch, 2004 public launch, 2006 public launch ec2

Regions: three or more availability zones, availability zone is composed of one or more discrete data centers with redundant power, networking, connectivity and used tto deploy infrastructure.

The shared responsibility model defines the distribution of responsibilities for security in the aws cloud.

## IAM 

Identity and Access Management

### Users and groups

root are created by default, should not be used or shared

group only contain users

user don't have to belong to a group, can belong to multiple groups

users and groups can be assigned JSON documents called **policies**, these policies define the premission of the users. **Lease priviledge principle.**

### Policy

version, id, statement (sid, effect, principle, action, resource, condition)
admin: full access.

Password policy:

- set min length
- requrie specific character use
- Allow all users change password
- require change password after some time
- prevent password reuse

Multi factor authentication (MFA)

- password you know and a security device you own
- Options: 
  - virtual MFA device: support for multiple tokens on single device
  - Universal 2nd factor (U2F) security key: support for multiple root and IAM users using a single key
  - Hardware key fob: provided by Gemalto
  - Hardware key fob for AWS GovCloud: providede by SurePassId

### Access keys, cli and sdk

three ways to access AWS:

- AWS management console
- AWS command line interface
- AWS software developer kit

access keys: keyId + secret access key

cli is built on aws sdk for python

aws --configure

IAM roles for services

- ec2 instance role, lablda function roles...

Security Tools

- IAM credentials report (account-level)
- IAM Access Advisor (user-level)

### Shared Responsibility Model For IAM

AWS:

- Infrastructure (global network security)
- configuration and vulnerability analysis
- compliance validation

You:

- users, groups roles, policies management and monitering
- emable MFA on all accounts
- rotate all your keys often
- use IAM tools to apply appropriate permissions
- analyze access pattern & review permissions

## EC2

### basics

EC2 = elastic compute cloud = IaaS

It mainly consists in the capability of:

- renting virtual machines (EC2)
- storing data on virtual drives (EBS)
- Distributing load accross machine (ELB)
- scaling the services using an auto-scaling group (ASG)

configuration options

- operating system
- how much compute power & cores (CPU)
- how much random-access memory (RAM)
- how much storage space
  - Network-attached (EBS, EFS)
  - Hardware (EC2 instance store)

- network card: speed, public ip address

- firewall rules: security group

- bootstrap script (configure at first launch): EC2 user data

  EC2 user data run with the root user (need sudo) is used to automate boot tasks such as:

  - installing updates
  - installing softwares
  - downloading common files from the internet
  - anything need to perform when a machine starts

Hands on:

T2micro

Linux, max, win10+: use pem

EBS volume (detele when termination is default)

public ip may change when it stops and restarts

### instance type

M5.2xlarge

- m: instance class
- 5: generation (improve overtime)
- 2xlarge: size with in the instance class

Types

- general purpose (t, m, a): balance between compute, memory and networking
- Compute optimized (C): high performance processors
  - batch processing workloads
  - media transcoding
  - high performance web servers
  - high performance computing
  - scientific modeling & machine learning
  - dedicated gaming servers
- momery optimized (r, x): for large data sets in memory
  - high performance, relational/non-relational databases
  - distributed web scale cache stores
  - In-memory databases optimized for BI
  - applications performing real-time processing of big unstructurd data
- storage optimized (i, d, h): high sequential read and write to large data sets on local storage
  - High frequency OLTP systems
  - relational & NoSQL databases
  - cache for in-memory databases (redis)
  - Data warehousing applications
  - distributed file systems

### security groups

introduction:

- fundamental of network security in AWS
- control how traffic is allowed into or out of
- only contain allow rules
- Can reference by Ip or by security group

Regulate:

- access to ports
- authorised Ip ranges - ipv4 and ipv6
- control of inbound network and outbound network

traits

- can be attached to multiple instances
- locked down to a region/vpc combination
- live outside of EC2
- good to maintain one seperate security group for ssh access
- if application 'time-out', security group issue, but 'connection-refused', application or launching error
- all inbound is blocked by default
- all outbound is authorised by default

classic ports

- 22=ssh - log into a linux instance
- 21 = ftp: uploads files into a file share
- 22 = sftp: uploads files using ssh
- 80: http
- 443: https
- 3389: rdp (remote desktop protocol): log into a windows instance

### SSH

```bash
chmod 0400 UdemyCourseInstance.pem
ssh -i UdemyCourseInstance.pem ec2-user@54.211.5.190
logout
```

or Ec2 Instance Connect

### Roles

Never input your access id and secret key in ec2 'aws configure', use IAM Role.

Security - modify IAM role

```bash
aws iam list-users
```

### Pricing options

- On demand instances - short workload, predictable pricing, pay by second

  - pay for what you use, linux/windows is billing per second after the first minute, all other OS is billing by hour

  - has the highest cost but no upfront payment.

  - no long-term commitment

  - recommended for short-term and un-interrupted workloads where you cannot predict how the application will behave.

- Reserved (1 & 3 years)

  - up to 72% discount compared to on-demand, reserve a specific instance attribute (instance type, region, tenancy, OS).

  - Reserve period: 1 year, 3 years

  - Payment options: No upfront, partial upfront, all upfront

  - Reserved instance scope: regional or zonal (reserve capacity in AZ)

  - Recommanded for steady-state usage applications (like database)

  - Can by or sell in the reserved instance marketplace

  - Two types:

    - reserved instances - long workloads

    - convertible reserved instances - long workloads with flexible instances: can chaneg type, instance family, OS, scope and tenancy. Up to 60% discount.

- Savings plan (1 & 3 years) - commitment to an amound of usage, long workload

  - get a discount based on long term usage (up to 72%)

  - commit to a certain type of usage ($10/hour for 1 or 3 years)

  - Usage beyond Ec2 savings plans is billed at on-demand price

  - locked to a specific instance family & region

  - flexible across instance size, OS, tenancy (host, dedicated, default)

- spot instances - short workloads, cheap, can lost instance

  - Discount up to 90%, most efficient

  - Useful if workloads that are resilient to failure, such as batch jobs, data analysis, image processing, distributed workloads, workloads with a flexible start and end time.

  - Can lose at any point of time if your max price is less than the current spot price.

- dedicated hosts - book an entire physical server, control instance placement

  - purchase options:
    - On-demand: pay per second for actice dedicated host
    - Reserved: 1 or 3 years, same payment options as above
  - the most expensive
  - useful for software that has complicated licensing model, for example software license per socket, per core, per vm...), or companies that have strong regulatory or complicance needs.

- dedicated instances - no other customers will share your hardware

  - may share hardware with other instances in the same account
  - no control over instance placement (can move hardware after stop/start)

- capacity reservations - reserve capacity in a specific AZ for any duration

  - no time commitment, no billing discount
  - combine with regional reserved instances and saving plans.
  - chared whether you run or not
  - Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ

### shared responsibility

user:

- security group rules
- operating system patches and updates
- software and utilities installed
- IAM roles and access management
- data security on instance

### EC2 Instance Storage

#### EBS

1. What

- An EBS (elastic block store) volume is a network drive you can attach to instance while they run, allow instance to persist data even after termination.

- Can only be mounted to one instance at a time (at the CCP level)

- Bound to a specific AZ
  - An EBS in us-east-1a cannot be attached to use-east-1b
  - to move across, you first need to snapshot it

- Have a provisioned capacity (size in GBs and IOPs)
  -  Free tier: 30GB general perpost or Magnetic per month.
  - You get billed for all provisioned capacity
  - You can increate the capacity overtime
- It is a network drive (not physical)
  - it uses the network to communicate the instance, have latency
  - it can be detached and attach to another quickly

- delete on termination attribute (default enable)

2. EBS Snapshots

- make backup at a point in time
- not necesssary to detach volume to do snapshot, but recommended
- can copy snapsots across AZ or region, then restore
- can create volume from a snapshot

3. EBS Snapshot archive

- 75% cheaper
- takes within 24-72 hours for restoring the archive

4. Recycle bin for EBS Snapshots

- setup rules to retain deleted snapshots so that you can recover after accidental deletion
- specify retention (1 day to 1 year)

#### AMI

- amazon machine image

- AMI are customization of an EC2 instance, you can add you own software, configuration, OS, monitering
- Faster boot/configuration time because all softwares are pre-packaged
- built for specific region, can be copied across regions
- You can launch EC2 instance from
  - public AMI: AWS provided
  - Own AMI: make and maintain yourself
  - AWS Marketplace AMI: someone else made

- process:
  - start EC2 instance and customize
  - Stop it for data integrity
  - Build an AMI, this also creates EBS snapshots
  - launch instance from other AMIs

select instance - image and template - create image -

in AMIs, select AMI -  launch instances from AMI

#### EC2 Image Builder

used to automate the creation of virtual machines or container images => automate the creation, maintain validate and test EC2 AMIs.

#### EC2 instance store

- when you need high performance hardware disk
- better IO performance
- EC2 instance store lose their storage if they are stopped(ephemeral)
- good for buffer/cache/scratch data/temporart content
- risk od data loss if hardware filas
- backups and replications are your responsibility

#### EFS

- elastic file system
- Managed NFS (network file system) can be mounted on 100s of EC2
- highly available, scalable, expensive (3x gp2), pay per use, no capacity planning
- data shared by instances mounted by them

#### EFS-IA Infrequent Access 

- cost optimized for files not accessed every day
- up to 92% lower cost compared to standard
- EFS will automatically move files to EFS-IA based on the last time they are accessed
- Enable EFS-IA with a lifecycle policy
- Transparent to the applications accessing EFS

#### shared responsibility

AWS:

- infrastructure
- replications for data for EBS volume & EFS drives
- replacing faulty hardware
- ensuring their employees cannot access your data

User:

- setting up backup/snapshot procedure
- setting up data encryption
- Responsibility of any data on the drives
- understanding the riskk of using EC2 instance store

### Amazon FSx

- Launch 3rd party high-performance file systems on AWS
- fully managed service
- FSx for windows file server
  - built on windows file server
  - support SMB protocol & windows NTFS
  - can be accessed from AWS on your on-premise infrastructure

- FSx for Luster
  - for high performace computing
  - Linux + cluster
  - scale up to 100s GB/s, milllions of IOPs, submillies latency

## ELB 

Elastic load balancing

load balancers are servers that forware internet traffic to multiple servers downstream.

Why loadbalancers:

- spread load accross multiple downstream instances
- expose a single point of access (DNS) to your application
- Seamlessly hande failures of downstream instances
- do regular health checks to your instances
- provide SSL termination (HTTPS) for your websites
- high availability across zones

Why EBS:

- an ELB is managed load balancer
  - AWS guarantee that it will be working
  - AWS take care of upgrades, maintenance, high avaiability
  - AWS provides only a few configuration knobs

- it costs less to set up your own load balancer but it will be more effort on your end (maintenance, integration)

- 4 kinds of load balancer offered by AWS

  - Application load balancer (HTTP/HTTPS only) - layer 7

  - network load balancer (ultra-high performance, allow TCP) - layer 4

  - Gateway load balancer - layer 3

  - classic load balancer (retired in 2023) - layer 4 & 7

    | ALB               | NLB                          | GWLB                                              |
    | ----------------- | ---------------------------- | ------------------------------------------------- |
    | HTTP, HTTPS, gRPC | TCP, UDP                     | GENEVE protocol on IP packets                     |
    | layer 7           | layer 4                      | Layer 3                                           |
    | Static DNS (url)  | static IP through elastic IP | route traffic to firewalls that you manage on EC2 |
    |                   | high performance             | Intrusion detection                               |

create instances, create target group, create ALB

### Scalability VS Elasticity

- Scalability: ability to accommodate a larger load by making the hardware stronger (scale up) or by adding nodes (scale out)
- Elasticity: once a sytsem is scalable, elasticity means that there will be some "auto-scaling" so that the system can scale based on the load. This is cloud friendly: pay-per-use, match demand, optimize costs
- Agility: not related to scalability (distracter), new IT resources are only a click away, which means that you reduce the time to make those resources available to your developers from weeks to just minutes.

### ASG - Auto Scaling Groups

The goal of ASG is to:

- scale out/in to match chaning load
- ensure we have a minimum and a maxinum number of machines running
- automatically register new instances to load balancer
- replace unhealthy instances
- Cost saving: only run at an optimal capacity

Strategies:

- manual scaling: update the size of ASG manually

- dynamic scaling: responde to changing demand

  - simple/step scaling: when a CloudWatch alarm is triggered (eg. CPU >70%) then add 2 units

  - target tracking scaling: eg. I want the avg ASG CPU stay at around 40%
  - scheduled scaling: anticipated a scaling based on known usage patterns

- predictive scaling: use machine learning to predict futire traffic ahead of time, automatically provisions the right number of instance in advance. Useful when yourload has predictable patterns.

## S3

Usage:

- backup and storage
- disaster recovery
- archive
- hybrid cloud storage
- application hosting
- media hosting
- data lakes & big data analytics
- software delivery
- static website

### Bucket 

- allow people to store objects (files) in "bucket"
- buckets must have globally unique name (across all regions, all accounts)
- buckets are defined at the region level
- S3 looks like a global service, but bucket are created in a region
- naming convention: no uppercase, no underscore, 3-63 characters, not an ip, must start with lowercase letter or number, must not start with the prefix "xn--", must not end with suffix "-s3alias".

### Objects

- files, have a key
- the key is the full path: s3://my-bucket/my-folder/my_file.txt
- the key is composed of prefix + object name
- there's no concept of "directories" within buckets (UI trick), just keys with long names that contains "/"
- Objects values are the content of the body, max 5TB, if larger, must use "multi-part upload"
- Metadata: list of text key/value pairs - system or user metadata
- Tags: unique key/value pair, up to ten, useful for security/lifecycle
- Version Id, if versioning is enabled

### Security

- user based:
  - IAM policies - which API calls should be allowed for a user

- resource based
  - bucket policy - bucket wide rules, allow cross account
  - object access control list (ACL) - finer grain (can be disabled)
  - bucket access control list (BCL) - less common (can be disabled)

- Note:
  - an IAM principle can access an S3 object if the user IAM permission allow it **or** the resource policy allows it, **and** there's no explicit DENY

- Encryption: encrypt objects in S3 using encryption keys

### S3 bucket policy:

- JSON based policies
  - Resources: buckets and objects
  - Effect: allow/deny
  - Actions: set of API to allow or deny
  - Principle: the account or user to appky the policy to

- use S3 bucket policy to :
  - grant public access to the bucket
  - force objects to be encrypted at upload
  - grant access to another account

- EC2 instance access: use IAM roles: create a corresponding EC2 instance role with IAM permissions

### static website hosting

Url:

- http://bucket-name.s3-website-aws-region.amazonaws.com
- http://bucket-name.s3-website.aws-region.amazonaws.com

### Versioning

- It is enabled at the buckey level

- some key overwrite will chaneg the "version"
- It is best practice to version your bucket
  - protect againest unintended deletes (ability to restore a version)
  - easy roll back to previous version

- any files that is not versioned prior to enabling versioning will have version "null"
- suspend versioning does not delete previous versions
- deleted objects have a 'delete marker' on its type - delete 'delete marker ' object will restore that object

### Replication (CRR & SRR)

- must enable versioning in source and destination buckets
- Cross-region replication
- Same-region replication
- Buckets can be in different AWS accounts
- copying is asynchronous
- must give proper IAM permissions to S3

Use case:

- CRR: compliance, lower latency access, replication across accounts
- SRR: log aggregation, live replication between products and test accounts

One bucket - management - replication rules - 

Version id keeps same

### S3 storage class

Types:

- Standard - General purpose
  - 99.99% availability
  - Used for frequently accessed data
  - low latency and high throughput
  - sustain 2 concurrent facility failures
  - use case: big data analytics, mobile & gaming applications, content distribution
- Standard - Infrequent Access (IA)
  - less frequently accessed, but require rapid access when needed
  - lower cost than S3 standard
  - 99.9% availability
  - use case: disaster recovery, backups
- One Zone - Infrequent Access
  - high durability in a single AZ, data lost when AZ is destroyed
  - 99.5% availability
  - use cases: store secondary backup copies of on-premise data, or data you can recreate
- Glacier Instant Retrival
  - Glacier: low-cost object storage meant for storage/backups, pricing is price for storage + object retrival cost
  - Millisecond retrieval, great for data retrived once a quarter
  - Minimum storage duration of 90 days
- Glacier Flexible Retrival
  - Expedited (1~5 minutes), standard (3~5 hours), bulk (5~12 hours) - free
  - Minimum storage duration of 90 days
- Glacier Deep Archive - for long term storage
  - standard (12 hours), bulk (48 hours)
  - Minimum storage duration of 180 days
- Intelligent Tiering
  - small monthly monitoring and auto-tiering fee
  - move objects automatically between access tiers based on usage
  - no retrial charges 
  - access tiers
    - frequent access: default
    - infrequent access: not accessed for 30 days
    - archive instance access: 90
    - archive access: 90~700+
    - deep archive access: 180~700+

Can move between classes manully or using S3 lifecycle configurations

Durability VS Availability

- High durability: 99.999999999% (11s 9) if you store 10,000,000 objects, you can expect incur one loss once 10,000 years. Same for all classes
- Availability: measures how readily available a service is, varies depending on storage class. Eg. S3 have 99.99%

### Encryption

- server side encryption: when user upload, default
- client side encryption: before upload

### IAM Access Analyzer

ensure only intended people have access

### Shared responsibility model

AWS:

- Infrastructure
- configuration and vilnerability analysis
- compliance validation

User:

- S3 versioning
- bucket policy
- replication setup
- logging and monitering
- strorage classes
- data encrption at rest and in transit

## Snow Family

Highly secure, portable, offline devices to collect and process data at the edge, and migrate data into and out of AWS.

- Data migration: snowcone (a better version of Snowball (100 TB)), snowball edge (computer suitcase (50-80 TB)), snowmobile (shipping container, pulled by a semi-trailer truck (100 PB))
  - Challenges: limited connectivity, liminited bandwidth, high network cost, shared bandwidth, connection stability

 Edge computing: snowcone, snowball edge

- Pay for device usage and data transfer out of AWS
- data transfer in to S3 is free
- on demand, include a one-time service fee per job
- Committed upfront, monthly/1 year/3 years, up to 62% discount

## Storage gateway

hybrid cloud due to long cloud migration, security migration, compliance requirement, IT strategy

### AWS storage cloud native options 

- Block: Amazon EBS, EC2 instance store
- File: Amazon EFS
- Object: S3, Glacier

### Gateway:

- bridge between on-premise data and cloud data in D3
- hybird storage service to allow on-premise to seamlessly use AWS cloud
- use cases: disaster recovery, backup & restore, tiered storage
- Types:
  - file gateway
  - volume gateway
  - tape gateway

## Database & Analytics

### Intro

storing data on disk (EFS, EBS, ECt instance store, S3) can have its limits, sometimes you want to store in a database, structure data, build indexes to efficiently query and defind relationships.

#### NoSql databases benefits:

- Flexibility: easy to evolve data model
- Scalability: designed to scale-out by using distributed clusters
- high performance: optimized for a specific data model
- high functiona: types optimized for the data model

#### Share responsibility:

AWS:

- AWS offers use to manage different databases
- benefits include
  - Quick provisioning, high availability, vertical and horizontal scaling
  - automated backup & restore, oprations, upgrades
  - operating system patching is handled by AWS
  - monitering and alerting

User:

resiliency, backup, patching, high availability, fault tolerance, scaling...

### RDS

- relational database service
- managed DB service for DB use SQL as a query langiage
- allows you to create databases in the cloud that are managed by AWS: postgres, mysql, mariaDB, oracle, SQL Server, Autora (AWS proprietary database)

Advangate over using RDS VS deploying DB on EC2

- automated provisioning, OS patching
- continuous backups and restore to specific timestamp (point in time restore)
- monitoring dashboards
- read replicas for improved read performance 
- multi AZ setup for DR(disaster recovery)
- Maintenance windows for upgrade
- scaling capability (vertical and horizontal)
- storage backed by EBS (gp2 or io 1)

But you cannot SSH into your instance

#### Aurora

- a proprietart technology from AWS (not open sourced)
- PostgreSQL and MySQL are both supported as Aurora DB
- Autora is AWS cloud optimzied and claims 5x performace improvement over MySQL on RDS, over 3x performance of Postgres on RDS
- Aurora storage automatically grows in increments of 10GB up to 128TB
- Autora costs more than RDS (20% more), but is more efficient
- not in the free tier

Aurora serverless:

- autoomated database instantiation and auto-scaling based on actual usage
- PostgreSQL and MySQL are both supported
- no capacity panning needed
- least management overhead
- pay per second, can be more cost-effective
- use cases: good fro infrequent, intermittent or unpredictable workloads

client connect through a 'proxy fleet', shared storage volume

### RDS deployments

- Read replicas:
  - Scale the read workload of your DB
  - create up to 15 read replicas
  - Data is only written to the main DB

- multi AZ
  - Failover in case of AZ outage (high availability)
  - data is only read/written t the maindatabase
  - can only have 1 other AZ as failover

- Multi-region (read replicas)
  - Read from replicas from other regions
  - disaster recovery in case of region issue
  - local performance for global reads
  - replication costs

### ElastiCache

- Same way RDS is to get managed relational databses
- ElastiCache is to get managed Redis or Memcached
- Caches are in-memory databases with high performace, low latency
- Helps reduce load off databases for read intensive workloads
- AWS takes care of OS maintenance/patching, optimizations, setup, configuration, monitering, failure recovery and backups

### Dynamo DB

- fully named high available with relication across 3AZ
- NoSQL database
- scales to massive worklaods, distributed "serverless" database
- Millions of requests per seconds, trillions of row, 100s of TB of storage
- fast and consistent in performance
- Single-digit millisecond latency, low latency retrieval
- integrated with IAM for security, authorization and administration
- low cost and auto scaling capabilities
- standard & infrequent access (IA) table class

Type of data

- key/value database

  primary key (partition key + sort key) + attributes

- schema defined per item

DynamoDB. Accelerator - DAX

- fully managed I'm-memory cache for DynamoDB
- 10x performance improvement -> microseconds latency
- secure, highly scalable, highly available
- different with ElastiCache at the CCP level: DAX us only iused for and is integrated with DynamoDB, while ElastiCache can be used for ther databases

Global tables

- make a DynamoDB table accessible with low latency in multiple-regions
- Active-Avtive replication, Read/write to any AWS region

### Redshift

- based on PostgreSQL, not used for OLTP
- It's OLAP, online analytical processing (analytics and data warehousing)
- load adata once every hour, not every second
- 10x better performance than other data warehouses, scales to PBs of data
- Columnar storage of data )instread of row based
- massively parallel query execution (MPP), highly available
- pay as you go based on the instances provisioned
- has a SQL interface for performing the queries
- BI tools such as AWS quicksight or Tableau integrate with it

Redshift serverless:

- automatically provisions and scales data warehouse underlying capacioty
- run analytics workloads without managing data warehouse infrastructure
- pay only for what you use (save cost)
- use cases: reporting ,dashboarding application, realtime analytics

### EMR

Elastic MapReduce

- helps creating **Hadoop cluster** to analyze and process big data
- the cluster can be made of hundreds of EC2 instance
- Also support Spark, HBase, Presto, Flink
- EMR takes care of all the provisioning and configuration
- aoto scaling with spot instance

### Athena

- severless query service to perform analytics against **S3 objects**
- use standard SQL
- supports csv, json, orc, Avro and Parquet (built on Presto)
- %5 per TB of data scanned
- use compressed or columnar data for cost savings
- use cases: BI/analytics, reporting, analyze & query VPC flow logs, ELB logs, CloudTrail trails...

### Quicksight

- serverless machine learning-powered BI service to create interactive dashboards
- fast, scalable, embeddable, with per-session pricing
- integrate with others (RDS, aurora, Redshift, Athena, S3)
- Use cases: Business analytics, building visualization, ad-hoc analysis

### DocumentDB

- AWS implementation for MongoDB
- Store, query adn index JSON data
- similar deployment concepts as Aurora
- Fully managed, high available in reoplication across 3 AZ
- automatically grows in increments of 10GB
- automatically scales to workloads with millions of requests per second

### Neptune

- fully managed **graph database**
- across 3 AZ, 15 read replicas
- optimized for complex queries, working with highly connected datasets
- store up to billions of relations, query with millisecond latency
- use cases: knowledge graph, fraud detection, recommendation, social networking

### Timestream

- Fully managed, fast, scalable, serverless, **Time series database**
- built in time series analytics functions

### QLDB

- quantum ledger database
- used to review history of all the changes made to your application data over time
- Immutable system: cryptographically variable, no entry can be removed or modified
- central database, in accordance with financial regulation rules

### Amazon managed blockchain

- join public blockchain networks
- or create own scalable private network
- compatible with Hiperledger Fabric & Ethereal

### Glue

- Managed **extract, transform and load (ETL)** service
- Serverless
- S3/RDS -> Glue ETL -> Redshift

### DMS

 Database migration Service

- sourceDB - ec2 instance running DMS - targetDB
- Support homo/heterogeneous migrations