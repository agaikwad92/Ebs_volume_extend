# Ebs_volume_extend
How to increase EBS volume on linux in AWS

How to increase EBS volume:

1.Modify ebs volume in AWS console

2. To get reflect we need to run below commands

A:  Check file system:

[ec2-user@ip-10-0-0-240 ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0   8G  0 part /

[ec2-user@ip-10-0-0-240 ~]$ sudo file -s /dev/xvda1
/dev/xvda1: SGI XFS filesystem data (blksz 4096, inosz 512, v2 dirs)


B:  To extend partition use growpart
[ec2-user@ip-10-0-0-240 ~]$ sudo growpart /dev/xvda 1
CHANGED: partition=1 start=4096 old: size=16773087 end=16777183 new: size=20967391,end=20971487

[ec2-user@ip-10-0-0-240 ~]$ lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  10G  0 disk
└─xvda1 202:1    0  10G  0 part /

[ec2-user@ip-10-0-0-240 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        475M     0  475M   0% /dev
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           492M  396K  492M   1% /run
tmpfs           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1      8.0G  2.7G  5.4G  34% /
tmpfs            99M     0   99M   0% /run/user/1000


C:  Resize the file system to capacity of volume
[ec2-user@ip-10-0-0-240 ~]$ sudo xfs_growfs /dev/xvda1
meta-data=/dev/xvda1             isize=512    agcount=4, agsize=524159 blks
=                       sectsz=512   attr=2, projid32bit=1
=                       crc=1        finobt=1 spinodes=0
data     =                       bsize=4096   blocks=2096635, imaxpct=25
=                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
=                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096635 to 2620923

[ec2-user@ip-10-0-0-240 ~]$ df -h
Filesystem      Size  Used Avail Use% Mounted on
devtmpfs        475M     0  475M   0% /dev
tmpfs           492M     0  492M   0% /dev/shm
tmpfs           492M  396K  492M   1% /run
tmpfs           492M     0  492M   0% /sys/fs/cgroup
/dev/xvda1       10G  2.7G  7.4G  27% /
tmpfs            99M     0   99M   0% /run/user/1000
