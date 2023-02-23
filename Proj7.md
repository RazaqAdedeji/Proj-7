# Documentation on Project 7

1.  PREPARE NFS SERVER

![alt text](./images/1%20lsblk%20fst.png)

![alt text](./images/2%20lsblk%20scnd.png)

![alt text](./images/3%20install%20lvm.png)

![alt text](./images/4%20pvcreate.png)

![alt text](./images/5%20vgcreate.png)

- Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs


![alt text](./images/6%20attached%20partition.png)

- Instead of formating the disks as ext4 you will have to format them as xfs

![alt text](./images/7%20format%20xfs.png)

- Create mount points on /mnt directory for the logical volumes as follow:
Mount lv-apps on /mnt/apps 
Mount lv-logs on  /mnt/logs 
Mount lv-opt on  /mnt/opt 

![alt text](./images/8%20mount%20lv.png)

- Install NFS server, configure it to start on reboot and make sure it is u and running

'sudo yum -y update'

'sudo yum install nfs-utils -y'

'sudo systemctl start nfs-server.service'

'sudo systemctl enable nfs-server.service'

'sudo systemctl status nfs-server.service'

- Make sure we set up permission that will allow our Web servers to read, write and execute files on NFS:

'sudo chown -R nobody: /mnt/apps'

'sudo chown -R nobody: /mnt/logs'

'sudo chown -R nobody: /mnt/opt'


'sudo chmod -R 777 /mnt/apps'

'sudo chmod -R 777 /mnt/logs'

'sudo chmod -R 777 /mnt/opt'

'sudo systemctl restart nfs-server.service'

- Configure access to NFS for clients within the same subnet (example of Subnet CIDR – 172.31.32.0/20 )

![alt text](./images/9%20edit%20vi.png)

'sudo exportfs -arv'

![alt text](./images/10%20export%20.png)

- Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)

'rpcinfo -p | grep nfs'

![alt text](./images/grep.png)

2. CONFIGURE THE DATABASE SERVER

![alt text](./images/11%20DB%20mysql%20create.png)

3. Prepare the Web Servers

- Install NFS client

'sudo yum install nfs-utils nfs4-acl-tools -y'

- Mount /var/www/ and target the NFS server’s export for apps

'sudo mkdir /var/www'
'sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www'

![alt text](./images/12%20mount%20app%20on%20www.png)

- Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot

'sudo vi /etc/fstab'

![alt text](./images/13%20vi%20fstab.png)

- Install Remi’s repository, Apache and PHP

'sudo yum install httpd -y'

'sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm'

'sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm'

'sudo dnf module reset php'

'sudo dnf module enable php:remi-7.4'

'sudo dnf install php php-opcache php-gd php-curl php-mysqlnd'

'sudo systemctl start php-fpm'

'sudo systemctl enable php-fpm'

'setsebool -P httpd_execmem 1'

![alt text](./images/14%20mnt%20logs%20on%20httpd.png)

![alt text](./images/15%20vi%20fstab%20httpd.png)

- Fork the tooling source code from Darey.io Github Account to your Github account.

![alt text](./images/16%20Darey%20github.png)

![alt text](./images/17%20git%20clone%20tooling.png)

- Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

![alt text](./images/18%20copy%20github%20html.png)

- Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command 

![alt text](./images/19%20fucntion%20php.png)

- Open the website in your browser http://<Web-Server-Public-IP-Address-or-Public-DNS-Name>/index.php

![alt text](./images/webpage.png)