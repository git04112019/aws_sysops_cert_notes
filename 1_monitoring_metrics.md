## CloudWatch

**Metrics**
> data points related to resources and applications
Retention Periods:
  * 1 minute metrics are available for 15 days (detailed monitoring)
  * 5 minute metrics are available for 63 days
  * 1 hour metrics are available for 455 days

Metrics can be graphed and compared
You are able to create custom dashboards

## CloudWatch Alarms

Three states of alarm:
  * __OK__ threshold in normal range
  * __ALARM__ threshold has been exceeded
  * __INSUFFICENT__ not enough data to evaluate the state

Main components of Alarm Config
  * __Metric__ The data being measured
  * __Thresholds__ point at which we want some type of notification
  * __Period__ defined amount of time before notifcaiton
  * __Action__ 
    * Change the alarm state
    * Send a notification via SNS

Notifications
  * __AutoScaling__
    * CloudWatch alarm trigger the Scale up/down of resource
  * __EC2__
    * Cloudwatch alarms trigger EC2 actions
      - Recover, Stop, Terminate or Reboot
    * Per Instance metric is required
  * __SNS (Simple Notification Service)__
    * A topic gets triggerd by CW
      - Subscribers to that topic are notified
        - HTML, Email, SQS, Application, Lambda, SMS, etc.

## CloudWatch Events

- Similar to alarms, Instead of configuring thresholds and alarming on metrics, CloudWatch Events are matching event patterns and use targets to react.
- Near real-time
- Consist of three parts:
  * Event Source - operation change in a service or can be scheduled
  * Rules - route matching events to targets
  * Targets - The services that will react to the event
    - There can be more than one target
    - Some of the services that can be targets
      * EC2, Lambdas, ECS task
      * Kinesis Data Streams and Firehose
      * System Manager Run Command and Automation
      * Code* projects
      * SNS and SQS

## CloudWatch Logs

Monitor, store and access your log files from
  * __EC2__ - applications can be configured to send logs
    - Exceptions, rate of errors, etc.
    - Reuqires the installation of CW Unified Agent or Logs Agent (*legacy*)
  * On-Prem Servers - *with Agent installed*
  * CloudTrail - get logs from API activity in your account
  * Route53 - log info from DNS queries

#### Components
  * __Log Events__ - Record of activity recorded by the monitored resource
  * __Log Streams__ - sequence of log events from the same source/application
  * __Log Groups__ - collection of log streams with same access control, monitoring and retention settings
  * __Metric Filters__ - assigned to log groups it extracts data from the groups log streams and converts tat data into a Metric data point
  * __Retention Settings__ - period of time logs are kept. assigned to log groups but applies to all streams in a group


## EC2 Status Checks

* __System Status Checks__
  - Reasons for Failure:
    * Loss of network connectivity
    * Loss of system power
    * Software issues on physical host
    * Hardware issues on the physical host that impact network reachability
  - Resolutions
    * EBS-backed instances:
      - Stop/Start instances to obtain new underlying hardware
    * Instance store-backed instances
      - Terminate and replace the instance for new underlying hardware

* __Instance Status Checks__
  - Monitors the network and software configs on the an instance
  - Must intervene to fix
  - Reasons for Failure:
    * Failed system status checks
    * Incorrect networking or startup config
    * Exhausted memory
    * Corrupted file system
    * Incompatible kernel
  - Solutions:
    * Make instance config changes
    * Reboot the instance

## EC2 Instance Types

__AMI Virtualization Types__

* __Hardware Virtual Machine (HVM)__
  - Executes the master boot record of the root storage device
  - Virtual hardware set allows for running an OS as if it were running on the bare-metal
  - No modification needed
  - Can use hardware extensions
    * Provices fast access to the host hardware
    * Enhanced networking and GPU processing

* __Paravirtual (PV)__
  - Runs a specail boot loader then loads the kernel
  - Can run on hardware that does not support virtualization
  - No hardware extension support
  - PV historically performed faster than HVM but that is no longer the case
  - __NOTE__ : PV has special drivers for networking and storage that used less overhead than HVM. These drivers can no be run on HVM
    * AWS recommends using HVM because the performance is the same as PV and features like enhanced networking and GPU processing can be utilized when neccessary

## EC2 Metrics

_Basic Monitoring_ : 5 minute intervals
_Detailed Monitoring_ : 1 minute intervals (will encure charges)

* CPU Utilization
* Disk Reads/Writes (_Bytes_)
* Disk Read/Write Operations
* Network In/Out (_Bytes_)
* Network Packets In/Out (_Bytes_)
* Status Check Failed

__CloudWatch Agent__ - allows for collection of additiona in-guest metrics and logs from EC2 (and on-prem)
  * Memory, disk-use, and swap file usage, etc.


## EBS Essentials

* Up to 16 TiB (_Roughly 16 Terabytes_)
* Volumes can only be mounted to one instance at a time
* Multiple volumes can be mounted to a single instance
* Raw, Unformated __block__ storage
  - A file system must be created
  - RAID is supported if the OS supports it
* Created in a __AZ__ and must be used with instances in that __AZ__
* Automatically replicated in that __AZ__
  - Protects against hardware failure, but __not__ high availability
* __Persistence__
  - Attached volumes are independent of the instance
    * Terminate the instance will __not__ terminate the volume
  - Root volumes, by default, terminate with the instance
    * This behavior is changed by setting _DeleteOnTermination_ to false
* __Snapshots__ - images or backups of EBS volumes
  - Stored in S3 (charged based on volume's total size)
  - Exact copy of the original volume
    * Encryption included
  - Incremental in nature, but full volume can be restored from any snapshot

## EBS Performance

* __IOPS__ (input/output operations per second)

* __SSDs__ Consistent performance with sequential and random operations
  - Good for frequent read/write using small I/O sizes
  - __IOPS__ measure with 256KiB I/O size
* __HDDs__ Optimal performance with large and sequential operations
  - Good for large streaming workloads (throughput)
  - __IOPS__ measure with 1024KiB I/O size
* __Burst Buckets__
  - Allows an EBS volume to "burst" above baseline perfomance
    * Volumes earn credits, that can be spent whenever volume needs more performance
    * There is a maximum number of credits
  - Not available for Provisioned IOS SSD (io1)
  - Reported as a CloudWatch Metric

#### SSD Volumes
  * __General Purpose SSD Volumes (gp2)__
    - Volume Size: 1GiB to 16TiB
    - 100 to 10K IOPS
      * 3 IOPS per GiB of volume size
        - Minimum of 100 IOPS (below 33.3GiB volume size)
      * Max throughput = 160MiB/s
    - Burst Bucket
  * __Provision IOPS SSD (io1)__
    - 4GiB to 16TiB
    - 100 to 32K IOPS
    - AWs SLA = 99.9 of the time with 10% of provisioned IOPS
    - Max Ratio is 50:1
      * A 640GiB volume size or greater can use max IOPS
    - AWS recommends a ration larger than 2:1 with Provisioned IOPS volumes
    - Max througput = 500MiB/s

#### HDD Volumes
  * __Throughput Optimized HDD Volumes (st1)__
    - Cannot be a boot device
    - Ideal for frequently accessed and throughput intensive workloads
  * __Cold HDD volumes (sc1)__
    - Not supported as a boot device
    - Ideal for infrequently accessed data and lower storage cost
  * __Magnetic volumes__
    - Deprecating
    - Low-cost storage for small volume sizes
    - 1Gib to 1TiB
