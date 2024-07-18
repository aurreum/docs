# User Guide for Windows OS

## Overview

This guide introduces how to install and configure the ADPS agent and how to properly use ADPS to back up and restore Windows OS.

The backup and restore features supported by ADPS include:

- Backup sources

	Single or multiple disks

- Backup types

	Full backup

- Backup targets

	Standard storage pool, deduplication storage pool, tape library pool, and object storage service pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore (OS-level)

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

To back up and restore Windows OS, first install the ADPS agent on the host where Windows OS resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where Windows OS resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of Windows OS. Supported Windows OS include:

- Windows 7/8/8.1/10/11/2003/2003 R2/2008/2008 R2/2012/2012 R2/2016/2019/2022

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
7. At the **Select components** step, select **OS** from the component list. Click **Next**.
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

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Windows OS, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. On the **Resource** page, select the host where Windows OS resides. Click the **Register** icon. After the registration, the **Activate** window appears.

3. In the **Activate** window, select the Windows OS backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup types

ADPS provides full backup for Windows OS.

- Full backup

	Backs up the disk partition data of the operating system.

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

### Before you begin

Before you back up and restore Windows OS, check the following:

1. Check the host status.
2. Check the resuorce status. On the **Resource** page, the host and Windows OS should be **Online**.
3. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

### Log in to the instance

Before you create a backup or restore job, log in to the Windows OS instance on the console. ADPS provides two authentication methods for Windows OS: 

- OS authentication

	Use a Windows OS user to log in the resource.

- Access key

	You can use the access key of the current ADPS user to log in to the resource. This method is suitable for the following scenarios:

	- You cannot get the OS user's username and password.
	- The user's password changes frequently.

	```{note}
	1. Access key authentication is not enabled by default. To enable this feature, log in to the ADPS console, go to **Settings**, open the **Security** tab, and select the **Access key login instance** checkbox.
	2. To get the access key, log in to the console, click **Personal settings** > **Account settings** on the upper right corner, find **Access key** on the **Preferences** tab, and click **View**.
	```

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the host list, find the host where the Windows OS resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Common/file_login.png)
	```

4. In the **Login** window, select an authentication method according to your needs.

	- Select **OS authentication**, enter the **Username** and **Password** of the OS user, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.


## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where Windows OS resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the disk partitions that you want to back up.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Windows_OS/windows-os_backup01.png)
	```

4. At the **Backup target** step, select a storage pool. Click **Next**.
5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Windows_OS/windows-os_backup02.png)
	```
   
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for Windows OS:

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
| Backup allocated blocks only | Only the actual occupied storage data on the disk partition will be backed up each time a job is performed. | --- |
| Block size to read from the block device |The smallest unit of storage space allocated to the file system by the operating system. 64KiB, 128KiB, 256KiB, 512KiB, 1MiB, or 2MiB can be selected. Generally, the larger the block size of the read block device, the faster the backup speed will be. | --- |
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
|Shadow copy provider|File share shadow and software shadow are provided.||
```

## Restore

### Restore types

ADPS provides an OS-level point-in-time restore for Windows OS.

### Configure a restore target

Configure a restore target first before creating an OS-level point-in-time restore job. To configure a restore target, do the following:

1. Select an ISO file and use a burning software to burn the ISO file to a media such as CD or DVD.

- For Windows 2003/Windows 2003R2 data sets, select adps_bootable_recovery-version~nt52.amd64.iso
- For Windows 2003/Windows 2003R2, select adps_bootable_recovery-version~nt.amd64.iso

2. Insert the recovery media into the restore target, and boot the restore target from the disc. The WinPE configuration window appears.

```{note}
For virtual machines in Hyper-V, it is recommended to have memory larger than 700M, otherwise the virtual machine may fail to start after the restore job is complete.
```

3.  Click **Backup server** and set **Server IP** and **Server port**. The default server port is 50305. If **SSL** is enabled, the server port is 60305. 

