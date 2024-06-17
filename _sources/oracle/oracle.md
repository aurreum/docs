# User Guide for Oracle

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore Oracle.

The backup and restore features supported by ADPS include:

- Backup sources

	Databases, tablespaces, PDBs, tables, data files, archive logs

- Backup types

	Full backup, incremental backup, cumulative incremental backup, traditional export, Data Pump export, log backup, continuous log backup, and synthetic backup

- Backup targets

	Standard storage pool, deduplication storage pool, data synthetic pool, local storage pool, real-time backup pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, and replication

- Restore types

	Media restore, point-in-time restore (databases, tablespaces, tables), traditional import, Data Pump import, instant recovery, Live Recovery, log restore, Data Pump recovery testing, traditional recovery testing, and instant recovery testing

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

To back up and restore Oracle, first install the ADPS agent on the host where Oracle resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where Oracle resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of Oracle in multiple versions, including:

- Oracle standalone 8i/9i/10g/11g/12c/18c/19c/21c
- Oracle RAC 9i/10g/11g/12c/18c/19c/21c
- Oracle two-node 11g
- Oracle DG 11g/12c
- Oracle ADG 11g/21c
- Oracle XE 11g

### Install the agent

The ADPS agent can be installed on Windows, Linux, and other operating systems. You can select the installation method according to your environment.

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
7. At the **Select components** step, select **Oracle** from the component list. Click **Next**.
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

	(2) From the **Component** list, select **Oracle**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

	```{code-block} shell
	curl "http://IP:50305/d2/update/script?modules=oracle&location=http%3A%2F%2FIP%3A50305&access_key=572bd4dbb395fd320a30fe9729a21db8&rm=&tool=curl" | sh
	```

7. Wait for the installation to complete.

#### Other operating systems

