# User Guide for DB2

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore DB2.

The backup and restore features supported by ADPS include:

- Backup sources

	Database (single or multiple)

- Backup types

	Full backup, incremental backup, cumulative incremental backup, log backup, and log backup (on demand)

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, data encryption, sessions, parallelism, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore, log restore, and recovery testing

- Restore targets

	Source host and different host

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore DB2, first install the ADPS agent on the host where DB2 resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where DB2 resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of DB2 on multiple systems, including:

- Windows 2003/2008/2008 R2/2012
- Linux 6/7/8
- AIX 6.1/7.1

Supported DB2 versions include:

- DB2 8.1/9.1/9.5/9.7/10.1/10.5/11.1

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
7. At the **Select components** step, select **DB2** from the component list. Click **Next**.
8. At the **Configure Aurreum Data Protection Suite agent** step, enter the following:

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

	(2) From the **Component** list, select **DB2**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the feature, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

  ```{code-block} python
    [root@localhost ~]# curl -o- "http://192.168.18.57:50305/d2/update/script?modules=db2&location=http%3A%2F%2F192.168.18.57%3A50305&access_key=b448e47a5e5ae07c5a4a77bf97c383f5&rm=&tool=curl" | sh
  ```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore DB2, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where DB2 resides. Click the **Register** icon. After the registration, the **Activate** window appears. 

3. In the **Activate** window, select the DB2 backup license, and click **Submit**. After the activation, the **Authorize** window appears.

4. In the **Authorize** window, do the following:

	(1) In the **Name** field, enter a name for the host.
	
	(2) From the **Data network** list, select a network from those added on the **Network** page as the data network.
	
	(3) In the **Preferred egress network** field, enter an IP address for the preferred network traffic egress of the host's backup data. IPv4 and IPv6 are supported.
	
	(4) From the **User group** list, select user groups to authorize access to the resource.
	
	(5) Set the resource as **Protected** or not. The **Protected** resource cannot be the restore target or the data replication target unless the administrator removes the **Protected** label.

	```{note}
	1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
	2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Backup

### Backup types

ADPS provides five common backup types for DB2:

- Full backup

	Backs up all data in one or more databases, including corresponding archive logs for data restore.

- Incremental backup

	Backs up only the data that has changed since the last backup with the last backup as the baseline. 
  
- Cumulative incremental backup

	Backs up only the data that have changed since the last full backup with a full backup as the baseline.

- Log backup

	Backs up the newly generated archive files of the current database. It requires enabling either the LOGRETAIN or DISK archiving method in the database.

- Log backup (on demand)

	When a log backup (on demand) job is created, it will remain in an idle state. If new archive files are generated locally in the database, the job will be triggered automatically. Before a log backup (on demand) job is created, you need to perform a full backup first, and enable the DISK archiving method in the database.

```{note}
- You can only initiate either a log backup or a log backup (on demand) job at a time. It is not possible to initiate both simultaneously.
```

### Backup policies

ADPS provides six backup schedule types: immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

1. Perform a **full backup** based on requirements. For example, perform a full backup job once every Sunday.
2. Perform a **full backup** on Sunday and **incremental backup** daily from Monday to Saturday. If data corruption occurs on Saturday, the full backup from Sunday along with all incremental backups from Monday through Friday are used for restore. The restore job restores the full backup from Sunday first, followed by incremental backups from Monday to Friday, which involves a total of six restores. This backup strategy requires less time for data backup but more time for data restore. 
3. Perform a **full backup** on Sunday and **cumulative incremental backup** from Monday to Saturday. If data corruption occurs on Saturday, the full backup from Sunday along with all cumulative incremental backups from Monday through Friday are used for restore. The restore job restores the full backup from Sunday first, followed by cumulative incremental backups from Monday to Friday, which involves a total of two restores. This backup strategy requires less time for data backup but more time for data restore. 
3. Perform a **log backup** hourly to ensure that you have a second-level point in time available.

### Before you begin

Before you back up and restore DB2, check the following:

1. Check the DB2 instance status

	(1) The DB2 database service should be in the “Active – Up” status for backup and restore.

  ```{only} scutech
  ![](../images/Backup_Restore/DBackup3/DB2/db2_service.png)
  ```

2. Check storage pools

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.


### Log in to the resource

Before you create a backup or restore job, log in to the DB2 resource on the console. ADPS provides two authentication methods for DB2:

- Database authentication

	You can use the DB2 installation user account of the operating system to log in to the resource.

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

2. From the host list, find the host where DB2 resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.

3. Click **Login** beside the resource. The **Login** window appears.

4. In the **Login** window, select an authentication method according to your needs.

	- Select **Database authentication**, enter the **Username** and **Password**, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.
	
5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.

2. At the **Hosts and resources** step, select the host where DB2 resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the databases that you want to back up.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/DB2/db2_backup_01.png)
	```
	
	```{note}
	To back up multiple databases at a time, select databases with the same code page settings. 
	```
	
4. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
   Incremental backups, cumulative incremental backups and log backups must have the same backup target as their baseline backups.
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
![](../images/Backup_Restore/DBackup3/DB2/db2_backup_02.png)
```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for DB2:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.||
|When the database is in NOARCHIVELOG mode|It only appears when the database is configured archive logging, and the following operations are provided for the backup job: {{ br }}- Send an alert message and cancel the job {{ br }}- Allow offline backup {{ br }}- Enable ARCHIVELOG  |Only available for full, incremental and cumulative incremental backup jobs.|
|Log archive method|Enables log archive method for the databases to be backed up.|Only available for log backup (on demand) jobs.|
|Delete archive logs|ADPS provides the following methods to remove archive logs:{{ br }}- Delete all backed-up archive logs. {{ br }}- Delete archive logs in the last X days/hours. {{ br }}- Do not delete archive logs.||
|Incremental Backup|It modifies the database "trackmod" parameter to "Yes", which allows the database to perform incremental backups. The first incremental backup will be an offline backup.|Only available for full backup jobs.|
|Sessions|Specifies the number of parallel data streams used to write data. It is recommended that the number of sessions is not greater than the number of logical CPU cores on the host.||
|Parallelism| Defines the number of tablespaces to be read in parallel, and assigns each process or thread to a tablespace. It is recommended that the number of parallelism is not greater than the number of tablespaces in the database.||
```

- Advanced options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup advanced options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.||
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.||
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
```

## Restore

### Restore types

For different needs, ADPS provides several restore types for DB2, including:

- Point-in-time restore

	When a logical error or disaster occurs in a database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. Restoring to the source or a different host is supported.

- Log restore

	Restores archived log files within a specified range to either the original or a remote database's archive log directory. This restore type is applicable to the databases with archive logging configured.

- Recovery testing

	Restores the latest backup sets to another instance on the source host or to another host hourly, daily, weekly, and monthly.

### Before you begin

To restore DB2 to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where DB2 resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/DB2/db2_timepoint_restore_01.png)
	```

	(1) From the **Restore type** list, select **Point-in-time restore**.

	(2) From the **Database** list, select a database for the restore job.

	(3) In the **Restore to point in time** section, specify a point in time for the restore job.

	To specify a point in time for the restore, you can click the time beside the slider control and enter a point in time, or you can drag the slider to specify a point in time.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	The restore target can be the source host or a different host.
	- **Source host**: The source host is selected by default.
	- **Different host**: Restoring to a different host requires the same OS versions and the same DB2 versions.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create an log restore job

To create a log restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where DB2 resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/DB2/db2_log_restore_01.png)
	```

	(1) From the **Restore type** list, select **Log restore**.

	(2) From the **Database** list, select a database for the restore job.

	(3) In the **Restore branch** list, select a branch for the restore job.

	(4) In the **Log range** section, specify a log range for the restore job.

	- Select **Time range**. Drag the slider control to specify a time range.
	- Select **Log sequence range**. Select the range to start and end.
	
	(5) Click **Next**.
	
4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target can be the source host or a different host.

   - **Source host**: The source host is selected by default.
   - **Different host**: Restoring to a different host requires the same OS versions and the same DB2 versions.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a recovery testing job

To create a recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where DB2 resides and select the instance. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Recovery testing**.
	
	(2) In the **Restore source** section, select databases for the restore job. 
	
	(3) Click **Next**.

```{only} scutech
![](../images/Backup_Restore/DBackup3/DB2/db2_recovery_testing_01.png)
```

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the source or a different host.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for DB2:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Restore database|You can overwrite the source database or create a new database. To overwrite the source database, leave the field blank; to restore to a new database, fill out the field.|Only available for point-in-time restore jobs.|
|Database location|Sets the location where the database manager stores database control files.|Only available for point-in-time restore jobs.|
|Non-automatic storage containers location|Sets the location for the non-automatic storage containers. The option is displayed only when non-automatic storage tablespace containers exist in the database to be restored. It is required to modify all non-automatic storage container paths.|Only available for point-in-time restore jobs.|
|Automatic storage containers location| Sets the database storage location where the database manager stores the automatic storage tablespace containers. When the container is a raw device, and no same raw device is available on the restore host, ADPS will automatically redirect and restore to DMS. You can create multiple storage containers locations by clicking the “+” icon.|Only available for point-in-time restore jobs.|
|Sessions|Specifies the number of parallel data streams used to write data. It is recommended that the number of sessions is not greater than that of the host logical CPU.|Only available for pint-in-time restore and recovery testing jobs.|
|Without rolling forward|When you enable the option, the database will keep “Pending” after the point-in-time restore job is executed successfully.|Only available for recovery testing jobs.|
|Archive log destination|Sets the archive directory path for restoring archive log files. This directory is a required field.|Only available for log restore jobs.|
|Restart instance after recovery|Selects to restart the instance or not after the restore job is completed.|Only available for recovery testing jobs.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.||
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.||
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
```


## Limitations

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Limitations
---
class: longtable
---
|Feature|Limitations|
| --- | --- |
|Backup|To log in to the DB2 database via the OS authentication method, use the DB2 instance user account on Linux; while on Windows use the Windows administrator account. If you want to log in to the database using the domain user on Windows, fill in the domain name to successfully log in to the database resource and back up the databases.|
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
```