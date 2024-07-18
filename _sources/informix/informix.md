# User Guide for Informix

## Overview

This guide introduces how to install and configure the ADPS agent and how to properly use ADPS to back up and restore Informix.

The backup and restore features supported by ADPS include:

- Backup sources

	Database, log

- Backup types

	Full backup, cumulative incremental backup, incremental backup, log backup, and log backup (on demand)

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, data synthetic pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, retry and backup taillog

- Restore types

	Point-in-time restore, mixed restore, full recovery testing, and continuous log recovery testing

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

To back up and restore Informix, first install the ADPS agent on the host where Informix resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where Informix resides is on the Aurreum Data Protection Suite's compatibility lists.

Supported Informix versions include:

- Informix 11.50/11.70/12.10/14.10

### Install the agent

The ADPS agent can be installed on Windows and Linux. You can select the installation method according to your environment.

The ADPS agent supports two methods to connect to Informix: ODBC and DBACCESS. See [Configure connection method](#configure-connection-method) for the specific configuration.

- If Informix CSDK (Client Software Development Kit) is installed, use ODBC. 
- If Informix CSDK (Client Software Development Kit) is not installed, use DBACCESS. 

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
7. At the **Select components** step, select **Informix** from the component list. Click **Next**.
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

	(2) From the **Component** list, select **Informix**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

	```{code-block} python
	[root@cache148 ~]#curl -o- "http://192.168.18.252:80/d2/update/script?modules=Informix&ignore_ssl_error=&access_key=7dc57757b7e675f2ec5495180f90ac70&rm=&tool=curl" | sh
	```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Informix, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where Informix resides. Click the **Register** icon. After the registration, the **Activate** window appears.

3. In the **Activate** window, select the Informix backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup types

ADPS provides five common backup types for Informix.

- Full backup

  Backs up the entire instance, including all online, non-temporary storage spaces and the used logical logs, as well as configuration files such as `onconfig`, `sqlhosts`, `ixbar`, and `oncfg`. This backup type corresponds to the Informix level-0 backup.

- Cumulative incremental backup

  Backs up all online, non-temporary storage spaces and the used logical logs, as well as configuration files such as `onconfig`, `sqlhosts`, `ixbar`, and `oncfg` that have changed since the last full backup with a full backup as the baseline. This backup type corresponds to the Informix level-1 backup.

- Incremental backup

	Backs up all online, non-temporary storage spaces and the used logical logs, as well as configuration files such as `onconfig`, `sqlhosts`, `ixbar`, and `oncfg` that have changed since the last backup. This backup type corresponds to the Informix level-2 backup.

- Log backup

	Backs up the current logical logs and other full logical logs.

- Log backup (on demand)

	When logical logs are full or when you manually execute log switch command, a log backup (on demand) job will be automatically executed to back up the current logical logs.


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
2. Perform a **cumulative incremental backup** or **incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.
3. Perform a **log backup** hourly if your database supports log backups to achieve the precise point-in-time restore and ensure that the restore granularity can reach the second level.

### Before you begin

Before you back up and restore Informix, check the following:

1. Check the Informix instance status.

	- Windows

	Log in to the host and open the service list. Check whether the Informix service status is Started or not.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Informix/informix_database_01.png)
	```

	- Linux

	Open the terminal on the host and use the `onstat -` command to check the Informix service status. The service status should be "On-Line".

	```{code-block} shell
	[informix@redhat6 ~]$ onstat -
	Your evaluation license will expire on 2023-10-02 00:00:00
	
	IBM Informix Dynamic Server Version 12.10.FC4TL -- On-Line -- Up 00:45:15 -- 208516 Kbytes
	```
	
2. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.


### Log in to the instance

Before you create a backup or restore job, log in to the Informix instance on the console. ADPS provides two authentication methods for Informix:

- Database authentication

	The Informix user should have the following privileges: `Connect`, `Resource`, and `DBA`. 

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

2. From the host list, find the host where Informix resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.

3. Click **Login** beside the resource. The **Login** window appears.

4. In the **Login** window, select an authentication method according to your needs.

	- Select **Database authentication**, enter the **Username** and **Password**, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.
	
5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

```{note}
If you use ODBC to access Informix 12.10 on Windows, follow the configuration steps below before you log in.

