# DevOps Tooling Website Solution
## Step 1 - Prepare NFS Server
1. Spin up a new EC2 instance with RHEL Linux Operating System.
   
**Log to aws account console and create EC2 instance of t2.micro type with RedHat Server launch in the default region us-east-1. name instance _Linux NFS server_**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2681b07a-2976-4872-9e61-48ba9e73ce71)

**Application and OS Images select RedHat free tire eligable version**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/be10eab8-0f78-4aa0-bdbd-43f98b32d2cc)

**Create new key pair or select existing key**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2a224898-c6a1-416b-b874-b1f3db9f36e5)

**Network setting create new security group or use existing security group**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d61b1359-539e-4c35-bda4-79a582c52681)

**Configure Storage and launch the instance**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b206b1f5-0d72-41d2-9ead-593580d326f4)

**View Instance**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7454b402-4a76-4834-b418-a005afdc1e37)

**Instance Details for web**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/04945132-11cd-4e32-8e72-1aa0d7c81149)


**Configure security group with the following inbound rules:**
- Allow traffic on port 22 (SSH) with source from any IP address. This is opened by default.
- Allow traffic on port 80 (HTTP) with source from anywhere on the internet.
- Allow traffic on port 443 (HTTPS) with source from anywhere on the internet.
  ![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/83aa77a2-0445-45fa-b968-0fad36af7d96)

2. Based on your LVM experience from Project 6, Configure LVM on the Server. Create 3 volumes in the same AZ as your Web Server EC2, each of 20 GiB.

**Add EBS Volume to an EC2 instance**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/86d4d944-ce76-49e3-9b0e-682e330ed485)

**Attach all three volumes one by one to our NFS Web Server EC2 instance**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/64c26f72-13aa-487d-a9f0-c40365672dc5)

 **Open up the Linux terminal to begin configuration**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/84be9f7d-7685-4843-9a2b-02a09e95bf97)

  **List Available Disks**
```
sudo lsblk
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b9686c5d-0179-40e0-b444-8974460f0b35)

**Use df -h command to see all mounts and free space**
```
df -h
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/5e0c9ecc-4988-4742-985b-7272a26f3f9e)

**Use gdisk utility to create a single partition on each of the 3 disks**
```
sudo gdisk /dev/xvdb
```
**List Existing Partitions: To see the current partitions, use the `p` command:**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/2004ce38-2dd7-4dd0-b1d2-06ecd52e5809)

**Create a New Partition: To add a new partition, enter `n`: then Press Enter to accept default value**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/db6f43d8-ee17-4cb1-bb9d-a71cbc92d928)

**Write Changes: Once you've created the desired partitions, write the changes to the disk with `w`:**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6b187aed-4386-4494-b256-e8a12dff6ae8)

**Yes to proceed and complete**
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/fddb01f3-626f-429f-9036-6ea5022be36e)

> we follow the same steps for remaining two and create partishion

**Use lsblk utility to view the newly configured partition on each of the 3 disks.**
```
lsblk
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/55299708-5cfa-4abd-ae9a-7234303b2b6a)

**Install lvm2 package**
```
sudo yum install lvm2
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/55b48752-0cc7-4a5a-971a-ea0bfa8744ea)

**Check for available partitions.**

```
sudo lvmdiskscan 
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/056282fe-8f59-4615-a393-2e0a1f469e95)

**Create Physical Volumes Use pvcreate utility to mark each of 3 disks as physical volumes (PVs) to be used by LVM**
```
sudo pvcreate /dev/xvdb1 /dev/xvdc1 /dev/xvdd1
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e8165611-f10c-4838-9fcb-2790fb1e3fa1)

**Verify that your Physical volume has been created successfully**
```
sudo pvs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/e8ead4b9-48fc-44e1-b57e-c1c6ad3bd2a3)

**Use vgcreate utility to add all 3 PVs to a volume group (VG) Name the VG webdata-vg**
```
sudo vgcreate webdata-vg /dev/xvdb1 /dev/xvdc1 /dev/xvdd1
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/dd7feb65-047e-4189-854b-418e5fef1250)

**Verify that your VG has been created successfully**
```
sudo vgs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/761cec16-28e5-426a-aa1e-f579e142da4f)

**Create Logical Volumes Use `lvcreate utility` to create logical volumes
```
sudo lvcreate -L 14G -n lv-apps webdata-vg
sudo lvcreate -L 14G -n lv-logs webdata-vg
sudo lvcreate -L 14G -n lv-opt  webdata-vg
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6b6b58ef-e55a-4a70-be2e-7a4abad0789a)

**Verify that our Logical Volume has been created successfully**
```
sudo lvs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d0510c5e-f675-4650-8165-b08e8f585bfc)

**Verify the entire setup #view complete setup - VG , PV, and LV**
```
sudo vgdisplay -v
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8ef02e1e-28c6-4ff9-aa8c-866a1e473451)

