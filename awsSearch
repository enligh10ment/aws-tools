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

def _aws_ec2_describe(**kwargs):
    self_awssession = kwargs['myawssession']
    self_onlyrunning = kwargs['myonlyrunning']
    self_notrunning = kwargs['mynotrunning']

    try:
        if self_onlyrunning == 1:
            self_response_dict = self_awssession.describe_instances(Filters=[{ 'Name': 'instance-state-name', 'Values': ['running']}])
        elif self_notrunning ==1:
            self_response_dict = self_awssession.describe_instances(Filters=[{'Name': 'instance-state-name', 'Values': ['pending', 'shutting-down', 'terminated', 'stopping', 'stopped']}])
        else:
            self_response_dict = self_awssession.describe_instances()
        return self_response_dict
    except:
        return("ERROR: Failed to execute describe instances")

def _main():
    parser = argparse.ArgumentParser()
    parser.add_argument('--profile', help='aws profile name to search, default will search all')
    parser.add_argument('--version', action='version', version='%(prog)s 1.0')
    parser.add_argument('--verbose', nargs='?', const=1, type=int, help='print out aws tags')
    parser.add_argument('--onlyrunning', nargs='?', const=1, type=int, help='only print those that are running')
    parser.add_argument('--notrunning', nargs='?', const=1, type=int, help='only print those not running')
    parser.add_argument('--search', help='string to search for')
    args = parser.parse_args()

    self_awsprofile_list = []
    self_awsregion_instances = []
    self_awsapplication = "ec2"
    self_awsregion_default = "us-east-1"
    self_default = "None"
    self_total_counter = 0

    if args.search:
        self_aws_search_string = args.search
        #print("Search String: {exp}".format(exp=self_aws_search_string))
    else:
        self_aws_search_string = '.*'
    self_exp = re.compile(self_aws_search_string, re.IGNORECASE)

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
            self_response_dict = _aws_ec2_describe(myawssession=self_awsClient, myonlyrunning=args.onlyrunning, mynotrunning=args.notrunning)

            self_dict_test = _is_dict(self_response_dict)

            if (self_response_dict is not None) and (self_dict_test is True) and ("ERROR: " not in self_response_dict):
                self_data = json.dumps(self_response_dict, default=_datetime_handler)
                self_data = json.loads(self_data)

                for self_aws_reservations in self_data['Reservations']:
                    for self_aws_Instances in self_aws_reservations['Instances']:
                        self_InstanceId = self_aws_Instances.get('InstanceId', self_default)
                        self_InstanceType = self_aws_Instances.get('InstanceType', self_default)
                        self_KeyName = self_aws_Instances.get('KeyName', self_default)
                        self_LaunchTime = self_aws_Instances.get('LaunchTime', self_default)
                        self_PrivateIpAddress = self_aws_Instances.get('PrivateIpAddress', self_default)
                        self_AZone = self_aws_Instances.get('Placement', self_default)
                        self_AZone = self_AZone.get('AvailabilityZone', self_default)
                        self_Tags = self_aws_Instances.get('Tags', self_default)
                        self_States = self_aws_Instances.get('State', self_default)
                        self_State = self_States.get('Name', self_default)

                        if (re.search(self_exp, str(self_InstanceId))) or (re.search(self_exp, str(self_InstanceType))) or (re.search(self_exp, str(self_KeyName))) or (re.search(self_exp, str(self_PrivateIpAddress))) or (re.search(self_exp, str(self_Tags))) or (re.search(self_exp, str(self_State))) or (re.search(self_exp, str(self_AZone))):

                            print(self_InstanceId + "," + self_awsprofile + "," + self_AZone + "," + self_PrivateIpAddress + "," + self_State + "," + self_LaunchTime + "," + self_InstanceType + "," + self_KeyName)

                            if args.verbose == 1:
                                if isinstance(self_Tags, list):
                                    for self_awsTag in self_Tags:
                                        print(self_awsTag['Key'] + ": " + self_awsTag['Value'])
                                else:
                                    print(str(self_Tags))
                                print()

                            self_counter = self_counter + 1
                            self_total_counter = self_total_counter + 1

            if self_counter > 0:
                self_aws_result_line = str(self_counter) + " found in profile " + self_awsprofile + " region " + self_awsregion + " that match your search terms " + self_aws_search_string
                self_awsregion_instances.append(self_aws_result_line)

    print()
    for self_aws_line in self_awsregion_instances:
        print(self_aws_line)
    print("-----------------")
    print(str(self_total_counter) + " Total count\n")

if __name__ == '__main__':
    _main()
