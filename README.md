ip-ranges RT/SG updater
=======================

Lambda function that updates the list of CIDRs in a given AWS Route Table
or Security Group with prefixes obtained from:
https://ip-ranges.amazonaws.com/ip-ranges.json

Lambda can be subscribed to the SNS topic and called every time the ip-ranges
list changes, or it can be called periodically, e.g. 1x per day.

Configuration
-------------

Configuration parameters are passed to the function as environment variables.

To select certain prefixes from ip-ranges.json use the following
JSON syntax:

* SELECT = [
	{
		"region": "ap-southeast-2",
		"services": [ "+AMAZON", "+EC2", "-S3" ] },
	{
		"region": "us-east-1",
		"services": [ "=AMAZON" ]
	}
]

The above filter will select prefixes from `ap-southeast-2` that
have either `AMAZON` or `EC2` service but _not_ `S3` service.
From `us-east-1` only the prefixes that have only `AMAZON` service
tag and no other tags (e.g. will not select a prefix that is _both_
`AMAZON` and `EC2`).

To update a _Route Table_ pass in these two variables:

* ROUTE_TABLES = rtb-12345678, rtb-abcdefgh
* RT_TARGET = igw-..., nat-..., vgw-...

To update a _Security Group_ pass in these two variables:

* SECURITY_GROUPS = sg-1234abcd, sg-abcd1234
* SG_INGRESS = tcp/80, tcp/443
* SG_EGRESS = tcp/443, udp/1234

Permissions
-----------

The lambda function needs permissions to update the _Security Groups_ and/or
the _Route Tables_.

It should also have permissions to write logs to _CloudWatch Logs_.

Author
------

Michael Ludvig @ Enterprise IT Ltd