Other operating systems include Solaris, HP-UX, and so on. For more information about installing agents on other operating systems, see [Install the agent](../agent_install/agent_install.md#install-the-agent) in Aurreum Data Protection Suite Agent Installation Guide.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Oracle, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where Oracle resides. Click the **Register** icon. After the registration, the **Activate** window appears. Click **Submit**.

	```{only} scutech
	![](../images/Common/register_resources.png)
	```

3. After you activate the license, the **Configure** window appears. In the **Configure** window, do the following:

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

ADPS provides five common backup types (full backup, incremental backup, cumulative incremental backup, and so on) and two advanced (synthetic backup and continuous log backup) for Oracle.

- Full backup

	Backs up the selected data. The backup set includes the selected data, control files, archive logs, password files, server parameter files (SPFILE), and configuration files (`tnsnames.ora` and `listener.ora`).

- Incremental backup

	Backs up only the data that has changed since the last backup with a full backup as the baseline.

- Cumulative incremental backup

	Backs up only the data that has changed since the last full backup with a full backup as the baseline.

- Logical export

	Includes Data Pump export and traditional export. You can export databases, tablespaces, and schema. Exporting databases and tablespaces requires a user with `DBA` privileges (such as `SYSTEM`, but the user cannot be `SYS`). Exporting schema requires the corresponding user or `SYSTEM`.

- Log backup

	Backs up Oracle archive logs.

- Synthetic backup

	The first synthetic backup is a full backup and the subsequent ones are incremental. When the synthesis condition is reached, the latest full backup and subsequent incremental backup will be synthesized to create a new full backup copy. Synthetic backups can improve the restore performance. You can directly mount the full backup copy to the target host through an instant recovery job without physical copies and additional storage space.

	Synthetic backups have the following requirements:

	(1) You have the advanced licenses: Oracle Synthetic Backup and Oracle Copy Data Management.

	(2) You have configured the iSCSI or FC link between the backup server and the host with the agent installed.

	(3) The data disks of the storage server use the ZFS file system.

	(4) You have created a data synthetic pool for the current user.

- Continuous log backup

	Continuously backs up Oracle online redo logs and archive logs to a real-time storage pool. After a continuous log backup job is created, the job is always running (except for job suspension caused by external factors).

	The continuous log backup has the following requirements:

	(1) You have the advanced licenses: Oracle Continuous Log Backup.

	(2) You have created a real-time backup pool for the current user.

	```{note}
	- Before you create a continuous log backup job, perform a full backup first.
	- The restore target of the continuous log backup job can only be a real-time backup pool.
	- You can only create one continuous log backup job for one database instance. To create a new continuous log backup job, stop or delete the old one.
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

1. Perform a **full backup** once a week when the application traffic is relatively small (Example: on the weekend).
2. Perform an **incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.).
3. Perform a **cumulative incremental backup** among incremental backups (Example: on Wednesday).
4. Perform a **log backup** hourly if the database supports log backups.
5. Perform other backups according to your needs.

```{note}
You can adjust backup policies according to your business requirements, database size, and available resources. Make sure the policy can meet your needs of Recovery Point Objective (RPO) and Recovery Time Objective (RPO) so that you can quickly and effectively restore your data when a disaster occurs.
```

### Before you begin

Before you back up and restore Oracle, check the following:

1. Check the Oracle status. The instance status must be OPEN or MOUNTED for backups.

	```
	SQL> select status from v$instance;

	STATUS
	------------------------
	OPEN
	```

2. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

	```{note}
	To use synthetic backups, ensure that you have created a data synthetic pool for the current user.
	```

	```{only} scutech
	![](../images/Common/storage_pool.png)
	```

### Log in to the resource

Before you create a backup or restore job, log in to the Oracle instance on the console. ADPS provides three authentication methods for Oracle:

- Database authentication

	With this method, you can use all of the ADPS features for Oracle. The user must have the `SYSDBA` or `SYSBACKUP` privilege and the roles of `RESOURCE` and `CONNECT`.

	```{note}
	- Only this method supports table restores.
	- Only Oracle 12c and above provide the `SYSBACKUP` privilege.
	```

- OS authentication

	With this method, you can connect to Oracle conveniently without specifying a database username and password. You can use `root` or the user who installed Oracle to log in to the databases.

	```{note}
	- This method does not support table restores.
	- This method does not support Windows OS.
	- Using `root` will automatically log in to all the database instances on the host. Using other OS accounts will automatically log in to only those instances installed by them.
	```

- Access key

	You can use the access key of the ADPS user who is authorized with access to the resource. This method is suitable for:
	- You cannot get the OS user's username and password.
	- The database user's password changes frequently.

	```{note}
	- This method does not support table restores.
	- Access key authentication is not enabled by default. To enable this feature, log in to the ADPS console as admin, go to **Settings**, open the **Security** tab, and select the **Access key login instance** checkbox.
	```

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the host list, find the host where Oracle resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Common/oracle_login.png)
	```

4. In the **Login** window, select an authentication method according to your needs.

	- Select **Database authentication**, enter the **Username** and **Password** of the Oracle user, and click **Login**.
	- Select **OS authentication**, enter the **Username** and **Password** of the OS user, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.

	```{note}
	To get the access key, log in to the console, click **Personal settings** > **Account settings** on the upper right corner, find **Access key** on the **Preferences** tab, and click **View**.
	```

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

```{note}
For Oracle RAC, Oracle RAC One Node, and two-node Oracle, you need to [bind nodes into a cluster](#bind-nodes-into-a-cluster) after activation.
```

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the databases that you want to back up.

	```{note}
	- If the database has not been fully backed up ever before or after the restore job, the first cumulative incremental backup will be run as a full backup by default.
	- If the database has not been fully backed up, the first tablespace backup will be run as a full database backup and the subsequent jobs will back up only tablespaces.
	- The **Data Pump export** option will be hidden when the Oracle version is older than 10g.
	- When one of the following conditions occurs, the **Data Pump export** option will be disabled:
		- The Oracle database authentication method is OS authentication.
		- The current instance status is STARTED or MOUNTED.
		- The `OPEN_MODE` status of the database is `READ ONLY`.
		- The Oracle database is a standby.
	```

4. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
	- The full backups, incremental backups, cumulative incremental backups, and log backups of one database must be stored in the same storage pool.
	- Synthetic backups require two storage pools. The data pool is used to store data files. The log pool is used to store files except data files.
	```

5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for Oracle:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.2}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.|Not available for continuous log backup jobs.|
|Source side compression of log backup|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.|Only available for log backup and synthetic backup jobs.|
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 255.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.|Not available for continuous log backup jobs.|
|When the database is in NOARCHIVELOG mode|- Send an alert message and cancel the job: When the database is in NOARCHIVELOG mode, ADPS will cancel the backup job and send an alert message. {{ br }}- Allow offline backup: When the database is in NOARCHIVELOG mode, ADPS will send an alert message and perform the backup job offline. {{ br }}- Enable ARCHIVELOG: When the database is in NOARCHIVELOG mode, ADPS will enable the ARCHIVELOG mode according to the specified archive log directory or the database default directory, send an alert message, and then perform the backup job.|Not available for logical backup and continuous log backup jobs.|
|Delete archive logs|- Delete all backed-up archive logs: All backed-up archive logs on the database will be deleted after the backup.{{ br }}- Delete archive logs in the last N days/hours: Backed-up archive logs that exist for longer than the specified N days/hours will be deleted. {{ br }}- Do not delete archive logs: Archive logs will not be deleted after the backup.|Not available for logical backup and continuous log backup jobs.|
|BCT|- BCT (Block Change Tracking) is a feature introduced in Oracle 10g for RMAN incremental backups. You can enable BCT to improve the incremental backup performance. With BCT enabled, you can customize the BCT directory or use the database's default BCT directory. {{ br }}- If you want to back up an Oracle RAC, enter an ASM path.|Only available for full backup and synthetic backup jobs.|
|Minimal supplemental logging|Only the log files generated after you enable this option can be used for log analysis. This operation is a one-time operation.|Not available for logical backup and continuous log backup jobs.|
|Force logging|You can enable the force logging mode of the database.|Not available for logical backup jobs.|
|Encryption wallet password|If you have enabled **Encryption wallet**, enter the encryption wallet password for database backups in NOARCHIVELOG mode.|Not available for continuous log backup jobs.|
|Add local online logs|If the database does not have online redo logs on the file system, you can add online redo logs on the file system to avoid job failures when ASM disk devices cannot be accessed during backup. If the job fails because the ASM disks cannot be accessed, enable this option to automatically create online redo logs or manually create online redo logs on each node and then restart the job.|Only available for continuous log backup jobs.|
|Compress export data|Metadata only is selected by default. {{ br }}- All: All the metadata and table data are compressed.{{ br }}- Data only: Only table data is compressed.{{ br }}- Metadata only: Only metadata is compressed.{{ br }}- None: No data is compressed.|Only available for logical backup jobs.|
|Username|Oracle database username|Only available for logical backup jobs.|
|Password|The password of the user|Only available for logical backup jobs.|
|Data cache directory|The Oracle user should have read and write permissions on the data cache directory.|Only available for logical backup jobs.|
|Automatically back up archive logs when|- Number of logs that are not backed up >= N{{ br }}- Size of logs that are not backed up >= N MiB/GiB/TiB {{ br }}- Remaining space < N MiB/GiB/TiB {{ br }}- Remaining space < N % {{ br }}- Switch log before automatic backup: Enabled forcibly.|Only available for periodical log backup jobs.|
|Allow log backup jobs to run concurrently with database backup jobs|Allows log backup jobs to run concurrently with database backup jobs.|Only available for log backup jobs.|
|When archive logs are missing|If you enable this option, the job will perform an incremental backup when the archive logs are missing to ensure data continuity.|Only available for log backup and tablespace/PDB backup jobs.|
```

- Advanced options

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.2}|
```
```{table} Backup advanced options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|Not available for synthetic backup and continuous log backup jobs.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for synthetic backup and continuous log backup jobs.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|Not available for continuous log backup jobs.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|Not available for continuous log backup jobs.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|Continuous log backup jobs only support pre-script.|
|Archive log backup times|Defines the archive log repeated backup times. The value ranges from 1 to 5.|Not available for logical backup and continuous log backup jobs.|
|Custom RMAN commands|You can customize RMAN commands and insert them between the `allocate channel` command and the `backup` command. For example, you can use `set maxcorrupt for datafile xxx to N;` to ignore bad blocks of some files.|
|Skip|You can set this option to skip inaccessible files, offline files, read-only files, and unrecoverable archived logs and send an alert when these files are skipped.|Only available for full backup, incremental backup, and cumulative incremental backup jobs.|
|Additional parameters|Additional parameters will be passed to `expdp`. Multiple parameters must be separated by space. For more details, see [Oracle documentation](https://docs.oracle.com/cd/E11882_01/server.112/e22490/dp_export.htm#SUTIL836). If the value for the `query` parameter contains double quotes, add single quotes to this value. Example: `query='C##TEST.TEST:"WHERE A in ('1')"'`|Only available for logical backup jobs. |
|Direct I/O|You can use this option to avoid using system cache.|Only available for synthetic backup jobs.|
|Disable synchronous I/O|Disables synchronous I/O system calls from Oracle to speed up the backup job.|Only available for synthetic backup jobs.|
|Synthesis method|- Synthesize by Oracle RMAN: Selected by default.  {{ br }}- Frontend pipeline synthesis: Synthesizes the data file immediately after its incremental backup is completed.{{ br }}Note: When `disk_asynch_io=false` and you use **Synthesize by Oracle RMAN**, the job will send an alert and change the method to **Frontend pipeline synthesis** automatically.|Only available for synthetic backup jobs.|
|Scan FC ports|The FC ports are scanned if you select a data synthetic pool with an FC protocol for synthetic backups. To avoid scanning all ports, you can select to scan a specified FC port.|Available for synthetic backup using FC and LAN-free backup jobs.|
```

````{note}
- The default synthesis method is **Synthesize by Oracle RMAN**. For ASM storage, you can select this option, enable **Mount as an ASM disk group**, and set the **Block size of staging disk**. If you set the size to 4 KiB, then the backend storage block size of the selected data synthetic pool will be 4 KiB. Meanwhile, you must modify the ASM_DISKSTRING parameter manually and add a directory scanner `/dev/adps/asm*` to `ASM_DISKSTRING` before the job. Add a new value rather than overwrite the original value to ensure that the database works normally.

	Example:
	```shell
	# Run the commands as user *grid*
	sqlplus / as sysasm
	select name,path from v$asm_disk;
	# Or show parameter asm_diskstring
	alter system set asm_diskstring='/dev/oradisk*','/dev/adps/asm*' scope=both;
	# Do not overwrite the above result. Add a directory `/dev/adps/asm*`
	```

- The restore target must add the above directory to mount as an ASM disk group
- The synthetic backup sets with the **Mount as an ASM disk group** enabled can only be restored to an instance managed by ASM disks.
````

## Restore

### Restore types

For different needs, ADPS provides several restore types for Oracle, including:

- Media restore

	When a media error occurs in Oracle databases such as data file corruption or accidental deletion, you can restore the databases or some data files through media restore jobs. If the redo logs and archive logs are intact, running media restores will not lose data. Before you create restore jobs, start the database instance but do not need to mount it.

- Database restore

	When a logical error or disaster occurs in Oracle databases, you can restore the databases to a specified point-in-time state through point-in-time restore jobs. Database point-in-time restore supports restoring to the source or a different host. Before you create the restore job, start the target database instance but do not need to mount the database.

- Tablespace or PDB restore

	Restores one or more non-SYSTEM tablespaces to a specified point in time without affecting other tablespaces. The Oracle 12c PDB point-in-time restore can restore a PDB to a specified point in time without affecting other PDBs. Oracle tablespace restore jobs are supported only when the databases are in ARCHIVELOG mode with Oracle 10.2.0.5 and later versions.

- Table restore

	Supports exporting a user’s single table to a point-in-time state. Oracle table restore jobs are supported for Oracle 10.2.0.5 and later versions.

	Before you create a table restore job, ensure that you have the Oracle Table Level Restore license.

- Logical import

	Includes two restore type: Data Pump import and traditional import, and supports importing databases, tablespaces, and schemas. Importing databases and tablespaces requires a user with `DBA` privileges (such as `SYSTEM`, but the user cannot be `SYS`). Importing schema requires the corresponding user or `SYSTEM`.

- Log restore

	Only available for Oracle databases in ARCHIVELOG mode. It can restore the archived logs and online logs in a specified range to the archive log directory in the database on the source or a different host. Without an archive log directory, the logs will be restored to the `db_recovery_file_dest` directory. Logs can be restored from the continuous log backup and log backups.

- Recovery testing

	For creating periodical restore jobs. It can restore the Oracle backup sets to a specified target host to verify the availability of the backup sets.

- Logical recovery testing

	For creating periodical logical import jobs. It can logically import the Oracle backup sets to a specified target host to verify the availability of backup sets.

- Instant recovery

	Only available for Oracle databases with complete synthetic backups. It can reduce the recovery time by mounting the synthetic backup set at the corresponding point in time in the data synthetic pool.

- Instant recovery testing

	For creating periodical instant recovery jobs. It can mount the Oracle synthetic backup sets to a specified target host to verify the availability of backup sets.

- Live Recovery

	Migrates the mounted database back to the source database smoothly after the source database undergoes emergency instant recovery due to a crash or other reasons.

### Before you begin

To restore Oracle to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a media restore job

To create a media restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_media_restore_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Media restore**.

	(3) In the **Restore source** section, select what you want to restore.

	(4) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

4. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

5. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
6. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
7. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a database restore job

To create a database restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_database_restore_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Databases**.

	(3) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to the latest time**. The restore job will restore the databases from the latest backup sets.
	- Select **Restore to point in time**, click the time beside the option, and drag the slider control to specify or enter a point in time for the restore job.
	- Select **Restore to SCN**, click the SCN beside the option, and drag the slider control to specify or enter an SCN for the restore job.
	- Select **Restore to backup state (shortest recovery time)**, click the SCN beside the option, and select a backup job for the restore job in the pop-up window.
	- Select **Analyze redo log files to find the exact SCN to restore to**. You will be redirected to [create a log analysis job](#create-a-log-analysis-job).

	(4) In the **Restore source** section, select the data that you want to restore.

	(5) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	- You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version (major version) are the same as the source's.
	- If you restore the parameter files to a different host, the target database may fail to start.
	- You can [restore to a new instance](#restore-to-a-new-instance).
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a tablespace or PDB restore job

```{note}
Limitations of tablespace point-in-time restores:

- For Oracle 11g R1 and earlier versions, this restore type does not support restoring tablespaces that are deleted by `DROP` or restoring renamed tablespaces to a point in time before it was renamed.
- If the tables in the tablespace to be restored have constraints to tables in other tablespaces, the related tablespaces must be restored together.
- Tablespaces with SYS user objects cannot be restored.
- Tablespaces with replicated master tables cannot be restored.
- Snapshot logs and snapshot tables cannot be restored.
- Tables with `VARRAY` columns, nested tables, and external files cannot be restored.
- Tablespaces with rollback segments cannot be restored.

Since different versions of Oracle have different limitations on tablespace point-in-time restore, see Oracle documentation for more details.
```

To create a tablespace or PDB restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_tablespace_restore_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Tablespace**.

	(3) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**, click the time beside the option, and drag the slider control to specify or enter a point in time for the restore job.
	- Select **Restore to SCN**, click the SCN beside the option, and drag the slider control to specify or enter an SCN for the restore job.
	- Select **Restore to backup state (shortest recovery time)**, click the SCN beside the option, and select a backup job for the restore job in the pop-up window.
	- Select **Analyze redo log files to find the exact SCN to restore to**. You will be redirected to [create a log analysis job](#create-a-log-analysis-job).

	(4) In the **Restore source** section, select the data that you want to restore.

4. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

5. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

6. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

7. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

```{note}
After the tablespace point-in-time restore job, the tablespace cannot be restored from the original backup sets by media restore or point-in-time restore jobs. It is recommended to back up the tablespace or the whole database.
```

### Create a table restore job

To create a table restore job, do the following:

```{note}
- Since table-level restore is performed on the source host and ADPS should connect to the source database, ensure that the database starts the listener, and the agent can connect to database with a net service name. You can use the `tnsping` or `sqlplus` command to confirm whether the database can be connected.
- Table restore supports Oracle 10.2.0.5 and later versions.
```

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_table_restore_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Table**.

	(3) You can enable the **Auxiliary instance for mount recovery** to reduce the auxiliary instant restore time. This option is only available when you have performed a synthetic backup.

	(4) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**, click the time beside the option, and drag the slider control to specify or enter a point in time for the restore job.
	- Select **Restore to SCN**, click the SCN beside the option, and drag the slider control to specify or enter an SCN for the restore job.
	- Select **Restore to backup state (shortest recovery time)**, click the SCN beside the option, and select a backup job for the restore job in the pop-up window.
	- Select **Analyze redo log files to find the exact SCN to restore to**. You will be redirected to [create a log analysis job](#create-a-log-analysis-job).

	(5) In the **Restore source** field, select a schema and enter the table that you want to restore.

	````{note}
	- Oracle 10g does not support importing the exported tables into the database. You need to use the `impdp` command to import the exported tables into the database. One job for Oracle 10g can only restore mutiple tables of one user.
	
		Example:
		```shell
		# Create an export directory in the database. In this example, dir is the export directory.
		create or replace directory dir as 'C:\exp1';
		# In the system terminal, use the impdp command to import the table.
		impdp \"sys/password as sysdba\" directory=dir dumpfile=20161014_143819.DMP tables="TEST"."TEST1"
		```

 	- Oracle 11g supports renaming the table and importing it into the database. You need to select the user that the tables belong to. In the **Restore source** field, enter the source table name in the left field and enter the target name in the right field.
 	- Oracle 12c supports renaming the source table and importing it into the database. You need to select the PDB and user that the tables belong to. In the **Restore source** field, enter the source table name in the left field and enter the target name in the right field. Only the tables in the user tablespace can be restored.
 	````

4. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

5. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

6. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

7. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

```{note}
Using the **Auxiliary instance for mount recovery** may result in the system global area (SGA) of the auxilary instance being too small to restore. You can create the `initaux.ora` file under the data cache direcotry and enter `*.sga_target=2G` in the file to set a bigger value for SGA for the restore.
```

### Create a logical import job

To create a logical import job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_logical_import_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Data Pump import** or **Traditional import**. If you have not performed a **Data Pump export** or **Traditional export**, these two types cannot be selected.

	(3) In the **Restore source** section, select the data that you want to restore.

	- Select a point in time under the database list. The whole database will be imported.
	- Select a point in time under the tablespace list. A tablespace will be imported.
	- Select a point in time of a certain user under the user list. A user schema will be imported. Multi-user import is supported.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a log restore job

To create a log restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_log_recovery_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Log restore**.

	(3) In the **Log range** section, select a range for the restore job.

	- Select **Time range**, click the time beside the option, and drag the slider control to specify or enter the time for the restore job.
	- Select **SCN range**, click the SCN beside the option, and drag the slider control to specify or enter the SCN for the restore job.
	- Select **Log sequence range** and enter the log sequence numbers and thread number in the **Start**, **End**, and **Thread** fields.

	(4) In the **Restore source** section, select the data that you want to restore.

	(5) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a recovery testing job

To create a recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_recovery_testing_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Recovery testing**.

	(3) In the **Restore to** field, set the relative point in time for the restore job. You can restore to the latest time, or restore to N minutes/hours/days/weeks/months before the start time of the restore job.

	(4) In the **Restore source** section, select the data that you want to restore.

	(5) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	- You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	- You can [restore to a new instance](#restore-to-a-new-instance).
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a logical recovery testing job

To create a logical recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_logical_recovery_testing_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Data Pump recovery testing** or **Traditional recovery testing**.

	(3) In the **Restore source** section, select what you want to restore.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create an instant recovery job

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_instant_recovery_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Instant recovery**.

	(3) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to the latest time**. The restore job will restore the databases from the latest backup sets.
	- Select **Restore to point in time**, click the time beside the option, and drag the slider control to specify or enter a point in time for the restore job.
	- Select **Restore to SCN**, click the SCN beside the option, and drag the slider control to specify or enter an SCN for the restore job.
	- Select **Restore to backup state (shortest recovery time)**, click the SCN beside the option, and select a backup job for the restore job in the pop-up window.
	- Select **Analyze redo log files to find the exact SCN to restore to**. You will be redirected to [create a log analysis job](#create-a-log-analysis-job).

	(4) In the **Restore source** section, select the data that you want to restore.

	(5) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create an instant recovery testing job

To create an instant recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_instant_recovery_testing_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Instant recovery testing**.

	(3) In the **Restore to** field, set the relative time for the restore job. You can restore to the latest time, or restore to N minutes/hours/days/weeks/months before the start time of the restore job.

	(4) In the **Restore source** section, select the data that you want to restore.

	(5) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a Live Recovery job

To create a Live Recovery job, do the following:

1. There are two entries to create a Live Recovery job:

- From the menu, click **Restore**. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.
- From the menu, click **CDM**, and find the instant recovery job record. Click the + icon beside the record. The job wizard appears.

2. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_live_recovery_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Live Recovery**.

	(3) In the **Restore source** section, select the data that you want to restore.

	(4) If you want to restore data files to another path or as other names, click **Modify the path for restored files**. In the pop-up window, click the data files. The **Rename** icon appears beside the file. Click the **Rename** icon and modify its name or path in the pop-up window.

3. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

	```{note}
	You can select the source host or a different host as the restore target. To restore to a different host, ensure that the operating system and the Oracle version are the same as the source's.
	```

4. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

5. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

6. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

7. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a log analysis job

```{note}
- Ensure that you have the Oracle LogMiner license.
- Before you create a log analysis job, enable the **Minimal supplemental log** option in the backup options: Only the logs generated after you enable this option can be used for log analysis jobs.
```

To create a log analysis job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Oracle resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Databases**, **Tablespace**, **Tables**, or **Instant recovery**.

	(2) In the **Restore point in time** section, select **Analyze redo log files to find the exact SCN to restore to**. The wizard goes to the **Restore target** step automatically.

4. At the **Restore target** step, select a restore target for the restore job. The wizard goes to the next step automatically.

	```{note}
	When the incarnation branch of the restore target is different from that of the original instance or you select another incarnation branch on the original instance, a database restore will be performed before the log analysis job.
	```

5. At the **LogMiner settings** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_log_miner_01.png)
	```

	- In the **Log analyzing job** field, select to create a new job or use the previous analysis.
	- In the **Log range** section, select a **Time range** or **SCN range** for the analysis.
	- In the **Table name** field, enter the tables that you want to analyze. The result of the log analysis will only show the records of these tables. The table name that you enter should follow this format: `schema_name.table_name`. You can use the wildcard * and press Space to confirm. This field is optional.
	- In the **Username** field, enter the user that you want to analyze. The result of the log analysis will only show the action records of these users. You can enter multiple usernames and press Space to confirm. This field is optional.
	- In the **Actions** field, enter the actions that you want to analyze. The result of the log analysis will only show the records of these actions. Supported actions include INSERT, UPDATE, DELETE, DDL, and so on. This field is optional.
	- In the **Directory of data dictionary file** field, set a directory for the data dictionary file. Note that if you set or modify the directory, the database will be restarted.
	- In the **Advanced options** section, you can set the **Archive log destination**, **Channels**, and **Reconnection time** options. See [Restore options](#restore-options) for more details.

6. After the submission, the log analysis job starts.

	```{note}
	During log analysis, you can leave the page. If you want to check the result, go back to this page and select the previous analysis to get the result.
	```

7. On the result page, select **Analyze redo log files to find the exact SCN to restore to** or **Undo the SQL statement** according to the result.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_log_miner_02.png)
	```

#### Find the exact SCN for the point-in-time restore job

To find the exact SCN for the point-in-time restore, do the following:

1. Select **Analyze redo log files to find the exact SCN to restore to** on the log analysis result page.

2. Click the transaction ID to view the details of the SQL statement.

3. Select an SCN for the restore job and click **Next**. The wizard goes back to the **Backup sets** step where you can see the SCN is what you have selected. See [Create a database restore job](#create-a-database-restore-job) for the following steps. The database will be restored to the SCN that you have selected.

#### Find the exact SCN to undo the SQL statement

To find the exact SCN to undo the SQL statement, do the following:

1. Select **Undo the SQL statement** on the log analysis result page.

2. Click the transaction ID to view the details of the SQL statement.

3. Select the transaction that you want to undo. Click **Next**.

4. The wizard goes to the **Restore target** step. Select a target for the job. The target is usually the original host. If you want to select a different host, the DBID and the incarnation of the source and the target should be the same. Click **Next**.

5. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

### Restore to a new instance

```{note}
- Only the Oracle single instance can be created.
- To create a new instance, you need to use a ADPS user with the administrator role. Otherwise, the **Create Oracle intance** will not be displayed at the **Restore target** step.
```

To create a new instance, do the following:

1. At the **Restore target** step of the database restore and recovery testing jobs, click the **Create Oracle instance** button. The **Create Oracle instance** window appears.

2. At the **Configure** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_add_new_instance_01.png)
	```

	(1) In the **ORACLE HOME** field, set the ORACLE_HOME directory for the new instance.

	(2) In the **ORACLE BASE** field, set the ORACLE_BASE directory for the new instance.

	(3) In the **Instance name** field, set the name for the new instance.

	(4) In the **Sys password** field, set the password for the new instance's sys user.

	(5) In the **Confirm new password** field, enter the password that you entered in the **Sys password** field.

	(6) In the **System user** field, the default user is the login user of the source instance. If the source instance is closed, the system user cannot be recognized, you need to enter the system user manually.

3. At the **Parameters** step, modify the instance initialized parameters according to your needs. Click **Submit** to finish the settings for the new instance.

4. At the **Restore target** step of the database restore and recovery testing jobs, select the new instance. For the following steps, see [Create a database restore job](#create-a-database-restore-job) and [Create a recovery testing job](#create-a-recovery-testing-job).

### Restore options

ADPS provides the following restore options for Oracle:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.20}|
```
```{table} Restore common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Channels|It can improve restore efficiency. The default value is 1 and the value ranges from 1 to 255.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.|Not available for logical import jobs.|
|Reset logs|Enable this option to open the database with the RESETLOG option to READ WRITE after the restore.|Not available for logical import and logical recovery testing jobs.|
|Archive log destination|The location of archive logs.{{ br }}Instant recovery and instant recovery testing:{{ br }}- Separate volume: Suitable for situations when the local storage is not enough or the backup device has a good performance.{{ br }}- Same volume as database copy: Suitable for the situation when archive logs require little space.{{ br }}- Local directory: Suitable for the situation when the local storage has a good performance and its space is enough for storing archive logs.|Not available for logical import and logical recovery testing jobs.|
|Auxiliary directory|Stores data including auxiliary data file sets. The space should be larger than the total size of the restored tablespace.|Available for tablespaces, table, and PDB restore jobs.|
|Data cache directory|Requires the Oracle user to have permissions on the directory that you enter in this field.|Available for table restore, logical import, and logical recovery testing jobs.|
|Username|The name of the Oracle user.|Available for logical import and logical recovery testing jobs.|
|Password|The password of the Oracle user.|Available for logical import and logical recovery testing jobs.|
|Switch instance|- Manual: You can switch the instances manually after the restore.{{ br }}- Immediate: The instances will be switched immediately after the restore.{{ br }}- Specify time: When the specified time is reached, the source instance will be shut down and the target instance will be opened.{{ br }}- Delay: The instances will be switched after the set time following the completion of the restore job.|Only available for Live Recovery jobs.|
|Apply archive log|- Host address of the mounted instance {{ br }}- Target host address{{ br }}- Sync period{{ br }}- Archive log cache directory for the target host{{ br }}- Delete synced logs of the mounted instance|Only available for Live Recovery jobs.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.20}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|Not available for instant recovery and instant recovery testing jobs.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for instant recovery and instant recovery testing jobs.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|Not available for instant recovery and instant recovery testing jobs.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is restored. The post-script is executed after the resource is restored.||
|Disable synchronous I/O|Disables synchronous I/O system calls from Oracle to speed up the restore job.|Not available for logical import jobs.|
|When recovering continuous archive logs|- Download buffer size: If the size of the archive log being downloaded at any time is greater than or equal to the set value, the download will be discontinued and then continue after the previously downloaded logs are applied and deleted. {{ br }}- Reserve free disk space: If the free space of the disk (the target disk that stores downloaded archive logs) at any time is less than or equal to the set value, the download will be discontinued and then continue after the previously downloaded logs are applied and deleted.|Only available for database restore jobs.|
|Additional parameters|Additional parameters will be passed to `impdp`. Multiple parameters should be separated by space. For more details, see [Oracle documentation](https://docs.oracle.com/cd/E11882_01/server.112/e22490/dp_export.htm#SUTIL836).|Only available for logical import and logical recovery testing jobs.|
|Storage protocol|iSCSI and FC|Available for instant recovery, Live Recovery, and instant recovery testing jobs.|
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
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_cdm_01.png)
	```

### Clone a copy

You can click the **Clone copy** icon to create an instant recovery job for the synthetic copy to create a new mounted copy.

To clone copies, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. On the display area, click a full copy under the resource. The **Clone copy** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_cdm_02.png)
	```

