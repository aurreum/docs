# User Guide for VMware

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore VMware.

The backup and restore features supported by ADPS include:

- Backup sources

   Virtual machines (VMs), virtual disks, hosts, clusters, resource pools, folders, datastores, categories, and tags

- Backup types

	Full backup, incremental backup, cumulative incremental backup, and synthetic backup

- Backup targets

	Standard storage pool, deduplication storage pool, tape library pool, object storage pool, LAN-free pool, and data synthetic pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Transport modes

   NBD, NBDSSL, SAN, HotAdd, NAS

- Data processing

	Data compression, multiple channels, reconnection, speed limit, and replication

- Restore types

   Virtual machine restore, virtual disk restore, instant recovery, and virtual machine files restore


## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## VMware data transport

The data transport modes for VMware backups and restores include NBD, NBDSSL, SAN, HotAdd, NAS, and Adaptive. Before you back up VMs, set the network among the backup server, the storage server, the backup host, and the virtualization platform. This section explains VMware data transport modes.

### NBD & NBDSSL transport

During the backup, data flows over the LAN network from the virtualization platform to the backup host and then from the backup host to the storage server. SSL encryption (NBDSSL) is also supported. These transport modes are suitable for the protection of VMware with simple deployment, simple IP network, small production data, and low transport performance requirements. The backup host supports flexible deployment in the following ways:

- The backup host, backup server, and storage server can reside on the same host.
- The backup host can reside on a Linux server that has network connectivity with the backup server and storage server. The Linux server can be physical or virtual.

```{only} scutech
![](../images/Backup_Restore/DBackup3/VMware/vmware_NBD.png)
```

### SAN transport

In SAN transport mode, the backup host is required to read the disk storage of the virtual machine directly across a network on an FC/iSCSI SAN. During the backup, data flows from the backup host to the storage server directly over the LAN network. The LAN-free backup will be realized when the backup host and the storage server are deployed on the same host.

1. Limitations:

   The datastore of the virtual disk is constructed on LUNs and is mounted to the backup host through FC/iSCSI. The backup host collects the backup data directly without going over the LAN.

2. Backup host deployment:

   - The backup host can reside on the same host as the storage server so that the VMware datastore can be mounted to the storage server through FC/iSCSI.
   - Or you can mount the VMware datastore through FC/iSCSI to an independent backup host.

```{only} scutech
![](../images/Backup_Restore/DBackup3/VMware/vmware_san.png)
```

### HotAdd transport

The backup host is deployed on a virtual machine of the ESXi server that needs to be backed up. During the backup, the SCSI HotAdd feature of ESXi is used to mount the virtual disk to the backup host. Data flows from the backup host to the storage server. The deployment of a backup host has the following requirements:

- The backup host must be installed on a VMware virtual machine with an SCSI controller. This transport mode is not supported for the backup and restore of IDE disks.
- The virtual machine that needs to be backed up and restored should be on the same VMware data center as the backup host. For HotAdd backups, we recommend each ESXi has at least one HotAdd backup host.
- The ESXi Server where the backup host resides must have access to the datastore of the virtual machine that needs to be backed up.

```{only} scutech
![](../images/Backup_Restore/DBackup3/VMware/vmware_hotadd.png)
```

### NAS transport

In NAS transport mode, the backup host takes a snapshot of the virtual machine that needs to be backed up, mounts the NFS datastore to its local storage, and uses VDDK to directly read the disk files of the virtual machines.

1. Limitations:

   - The datastore where the virtual machine resides is a NFS datastore.
   - If the virtual machine that needs to be backed up has snapshots and its disks are larger than 2 TB, the NAS transport mode cannot be used for its backup.

2. Backup host deployment:

   - The backup host can access the NFS server of the NFS datastore and has the permissions on the export point for mounting the NFS datastore.

```{only} scutech
![](../images/Backup_Restore/DBackup3/VMware/vmware_nas.png)
```

### Adaptive transport

You can select the Adaptive mode to prioritize the transport modes manually.

## Configure the backup host

Before you back up and restore VMware, install the agent on the backup host and connect it to the backup server.

### Verify the compatibility

Ensure that your environment is in the following compatibility list:

```{table} Backup host compatibility
| Operating system     | OS bits |
| ----------------  | --------------------- |
| Ubuntu 16.04      | x86_64                |
| Ubuntu 18.04      | x86_64                |
| Ubuntu 20.04      | x86_64                |
| CentOS 7.4        | x86_64                |
| CentOS 7.6        | x86_64                |
| CentOS 7.9        | x86_64                |
| CentOS 8.5        | x86_64                |
| Red Hat 7.6       | x86_64                |
| Red Hat 7.9       | x86_64                |
| Red Hat 8.2       | x86_64                |
| Red Hat 8.6       | x86_64                |
| Red Hat 9.0       | x86_64                |
```

