# User Guide for Microsoft SQL Server

## Overview

This guide introduces how to install and configure the ADPS agent and how to properly use ADPS to back up and restore SQL Server.

The backup and restore features supported by ADPS include:

- Backup sources

	Database (single or multiple) and transaction logs

- Backup types

	Full backup, cumulative incremental backup, log backup, and synthetic backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, data synthetic pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, and replication

- Restore types

	Media restore, point-in-time restore, instant recovery, disaster recovery, and recovery testing

- Restore targets

	Original host and different host

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore SQL Server, first install the ADPS agent on the host where SQL Server resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where SQL Server resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of SQL Server standalone, failover cluster, Always On availability groups (AGs), and Always On failover cluster instances (FCIs). Supported versions include:

- SQL Server 2000/2005/2008/2008 R2/2012/2014/2016/2017/2019/2022

### Install the agent

The ADPS agent can be installed on Windows and Linux. You can select the installation method according to your environment.

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
6. Log in to the Windows host as a user with administrative privileges. Double-click the package and open the installation wizard. Click **Next**.
7. At the **Select components** step, select **Microsoft SQL Server** from the component list. Click **Next**.
8. At the **Configure Aurreum Data Protection Suite agent** step, enter the following:

	```{only} scutech
	![](../images/Common/mssql_agent.png)
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

For Linux OS, ADPS agent supports online and offline installation. We recommend online installation.

1. Online installation:
	ADPS provides `curl` and `wget` commands for installation.
2. Offline installation:
	See [Offline installation](../agent_install/agent_install.md#offline-installation) in Aurreum Data Protection Suite Agent Installation Guide.

To install the agent online, do the following:

1. Log in to the ADPS console.
2. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
3. From the toolbar, click the **Install agent** icon. The **Install agent** window appears.
4. In the **Install agent** window, do the following:

	(1) From the **Select system** list, select **Linux**.

	(2) From the **Component** list, select **MS SQL Server**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

	```{code-block} python
	[root@localhost ~]# curl -o- "http://192.168.20.85:50305/d2/update/script?modules=mssql&location=http%3A%2F%2F192.168.20.85%3A50305&access_key=2042288d749dba47e963d1ab09a6472b&rm=&tool=curl" | sh
	  % Total    % Received % Xferd  Average Speed   Time    Time     	Time  Current
	                                Dload  Upload   Total   Spent    Left  Speed
	100  7905    0  7905    0     0  23844      0 --:--:-- --:--:-- --:--:-- 44661
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                Dload  Upload   Total   Spent    Left  Speed
	  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 58.7M  100 58.7M    0     0  1804k      0  0:00:33  0:00:33 --:--:-- 4859k
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                Dload  Upload   Total   Spent    Left  Speed
	  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 6728k  100 6728k    0     0  1900k      0  0:00:03  0:00:03 --:--:-- 1874k
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                Dload  Upload   Total   Spent    Left  Speed
	  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 3879k  100 3879k    0     0  5703k      0 --:--:-- --:--:-- --:--:-- 6247k
	```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore SQL Server, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where SQL Server resides. Click the **Register** icon. After the registration, the **Activate** window appears.

	```{only} scutech
	![](../images/Common/mssql_license.png)
	```

3. In the **Activate** window, select the SQL Server backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup types

ADPS provides three common backup types (full backup, cumulative incremental backup, and log backup) and one advanced (synthetic backup) for SQL Server.

- Full backup

	Backs up all the data of one or more databases and enough logs to recover these data.

- Cumulative incremental backup

	Backs up only the data that has changed since the last full backup with a full backup as the baseline.

- Log backup

	If your databases use the full or bulk-logged recovery model, you can back up transaction logs periodically based on full backups or cumulative incremental backups. Transaction log backups save more time and space than full backups and truncate the log after the job is completed to avoid the log fills.

- Synthetic backup

	The first synthetic backup is a full backup, and the subsequent ones are incremental. When the synthesis condition is reached, the latest full backup and subsequent incremental backup will be synthesized to create a new full backup copy. Synthetic backups can improve the restore performance. You can directly mount the full backup copy to the target host through an instant recovery job without physical copies and additional storage space.

	Synthetic backups have the following requirements:

	(1) You have the advanced licenses: MS SQL Server Synthetic Backup and MS SQL Server Copy Data Management.

	(2) You have configured the iSCSI or FC link between the backup server and the host with the agent installed.

	(3) The data disks of the storage server use the ZFS file system.

	(4) You have created a data synthetic pool for the current user.

### Backup policies

ADPS provides six backup schedule types: immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

1. Perform a **full backup** once a week when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.
2. Perform a **cumulative incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.
3. Perform a **log backup** hourly if your database supports log backups to achieve the precise point-in-time restore and ensure that the restore granularity can reach the second level.

To use the advanced synthetic backup, we recommend the following backup policy:

1. Perform a **synthetic backup** every day to ensure that you have a recoverable point in time every day.
2. Perform a **log backup** hourly based on the synthetic backup to achieve the precise point-in-time instant recovery.

### Before you begin

Before you back up and restore SQL Server, check the following:

1. Check the SQL Server instance status.

	- Windows

	Log in to the host and open the service list. Check whether the SQL Server service status is *Started* or not.

	```{only} scutech
	![](../images/Common/mssql_database.png)
	```

	- Linux

	Open the terminal on the host and use the `systemctl status` command to check the SQL Server service status.

	```{code-block} shell
	[root@localhost ~]# systemctl status mssql-server
	● mssql-server.service - Microsoft SQL Server Database Engine
	  Loaded: loaded (/usr/lib/systemd/system/mssql-server.service; enabled; vendor preset: disabled)
	  Active: active (running) since Two 2022-05-31 04:55:30 EDT; 5s ago
	    Docs: https://docs.microsoft.com/en-us/sql/linux
	Main PID: 15804 (sqlservr)
	  CGroup: /system.slice/mssql-server.service
	          └─15804 /opt/mssql/bin/sqlservr

	2022-05-31 04:55:30 localhost.localdomain systemd[1]: Started Microsoft SQL Server Database Engine.
	2022-05-31 04:55:30 localhost.localdomain systemd[1]: Starting Microsoft SQL Server Database Engine...
	```

2. Check storage pools

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

	```{note}
	To use synthetic backups, ensure that you have created a data synthetic pool for the current user.
	```

### Log in to the instance

Before you create a backup or restore job, log in to the SQL Server instance on the console. ADPS provides three authentication methods for SQL Server:

- Database authentication

	The SQL Server user should have the following roles: server roles of `sysadmin` and `dbcreator`, database roles of `db_backupoperator` and `db_owner`. If the SQL Server user changes its password, you must re-log in to the instance. Otherwise, the job will fail.

- OS authentication

	The Windows user can be the Administrator or the one who installed the SQL Server. OS Authentication does not require the password of the SQL Server user. The password changes of SQL Server users will not affect the backup and restore jobs.

- Access Key

	You can use the access key of the ADPS user who is authorized with access to the resource. This method is suitable for the following scenarios:

	- You cannot get the OS user's username and password.
	- The user's password changes frequently.

	```{note}
	1. Access key authentication is not enabled by default. To enable this feature, log in to the ADPS console, go to **Settings**, open the **Security** tab, and select the **Access key login instance** checkbox.
	2. To get the access key, log in to the console, click **Personal settings** > **Account settings** on the upper right corner, find **Access key** on the **Preferences** tab, and click **View**.
	```

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the host list, find the host where SQL Server resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Common/mssql_login.png)
	```

