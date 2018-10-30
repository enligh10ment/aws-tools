# aws-tools

### Overview

Various aws cli tools I made to help me in my day to day.

### Tools

#### Python w/boto
* awsSearch.py    - tool to search/find ec2 instances in all regions and report back details. output from script can be used with sshotgun.py
* awsStatusChk.py - tool to list out any ec2 instances that are marked for maintenance.
* s3BucketSize.py - tool to list out any s3 buckets larger than 1 GB with a cost estimate.
* sshotgun.py     - tool that uses the non-verose output of `awsSearch.py` as input and mass runs a command on ec2 instances. 2200+ instnaces across multiple AWS accounts took under 20 seconds.

#### Bash
* role-switcher   - tool for use with assume role IAM accounts, add your accounts to the script and this tool will open a formatted url that will auto-fill the assume-role form.

