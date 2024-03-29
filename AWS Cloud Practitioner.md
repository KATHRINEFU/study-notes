# AWS Cloud Practitioner

[TOC]



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

![](/Users/katefu/Study/Study Notes/Screenshots/Screenshot 2024-01-15 at 3.24.35 PM.png)

EngYcl0MeNzasIKyqOMOBAh3j$4d

## ECS

elastic container server: launch docker containers on AWS

- you must provision & maintain the infrastructure (EC2 instance)
- AWS take care of starting/stopping container
- has integrations with the application load balancer

### Fargate

- no dot provision the infrastructure
- serverless
- AWS just run containers for you based on the CPU/RAM you need

### ECR

elastic container registry: private docker registry on AWS, store docker images

### Serverless

- new paradigm that developers don't have to manage servers anymore
- they just deploy code
- just deploy functions
- Initially serverless = FaaS (function as a servers)
- Pioneered by AWS lambda but now also includes anything that's managed "databases, messaging, storage, etc"
- does not mean no servers, but you just don't manage/see them

So far in this course: S3, DynamoDB, Fargate, lambda

### Lambda

VS EC2:

![](/Users/katefu/Study/Study Notes/Screenshots/Screenshot 2024-01-16 at 4.10.17 PM.png)

Benefits:

- easy pricing
  - pay per request and compute time
  - free tier of 1,000,000 requests and 400,000 GBs of compute time

- integrated with the whole AWS suite of srrvers
- Event-driven: functions get invoked by AWS when needed
- integrate with many programming language
- easy monitering through AWS CloudWatch
- easy to get more resources per functions (up to 10GB of RAM)
- increasing RAM will also improve CPU and network

Use cases: create thumbnails for images uploaded to S3, run a serverless cron job

### API Gateway

eg. building a serverless API (public access to Lambda)

### Batch

- fully managed batch processing at any scale
- efficiently run 100,00s of computing batch jobs on AWS
- a "bitch" job is a job with a start and end end (opposed to continuous)
- batch will dynamically launch EC2 instance or Spot instances
- AWS batch provisions the right amount of compute/memoey
- you submit or schedule batch jobs and AWS Batch does the rest
- Batch jobs are defined as Docker images and run on ECS
- Helpful for cost optimizations and focuing less on the infrastructure

VS Lambda

![Screenshot 2024-01-16 at 4.30.01 PM](/Users/katefu/Study/Study Notes/Screenshots/Screenshot 2024-01-16 at 4.30.01 PM.png)

### Lightsail

- virtual servers, storage, databases and networking
- low & predictable pricisng
- simpler alternative to use EC2, RDS, ELB, EBS, S3...
- great for people with little cloud experience
- get setup notifications and monitering of your lightsail resources
- use cases: simple web apps, websites (templatess for WorkPress), dev/test environment
- high availability but no auto scaling

## Deployments and Managing infrastructure at scale

### CloudFormation

declarative way of outlining your AWS infrastructure for any resources

the CloudFormation creates those for you, in the right order with exact configuration that you specify

Benefits:

- infrastructure as code
  - no resources are manully created, excellent for control
  - changes to infrastructure are reviewed through code

- Cost
  - each resources within the stack is tagged with an identifier so you can easily see how much a stack cost you
  - estimate the costs of your resources using CloudFormation template
  - saving strategy: automation delettion of templates at 5PM and recreated at 8am safely

- productivity
  - Ability to destroy and re-create an infrastructure on the cloud on the fly
  - Automated generation of diagram for your template
  - Declarative programming

- don't re-invent the wheel
  - leverage existing templates on the web
  - Leverage the documentation

- Support (almost) all AWS resources

CloudFormation Stack Designer: see all the resources and relations, different regions/accounts

### CDK

cloud development kit

- Define your cloud infrastructure using a familiar language
- the code is compiled into a CloudFormation template
- you can therefroe deploy infrastructure and application runtime code together
  - great for lambda functions
  - great for docker containers in ECS/EKS

### Elastic Beakstalk

A developer centric view of deploying an application on AWS, uses all components we've seen before, but in one view. Still have full control over the configuration. PaaS, free but you pay for the underlying instances.

