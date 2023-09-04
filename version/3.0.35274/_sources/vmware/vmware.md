# User Guide for VMware

## Introduction

Aurreum Data Protection Suite (ADPS) provides the capability for the backup and restore of virtual machines (VMs) running on VMware ESXi servers. This guide introduces how to properly use ADPS to back up and restore VMware virtual machines.

## Features

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} VMware Features
---
class: longtable
---
|Feature |Description|
|------|------|
|Backup source|Virtual machine, virtual disk, host, cluster, resource pool, file folder, datastore|
|Backup type|Full backup, incremental backup, cumulative incremental backup|
|Backup target|Standard storage pool, de-duplication storage pool, object storage service pool, tape library pool|
|Backup compression |None, fast|
|Backup channels| A positive integer between 1 and 255 |
|Backup schedule |Immediate, one-time, minutely, hourly, daily, weekly, monthly|
|Reconnection time| The default value is 10 minutes. |
|Restore type|Virtual machine restore, virtual disk restore|
|Restore location |Original virtual machine, another virtual machine|
|Restore granularity| Disk or virtual machine |
|CBT| Enable and reset |
|Speed limit |The data transfer speed or disk read and write speed can be limited.|
|Stop jobs|Backup and restore jobs can be stopped.|
|Back up independent disks in persistent mode| Independent disks in persistent mode can be backed up after the shutdown of the virtual machine. |
|Back up RDM disks in physical compatibility mode| RDM disks in physical compatibility mode can be backed up after the shutdown of the virtual machine. |
|Virtual machines per host to be backed up in parallel| 5 virtual machines per host are supported to be backed up in parallel by default. |
|D2C| Data can be backed up directly to object storage services. |
|D2T |Data can be backed up directly to tape libraries.|
|Snapshot |Automatic, Quiesced Snapshot, and Normal Snapshot can be selected. If Automatic is selected, a quiesced snapshot will be created first. If it fails, a normal snapshot will be created.|
|Retain MAC Address after restoring |It is enabled by default. Network addresses may have conflicts during the restore if the option is enabled.|
|Restore network adapter| Restoring network adapter is supported |
|IPv6| Data transfer and management over IPv6 are supported. |
|Retain the virtual machine BIOS UUID| The BIOS UUID of the virtual machine is retained by default. |
|Pool replication| D2D2D, D2D2C, D2D2T |
|Restore from target pools| Data can be restored from target pools. |
|Backup and restore using VMware least privilege account| VMware least privilege account can be used to implement the backup and restore. |
|Auto VDDK version selection| The VDDK version can be automatically selected according to the VMware version. |
|Allocated blocks| The backup of allocated blocks in a virtual machine is supported. |
|Transport mode| NBD, NBDSSL, SAN, HotAdd|
|Start virtual machine after restoring| The restored virtual machines can be started after the restore job is completed. |
|View the backup and restore state in job details| The job details show the state of a virtual machine when the job is running. |
|Add and delete virtual machines and disks when modifying the jobs| Virtual machines or virtual disks can be added or deleted during the modification. |
|Restore virtual machines between hosts with different ESXi versions| Restoring virtual machines between hosts with different ESXi versions is supported. |
```

## VMware Platform Version Compatibility

```{table} VMware Platform Version Compatibility
| Virtual Platform Version | Support |
| ------------------------ | ------- |
| ESXi 5.1                 |Yes      |
| ESXi 5.5                 |Yes      |
| ESXi 6.0                 |Yes      |
| ESXi 6.5                 |Yes      |
| ESXi 6.7                 |Yes      |
| ESXi 7.0                 |Yes      |
| VMware vCenter 5.5       |Yes      |
| VMware vCenter 6.0       |Yes      |
| VMware vCenter 6.5       |Yes      |
| VMware vCenter 6.7       |Yes      |
| VMware vCenter 7.0       |Yes      |
```

## VMware Data Transport

The data transport modes for VMware backup and restore include NBD, NBDSSL, HotAdd, SAN, and Adaptive. Before you back up VMs, set the network among the backup server, backup storage server, backup host, and virtual center. This chapter explains VMware data transport modes.

### NBD & NBDSSL Transport

During the backup and restore, data flows over the LAN network from the virtualization platform to the backup host and then from the backup host to the storage server. SSL encryption (NBDSSL) is also supported. These transport modes are suitable for the protection of the VMware virtual center with simple deployment, simple IP network, small production data, and low transport performance requirements. The backup host supports flexible deployment in the following ways:

- The backup host, backup server, and backup storage server can reside on the same host.

- The backup host can reside on a Linux server that has network connectivity with the backup server and backup storage server. The Linux server can be physical or virtual.

  ![](../images/03-vmware/vmware/vmware_NBD.jpg)

### HotAdd Transport

The backup host is deployed on a virtual machine of the ESXi server that needs to be backed up. During the backup, the SCSI HotAdd feature of ESXi is used to mount the virtual disk to the backup host. Data flows from the backup host to the storage server. The deployment of a backup host has the following requirements:

- The backup host must be installed on a VMware virtual machine with a SCSI controller. This transport mode is not supported for the backup and restore of IDE disks.

- The virtual machine that needs to be backed up and restored should be on the same VMware data center as the backup host. For HotAdd backup, we recommend at least one HotAdd backup host on each ESXi.

- The ESXi Server where the backup host resides must have access to the Datastore of the virtual machine that needs to be backed up.

  ![](../images/03-vmware/vmware/vmware_hotadd.jpg)

### SAN Transport

In SAN transport mode, the backup host is required to read the disk storage of the virtual machine directly through SAN (FC/iSCSI). During the backup, data flows from the backup host to the storage server directly over the LAN network. The LAN-Free backup will be achieved when the backup host and the storage server are on the same host.

1. Applicable scenarios:

   - The datastore of the virtual disk is constructed by LUN and is mounted to the backup host through FC/iSCSI. The backup host collects the backup data directly without going over the LAN.

2. Backup host deployment:

   - The backup host can reside on the same host as the backup storage server so that the VMware datastore can be mounted to the storage server through FC/iSCSI.

   - Or you can mount the VMware datastore through FC/iSCSI to an independent host as the backup host.

      ![](../images/03-vmware/vmware/vmware_san.jpg)

### Adaptive Transport

You can select the Adaptive mode to prioritize the transport modes manually.

## Configure Backup Host

This chapter introduces how to download and install the agent and connect it to the backup server before the first backup and restore.

### Environment Compatibility

```{table} Environment Compatibility
| Operating System | Operating System Bits | Support |
| ---------------- | --------------------- | ------- |
| Ubuntu 16.04     | x86_64                |Yes      |
| Ubuntu 18.04     | x86_64                |Yes      |
| CentOS 7.4       | x86_64                |Yes      |
| CentOS 7.6       | x86_64                |Yes      |
| CentOS 7.9       | x86_64                |Yes      |
| CentOS 8.5       | x86_64                |Yes      |
| Redhat 7.6       | x86_64                |Yes      |
| Redhat 7.9       | x86_64                |Yes      |
| Redhat 8.2       | x86_64                |Yes      |
```

### Install Agent

1. Open a browser and log in to the ADPS console as an *admin*.

2. Click **Resource** -> **Install Agent** icon to access the pop-up installation window.

   ![](../images/01-license/vmware/vmware_lincense01.png)

3. Select **Linux** as the operating system and **VMware** as the module. Click the **Replicate** icon to copy installation commands. You can use curl and wget to install the agent.

   ![](../images/01-license/vmware/vmware_lincense04.png)

4. Open the command line window on the backup host, paste the command, and press Enter to execute the installation.

   ![](../images/01-license/vmware/vmware_lincense05.png)

### Configure VDDK

For VMware backup and restore, you need to install VDDK on the backup host. Add VDDK according to the following steps:

1. Upload vddk.tar.gz to the backup host, and extract it under the /opt/aurreum/adps/ directory. The command is:

   ```shell
   tar -zxvf vddk.tar.gz -C /opt/aurreum/adps/
   ```

2. When creating backup and restore jobs, you can select Automatic or specify a VDDK version. If you enable Automatic, ADPS will automatically select the VDDK version according to the vSphere version. The VDDK versions that you can specify are:
   - VDDK: 6.0 / 6.5 / 6.7 / 7.0

### Check Successful Installation

After the agent installation, log in to the ADPS console as an admin and open the **Resource** page. The backup host is on the resource list.

![](../images/01-license/vmware/vmware_lincense06.png)

## Activate and Authorize

After deploying the backup software and agent on the backup host, activate the host and authorize it to users. If you have multiple agents, you can install them first and then carry out batch activation and authorization. For details, see *Batch Register/Activate/Authorize* in *Administrator's Guide*.

1. Open a browser and log in to the ADPS console as an admin.

2. Go to **Resource** and find the host that you want to activate. Click the **Register** icon.

   ![](../images/01-license/vmware/vmware_lincense02.png)

3. In the pop-up **Activate** window, select the resource you want to activate. Click **Submit**.

   ![](../images/01-license/vmware/vmware_lincense07.png)

4. In the pop-up **Authorize** window, authorize the resource to users to operate the resource. Click **Submit** to complete the authorization.

   ![](../images/01-license/vmware/vmware_lincense03.png)

## Register VMware Virtual Center

### Enable VMware

1. Open a browser and log in to the ADPS console as an admin.

2. Select **Settings** -> **Resource** and click **Enable VMware**.

   ![](../images/03-vmware/vmware/vmware_function01.png)

### Register VMware

1. Click **Resource** -> **Add** icon. Select **VMware vCenter or ESXi**.

   ![](../images/03-vmware/vmware/vmware_function04.png)

2. Enter the parameters in the pop-up **Add VMware vCenter or ESXi** window. Click **Submit**.

   ![](../images/03-vmware/vmware/vmware_function02.png)

   - **Name**: Enter an identifiable name.
   - **Address**: Enter one of the following address in this field:
     - The IP address of the VMware host.
     - The IP address or the domain name of the vCenter Server.
   - **SSL** / **Port**: The SSL connection and the 443 port are used to register VMware by default.
   - **Username**: Enter the username for logging in to the VMware server. See *Limitations* for the least privilege requirements of user roles.
   - **Password**: Enter the corresponding password of the user account for logging into the VMware server.
   - **Agent**: Select the agent that you have activated and authorized.

3. After the VMware data center is added successfully, activate the VMware and authorize it to user groups according to *Activate and Authorize*.


### Manage VMware

After the VMware data center is added, you can manage it on the **Resource** page.

![](../images/03-vmware/vmware/vmware_function05.png)

- **Modify**: To change the name and network of the virtualization platform, click the **Modify** icon to edit the above information in the pop-up **Modify VMware** window.
- **Settings**: When the registered information of the virtualization platform is changed, you can click the **Settings** icon to update the information in the pop-up **Set Up VMware** window.
- **Unregister**: If you do not want to use the VMware, click the **Unregister** icon to delete the virtualization platform.

## Before You Begin

### Check Resource

1. Log in to the ADPS console as an *operator*.

2. Go to **Resource**. The activated and authorized host and resource are on the list with an *Online* state. If the resource is not available, check *Activate and Authorize*.

   ![](../images/03-vmware/vmware/vmware_check01.png)

### Check Storage Pool

1. Log in to the ADPS console as an *operator*.

2. Go to **Storage Pool** and verify the presence of storage pools. If no storage pool is available, contact an admin to create one and assign the permissions to users.

   ![](../images/03-vmware/vmware/vmware_check02.png)

## Create Backup Jobs

This chapter introduces how to back up VMware.

### Prerequisites

1. Open a browser and log in to the ADPS console as an *operator*.

2. You have set the data transport network and deployed the corresponding backup host. See *VMware Data Transport* and *Configure Backup Host*.

3. You have activated the backup host as well as the registered VMware, and authorized them to users. For details, see *Activate and Authorize*.

### Create Full Backup Jobs

1. Click **Backup** and select the VMware resource. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_backup_01.png)

2. Set the backup type and backup source.

   (1) Select **Full Backup** as the backup type.

   ![](../images/03-vmware/vmware/vmware_backup_02.png)

   (2) Click the **Add** icon to access the pop-up **Add Backup Source** window. You can enter the virtual machine name directly in the search box for accurate search (keyword query is supported). Or you can expand the virtualization platform by granularity including cluster, host, resource pool, or file. Select the virtual machines that you want to back up and click **Submit**.

   ![](../images/03-vmware/vmware/vmware_backup_04.png)

   (3) Confirm the selected backup source. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_backup_05.png)

   > Different granularity of the backup source can be included in one backup job.

3. Go to **Agent**. Select the corresponding backup host according to *VMware Data Transport*. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_backup_06.png)

4. Select a **Backup Target**. You can select a standard storage pool, de-duplication storage pool, tape library pool, object storage service pool, encrypted storage pool, etc.

   ![](../images/03-vmware/vmware/vmware_backup_07.png)

5. Select a **Backup Schedule** to set the execution time of the backup job. We recommend running a full backup on a weekly basis. Six schedule types are supported:

   ```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
   ```
   ```{table} Backup Schedule
   | Type      | Description                                                  |
   | --------- | ------------------------------------------------------------ |
   | Immediate | The job immediately starts after it is submitted.            |
   | One time  | After the job is created, the job will be in an idle state and start to run when the specified execution time is reached. |
   | Hourly    | After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of hours/minutes within the time range according to the setting. If the unit is Hour, you can set the value from 1 to 24. If you select the Minute as the unit, then the value can be set from 1 to 60. |
   | Daily     | After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of days according to the setting. The value is an integer between 1 and 5. |
   | Weekly    | After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of weeks according to the setting. You can specify which day of the week to run the job. |
   | Monthly   | The job runs on the specified days of some months at the specified time. For example, you can set the job to run on January 1 and June 1 at 20:00. Or you can set it to run on the first Monday of every month at 20:00. |
   ```

   Example: Run the job every two weeks on Friday at 18:00.

   ![](../images/03-vmware/vmware/vmware_backup_08.png)

6. Set **Backup Options** including common and advanced options.

   (1) **Common options**:

   ![](../images/03-vmware/vmware/vmware_backup_09.png)

   > - **Compression**: Fast compression is enabled by default. Backup data is compressed at the source side for transmission. It can reduce the backup time, improve backup efficiency, and save backup space.
   > - **Channels**: It refers to the maximum number of virtual machines that can be backed up at the same time. The default value is 8. We recommend that the channel number is no more than the number of CPU cores. If it exceeds, the efficiency will not be improved obviously.
   > - **Number of virtual machines per host backup in parallel**: It refers to the number of virtual machines per host to be backed up at the same time. The number cannot exceed 5 by default.
   > - **Data transport mode**: It determines how the backup host reads data from the Datastore. Select the corresponding data transport mode according to the *VMware Data Transport* and the selected backup host. Five modes are supported:
   >   1. **NBD**: The backup host collects data over the LAN network, which has low efficiency.
   >   2. **NBDSSL**: An encrypted network transport mode. The data transport process will be encrypted.
   >   3. **SAN**: The backup host reads data directly over the SAN rather than LAN. This mode requires that the backup host can directly read the disk storage of the virtual machine over the SAN (FC/iSCSI).
   >   4. **HotAdd**: The backup host is installed on a virtual machine of the ESXi server that needs to be backed up so that the host can directly read the backup data in ESXi.
   >   5. **Adaptive**: This mode enables you to prioritize the transport modes manually according to your needs.
   > - **VDDK version**: You can select Automatic or specify a VDDK version. 6.0, 6.5, 6.7, and 7.0 are supported. If you enable Automatic, ADPS will automatically select the VDDK version according to the vSphere version.
   > - **Enable CBT**: It is enabled by default. CBT should be enabled for virtual machine incremental backups. It supports backing up allocated blocks in virtualization environment with an old version (such as **ESXi 5.X**). If you are not allowed to enable CBT for special reasons, then do not select the option check box. However, disabling CBT may have some potential risks such as the failure of allocated block backups and incremental backups.
   > - **Reset CBT**: Reset the CBT of the virtual machine before full backups. If the virtual machine CBT fails, the backup host will be unable to get the data blocks that have changed, which will influence incremental backups. This option can solve the above problem.
   > - **Backup allocated blocks only**: This option only backs up allocated disk blocks during the full backup. If a virtual environment version is too old, it may fail to back up allocated blocks. Under this situation, you can try to enable CBT for backups.
   > - **Backup independent disks in persistent mode**: This option is enabled by default. It supports the backup of independent disks in persistent mode on virtual machines, but the virtual machines should be shut down manually, or you can enable the **Shutdown virtual machine during backup** option.
   > - **Backup RDM disks in physical compatibility mode**: It supports the backup of physical RDM disks on the virtual machine. However, the virtual machines should be shut down manually or you can enable the **shutdown virtual machine during backup** option.
   > - **Shutdown virtual machine during backup**: It is disabled by default. You can enable this option when the virtual machine must be shut down for backups. Otherwise we do not recommend enabling it, which may affect the normal running of virtual machine business.

   (2) **Advanced options**:

   ![](../images/03-vmware/vmware/vmware_backup_10.png)

   > - **Reconnection time**: The job continues after the abnormal reset occurs in the network within the set time. 1-60 minutes are supported. The unit is minute.
   > - **Speed limit**: The data transfer speed or disk read and write speed during a specific time period can be limited. The unit is MiB/s.
   > - **NBD(SSL) transport compression algorithm**: You can use zlib, fastlz, skipz, or none for the NBD or NBDSSL transport mode. Different compression algorithms may lead to different backup efficiency.
   > - **VDDK read buffer size**: You can set the data block size that VDDK reads each time. The default size is 1 MiB. You can also set 64 KiB, 128 KIB, ..., and 64 MiB.
   > - **VDDK transport log level**: You can set Panic, Error, Warning, Audit, Info, Verbose, and Trivia. By default, VDDK transport logs above the Audit level are printed to agent.log.
   > - **NFC operations log level**: You can set Quiet, Error, Warning, Info, Debug. Under the NBD/NBDSSL transport mode, NFC (network file copy) operation logs above the Error level are printed to agent.log by default.
   > - **Snapshot**: You can set Automatic, Quiesced Snapshot, and Normal Snapshot. Automatic is selected by default, which will create a quiesced snapshot first to ensure data consistency in file systems. If it fails to create a quiesced snapshot, it will retry to create a normal snapshot.

7. Set a **Job Name** and confirm the job information. Click **Submit**.

   ![](../images/03-vmware/vmware/vmware_backup_11.png)

### Create Incremental/Cumulative Incremental Backup Jobs

We recommend running full backup jobs for virtual machines regularly (such as weekly) and alternatively running incremental or cumulative incremental backup jobs at short intervals (such as daily) to ensure at least one recoverable point in time every day. You can create an appropriate backup policy based on the information such as the amount of data your business creates every day, recovery speed requirements, and storage space.

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Backup Type
| Backup Type                   | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| Incremental backup            | This type only backs up data that has changed since the last full or incremental backup. It helps to save more space and increase the backup speed. However, the restore process takes a long time because it requires a full backup and all corresponding incremental backups. |
| Cumulative incremental backup | This type only backs up data that has changed since that last full backup. It requires more time and space than the incremental backup. However, the restore speed is faster because it requires only a full backup and the last cumulative incremental backup. |
```

