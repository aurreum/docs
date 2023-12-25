# User Guide for Hyper-V

## Overview

ADPS supports the backup and restore of Hyper-V virtual machines (VMs) running on Windows Servers or bare metal servers. The Hyper-V agent of ADPS should be installed on the Hyper-V server. This guide describes how to install the agent and how to use ADPS to back up and restore Hyper-V virtual machines.

The backup and restore features supported by ADPS include:

- Backup types

	Full backup, incremental backup

- Transport modes

   Network, SAN

- Backup sources

   Virtual machines, hosts

- Backup targets

	Standard storage pool, deduplication storage pool, tape library pool, object storage pool, LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, multiple channels, reconnection, speed limit, and replication

- Restore types

   Virtual machine restore, virtual disk restore

- Network protocols

   IPv4, IPv6

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore Hyper-V, first install the ADPS agent on the host where Hyper-V resides.

### Install the agent

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
7. At the **Select components** step, select **Hyper-V** from the component list. Click **Next**.
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

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Hyper-V, register the host, activate the Hyper-V backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where Hyper-V resides. Click the **Register** icon. After the registration, the **Activate** window appears. Click **Submit**.

	```{only} scutech
   ![](../images/Backup_Restore/DBackup3/Hyper-V/register1.png)
   ```

3. After you activate the license, the **Configure** window appears. In the **Configure** window, do the following:

	(1) In the **Name** field, enter a name for the host.

	(2) From the **Data network** list, select a network from those added on the **Network** page as the data network.

	(3) In the **Preferred egress network** field, enter an IP address for the preferred network traffic egress of the host's backup data. IPv4 and IPv6 are supported.

	(4) From the **User group** list, select user groups to authorize access to the resource.

	(5) Set the resource as **Protected** or not. The **Protected** resource cannot be the restore target or the data replication target unless the administrator removes the **Protected** label.

   ```{only} scutech
   ![](../images/Backup_Restore/DBackup3/Hyper-V/register3.png)
   ```

	```{note}
	1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
	2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Backup

### Backup types

ADPS provides full backup and incremental backup for Hyper-V.

- Full backup

	Backs up all the disks and disk files on virtual machines at some point in time.

- Incremental backup

	Backs up only the disks and disk files that have changed since the last backup (including full backup and incremental backup) with a full backup as the baseline.

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

### Before you begin

Before you back up and restore Hyper-V, check the following:

1. Check the resource

   (1) Click **Resource** > **Resource**. The **Resource** page appears.

   (2) Check whether the backup host and the Hyper-V resource are on the page with an *Online* state. If no, see [Activate licenses and authorize users](#activate-licenses-and-authorize-users).

2. Check storage pools

   (1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

   (2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the Hyper-V host and resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

   (1) From the **Backup type** list, select a backup type.

   (2) In the **Backup source** section, click the "+" icon. The **Add backup source** window appears. You can enter the virtual machine name in the search box for accurate search (keyword query is supported) or expand the backup source by clusters, hosts, resource pools, virtual machines, or folders. Select the virtual machines that you want to back up and click **Submit**.

   ```{note}
   You can use Ctrl and Shift to select multiple items. Confirm the range that you want, select the checkbox of the first item in the range, press Ctrl or Shift, and click the checkbox of the last item in the range. All the items between the first and the last are selected automatically.
   ```

   (3) Confirm the selected backup source. You can use the **Filter** to exclude virtual machines that you do not want to back up. Click **Next**.

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

ADPS provides the following backup options:

- Common options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup common options
---
class: longtable
---
|Feature|Description|
|---|-----|
|Compression|Fast is enabled by default. Backup data is compressed at the source side for transmission. It can reduce the backup time, improve backup efficiency, and save backup space.|
|Concurrent backup of VMs|The maximum number of virtual machines that can be backed up simultaneously. The default value is 8. We recommend that the value is no more than the number of CPU cores. If it exceeds, the efficiency will not be improved obviously.|
|Concurrent disks per VM|The maximum number of disks per VM that can be processed concurrently. Note that a high value will lead to a high VM IO usage.|
```

- Advanced options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup advanced options
---
class: longtable
---
|Feature|Description|
|---|-----|
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|
|Checkpoint consistency level|- Crash consistent: Creates application-consistent checkpoints that capture the current state of applications.{{ br }}- Application consistent: Uses the backup technology in the guest operating system to create data-consistent checkpoints that do not contain information about running applications.{{ br }}- Automatic: Application consistent is used by default. If it fails, Crash consistent will be used instead.|
```

## Restore

ADPS provides several restore types for Hyper-V, including:

- Virtual machine restore

   When a disaster occurs in the virtual machine, a virtual machine restore job can help you to restore the entire machine. It supports restoring to another virtual center, another host, the original path, and a new path.

- Virtual disk restore

   When a disaster occurs in some disks of the virtual machine, a virtual disk restore job can restore those disks to the target virtual machine. You can overwrite the existing disks, or create new disks.

### Before you begin

1. You have a completed backup job. See [Create a backup job](#create-a-backup-job).
2. To restore to another virtual center, deploy the backup host, register and activate that virtual center, and authorize user access to the resource.

```{note}
You can restore to a target host with the same version as or a higher version than the source host. If the target host has a lower version, the restore may fail.
```

### Create a virtual machine restore job

To create a virtual machine restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the Hyper-V host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual machine restore**.

   (3) In the **Restore source** section, expand the resource and select points in time under virtual machines for the restore. You can search the name or GUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   For the **Restore location** option:
   - Original path: The virtual machine will be restored to its original path with its original name and configuration.
   - New path: The virtual machine will be restored to a customized path with its configuration modified. If you click the icon beside **New path**, a wizard will appear. For more details, see [Restore to a new path](#restore-to-a-new-path).
   ```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

