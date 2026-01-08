# My LVM Project
Project: Secure Disk Partitioning with LVM on CentOS for Enterprise Server. Real-world scenario: Configured CentOS VM as a multi-user file server for a small business – partitioned disks for OS, user data (/home), logs (/logs), and backups (/backup) using LVM for flexibility and easy expansion. Prevents full-disk issues and enhances data isolation.

![Step 1](/1.png)  
Create a virtual machine using VirtualBox, and apply your configurations

![Step 3](/4.png)  
Enter VM Settings → Storage → Controller: SATA → select hard disk icon

![Step 4](/5.png)  
Hard Disk selector, click the Create New icon

![Step 5](/6.png)  
Name file, set size (20GB), choose format

![Step 6](/8.png)  
Verify hard disk created → ready to launch VM

![Step 7](/9.png)
Boot VM & log in 
-Run lsblk > list disks, LVM, swap devices
![Step 8](/10.png)  
Lsblk shows size, mount point, and type
(Note: A disk can only provision 4 partitions. To circumvent this limitation, LVM allows you to create, edit, and manage partitions more easily)
fdisk /dev/sdc open device partition table 
![Step 9](/11.png) 
A list of prompts will follow to input the desired parameters for your new logical partition. 

Prompt........ Select: P (primary) or E (extended) determines the type of partition you are to create.   Here we are using P (primary)

Partition number: identifies which partition of a disk you are creating  (1 -4) or (E -extended) 

-First sector: first 516 bytes of the LVM

-Last sector: size of the LVM determined by available space

-Partition table prompt command: T > to change the ID type of LVM 

-Selected partition: identify specific partition 
![Step 10](/12.png) 
8e -LVM partition type
w to save and exit

![Step 11](/13.png)
pvcreate /dev/sdc1 creates a physical volume 

![Step 12](/14.1.png)
vgcreate data /dev/sdc1 = creates physical volumes, (data) provision a name for the volume group
 Note: Several logical volumes can be created under one volume group (VG)
 
![Step 13](/14.png)
Lvcreate -L 5m -n home_lv data =  creates LV, (-L) determines sizes of LV,  (-n) names the LV, (data) identifies VG
*Repeat Lvcreate for each division ( /home, /logs, & /backup ) to create flexibility, security, and expansion, allowing for data isolation and preventing disk issues

![Step 14](/16.png) 
Make a filesystem using (MKFS) with .ext4 
mkfs.ext4 /dev/mapper/data-home_lv 
repeat step for each division ( /home, /logs , /backup) 
-Create a mount point for each division: mkdir (/home, /logs, /backup)

![Step 15](/17.png)  
mount /dev/mapper/data-home_lv /data = mount FS to logical volume

![Step 16](/18.png)
lsblk / df -hT
Verify the mount using

![Step 17](/15.png) 
vi /etc/fstab sets permanent mount points for partitions & initiates auto mount in boot

![Step 18](/19.png) 
Vi /etc/fstab / mount -a 
Set permanent mount points

![Step 19](/20.png)
Insert entry into fstab  /dev/mapper/data-home_lv  /home  ext4 defaults 0 0 
Note: repeat for each division (/home, /logs, /backup)

![Step 20](21.png) 
Run the command (mount -a), then reboot VM  
Verify LV is persistent (lsblk, df -ht)
![Step 21](/22.png)

![Step 22](/23.png) 
review /etc/ftsab, verify persistence lsblk / df -hT

![Step 23](/24.png) 
df -hT to view filesystems

![Step 24](/25.1.png) 
Let's extend our filesystems, lvextend to increase size of each filesystem

![Step 25](/25.2.png) 
Lvdisplay to show the table of logical volumes

![Step 26](/25.png) 
Lvdisplay shows updated LV info

![Step 27](/26.png)  
Verify extension

![Step 28](/27.png) 
df -hT verify 

Success!!!! 