### Install the agent

ADPS agent can be installed on Linux and supports online and offline installation. We recommend online installation.

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

	(2) From the **Component** list, select **VMware**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the feature, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

   ```{only} scutech
   ![](../images/Backup_Restore/DBackup3/VMware/vmware_license04.png)
   ```

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation.
7. Wait for the installation to complete.

### Configure VDDK

VDDK is required for VMware backups and restores. To install VDDK on the backup host, do the following:

1. Upload `vddk.tar.gz` to the backup host and extract it under the `/opt/aurreum/adps/` directory.

   ```shell
   tar -zxvf vddk.tar.gz -C /opt/aurreum/adps/
   ```

2. When you create a backup and restore job, you can select Automatic or specify a VDDK version. If you select Automatic, ADPS will automatically select the VDDK version according to the vSphere version.
   - VDDK: 6.0/6.5/6.7/7.0/8.0

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The backup host with the agent installed appears on the page.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the backup host. Click the **Register** icon. Then the **Activate** window appears.
3. In the **Activate** window, select the resource and click **Submit**.
4. After you activate the license, the **Authorize** window appears. From the **User group** list, select user groups to authorize access to the resource. Click **Submit**.

## Add VMware

### Enable VMware

To enable the VMware features on the console, do the following:

1. Log in to the console as admin.
2. From the menu, click **Settings** > **Settings**. The **Settings** page appears.
3. On the **Resource** tab, select the **VMware** checkbox.

```{only} scutech
![](../images/Backup_Restore/DBackup3/VMware/vmware_function01.png)
```

### Register VMware

To add the VMware resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the toolbar, click the "+" icon and select **VMware vCenter or ESXi**. The **Add VMware vCenter or ESXi** window appears.
3. In the **Add VMware vCenter or ESXi** window, do the following:

   - In the **Name** field, enter a name for the resource.
   - In the **Address** field, enter the IP address of the VMware host, the IP address of the vCenter Server, or the domain name of the vCenter Server.
   - You can enable **SSL** for secure connection.
   - In the **Port** field, the default value is 443.
   - In the **Username** field, enter the username to log in to the VMware server.
   - In the **Password** field, enter the password of the user.
   - From the **Backup host** list, select a backup host.