### Restore to a new path

When you select **New path** at the **Restore options** step, a wizard appears. Follow the wizard to set the new restore location.

To restore the virtual machine to a new path, do the following:

1. At the **VM** step, do the following:

   (1) Click the name in the **New VM name** column. The **Rename** window appears.

   (2) In the **Rename** window, set a new name, add a prefix, or add a suffix for the virtual machine. Click **Submit**.

   (3) The display area shows the new setting. Click **Next**. The wizard goes to the next step.

   ```{note}
   To rename a batch of virtual machines, select their checkboxes and click **Batch rename**.
   ```

2. At the **Datastore** step, do the following:

   (1) Click the value in the **Storage directory** column. The **Set up storage directory** window appears.

   (2) In the **Set up storage directory** window, select the target datastore for the virtual machine. Click **Submit**.

   (3) The display area shows the new setting. Click **Next**. The wizard goes to the next step.

   ```{note}
   To set the same storage directory for a batch of files, select their checkboxes and click **Set storage directories in batch**.
   ```

3. At the **Network** step, do the following:

   (1) Click the name in the **Virtual switch** column. The **Set up virtual switch** window appears.

   (2) In the **Set up virtual switch** window, select the target virtual switch. Click **Submit**.

   (3) The display area shows the new setting. Click **Next**. The wizard goes to the next step.

   ```{note}
   - You can click **Add network** beside the virtual machine and click **Configure** to select a virtual switch.
   - To set a batch of networks, select their checkboxes and click **Batch set up virtual switch**.
   ```

4. Click **Submit** and go back to the **Restore options** step.
5. You can click the **Set up** icon beside the **New path** to modify your previous settings.


### Create a virtual disk restore job

To create a virtual disk restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the Hyper-V host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual disk restore**.

   (3) In the **Restore source** section, expand the resource and select disks under points in time for the restore. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   For the **Restore location** option:
   - Original path: The virtual machine will be restored to its original path with its original name and configuration.
   - New path: The virtual machine will be restored to a customized path with its configuration modified. If you click the icon beside **New path**, a wizard will appear. Select a storage directory for the disk.
   ```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.


### Restore options

ADPS provides the following restore options:

- Common options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Restore common options
---
class: longtable
---
|Option|Description|
|---|-----|
|Restore location|See the respective job section for more details. |
|Concurrent recovery of VMs|The maximum number of virtual machines that can be restored simultaneously. The default value is 8. We recommend that the value is no more than the number of CPU cores. If it exceeds, the efficiency cannot be improved obviously.|
|Concurrent disks per VM|The maximum number of disks per VM that can be processed concurrently. Note that a high value will lead to a high VM IO usage.|
|Existing VMs|You can select Add timestamp suffix for new VMs, Overwrite existing VMs, or Skip the restore of existing VMs. If you select Skip the restore of existing VMs and all the VMs to be restored exist on the restore target, the restore job will fail.|
|Start virtual machine after restore|The restored virtual machines are automatically started after the successful restore job.|
|Restore the network adapter|You can restore the network adapter of the original virtual machine.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option|Description|
|---|-----|
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|
```


## Limitations

```{tabularcolumns} |\Y{0.15}|\Y{0.85}|
```
```{table} Limitations
---
class: longtable
---
| Feature | Limitations                                                  |
| -------- | ------------------------------------------------------------ |
| Backup   | Differential backup is not supported. {{ br }}VM grouping is not supported. {{ br }}If the VM to be backed up is overwritten and restored, the old backup jobs will be unavailable and should be recreated.{{ br }}Under the following conditions, the backup job cannot be stopped: The agent is creating checkpoints; or the agent is using a LAN-free pool and mounting the disks.{{ br }}Checkpoints include crash-consistent checkpoints and application-consistent checkpoints. The default checkpoint is application-consistent.{{ br }}On Windows Server 2016 and later versions, Hyper-V backup jobs will not reserve checkpoints after the job completion. {{ br }}A host can only run one backup or restore job at a time. A backup job can back up multiple VMs in parallel, while a restore job can only restore VMs one by one. {{ br }} Hyper-V LAN-free backup and restore only supports SAN transport mode.|
| Restore  | Mount recovery is not supported.{{ br }}The interface cannot display the restore progress of a single virtual disk. {{ br }}To overwrite and restore to the disk, ensure that the original host is shut down and no checkpoints remain.{{ br }}To restore between hosts with different versions, ADPS only supports restoring from a low version to a high version. {{ br }}When the agent is merging subdisks, the restore job cannot be stopped.{{ br }}ADPS cannot restore the Ubuntu/CentOS Generation 2 virtual machines on the Hyper-V 2012 series.{{ br }}ADPS cannot restore the virtual disks on Ubuntu/CentOS Generation 2 virtual machines. {{ br }}The Generation 1 virtual machines can only be booted from the IDE controller. The Generation 2 virtual machines can only be booted from the SCSI controller. |
```



