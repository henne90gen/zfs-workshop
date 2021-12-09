# ZFS Workshop

ZFS - The Last Word in File Systems

- Filesystem like ext4, btrfs or ntfs
- Easy to set up and easy to maintain
- Scales well from small to large applications
  - personal computer
  - home NAS
  - servers/datacenter
- Out of the box features:
  - raidz
  - compression
  - encryption
  - read and write cache for better performance
  - snapshots

## Status Commands

- `zpool status`
- `zfs list`

## Create Pool

- `for i in {1..3}; do truncate -s 2G /tmp/$i.img; done`
- `zpool create tank raidz1 /tmp/1.img /tmp/2.img /tmp/3.img`

## Create Dataset

- `zfs create tank/subset`

## Change Dataset Settings

- `zfs set quota=1G tank/subset`

## Finding and Replacing Broken Disks

- Breaking one of the disks: `dd if=/dev/zero of=/tmp/2.img bs=4M count=1 2>/dev/null`
- Finding the broken disk: `zpool scrub tank`
- Watch progress: `zpool status tank`
- Create new disk: `truncate -s 2G /tmp/new.img`
- Add new disk to pool: `zpool replace tank /tmp/2.img /tmp/new.img`

## Snapshots

- `zfs list -t snapshot`
- `zfs snapshot tank@snap1`
- `zfs destroy tank@snap1`

### Automatic Snapshots

- lots of different solutions
- many don't seem to be well supported or well documented
- I've been using [sanoid](https://github.com/jimsalterjrs/sanoid) successfully

## Encryption

TODO flush this out

- `zfs create -o encryption=[algorithm] -o keylocation=[location] -o keyformat=[format] tank/my_encrypted`
- `zfs load-key [-nr] [-L location] [-a] tank/my_encrypted`
- `zfs change-key [-l] [-o keylocation=location] [-o keyformat=format] [-o pbkdf2iters=value] tank/my_encrypted`

## ZFS as root file system

TODO

### With Encryption

TODO
