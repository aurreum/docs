# User Guide for MySQL 

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore MySQL.

The backup and restore features supported by ADPS include:

- Backup sources

	Single or multiple databases, instances

- Backup types

	Full backup, incremental backup, log backup, continuous log backup, logical backup, and synthetic backup

- Backup targets

	Standard storage pool, deduplication storage pool, data synthetic pool, local storage pool, tape library pool, LAN-free pool, and real-time backup pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, replication, lock retry, and backup retry

- Restore types

	Point-in-time restore, logical recovery, instant recovery, log restore and recovery testing

- Restore targets

	Original host and different host

- Restore options

	Database service startup/stop script, binlog applying, MySQL service startup timeout, cache directory, keep the mount point even if job fails

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured all the backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore MySQL, first install the agent on the host where MySQL resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where MySQL resides is on the Aurreum Data Protection Suite's compatibility lists.

The ADPS supports the backup and restore of MySQL, including standalone single instance, standalone multiple instances, source-replica replication, and source-source replication.

Supported MySQL versions include:

MySQL 4.1/5.0/5.1/5.4/5.5/5.6/5.7/8.0

### Install the agent

ADPS agent can be installed on Windows and Linux. You can select the installation method according to your environment.

#### Windows

To install the agent, do the following:

1. Log in to the ADPS console.

2. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

3. From the toolbar, click the **Install agent** icon. The **Install agent** window appears.

4. In the **Install agent** window, do the following:

	(1) From the **Select system** list, select **Windows**.
	
	(2) From the **Select file** list, select the package that you want to install.
	
	(3) Click **Download**.

5. Upload the package to the Windows host.

6. Log in to the Windows host with administrative privileges. Double-click the package and open the installation wizard. Click **Next**.

7. At the **Select components** step, select **MySQL** from the component list. Click **Next**.

8. At the **Configure Aurreum Data Protection Suite agent** step, enter the following:

	```{only} scutech
	![](../images/Common/windows_install.png)
	```

	(1) In the **Backup server address** field, enter the IP or domain name of the backup server.
	
	(2) In the **Backup server port** field, enter the port number. The default value is 50305. If you enable the **Use SSL secure connection** option, enter 60305 in the **Backup server port** field.
	
	(3) The **Access key** field is optional and blank by default. If your backup server adopts multi-tenancy, you must enter the access key of the tenant for the agent.
	
	(4) Click **Next**.


	```{note}
	To get the access key of the user/tenant:
	1. Log in to the ADPS console.
	2. On the upper right corner, click your avatar, and go to **Personal settings** > **Account settings**.
	3. On the **Preference** tab, click **View** to get the access key of the current user/tenant.
	```
 9. Confirm the **Destination folder** or specify another folder. Click **Next**.

 10. Wait for the installation to complete.

#### Linux

For Linux OS, ADPS agent supports online and local installation. We recommend online installation.