4. In the **Login** window, select an authentication method according to your needs.

	- Select **Database authentication**, enter the **Username** and **Password**, and click **Login**.
	- Select **OS authentication**, enter the **Username** and **Password** of the OS user, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where SQL Server resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the databases that you want to back up.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3//MSSQL/mssql_backup_01.png)
	```

4. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
	Cumulative incremental backups and log backups must have the same backup target as their baseline backups.
	```

5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_backup_02.png)
	```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for SQL Server:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup common options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.|Not available for synthetic backup jobs.|
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 64.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.{{ br }}The value for synthetic backup jobs ranges from 1 to 255.||
|Verify backup files|Uses RESTORE VERIFYONLY to verify whether the backup set is intact and available after the backup without restoring the backup set.|Not available for synthetic backup jobs.|
|Backup new databases|Not enabled by default. When enabled, it will check whether the SQL Server instance has new databases before the backup job starts. If so, the new databases will be added to the backup job.||
|Log backup threshold|Not enabled by default. You can use it to set the log space usage threshold. The job will first check the log space usage rate and then decide whether to start the backup or not. The log backup will be performed on the databases whose log space usage reaches the threshold.|Only available for periodical log backup jobs.|
|Full backup threshold|The synthetic backup job first checks the data page changes. When the data page changes of the database reach the full backup threshold, the job will be performed as a full backup but not an incremental one. The value is 50 by default and ranges from 5 to 100.|Only available for synthetic backup jobs.|
```