4. After you add the VMware resource, see [Activate licenses and authorize users](#activate-licenses-and-authorize-users) to activate the resource and authorize user access to the resource.

   ```{note}
   You can enable the **Protected** option in the **Authorize** window to prevent the resource from overwriting.
   ```

### Manage VMware

After you add the VMware resource, you can manage it on the **Resource** page.

```{only} scutech
![](../images/Backup_Restore/DBackup3/VMware/vmware_function05.png)
```

- **Modify**: To change the name and network of the virtualization platform, click the **Modify** icon to edit the above information in the pop-up **Modify VMware** window.
- **Configure**: When the registered information of the virtualization platform is changed, you can click the **Configure** icon to update the information in the pop-up **Configure VMware** window.
- **Unregister**: If you do not want to back up and restore the VMware, click the **Unregister** icon to delete the resource.
- **Tag**: You can add a tag for the resource.

## Backup

### Backup types

ADPS provides three common backup types (full backup, incremental backup, and cumulative incremental backup) and one advanced (synthetic backup) for VMware.

- Full backup

	Backs up all the disks and configuration files on virtual machines at some point in time.

- Incremental backup

	Backs up only the disks and configuration files that have changed since the last backup (including the full backup, the incremental backup, and the cumulative incremental backup) with a full backup as the baseline.

- Cumulative incremental backup

	Backs up only the disks and configuration files that have changed since the last full backup with a full backup as the baseline.

- Synthetic backup

	The first synthetic backup is a full backup and the subsequent ones are incremental. When the synthesis condition is reached, the latest full backup and subsequent incremental backup will be synthesized to create a new full backup copy. Synthetic backup can improve the restore performance. You can directly mount the full backup copy to the target host through an instant recovery job without physical copies and additional storage space.

	Synthetic backups have the following requirements:

	(1) You have the advanced licenses: VMware Synthetic Backup and VMware Copy Data Management.

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
2. Perform an **incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.
3. Perform a **cumulative incremental backup** among incremental backups (Example: on Wednesday). The restore job may use a full backup and the latest cumulative incremental backup to raise the restore speed.

To use the advanced synthetic backup, we recommend the following backup policy:

- Perform a **synthetic backup** every day to ensure that you have a recoverable point in time every day.

### Before you begin

Before you back up and restore VMware, check the following:

1. Check the resource

   (1) Click **Resource** > **Resource**. The **Resource** page appears.

   (2) Check whether the backup host and the VMware resource are on the page with an *Online* state. If no, see [Activate licenses and authorize users](#activate-licenses-and-authorize-users).

2. Check storage pools

   (1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

   (2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

   ```{note}
	To use synthetic backups, ensure that you have created a data synthetic pool for the current user.
	```

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the VMware host and resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

   (1) From the **Backup type** list, select a backup type.

   (2) In the **Backup source** section, click the "+" icon. The **Add backup source** window appears. You can enter the virtual machine name in the search box for accurate search (keyword query is supported) or expand the backup source by clusters, hosts, resource pools, virtual machines, or folders. Select the virtual machines that you want to back up and click **Submit**.

   ```{note}
   You can use Ctrl and Shift to select multiple items. Confirm the range that you want, select the checkbox of the first item in the range, press Ctrl or Shift, and click the checkbox of the last item in the range. All the items between the first and the last are selected automatically.
   ```

   (3) Confirm the selected backup source. You can use the **Filter** to exclude virtual machines that you do not want to back up. Click **Next**.

4. At the **Backup host** step, select a backup host based on your data transport mode. For details, see [VMware data transport](#vmware-data-transport). Click **Next**.
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

ADPS provides the following backup options:

- Common options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|
|---|-----|
|Compression|Fast is enabled by default. Backup data is compressed at the source side for transmission. It can reduce the backup time, improve backup efficiency, and save backup space.|
|Concurrent backup of VMs|The maximum number of virtual machines that can be backed up simultaneously. The default value is 8. We recommend that the value is no more than the number of CPU cores. If it exceeds, the efficiency will not be improved obviously.|
|Number of virtual machines per host backup in parallel|The maximum number of virtual machines per host to be backed up simultaneously. The default value is 5.|
|Number of virtual disks per virtual machine backup in parallel|The maximum number of disks per virtual machine to be backed up simultaneously. The default value is 1.|
|Data transport mode|You can select NBD, NBDSSL, SAN, HotADD, NAS, or Adaptive. If you select Adaptive, you can set the priority of transport modes. For details, see [VMware data transport](#vmware-data-transport). |
|VDDK version|You can select Automatic or specify a VDDK version. 6.0, 6.5, 6.7, and 7.0 are supported. If you enable Automatic, ADPS will automatically select the VDDK version according to the vSphere version.|
|Enable CBT|Enabled by default. CBT should be enabled for virtual machine incremental backups. It supports backing up allocated blocks in a virtualization environment with an old version (such as ESXi 5.X). If you are not allowed to enable CBT for special reasons, deselect the checkbox. However, disabling CBT may have potential risks such as the failure of allocated block backups and incremental backups.|
|Reset CBT|Resets the CBT of the virtual machine before full backups. If the CBT becomes invalid, the backup host will be unable to get the data blocks that have changed, which will affect incremental backups. This option can solve the above problem.|
|Backup allocated blocks only|Backs up only the allocated disk blocks.{{ br }}Allocated data chunk size: This parameter applies to VDDK >= 6.7 and ESXi >= 6.0. Increasing the chunk size can speed up getting valid data, but will back up extra data. |
|Backup independent disks in persistent mode|Enabled by default. It supports the backup of independent disks in persistent mode on virtual machines, but the virtual machines should be shut down. You can shut down virtual machines manually or you can enable the **Shutdown virtual machine during backup** option.|
|Backup RDM disks in physical compatibility mode|Supports the backup of physical RDM disks on the virtual machine. However, the virtual machines should be shut down. You can shut down virtual machines manually or enable the **Shutdown virtual machine during backup** option.|
|Shutdown virtual machine during backup|Disabled by default. You can enable this option when the virtual machine must be shut down for backups. Otherwise we do not recommend enabling it, which may affect the normal running of the virtual machine business.|
|Do not perform backup when|Before taking a snapshot of a virtual machine, the job will check whether the remaining space meets all the specified conditions.|
```

- Advanced options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup advanced options
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
|NBD/NBDSSL transport compression algorithm|You can use zlib, fastlz, skipz, or none for the NBD or NBDSSL transport mode. Different compression algorithms may lead to different backup efficiency.|
|VDDK read buffer size|Defines the data block size that VDDK reads each time. The default size is 1 MiB. Values include 64 KiB, 128 KIB, …, and 64 MiB.|
|VDDK transport log level|Includes Panic, Error, Warning, Audit, Info, Verbose, and Trivia. VDDK transport logs above the Audit level are printed to `agent.log` by default.|
|NFC operations log level|Includes Quiet, Error, Warning, Info, Debug. Under the NBD/NBDSSL transport mode, NFC (network file copy) operations logs above the Error level are printed to `agent.log` by default.|
|Snapshot|Includes Automatic, Quiesced Snapshot, and Normal Snapshot. Automatic is selected by default, which will create a quiesced snapshot first to ensure data consistency in file systems. If it fails to create a quiesced snapshot, it will retry to create a normal snapshot.|
|Backup retry|- Backup retry count: Refers to the number of attempts to run the backup job if it fails to back up some virtual machines. Only the failed virtual machines are processed during a job retry. 0 means no retries. {{ br }}- Backup retry interval: Refers to the time interval between job retries if it fails to back up some virtual machines. Only the failed virtual machines are processed during a job retry.{{ br }}Manually stopping the backup job will not initiate the retry.|
```

## Restore

ADPS provides several restore types for VMware, including:

- Virtual machine restore

   When a disaster occurs in the virtual machine, a virtual machine restore job can help you to restore the entire machine. It supports restoring to another virtual center, another host, the original path, and a new path.

- Virtual disk restore

   When a disaster occurs in some disks of the virtual machine, a virtual disk restore job can restore those disks to the target virtual machine. You can overwrite the existing disks, or create new disks.

- Virtual machine files restore

   The job can restore `vmx`, `vmdk`, `nvram`, and `dsd` files to the VMware datastore or the backup host's local directory.

- Instant recovery

   The job can mount the virtual machine backup sets in the storage server to achieve fast recovery. It has the following advantages: fast recovery speed, low resource consumption, saving disk space, and improving the availability of backup sets.

### Before you begin

1. You have a completed backup job. See [Create a backup job](#create-a-backup-job).
2. To restore to another virtual center, deploy the backup host, register and activate that virtual center, and authorize user access to the resource.

```{note}
When restoring a virtual machine from one virtual center to another, you can restore the VM to the original VM hardware version or the target ESXi/ESX version. If the target ESXi/ESX host version is not new enough to support the original VM hardware version, the restore may fail. You can disable the **Retain virtual machine version** option to restore the VM to the ESXi/ESX version.
```

### Create a virtual machine restore job

To create a virtual machine restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the VMware host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual machine restore**.

   (3) In the **Restore source** section, expand the resource and select points in time under virtual machines for the restore. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Backup host** step, select a backup host based on your data transport mode. For details, see [VMware data transport](#vmware-data-transport). Click **Next**.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   For the **Restore location** option:
   - Original path: The virtual machine will be restored to its original path with its original name and configuration.
   - New path: The virtual machine will be restored to a customized path with its configuration modified. If you click the icon beside **New path**, a wizard will appear. For more details, see [Restore to a new path](#restore-to-a-new-path).
   ```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.


### Create a virtual disk restore job

To create a virtual disk restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the VMware host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual disk restore**.

   (3) In the **Restore source** section, expand the resource and select disks under points in time for the restore. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Backup host** step, select a backup host based on your data transport mode. For details, see [VMware data transport](#vmware-data-transport). Click **Next**.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   For the **Restore location** setting, see [Set the restore location](#set-the-restore-location).
   ```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

#### Set the restore location

To set the restore location for the virtual disk, do the following:

1. In the **Restore location** section, click the expand arrow icon to display the options. The **Disk** option shows the disk name that will be restored.

2. To change the target virtual machine, click the host name with a dashed line and an edit icon. In the **Target VM** window, select a virtual machine as the target. Click **Submit**.

3. From the **Recovery mode** list, do the following:

   - To overwrite the virtual disk with the same UUID, select **Overwrite**.
   - To create a new virtual disk, select **New** and set the **File location**, **Disk provisioning**, and **Controller position**.

### Create an instant recovery job

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the VMware host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Instant recovery**.

   (3) In the **Restore source** section, expand the resource and select points in time under virtual machines for the restore. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Backup host** step, select a backup host based on your data transport mode. For details, see [VMware data transport](#vmware-data-transport). Click **Next**.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   For the **Restore location** option:
   - Original path: The virtual machine will be restored to its original path with its original name and configuration.
   - New path: The virtual machine will be restored to a customized path with its configuration modified. If you click the icon beside **New path**, a wizard will appear. For more details, see [Restore to a new path](#restore-to-a-new-path).
   ```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

### Create a virtual machine files restore job

To create a virtual machine files restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the VMware host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual machine files restore**.

   (3) In the **Restore source** section, expand the resource and select virtual machine files under the point in time. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Backup host** step, select a backup host based on your data transport mode. For details, see [VMware data transport](#vmware-data-transport). Click **Next**.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   The files can be restored to the datastore or the backup host.

   - To restore to the datastore, select **Datastore** from the **Restore target** list and click the **Browse...** button to select a file location.
   - To restore to the backup host, select **Backup host** from the **Restore target** list and enter a directory on the backup host as the file location.
   ```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

### Restore to a new path

When you select **New path** at the **Restore options** step, a wizard appears. Follow the wizard to set the new restore location.

To restore the virtual machine to a new path, do the following:

1. At the **Host/Cluster** step, do the following:

   (1) Click **Set up**. The **Set up host/cluster** window appears.

   (2) In the **Set up host/cluster** window, select the target host/cluster. Click **Submit**.

   (3) The display area shows the new setting. Click **Submit**. The wizard goes to the next step.

   To set the same host/cluster for a batch of virtual machines, select their checkboxes and click **Batch set up host/cluster**.

2. At the **Resource pool/vApp** step, do the following:

   (1) Click **Resources**. The **Set up resource pool/vApp** window appears.

   (2) In the **Set up resource pool/vApp** window, select the target resource pool/vApp. Click **Submit**.

   (3) The display area shows the new setting. Click **Next**. The wizard goes to the next step.

   To set the same resource pool/vApp for a batch of virtual machines, select their checkboxes and click **Batch set up resource pool/vApp**.

3. At the **Folder** step, do the following:

   (1) Click the name in the **New VM name** column. The **Rename** window appears.

   (2) In the **Rename** window, set a new name, add a prefix, or add a suffix for the virtual machine. Click **Submit**.

   (3) The display area shows the new setting. Click the name in the **Folder** column. The **Set up folder** window appears.

   (4) In the **Set up folder** window, select the target folder. Click **Submit**.

   (5) The display area shows the new settings. Click **Next**. The wizard goes to the next step.

   To rename a batch of virtual machines or set the same folder for them, select their checkboxes, click **Batch rename** or **Batch set up folder**.

4. At the **Datastore** step, do the following:

   (1) Click the value in the **Datastore** column. The **Set up datastore** window appears.

   (2) In the **Set up datastore** window, select the target datastore for the virtual machine. Click **Submit**.

   (3) The display area shows the new setting. Click the value in the **Disk provisioning** column. The **Set up disk provisioning** window appears.

   (4) In the **Set up disk provisioning** window, select **Thin Provision**, **Thick Provision Lazy Zeroed**, or **Thick Provision Eager Zeroed** from the **Disk provisioning** list. Click **Submit**.

   (5) The display area shows the new settings. Click **Next**. The wizard goes to the next step.

   To set the same datastore or disk provisioning for a batch of virtual machines, select their checkboxes, click **Batch set up datastore** or **Batch set up disk provisioning**.

5. At the **Redirect snapshot** step, if you want to redirect the snapshot, do the following:

   ```{note}
   This step is only available for instant recovery jobs. A snapshot of the virtual machine will be taken and stored in the specified datastore to improve the read/write performance of the virtual machine.
   ```

   (1) Select the **Redirect snapshot** checkbox.

   (2) Click the value in the **Datastore** column. The **Set up redirect snapshot** window appears.

   (3) In the **Set up redirect snapshot** window, select the target datastore. Click **Submit**.

   (4) The display area shows the new setting. Click **Next**. The wizard goes to the next step.

   To set the same datastore for a batch of virtual machines, select their checkboxes and click **Batch set up network**.

6. At the **Network** step, do the following:

   (1) Click the name in the **Target network** column. The **Set up network** window appears.

   (2) In the **Set up network** window, select the target network. Click **Submit**.

   (3) The display area shows the new setting. Click **Next**. The wizard goes to the next step.

   To set a batch of virtual machines with the same network, select their checkboxes and click **Batch set up network**.

7. At the **Advanced** step, do the following:

   (1) Click the value in the **Number of cores** or **CPU cores per socket** column. The **Set up CPU** window appears.

   (2) In the **Set up CPU** window, set the number of cores and CPU cores per socket for the virtual machine. Click **Submit**.

   (3) The display area shows the new settings. Click the value in the **Memory** column. The **Set up memory** window appears.

   (4) In the **Set up memory** window, set the memory size. Click **Submit**.

   (5) The display area shows the new settings.

   To set the CPU and memory for a batch of virtual machines, select their checkboxes, click **Batch set up CPU** or **Batch set up memory**.

8. Click **Submit** and go back to the **Restore options** step.
9. You can click the **Set up** icon beside the **New path** to modify your previous settings.


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
|Number of virtual machines per host restore in parallel|The maximum number of virtual machines per host that can be restored simultaneously. The default value is 5.|
|Data transport mode|You can select NBD, NBDSSL, SAN, HotADD, NAS, or Adaptive. If you select Adaptive, you can set the priority of transport modes. For details, see [VMware data transport](#vmware-data-transport). |
|VDDK version|You can select Automatic or specify a VDDK version. 6.0, 6.5, 6.7, and 7.0 are supported. If you enable Automatic, ADPS will automatically select the VDDK version according to the vSphere version.|
|Existing VMs|You can select Add timestamp suffix for new VMs, Overwrite existing VMs, Unregister existing VMs, or Skip the restore of existing VMs. If you select Skip the restore of existing VMs and all the VMs to be restored exist on the restore target, the restore job will fail.|
|Start virtual machine after restore|The restored virtual machines are automatically started after the successful restore job.|
|Restore the CD/DVD drive|You can enable this option to restore the CD/DVD drive of the original virtual machine.|
|Retain the virtual machine version|This option is enabled by default. The virtual machine will be restored to the original VM hardware version. If you disable it, the virtual machine will be restored to the target host version. This option applies to a target host with an old version that does not support the hardware version of the original virtual machine.|
|Retain the virtual machine BIOS UUID|You can retain the BIOS UUID of the original virtual machine.|
|Retain the virtual machine Instance UUID|You can retain the Instance UUID of the original virtual machine.|
|Restore the network adapter|You can restore the network adapter of the original virtual machine.|
|Retain the MAC address|You can retain the MAC address of the original virtual machine.|
|Restore virtual machine tags|For vCenter 6.5 and above, you can retain the tags of the original VMs after restore.|
|Restore virtual machine DataSet|The ESXi host must be at ESXi 8.0 or later, and virtual machines must be of hardware version 20 or later.|
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
|NBD/NBDSSL transport compression algorithm|You can use zlib, fastlz, skipz, or none for the NBD or NBDSSL transport mode. Different compression algorithms may lead to different backup efficiency.|
|VDDK write buffer size|Defines the data block size that VDDK writes each time. The default size is 1 MiB. Values include 64 KiB, 128 KIB, …, and 64 MiB.|
|VDDK transport log level|Includes Panic, Error, Warning, Audit, Info, Verbose, and Trivia. VDDK transport logs above the Audit level are printed to `agent.log` by default.|
|NFC operations log level|Includes Quiet, Error, Warning, Info, Debug. Under the NBD/NBDSSL transport mode, NFC (network file copy) operations logs above the Error level are printed to `agent.log` by default.|
|Restore template to virtual machine|If the backup source is a virtual machine template and the restore target is vCenter, the template will be restored. If the restore target is ESXi, the template will be automatically restored to a virtual machine because ESXi does not support templates.|
|Use NFSv4|Only available for instant recovery jobs. If it is disabled, NFSv3 will be used by default.|
|Restore retry count|Number of attempts to run the restore job if it fails to restore some VMs. Only the failed VMs are processed during a job retry. 0 means no retries. Only available for instant recovery jobs. |
|Restore retry interval|Time interval between job retries if it fails to restore some VMs. Only the failed VMs are processed during a job retry. Only available for instant recovery jobs. |
```

## Copy data management

On the CDM page, you can manage the copies generated by instant recovery and synthetic backup jobs, including viewing, cloning, unmounting, and deleting copies.

### Unmount a copy

You can click the **Unmount** icon to unmount the mounted copies created after the instant recovery job.

To unmount a copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select a mounted copy. The **Unmount** icon appears on the right of the copy.
4. Click the **Unmount** icon. A confirmation window appears.
5. Confirm the warning and enter the verification code. Click **OK**.
6. After the unmounting, you can see no such mounted copy record under the full copy.


### Remount an unmounted copy

You can click the **Mount** icon to remount an unmounted copy.

To remount an unmounted copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select an unmounted copy. The **Mount** icon appears on the right of the copy.
4. Click the **Mount** icon. The job wizard appears.
5. Finish the configuration. See [Create an instant recovery job](#create-an-instant-recovery-job).
6. After the job, the unmounted copy becomes mounted again.

### Delete an unmounted copy

You can click the **Delete** icon to delete an unmounted copy if you confirm that the copy is no longer needed.

To delete an unmounted copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select an unmounted copy. The **Delete** icon appears on the right of the copy.
4. Click the **Delete** icon. A confirmation window appears.
5. Confirm the warning and enter the verification code. Click **OK**.
6. After the operation, you can see no such copy record under the full copy.

## Limitations

```{tabularcolumns} |\Y{0.15}|\Y{0.85}|
```
```{table} Limitations
---
class: longtable
---
| Feature  | Limitations  |
|--------------------|-------------------------------|
|Backup|1. Synthetic backup is not supported. {{ br }}2. Automatic selection logic for VDDK version: If the vSphere version is older than or equal to 6.0, VDDK 6.0 will be selected. For other vSphere versions, the corresponding VDDK version will be selected. For example, if the vSphere version is 6.5, VDDK 6.5 will be selected automatically. {{ br }}VDDK does not support ARM architecture.{{ br }}3. VDDK does not support the ARM architecture.{{ br }}4. In SAN mode backup, the agent must be able to access the FC or iSCSI SAN where the ESXi virtual machine storage resides. {{ br }}5. VVol datastore is not supported.{{ br }}6. Due to the limitaions of VDDK 6.5, the agent program may crash when the HotAdd transport mode is used to back up virtual machines in parallel. {{ br }}7. VDDK 7.0.3 and VDDK 8.0.0 do not support independent mode disks and, therefore, do not support the backup of independent mode disks.{{ br }}8. Valid data cannot be obtained from the disks with the NFS data storage and the disks can only be backed up in full.{{ br }}9. If the virtual machine to be backed up already has a snapshot, it may lead to the failure of CBT getting allocated blocks. It is not recommended that virtual machines have snapshots before backup. {{ br }}10. Storage multipathing is not supported in SAN mode.{{ br }}11. Virtual machines whose disk type is thick provisioned eager zeroed do not support the **Backup allocated blocks only** option. The option for incremental and cumulative incremental backups requires that the disks be thin provisioned and the VDDK version must be 6.7 or higher.{{ br }}12. It is not recommended to perform regular backups and synthetic backups for the same virtual machine. If you must perform both backups, uncheck their "Reset CBT" option. Otherwise they will affect each other.{{ br }}13. Soft shutdown of a virtual machine requires VMware Tools to be installed and running normally. If the Guest OS fails to shut down, or the shutdown fails after the waiting time, the virtual machine will be forcibly powered off.|
|Environment|1. HotAdd transport requirements: The virtual machine agent must have a SCSI controller. IDE disks are not supported. The virtual machine agent can access the datastore of the target virtual machine disk and connect to the 902 port of ESXi. {{ br }}2. NAS transport mode requires that the virtual machine resides on an NFS datastore, the backup host can access the NFS server of the NFS datastore, and the backup host has permissions on the export point to mount the NFS datastore. When a virtual machine already has a snapshot and contains a disk larger than or equal to 2TB, you cannot use NAS transport mode to back up and restore the virtual machines.|
|Restore|1. File-level Restore and Mount Restore of Data are not supported. {{ br }}For versions older than vSphere 5.5, the size of the virtual disk being restored through the SAN transport mode must be an integer multiple of the underlying VMFS block size. {{ br }}2. ESXi 5.x does not support the AHCI controller. Only the virtual disk restore job can be used across versions.{{ br }}3. Instant recovery requires checking and shutting down the system NFS service.{{ br }}4. Before you delete a synthetic backup point of a virtual machine, confirm whether a copy of the virtual machine exists. If it exists, uninstall and delete the copy before deleting the synthetic backup point.{{ br }}5. Before you remount the copy, ensure that ESXi and vCenter do not have NFS datastores with the same name.{{ br }}6. Virtual machine files restore only supports restoring vmx, vmdk (description files), nvram, and dsd files. It is not compatible with older versions of backup sets.|
```

## Glossary

```{tabularcolumns} |\Y{0.2}|\Y{0.8}|
```
```{table} Glossary
---
class: longtable
---
| Term | Description  |
|--------------------|-------------------------------|
| CBT                                 | CBT is the abbreviation of Changed Block Tracking. When CBT is enabled, the backup host can collect the data blocks that have changed since the last backup. |
| snapshot type                       | Snapshot types include quiesced snapshot and normal snapshot. {{ br }}Quiesced snapshot: Before the virtual machine takes a snapshot, it will write the data from the memory to the disk, quiesce the file system, and take a snapshot. {{ br }}Normal snapshot: Normal snapshot will take a snapshot of the memory and create a file to record the content of the memory at the creation time of the snapshot. When Quiesced Snapshot is selected and it fails to create a quiesced snapshot, the snapshot type will automatically change to Normal Snapshot. |
| independent disk in persistent mode | Disks in persistent mode behave like regular disks on a physical device. All data written to the disk in persistent mode is written to it permanently. |
| RDM disk                            | RDM is the abbreviation of Raw Device Mapping. It has two compatibility modes: {{ br }}(1) Physical compatibility mode: In this mode, the LUN of physical storage can be identified and used by virtual machines. Virtual machines can directly read and write the LUN. However, the space cannot be cloned or used as a template. The file name is generally xxxx-rdmp.vdmk{{ br }}(2) Virtual compatibility mode: In this mode, the LUN of physical storage from the view of virtual machines is a virtual disk. It can be used as a snapshot and template. And it can be migrated, etc. |
| thin provision                      | Thin disks use thin provisioning to optimize the space usage in the SAN. They only consume the capacity required for running. As guest operating systems allocate more I/O to thin disks, the space required grows over time. For example, if you create an 8 GB thin disk, the disk only consumes 1 GB initially and up to 8 GB as it grows. |
| thick provision lazy zeroed         | A thick-provisioned lazy-zeroed disk allocates all its disk space at the time of its creation. Note that thick-provisioned lazy-zeroed disks may contain old data from physical media. It must be zeroed out on command before new data can be written to the disk blocks. The delayed-zeroed disk can be created quickly, but its performance typically degrades due to increased IOPS. |
| thick provision eager zeroed        | Thick-provisioned zeroed disks create VM disks in the default thick format and allocate the maximum required disk space at the time of their creation. The allocated disk space does not contain any previous data on physical media. Thick Provision Zeroed supports VMware Fault Tolerance. During the creation, the data from physical media will be zeroed out. Therefore, creating this type of disk may take longer than creating other disks. Thick disks use thick provisioning to pre-allocate physical storage after the disks are created. If you create a 50 GB virtual disk, the disk will consume exactly 50 GB of physical space. Then no other disk or VM can use that disk space. |
| virtual machine template            | A template is a primary copy of a virtual machine that can be used to deploy virtual machines in batches. It cannot be powered on or connected to any resource pool. |
```

## VMware backup and restore account privileges

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} VMware backup and restore account privileges
---
class: longtable
---
|Object            |Privileges            |
|-------------------|---------------------------|
|Global|Enable methods{{ br }}Disable methods|
|Virtual machine.Guest operations|Guest operation modifications{{ br }}Guest operation program execution{{ br }}Guest operation queries|
|Virtual machine.Inventory|Create{{ br }}Move{{ br }}Register{{ br }}Remove{{ br }}Unregister |
|Virtual machine.Snapshot management|Create snapshot{{ br }}Remove snapshot{{ br }}Revert to snapshot |
|Virtual machine.Configuration|Acquire disk lease{{ br }}Add existing disk{{ br }}Add new disk{{ br }}Add or remove device{{ br }}Advanced configuration{{ br }}Change CPU count{{ br }}Change Memory{{ br }}Configure Raw device{{ br }}Change resource{{ br }}Change Settings{{ br }}Reload from path{{ br }}Remove disk{{ br }}Set annotation{{ br }}Toggle disk change tracking |
|Virtual machine.Interaction|Configure CD media{{ br }}Connect devices{{ br }}Configure floppy media{{ br }}Guest operating system management by VIX API{{ br }}Power on{{ br }}Power off|
|Virtual machine.Provisioning|Allow disk access{{ br }}Allow file access{{ br }}Allow read-only disk access{{ br }}Allow virtual machine download{{ br }}Create template from virtual machine{{ br }}Mark as template{{ br }}Mark as virtual machine|
|Datastore|Allocate space{{ br }}Browse datastore{{ br }}Configure datastore{{ br }}Low level file operations{{ br }}Remove datastore{{ br }}Remove file{{ br }}Update virtual machine files{{ br }}Update virtual machine metadata |
|Resource|Assign virtual machine to resource pool|
|vApp|Add virtual machine|
|Network|Assign network|
```