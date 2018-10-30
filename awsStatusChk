#!/usr/bin/env python3

import boto3, json, datetime, re, argparse, os, sys

def _datetime_handler(x):
    if isinstance(x, datetime.datetime):
        return x.isoformat()
    raise TypeError("Unknown type")

def _is_dict(mydict):
    if isinstance(mydict, dict):
        return True
    else:
        return False

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

def _aws_session_builder(**kwargs):
    self_awsprofile = kwargs['myawsprofile']
    self_awsregion = kwargs['myawsregion']
    self_awsapplication = kwargs['myawsapplication']

    try:
        self_awssession = boto3.session.Session(profile_name=self_awsprofile, region_name=self_awsregion)
        self_awsClient = self_awssession.client(self_awsapplication)
        return self_awsClient
    except:
        return("ERROR: Failed to build aws session")

def _aws_ec2_status(**kwargs):
    self_awssession = kwargs['myawssession']

    try:
        self_response_dict = self_awssession.describe_instance_status(Filters=[{'Name': 'event.code', 'Values': ['*']}])
        return self_response_dict
    except:
        return("ERROR: Failed to execute instance status")

def _main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--profile', help='aws profile name to search, default will search all')
    parser.add_argument('--version', action='version', version='%(prog)s 1.0')
    args = parser.parse_args()

    self_awsprofile_list = []
    self_awsregion_instances = []
    self_eventlist = []
    self_total_counter = 0
    self_awsapplication = "ec2"
    self_awsregion_default = "us-east-1"


    if args.profile:
        self_awsprofile_list.append(str(args.profile).split())
    else:
        self_awsprofile_list = _aws_profile_finder()

    print()
    for self_awsprofile in self_awsprofile_list:
        self_awsprofile = str(self_awsprofile[0])

        self_awsrclient = _aws_session_builder(myawsprofile=self_awsprofile, myawsregion=self_awsregion_default, myawsapplication=self_awsapplication)

        for self_awsregion in self_awsrclient.describe_regions()['Regions']:
            self_awsregion = self_awsregion['RegionName']
            self_counter = 0

            self_awsClient = _aws_session_builder(myawsprofile=self_awsprofile, myawsregion=self_awsregion, myawsapplication=self_awsapplication)
            #self_response_dict = _aws_ec2_describe(myawssession=self_awsClient, myonlyrunning=args.onlyrunning, mynotrunning=args.notrunning)
            self_response_dict = _aws_ec2_status(myawssession=self_awsClient)

            self_dict_test = _is_dict(self_response_dict)

            if (self_response_dict is not None) and (self_dict_test is True) and ("ERROR: " not in self_response_dict):
                self_data = json.dumps(self_response_dict, default=_datetime_handler)
                self_data = json.loads(self_data)
                for self_aws_instancestatus in self_data['InstanceStatuses']:
                    self_azone = self_aws_instancestatus['AvailabilityZone']
                    self_instanceid = self_aws_instancestatus['InstanceId']
                    self_events = self_aws_instancestatus['Events']
                    self_counter = self_counter + 1
                    self_total_counter = self_total_counter + 1
                    #print(self_instanceid + ", " + self_azone)
                    if isinstance(self_events, list):
                        for self_event in self_events:
                            self_event['InstanceId'] = self_instanceid
                            self_event['AZone'] = self_azone
                            self_event['AWSProfile'] = self_awsprofile
                            self_eventlist.append("\n")
                            for k, v in self_event.items():
                                self_eventstring = k + " : " + v
                                self_eventlist.append(self_eventstring)
                                #print(k + " : " + v)
            if self_counter > 0:
                for self_eventline in self_eventlist:
                    print(self_eventline)
                print()
                self_aws_result_line = str(self_counter) + " found in profile " + self_awsprofile + " region " + self_awsregion
                self_awsregion_instances.append(self_aws_result_line)
                #print(self_counter + " instances found with events in " + self_awsprofile + " " + self_awsregion)
    print()
    for self_aws_line in self_awsregion_instances:
        print(self_aws_line)
    print("-----------------")
    print(str(self_total_counter) + " Total count\n")

if __name__ == '__main__':
    _main()