The procedure of an incremental/cumulative incremental backup is the same as that for a full backup. Select **Incremental Backup** or **Cumulative Incremental Backup** as the backup type and choose the virtual machine that you want to back up.

![](../images/03-vmware/vmware/vmware_backup_12.png)

> If the virtual machine has not been fully backed up, the first incremental backup will be performed as a full backup by default.

## Create Restore Jobs

This chapter introduces how to restore VMware virtual machines. According to the actual needs of users, ADPS provides various restore types including Virtual Machine Restore and Virtual Disks Restore.

### Prerequisites

1. You have a completed backup job. See *Create Backup Jobs*.

2. To restore to another virtual center, deploy the backup host, register and activate that virtual center, and authorize the virtual center to users.

> When restoring a VM from one virtual center to another, you can restore the VM to the original VM hardware version or the target ESXi/ESX version. If the target ESXi/ESX host version is not new enough to support the original VM hardware version, the restore may fail. You can disable the **Retain virtual machine version** option to restore the VM to the ESXi/ESX version.

### Create Virtual Machine Restore Jobs

When a disaster occurs in the virtual machine, you can restore the entire machine through **Virtual Machine Restore**. It supports restoring to another virtual center, another host, the original path, and a new path.

1. Go to **Restore**. Select the VMware resource that you want to back up. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_restore_01.png)