3. Instead of formatting the disks as ext4 you will have to format them as xfs
- Ensure there are 3 Logical Volumes `lv-opt` `lv-apps`, and `lv-logs`
```
sudo lsblk
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/30185aa4-061f-49e7-8f37-0e6f53b09619)

**Format the Logical Volumes as XFS:**
```
sudo mkfs.xfs /dev/webdata-vg/lv-apps
sudo mkfs.xfs /dev/webdata-vg/lv-logs
sudo mkfs.xfs /dev/webdata-vg/lv-opt
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/66b58d40-98f6-43d3-96a0-8e696f21b1b2)

- Create mount points on `/mnt` directory for the logical volumes as follows: `Mount lv-apps` on /mnt/apps - To be used by webservers ,`Mount lv-logs` on /mnt/logs - To be used by webserver logs, `Mount lv-opt` on /mnt/opt - To be used by Jenkins server in Project 8

**Create Directories**:
```
sudo mkdir /mnt/apps
sudo mkdir /mnt/logs
sudo mkdir /mnt/opt
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d967f7e3-0d89-4e68-9dea-adf3014d771d)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/35a8afb6-96c6-487c-88a0-f2a6ca9fb4c7)

**Mount Logical Volumes**
```
sudo mount /dev/webdata-vg/lv-apps /mnt/apps
sudo mount /dev/webdata-vg/lv-logs /mnt/logs
sudo mount /dev/webdata-vg/lv-opt /mnt/opt

```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/f4308135-587f-4c6d-919b-0b61b61da04c)

**Add Mount Points to /etc/fstab**
```
sudo vi /etc/fstab
```
**Add the following lines**:
```
/dev/webdata-vg/lv-apps /mnt/apps xfs defaults 0 0
/dev/webdata-vg/lv-logs /mnt/logs xfs defaults 0 0
/dev/webdata-vg/lv-opt /mnt/opt xfs defaults 0 0
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/91a056b5-d84f-45bb-87e3-ab83ac6597d8)

**Verify Mounts**:

```
sudo mount -a
```
```
df -h
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4662fc86-dc51-4a26-88d3-f89fcbcd7ec6)

4. Install NFS server, configure it to start on reboot and make sure it is up and running

**Update the System and Install NFS Utilities**:
```
sudo yum -y update
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/8d37fffe-f3a2-4a64-8627-44f348c045e3)

```
sudo yum install nfs-utils -y
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/29c33604-b690-49e1-a027-2e2d917cc0d7)

**Start and Enable NFS Server**:

```
sudo systemctl start nfs-server.service
```
```
sudo systemctl enable nfs-server.service
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/0c99fba5-5916-4a08-a1a4-5b4d340c57bc)

```
sudo systemctl status nfs-server.service
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/048af360-e331-41c5-a7a9-eaf2e29949ba)


5. Export the NFS Mounts'

> Use `subnet cidr` to connect as clients. For simplicity, we will install our all three Web Servers inside the same subnet, but in _production_ set up  would probably want to separate each tier inside its own subnet for higher level of security. To check our subnet cidr - open our EC2 details in AWS web console and locate `Networking` tab and open a Subnet link:

**Set Permissions on Mount Points**
```
sudo chown -R nobody:nobody /mnt/apps
sudo chown -R nobody:nobody /mnt/logs
sudo chown -R nobody:nobody /mnt/opt
sudo chmod -R 777 /mnt/apps
sudo chmod -R 777 /mnt/logs
sudo chmod -R 777 /mnt/opt
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/69154fdf-2a59-42df-86c9-7456789f7fc1)

**Restart NFS Server**
```
sudo systemctl restart nfs-server.service
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1bb1674a-2fb4-461c-b663-683d006c02e0)

6. Configure access to NFS for clients within the same subnet (example of Subnet CIDR - 172.31.16.0/20 ):
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b37f58e1-d9c1-451b-91aa-224b316f74d1)

```
sudo vi /etc/exports
```
**Add the following lines**:

```
/mnt/apps 172.31.16.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/logs 172.31.16.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/opt 172.31.16.0/20(rw,sync,no_all_squash,no_root_squash)
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1818c80f-790d-4cff-9fde-52e27fad870d)

**save and exit from the editor by** `Esc + :wq!`

**Export the NFS Shares**:

```
sudo exportfs -arv
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/46535253-4d4c-4de0-bc1f-98359d16e92e)


7. Check which port is used by NFS and open necessary ports in Security Groups (add new Inbound Rule)

**Check NFS Ports**:

```
rpcinfo -p | grep nfs
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d95f15bf-aad7-456a-a855-75c679105aed)

> **Important note**: In order for NFS server to be accessible from our client,we open following ports:
- `TCP 111`
- `UDP 111`
- `UDP 2049`
- `UDP 2049`
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/801f96f8-47ce-4645-ad4a-aa55659e83f6)


8.  Final Verification

**Test NFS Export**: From a client within the same subnet, try mounting the NFS share.

```
sudo mount -t nfs <NFS-Server-IP>:/mnt/apps /mnt
```
**Verify Access**:

```
ls /mnt
```