- managed service
  - instance config/OS is handled by BeanStalk
  - deployment strategy is configurable but performed by EB
  - capacity provisioning
  - load balancing & auto-scaling
  - application health-monitering & responsiveness

- just the application code is the responsibility of the developer
- three architecture model:
  - Single instance deployment: good for dev
  - LB + ASG: great for production or pre-production web applications
  - ASG only: great for non-web apps in production (eg. Workers)

- support many platforms (docker, go, java, ...)
- Health agent pushes metrics to CloudWatch, checks for app health, publish events

### Code Related

#### CodeDeploy

deploy application automatically: V1 -> V2

- works with EC2 instance
- Work with On-premises servers
- hybrid service
- Servers/instances must be provisioned and confugured ahead of time with CodeDeploy Agent

#### Code Commit

Github's competing product, fully manager, scalable, private, secured, integrated with AWS servers

#### CodeBuild

compiles source code, run test, produces packages that are ready to be deployed, fully managed, serverless, scalable, secure, pay as you go, only pay for the build time

#### Code Pipeline

oschetrate the different steps to have the code automatically pushed to production: code -> build -> test -> provision -> deploy, base for CICD

#### Code Artifact

Storing and retriving dependencies, works with common dependency management tools like maven, cradle, npm, yarn...

Developers and CodeBuild can directly retrieve dependencies from it

#### CodeStar

unified UI to easily manage software developmemt activities in one place

#### Cloud 9

Cloud IDE, edit code in the cloud

### SSM

Systems manager

- helps you manage EC2 and on-premises systems at scale
- another hybrid AWS service
- get oprational insights about the state of your infrastructure
- suite of 10+ products
- most important fratures are:
  - **Patching automation for enhanced compliance**
  - r**un commands across an entire fleet of servers**
  - store parameter configuration with SSM parameter store

- work for Linux, Windows, MacOS and Raspberry Pi OS

How does it work: install SSM agent on to the system, installed by default on Amazon Linux

#### SSM Session Manager

allow you start a secure shell on your EC2 and on-premise servers

- no SSH access, bastion hosts, or SSH keys are needed
- no port 22 needed (better security)
- send session log data to S3 or CLoudWatch logs

### SSM Parameter Store

- secure storage for configuration and secrets
- api keys, passwords, configurations
- serverless, scalable, durable, easy SDK
- control access permissions using IAM
- version tracking

## Global infrastructure

### Route53

Managed DNS

www.google.com -> 12.34.56.78 == A record (ipv4)

routing policy:

- simple routing policy: no health checks
- weighted routing policy: distribute requests to multiple EC2 instances
- latency routing policy: geographically assign
- failover ...: disaster check, health check on primary, if not, go to failover 

### CloudFront

- content delivery network

- imporve read performace, content is cached at the edge

- 216 point of presence globally (edge locations)

- DDos Protection, integrated with Shield, AWS Web Application Firewall

VS S3 Cross Region Replication

![Screenshot 2024-01-18 at 7.30.57 PM](/Users/katefu/Study/Study Notes/Screenshots/Screenshot 2024-01-18 at 7.30.57 PM.png)

### S3 transfer acceleration

upload file in US ->(public www) edge location -> (private AWS) S3 Bucket (AU)

### AWS Global Acceleration

- Use AWS internal network optimize the route to your application of 60% improvement

- 2 AnyCast IP are created for your application and traffic is sent through edge locations, then send to your application
- no caching
- good for HTTP use cases that require static IP addresses
- good for HTTP use cases that required deterministic, fast regional failover

### AWS Outposts

Used in Hybrid Cloud. Server racks that offers the same AWS infrastructure, services, APIs & tools to build your own application on-premises just as in the cloud. You are responsible for the  Outposts rack physical security. 

Benefits:

- low latency access to on-premises systems
- local data processing
- data residency
- easier migration from on-premises to the cloud
- fully managed service

### AWS WaveLength