2. Select **Virtual Machine Restore** as the restore type. Expand the restore source by level. Select the virtual machine and point in time that you want to restore. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_restore_02.png)

3. Select a **Restore Target**. The original virtual center is selected by default. You can select another virtual center.

   ![](../images/03-vmware/vmware/vmware_restore_03.png)

4. Select an **Agent**. We recommend that you select the corresponding backup host according to *VMware Data Transport*. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_restore_04.png)

5. Set the **Restore Schedule**. Only immediate and one-time schedules are supported.

   ![](../images/03-vmware/vmware/vmware_restore_05.png)

6. Go to **Restore Options**. Set **Restore Location**.

   (1) Restore to the original path

   When you choose **Original Path**, the job will use the original VM configuration to overwrite and restore the VM.

   ![](../images/03-vmware/vmware/vmware_restore_07.png)

   (2) Restore to a new path

   When you choose **New Path**, the new path setting window appears. You can create a new virtual machine on the original or another host.

   ![](../images/03-vmware/vmware/vmware_restore_08.png)

   a. Set the host

   - Click **Host** and go to the setting page.

     > **Batch Set Up Host**: You can set the same host for multiple virtual machines.

     ![](../images/03-vmware/vmware/vmware_restore_09.png)

   - Expand the virtualization platform by level and select the target host. Click **Submit** to complete the setting.

      ![](../images/03-vmware/vmware/vmware_restore_10.png)

   b. Set the resource pool/vApp

   - The virtual machine is restored to the sub-resource pool of the compute resource to which the host belongs by default. Click the value under the **Resource pool/vApp**. The resource pool setting window appears.

     > **Batch Set Up Resource pool/vApp**: You can set the same resource pool for multiple virtual machines.

     ![](../images/03-vmware/vmware/vmware_restore_11.png)

   - Select the target resource pool. Click **Submit** to complete the changes.

     ![](../images/03-vmware/vmware/vmware_restore_12.png)

   c. Set the virtual machine name and folder

   - It is restored to the virtual machine subfolder in the data center by default.

     ![](../images/03-vmware/vmware/vmware_restore_13.png)

   - Rename the new virtual machine

     To rename the virtual machine, click the name under the **New VM Name** to access the **Rename** window. You can set the New VM Name or use Add Prefix and Add Suffix to change the name. Click **Submit** to complete the settings.

     > Batch Rename: You can add the same prefix/suffix for multiple virtual machines.

     ![](../images/03-vmware/vmware/vmware_restore_14.png)

   - Change the folder where the virtual machine resides

     To change the folder, click the name under the **Folder**. In the pop-up window, expand the folders and select one. Click **Submit** to complete the setting.

     > Batch Set Up Folder: You can set the same folder for multiple virtual machines.

     ![](../images/03-vmware/vmware/vmware_restore_15.png)

   d. Set the datastore

   - The first datastore of the host is selected by default.

     ![](../images/03-vmware/vmware/vmware_restore_16.png)

   - Set the **Datastore**

     - To change the datastore, click the value under the **Datastore** to access the setting window. Select a datastore and click **Submit** to complete the setting.

      ![](../images/03-vmware/vmware/vmware_restore_17.png)

      > Batch Set Up Datastore: You can set the same datastore for multiple files or disks.

   - Set the **Disk Provisioning**

     - To change the disk provisioning, click the value under the **Disk Provisioning** to access the setting window. Select **Thin Provision**, **Thick Provision Lazy Zeroed**, or **Thick Provision Eager Zeroed** from the **Disk Provisioning** list. Click **Submit** to complete the setting.

      > Batch Set Up Disk Provisioning: You can set the same disk provisioning for multiple disks.

      ![](../images/03-vmware/vmware/vmware_restore_18.png)

   e. Set the network

   - Click the value under the **Target Network** to access the pop-up setting window.

     ![](../images/03-vmware/vmware/vmware_restore_19.png)

   - Select a network and click **Submit** to complete the setting.

     > Batch Set Up Network: You can set the same network for multiple virtual machines.

     ![](../images/03-vmware/vmware/vmware_restore_20.png)

   f. Go back to the **Restore Options** page. You can click the **Modify** icon beside the **New Path** to re-modify the specifications of the target machine.

      ![](../images/03-vmware/vmware/vmware_restore_21.png)

