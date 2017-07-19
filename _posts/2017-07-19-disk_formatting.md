---
title: Disk formatting
---
# Standard layout

| start  | end/size | code | use |
| ------ | -------- | ---- | --- |
| 34 | 2047 | ef02 | BIOS boot partition |
| 2048 | +512 MiB | ef00 | EFI system partition |
| ... | +512 MiB | 8300 | /boot |
| ... | +x MiB | 8300 | / |

# script
```
#!/bin/bash
set -e

DISK=$1 && [ $DISK ]
echo "going to partition ${DISK}"
sleep 2

sgdisk -og ${DISK}
sgdisk -a 1 -n 1:34:2047 -c 1:"BIOS Boot Partition" -t 1:ef02 ${DISK}
sgdisk -n 2:2048:413695 -c 2:"EFI System Partition" -t 2:ef00 ${DISK}
sgdisk -n 3:413696:823295 -c 3:"Linux /boot" -t 3:8300 ${DISK}
#ENDSECTOR=`sgdisk -E $1`
#sgdisk -n 4:823296:$ENDSECTOR -c 4:"Linux" -t 4:8300 $1
#sgdisk -n 4:823296:0 -c 4:"Linux" -t 4:8300 ${DISK}
sgdisk -p ${DISK}

mkfs.vfat ${DISK}2
mkfs.ext4 ${DISK}3
#mkfs.btrfs ${DISK}4
```
