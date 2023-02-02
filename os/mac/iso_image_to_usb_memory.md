# ISOイメージをUSBメモリーに焼く

**diskutil list**

disk3 が USBメモリーになっている
```
/dev/disk3 (external, physical):
   #:                    TYPE NAME      SIZE     IDENTIFIER
   0:  FDisk_partition_scheme          *8.0 GB   disk3
   1:              DOS_FAT_32 8GB-2     8.0 GB   disk3s1
```

**diskutil eraseDisk MS-DOS UNTITLED /dev/disk3**

USBメモリーを初期化
```
Started erase on disk3
Unmounting disk
Creating the partition map
Waiting for partitions to activate
Formatting disk3s2 as MS-DOS (FAT) with name UNTITLED
512 bytes per physical sector
/dev/rdisk3s2: 15203296 sectors in 1900412 FAT32 clusters (4096 bytes/cluster)
bps=512 spc=8 res=32 nft=2 mid=0xf8 spt=32 hds=255 hid=411648 drv=0x80 bsec=15233024 bspf=14847 rdcl=2 infs=1 bkbs=6
Mounting disk
Finished erase on disk3
```

**diskutil unmountDisk /dev/disk3**

USBメモリーをアンマウント
```
Unmount of all volumes on disk3 was successful
```

**sudo dd if=~/Documents/modules/Linux/CentOS-7-x86_64-Minimal-1804.iso of=/dev/disk3 bs=1m**

USBメモリーに ISOイメージをコピー
```
115968+0 records in
115968+0 records out
950009856 bytes transferred in 1174.534522 secs (808839 bytes/sec)
```

**diskutil eject /dev/disk3**

USBメモリーをアンマウント
```
Disk /dev/disk3 ejected
```
