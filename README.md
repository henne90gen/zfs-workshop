# zfs Workshop

## Status Commands

`zpool status`
`zfs list`

## Create Pool

`for i in {1..3}; do truncate -s 2G /tmp/$i.img; done`
`zpool create tank raidz1 /tmp/1.img /tmp/2.img /tmp/3.img`

## Create Datasets

`zfs create tank/subset`

## Change Dataset Settings

`zfs set quota=1G tank/subset`

## Finding and Replacing Broken Disks

Breaking one of the disks: `dd if=/dev/zero of=/tmp/2.img bs=4M count=1 2>/dev/null`
Finding the broken disk: `zpool scrub tank`
Watch progress: `zpool status tank`
Create new disk: `truncate -s 2G /tmp/new.img`
Add new disk to pool: `zpool replace tank /tmp/2.img /tmp/new.img`

## Snapshots

`zfs list -t snapshot`
`zfs snapshot tank@snap1`
`zfs destroy tank@snap1`

### Automatic Snapshots

-   lots of different solutions
-   many don't seem to be well supported or well documented
-   I've been using [sanoid](https://github.com/jimsalterjrs/sanoid) successfully