1. Modify the file `C:\Program Files\IBM Informix Software Bundle\etc\sqlhosts` as the follow:
	- Change the "HostName" of the instance “ol_informix1210” to "127.0.0.1".

2. Launch Setnet32, click "Server Information" and fill in the display options according to the `sqlhosts` file. See below:
	- IMB Informix Server: ol_informix1210 (Instance name)
	- HostName: ‘\*’127.0.0.1 (The ‘\*’ sign cannot be missing.)
	- Protocolname: olsoctcp
	- Service Name: ol_informix1210

3. After the modification, click "Make Default Server".
4. Restart the Informix instance service.
5. Open Windows Start menu, select "IBM Informix Client SDK" program, and click "ConnectTest Demo" to test if the connection is successful.
```

## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where Informix resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the databases that you want to back up.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Informix/informix_backup_01.png)
	```

4. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
	Cumulative incremental backups, incremental backups, log backups, and log backups (on demand) must have the same backup target as their baseline backups.
	```

5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

```{note}
	No **Backup schedule** configuration is required for the log backup (on demand) job, as the job will be automatically triggered upon a database log switch event.
```

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Informix/informix_backup_02.png)
	```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

```{note}
- If the database has never been fully backed up, or has not been fully backed up after the restore, run a full backup job before the log backup (on demand) job.
- One database cannot have a periodical log backup job and a log backup (on demand) job at the same time. If one type of job already exists, creating the other type will fail.
- When a log backup (on demand) job for Informix 11.50 is created, ADPS checks the ALARMPROGRAM setting for the database instance. If the ALARMPROGRAM is not set to the 'alarmprogram' script, ADPS will reset the ALARMPROGRAM and then restart the database instance to ensure a successful execution of the backup job.
```

8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for Informix:

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
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 64.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.||
| ON-Bar | - The option only appears for the fist-time backup job creation. {{ br }}- If your Informix version is lower than 12.10, the database will be shut down and restarted during the ON-Bar configuration, and it is recommended that you schedule job execution in idle business time. {{ br }}- Configuring ON-Bar will modify the following values in `$INFORMIXDIR/etc/onconfig` file: `BAR_BSALIB_PATH`, `BAR_MAX_BACKUP`, and `LTAPEDEV`. | |
| Failover log archive path | Specify an existing local directory for path failover. The database user must have read and write permissions on the path. When the primary archive path fails, the specified path will be used to archive the log files temporarily. When the primary path becomes available, the logs will be moved to the primary path from the specified path. |Only available for log back (on demand) jobs. |
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

For different needs, ADPS provides several restore types for Informix, including:

- Point-in-time restore

	When a disaster occurs in an Informix database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. You can restore to the original or a different host.

- Mixed restore

	A mixed restore is a cold restore for critical dbspaces followed by a warm restore for the remaining dbspaces. Compared with a cold restore for all dbspaces, it makes critical data available and the database online soon.

- Full recovery testing

	With the hourly, daily, weekly, or monthly schedule, a full recovery testing job can recover the latest Informix backup set to the specified target host to verify that the backup set is available.

- Continuous log recovery testing

	A continuous log recovery testing job allows you to create periodic log recovery testing jobs that apply the latest logical log files from the Informix host to the target instance synchronously.

### Before you begin