- Advanced options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup advanced options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|Not available for synthetic backup jobs.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for synthetic backup jobs.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
|Shadow copy provider|Specifies a shadow copy provider in the host to perform the synthetic backup job.|Only available for synthetic backup jobs.|
|Backup tail-log|When the database is damaged or data files are deleted, you can try to create a log backup first before the restore and enable this option. It can capture any log records (tail log) that have not been backed up. {{ br }}After the log backup, you can create a restore job and restore the database to a point in time before the failure.|Only available for log backup jobs.{{ br }}To perform a successful tail-log backup, check the following: {{ br }}1. The SQL Server instance runs normally under the full or bulk-logged recovery model.{{ br }}2. The database state is Online, Recovery, Recovery Pending, and Suspect. The database does not contain any bulk-logged changes. {{ br }}3. Not available for databases with the following states: Offline, Restoring, and Emergency. {{ br }}4. The **Backup new databases** option is not compatible with the **Backup tail-log** option. You cannot enable them at the same time.|
```

## Restore

### Restore types

For different needs, ADPS provides several restore types for SQL Server, including:

- Media restore

	When a media error occurs in a SQL Server database, such as data file corruption or accidental deletion, you can perform a media restore job to restore one or more data files to the latest state of the newest backup set.

- Point-in-time restore

	When a disaster occurs in a SQL Server database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. You can restore to the original or a different host.

- Instant recovery

	When a disaster occurs in a SQL Server database, you can perform an instant recovery job to create a database copy using the synthetic backup set and mount the copy directly to a host with the agent installed, with which the database can become online quickly. The data can be recovered quickly to reduce the recovery time.

	```{note}
	Instant recovery jobs require that you have performed a synthetic backup.
	```

- Disaster recovery

	When a disaster occurs in a SQL Server database, you can create a disaster recovery job to restore one or more databases to the latest state of the newest backup set. You can restore to the original or a different host.

- Recovery testing

	Restores the latest backup sets to another instance on the original host or a different host hourly, daily, weekly, and monthly.

### Before you begin

To restore SQL Server to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a media restore job

To create a media restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where SQL Server resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_restore_01.png)
	```

	(1) From the **Restore type** list, select **Media restore**.

	(2) In the **Restore source** section, select what you want to restore.

	(3) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

	(4) Click **Next**.

4. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

5. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
6. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
7. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

```{note}
1. Media restore jobs can only restore to the original host.
2. Under the simple recovery model, files must belong to a read-only filegroup for media restore jobs.
```

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where SQL Server resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_restore_02.png)
	```

	(1) From the **Restore type** list, select **Point-in-time restore**.

	(2) From the **Database** list, select a database for the restore job.

	(3) In the **Restore as** field, the default setting is to restore the database as the original. You can click the **Rename** icon beside the database name to rename the database, data file, and path.

	(4) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**. The default setting is to restore the database to the latest point in time of the newest backup set. You can click the time beside the option. In the pop-up window, drag the slider to specify or enter a point in time for the restore and click the tick button. The restore branch and backup set information of the point in time appear. This option is only available for databases with successful log backups.

	- Select **Restore to LSN**. The default setting is to restore the database to the latest SCN of the newest backup set. You can click the LSN beside the option. In the pop-up window, drag the slider control to specify or enter an LSN for the restore job and click the tick button. The restore branch and backup set information of the LSN appear. This option is only available for databases with successful log backups.

	- Select **Restore to backup state**. The default setting is to restore the database to the latest state of the newest backup set. You can select a backup set in the **Backup sets** field to restore to its latest backup state.

	(5) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create an instant recovery job

```{note}
1. If the storage pool storing synthetic backup sets is of the iSCSI type, you must install the iSCSI Initiator on the host where you perform the restore job.
2. If the storage pool storing synthetic backup sets is of the FC type, you must install the HBA API Driver corresponding to the HBA card on the agent where you perform the restore job.
```

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where SQL Server resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_restore_03.png)
	```

	(1) From the **Restore type** list, select **Instant recovery**.

	(2) From the **Database** list, select a database for the restore job.

	(3) In the **Restore as** field, the default setting is to restore the database as the original. You can click the **Rename** icon beside the database name to rename the database, data file, and path.

	(4) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**. The default setting is to restore the database to the latest point in time of the newest backup set. You can click the time beside the option. In the pop-up window, drag the slider to specify or enter a point in time for the restore and click the tick button. The restore branch and backup set information of the point in time appear. This option is only available for databases with successful log backups.

	- Select **Restore to LSN**. The default setting is to restore the database to the latest SCN of the newest backup set. You can click the LSN beside the option. In the pop-up window, drag the slider control to specify or enter an LSN for the restore job and click the tick button. The restore branch and backup set information of the LSN appear. This option is only available for databases with successful log backups.

	- Select **Restore to backup state**. The default setting is to restore the database to the latest state of the newest backup set. You can select a backup set in the **Backup sets** field to restore to its latest backup state.

	(5) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the instant recovery, go to the **CDM** page. A mounted copy is added below the full copy. See [View a copy](#view-a-copy).


### Create a disaster recovery job

To create a disaster recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where SQL Server resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_restore_04.png)
	```

	(1) From the **Restore type** list, select **Disaster recovery**.

	(2) In the **Restore source** section, select databases for the restore job. If you want to rename the restore source, click the database, the data file, or the path. The **Rename** icon appears beside the name. Click the icon and specify a name in the pop-up window.

	(3) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a recovery testing job

To create a recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where SQL Server resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_restore_05.png)
	```

	(1) From the **Restore type** list, select **Recovery testing**.

	(2) In the **Restore source** section, select databases for the restore job. If you want to rename the restore source, click the database, the data file, or the path. The **Rename** icon appears beside the name. Click the icon and specify a name in the pop-up window.

	(3) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the original or a different host.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for SQL Server:

