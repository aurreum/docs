# User Guide for NDMP

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore NDMP hosts.

The backup and restore features supported by ADPS include:

- Backup sources

	Volume

- Backup types

	Full backup, incremental backup

- Backup targets

	Standard storage pool, deduplication storage pool, tape library pool, and object storage service pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore

- Restore targets

	Original volume, new volume

- Restore options

	Restore location (original path or customized path)

- Protocols

    V3, V4

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can log in to the resource, create backup and restore jobs.
```

## Install and configure the agent

### Install the agent

The ADPS agent can be installed on Linux and supports online and offline installation. We recommend online installation.

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

	(2) From the **Component** list, select **NDMP**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

   ```{code-block} shell
   curl "http://IP:50305/d2/update/script?modules=ndmp&location=http%3A%2F%2FIP%3A50305&ignore_ssl_error=&access_key=3c6befb8b91bf283373737bd71a05365&rm=&tool=curl" | sh
   ```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The backup host with the agent installed appears on the page.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the backup host. Click the **Register** icon. After the registration, the **Activate** window appears. Click **Submit**.
3. After you activate the license, the **Authorize** window appears. From the **User group** list, select user groups to authorize access to the resource. Click **Submit**.

    ```{only} scutech
  	![](../images/Backup_Restore/DBackup3/NDMP/ndmp_license_01.png)
    ````

	```{note}
	1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
	2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Add and configure the NDMP host

To back up and restore NDMP, first install the ADPS agent on a host that can communicate with NDMP hosts.

### Verify the compatibility

ADPS supports the backup and restore of the following NDMP hosts:

- ONTAP 8.3 (node-scoped mode)/9.0 (node-scoped mode, SVM-scoped mode)
- OneFS 8.1.0.2
- EMC VNX5500
- NetApp FAS3240
```{only} scutech
- OceanStor DeviceManager V300R006
```

### Add the NDMP host

To add an NDMP host, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the toolbar, click the "+" icon and select **NDMP host**. The **Add NDMP host** window appears.
3. In the window, do the following:

	- In the **Name** field, enter a name for the NDMP host.
    - In the **Address** field, enter the address of the NDMP host. For NetApp Clustered Data ONTAP, enter the address of the storage virtual machine (SVM).
    - In the **Port** field, enter the port of the NDMP host.
    - From the **Authentication method** list, select an authentication method.
        - NONE: No password authentication.
        - TEXT: Log in with the username and password of NDMP. The password is transmitted in clear text.
        - MD5: Log in with the username and password of NDMP. The password is transmitted encrypted.
    - From the **Backup host** list, select a host to list backup content and as the default host for backups and restores.

### Activate the NDMP license and authorize users

To activate the license and authorize users, do the following:

1. After you add the NDMP host, a window to activate the NDMP license appears. Click **Activate**.

2. After activation, the **Authorize** window appears.

3. In the **Authorize** window, do the following:

    (1) From the **User group** list, select user groups to authorize access to the resource.

    (2) Set the resource as **Protected** or not. The **Protected** resource cannot be the restore target or the data replication target unless the administrator removes the **Protected** label.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

## Backup

### Backup types

ADPS provides two common backup types for NDMP hosts.

- Full backup

	Backs up the whole volume.

- Incremental backup

	Backs up only the data that has changed since the last backup (including the full backup and the incremental backup) with a full backup as the baseline.

### Backup policies

ADPS provides six types of backup schedules: immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

1. Perform a **full backup** once a week when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.
2. Perform an **incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.

### Before you begin

Before you back up and restore NDMP hosts, check the following:

1. Check the NDMP host status.

    Log in to the ADPS console as an operator. On the **Resource** page, check whether the activated and authorized NDMP host exists on the host list with an Online state.

2. Check the storage pool.

    (1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

    (2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the NDMP host and resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

    (1) From the **Backup type** list, select a backup type.

    (2) From the **Backup method** list, select a backup method. Different vendors support different methods. Generally, they all support **dump**.

    (3) In the **Backup source** section, select the volume that you want to back up.

    ````{note}
    To back up NetApp FAS volume using smtape, the volume should be put in restricted state for restore. To restrict a volume, do the following:
    ```{code-block} shell
    vol restrict /vol/vol1
    ```
    Since the primary volume cannot be restricted, you can use the **dump** method to back it up.
    ````

4. At the **Backup host** step, select a backup host and resource. The wizard goes to the next step automatically.
5. At the **Backup target** step, select a storage pool. Click **Next**.
6. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

7. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.
8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
9. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for NDMP:

- Common options

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|
| --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.|
```

- Advanced options

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Backup advanced options
---
class: longtable
---
|Option|Description|
| --- | --- |
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|
|NDMP envrionment variable|Specifies the environment variable for the backup process.|
```

## Restore

### Restore types

For different needs, ADPS provides the point-in-time restore for NDMP.

- Point-in-time restore

	When the folders or files are missing in the volume, you can perform a point-in-time restore job to restore the volume to a specified point in time. It can restore to the original or a new volume and can restore to the original or a specified path.

### Before you begin

To restore to a different host, install the agent on that host or register the resource, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the NDMP host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

    (1) In the **Restore source** section, select a point in time for the restore job.

    (2) In the **File** section, select a volume for the restore job.

    (3) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.
5. At the **Backup host** step, select a backup host and an IP address.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.
7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
9. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for NDMP:

- Common options:

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Restore common options
---
class: longtable
---
|Option |Description|
| --- | --- |
|Restore location|You can set the restore location to the original location or a specified location. To specify a location, enter the path manually or click Browse to select the target folder.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option |Description|
| --- | --- |
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is restored. The post-script is executed after the resource is restored.|
|NDMP envrionment variable|Specifies the environment variable for the restore process.|
```

## Glossary

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Glossary
---
class: longtable
---
|Term|Description|
| --- | --- |
|fast compression|A compression method that uses fast compression algorithms to compress data during the backup job.|
|restricted|The restricted state only allows operations such as parity rebuild, but does not allow data access.|
```