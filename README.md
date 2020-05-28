# lba2file
Given a raw sector number on a disk, report which file uses that sector

## Usage

```
lba2file  [-b]  <sector number>  /dev/sdX
    -b: Use byte address instead of sector
```

## Example usage with S.M.A.R.T.

If your hard drive has a bad sector, you may want to find out what file is corrupted before you remap the sector by writing zeros to it. You can do so easily using smartctl and lba2file.

```
kremvax$ sudo smartctl -C -t short /dev/sdd    
kremvax$ sudo smartctl -a /dev/sdd | grep '^# 1'
# 1  Short captive   Completed: read failure   90%   20444   1218783739
```

The final number 1218783739 is the disk address in sectors, not bytes:

```
kremvax$ sudo lba2file 1218783739 /dev/sdd
Disk Sector 1218783739 is at filesystem block 152347711 in /dev/sdd1
Block is used by inode 31219834
Searching for filename(s)...
Inode           Pathname
31219834        /home/mryuk/2020-11-03-3045-us-la-msy.jpg
31219834        /home/mryuk/web/2020-11-03-3045-us-la-msy.jpg
```

## Discussion

My script defaults to a sector address (often called "LBA") rather than bytes. This is because LBA is what tools like `smartctl` will report when there is a bad block on the drive. However, if you want to specify an address in bytes instead of sectors, just give the -b flag.

To test that this script is working you can do the reverse, looking up the LBA from the filename using `hdparm --fibmap /foo/bar`.

## Alternatives

You can do the calculations by hand, if you wish. See the answer to https://superuser.com/questions/490787/reverse-lookup-of-inode-file-from-offset-in-raw-device-on-linux-and-ext3-4/

