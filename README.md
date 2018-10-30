# aws-tools

### Overview

Various aws cli tools I made to help me in my day to day.

### Tools

#### Python w/boto
* awsSearch.py    - tool to search/find ec2 instances in all regions and report back details. output from script can be used with sshotgun.py
```
 $ ./awsSearch.py --profile control --verbose --search terraform

i-0deadbeef2ff888,control,us-east-1d,172.30.333.104,running,2018-07-31T17:38:12+00:00,t2.medium,control-pem
island: control
healing-strategy: manual
application: terraform
Name: terraform y29CWY0U
cluster: production
provisioned-by: terraform
requester: team:automation
aws:autoscaling:groupName: tf-asg-deadbeef0000000006
service: terraform
index: 1


1 found in profile control region us-east-1 that match your search terms terralign
-----------------
1 Total count
```

* awsStatusChk.py - tool to list out any ec2 instances that are marked for maintenance.
```
 $ ./awsStatusChk.py

Code : instance-stop
Description : [Completed] The instance is running on degraded hardware
NotBefore : 2018-10-29T17:00:00+00:00
InstanceId : i-0deadbeefdeadbeef
AZone : us-east-1b
AWSProfile : dev


1 found in profile dev region us-east-1
-----------------
1 Total count
```

* s3BucketSize.py - tool to list out any s3 buckets larger than 1 GB with a cost estimate.
```
 $ (./s3BucketSize.py --profile control) | sort -nrk 3,3 | column -t
control  some-bucket-1   11  GB  $  0.25  mo
control  some-bucket-2   4   GB  $  0.09  mo
control  some-bucket-3   4   GB  $  0.09  mo
control  some-bucket-4   1   GB  $  0.02  mo
```

* sshotgun.py     - tool that uses the non-verose output of `awsSearch.py` as input and mass runs a command on ec2 instances. 2200+ instnaces across multiple AWS accounts took under 20 seconds.
```
example coming soon
```

#### Bash
* role-switcher   - tool for use with assume role IAM accounts, add your accounts to the script and this tool will open a formatted url that will auto-fill the assume-role form.