To restore Informix to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Informix resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Informix/informix_timepoint_restore_01.png)
	```

	(1) From the **Restore type** list, select **Point-in-time restore**.

	(2) In the **Restore to point in time** section, select a point in time for the restore job.

	(3) In the **Restore source** section, specify a restore source.

	(4) Click **Next**.


```{note}
If you need to modify the path where the chunk files are located, click **Modify the path of the diretory where the chunk file is located** to select a path. It only supports modifying the path where the chunk files are located. It does not allow changing the path of individual chunk files, nor does it support modifying the paths of the `onconfig` and `sqlhosts` configuration files.
```

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a mixed restore job

To create a mixed restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Informix resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Mixed restore**.

	(2) From the **Restore to point in time** list, select a point in time for the restore job.

	(3) In the **Restore source** section, select folders or files that you want to restore.

	(4) Click **Next**.

```{note}
- In the **Restore source** section, if the `onconfig` and `sqlhosts` files are unchecked, the files will still be restored to their original directory, but they will be named as `file.restore` instead of overwriting the original configuration files.

- If you need to modify the path where the chunk files are located, click **Modify the path of the diretory where the chunk file is located** to select a path. It only supports modifying the path where the chunk files are located. It does not allow changing the path of individual chunk files, nor does it support modifying the paths of the `onconfig` and `sqlhosts` configuration files.
```

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

  ```{only} scutech
    ![](../images/Backup_Restore/DBackup3/Informix/informix_mixed_restore_02.png)
  ```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the instant recovery, go to the **CDM** page. A mounted copy is added below the full copy. See [View a copy](#view-a-copy).

### Create a full recovery testing job

To create a full recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where Informix resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Full recovery testing**.

	(2) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the original or a different host.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a continuous log recovery testing job

To create a continuous log recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where Informix resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Continuous log recovery testing**.

	(2) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the original or a different host.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

```{note}
A continuous log recovery testing job will set the target database to the "fast recovery" state. In this state, the database will be unable to be connected for query operations. To restore the target database to the "On-Line" state, you can disable the continuous log recovery testing job.
```

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for Informix:

- Commom options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore common options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Backup tail-log|With tail-log backups, the database can be restored to the state when the disaster occurred. Tail-log backup depends on full backup. If a full backup is not performed after recovery, do not perform the tail-log backup.|Only available for point-in-time restore jobs.|
|Apply tail log|Enabling the option only if you want to restore the database to the latest point in time by applying the logical logs that have not been backed up.|Only available for mixed restore jobs.|
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
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is restored. The post-script is executed after the resource is restored.||
|Serial restore|Set BAR_MAX_BACKUP=1 to spawn one thread each time when restoring the instance. Use serial restore in case parallel restore fails.|Only available for point-in-time restore and mixed restore jobs.|
```

## Configure connection method

The ADPS agent supports two methods to connect to an Informix database: ODBC and DBACCESS. 

- If Informix CSDK (Client Software Development Kit) is installed, use ODBC. 

- If Informix CSDK (Client Software Development Kit) is not installed, use DBACCESS. 

### Linux

(1) Configure DBACCESS.

```shell
   > /etc/init.d/adps-agent config informix
   Detected Informix home:
   /opt/informix
   Your Choice: /opt/informix
   Enter access mode (ODBC or DBACCESS):DBACCESS
   Stopping adps-agent                                    [  OK  ]
   Starting adps-agent                                    [  OK  ]
```
(2) Verify the successful configuration.

```shell
   > cat /etc/opt/aurreum/adps/agent/conf.d/informix.conf
   INFORMIX_DIR=/opt/informix
   INFORMIX_LD_LIBRARY_PATH=/opt/informix/lib
   # mode: DBACCESS or ODBC
   INFORMIX_ACCESS_MODE=DBACCESS
```

### Windows

If CSDK is not installed, DBACCESS will be used automatically. The corresponding InformixAccess registry entries are as follows:

```shell
   ## Add Registry
   HKEY_LOCAL_MACHINE\SOFTWARE\aurreum\adps\agent\InformixAccess
   #String value DBACCESS
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
|Restore|The database version number, instance name, and data storage path of the restore target are required to be the same as those of the source database.|
| Backup tail-log |Only backs up data, not new tables. |
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