7. Besides the **Restore Location**, you can also set other options.

   (1) **Common options**:

   ![](../images/03-vmware/vmware/vmware_restore_22.png)

   > - **Channels**: It refers to the maximum number of virtual machines that can be restored at the same time. The default value is 8. We recommend that the channel number is no more than the number of CPU cores. If it exceeds, the efficiency cannot be improved obviously.
   > - **Number of virtual machines per host restore in parallel**: It refers to the number of virtual machines per host that can be restored at the same time. The number can not exceed 5 by default.
   > - **Data transport mode**: You can choose NBD, NBDSSL, SAN, HotAdd, and Adaptive.
   > - **VDDK version**: You can select Automatic or specify a VDDK version. 6.0, 6.5, 6.7, and 7.0 are supported. If you enable Automatic, ADPS will automatically select the VDDK version according to the vSphere version.
   > - **Overwrite existing virtual machines**: If a virtual machine has the same name as the restored virtual machine, this option should be enabled for successful restores. This option is required when you choose to restore to the original path.
   > - **Start virtual machine after restoring**: The restored virtual machine can be automatically started after the successful restore job.
   > - **Restore the CD/DVD drive**: You can enable this option to restore the CD/DVD drive of the original virtual machine.
   > - **Retain the virtual machine version**: This option is enabled by default. The VM will be restored to the original VM hardware version. If you disable it, the virtual machine will be restored to the target host version. This option is applicable to a target host with an old version that does not support the hardware version of the original virtual machine.
   > - **Retain the virtual machine BIOS UUID**: You can retain the BIOS UUID of the virtual machine.
   > - **Restore the network adapter**: You can restore the network adapter of the original virtual machine.
   > - **Retain the MAC address**: The MAC address of the original virtual machine can be retained.

   (2) **Advanced options**:

   ![](../images/03-vmware/vmware/vmware_restore_23.png)

   > - **Reconnection time**: The job continues after the abnormal reset occurs in the network within the set time. 1-60 minutes are supported. The unit is minute.
   > - **Speed limit**: The data transfer speed or disk read and write speed during a specific time period can be limited. The unit is MiB/s.
   > - **NBD(SSL) transport compression algorithm**: You can use zlib, fastlz, skipz, or none for the NBD or NBDSSL transport mode. Different compression algorithms may lead to different restore efficiency.
   > - **VDDK write buffer size**: You can set the data block size that VDDK writes each time. The default size is 1 MiB. You can also set 64 KiB, 128 KiB, ..., and 64 MiB.
   > - **VDDK transport log level**: You can set Panic, Error, Warning, Audit, Info, Verbose, and Trivia. By default, VDDK transport logs above the Audit level are printed to agent.log.
   > - **NFC operations log level**: You can set Quiet, Error, Warning, Info, and Debug. By default, under the NBD/NBDSSL transport mode, NFC (network file copy) operation logs above the Error level are printed to agent.log.
   > - **Restore template to virtual machine**: If the backup source is a virtual machine template and the restore target is vCenter, the template will be restored. If the restore target is ESXI, the template will be automatically restored to a virtual machine because ESXi does not support templates.