```{note}
**Access key** is optional. When the backup server is a multi-tenant enviroment, configuring a tennat's access key is required.
```

4.  Click **Apply** and **Start**. Then the restore target can communicate with the backup server. The restore target is a WinPE host now.
5.  If the WinPE host fails to acquire a dynamic IPv4 address, you can click **IPv4** in the WinPE configuration window and manually configure a static IPv4 address.

```{note}
- Click **Driver tools** > **IP configuration - Show** to check the network status.
- If a network connection error appears, it may be due to a missing NIC driver. You can manually add the NIC driver. 
- To add the NIC driver by WinPE configuration windows, do the following:

	(1) Mount the corresponding NIC driver to the restore machine using a USB flash drive.
	
	(2) In the WinPE configuration window, click **Driver tools** > **Command prompt - Open**. The command prompt appears.
	
	(3) On the command prompt, enter the corresponding driver directory, then use the command `drvload vmxnet.inf (driver name)` to add the driver.
```
6. If the network type is IPv6, click **IPv6** to configure the network.

7. Go to ADPS console, and click **Resource**. The **Resource** page appears. On the **Resource** page, the WinPE host appears. Before you create a restore job, register the WinPE host, activate the backup license, and authorize users.
8. After the successful authorization, you can [create a Windows OS point-in-time restore job](#create-a-windows-os-point-in-time-restore-job).
9. After a restore job is executed successfully, click **Driver tools** > **Windows system - Reboot** in the the WinPE configuration window to reboot the system. 

```{note}
- After a restore job is executed successfully, if the WinPE host cannot start or the WinPE configuration window cannot recognize the system driver, you can manually add the driver in the window or from the command prompt. Before loading the system drivers, you need to copy the corresponding NIC drivers to another device (such as a USB drive or optical disc), insert the device to the WinPE host, and then load the system drivers. After the system driver loading is complete, connect the WinPE host to the agent and perform a point-in-time restore job.

To add the drivers, select one of the methods:

- In the WinPE configuration window, click **Driver tools** > **Driver update**, select **Driver folder** or **Single driver** to add the driver. Click **Add** to find the desired driver to add. You can click **Get** to check whether the driver is added successfully.
	- Driver folder: for selecting the desired driver directory.
	- Single driver: for selecting the corresponding driver file in the driver directory.

- Enter to command prompt. On the command prompt, enter the corresponding driver directory, then use the `dism` command (dism + system disk + /add-driver + [XXX.inf file path]) to add the driver. For example: `dism /image:E:\ /add-driver /driver:X:\drivers\2k12R2\amd64\viostor\viostor.inf`
```

### Create a Windows OS point-in-time restore job

#### Before you begin

To restore the Windows OS to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where the operating system resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Point-in-time restore**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) In the **Partition** section, select disks for the restore job. 
	
	(5) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.
9. When a restore job encounters issues (for example, system disk data is restored but the `bcdedit` command cannot be executed, or the restore job fails), it is necessary to rebuild the partition. To restart the partition, do the following:

	(1) In the WinPE configuration window, click **Driver tools** > **Command prompt - Open**. The command prompt appears. In the command prompt, enter the `diskpart` command.
	
	(2) Enter `list disk`. The disk information will be listed.
	
	(3) Select the system disk that requires the partition rebuilding, and enter the command `select disk xx` (where "xx" is the actual disk index number).
	
	(4) Enter `list part` to check the partition information, and confirm that the boot and system partitions exist.
	
	(5) Enter `detail part` to check the file system type and the hidden property.
	
	(6) Execute the following commands to restart the WinPE host.
	
