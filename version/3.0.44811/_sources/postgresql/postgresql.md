# User Guide for PostgreSQL

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore PostgreSQL.

The backup and restore features supported by ADPS include:

- Backup sources

	Database, schema, and table

- Backup types

	Full backup, incremental backup, log backup, and logical backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore, logical recovery, log restore, and recovery testing

- Restore targets

	Original host and different host (ADPS supports cross-machine restore of PostgreSQL with varying minor versions.)

- Restore options

	Channels, restore database (origin or new), open database after restore, archive log destination, database configuration file

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore PostgreSQL, first install the ADPS agent on the host where PostgreSQL resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where PostgreSQL resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of PostgreSQL, including standalone single instance, standalone multiple instances, and Master-Slave replication. Supported versions include:

- PostgreSQL 8.1/8.4
- PostgreSQL 9.1/9.2/9.3/9.4/9.5/9.6
- PostgreSQL 10.6/10.8/10.19
- PostgreSQL 11.0/11.1/11.7
- PostgreSQL 12.1/12.2/12.3/12.9
- PostgreSQL 13.0/13.4/13.5/13.8
- PostgreSQL 14.0/14.1/14.2/14.3/14.4/14.5/14.6
- PostgreSQL 15.0/15.1

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
7. At the **Select components** step, select **PostgreSQL** from the component list. Click **Next**.
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

	(2) From the **Component** list, select **PostgreSQL**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the feature, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

  ```{code-block} python
  root@ubuntu:~# curl -o- "http://192.168.20.192:50305/d2/update/script?modules=postgres&location=http%3A%2F%2F192.168.20.192%3A50305&access_key=acaa24b49fbf2655de1a5dae72a4b56e&rm=&tool=curl" | sh
  	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
  	100  9210    0  9210    0     0  1697k      0 --:--:-- --:--:-- --:--:-- 2998k
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
      0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
      100 68.7M  100 68.7M    0     0  80.7M      0 --:--:-- --:--:-- --:--:-- 71.7M
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
      0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
      100 7890k  100 7890k    0     0  60.2M      0 --:--:-- --:--:-- --:--:-- 60.2M
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                   Dload  Upload   Total   Spent    Left  Speed
      0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
      100 5913k  100 5913k    0     0  60.2M      0 --:--:-- --:--:-- --:--:-- 60.2M
  ```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore PostgreSQL, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where PostgreSQL resides. Click the **Register** icon. After the registration, the **Activate** window appears.

```{only} scutech
![](../images/Backup_Restore/DBackup3/PostgreSQL/postgres_license01.png)
```

3. In the **Activate** window, select the PostgreSQL backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

	```{note}
	With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Backup

### Backup types

ADPS provides four common backup types for PostgreSQL.

- Full backup

	Backs up all the databases on the instance.

- Incremental backup

	 Backs up data blocks that have changed since the last backup, with a full backup as the baseline. If the database has not been fully backed up, or has not been fully backed up after the restore, the first incremental backup will be performed as a full backup by default.

- Log backup

	 Backs up the archive logs of database instances, with a full backup as the baseline.

- Logical backup

	Backs up one or more databases, schemas, and tables. Currently, you can select only one of the following types for a logical backup job: database, schema, and table.


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
2. Perform an **incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.
3. Perform a **log backup** hourly if your database supports log backups to achieve the precise point-in-time restore and ensure that the restore granularity can reach the second level.

Avoid using the following strategies: Only perform full backups or perform a full backup followed by all incremental or log backups.

### Before you begin

Before you back up and restore PostgreSQL, check the following:

1. Check the PostgreSQL instance status

  - Windows

	Log in to the host and open the service list. Check whether the PostgreSQL service status is *Running* or not.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/PostgreSQL/postgres_database01.png)
	```

  - Linux

	The PostgreSQL database service should be in the “Running” state for backup and restore.

  ```{code-block} shell
  -bash-4.2$ /usr/pgsql-13/bin/pg_ctl -D /var/lib/pgsql/13/data/ -l logfile status
   pg_ctl: server is running (PID: 18378)
   /usr/pgsql-13/bin/postgres "-D" "/var/lib/pgsql/13/data/"
  ```

2. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

3. Check the minimum privileges required for a database user to do backups.

```{tabularcolumns} |\Y{0.13}|\Y{0.62}|\Y{0.25}|
```
```{table} Minimum privileges required
---
class: longtable
---
   | Backup types   | Minimum privileges required                      | Database versions required                  |
   | --------- | ---------------------------------------- | ------------------------ |
   |Physical backup|grant execute on function pg_start_backup(text,boolean,boolean) to <user_name>;{{ br }}grant execute on function pg_stop_backup() to <user_name>;{{ br }}grant pg_read_all_settings to <user_name>;|PostgreSQL 10.0 and later|
   |Physical backup|alter user <user_name> with superuser;|Versions prior to PostgreSQL 10.0 |
   |Log backup|grant execute on function pg_switch_wal() to <user_name>;|PostgreSQL 10.0 and later|
   |Log backup|grant execute on function pg_switch_xlog() to <user_name>;|Versions prior to PostgreSQL 10.0|
   |Logical backup|grant connect on database <database_name> to <user_name>;{{ br }}grant usage on schema <schema_name> to <user_name>; {{ br }}grant select on all tables in schema <schema_name> to <user_name>;{{ br }}grant select on all sequences in schema <schema_name> to <user_name>; | |
```

### Log in to the resource

Before you create a backup or restore job, log in to the PostgreSQL on the console.

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. From the host list, find the host where PostgreSQL resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.

3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/PostgreSQL/postgres_login01.png)
	```

4. In the **Login** window, enter the **Username** and **Password**, and click **Login**.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.



## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where PostgreSQL resides and select the instance. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the databases that you want to back up.

4. At the **Backup target** step, select a storage pool. Click **Next**.

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

ADPS provides the following backup options for PostgreSQL:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Use the fast compression algorithms to compress data during the backup job.|Not available for synthetic backup jobs.|
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 64.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.{{ br }}The value for synthetic backup jobs ranges from 1 to 255.|Only available for full backup jobs.|
|Archive log directory|Set the archive log directory that the PostgreSQL login user has permission to operate on. The archive directory for the PostgreSQL cluster needs to be manually enabled.|Not available for logical backup jobs.|
|Delete archive logs|{{ Product }} provides the following options to delete archive logs that are backed up. {{ br }}- Delete all backed-up archive logs. {{ br }}- Delete archive logs older than 7 day(s)/hour(s). {{ br }}- Do not delete archive logs.|Not available for logical backup jobs.|
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
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|Not available for synthetic backup jobs.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for synthetic backup jobs.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
```

## Restore

### Restore types

For different needs, ADPS provides several restore types for PostgreSQL, including:

- Point-in-time restore

  When a logical error or disaster occurs in a PostgreSQL database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. You can restore to the original or a different host.

- Logical recovery

  When a database, schema, or table in PostgreSQL loses data, such as files damaged or deleted by mistake, you can restore them to the latest state through Logical Recovery.

- Log restore

  A log restore job can only be executed when a log backup set exists. It only downloads archive logs to the specified directory on the target host, and does not perform the restore operation.

- Recovery testing

  Restores the latest backup sets to another instance on the original host or to another host hourly, daily, weekly, and monthly.

### Before you begin

