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


