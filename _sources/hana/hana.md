# User Guide for SAP HANA

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore SAP HANA databases.

The backup and restore features supported by ADPS include:

- Backup sources

	Databases, transaction logs

- Backup types

	Full backup, incremental backup, cumulative incremental backup, and log backup (on demand)

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, and object storage service pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore

- Restore targets

	Original host, different host

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore SAP HANA, first install the ADPS agent on the host where SAP HANA resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where SAP HANA resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of SAP HANA single-tenant, multi-tenant, and cluster environments, including:

SAP HANA 1.00.112.00/1.00.112.03/1.00.112.21/1.00.122.00/2.00.000.00/2.00.030.00/2.00.045.00/2.00.056.00/2.00.062.00/2.00.070.00

### Install the agent

The ADPS agent can be installed on Linux and supports online and offline installation. We recommend online installation.

- Online installation:
	ADPS provides `curl` and `wget` commands for installation.
- Offline installation:
	See [Offline installation](../agent_install/agent_install.md#offline-installation) in Aurreum Data Protection Suite Agent Installation Guide.

To install the agent online, do the following:

1. Log in to the ADPS console.
2. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
3. From the toolbar, click the **Install agent** icon. The **Install agent** window appears.
4. In the **Install agent** window, do the following:

	(1) From the **Select system** list, select **Linux**.

	(2) From the **Component** list, select **SAP HANA**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

	```{code-block} python
    linux-hana:~ # curl -o- "http://192.168.20.106:50305/d2/update/script?modules=hana&location=http%3A%2F%2F192.168.20.106%3A50305&access_key=3ccaaff015c4401b9e0136e1cc1dbce9&rm=&tool=curl" | sh
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
    100  9206    0  9206    0     0  1023k      0 --:--:-- --:--:-- --:--:-- 1123k
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
     0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
    100 68.6M  100 68.6M    0     0  6848k      0  0:00:10  0:00:10 --:--:-- 7218k
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
     0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
    100 7884k  100 7884k    0     0  6394k      0  0:00:01  0:00:01 --:--:-- 7153k
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
     0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
    100 3919k  100 3919k    0     0  6700k      0 --:--:-- --:--:-- --:--:-- 7212k
	```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore SAP HANA, register the host, activate the SAP HANA backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host. Click the **Register** icon. After the registration, the **Activate** window appears. Click **Submit**.

	```{only} scutech
	![](../images/Common/hana_license.png)
	```

3. After you activate the license, the **Configure** window appears. In the **Configure** window, do the following:

	(1) In the **Name** field, enter a name for the host.

	(2) From the **Data network** list, select a network from those added on the **Network** page as the data network.

	(3) In the **Preferred egress network** field, enter an IP address for the preferred network traffic egress of the host's backup data. IPv4 and IPv6 are supported.

	(4) From the **User group** list, select user groups to authorize access to the resource.

	```{note}
	1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
	2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Backup

### Backup types

ADPS provides four backup types for SAP HANA.

- Log backup (on demand)

    When the transaction logs are full or the time reaches the database's log backup setting, the log backup (on demand) job will be performed automatically.

- Full backup

	Backs up one or more databases.

- Incremental backup

	Backs up only the data blocks that have changed since the last full backup or incremental backup.

- Cumulative incremental backup

	Backs up only the data blocks that have changed since the last full backup.

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
3. Perform a **cumulative incremental backup** among incremental backups (Example: on Wednesday). The restore job may use a full backup and the latest cumulative incremental backup to raise the restore speed.

### Before You Begin

Before you back up and restore SAP HANA, check the following:

1. Check the SAP HANA service status.

    Open the terminal and use the `sapcontrol -nr 00 -function GetProcessList` command to check the SAP HANA service status. The status should be "Running" for the backup and restore.

	```{code-block} python
	hdbadm@linux-hana:/usr/sap/HDB/HDB00> sapcontrol -nr 00 -function GetProcessList

    18.05.2023 14:00:18
    GetProcessList
    OK
    name, description, dispstatus, textstatus, starttime, elapsedtime, pid
    hdbdaemon, HDB Daemon, GREEN, Running, 2023 04 11 14:38:46, 887:21:32, 20277
    hdbcompileserver, HDB Compileserver, GREEN, Running, 2023 04 11 14:39:48, 887:20:30, 20478
    hdbnameserver, HDB Nameserver, GREEN, Running, 2023 04 11 14:38:47, 887:21:31, 20293
    hdbpreprocessor, HDB Preprocessor, GREEN, Running, 2023 04 11 14:39:48, 887:20:30, 20480
    hdbwebdispatcher, HDB Web Dispatcher, GREEN, Running, 2023 04 11 14:39:50, 887:20:28, 20515
	```

2. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

### Log in to the resource

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the host list, find the host where SAP HANA resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.
4. In the **Login** window, enter the **Username** and **Password** of SAP HANA, and click **Login**.
5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

```{note}
- Configure the Access Key of the user in `etc/default/adps-agent` for the normal backup and restore functions.
- After restoring SYSTEMDB to a different host, re-import the license into the restore target.
```

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host and SAP HANA instance. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

    (1) From the **Backup type** list, select a backup type.

    (2) In the **Backup source** section, select the volume that you want to back up.

4. At the **Backup target** step, select a storage pool. Click **Next**.
5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

    ```{note}
    The log backup (on demand) job does not have the **Backup schedule** step. Its schedule is based on the database's log backup setting.
    ```

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for SAP HANA:

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
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|
```

## Restore

ADPS provides the point-in-time restore job for SAP HANA. When a disaster occurs in a SAP HANA database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state.

### Before you begin

To restore to a different host, install the agent on that host or register the resource, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host and SAP HANA instance. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

    (1) From the **Database** list, select a database for the restore job.

    (2) In the **Restore point in time** field, drag the slider control to specify a restore point in time. The corresponding backup sets are displayed in the **Backup sets** field.

    (3) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.
	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.
6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for SAP HANA:

- Advanced options:

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option |Description|
| --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is restored. The post-script is executed after the resource is restored.|
```

## SAP HANA cluster backup and restore

ADPS supports the backup and restore for SAP HANA clusters. Before you back up and restore a cluster, bind the SAP HANA nodes into a cluster on the ADPS console.

### Before you begin

Deploy the environment of each node in the cluster according to [Install and configure the agent](#install-and-configure-the-agent) as well as [Activate licenses and authorize users](#activate-licenses-and-authorize-users).

### Bind nodes into a cluster

To bind nodes into a cluster on the console, do the following:

1. From the menu, click **Resource** > **Cluster**. The **Cluster** page appears.
2. From the toolbar, click the **+** icon. The **Cluster binding** window appears.
3. In the **Cluster binding** window, do the following:

    (1) In the **Name** field, enter a name for the cluster.

    (2) From the **Primary node** list, select the active node in the cluster.

    (3) From the **Type** list, select **Active-Passive**.

    (4) From the **Nodes** list, select the passive node in the cluster. You can click **x** to remove the selected node.

    (5) Click **Submit**.


### Backup and Restore

ADPS provides the same backup and restore types for SAP HANA clusters as a single instance.

- The **Backup** page displays only the active nodes in the SAP HANA cluster. Select an active node as the backup source. The remaining steps are similar to those in [Create a backup job](#create-a-backup-job).
- When a switchover occurs in the nodes of the SAP HANA cluster, the new active node will automatically take over the backup job.
- When creating a restore job, you can select a single instance or cluster as the restore target.

## Limitations

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Limitations
---
class: longtable
---
| Feature | Limitations                                                  |
| -------- | ------------------------------------------------------------ |
| Backup   | A log backup (on demand) job should be created before full backups.{{ br }}IPv6 cannot be the localhost address.{{ br }} LAN-free storage pools are not supported.{{ br }}After the restore, you must perform a full backup before incremental or cumulative incremental backups. Otherwise, the backup sets cannot be displayed. |
```