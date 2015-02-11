hygro-vpc-base
==============

This [hygroscopic](https://github.com/agperson/hygroscope) CloudFormation template creates a standard VPC layout utilzing a /20 CIDR allocation (4096 available IP addresses).  It creates a single public and private subnet in each provided availability zone.  The public subnets are attached to an internet gateway and the private subnets are ready to be attached to NAT instances.

A mapping file is used to determine subnet address ranges based on the assumption that multiple non-overlapping /20 range VPCs will be connected to each other (via VPC peering) or to one or more data centers (via VPN tunnel or Direct Connect).  Thus, valid VPC CIDR blocks in, for example, the `10.254.0.0/16` range are:
- `10.254.0.0/20`
- `10.254.16.0/20`
- `10.254.32.0/20`
- etc.

One possible configuration is to allocate a given region (such as us-east-1) a dedicated /16 block and then create up to 16 /20 VPCs within that region.

Because a few regions support only 2 availiability zones, and because CloudFormation logic is limited, a condition is used to determine whether or not to create subnets in a third availability zone.

Network layout example
----------------------

The bare VPC is created with the following subnets pre-allocated, leaving ample space for additional subnets to be added within each AZ for different functions.  Such additional uses could include, for example, services that need an unroutable data tier that is more isolated than the "private" subnets, or a set of subnets that are routed back to a datacenter only.

A standard of a /20 should provide sufficient space for these variations in most cases by granting an additional 3 /24 subnets (or more smaller subnets) within each of three availability zones.  One downside of this approach is an orphaned /24 block at the end with no clear functional use.

<table>
  <tr><th>AZ</th><th>Subnet</th><th>Purpose</th></tr>
  <tr><td rowspan="5">1</td><td>0</td><td>Public</td></tr>
  <tr><td>1</td><td></td></tr>
  <tr><td>2</td><td></td></tr>
  <tr><td>3</td><td></td></tr>
  <tr><td>4</td><td>Private</td></tr>

  <tr><td rowspan="5">2</td><td>5</td><td>Public</td></tr>
  <tr><td>6</td><td></td></tr>
  <tr><td>7</td><td></td></tr>
  <tr><td>8</td><td></td></tr>
  <tr><td>9</td><td>Private</td></tr>

  <tr><td rowspan="5">3</td><td>10</td><td>Public</td></tr>
  <tr><td>11</td><td></td></tr>
  <tr><td>12</td><td></td></tr>
  <tr><td>13</td><td></td></tr>
  <tr><td>14</td><td>Private</td></tr>

  <tr><td>None</td><td>15</td><td>Unallocated</td></tr>
</table>
