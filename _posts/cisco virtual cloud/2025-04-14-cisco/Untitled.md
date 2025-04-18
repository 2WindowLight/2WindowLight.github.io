```
Last login: Thu Apr 10 11:47:59 on ttys000
(base) changhee@ichanghuiui-MacBookPro ~ % conda deactivate
changhee@ichanghuiui-MacBookPro ~ % cd /Users/changhee/Downloads
changhee@ichanghuiui-MacBookPro Downloads % ssh -i ongja_key2.pem ec2-user@3.36.103.161
The authenticity of host '3.36.103.161 (3.36.103.161)' can't be established.
ED25519 key fingerprint is SHA256:EogHbv1EIuUavcdARhMB5DxEx3RFJLu9bfuAsjRdOpQ.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '3.36.103.161' (ED25519) to the list of known hosts.
   ,     #_
   ~\_  ####_        Amazon Linux 2
  ~~  \_#####\
  ~~     \###|       AL2 End of Life is 2026-06-30.
  ~~       \#/ ___
   ~~       V~' '->
    ~~~         /    A newer version of Amazon Linux is available!
      ~~._.   _/
         _/ _/       Amazon Linux 2023, GA and supported until 2028-03-15.
       _/m/'           https://aws.amazon.com/linux/amazon-linux-2023/

[root@EC2-STG1 ~]# df -hT /dev/xvda1
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/xvda1     xfs   8.0G  2.3G  5.8G  29% /
[root@EC2-STG1 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk 
└─xvda1 202:1    0   8G  0 part /
[root@EC2-STG1 ~]# blkid
/dev/xvda1: LABEL="/" UUID="9a8e6632-d266-4bc2-8864-55695e6f1b8d" TYPE="xfs" PARTLABEL="Linux" PARTUUID="29e1278b-9909-4aaf-a132-9cb6078244b4"
[root@EC2-STG1 ~]# cat etcfstab
cat: etcfstab: No such file or directory
[root@EC2-STG1 ~]# cat etc/fstab
cat: etc/fstab: No such file or directory
[root@EC2-STG1 ~]# cat /etc/fstab
#
UUID=9a8e6632-d266-4bc2-8864-55695e6f1b8d     /           xfs    defaults,noatime  1   1
[root@EC2-STG1 ~]# 
[root@EC2-STG1 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk 
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  20G  0 disk 
[root@EC2-STG1 ~]# mkfs -t xfs /dev/xvdf
meta-data=/dev/xvdf              isize=512    agcount=4, agsize=1310720 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=1, rmapbt=0
         =                       reflink=1    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=5242880, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
[root@EC2-STG1 ~]# mkdir /data
[root@EC2-STG1 ~]# mount /dev/xvdf /data
[root@EC2-STG1 ~]# echo "EBS Test" > /data/memo.txt
[root@EC2-STG1 ~]# cat /data/memo.txt
EBS Test
[root@EC2-STG1 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0   8G  0 disk 
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  20G  0 disk /data
[root@EC2-STG1 ~]# df -hT /dev/xvdf
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/xvdf      xfs    20G  176M   20G   1% /data
[root@EC2-STG1 ~]# cat /etc/fstab
#
UUID=9a8e6632-d266-4bc2-8864-55695e6f1b8d     /           xfs    defaults,noatime  1   1
[root@EC2-STG1 ~]# blkid
/dev/xvda1: LABEL="/" UUID="9a8e6632-d266-4bc2-8864-55695e6f1b8d" TYPE="xfs" PARTLABEL="Linux" PARTUUID="29e1278b-9909-4aaf-a132-9cb6078244b4"
/dev/xvdf: UUID="02c51fea-381a-479f-982d-677e7125e752" TYPE="xfs"
[root@EC2-STG1 ~]# echo "UUID="02c51fea-381a-479f-982d-677e7125e752 /data xfs defaults.nofail 0 2" >> /etc/fstab
```



```
[root@EC2-STG1 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  20G  0 disk 
└─xvda1 202:1    0   8G  0 part /
xvdf    202:80   0  20G  0 disk /data
[root@EC2-STG1 ~]# df -hT /dev/xvda1
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/xvda1     xfs   8.0G  2.3G  5.8G  29% /
[root@EC2-STG1 ~]# growpart /dev/xvda 1
CHANGED: partition=1 start=4096 old: size=16773087 end=16777183 new: size=41938911 end=41943007
[root@EC2-STG1 ~]# lsblk
NAME    MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
xvda    202:0    0  20G  0 disk 
└─xvda1 202:1    0  20G  0 part /
xvdf    202:80   0  20G  0 disk /data
[root@EC2-STG1 ~]# xfs_growfs -d /
meta-data=/dev/xvda1             isize=512    agcount=4, agsize=524159 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=1, sparse=0, rmapbt=0
         =                       reflink=0    bigtime=0 inobtcount=0
data     =                       bsize=4096   blocks=2096635, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096635 to 5242363
[root@EC2-STG1 ~]# df -hT /dev/xvdf
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/xvdf      xfs    20G  176M   20G   1% /data
[root@EC2-STG1 ~]# fallocate -l 10G /home/10G.dummy
[root@EC2-STG1 ~]# df -hT /dev/xvda1
Filesystem     Type  Size  Used Avail Use% Mounted on
/dev/xvda1     xfs    20G   13G  7.7G  62% /
[root@EC2-STG1 ~]# 
 
```