- WaveLength zones are infrastructure deployments embedded within the telecommunication provider's data centers at the edge of 5G network.
- Bring AWS services to the edge of 5G networks, ultra-low latency. 
- traffic does not leave Communication Server Provider's (CSP) network
- High bandwidth and secure connection to the parent AWS region
- no additional charges or service agreements
- use cases: smart cities, ML-assisted diabetics, AR/VR.

### AWS Local Zones

Place AWS compute, storage, database and other selected AWS services closer to end users to run latency-sensitive applications

## Message queue

decouple your applications:

- use SQS: queue model
- use SNS: pub/sub model
- use Kinesis: real-time data streaming model

### SQS

Simple Queue Service

- Standard queue
  - oldest aws offering, >100 y
  - fully managed, serverless, use to decouple services
  - scales form 1 message per second to 10,000s
  - default retentino of 4-14 days
  - no limit how many in tje queue
  - message are deleted after they're read
  - low latency <10ms
  - consumers ahre the work to read messages & scale horizontally

- fifo queues, messages are processed in order by the consumer

### Kinesis

realtime big data streaming

managed service to collect, process, ana analyze realtime data at any scale

### SNS

Simple notification Service

- the event publishers only send messages to one SNS topic
- as many "event subscribers" as we want to listen to SNS topic notifications
- each subscribers to the topic will get all the messages
- subscribers can be SQS, Lambda, Kinesis, Emails, SMS, Http endpoints

### Amazon MQ

- SQS, SNS are cloud native service
- when migrating to te cloud, instead of re-engineering , we can use Amazon MQ
- Amazon MQ is a managed message broker service for RabbitMQ, ActiveMQ
- AmazonMQ does not scale as much as SQS/SNS
- it runs on servers, can run in Multi-AZ with failover
- it has both queue feature (SQS) and topic features(SNS)

## Cloud Monitering

### CloudWatch

#### Metrics

- metrics is a variable to monitor
- have timestamps

#### Important Metrics

- EC2 instance: CPU, status check, network (not ram), default 5min
- EBS volumes: disk read/writes
- S3 buckets: bucketSizeBytes, NumberOfObjects, AllRequests
- Billing: total estimate charge (only in us-east-I)
- service limit: how much you've been using a service API
- Custom metrics

#### Alarms

- Auto scaling
- EC2 actions
- SNS notifications
- various options, periods
- states: OK, INSUFFICIENT_DATA, ALARM

#### Logs

Logs can collect logs from: elastic beanstalk, ECS, AWS Lambda, CloudTrail based on the filter, CloudWatch log agents on EC2 machines and on-premise servers, Route53 log DNS queries.

- enable realtime
- set retention

### EventBridge

former cloudWatch events

- Schedule: cron jobs
- event pattern: event rules to react to a service doing something
- trifger lambda functions

Default / partner/ custom event bus

- schema registry
- archive events
- replay archived events

### CloudTrail

- provides governance, compliance and audit for your AWS account
- enabled by default
- get a history of events/api calls made within your account by console, sdk, cli, aws service
- can put logs from CloudTrail into CloudWatch Logs or S3
- a trail can be applied to all regions or a single region
- if **a resource is deleted in AWS, investigate CloudTrail first**

### AWS X-Ray

Visual analysis of applications

Solve problems:

- debugging in production
- log format difffer across applications and log analysis is hard
- Debugging: one big monolith is easy, distributed services hard
- No common views of your entire architecture

advantages:

-  **troubleshooting** performance (bottlenecks)
- understand dependencies in a **microservice** architecture
- pinpoint service issues
- review request behavior
- find errors and exceptions
- are we meeying time SLA
- where I am throttled
- identify users that are impacted

### CodeGuru

An ML-powered service for **automated code reviews** and **application performance recommendations**

- CodeGuru Reviewer: automated reviews for static code analysis
- CodeGuru profiler: visibility/recommendation about application performance
  - help understand runtime behavior
  - Improve performace
  - decrease compute cost
  - provide heap memory
  - anomaly detection

Supported Python and Java

### AWS Health Dashboard

- Service history: show all regions all service health, shows historical information for each day, has RSS feed you can subscribe to
- Your account: alerts and remediation guidance when aws is experiencing events that may impact you 