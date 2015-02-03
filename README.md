bare-vpc
========

This [hygroscopic](https://github.com/agperson/hygroscope) CloudFormation template creates a standard VPC layout utilzing a /20 CIDR allocation (4096 available IP addresses).  It creates a single public and private subnet in each provided availability zone.  The public subnets are attached to an internet gateway and the private subnets are ready to be attached to NAT instances.

A mapping file is used to determine subnet address ranges based on the assumption that multiple non-overlapping /20 range VPCs will be connected to each other (via VPC peering) or to one or more data centers (via VPN tunnel or Direct Connect).  Thus, valid VPC CIDR blocks in, for example, the 10.254.0.0/16 range are:
- 10.254.0.0/20
- 10.254.16.0/20
- 10.254.32.0/20
- etc.

One possible configuration is to assign a given region (such as us-east-1) a dedicated /16 block and then create up to 16 /20 VPCs within that region.

Because a few regions support only 2 availiability zones, and because CloudFormation logic is limited, a condition is used to determine whether or not to create subnets in a third availability zone.

Network layout example
----------------------

Here is a sample network layout for a VPC with a 10.254.0.0/20 allocation:

CIDR Range     | Allocation
-------------- | -------------
10.254.0.0/24  | AZ1 - Public
10.254.1.0/24  | AZ1 - Open
10.254.2.0/24  | AZ1 - Open
10.254.3.0/24  | AZ1 - Open
10.254.4.0/24  | AZ1 - Private
10.254.5.0/24  | AZ2 - Public
10.254.6.0/24  | AZ2 - Open
10.254.7.0/24  | AZ2 - Open
10.254.8.0/24  | AZ2 - Open
10.254.9.0/24  | AZ2 - Private
10.254.10.0/24 | AZ3 - Public
10.254.11.0/24 | AZ3 - Open
10.254.12.0/24 | AZ3 - Open
10.254.13.0/24 | AZ3 - Open
10.254.14.0/24 | AZ3 - Private
10.254.15.0/24 | Unallocated
10.254.16.0/24 | Unallocated

The expectation is that the "default" layout will be expanded with additional subnets in each availability zone as needed, which is why only two are specifically allocated per AZ in the base configuration.
