# RAID-LUKS-LVM

RAID5-LUKS-LVM Config

**Create the partition for your disks with gdisk**

➡️ `gdisk /dev/xvdf`

**Now create a RAID 5 array**

➡️ `mdadm --create /dev/md0 --level 5 --raid-devices 3 /dev/xvdf1 /dev/xvdg1 /dev/xvdh1`

**Check if everything is alright and wait for the State: Clean** ✔️

**Now encrypt your folder with LUKS**

 ➡️ `cryptsetup luksFormat --hash=sha512 --key-size=512 --cipher=aes-xts-plain64 --verify-passphrase /dev/md0`

**Use this command to initialize the volume and create the mapping**

 ➡️ `cryptsetup luksOpen /dev/md0 md0_crypt`

**Create a physical volume with LVM**

 ➡️ `pvcreate /dev/mapper/md0_crypt`

**Create a volume group for your physical (I chose the name vg0)**

 ➡️ `vgcreate vg0 /dev/mapper/md0_crypt`

**Create a logical volume of your volume** 

 ➡️ `lvcreate -n vg0lv0 -l +100%FREE vg0`

**Construct a filesystem, I used XFS**

 ➡️ `mkfs.xfs /dev/vg0/vg0lv0`
 
 **Create a folder at /mnt/**
 
 ➡️ `mkdir /mnt/raid5_homes`
 
 **Now mount the disk**
 
 ➡️ `mount /dev/vg0/vg0lv0 /mnt/raid5_homes/`
 
 **Do cat /etc/mtab and copy the last line "/dev/mapper/vg0....."**
 
 **Now go to /etc/fstab and paste the line there with ,nofail between some words like this** 
 
 ➡️ `,attr2,nofail,inode64,`
 
 **"Optional" - If you want to some extra keys to your folder do**
 
 ➡️ `cryptsetup luksAddKey /dev/md0`
 
 **Everytime you reboot you have to do this**
 
 `cryptsetup luksOpen /dev/md0 md0_crypt` and then `mount -a`
 