1. Online installation: 
ADPS provides `curl` and `wget` commands for installation.
2. Local installation:
See [Offline installation](../agent_install/agent_install.md#offline-installation) in Aurreum Data Protection Suite Agent Installation Guide.

To install the agent online, do the following:

1. Log in to the ADPS console.

2. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

3. From the toolbar, click the **Install agent** icon. The **Install agent** window appears.

4. In the **Install agent** window, do the following:

	(1) From the **Select system** list, select **Linux**.
	
	(2) From the **Component** list, select **MySQL**. The `curl` and `wget` commands appear in the window.
	
	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.
	
	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the feature, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.

6. Log in to the Linux host as user root. Paste the command in the terminal and press Enter to start the installation. Example:

  ```{code-block} python
  root@ubuntu:~# curl -o- "http://192.168.17.31:50305/d2/update/script?modules=mysql&location=http%3A%2F%2F192.168.17.31%3A50305&access_key=572bd4dbb395fd320a30fe9729a21db8&rm=&tool=curl" | sh
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	100  9204    0  9204    0     0  2247k      0 --:--:-- --:--:-- --:--:-- 2996k
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 51.1M  100 51.1M    0     0   141M      0 --:--:-- --:--:-- --:--:--  141M
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 5684k  100 5684k    0     0  48.6M      0 --:--:-- --:--:-- --:--:-- 99.0M
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100  733k  100  733k    0     0  11.5M      0 --:--:-- --:--:-- --:--:-- 11.5M
  ```

7. Wait for the installation to complete.


## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore MySQL, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where MySQL resides. Click the **Register** icon. Then the **Activate** window appears. 

    ```{only} scutech
	![](../images/Common/register_resources.png)
    ```

3. In the **Activate** window, select the MySQL backup license, and click **Submit**. After the activation is completed, the **Authorization** window appears.

```{note}
If you are prompted with "No enough licenses", contact the administrator to add licenses.
```
4. In the **Authorization** window, select user groups to authorize access to the resource. Click **Submit**.

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup type

ADPS provides four common backup types (full backup, incremental backup, log backup, and logical backup) and two advanced (continuous log backup and synthetic backup) for MySQL.

- Full backup

	Backs up all data of database instances.

- Incremental backup

	Backs up only the data that has changed since the last backup with a full backup as the baseline.

- Log backup

	Backs up the binary logs of database instances with a full backup as the baseline. To perform a log backup, the database instance needs to have binary logging enabled.

- Logical backup

	Backs up all data of one or more databases. 

- Continuous log backup

	Backs up the binary log files constantly, with a full backup as the baseline, to the real-time backup pool. To perform a continuous log backup job, the database needs to have binary logging enabled and server_id configured. 

	One instance can only run one continuous log backup job at one time. When a full backup job runs, the continuous log backup job will be waiting.

	Continuous log backups have the following requirements:

	(1) You have the advanced license: MySQL continuous log backup.

	(2) You have created a real-time backup pool for the current user.

- Synthetic backup

	The first synthetic backup is a full backup, and the subsequent ones are incremental. When the synthesis condition is reached, the latest full backup and subsequent incremental backup will be synthesized to create a new full backup copy. Synthetic backups can improve the restore performance. You can directly mount the full backup copy to the target host through an instant recovery job without physical copies and additional storage space.

	Synthetic backups have the following requirements:

	(1) You have the advanced licenses: MySQL synthetic backup and MySQL Copy Data Management.

	(2) The data disks of the storage server use the ZFS file system. 

	(3) You have configured the iSCSI or FC link between the backup server and the host with the agent installed.
	
	(4) You have created a data synthetic pool for the current user.

### Backup policies

ADPS provides 6 types of backup schedules, including immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

1. Perform a **full backup** once a week when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.
2. Perform an **incremental backup** every day when the application traffic is relative small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.
3. Perform a **log backup** hourly if your database supports log backups to achieve the precise point-in-time restore and ensure that the restore granularity can reach the second level.

To use the advanced synthetic backup, we recommend the following backup policy:

1. Perform a **synthetic backup** every day to ensure that you have a recoverable point in time every day.
 2. Perform a **log backup** hourly based on the synthetic backup to achieve the precise point-in-time instant recovery.

### Before you begin

Before you back up and restore MySQL, do the following:

1. Check the MySQL instance status:

- Windows:

	Log in to the host and open the service list. Check whether the MySQL service status is Started or not.

- Linux:

	Open the terminal on the host and use the `systemctl status` command to check the MySQL service status.

	 ```{code-block} python
	 [root@centos7 ~]# systemctl status mysql
	 ● mysql.service - LSB: start and stop MySQL
	  Loaded: loaded (/etc/rc.d/init.d/mysql; bad; vendor preset: disabled)
	  Active: active (running) since Wed 2023-05-10 11:34:08 CST; 5 days ago
	    Docs: man:systemd-sysv-generator(8)
	  CGroup: /system.slice/mysql.service
	         ├─3339 /bin/sh /usr/local/mysql//bin/mysqld_safe --datadir=/usr/local/mysql/data --pid-file=/usr/local/mysql/data/centos7.4.pid
	         └─3567 /usr/local/mysql/bin/mysqld --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --plugin-dir=/usr/local/mysql//lib/plugin --user=m...
	
	 May 10 11:34:07 centos7.4 systemd[1]: Starting LSB: start and stop MySQL...
	 May 10 11:34:08 centos7.4 mysql[3255]: Starting MySQL SUCCESS!
	 May 10 11:34:08 centos7.4 systemd[1]: Started LSB: start and stop MySQL.
	 ```

```{note}
For Linux hosts that do not support the aforementioned method, use the command `service [mysql_service_name] status` to check MySQL service status.
```

2. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.
	
	```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
	```
	```{table} Storage pool required for backups
	---
	class: longtable
	---
	|Backup types|Storage pool types required|
	| ---- | ---------------- |
	|Full backup, incremental backup, log backup, and logical backup|Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage service pool, and LAN-free pool|
	| Synthetic backup | Data synthetic pool |
	| Continuous log backup | Real-time backup pool|
	```


### Log in to the instance

Before you create a backup or restore job, log in to the MySQL resource.

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the host list, find the host where MySQL resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.

    ```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_login_01.png)
   ```

 4. Enter the correct username and password, and click **Login**. 

 5. Check the minimum required privileges for the database user to perform backups:

   ```{tabularcolumns} |\Y{0.15}|\Y{0.60}|\Y{0.25}|
   ```
   ```{table} Minimum privileges required for the database user
   ---
   class: longtable
   ---
   | Backup type      | Minimum privileges required for the database user                     | Note                     |
   | --------- | ---------------------------------------- | ------------------------ |
   | Physical backup|RELOAD, PROCESS, LOCK TABLES, REPLICATION SLAVE, REPLICATION CLIENT | BACKUP_ADMIN required for MySQL 8.0 and above |
   | Logical backup|SELECT, RELOAD, EVENT, EXECUTE, SHOW VIEW, LOCK TABLES, TRIGGER | |
   ```
### Add a MySQL instance manually

On the **Resource** page, if ADPS fails to identify the MySQL resource, you may manually add the MySQL instance.

This method is applicable to the following scenarios: The agent cannot be installed in some environments. And the instance on the agent host cannot be unidentified, resulting in backup failure. Unidentified instances include:

```{only} scutech
- Alibaba Cloud RDS for MySQL
- Huawei Cloud RDS for MySQL
```
- MySQL instance in the container
- MySQL derivatives
- Instance without port open
- Identified MySQL instance with incorrect configuration
- ...

```{note}
When manually adding a MySQL instance, note that:
1. Only users with administrative privileges can manually add MySQL instances.
2. Logical backup and restore are available for cloud-based MySQL. 
```

To add a MySQL instance manually, do the following:

 1. From the menu, click **Resource** > **Resource**, select the MySQL database host, and click **Add MySQL instance** icon. The **Login agent** window appears. 
 2. In the **Login agent** window, enter the username and password of the agent host. The **Add MySQL instance** page appears.
 3. On the **Add MySQL instance** page, at the **Database connection** step, select the connection method.

	- If you select **TCP/IP**, enter the required fields: **Host** and **Port**. The **Username** and **Password** fields are optional. Click **Next**.
	- If you select **Local socket**, enter the **Socket path**. The **Username** and **Password** fields are optional. Click **Next**.

  ```{only} scutech
  ![](../images/Backup_Restore/DBackup3/MySQL/mysql_add_01.png)
  ```
  ```{only} scutech
  ![](../images/Backup_Restore/DBackup3/MySQL/mysql_add_02.png)
  ```

 4. At the **Parameters** step, select the parameter type.

	- If select **Logical**, enter the **Instance name**, **`mysql`Command-line tool path**, **`mysqldump` Path**, **`mysqlpump` Path**. When the parameter is logical, and the instance is successfully added, only the logical backup, logical restore, and recovery testing are available. Click **Next**.
	- If select **Physical**, enter the **Instance name**, **Data directory**, **`mysql`Command-line tool path**, **`mysqldump` Path**, **`mysqlpump` Path**, **`mysql.cnf` Path**, **`binlog` Path**.  When the parameter is physical, and the instance is successfully added, all MySQL backup and restore types are available. Click **Next**.

  ```{only} scutech
  ![](../images/Backup_Restore/DBackup3/MySQL/mysql_add_03.png)
  ```

  ```{only} scutech
  ![](../images/Backup_Restore/DBackup3/MySQL/mysql_add_04.png)
  ```

 5. At the **Activate** step, the license type is selected by default. After granting database resources to user groups, click **Submit**. The instance is successfully added.

### Create a backup job

To create a backup job, do the following:

 1. From the menu, click **Backup**. The backup job wizard appears.

 2. At the **Hosts and resources** step, select the host where MySQL resides and select the resource. The wizard goes to the next step automatically.

 3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) From the **Backup source** list, select the databases that you want to back up.
	
	```{only} scutech
	 ![](../images/Backup_Restore/DBackup3/MySQL/mysql_backup_01.png)
	```
	
4. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
	Incremental backups and log backups must have the same backup target as their baseline backups.
	```

5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select Immediate. ADPS performs the job immediately after it is created.
	- Select One time and set the start time for the job.
	- Select Hourly. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select Daily. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select Weekly. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select Monthly. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

	```{note}
	The **Backup schedule** step is not available for continuous log backups. When a continuous log backup job is created, the binary log files are being backed up constantly to a real-time backup pool.
	```

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.

 ```{only} scutech
 ![](../images/Backup_Restore/DBackup3/MySQL/mysql_backup_02.png)
 ```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After submission, you will be redirected to the **Job** page automatically. On this page, you can start, edit, clone, and delete the job.

### Backup options

ADPS provides the following backup options for MySQL:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.20}|
```
```{table} Common options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }} - None: No compression during backup.{{ br }} - Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }} - Fast: Uses the fast compression algorithms to compress data during the backup job.|Not available for continuous log backup jobs.|
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 255.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.|Not available for log and continuous log backup jobs.|
|binlog|This option is only displayed when the database does not enable binlog.{{ br }}Enabling binlog allows for a specific point-in-time restore. This operation requires to restart the database. |Only available for full and incremental backup jobs.|
|File name|The option appears together with the binlog option.{{ br }}Set the base name for the binary log. You can specify the path where the binary files are stored, but the storage directory requires MySQL user access privileges.|Only available for full and incremental backup jobs.|
|Format|The option appears together with the binlog option.{{ br }}Set the format for binary logs, including ROW, STATEMENT or MIXED.{{ br }} - ROW: Records row changes to the database.{{ br }} - STATEMENT: Records SQL statements.{{ br }} - MIXED: MySQL automatically changes the format from STATEMENT to ROW.|Only available for full and incremental backup jobs.|
|sync_binlog|The option appears together with the binlog option.{{ br }}Sets the number of transactions that need to be committed to synchronize the binary logs from the cache to the disk. When the transaction commits reach the set number, the logs will be flushed to the disk. The value ranges from 0 to 4294967295. 0 indicates that the synchronous flushing is disabled, and the timing of the synchronization is determined by the operating system. The number affects database efficiency and data consistency. The larger the value, the higher the efficiency and the lower the data consistency.|Only available for full and incremental backup jobs.|
|Backed-up binary logs|The option is enabled by default.{{ br }} - Enable: Deletes the backed-up binary logs after the log backup job is completed or the continuous log backup is stopped.{{ br }} - Disable: After the log backup job is completed or the continuous log backup is stopped, the backed-up binary log files are not immediately deleted. Instead, MySQL automatically deletes them based on the configuration. |Only available for log and continuous log backup jobs.|
```