4. Click the **Clone copy** button and drag the slider to select a point in time. You will be redirected to the **Backup sets** step. See [Create an instant recovery job](#create-an-instant-recovery-job) to configure the job.

5. After the instant recovery, a copy with a mounted state is added below the selected point in time on the **CDM** page.

### Unmount a copy

You can click the **Unmount** icon to unmount the mounted copies. This operation will make the mounted directory on the restore target inaccessible.

To unmount a copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select a mounted copy. The **Unmount** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_cdm_03.png)
	```

4. Click the **Unmount** icon. A confirmation window appears.
5. Confirm the warning and enter the verification code. Click **OK**.
6. After the unmounting, you can see no such mounted copy record under the full copy.

### Mount a copy

You can click the **Mount** icon to mount an unmounted copy.

To mount an unmounted copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select an unmounted copy. The **Mount** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_cdm_04.png)
	```

4. Click the **Mount** icon. Select the host and resource. Click **Finish**. A confirmation window appears.

5. Confirm the warning and enter the verification code. Click **OK**.

6. After the mounting, you can see the copy become Mounted.

## Oracle cluster backup and restore

ADPS supports the backup and restore of Oracle RAC, Oracle RAC One Node, and two-node Oracle. Before you back up and restore the Oracle cluster, bind nodes into a cluster on the console.

