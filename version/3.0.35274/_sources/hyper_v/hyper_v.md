# User Guide for Hyper-V

## Introduction

Aurreum Data Protection Suite (ADPS) supports the backup and restore of virtual machines (VMs) running on Windows Servers or bare-metal Hyper-V Servers. It is easy to deploy the Hyper-V agent of ADPS. The Hyper-V agent should be installed on the Hyper-V server. This guide describes how to install the agent and how to use ADPS to back up and restore Hyper-V virtual machines.

## Features

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Features
---
class: longtable
---
|Feature |Description|
|------|------|
|Backup source|Virtual machine, host|
|Backup type|Full backup: Back up all the data on the VMs.{{ br }}Incremental backup: Back up the data that has changed since the last backup.|
|Backup target|Standard storage pool, de-duplication storage pool, object storage service pool, tape library pool|
|Backup compression |None, fast|
|Multiple channels| A positive integer from 1 to 32 |
|Backup schedule |Immediate, one-time, hourly, daily, weekly, monthly|
|Reconnection time| The default value is 10 minutes. |
|Restore type|Virtual machine restore, virtual disk restore|
|Restore location |Original host, other hosts|
|Restore granularity| Disk or virtual machine |
|Speed limit |The data transfer speed or disk write and read speed can be limited.|
|D2C|Data can be backed up directly to object storage.|
|D2T |Data can be backed up directly to the tape library.|
|Standard checkpoint |Standard checkpoints (i.e., crash-consistent checkpoints) can be created.|
|Production checkpoint |Production checkpoints (i.e., application-consistent checkpoints) can be created.|
|Restore the network adapter|The network adapter of the VM can be restored.|
|Pool replication| D2D2D, D2D2C, D2D2T |
|Restore from target pools|Restoring from target storage pools is supported.|
|Transport mode| Network mode: Transport data over the TCP/IP network.{{ br }}SAN mode: Read backup data directly over SAN not LAN. This mode requires the backup server to read the backup data directly through SAN. |
|Start virtual machine after restoring| The restored VMs can be started after the restore job is completed. |
|Browse type| The backup set list is in decending order based on the point in time |
```

## Platform Compatibility List

```{table} Platform Compatibility List
| Virtualization Platform Version | Support |
| ------------------------------- | ------- |
| Windows Server 2008 R2| Yes |
| Windows Server 2012 R2| Yes |
| Windows Server 2016| Yes |
| Windows Server 2019| Yes |
| Windows Server 2022| Yes |
| Hyper-V Server 2008 R2| Yes |
| Hyper-V Server 2012 R2| Yes |
| Hyper-V Server 2016| Yes |
| Hyper-V Server 2019| Yes |
```

## Environment Compatibility List

```{table} Environment Compatibility List
| Operating System | Operating System Bits | Support |
| ---------------- | --------------------- | ------- |
| Ubuntu 16.04     | x86_64                | Yes     |
| Ubuntu 18.04     | x86_64                | Yes     |
| Ubuntu 20.04     | x86_64                | Yes     |
| CentOS 7.6       | x86_64                | Yes     |
```

## Download Agent Package

1. Log in to the ADPS console as the admin. Click **Resource** -> **Install Agent** icon. The **Install agent** window appears.

   ![](../images/01-license/Hyper-V/license1.png)

2. Select **Windows** and click **Download Windows agent** to download the package.

   ![](../images/03-Hyper-V/Hyper-V/agent.png)

## Install and Configure Agent

1. Double-click the package to launch the setup wizard.

2. Select the Hyper-V component manually. You can select other resources according to your needs. If you select a resource, its backup and restore module will be enabled.

   ![](../images/03-Hyper-V/Hyper-V/install1.png)

3. Enter the **Backup Server Host**, **Backup Server Port**, and **Access Key**. The Access Key identifies the operator of the agent. You can log in to the ADPS console and see the account's Access Key on the **Personal Settings** -> **Settings**. If the backup server is a non-tenant version, then it is not required to configure the Access Key.

   ![](../images/03-Hyper-V/Hyper-V/install2.png)

4. Set the **Destination Folder** for the backup agent.

5. After the configuration, follow the wizard to complete the installation.

## Register Resource

1. Log in to the ADPS console and open **Resource**. Click the **Register** icon to register the Hyper-V resource.

   ![](../images/01-license/Hyper-V/register0.png)

2. Select the check box to activate the resource.

   ![](../images/01-license/Hyper-V/register1.png)

3. Assign the resource to user groups.

   ![](../images/01-license/Hyper-V/register2.png)

4. Click the **Modify** icon to assign the data network to the resource.

   ![](../images/01-license/Hyper-V/register3.png)

5. Click **Login**. Enter the user name and password of the host's operating system.

   ![](../images/01-license/Hyper-V/register4.png)

## Job

Log in to the ADPS console and go to the **Job** page.

![](../images/03-Hyper-V/Hyper-V/task1.png)

- View the job detail: You can click a **Job Name** to view the job's detailed information.

  ![](../images/03-Hyper-V/Hyper-V/task2.png)

- Start: Select a non-running job and click ![](../images/03-Hyper-V/Hyper-V/task3.png) to start the job.

- Stop: Select a running job and click ![](../images/03-Hyper-V/Hyper-V/task4.png) to stop the job.

- Modify: Select a non-running job and click ![](../images/03-Hyper-V/Hyper-V/task5.png) to modify the job.

- Delete: Select a non-running job and click ![](../images/03-Hyper-V/Hyper-V/task6.png) to delete the job.

- Disable: Select a non-running scheduled job and click ![](../images/03-Hyper-V/Hyper-V/task7.png) to disable the scheduled job.

## Back Up Hyper-V

Go to the **Backup** page.

![](../images/03-Hyper-V/Hyper-V/backup0.png)

The steps to create a backup job are:

1. **Hosts and Resources**: Select the host and Hyper-V that you want to back up. Click **Next**.

2. **Backup Source**: Select **Full Backup** or **Incremental Backup**. Choose virtual machines or the host that you want to back up. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/backup1.png)

   ![](../images/03-Hyper-V/Hyper-V/backup7.png)

   ![](../images/03-Hyper-V/Hyper-V/backup8.png)

   Backup type:

   - Full backup: Back up all the data on the VMs.
   - Incremental backup: Back up the data that has changed since the last backup (no matter what type of the last backup is, the data that has changed will be backed up).

   Backup source:

   - Virtual machine: You can select the specific VMs that you want to back up.
   - Host: You can select the host to back up all the VMs on it.

3. **Backup Target**: Select a storage pool. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/backup2.png)

  - It supports standard storage pool, de-duplication storage pool, object storage service pool, and tape library pool.

4. **Backup Schedule**: Select a schedule type according to your needs. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/backup3.png)

5. **Backup Options**: Set backup options according to your needs. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/backup4.png)

   Common options:

   - Compression: You can select fast compression.
   - Channels: It refers to the maximum number of virtual disks to be backed up at the same time.

   ![](../images/03-Hyper-V/Hyper-V/backup5.png)

   Advanced options:

   - Reconnection time: The job continues after the abnormal reset occurs in the network within the set time. The default value is 10 minutes and the unit is minute.
   - Resumption buffer size: You can set the resumption buffer size. The default value is 10 MiB.
   - Speed limit: You can set different backup speeds for different time periods.
   - Precondition: The precondition is checked before the job starts. The job execution is aborted when the precondition is invalid.
   - Pre/Post action: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.
   - Checkpoint consistency level:
     - Crash consistent: Create application-consistent checkpoints that capture the current state of applications.
     - Application consistent: Use backup technology in the guest operating system to create data-consistent checkpoints that do not contain information about running applications.
     - Automatic: Application Consistent is used by default. If it fails, Crash Consistent will be used instead.

6. **Finish**: Enter the job name and click **Submit**.

   ![](../images/03-Hyper-V/Hyper-V/backup6.png)

## Restore Hyper-V

Go to the **Restore** page.

![](../images/03-Hyper-V/Hyper-V/recovery0.png)

The steps to create a restore job are:

1. **Hosts and Resources**: Select the host and Hyper-V that you want to restore. Click **Next**.

2. **Backup Sets**: Select **Virtual Machine Restore** or **Virtual Disk** as the restore type. Select the restore source. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery1.png)

   ![](../images/03-Hyper-V/Hyper-V/recovery2.png)

3. **Restore Target**: Select a restore target. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery3.png)

4. **Restore Schedule**: Select a schedule type according to your needs. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery4.png)

5. **Restore Options**: Set restore options according to your needs. Click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery5.png)

   Common options:

   - Restore location:
     - Original path: The location of the restored configuration file and disk is the same as the original location, i.e. to overwrite and restore to the original path.
     - New path: New virtual machines or virtual disks will be created. It is required to set the name, configuration, and storage directory of the new virtual machines.

   - Channels: It refers to the number of virtual disks that can be restored at the same time.
   - Start virtual machine after restoring: The virtual machine will be started automatically after the restore job.
   - Restore the network adapter: The network adapter of the VM can be restored.

   ![](../images/03-Hyper-V/Hyper-V/recovery6.png)

   Advanced options:

   - Reconnection time: The job continues after the abnormal reset occurs in the network within the set time. The default value is 10 minutes and the unit is minute.
   - Resumption buffer size: You can set the resumption buffer size. The default value is 10 MiB.
   - Speed limit: You can set different restore speeds for different time periods.
   - Precondition: The precondition is checked before the job starts. The job execution is aborted when the precondition is invalid.
   - Pre/Post action: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.

6. **Finish**: Enter the job name and click **Submit**.

   ![](../images/03-Hyper-V/Hyper-V/recovery7.png)

### Restore Virtual Machines

You can select **Virtual Machine Restore** to create a VM restore job.

![](../images/03-Hyper-V/Hyper-V/recovery1.png)

#### Overwrite and Restore to Original Path

To overwrite and restore to the original path, select the original host as the restore target and the **Original Path** as the restore location.

![](../images/03-Hyper-V/Hyper-V/recovery8.png)

#### Restore to New Path on Original Host

Select the original host as the restore target and the **New Path** as the restore location. Set the new VM name and storage directory.

![](../images/03-Hyper-V/Hyper-V/recovery9.png)

The steps to set a new path:

1. Rename the new VM:

   (1) Click the VM name to access the **Rename** window.

   ![](../images/03-Hyper-V/Hyper-V/recovery10.png)

   (2) Rename the VM directly or add a prefix and suffix. Click **Submit**.

   ![](../images/03-Hyper-V/Hyper-V/recovery11.png)

   (3) Confirm the new VM name and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery12.png)

2. Set the storage directory:

   The steps to set the directories in a batch are:

   (1) Select all the files and click the **Set Storage Directories in batch** option.

   ![](../images/03-Hyper-V/Hyper-V/recovery13.png)

   (2) Select the storage directory and click **OK**.

   ![](../images/03-Hyper-V/Hyper-V/recovery14.png)

   (3) Confirm the directory and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery15.png)

   The steps to set one directory are:

   (1) Click the storage directory of a specific disk.

   ![](../images/03-Hyper-V/Hyper-V/recovery16.png)

   (2) Select the storage directory and click **OK**.

   ![](../images/03-Hyper-V/Hyper-V/recovery17.png)

   (3) Confirm the directory and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery18.png)

#### Restore to New Path on Another Host

Select another host as the restore target and the **New Path** as the restore location.

![](../images/03-Hyper-V/Hyper-V/recovery25.png)

Set the new VM name and storage directory.

![](../images/03-Hyper-V/Hyper-V/recovery19.png)

The steps to create a new path are:

1. Rename the new VM:

   (1) Click the VM name to access the **Rename** window.

   ![](../images/03-Hyper-V/Hyper-V/recovery10.png)

   (2) Rename the VM directly or add a prefix and suffix. Click **Submit**.

   ![](../images/03-Hyper-V/Hyper-V/recovery11.png)

   (3) Confirm the new VM name and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery12.png)

2. Set the storage directory:

   The steps to set the directories in a batch are:

   (1) Select all the files and click the **Set Storage Directories in batch** option.

   ![](../images/03-Hyper-V/Hyper-V/recovery13.png)

   (2) Select the storage directory and click **OK**.

   ![](../images/03-Hyper-V/Hyper-V/recovery20.png)

   (3) Confirm the directory and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery21.png)

   The steps to set one directory are:

   (1) Click the storage directory of a specific disk.

   ![](../images/03-Hyper-V/Hyper-V/recovery22.png)

   (2) Select the storage directory and click **OK**.

   ![](../images/03-Hyper-V/Hyper-V/recovery23.png)

   (3) Confirm the directory and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery24.png)

> Note:
>
> - Restore to a new path on another host: To restore between hosts with different versions, ADPS only supports restoring from a low version to a high version.

### Restore Virtual Disks

You can select **Virtual Disk** to create a virtual disk restore job.

![](../images/03-Hyper-V/Hyper-V/recovery2.png)

#### Overwrite and Restore to Original Disk

To overwrite and restore to the original disk, select the original host as the restore target and the **Original Path** as the restore location.

![](../images/03-Hyper-V/Hyper-V/recovery26.png)

#### Restore to New Disk on Original VM

Select the original host as the restore target and the **New Path** as the restore location. Set the storage directory of the new disk.

![](../images/03-Hyper-V/Hyper-V/recovery38.png)

The steps to create a new path are:

1. Set the storage directory:

   (1) Select all the files and click the **Set Storage Directories in batch** option, or click the storage directory of a specific disk.

   ![](../images/03-Hyper-V/Hyper-V/recovery27.png)

   (2) Select the storage directory and click **OK**.

   ![](../images/03-Hyper-V/Hyper-V/recovery28.png)

   (3) Confirm the directory and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery29.png)


#### Restore to New Disk on Another Host

Select another host as the restore target and the **New Path** as the restore location.

![](../images/03-Hyper-V/Hyper-V/recovery25.png)

Then, set the storage directory of the new disk.

![](../images/03-Hyper-V/Hyper-V/recovery39.png)

The steps to create a new path are:

1. Set the storage directory:

   (1) Select all the files and click the **Set Storage Directories in batch** option, or click the storage directory of a specific disk.

   ![](../images/03-Hyper-V/Hyper-V/recovery27.png)

   (2) Select the storage directory and click **OK**.

   ![](../images/03-Hyper-V/Hyper-V/recovery40.png)

   (3) Confirm the directory and click **Next**.

   ![](../images/03-Hyper-V/Hyper-V/recovery41.png)

## Limitations

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Limitations
---
class: longtable
---
| Function | Limitations                                                  |
| -------- | ------------------------------------------------------------ |
| Backup   | Differential backup is not supported. {{ br }}VM grouping is not supported. {{ br }}If the VM to be backed up is overwritten and restored, the backup jobs will be unavailable and should be recreated.{{ br }}Under the following conditions, the backup job cannot be stopped: The agent is creating checkpoints, or the agent is using a LAN-Free storage pool and mounting the disks.{{ br }}Checkpoints include crash-consistent checkpoints and application-consistent checkpoints. The default checkpoint is application-consistent.{{ br }}On Windows Server 2016 and later versions, Hyper-V backup jobs will not reserve checkpoints after the job completion. {{ br }}A host can only run one backup or restore job at a time. A backup job can back up multiple VMs in parallel, while a restore job can only restore VMs one by one. |
| Restore  | Mount recovery is not supported.{{ br }}The interface cannot display the restore progress of a single virtual disk. {{ br }}To overwrite and restore to the disk, ensure that the original host is shut down and no checkpoints remain.{{ br }}To restore between hosts with different versions, ADPS only supports restoring from a low version to a high version. {{ br }}When the agent is merging subdisks, the restore job cannot be stopped.{{ br }}ADPS cannot restore the Ubuntu/CentOS Generation 2 virtual machines on Hyper-V 2012 series.{{ br }}ADPS cannot restore the virtual disks on Ubuntu/CentOS Generation 2 virtual machines. {{ br }}The Generation 1 virtual machines can only be booted from the IDE controller. The Generation 2 virtual machines can only be booted from the SCSI controller. |
```
