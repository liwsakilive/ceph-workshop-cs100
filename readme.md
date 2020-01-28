# Operation


##### Temporarily Disable Scrubbing
```
ceph osd set noscrub
ceph osd set nodeep-scrub
```

#### Limit Backfill
```
osd_max_backfills = 1
osd_recovery_max_active = 1
osd_recovery_op_priority = 1
```

# Replace OSD

#### Set OSD down
```
systemctl stop ceph-osd@1
```
#### Purge OSD
```
ceph osd purge 1 --yes-i-really-mean-it
```
#### Unmount
```
umount /var/lib/ceph/osd/ceph-1
```
#### Clean Disk
```
ceph-disk zap  /dev/sdb
```
#### Create new OSD
```
ansible-playbook -i ncp-c.ini  osd-configure.yml --limit ncp-c-3
```

# Host Down and can't data loss


การ Set OSD out จะทำให้ Ceph เกิดการ fillback ของdata ไปยัง osd อื่น
ให้ตัดสินใจว่า มี hardware มาพร้อมหรือยัง
ถ้ามี hardware มาพร้อมแล้ว ก็ให้ set noout

#### Set Cluster to noout. Prevent fillback process
```
ceph osd set noout
```
#### Set OSD out 
```
ceph osd out osd.8
```
#### Purge OSD ใน host นั้นให้หมด 
```
ceph osd purge  osd.8 --yes-i-really-mean-it
```
#### Remove OSD auth 
```
ceph auth del osd.6
```
#### Remove Host on crushmap
```
ceph osd crush rm ncp-c-3
```
#### Check OSD tree
```
ceph osd tree
```
# Add a new Host


#### Set norebalance
```
ceph osd set norebalance
```
#### Run Ansible for Add a new host
```
ansible-playbook -i ncp-c.ini  site.yml --limit ncp-c-3
```
### If ansible run error, you map need to clean disk by this command, and run it again.
```
ceph-disk zap /dev/sdb
```
#### Unset norebalance
```
ceph osd unset norebalance
```