- Advanced options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore advanced options
---
class: longtable
---
|Feature |Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.||
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.||
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
|Restore state|The default setting is to restore to the RECOVERY state.{{ br }}- RECOVERY: Uncommitted transactions are rolled back, and the database is available for use.{{ br }}- NORECOVERY: Uncommitted transactions are not rolled back, and the database is not usable. Transaction logs can be applied.{{ br }}- STANDBY: Uncommitted transactions are rolled back to a standby file, and the database is in read-only mode.|Only available for point-in-time restore jobs.{{ br }}Not available when you select synthetic backup sets for the restore job.|
|Storage protocol|You can use it to select iSCSI or FC and set the target name to restore the synthetic backup set from a database synthetic pool. During the instant recovery, the selected storage protocol will be used to transfer data between the storage server and the restore target.|Only available when you select synthetic backup sets for the restore job.|
|Keep CDC|For preserving CDC (Change Data Capture) settings when the database is recovered.|Only available for point-in-time restore and disaster recovery jobs.{{ br }}Not available when you select synthetic backup sets for the restore job.|
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

### Clone a copy

You can click the **Clone copy** icon to create an instant recovery job for the synthetic copy to create a new mounted copy.

To clone copies, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. On the display area, click the resource. The **Clone copy** icon appears on the right of the resource.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_cdm_01.png)
	```

4. Click the **Clone copy** button. You will be redirected to the **Backup sets** step. See [Create an instant recovery job](#create-an-instant-recovery-job) to configure the job.
5. After the instant recovery, a copy with a mounted state is added below the selected point in time on the **CDM** page.

### Unmount a copy

You can click the **Unmount** icon to unmount the mounted copies. This operation will make the mounted directory on the restore target inaccessible.

To unmount a copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select a mounted copy. The **Unmount** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_cdm_02.png)
	```

4. Click the **Unmount** icon. A confirmation window appears.
5. Confirm the warning and enter the verification code. Click **OK**.
6. After the unmounting, you can see no such mounted copy record under the full copy.

## Backup and restore

ADPS supports the backup and restore of SQL Server failover clusters, Always On availability groups, and Always On failover clusters. Before you back up and restore the SQL Server cluster, bind nodes into a cluster on the console.

### Before you begin

