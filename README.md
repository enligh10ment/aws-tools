# aws-tools

### Overview

Various aws cli tools I made to help me in my day to day.

---

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
 $ time ./sshotgun.py --awsfile list --awsuser centos --command "hostname; uptime"

Failed to ssh to host {'hostname': '172.30.131.89', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x1103949e8>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Error Msg: Authentication failed.

Failed to ssh to host {'hostname': '172.30.131.16', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x1103946d8>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Error Msg: Authentication failed.

Failed to ssh to host {'hostname': '172.30.131.51', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x1103948d0>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Error Msg: Authentication failed.

Host Config: {'hostname': '172.30.130.101', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x110394cf8>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-101.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:37,  0 users,  load average: 0.11, 0.16, 0.18\n']

Host Config: {'hostname': '172.30.131.26', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x110394358>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-131-26.ptr.some.tld\n', ' 22:24:46 up 5 days,  5:21,  0 users,  load average: 0.16, 0.43, 0.47\n']

Host Config: {'hostname': '172.30.130.176', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x110366da0>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-176.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:35,  0 users,  load average: 0.03, 0.05, 0.12\n']

Host Config: {'hostname': '172.30.130.147', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x110394c88>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-147.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:37,  0 users,  load average: 0.82, 0.74, 0.58\n']

Host Config: {'hostname': '172.30.131.57', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x110366f60>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-131-57.ptr.some.tld\n', ' 22:24:46 up 5 days,  2:09,  0 users,  load average: 0.16, 0.25, 0.23\n']

Host Config: {'hostname': '172.30.130.199', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x110394048>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-199.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:41,  0 users,  load average: 0.44, 0.22, 0.17\n']

Host Config: {'hostname': '172.30.130.60', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x1103945c0>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-60.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:39,  0 users,  load average: 0.23, 0.19, 0.19\n']

Host Config: {'hostname': '172.30.130.253', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x10fdd5898>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-253.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:39,  0 users,  load average: 0.84, 0.63, 0.57\n']

Host Config: {'hostname': '172.30.130.77', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x1103944e0>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-130-77.ptr.some.tld\n', ' 22:24:46 up 5 days,  1:40,  0 users,  load average: 0.43, 0.28, 0.22\n']

Host Config: {'hostname': '172.30.131.87', 'username': 'centos', 'pkey': <paramiko.rsakey.RSAKey object at 0x10fdd59e8>, 'timeout': 2, 'allow_agent': False, 'auth_timeout': 2, 'banner_timeout': 2}
Command Output: ['ip-172-30-131-87.ptr.some.tld\n', ' 22:24:46 up 5 days,  5:21,  0 users,  load average: 0.14, 0.17, 0.21\n']

10/13 Succeeded
Failed ssh connect to: 3
SSH key not found for: 0


real	0m0.958s
user	0m0.448s
sys	0m0.102s
```

---

#### Bash

* role-switcher   - tool for use with assume role IAM accounts, add your accounts to the script and this tool will open a formatted url that will auto-fill the assume-role form.