## Step 2 - Configure the database server
Log to aws account console and create EC2 instance of t2.micro type with Ubuntu Server launch in the default region us-east-1. name instance MySQL server
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/7692b0cf-2cf2-4692-a726-592ff2e2f58c)
 Follow the same step and finally the instance created like this 
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/1422fff3-ba37-4b08-8919-cbeaa821813a)

1. Install MySQL Server
First, we need to connect to our  Mysql server server
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/4bac19a7-71cb-4a06-9b89-b29d4b48af48)

1.1. Update the package index:
```
sudo apt update
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/6412cdbe-5c23-4e82-aedd-f599db0b6da8)

1.2. Install MySQL server:
```
sudo apt install mysql-server
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3d39ad91-22a5-463f-a601-cc5ffb731836)

1.3 Check status :
```
sudo systemctl status mysql
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/983caa69-beed-4e0e-8f13-cfd18dc42d66)

1.4. Enable MySQL to start on boot:
```
sudo systemctl enable mysql
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/d8fdde26-9b5c-4a2e-8896-4d9a499053af)

1.5. Secure the MySQL installation (set root password, remove test databases, etc.):
```
sudo mysql_secure_installation
```
Follow the prompts to complete the configuration.
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/b9468974-933f-4248-bb02-5147812f5664)


2. Create a database and name it tooling

 2.1. Log in to the MySQL server as the root user:
```
sudo mysql -u root -p
```
2.2. Create the database:
```
CREATE DATABASE tooling;
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/3874798a-58c7-4356-833e-9d978b2381ee)

![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/a1361f43-77a0-4ed3-90ee-d35855611192)

3. Create a database user and name it `webaccess`

```
CREATE USER 'webaccess'@'%' IDENTIFIED BY 'PassWord.1';
```
![image](https://github.com/melkamu372/StegHub-DevOps-Cloud-Engineering/assets/47281626/14b641d4-8b59-4270-bcc4-40daa0a11f93)

4. Grant permission to webaccess user on tooling database to do anything only from the webservers subnet `cidr`
> To grant permissions to a MySQL user based on a specific subnet (CIDR), you can use a series of wildcard characters to match the IP address range. MySQL does not support direct CIDR notation, so you need to translate the CIDR range into a pattern that MySQL understands.

```
GRANT ALL PRIVILEGES ON tooling. * TO 'webaccess'@'172.31.28.68';
```
**Apply the changes**:
```
FLUSH PRIVILEGES;
```
**Exit MySQL**
```
exit
```
## Step 3 - Prepare the Web Servers

 **In this step we will do the following**
 - Configure NFS client (this step must be done on all _three servers_)
 - Deploy a Tooling application to our Web Servers into a shared NFS folder
 - Configure the Web Servers to work with a single MySQL database
**For server One**

1. Launch a new EC2 instance with RHEL 8 Operating System

2. Install NFS client
```
sudo yum install nfs-utils nfs4-acl-tools -y
```
3. Mount /var/www/ and target the NFS server's export for apps
```
sudo mkdir /var/www
```
```
sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www
```
4. Verify that NFS was mounted successfully
```
 df -h
 ```
  
Make sure that the changes will persist on Web Server after reboot:
```
sudo vi /etc/fstab
```

add following line
```
<NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0
```

5. Install Remi's repository, Apache and PHP

```
sudo yum install httpd -y
```
```
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```
```
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```
```
sudo dnf module reset php
```
```
sudo dnf module enable php:remi-7.4
```
```
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
```
```
sudo systemctl start php-fpm
```
```
sudo systemctl enable php-fpm
```
```
setsebool -P httpd_execmem 1
```
> Repeat steps 1-5 for another 2 Web Servers.

6. Verify NFS Mount and Apache Setup 

Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files - it means NFS is mounted correctly. You can try to create a new file
```
touch test.txt
```
from one server and check if the same file is accessible from other Web Servers.


7. Locate the log folder for Apache on the Web Server and mount it to NFS server's export for logs. Repeat step №4 to make sure the mount point will persist after reboot.

8. Fork the tooling source code from Darey.io Github Account to your Github account.


9. Deploy the tooling website's code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

> Note 1: Do not forget to open TCP port 80 on the Web Server.

> Note 2: If you encounter 403 Error - check permissions to your /var/www/html folder and also disable SELinux sudo setenforce 0 To make this change permanent - open following config file 

```
sudo vi /etc/sysconfig/selinux
```
 and set SELINUX=disabled, then restrt httpd.

10. Update the website's configuration to connect to the database (in /var/www/html/functions.php file). Apply `tooling-db.sql` script to your database using this command
 
 ```
  mysql -h <databse-private-ip> -u <db-username> -p <db-pasword> < tooling-db.sql
```

11. Create in MySQL a new admin user with `username: myuser` and `password: password`:
```
INSERT INTO 'users' ('id', 'username', 'password', 'email', 'user_type', 'status') VALUES -> (1, 'myuser', '5f4dcc3b5aa765d61d8327deb882cf99', 'user@mail.com', 'admin', '1');
```

12. Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php and make sure you can login into the websute with myuser user.