See [Install and configure the agent](#install-and-configure-the-agent) and [Activate license and authorize users](#activate-licenses-and-authorize-users) to complete the environment deployment on each node.

### Bind nodes into a cluster

To bind nodes into a cluster on the console, do the following:

1. From the menu, click **Resource** > **Cluster**. The **Cluster** page appears.

2. From the toolbar, click the **+** icon. The **Cluster binding** window appears.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/MSSQL/mssql_cluster_01.png)
	```

3. In the **Cluster binding** window, do the following:

	- To bind the nodes of a SQL Server failover cluster:

	(1) In the **Name** field, enter a name for the cluster.

	(2) From the **Primary node** list, select the active node in the cluster.

	(3) From the **Type** list, select **Active-Passive**.

	(4) From the **Nodes** list, select the passive node in the cluster. You can click **x** to remove the selected node.

	(5) In the **Database cluster address** field, enter the IP address of the cluster.

	(6) Click **Submit**.

	- On the ADPS console, the availability group is shown as a virtual instance. After the binding, you need to activate the license for the virtual instance. To bind the nodes of an Always On availability group:

	(1) In the **Name** field, enter a name in the `instance name-ag-always on availability group name` format for the cluster.

	(2) From the **Primary node** list, select the virtual instance of the current primary replica.

	(3) From the **Type** list, select **Always On availability groups**.

	(4) From the **Nodes** list, select virtual instances of secondary replicas.

	(5) Click **Submit**.

	(6) See [Activate licenses and authorize users](#activate-licenses-and-authorize-users) to activate the virtual instances.

	- To bind the nodes of an Always On failover cluster:

	(1) In the **Name** field, enter a name for the cluster.

	(2) From the **Primary node** list, select the active node in the cluster.

	(3) From the **Type** list, select **Active-Passive**.

	(4) From the **Nodes** list, select the passive node in the cluster. You can click **x** to remove the selected node.

	(5) In the **Database cluster address** field, enter the IP address of the cluster.

	(6) Click **Submit**. On the **Resource** page, log in to the physical instance. After login, you can see a virtual instance below the physical instance.

	(7) Bind the virtual instances using the **Always On availability groups** type.

	(8) See [Activate licenses and authorize users](#activate-licenses-and-authorize-users) to activate the virtual instances.

### Backup and restore

When a switchover occurs, the new primary node will take over the backup job automatically. To create a SQL Server cluster backup/restore job, see [Create a backup job](#create-a-backup-job) and [Restore](#restore). Note that:

1. When you create a backup job for a failover cluster, select the instance on the active node at the **Hosts and resources** step.
2. When you create a backup job for an Always On availability group or Always On failover cluster, select the virtual instance at the **Hosts and resources** step.

## Limitations

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Limitations
---
class: longtable
---
|Feature|Limitations|
| --- | --- |
|Backup|Before you use ADPS to back up SQL Server, close the backup jobs of SQL Server itself and other third-party backup providers.{{ br }}Cumulative incremental backups do not support the system databases.|
|Synthetic backup|Reqires Microsoft iSCSI Software Initiator installed on the Windows 2003 host.{{ br }}Not available for an environment where the system is 64-bit and the SQL Server database is 32-bit.{{ br }}Not available for Always On availability groups and SQL Server on Linux.{{ br }}Not available for the SQL Server system databases.|
|Media restore|SQL Server 2000 only supports offline restore. SQL Server 2005 and later Enterprise Editions support online restore.{{ br }}Cannot be used to restore the deleted databases.{{ br }}Cannot restore from synthetic backup sets.{{ br }}Cannot restore to a different host.{{ br }}Not available for databases in Restoring and Emergency states.{{ br }}After you restore the primary filegroup, run a full backup before running a media restore again.{{ br }}Log backups are required when the databases with the full and bulk-logged recovery models have read-only filegroups. Otherwise, media restore jobs will fail when files are lost.{{ br }}Cannot restore from backup sets in target pools of pool replications|
|Point-in-time restore|Cannot restore Always On availability groups to a specified state.|
|Instant recovery|Cannot restore multiple databases at present.{{ br }}Supports online or offline databases.{{ br }}Not available for Always On availability groups and SQL Server on Linux.|
|Always On availability groups|The databases in availability groups must be with the full recovery model. Therefore, when a virtual instance is selected as the restore target, the database with the simple or bulk-logged recovery model cannot be restored to the target.{{ br }}Synthetic backup and instant recovery jobs are not supported.|
|Authentication method|Not available for SQL Server on Linux and Always On availability groups on Linux.{{ br }}Only Windows SQL Server single instance supports the access key authentication.|
```

## Glossary

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Glossary
---
class: longtable
---
|Term|Description|
| --- | --- |
|fast compression|A compression method that uses fast compression algorithms to compress data during the backup job.|
|log truncation|Removes inactive virtual log files from the logical transaction logs in a SQL Server database to free up space in the logical logs for reuse by the physical transaction logs. For details, see [Traction log trunction](https://learn.microsoft.com/en-us/sql/relational-databases/logs/the-transaction-log-sql-server?view=sql-server-ver16#Truncation).|
|failover cluster|Only one host is allowed to mount the shared disk while the other is closed. When the active node fails, the inactive node will become the active node and mount the shared disk. For details, see [Windows Server Failover Clustering with SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-ver16). |
|Always On availability group|Under synchronous-commit mode, the data of the primary replica is updated to secondary replicas synchronously. Real-time synchronization can be maintained between the primary replica and secondary replicas. When the system detects the failure of the primary replica, a secondary replica will immediately become the new primary. For details, see [What is an Always On availability group?](https://learn.microsoft.com/en-us/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-ver16).|
|snapshot|A read-only static view of a SQL Server database (source database). For details, see [Database snapshots (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/database-snapshots-sql-server?view=sql-server-ver16).|
```