- If the ISO file contains the boot partition, and the file system type can be viewed, modify the start configuration. See the example:

	```{note}
	- bcdedit /store \\?\Globalroot\Device\Harddisk0\Partition1\Boot\BCD /set {bootmgr} device partition=\Device\Harddisk0\Partition1 (The boot partition path should be filled in based on the actual situation. In this example, it is on disk 0, partition 1.)
	- bcdedit /store \\?\Globalroot\Device\Harddisk0\Partition1\Boot\BCD /set {default} device partition=\Device\Harddisk0\Partition2 (The boot partition path should be filled in based on the actual situation. In this example, it is on disk 0, partition 1.)
	- bcdedit /store \\?\Globalroot\Device\Harddisk0\Partition1\Boot\BCD /set {default} osdevice partition=\Device\Harddisk0\Partition2 (The system partition path should be filled in based on the actual situation. In this example, it is on disk 0, partition 1.)
	```

- If the ISO file does not contain the boot partition, the hidden property is Yes, and the file system type cannot be viewed, use `bcdboot` command to configure the boot information. To configure the boot information, do the following:
	
	(1) In the WinPE configuration window, click **Driver tools** > **Command prompt - Open**. The command prompt appears. On the command prompt, enter the `diskpart` command.
	
	(2) Enter `create partition primary size=350 id=7` to create the boot partiton whose size is 350MB. The specific size can be modified according to the actual situation.
	
	(3) Enter `format fs=ntfs` command to format the partition.
	
	(4) Assign the corresponding drive letter. For example: set the letter to drive K: using the command `assign letter=K`.
	
	(5) Enter `active` command to active the partition.
	
	(6) Enter `bcdboot` command to create a new BIOS-bootable boot partition. For example: `bcdboot C:\Windows /s K: /f BIOS /l en-us`

```{note}
1. If you are unable to enter the system after restarting the WinPE host, you can create a restore job again with the **Automatic driver update** option enabled in [Restore options](#restore_options), then rebuild the boot partition.
2. After rebuilding the boot partition, run the `bcdedit` command to check if there is any output. If there is output, restart the WinPE host. If there is no output, use the `bcdedit` command again to modify the boot configuration, and then restart.
```

### Restore options

ADPS provides the following restore options for Windows OS:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore common options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Automatic driver update|Enabling the option will update the NIC driver.||
|Partition rebuild mode| The **Full rebuild** mode restores the whole partition table in the backup host, while the **Partial rebuild** mode only creates the partitions selected for the restore job. ||
|Block size to write to the block device|The smallest unit of storage space allocated to the file system by the operating system. 64KiB, 128KiB, 256KiB, 512KiB, 1MiB, and 2MiB can be selected. 256KiB is selected by default. Generally speaking, the larger the block size of the read block device, the faster the backup speed will be.||
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
|Backup|- Windows 2000, Windows XP, Windows XP Service Pack 1, Windows XP Service Pack 2, and Windows XP Service Pack 3 are not supported.{{ br }}- The system disk must reserve a certain amount of space in order to create a snapshot, otherwise the backup will fail.{{ br }}- Windows 2008, Windows 2008 R2, Windows 2012, Windows 7, Windows 8, Windows 2003, and Windows 2003 R2 cannot back up and restore NVMe system disks.{{ br }}- Dynamic disks of the Windows OS are not supported for backup and restore.{{ br }}- LAN-free pools and local storage pools are not supported for backup. {{ br }}- If the drive letter changes, the backup job may fail. In this case, you need to recreate the backup job.|
|Restore|- Windows 2003 and Windows 2003 R2 cannot back up and restore SATA and NVMe disks.{{ br }}- Windows 2003 and Windows 2003 R2 can only restore IDE disks to IDE disks, and SCSI disks to SCSI disks.{{ br }}- Backup sets of Windows OS booted with BIOS cannot be restored to Windows OS booted with UEFI, and vice versa.{{ br }}- During restore in a KVM environment, automatic driver updates are supported for KVM-Virtio, but not for Windows 2003, Windows 2003 R2, and Windows 2008 systems.{{ br }}- If the drive letter changes, the restore job may fail. In this case, you need to recreate the restore job.|
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
| block size |The minimum unit of storage space allocated by the operating system to the file system. |
| snapshot | The snapshot of the operating system is a read-only static view of the Windows OS. |
```