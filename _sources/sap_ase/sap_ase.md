# User Guide for SAP ASE

## Overview

This guide introduces how to install and configure the ADPS agent and how to properly use ADPS to back up and restore SAP ASE.

The backup and restore features supported by ADPS include:

- Backup sources

	Single or multiple databases

- Backup types

	Full backup and log backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, and speed limit

- Restore types

	Point-in-time restore and recovery testing

- Restore targets

	Original host (original database, new database) and different host (Linux, Windows)

- Restore options

	Reconnection time, resumption buffer size, speed limit, precondition, pre-script, and post-script

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore SAP ASE, first install the ADPS agent on the host where SAP ASE resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where SAP ASE resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of SAP ASE on multiple systems, including:

- Windows 2003 R2 SP2/Windows 2008/Windows 2008 R2
- Red Hat 4/6/6.5
- AIX 7.1
- Solaris 10/11

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
7. At the **Select components** step, select **SAP ASE** from the component list. Click **Next**.
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

	(2) From the **Component** list, select **SAP ASE**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

  ```{code-block} shell
  curl "http://IP:80/d2/update/script?modules=sybase&ignore_ssl_error=&access_key=7dc57757b7e675f2ec5495180f90ac70&rm=&tool=curl" | sh
  ```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore SAP ASE, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. On the **Resource** page, select the host where SAP ASE resides. Click the **Register** icon. After the registration, the **Activate** window appears.

  ```{only} scutech
  ![](../images/Common/sap_ase_register_resources.png)
  ```

3. In the **Activate** window, select the SAP ASE backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup types

ADPS provides two common backup types for SAP ASE:

- Full backup

	Backs up one or more databases.

- Log backup

	Backs up logs, with a full backup as the baseline.

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
2. Perform a **log backup** hourly if your database supports log backups to achieve the precise point-in-time restore and ensure that the restore granularity can reach the second level.

### Before you begin

Before you back up and restore SAP ASE, check the following:

1. Check storage pools

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

### Log in to the resource

Before you create a backup or restore job, log in to the SAP ASE instance.

To log in to the instance, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. From the host list, find the host where the SAP ASE resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.

3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Common/sap_ase_login.png)
	```

4. In the **Login** window, enter the **Username** and **Password** of the OS user, and click **Login**.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.


## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where SAP ASE resides and select the resource. The wizard goes to the next step automatically.
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

ADPS provides the following backup options for SAP ASE:

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

For different needs, ADPS provides several restore types for SAP ASE, including:

- Point-in-time restore

	When a disaster occurs in a SAP ASE database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. You can restore to the original or a different host.

- Recovery testing

	Restores the latest backup sets to another instance on the original host or a different host hourly, daily, weekly, and monthly.

### Before you begin

To restore SAP ASE to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where SAP ASE resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **Point-in-time restore**.
	(2) From the **Database** list, select a database.

	(3) In the **Restore to point in time** section, specify a point in time.

	(4) From the **Backup sets** list, select a backup set.
	(5) Click **Next**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/SAP_ASE/sap_ase_timepoint_restore_01.png)
	```

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
2. At the **Hosts and resources** step, select the host where SAP ASE resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/SAP_ASE/sap_ase_recovery_testing_01.png)
	```

	(1) From the **Restore type** list, select **Recovery testing**.

	(2) In the **Restore source** section, select one or more databases for the restore job.

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

ADPS provides the following restore options for SAP ASE:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore common options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Restore database|Leave it blank to overwrite the original database or enter a name to create a new database.||
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
|Backup and restore|Backup and restore operations for multiple instances of SAP ASE on a single server are not supported.  |
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