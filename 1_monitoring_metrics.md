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