### Before you begin

See [Install and configure the agent](#install-and-configure-the-agent) and [Activate license and authorize users](#activate-licenses-and-authorize-users) to complete the environment deployment on each node.

### Bind nodes into a cluster

To bind nodes into a cluster on the console, do the following:

1. From the menu, click **Resource** > **Cluster**. The **Cluster** page appears.

2. From the toolbar, click the **+** icon. The **Cluster binding** window appears.

3. In the **Cluster binding** window, do the following:

	- Bind the nodes of a two-node cluster:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_cluster_01.png)
	```

	(1) In the **Name** field, enter a name for the cluster.

	(2) From the **Primary node** list, select the active node in the cluster.

	(3) From the **Type** list, select **Active-Passive**.

	(4) From the **Nodes** list, select the passive node in the cluster. You can click **x** to remove the selected node.

	(5) Click **Submit**.

	- Bind the nodes of an Oracle RAC and Oracle RAC One Node:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Oracle/oracle_cluster_02.png)
	```

	(1) In the **Name** field, enter a name for the cluster.

	(2) From the **Primary node** list, select the active node in the cluster.

	(3) From the **Type** list, select **Oracle RAC**.

	(4) From the **Nodes** list, select all the other nodes in the cluster. You can click **x** to remove the selected nodes.

	(5) Click **Submit**.