- Advanced options

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.20}|
```
```{table} Advanced options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|Not available for synthetic backup jobs and the backup jobs with a LAN-free pool selected as the backup target.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for synthetic backup jobs and the backup jobs with a LAN-free pool selected as the backup target.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|Not available for synthetic backup jobs.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
|Acquiring database global lock timeout|The job will be stopped if acquiring database global lock timeout. 0 means no timeout. The default unit is minute.|Only available for full, incremental and synthetic backup jobs.|
|Lock retry count|Count of retries to acquire locks.|Only available for full, incremental and synthetic backup jobs.|
|Slow query threshold|Don't add lock before slow queries over this threshold are finished. 0 means no waiting. The default unit is second.|Only available for full, incremental and synthetic backup jobs.|
|Slow query timeout|Timeout to wait slow query to finish. 0 means no waiting.|Only available for full, incremental and synthetic backup jobs.|
|Log copy interval|Time interval between transaction log copying. The unit is millisecond.|Only available for full, incremental and synthetic backup jobs.|
|Backup retry count|Number of attempts to restart the backup when an optimized (without redo logging) DDL operation is performed. 0 means no retries.|Only available for full, incremental and synthetic backup jobs.|
|Backup retry interval|Time interval between backup retries when an optimized (without redo logging) DDL operation is performed. The unit is minute.|Only available for full, incremental and synthetic backup jobs.|
|Connection compression|Uses compression algorithm to connect to MySQL.{{ br }} - For MySQL versions below 8.0.18, the available compression algorithms are zlib and none. The default compression algorithm is none.{{ br }} - For MySQL version 8.0.18 and above, the available compression algorithms are zlib, zstd, and none. The default compression algorithm is zstd.|Only available for logical backup jobs.|
|Additional parameters|The parameters passed to mysqldump. To enter multiple parameters, separate them by space.|Only available for logical backup jobs.|
```

## Restore

For different needs, ADPS provides several restore type for MySQL, including:

- Point-in-time restore

	When a logical error or disaster occurs in a MySQL database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. 

- Logical recovery

	When data loss occurs in a MySQL database such as data files damaged or deleted by mistake, you can perform a logical recovery job to recover the databases or files to the latest state. 

- Instant recovery

	When a disaster occurs in a MySQL database, you can perform an instant recovery job to create a database copy using the synthetic backup set and mount the copy directly to a host with the agent installed, with which the database can become online quickly. The data can be recovered quickly to reduce the recovery time.

	```{note}
	Instant recovery jobs require that you have performed a synthetic backup.
	```

- Log restore

	The restore type does not conduct a database restore; instead, it only downloads the binary logs to the specified directory on the agent host.

	```{note}
	Log restore jobs require that you have performed a log backup or continuous log backup.
	```

- Recovery resting

	Restores the latest backup sets to another path on the original host or to another host hourly, daily, weekly, and monthly.

### Before you begin

To restore MySQL to a different host, install the agent on that host, activate the licenses, and authorize the resource to the current ADPS user.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where the MySQL resides and select the database. Click **Next**.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_timepoint_restore_01.png)
	```

	(1) From the **Restore type** list, select **Point-in-time restore**.

	(2) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**. The default setting is to restore the database to the latest point in time of the newest backup set. You can click the time beside the option. In the pop-up window, select a point in time or time range. If a time range is selected, drag the slider to specify or enter a point in time for the restore. This option is only available for databases with successful log or continuous log backups.

	- Select **Restore to GTID**. It restores the latest GTID of the most recent backup set by using the restore branch and the specified GTID. 
	```{note}
	The **Restore to GTID** is only displayed when the following conditions are met:
	 - You have enabled GTIDs in MySQL configuration files. For the GTID configuration method, please go to MySQL official website and check the relevant documents.
	 - A log backup or continuous log backup job has been completed.
	```

	- Select **Restore to backup state (shortest recovery time)**. It restores data to the latest state of the most recent backup set. Then from the **Backup sets** list, select a backup set. The restore job will restore the data to the latest state of the selected backup set, but will not restore the binlog.

	(3) Click **Next**.

	```{note}
	If the host to be restored is MySQL cluster:
	
	- Log backup sets of all nodes in the cluster can be selected. If no log backups are performed, full and incremental backup sets will be displayed as points in time. After performing log backups, full, incremental and log backup sets will be automatically merged into a time range.  
	
	- At the **Backup sets** step, **Filter** is provided. The filter allows for the exclusion of specific databases from the restore process.
	```

 4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, edit, and delete the job.

 ```{note}
1. Performing point-in-time restore jobs requires restarting the MySQL service, which results in the overwriting of the database data. If the restore target has a periodic log backup job, you need to perform a full backup job on the restore target after a point-in-time restore and before the next log backup.
2. After completing the point-in-time restore job for a MySQL cluster, it is important to verify the correctness of the replication relationships within the cluster. If any replication issues arise, manual configuration of the cluster's replication will be necessary.
 ```