To restore PostgreSQL to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where PostgreSQL resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Point-in-time restore**.

	(2) In the **Restore point in time** section, specify a point in time for the restore job.

	- Select **Restore to point in time**. If you have performed a log backup job, you can specify the restore point in time by entering the date and time or dragging the slider control.
	- Select **Restore to backup state (shortest recovery time)**. You can select a backup set in the **Backup sets** section to restore to its latest backup state.

	(3) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a logical recovery job

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where PostgreSQL resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Instant recovery**.

	(2) From the **Recovery type** list, select a data recovery type for the restore job.

	(3) In the **Restore source** section, select a database and a point in time.

	(4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the instant recovery, go to the **CDM** page. A mounted copy is added below the full copy. See [View a copy](#view-a-copy).


### Create a log restore job

To create a log restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where PostgreSQL resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Log restore**.

	(2) In the **Time range** section, click the time next to the option and a window pops up. In the pop-up window, select a time range. You can also drag the slider control or enter the time to specify a time range for the restore job.

	(3) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The target can be a standalone PostgreSQL or a cluster. When the restore target is a cluster, the restore job will be run on all the nodes in the cluster.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a recovery testing job

To create a recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where PostgreSQL resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

   (1) From the **Restore type** list, select **Recovery testing**.

   (2) In the **Restore source** section, select databases for the restore job. If you want to rename the restore source, click the database. The **Rename** icon appears beside the name. Click the icon and specify a name in the pop-up window.

   (3) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the original or a different host.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

   - Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
   - Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
   - Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
   - Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for PostgreSQL:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Channels|It can improve backup efficiency. The default value is 1. The maximum value for the range should not exceed the maximum number of channels set in the selected backup set.|Only available for point-in-time restore jobs.|
|Open database after restore|After this option is enabled, the database will automatically start upon successful recovery. It is checked by default.|Only available for point-in-time restore jobs.|
|Restore database|Leave the field blank to overwrite the original database. To restore to a new database, enter the new database name.|Only available for logical recovery jobs.|
|Archive log destination|Set the restore directory for archive logs.|Only available for log restore jobs.|
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
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is restored. The post-script is executed after the resource is restor.||
|Database configuration file|Click **Browse** to select a path or manually enter the path for the PostgreSQL configuration file of the restore target. If the option is left blank, the configuration file from the backup set will be used by default.|Only available for point-in-time restore jobs.|
```

## Cluster backup and restore

ADPS supports the backup and restore of PostgreSQL master-slave clusters. Before you back up and restore the PostgreSQL cluster, bind nodes into a cluster on the console.

### Before you begin

See [Install and configure the agent](#install-and-configure-the-agent) and [Activate license and authorize users](#activate-licenses-and-authorize-users) to complete the environment deployment on each node.

### Bind nodes into a cluster

To bind nodes into a cluster on the console, do the following:

1. From the menu, click **Resource** > **Cluster**. The **Cluster** page appears.

2. From the toolbar, click the **+** icon. The **Cluster binding** window appears.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/PostgreSQL/postgres_cluster01.png)
	```

3. In the **Cluster binding** window, do the following:

	(1) In the **Name** field, enter a name for the cluster.

	(2) From the **Primary node** list, select the active node in the cluster.

	(3) From the **Type** list, select **Master slave replication**.

	(4) From the **Nodes** list, select the inactive node in the cluster. You can click **x** to remove the selected node.

	(5) Click **Submit**.


### Backup and restore

When a switchover occurs, the new primary node will take over the backup job automatically. To create a PostgreSQL cluster backup/restore job, see [Create a backup job](#create-a-backup-job) and [Restore](#restore). Note that:

1. On the backup job wizard, only the active nodes of the PostgreSQL cluster are displayed.
2. In the event of a switchover within the PostgreSQL cluster, the new active node automatically takes over the backup job.
3. On the restore job wizard, one instance or cluster can be selected as the restore target.

## Limitations

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Limitations
---
class: longtable
---
|Feature|Limitations|
| --- | --- |
|Resources|- ADPS does not support PostgreSQL cluster on Windows.{{ br }}- After cluster binding, all nodes must be online for job creation.{{ br }}- Once the cluster is configured, the primary node cannot be changed unless the entire cluster is deleted.|
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