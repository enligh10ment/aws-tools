#!/usr/bin/env python3

import boto3, datetime, argparse, os, sys, re

def _aws_profile_finder():
    with open(os.path.join(os.path.expanduser('~'),'.aws/config'), 'r') as f:
        self_lines = f.read().splitlines()

    self_aws_profile_list = []

    for self_line in self_lines:
        if "[profile" in self_line:
            self_fields = self_line.strip().split()
            self_aws_profile_name = re.sub('(])', r'', self_fields[1])
            self_aws_profile_list.append(str(self_aws_profile_name).splitlines())

    return self_aws_profile_list


def _main():
  parser = argparse.ArgumentParser()
  parser.add_argument('--profile', help='aws profile name to search')
  parser.add_argument('--region', help='aws region name to search')
  parser.add_argument('--version', action='version', version='%(prog)s 1.0')
  args = parser.parse_args()

  self_awsprofile_list = []

  self_awsregion_default = "us-east-1"
  if  args.region:
    self_awsregion_default = args.region

  if args.profile:
    self_awsprofile_list.append(str(args.profile).split())
  else:
    self_awsprofile_list = _aws_profile_finder()

  for self_awsprofile in self_awsprofile_list:
    self_awsprofile = str(self_awsprofile[0])

    #print("Profile {profile}".format(profile=self_awsprofile))

    now = datetime.datetime.now()
    self_awssession = boto3.session.Session(profile_name=self_awsprofile, region_name=self_awsregion_default)

    cw = self_awssession.client('cloudwatch')
    s3client = self_awssession.client('s3')

    allbuckets = s3client.list_buckets()

    for bucket in allbuckets['Buckets']:
      # For each bucket item, look up the cooresponding metrics from CloudWatch
      response = cw.get_metric_statistics(Namespace='AWS/S3',
                                        MetricName='BucketSizeBytes',
                                        Dimensions=[
                                            {'Name': 'BucketName', 'Value': bucket['Name']},
                                            {'Name': 'StorageType', 'Value': 'StandardStorage'}
                                        ],
                                        Statistics=['Average'],
                                        Period=3600,
                                        StartTime=(now-datetime.timedelta(days=1)).isoformat(),
                                        EndTime=now.isoformat()
                                        )
      # The cloudwatch metrics will have the single datapoint, so we just report on it. 
      for item in response["Datapoints"]:
        isZero = int(((item["Average"]/1024)/1024)/1024)
        if isZero != 0:
          print("{profile} {bucket} {size:,} GB $ {cost:,} mo".format(profile=self_awsprofile, bucket=bucket["Name"], size=isZero, cost=round(isZero*0.023, 2)))

if __name__ == '__main__':
    _main()
