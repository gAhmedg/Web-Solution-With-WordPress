
In this project we will  prepare storage infrastructure on 2 Linux servers and implement a basic web solution using
WordPress. 
WordPress is a free and open-source content management system written in PHP and paired with MySQL as it's backend Relational Database Management System (RDBMS).

# Project consists of two parts:

1. Configure storage subsystem for Web and Database servers based on Linux OS. The focus of this part is to give you practical 
experience of working with disks, partitions and volumes in Linux.

2. Install WordPress and connect it to a remote MySQL database server. This part of the project will solidify your skills of deploying
 Web and DB tiers of Web solution.

# Three-tier Architecture

Three-tier Architecture is a client-server software architecture pattern that comprise of 3 separate layers.

![3-tier Architecture ](/screenshots/3-tier%20Architecture.svg)
1. Presentation Layer (PL): This is the user interface such as the client server or browser on your laptop.
2. Business Layer (BL): This is the backend program that implements business logic. Application or Webserver
3. Data Access or Management Layer (DAL): This is the layer for computer data storage and data access. [Database](https://www.computerhope.com/jargon/d/database-server.htm) Server or File System Server such as [FTP](https://titanftp.com/2022/07/05/what-is-an-ftp-server/) server, or [NFS](https://www.techtarget.com/searchenterprisedesktop/definition/Network-File-System) Server



we use RedHat OS for this project

# LAUNCH AN EC2 INSTANCE THAT WILL SERVE AS “WEB SERVER” and “DB SERVER”.

 1. Launch two EC2 instance one for serve as "Web Server" and "DB Server". Create 3 volumes for each instance in the same AZ as your EC2, each of 10 GiB and Attach all three volumes then create.

 **instance**

 ![instance](/screenshots/instance.png)

 **Volumes**

 ![Volumes](/screenshots/Volumes.png)

 2. Use gdisk utility to create a single partition on each of the 3 disks and repeat it for DB .

    ```sudo gdisk /dev/xvdf ```

    PROMPT: 

    ```
    
    Disk /dev/xvdf: 20971520 sectors, 10.0 GiB
    Sector size (logical/physical): 512/512 bytes
    Disk identifier (GUID): D936A35E-CE80-41A1-B87E-54D2044D160B
    Partition table holds up to 128 entries
    Main partition table begins at sector 2 and ends at sector 33
    First usable sector is 34, last usable sector is 20971486
    Partitions will be aligned on 2048-sector boundaries
    Total free space is 2014 sectors (1007.0 KiB)

    Number  Start (sector)    End (sector)  Size       Code  Name
    1            2048        20971486   10.0 GiB    8E00  Linux LVM

    Command (? for help): w

    Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
    PARTITIONS!!

    Do you want to proceed? (Y/N): yes
    OK; writing new GUID partition table (GPT) to /dev/xvdf.
    The operation has completed successfully.
    Now,  your changes has been configured succesfuly, exit out of the gdisk console and do the same for the remaining disks.
    ```


3. Use lsblk utility to view the newly configuredpartition on each of the 3 disks.

    **Web-server**

    ![lsblkweb](/screenshots/lsblk%20web.png)

    **DB-server**

    ![lsblkDB](/screenshots/lsblk%20DB.png)

4. Install lvm2 package using `sudo yum install lvm2`. 

    
5. Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM

    ```
    sudo pvcreate /dev/xvdf1
    sudo pvcreate /dev/xvdg1
    sudo pvcreate /dev/xvdh1
    ```

6. Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

    ```
    sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
    ```

7. Verify that your VG has been created successfully by running 

    ```
    sudo vgs
    ```

    ![sudo vgs](/screenshots/sudo%20vgs.png)


8. Use lvcreate utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of
    the PV size. NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

    **Web-server**
    
    ```
    sudo lvcreate -n apps-lv -L 14G webdata-vg
    sudo lvcreate -n logs-lv -L 14G webdata-vg
    ```
    **DB-server**
    ```
    sudo lvcreate -n apps-lv -L 14G webdata-vg
    sudo lvcreate -n logs-lv -L 14G webdata-vg
    ```

9. Verify that your Logical Volume has been created successfully by running 

    ```
    sudo lvs
    ```

    ![sudo lvs](/screenshots/sudo%20lvs.png)


    

10. Use mkfs.ext4 to format the logical volumes with ext4 filesystem

     **Web-server**

    ```
    sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
    sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
    ```

     **DB-server**

    ```
    sudo mkfs -t ext4 /dev/webdata-vg/db-lv
    sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
    ```

11. Create /var/www/html directory to store website files in web server and for DB-server /db directory to store DB files.

    **Web-server**

        
        sudo mkdir -p /var/www/html
        
    **DB-server**

        
        sudo mkdir -p /db
        
12. Create /home/recovery/logs to store backup of log data , Mount /var/www/html on apps-lv logical volume (don't forget to mount DB into /db)and Use rsync backup all the files in the log directory /var/log into /home/recovery/logs

    ```
    sudo mkdir -p /home/recovery/logs
    sudo mount /dev/webdata-vg/apps-lv /var/www/html/
    sudo rsync -av /var/log/. /home/recovery/logs/
    ```


    


13. Mount /var/log on logs-lv logical volume then restore log files back into /var/log directory

    ```
    sudo mount /dev/webdata-vg/logs-lv /var/log
    sudo rsync -av /home/recovery/logs/. /var/log

    ```

    

14. UPDATE THE `/ETC/FSTAB` FILE in each **Web and DB server**

    The UUID of the device will be used to update the /etc/fstab file;

    ```
    sudo blkid
    ```

    ![blkid](/screenshots/blkid.png)
    ![blkid](/screenshots/blkid-web.png)

    ```
    sudo vim /etc/fstab
    ```

    Update /etc/fstab in each **Web and DB server**quotes.

    ![fstab](/screenshots/-etc-fstab.png)
    ![fstab](/screenshots/-etc-fstab-web.png)
        