### Create a logical recovery job

To create a logical recovery job, do the following:

 1. From the menu, click **Restore**. The restore job wizard appears.

 2. At the **Hosts and resources** step, select the host where MySQL resides and select the resource. Click **Next**.

 3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_logical_restore_01.png)
	```

	(1) From the **Restore type** list, select **Logical recovery**.

	(2) From the **Restore source** list, select a database and point in time.

	(3) Click **Next**.

 4. At the **Restore target** step, select a restore target. Click **Next**. 

 5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

 6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

 7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

 8. After the submission, you will be redirected to the **Job** page. You can start, edit, and delete the job.

### Create an instant recovery job

```{note}
1. If the storage pool storing synthetic backup sets is of the iSCSI type, you must install the iSCSI Initiator on the host where you perform the restore job.
2. If the storage pool storing synthetic backup sets is of the FC type, you must install the HBA API Driver corresponding to the HBA card on the host where you perform the restore job.
3. When the restore target for an instant recovery job is a cluster, all nodes in the cluster will run the job at the same time. ADPS mounts different LUNs on every node.
```

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where MySQL resides and select the instance. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_instant_restore_01.png)
	```
	(1) From the **Restore type** list, select **Instant recovery**.

	(2) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**. The default setting is to restore the database to the latest point in time of the newest backup set. You can click the time beside the option. In the pop-up window, select a point in time or time range. If a time range is selected, drag the slider to specify or enter a point in time for the restore. This option is only available for databases with successful log or continuous log backups.

	- Select **Restore to GTID**. It restores the latest GTID of the most recent backup set by using the restore branch and the specified GTID. 

	```{note}
	The **Restore to GTID** is only displayed when the following conditions are met:
	 - You have enabled GTIDs in MySQL configuration files. For the GTID configuration method, please go to MySQL official website and check relevant documents.
	 - A log backup or continuous log backup job has been completed.
	```

	- Select **Restore to backup state (shortest recovery time)**. It will restore data to the latest state of the most recent backup set by default. Then from the **Backup sets** list, select a backup set. The restore job will restore the data to the latest state of the selected backup set, but will not restore the binlog.

	(3) Click **Next**.