8. Set a **Job Name** and confirm the job information. Click **Submit**.

   ![](../images/03-vmware/vmware/vmware_restore_24.png)

9. Enter the correct verification code in the pop-up verification window. Click **Submit** to confirm again before the restore.

### Create Virtual Disk Restore Jobs

When a disaster occurs in some disks of the virtual machine, you can use **Virtual Disk Restore** to restore those disks to the target virtual machine. You can overwrite the existing disks and create new disks.

1. Open **Restore**. Select the VMware resource that you want to restore. Click **Next**.

2. Select **Virtual Disk** as the restore type. Expand the restore source by level and select the disk under the backup point in time that you want to restore. Click **Next**.

   ![](../images/03-vmware/vmware/vmware_restore_25.png)

3. Select a **Restore Target**. The original virtual center is selected by default. You can restore to another virtual center.

4. Select an **Agent**. We recommend that you select the corresponding backup host according to the *VMware Data Transport*. Click **Next**.

5. Set a **Restore Schedule**. Only immediate and one-time schedules are supported.

6. Go to **Restore Options**. Set **Restore Location**.

   Click the **Expand** icon to list the information of the selected disks. Set the target virtual machine and recovery mode.

   ![](../images/03-vmware/vmware/vmware_restore_26.png)

   (1) Set the target virtual machine

   - Restore to the original virtual machine: Keep the default settings.

   - Restore to another virtual machine: Click the **Modify** icon to access the setting window. Choose a target machine and click **Submit** to complete the setting.

     ![](../images/03-vmware/vmware/vmware_restore_27.png)

   (2) Set the recovery mode

   - Overwrite: **Overwrite** is selected by default. It is applicable to restore to the original disk, or the disk with the same UUID on the target machine.

   - New: If you select **New**, set the file location, disk provisioning, the controller position, and retain the disk UUID. It is applicable to restore to a new disk on the original or another virtual machine.

     ![](../images/03-vmware/vmware/vmware_restore_28.png)