### Backup and restore

When a switchover occurs, the new active node will take over the backup job automatically. To create an Oracle cluster backup/restore job, see [Create a backup job](#create-a-backup-job) and [Restore](#restore).

```{note}
- During synthetic backup jobs, the Target will be mounted to the active node, and the job will be performed on the active node rather than the passive one.
- The instant recovery job will mount disks to the restore target. If the restore target is a node in an Oracle RAC and the disks are mounted to the ASM storage, all the nodes in the cluster will be able to access the mounted data. Otherwise, the mounted data will only be available for the target node.
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
|OS authentication|- Windows platforms are not supported. {{ br }}- Table restore is not supported. {{ br }}- The continuous log backup job will be stopped after you restore the database. You need to perfrom a full backup first.|
|Access key authentication|Table restore is not supported.|
|Backup|- RMAN commands: Inserted between the `allocate channel` command and the `backup` command. For example, you can use `set maxcorrupt for datafile xxx to N;` to ignore bad blocks of some files. {{ br }}- Delete archive logs: When Oracle backup and data synchronization exist at the same time, you should select the **Do not delete archive logs** option when creating backups. Otherwise, the backup jobs will delete unsynchronized archive logs, leading to the failure of data synchronization.{{ br }}- The **Data Pump export** option will be hidden when the Oracle version is older than 10g.{{ br }}- When one of the following conditions occurs, the **Data Pump export** option will be disabled: The Oracle database authentication method is OS authentication; The current instance status is STARTED or MOUNTED; The `OPEN_MODE` status of the database is `READ ONLY`; The Oracle database is a standby.{{ br }}- Traditional export is not supported when the following conditions occur: the database is logged in with an OS user, or the instance status is STARTED or MOUNTED.{{ br }}- Log backup is not supported when the database is in NOARCHIVELOG mode.{{ br }}- Continuous log backup is not supported when the database is in NOARCHIVELOG mode.{{ br }}- Synthetic backup is not supported for HP-UX on PA-RISC, HP-UX on IA64, Solaris SPARC, and Solaris-x86.{{ br }}- When the Oracle database major version is newer than or equal to 11, the traditional export job may fail. When the database OPEN-MODE is READ WRTIE, Data Pump export is recommended.{{ br }}- The full backup and log backups should be in the same pool. Otherwise, the recovery will fail.|
|Synthetic backup|- The agent supports Linux (Red Hat 4.9 and later), AIX, and Windows 2008 and later. The database version supports Oracle 10g and later versions.{{ br }}- The **Disable synchronous I/O** option is only available for Linux platforms.{{ br }}- If iSCSI is used, `iscsi-initiator-utils` should be installed on the host with the agent installed.{{ br }}- The Oracle database for AIX Oracle cannot be larger than 128T for synthetic backups.{{ br }}- If the Oracle does not enable AsyncIO, ADPS will not automatically switch the synthesis method to Frontend Pipeline Synthesis.{{ br }}- Databases on Red Hat 4.0 cannot be larger than 16T for synthetic backups.{{ br }}- Synthetic pools on Red Hat 4.0 should use the ext3 filesystem format.|
|Live Recovery|Before Live Recovery, an instant recovery job should be created and performed.|
|Analyze redo log files to find the exact SCN to restore to|- The logs selected for the analysis should be in the same incarnation branch as the current database. To select another incarnation branch for the analysis, perform a point-in-time restore before the log analysis.{{ br }}- Only supports analyzing the source database.{{ br }}- When you set or modify the directory for data dictionary files, the database will be restarted.{{ br }}- Cannot show the analysis progress and cannot stop the analysis.{{ br }}- Cannot undo DDL (CREATE, ALTER, DROP, and so on) SQL statements. |
|Two-node Oracle|Cannot be backed up to a LAN-free pool.|
|Oracle RAC|The multi-node concurrent backup of a database cannot be achieved with OS authentication.|
|Oracle XE|- Database logical backup and restore are not supported.{{ br }}- Tablespace and table poin-in-time restore are not supported.|
|Oracle Data Guard|- ADPS can back up the primary database in the Oracle Data Guard. However, when a switchover occurs, the new primary database will not take over the backup jobs automatically. You need to recreate the backup jobs.{{ br }}- ADPS can restore the primary database in the Oracle Data Guard. However, after a switchover occurs, you need to install the agent on the new primary, connect it to the backup server, activate licenses, and authorize users so that a restore job can be performed.|
|Oracle Active Data Guard|- ADPS can perform a continuous log backup job for the primary and standby databases. {{ br }}- Before you perform a restore on the standby database, disable the log application.{{ br }}- If you enable the **Reset log** option and select the standby as the restore target, the standby will become the new primary after the restore.{{ br }}- After a restore job is performed on the standby and it is opened, you need to check the alerts. If alerts like `ORA-00338: log 5 of thread 1 is more recent than control file` are reported, you can execute `alter database clear unarchived logfile group 5;`.|
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
|Oracle RAC|RAC is the abbreviation of Real Application Clusters. It is a new technology adopted in Oracle databases with new versions and a kind of high availability. RAC is also the core technology that Oracle databases support grid computing environment.|
|Oracle XE|A small database introduced by Oracle that can be used freely.|
|Oracle DG |DG is the abbreviation of Data Guard. Oracle Data Guard is the most efficient and comprehensive solution of data availability, data protection, and disaster recovery for enterprise databases.|
```