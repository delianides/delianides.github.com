AWS EBS Volumes
===============

Problem
-------
EBS Volumes attached to EC2 instances as root volumnes only have a 8GB partition by default. We needed the partition to be larger or to fill the entire volume file size. 

Solution
--------
AWS has really good documentation for how to handle this issue. You can find that [here](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/storage_expand_partition.html). Be sure to run `e2fsck -f <vol>` before you create the AMI otherwise you'll have to run it on the instance after its created.