4. At the **Restore target** step, select a restore target. Click **Next**.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

 8. After the instant recovery, go to the **CDM** page. A mounted copy is added below the full copy. See [View a copy](#view-a-copy).

### Create a log restore job

To create a log restore job, do the following:

 1. From the menu, click **Restore**. The restore job wizard appears.

 2. At the **Hosts and resources** step, select the host where MySQL resides and select the resource. The wizard goes to the next step automatically.

 3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_log_restore_01.png)
	```

	(1) From the **Restore type** list, select **Log restore**.

	(2) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**. The default setting is to restore the database to the latest point in time of the newest backup set. You can click the time beside the option. In the pop-up window, select a point in time or time range. If a time range is selected, drag the slider to specify or enter a point in time for the restore. This option is only available for databases with successful log or continuous log backups.

	- Select **Restore to GTID**. It restores the latest GTID of the most recent backup set by using the restore branch and the specified GTID. 

	```{note}
	The **Restore to GTID** is only displayed when the following conditions are met:
	 - You have enabled GTIDs in MySQL configuration files. For the GTID configuration method, please go to MySQL official website and check relevant documents.
	 - A log backup or continuous log backup job has been completed.
	```

	- Select **Restore to backup state (shortest recovery time)**. It restore data to the latest state of the most recent backup set. Then from the **Backup sets** list, select a backup set. The restore job will restore the data to the latest state of the selected backup set, but will not restore the binlog.

	(3) Click **Next**.

 4. At the **Restore target** step, select a restore target. Click **Next**.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, edit, and delete the job.

### Create a recovery testing job

To create a recovery testing:

 1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where MySQL resides and select the instance. Click **Next**.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_recovery_testing_01.png)
	```

	(1) From the **Restore type** list, select **Recovery testing**.
	
	(2) In the **Restore to** field, set a time for the restore job. You can restore to the latest time, or restore to N minutes/hours/days/weeks/months before the start time of the restore job.
	
	(3) In the **Restore source** section, select a database or instance. If you want to rename the restore source, click the database. The Rename icon appears beside the name. Click the icon and specify a name in the pop-up window.
	
	(4) Click **Next**.

	```{note}
	For performing recovery testing jobs on Windows, only the database can be selected in **Restore source** section.
	```

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the original or a different host.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, edit, and delete the job.

### Restore options

ADPS provides the following restore options for MySQL:

- Common options:
```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.20}|
```
```{table} Restore common options
---
class: longtable
---
|Feature |Description|Limitations|
| --- | --- | --- |
|Restore database|You can set the database restore method. The default option is Overwrite the original database. Alternatively, you can input a new database name to restore the data to a new database.|Only available for logical recovery jobs.|
|Database configuration file|Click **Browse** or manually enter the MySQL configuration file path. If you leave it blank, the default configuration file will be used.|Only available for instant recovery jobs.|
|binlog destination|Click **Browse** or manually enter the file path to restore the logs to the specified directory on the agent host. The field is required.|Only available for log restore jobs.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.20}|
```
```{table} Restore advanced options
---
class: longtable
---
|Feature |Description|Restrictions|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.||
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for instant recovery jobs.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|Not available for instant recovery jobs.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
|Database service startup/stop script|When the database's start and stop scripts are not the common methods (such as using service or systemctl), you need to manually enter the start and stop scripts.|Only available for point-in-time restore, instant recovery and recovery testing jobs.|
|Skip grant tables while applying binlog|Enabling this option can prevent issues with binary logs encountering forbidden access due to insufficient privileges during a log restore job is running.|Only available for point-in-time restore and instant recovery jobs.|
|Accelerate applying binlog|Enabling this option speeds up the restore of binlogs.|Only available for point-in-time restore and instant recovery jobs.|
|Force applying binlog|Enabling this option allows the continued application of binlogs even in the presence of errors.|Only available for point-in-time restore and instant recovery jobs.|
|MySQL service startup timeout|The duration to wait for the target MySQL database to start when a restore job is running.|Only available for point-in-time restore and instant recovery jobs.|
|Cache directory|When restoring an incremental backup set, if a cache directory is configured, the incremental backup set is first downloaded to the cache directory before being restored. If no cache directory is set, the incremental backup set is restored directly to MySQL's data directory.|Only available for point-in-time restore and recovery testing jobs.|
|Keep the mount point even if job fails|It is used to manually start a MySQL instance on a mount point or to check why a job failed.|Only available for instant recovery jobs.|
|Additional parameters|The parameters passed to MySQL. Multiple parameters should be separated by space.|Only available for logical recovery jobs.|
```

## Copy data management

On the **CDM** page, you can manage the copies generated by instant recovery and synthetic backup jobs, including viewing, cloning, unmounting, and deleting copies.

### View a copy

To view copies, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.

2. From the toolbar, select the resource and the period when copies are created. The display area shows copies of this resource in this period. Copies are named by the creation time.

3. Click the copy name. The details of this copy appear on the right side of the page. Different icons represent different copy types.

	- Full copy: A data copy created by a synthetic backup.
	- Mounted copy: A data copy created by an instant recovery.

  ```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_cdm_01.png)
  ```

### Clone a copy

You can click the **Clone copy** icon to create an instant recovery job for the synthetic copy to create a new mounted copy.