7. Besides **Restore Location**, you can set other options in **Restore Options**.

8. Set a **Job Name** and confirm the job information. Click **Submit**.

9. Enter the correct verification code in the pop-up verification window. Click **Submit** to confirm again before the restore.

## Manage Jobs

On the Job interface, you can view all the job information of VMware backups and restores. You can also start, modify, clone, and delete the jobs.

![](../images/03-vmware/vmware/vmware_restore_29.png)

- View the job details: Click **Job Name** to view the detailed information about the job.
- Start: Click ![](../images/03-vmware/vmware/vmware_job03.png) to start the job immediately.
- Modify: Click ![](../images/03-vmware/vmware/vmware_job04.png) to modify the basic job information, backup/restore schedule, and backup/restore options.
- Stop: Click ![](../images/03-vmware/vmware/vmware_job06.png) to stop the job.
- Disable: Click ![](../images/03-vmware/vmware/vmware_job07.png) to disable the periodic time strategy of the job.
- Delete: Click ![](../images/03-vmware/vmware/vmware_job05.png) to access the confirmation window. Click **OK** to delete the job.

## Limitations

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Limitations
---
class: longtable
---
| Function             | Limitations                                                  |
| -------------------- | ------------------------------------------------------------ |
| User role privileges | Global: Enable methods, disable methods{{ br }}Guest operations: Guest operation queries, guest operation modifications, guest operation program execution{{ br }}Inventory: Create, register, remove, move {{ br }}Snapshot management: Create snapshot, remove snapshot, revert to snapshot{{ br }}Configuration: Toggle disk change tracking, configure raw device, add or remove device, add existing disk, add new disk, change resource, remove disk, change settings, advanced configuration, set annotation, acquire disk lease{{ br }}Interaction: Power off, configure CD media, configure floppy media, device connection, guest operating system management by VIX API, power on{{ br }}Provisioning: Allow read-only disk access, allow virtual machine download, allow file access, allow disk access, create template from virtual machine, mark as virtual machine, mark as template{{ br }}Datastore: Allocate space, browse datastore, low level file operations, remove file{{ br }}Resource: Assign virtual machine to resource pool{{ br }}vApp: Add virtual machine{{ br }}Network: Assign network |
| Backup               | Synthetic backup is not supported. {{ br }}Automatic selection logic for VDDK version: If the vSphere version is older than or equal to 6.0, VDDK 6.0 will be selected. For other vSphere versions, the corresponding VDDK version will be selected. For example, if the vSphere version is 6.5, VDDK 6.5 will be selected automatically. {{ br }}VDDK does not support ARM architecture.{{ br }}In SAN mode backup, the agent must be able to access the FC or iSCSI SAN where the ESXi virtual machine storage resides. {{ br }}VVol datastore is not supported.{{ br }}HotAdd transport requirements: The virtual machine agent must have a SCSI controller. IDE disks are not supported. The virtual machine agent can access the datastore of the target virtual machine disk and connect to the 902 port of ESXi. |
| Restore              | File-level Restore and Mount Restore of Data are not supported. {{ br }}For versions older than vSphere 5.5, the size of the virtual disk being restored through the SAN transport mode must be an integer multiple of the underlying VMFS block size. |
```

## Glossary

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Glossary
---
class: longtable
---
| Term                                | Description                                                  |
| ----------------------------------- | ------------------------------------------------------------ |
| CBT                                 | CBT is the abbreviation of Changed Block Tracking. When CBT is enabled, the backup host can collect the data blocks that have changed since the last backup. |
| Snapshot type                       | Snapshot types include quiesced snapshot and normal snapshot. {{ br }}Quiesced snapshot: Before the virtual machine takes a snapshot, it will write the data from the memory to the disk, quiesce the file system, and take a snapshot. {{ br }}Normal snapshot: Normal snapshot will take a snapshot of the memory and create a file to record the content of the memory at the creation time of the snapshot. When Quiesced Snapshot is selected and it fails to create a quiesced snapshot, the snapshot type will automatically change to Normal Snapshot. |
| Independent disk in persistent mode | Disks in persistent mode behave like regular disks on a physical device. All data written to the disk in persistent mode is written to it permanently. |
| RDM disk                            | RDM is the abbreviation of Raw Device Mapping. It has two compatibility modes: {{ br }}(1) Physical compatibility mode: In this mode, the LUN of physical storage can be identified and used by virtual machines. Virtual machines can directly read and write the LUN. However, the space cannot be cloned or used as a template. The file name is generally xxxx-rdmp.vdmk{{ br }}(2) Virtual compatibility mode: In this mode, the LUN of physical storage from the view of virtual machines is a virtual disk. It can be used as a snapshot and template. And it can be migrated, etc. |
| Thin provision                      | Thin disks use thin provisioning to optimize the space usage in the SAN. They only consume the capacity required for running. As guest operating systems allocate more I/O to thin disks, the space required grows over time. For example, if you create an 8 GB thin disk, the disk only consumes 1 GB initially and up to 8 GB as it grows. |
| Thick provision lazy zeroed         | A thick-provisioned lazy-zeroed disk allocates all its disk space at the time of its creation. Note that thick-provisioned lazy-zeroed disks may contain old data from physical media. It needs to be zeroed out on command before new data can be written to the disk blocks. The delayed-zeroed disk can be created quickly, but its performance typically degrades due to increased IOPS. |
| Thick provision eager zeroed        | Thick-provisioned zeroed disks create VM disks in the default thick format and allocate the maximum required disk space at the time of their creation. The allocated disk space does not contain any previous data on physical media. Thick Provision Zeroed supports VMware Fault Tolerance. During the creation, the data from physical media will be zeroed out. Therefore, creating this type of disk may take longer than creating other disks. Thick disks use thick provisioning to pre-allocate physical storage after the disks are created. If you create a 50 GB virtual disk, the disk will consume exactly 50 GB of physical space. Then no other disk or VM can use that disk space. |
| Virtual machine template            | A template is a primary copy of a virtual machine that can be used to deploy virtual machines in batches. It cannot be powered on or connected to any resource pool. |
```