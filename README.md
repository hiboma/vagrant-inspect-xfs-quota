# Inspect XFS Quota

## vagrant up

Please install `vagrant-persistent-storage` before `vagrant up`.

```
$ vagrant plugin install vagrant-persistent-storage
$ vagrant up
```

## Block quota is set

```
[vagrant@vagrant-centos65 ~]$ sudo xfs_quota -x -c 'report'
Project quota on /mnt/xfs (/dev/mapper/vps--vg1-xfs)
                               Blocks                     
Project ID       Used       Soft       Hard    Warn/Grace     
---------- -------------------------------------------------- 
vagrant             0      51200      51200     00 [--------]

```

A sample one-line script which block quota (write(2) returns ENOSPC)

```
[vagrant@vagrant-centos65 ~]$ sudo dd if=/dev/zero of=/mnt/xfs/blockquota.dd bs=1024k count=100
dd: writing `/mnt/xfs/blockquota.dd': No space left on device
```

## Inode quota is set

```
[vagrant@vagrant-centos65 ~]$ sudo xfs_quota -x -c 'report -i'
Project quota on /mnt/xfs (/dev/mapper/vps--vg1-xfs)
                               Inodes                     
Project ID       Used       Soft       Hard    Warn/ Grace     
---------- -------------------------------------------------- 
vagrant             1       1000       1000     00 [--------]
```

A sample one-line script which exceeds inode quota ( open(2) returns EDQUOTA)

```
[vagrant@vagrant-centos65 ~]$ sudo perl -e 'for (0..2000){ open my $fh, ">", "/mnt/xfs/$_.txt" or die $! }'
Disk quota exceeded at -e line 1.
```