To clone copies, do the following:

 1. From the menu, click **CDM**. The **CDM** page appears.
 2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
 3. On the display area, click the name of the MySQL instance. The **Clone copy** icon appears on the right of the instance.

  ```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_cdm_02.png)
  ```

 4. Click the **Clone copy** button. You will be redirected to the **Backup sets** step. See [Create an instant recovery job](#create-an-instant-recovery-job) to configure the job.

  ```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_instant_restore_01.png)
  ```

 5. After the instant recovery, a copy with a mounted state is added below the selected point in time on the **CDM** page.

### Unmount a copy

You can click the **Unmount** icon to unmount the mounted copies. This operation will make the mounted directory on the restore target inaccessible.

To unmount a copy, do the following:

 1. From the menu, click **CDM**. The **CDM** page appears.

 2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.

3. Expand the full backup copy and select a mounted copy. The **Unmount** icon appears on the right of the copy.

  ```{only} scutech
	![](../images/Backup_Restore/DBackup3/MySQL/mysql_cdm_03.png)
  ```

4. Click the **Unmount** icon. A confirmation window appears.

 5. Confirm the warning and enter the verification code. Click **OK**.

6. After the unmounting, you can see no such mounted copy record under the full copy.

## MySQL cluster backup and restore

ADPS supports the backup and restore of MySQL clusters. Before you back up and restore the MySQL cluster, bind nodes into a cluster on the console.

### Before you begin

See [Install and configure the agent](#install-and-configure-the-agent) and [Activate license and authorize users](#activate-licenses-and-authorize-users) to complete the environment deployment on each node.

### Bind nodes into a cluster

To bind nodes into a cluster on the console, do the following:

1. From the menu, click **Resource** > **Cluster**. The **Cluster** page appears.


2. From the toolbar, click the **+** icon. The **Cluster binding** window appears.

```{only} scutech
![](../images/Backup_Restore/DBackup3/MySQL/mysql_cluster_01.png)
```

3. In the **Cluster binding** window, do the following:

- To bind the source-replica replication cluster:
	
	(1) In the **Name** field, enter the name for the cluster.
	
	(2) From the **Primary node** list, select the primary node in the source-replica replication cluster. 
	
	(3) From the **Type** list, select **Master-slave replication**.
	
	(4) From the **Nodes** list, select the replica node in the cluster.
	
	(5) In the **Keepalived configuration file** field, select a configuration file. The keepalived primary node is chosen based on the configuration file. If the configuration file is not available, you may not select it.
	
	(6) From **Preferred backup node** list, select the MySQL cluster node that needs to be prioritized for backup. Not selected by default.

	```{note}
	ADPS will select the cluster backup node based on the priority order of **Preferred backup node** > **Keepalived configuration file** > No configuration.
	```

    (7) Click **Submit**. The binding is successful.

- The steps to bind nodes in a source-source replication cluster are similar to binding nodes in a source-replica replication cluster. However, note that when performing the binding, select the **Dual-master replication** from the **Type** list.

### Cluster backup and restore

When a switchover occurs, the new primary node will take over the backup job automatically. To create a MySQL cluster backup/restore job, see [Create a backup job](#create-a-backup-job). 

## Limitations

```{tabularcolumns} |\Y{0.2}|\Y{0.8}|
```
```{table} Limitations
---
class: longtable
---
|Feature|Limitations|
| ---- | ---------------------------------------- |
| Resource |- ADPS does not support MySQL cluster on Windows.{{ br }}- After cluster binding, all nodes must be online for job creation.{{ br }}- Once the cluster is bound, the primary node cannot be changed unless the entire cluster is deleted. |
| Backup |- ADPS only supports MySQL logical backup on Windows. {{ br }}- To execute physical backups and synthetic backups, MySQL version 5.138 or above (with the MySQL InnoDB Plugin installation package) on Linux is required. {{ br }}- MySQL 5.1 requires the installation of the InnoDB plugin for performing physical backups and synthetic backups.{{ br }}- ADPS does not support incremental backups for MySQL using the MyISAM engine. By default, incremental backups for MySQL with the MyISAM engine will be performed as the full backup.{{ br }}- For iSCSI-based data synthetic pools, the agent host requires the installation of iscsi-initiator-utils. For FC-based data synthetic pools, the agent host needs to have an HBA card installed with the corresponding HBA API Driver.{{ br }}- Reconnection time, resumption buffer size and speed limit are not available for synthetic backups.  |
| Restore |- When creating the point-in-time restore, instant recovery, and recovery testing jobs, if MySQL is started in a non-standard way, inputting the corresponding scripts or commands in the Database service startup/stop script restore options is required.{{ br }}- For point-in-time restore jobs, selecting Restore to Backup State (shortest recovery time) allows for the restore of full, incremental, and synthetic backup sets. For instant recovery jobs, selecting Restore to backup state (shortest recovery time) only allows for the recovery of synthetic backup sets. When selecting Restore to backup state (shortest recovery time), binlog restore will not be performed.{{ br }}- Reconnection time, resumption buffer size and speed limit are not available for instant recovery jobs.{{ br }}- When performing an instant recovery job, if the agent host has apparmor or selinux enabled, you need to add `/mnt/iscsi_disk/ r, /mnt/iscsi_disk/** rwk,` to the configuration file `/etc/apparmor.d/usr.sbin.mysqld`. Afterward, execute the command `/etc/init.d/apparmor reload`.{{ br }}- When configuring a restore job for a MySQL cluster, the Filter option is only applicable to the log restore job. Additionally, ensure that the databases specified for exclusion have corresponding records within the log backup set.{{ br }}- After performing a point-in-time restore job on a MySQL cluster, the group replication relationship of the cluster will not be automatically restored. Therefore, you need to manually execute the command `start group_replication` to initiate group replication. |
```

## Glossary

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Glossary
---
class: longtable
---
|Term|Description|
| ---- | ---------------- |
|fast compression|A compression method that uses fast compression algorithms to compress data during the backup job.|
```