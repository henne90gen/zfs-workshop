# ZFS Workshop

ZFS - The Last Word in File Systems

- Filesystem like ext4, btrfs or ntfs
- Easy to set up and easy to maintain
- Scales well from small to large applications
  - personal computer
  - home NAS
  - servers/datacenter
- Out of the box features:
  - RAIDZ (special software RAID variant for ZFS)
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
- `echo "Hello World!" > /tank/hello.txt`

## Create Dataset

- `zfs create tank/subset`
- `echo "Hello World!" > /tank/subset/hello.txt`

## Change Dataset Settings

- `zfs set quota=1G tank/subset`

## Finding and Replacing Broken Disks

- Breaking one of the disks: `dd if=/dev/zero of=/tmp/2.img bs=4M count=1`
- Finding the broken disk: `zpool scrub tank`
- Watch progress: `zpool status tank`
- Create new disk: `truncate -s 2G /tmp/new.img`
- Add new disk to pool: `zpool replace tank /tmp/2.img /tmp/new.img`

## Snapshots

- zfs is copy-on-write -> snapshots take up very little space
- `zfs list -t snapshot`
- `zfs snapshot tank@snap1`
- `echo "We made some changes" > /tank/hello.txt`
- `cd /tank/.zfs` (invisible directory `.zfs` in root of dataset)
- `cat /tank/.zfs/snapshot/snap1/hello.txt` (snapshots are readonly)
- `zfs rollback tank@snap1`
- `zfs destroy tank@snap1`

### Automatic Snapshots

- automatic daily, weekly and monthly snapshots are possible
- lots of different solutions
- many don't seem to be well supported or well documented
- I've been using [sanoid](https://github.com/jimsalterjrs/sanoid) successfully

## Encryption

TODO flush this out

- `zfs create -o encryption=on -o keylocation=prompt -o keyformat=passphrase tank/my_encrypted`
- `zfs get encryption tank/my_encrypted` (automatically loaded key and mounted directory)
- `echo "This is secret" > /tank/my_encrypted/secret.txt`
- `zfs unmount tank/my_encrypted && zfs unload-key tank/my_encrypted`
- `zfs load-key tank/my_encrypted && zfs mount tank/my_encrypted`
- `zfs change-key -o keylocation=prompt -o keyformat=passphrase tank/my_encrypted`
