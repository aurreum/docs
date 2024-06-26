# User Guide for File

## Introduction

Aurreum Data Protection Suite (ADPS) provides the capability for the backup and restore of files. This guide introduces how to correctly use ADPS to back up and restore files.

## Features

```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Features
---
class: longtable
---
| Feature                              | Description                                                  |
| ------------------------------------ | ------------------------------------------------------------ |
| Backup type                          | Full backup: Back up directories or files on the operating system. {{ br }}Incremental backup: Back up data that has changed since the last backup point in time. {{ br }}Cumulative incremental backup: Back up all data changed after the most recent incremental backup. {{ br }}Synthetic backup: Full backup for the first time, followed by incremental backups. |
| Filter                               | Exclusion: Do not back up selected files or directories. {{ br }}Inclusion: Back up selected files or directories. |
| Backup source                        | One or more directory files                           |
| Channels                             | A positive integer between 1 and 255                         |
| Backup target                        | Standard storage pool, de-duplication storage pool, local storage pool, file synthetic pool, tape library pool, object storage service pool, LAN-Free pool |
| Clone backup job                     | Backup jobs can be cloned. The cloning will change an incremental backup job to a full backup job and relevant backup sets will be selected. |
| Stop jobs                            | Stop a backup or restore job                                 |
| Backup compression                   | None, fast, tunable                                          |
| Backup schedule                      | Immediate, one-time, minutely, hourly, daily, weekly, monthly |
| VSS                                  | Support Windows                                              |
| Sparse files                         | Support Linux, Windows                                       |
| Hard link                            | Support Linux, UNIX                                          |
| Restore type                         | Timepoint restore: Restore files to a specific timepoint. {{ br }}Instant recovery: Mount the backup sets of files in the storage server to achieve instant recovery. {{ br }}Recovery testing: Periodically restore the latest backup sets of files to the target or source host. |
| Restore location                     | Support the restore to original path or custom path. {{ br }}Types to convert the custom path: strip directory and path transform. {{ br }}Methods to handle files with the same name: overwrite, skip, retain most recent, rename and save. |
| Restore granularity                  | Single, multiple                                             |
| Browse method                        | Browse by timepoint or directory                             |
| Restore to different hosts           | Different hosts can be selected as restore targets, which support Linux and Windows. |
| Reconnection time                    | The job continues after the abnormal reset occurs in the network within the set time. The set time is 10 minutes by default. |
| Storage pool replication             | File backup sets support storage pool replication            |
| Restore from target Pool             | Restore from the target storage pool                         |
| Pre/Post action                      | The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored. |
| Limit speed within different periods | Limit data transfer speed or disk read and write speed within different periods. |
| D2C                                  | Back up data directly to object storage.                     |
| D2T                                  | Back up data directly to tape library.                       |
| LAN-Free                             | Back up to and restore from LAN-Free storage pools.          |
| Modify backup target of a job        | Do not support modifying the backup target of a job.         |
| IPv6                                 | Support the data transfer and management via IPv6.           |
| Access Key login                     | Support using the operator's Access Key to log in to files' resources. |
| Backup retry count                   | The number of times to retry a backup job. The backup retry count is 0 time by default. |
| Backup retry interval                | The interval to retry a backup job. The backup retry interval is 30 minutes by default. |
```
> Note:
>
> - ADPS supports synthetic backup on all platforms.  Character encoding conversion is available for the mount recovery job.

## Install and Configure Agent

### Verify Compatibility

ADPS supports the backup and restore of files. You should check whether the operating system (OS) is supported before deploying the agent.

#### Compatibility List

```{table} Compatibility List
---
class: longtable
---
| OS                   | CPU Architecture | OS Bits | Note         |
| -------------------- | ---------------- | ------- | ------------ |
| Windows 2003 SP2     | x86              | 32      |              |
| Windows 2000 SP4     | x86              | 32      |              |
| Windows XP SP3       | x86              | 32      |              |
| Windows 2003 R2      | x86              | 32      |              |
| Windows 2003 R2      | x86              | 64      |              |
| Windows 7            | x86              | 32      |              |
| Windows 7            | x86              | 64      |              |
| Windows 8            | x86              | 64      |              |
| Windows 10           | x86              | 64      |              |
| Windows 2008         | x86              | 32      |              |
| Windows 2008         | x86              | 64      |              |
| Windows 2008 R2      | x86              | 64      |              |
| Windows 2003 SP2     | x86              | 32      |              |
| Windows 2003 Sp2     | x86              | 64      |              |
| Windows 2012         | x86              | 64      |              |
| Windows 2008 SP2     | x86              | 32      |              |
| Windows 2008 Sp2     | x86              | 64      |              |
| Windows 2008 R2      | x86              | 32      |              |
| Windows 2012 R2      | x86              | 64      |              |
| Windows 2016         | x86              | 64      |              |
| Red Hat 9            | x86              | 32      |              |
| Red Hat 4            | x86              | 32      |              |
| Red Hat 4            | x86              | 64      |              |
| Red Hat 5            | x86              | 32      |              |
| Red Hat 5            | x86              | 64      |              |
| Red Hat 6            | x86              | 32      |              |
| Red Hat 6            | x86              | 64      |              |
| Red Hat 6.5          | x86              | 64      |              |
| Red Hat 7            | x86              | 32      |              |
| Red Hat 7            | x86              | 64      |              |
| Red Hat 8            | x86              | 64      |              |
| Ubuntu 12.04         | x86              | 32      |              |
| Ubuntu 12.04         | x86              | 64      |              |
| Ubuntu 14.04         | x86              | 32      |              |
| Ubuntu 14.04         | x86              | 64      |              |
| Ubuntu 16.04         | x86              | 64      |              |
| Ubuntu 18.04         | x86              | 64      |              |
| AIX 5.1              | POWER            | 64      |              |
| AIX 5.3              | POWER            | 64      |              |
| AIX 6.1              | POWER            | 64      |              |
| AIX 7.1              | POWER            | 64      |              |
| SLES 9               | x86              | 32      |              |
| SLES 9               | x86              | 64      |              |
| SLES 10              | x86              | 32      |              |
| SLES 10              | x86              | 64      |              |
| SLES 11              | x86              | 32      |              |
| SLES 11              | x86              | 64      |              |
| SLES 11              | s390/s390x       | 64      |              |
| SLES 12              | x86              | 64      |              |
| Solaris 9            | x86              | 64      |              |
| Solaris 9            | SPARC            | 64      |              |
| Solaris 10           | x86              | 64      |              |
| Solaris 10           | SPARC            | 64      |              |
| Solaris 11           | x86              | 64      |              |
| Solaris 11           | SPARC            | 64      |              |
| HP-UX B.11.11        | PA-RISC          | 64      |              |
| HP-UX B.11.23        | PA-RISC          | 64      |              |
| HP-UX B.11.31        | IA64             | 64      |              |
| Ubuntu 16.04         | ARM              | 64      |              |
| Debian 6.0           | x86              | 32      |              |
| Debian 6.0           | x86              | 64      |              |
| Debian 7.0           | x86              | 32      |              |
| Debian 7.0           | x86              | 64      |              |
| Debian 7.5           | x86              | 32      |              |
| Debian 7.6           | x86              | 32      |              |
| Debian 7.6           | x86              | 64      |              |
| Debian 7.8           | x86              | 32      |              |
| Debian 8.0           | x86              | 32      |              |
| Debian 8.0           | x86              | 64      |              |
| Debian 8.3           | x86              | 64      |              |
| Debian 9.0           | x86              | 64      |              |
| OpenSUSE 10.3        | x86              | 64      |              |
| OpenSUSE 11.4        | x86              | 64      |              |
| CentOS 3.9           | x86              | 64      |              |
| CentOS 4.5           | i686             | 32      |              |
| CentOS 4.6           | IA64             | 64      |              |
| CentOS 5.1           | x86              | 64      |              |
| CentOS 6.0           | x86              | 64      |              |
| CentOS 6.8           | x86              | 64      |              |
| CentOS 7.3           | x86              | 64      |              |
| CentOS 7.5           | x86              | 64      |              |
| SUSE 11 SP4          | POWER Linux      | 64      | (Big Endian) |
| Red Hat 6.5          | POWER Linux      | 64      | (Big Endian) |
| Server 7 SP3         | x86              | 64      |              |
```

### Download Agent Package

Open a browser and log in to ADPS as the admin. Click **Resource** -> **Install Agent** icon. You can download the installation packages according to your needs.

![file_agent01](../images/01-agent/file/file_agent01.png)

### Install and Configure Agent on Windows

#### Download Installation Package

Select **Windows** and click **Download Windows agent**.

![file_agent02](../images/01-agent/file/file_agent02.png)



#### Install Agent on Windows

 1. Upload the installation package to the target host.

 2. Double-click the package to install it according to the wizard and click **Next**.

 3. This installation package is a collection of components. It checks the file resources, files, or applications installed on the host by default. Check the **File** and click **Next**.

    ![file_agent03](../images/01-agent/file/file_agent03.png)

 4. Set the **Backup Server Host**, **Backup Server Port**, and **Access Key**. Click **Next**.

    ![file_agent04](../images/01-agent/file/file_agent04.png)

 5. Select **Destination Folder** and click **Next** to install the software. Wait for the installation to complete.

###  Install and Configure Agent on Linux

1. Select **Linux** and **File**. Copy an installation command.

![file_agent05](../images//01-agent/file/file_agent05.png)

2. Paste the command on the command line and press Enter to execute the installation.

   ```
   root@ubuntu1804:~# curl -o- "http://192.168.17.80:50305/d2/update/script?modules=file&location=http%3A%2F%2F192.168.17.80%3A50305&access_key=17dcab134000ef21d3784f3e8808a3dd&rm=&tool=curl" | sh
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   100  7904    0  7904    0     0  2572k      0 --:--:-- --:--:-- --:--:-- 2572k
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
   100 47.5M  100 47.5M    0     0  99.3M      0 --:--:-- --:--:-- --:--:-- 99.3M
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
   100 5575k  100 5575k    0     0  63.3M      0 --:--:-- --:--:-- --:--:-- 63.3M
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
   100  665k  100  665k    0     0  16.2M      0 --:--:-- --:--:-- --:--:-- 16.2M
   ```


### Check Successful Installation

After the successful installation, you can log in to ADPS as the admin and see that the host is on the **Resource** list.

![file_license01](../images/02-license/file/file_license01.png)

## Activate License and Assign Authorization

This chapter is applicable to configuring one agent. If you have multiple agents, you can deploy them first, then carry out batch activation and authorization. Please refer to *Batch Activate* from *Administrator's Guide* for more details.

### Register Host

Log in to ADPS as the admin, go to **Resource**, and find the host that you need to activate. Click the **Register** icon.

![file_license02](../images/02-license/file/file_license02.png)

### Activate License

In the pop-up **Activate** window, select the resource you want to activate. Click **Submit**.

![file_license03](../images/02-license/file/file_license03.png)

### Assign Authorization

After the successful activation, you can authorize users to operate the resource in the pop-up **Authorize** window.

![file_license04](../images/02-license/file/file_license04.png)

## Before You Begin

### Check Resource

Log in to ADPS as the operator, go to **Resource**, and check whether the activated and authorized resources are in the "Online" state. If the resource is not available, please refer to *Activate License and Assign Authorization*.

![file_login01](../images/03-file/file_login01.png)

### Check Storage Pool

Log in to ADPS as the operator, go to **Storage Pool**, and check whether there is any storage pool. If a storage pool is not listed, please contact the admin to create one and assign permissions to the operator.

![file_storaged01](../images/03-file/file_storaged01.png)

## Log in to Instance for the First Time

1. Before using File resources to create backup and restore jobs, you need to **Login** file first.

![file_login02](../images/03-file/file_login02.png)


2. File supports the login by two authentication methods: **OS Authentication**, and **Access Key**.

- **OS Authentication**: Use the admin account to log in.
- **Access Key**: Log in with the Access Key of the user who has the permission of the resource. You can run a job without a password, which has potential data security risks.

3. Select the **Authentication** method. Enter **User** and **Password**. Click **Login**.

![file_login03](../images/03-file/file_login03.png)

> **Note**:
>
> - To use the Access Key authentication, the admin must enable Access Key Login Instance in Settings->Settings->Security.

## Create Backup Jobs

This chapter introduces how to use ADPS to back up files.

### Prerequisites

- You have installed the agent. For installation, see *Install and Configure Agent*.
- You have activated the license and assigned the authorization. For details about the activation and authorization, see *Activate License and Assign Authorization*.
- Log in to ADPS as the *operator*.

### Create Full Backup Jobs

All files can execute backup jobs.

(1) Click **Backup**. Select file host and instance.

(2) Select **Full** as the backup type and choose file folders or files.

![file_backup01](../images/03-file/file_backup01.png)

(3) Select **Backup Target**. You can choose standard storage pool, de-duplication storage pool, tape library pool, object storage service pool, or LAN-Free pool.

>  Note:
>
>  - It's not supported to store full backups, incremental backups, and cumulative incremental backups in different storage pools.

(4) Go to **Backup Schedule** and set the run time of the backup job. For details, see *Backup Schedule Operation*. It is generally recommended to run a full backup on a weekly basis.

(5) Set **Backup Options** including common and advanced options.

- **Common options**:

![file_backup02](../images/03-file/file_backup02.png)

**Compression**: Fast is enabled by default.

- None: No compression during the backup.
- Tunable: You can customize the Compression Level, which requires the activation of the advanced features.
- Fast: Use the fast compression algorithm during the backup.

**Channels**: Used to improve backup efficiency. The default value of Channels is 1 and the range is 1 to 255. For details, refer to *Channel Number Configuration*.

- **Advanced options**:

![file_backup03](../images/03-file/file_backup03.png)

**Reconnection time**: The job continues after the abnormal reset occurs in the network within the set time. The value can be 1 to 60. The unit is minute(s).

**Speed limit**: Set the limit for data transfer speed or disk read and write speed. The unit can be MiB/s or KiB/s. Click the ‘’+‘’ icon to add limits at different times.

**Precondition**: The precondition is executed before the job starts. The job execution is aborted when the precondition is not met.

**Pre/Post action**: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.

**Snapshot**: Enabled by default for consistent backups via snapshots when there are backup files in a locked state. Only available for Windows.

**Pre/Post snapshot action**: The pre snapshot action is called before resources are backed up or restored after the job starts. The post snapshot action is called after the resource has been backed up or restored. Ignore snapshot and action error by default. Only available for Windows.

**Backup NTFS ADS and ACLs**: Use the BackupRead API to back up NTFS Alternate Data Stream and Security Information. The option is enabled by default to prevents failures to CreateFile when backing up CIFS without ADS and ACL. Only available for Windows.

**Backup retry count**: The number of times to retry the backup if the backup fails due to network error or other reasons. The range is 0~10 times, the default is 0.

**Backup retry interval**: The time interval to retry the backup if the backup fails due to network error or other reasons. The range is 0~120 minutes, the default is 30 minutes.

**Detect sparse files**: Auto-detection by default, which detects sparse files with very small valid data. Only available for Linux.

**Cross mount points**: Only available for Linux.

- The option is enabled by default, which backs up all files in the selected directories.

- When it's disabled, if you select multiple directories that belong to different file systems, only back up files in the file system where the parent directory of these directories is located.

  > For example:
  >
  > - With the option disabled, you select three directories: A, B, and C.
  > - Directory A and Directory B belong to the xfs file system, Directory C belongs to the tmpfs file system, and their parent directory belongs to the xfs file system.
  > - Since the parent directory, Directory A and Directory B are in the same file system, only files from Directory A and Directory B will be backed up.

**Reparse Point Types**: You need to select dedup when Windows enables the data deduplication feature.

(6) Set **Job Name** and check whether the job information is correct. Click **Submit**.

### Create Incremental Backup Jobs

It is generally recommended to create file full backups regularly (such as weekly) or create file incremental backups at short intervals (such as daily). You can use the cumulative incremental backup to back up data that has changed since the last full backup so that you have at least one recoverable point in time every day.

- The steps are the same as steps to create a full backup. Select **Incremental** as the backup type, choose the full backup job for incremental backup, and perform the incremental backup according to the folders or files selected by full backup job.

  ![file_icr01](../images/03-file/file_icr01.png)


### Create Cumulative Incremental Backup Jobs

It is generally recommended to create full file backups regularly (such as weekly) or create cumulative incremental backups at short intervals (such as daily). You can use the cumulative incremental backup to back up data that has changed after the most recent incremental backup so that you have at least one recoverable point in time every day.

- The steps are the same as steps to create a full backup. Select **Cumulative Incremental Backup** as the backup type and perform the cumulative incremental backup based on the folders or files selected by the full backup job.

  ![file_icr02](../images/03-file/file_icr02.png)

### Create Synthetic Backup Jobs

Synthetic backup is the process of synthesizing the existing full backup sets as the base with subsequent incremental backups over a storage device. The synthetic result will produce new synthetic backup sets.

Due to the use of synthetic backup, there is no need for the storage device to save multiple copies of full backup data, which correspondingly reduces the pressure of management and storage space caused by data growth, and the storage cost. In addition, when restoring incremental backup sets, ordinary backups need to refer to several different point in time backups, and then restore the backup sets to the data, which will inevitably lead to performance loss and time consumption. However, synthetic backups only need to refer to the backup at one point in time, so the restore efficiency is improved accordingly when performing restore jobs.

(1) Creating a synthetic backup job is similar to creating a full backup job. Select **Synthetic Backup** as the backup type and choose file folders or files.

![file_merge01](../images/03-file/file_merge01.png)

(2) Select **Backup Target**. The backup target can only be the file synthetic pool. If the file synthetic pool is not listed, please contact the admin to create one.

![file_merge02](../images/03-file/file_merge02.png)

## Create Restore Jobs

This chapter introduces how to restore files. According to the actual needs of users, ADPS provides a variety of restore types including timepoint restore, instant recovery, and recovery testing.

### Create Timepoint Restore Jobs

When folders or files are lost from the system, timepoint restore can be used to restore files to the specified point-in-time state. It supports the restore to local and different host, and the restore to the original location or to the custom path.

(1) Select the File host and instance. Click **Next**.

(2) Select **Timepoint Restore** and the specified point in time of file backup sets. The timepoints of both the incremental and cumulative incremental backup sets are displayed under the full backup as the base. For the listed file directory tree, you can use the search box to further search the specific file resources that need to be restored. The search function supports wildcards (*[]?) search, and fuzzy search. Click **Next**.

![file_restore01](../images/03-file/file_restore01.png)

>Note:
>
>- The search box supports searching matching files, but not directories. You can use multiple filters on Search.
>- The Restore page does not list all the backup sets that are backed up to local storage in detail. You can restore the corresponding backup sets by selecting the time point of a job .

(3) Set **Restore Target**. It supports the restore to the source host or different host, and the restore to Hadoop resource and object storage resource. Click **Next**.

![file_restore02](../images/03-file/file_restore02.png)

(4) Set **Restore Schedule**. It only supports immediate and one-time restore schedules.<br>
(5) Set **Restore Options** including common and advanced options.

- **Common options**:

![file_restore03](../images/03-file/file_restore03.png)

**Channels**: This option can improve backup efficiency. The default value of Channels is 1 and the range is 1 to 255. For details, please refer to *Channel Number Configuration*.

**Restore location**: You can set the restore location as the original path or custom path. The custom path can be entered manually or you can click **Browse** to select the target folder directly in the pop-up window.

**How to handle files with the same name**: Available options include "Overwrite", "Skip", "Retain most recent", "Rename and save".

**Path conversion type**: This option is only available when you set the restore location to the custom path. You can choose from strip directory and path transform.

- **Advanced options**:

![file_restore04](../images/03-file/file_restore04.png)

**Reconnection time**: The job continues after the abnormal reset occurs in the network within the set time. The value can be 1 to 60. The unit is minute(s).

**Speed limit**: Set the limit for data transfer speed or disk read and write speed. The unit can be MiB/s or KiB/s. Click the ‘’+‘’ icon to add limits at different times.

**Precondition**: The precondition is executed before the job starts. The job execution is aborted when the precondition is not met and the job will be in idle state.

**Pre/Post action**: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.

(6) Confirm the restore content and submit the job. Click **Submit**.

### Create Instant Recovery Jobs

File Instant Recovery can instantly recover file backup sets from the storage server by mounting. File Instant Recovery has the advantages of fast recovery speed, low resource consumption, disk space saving and increased availability of backup sets.

> Note:
>
> - The file instant recovery requires the adps-nfsd package to be installed on the server where Storaged is located.
> - File Instant Recovery currently only supports recovering backup sets from standard storage pools without multi-storage and data storage encryption enabled, and from file synthetic pools on Ubuntu.

The interface provides the following two portals to create instant recovery jobs for cloning copies: **Restore** and **CDM**.

#### Create from Restore Menu

(1) Enter the **Restore** main menu. Select the File host and instance. Click **Next**.

(2) Select **Instant Recovery** as the restore type, and select the point in time of the file backup sets. Click **Next**.

![file_restore05](../images/03-file/file_restore05.png)

(3) On **Export** page, set the export directory and access control list of backup sets. Click **Next**.

![file_restore06](../images/03-file/file_restore06.png)

**Export**: Set the mount point to export.

**Access control list**: The list of agents that can mount access to the backup sets. It supports the specified IP or network. **"*"** indicates that any agent can access.

**Bridge**: You can add bridge to export the backup sets and avoid conflict in nfsd usages.

**Path conversion type**: UTF8 path encoding is used by default for file instant recovery.

(4) Confirm whether the job information is correct, and submit the job after confirming that it is correct.

> Note:
>
> - You need to enter the fourth bit of the Bridged IP Address in the Advanced Options manually, and this IP address must be a valid IP address that is not in use on that network.
>
> - To set up the bridge, you need to install bridge-utils and add the following to the configuration file in /etc/network/interfaces:
>
>   ```bash
>   auto br0
>   iface br0 inet static
>   address 192.168.88.10
>   netmask 255.255.255.0
>   gateway 192.168.88.1
>   bridge_ports eth0
>   bridge_stp off
>   bridge_fd 0
>   ```

#### Create from CDM Menu

The CDM interface is used to manage all the data copies on storage servers. You can view the corresponding data copies on a storage server and create instant recovery jobs to clone copies, which allows you use the same data in multiple business situations.

(1) Enter **CDM** menu and filter out the relevant instances by file resource. To run an instant recovery job, select the corresponding host and instance, choose a point in time of a backup set, and click **Create Copy** icon.

![file_cdm01](../images/03-file/file_cdm01.png)

(2) The other operations such as **Export**, and **Finish** are the same as the operations in the previous chapter.

#### Check Successful Recovery

After the instant recovery is completed, enter the **CDM** menu. You can find a recovery record under the backup point in time of the corresponding host and instance. If you need to mount the copy manually, click **Help** icon for reference.

![file_cdm02](../images/03-file/file_cdm02.png)

- Help: Click the **Help** icon to view the help page where you can follow the steps to mount or unmount manually.

- Edit: Click the **Edit** icon to modify the export directory and access control list.
- Delete: Click the **Delete** icon to delete the mounted copy.

#### Detach Copy

You can detach the mounted directory from the agent by clicking the **Detach** icon.

- Click the **Detach** icon beside the data copy record.

  ![file_cdm03](../images/03-file/file_cdm03.png)

  > Note:
  >
  > - Before unmounting the copy, make sure the agent has been uninstalled, if not, you need to uninstall the agent first, then unmount the copy, otherwise the agent will be undergoing a state of Freeze when accessing the mount directory.

- Pay attention to the warning and enter the verification code. Please operate with caution.

  ![file_cdm04](../images/03-file/file_cdm04.png)

### Create Recovery Testing Jobs

With the hourly, daily, weekly, or monthly schedule, recovery testing can restore the latest file backup set to the target host or source host, used to verify that the backup set is available.

(1) Select File host and instance. Click **Next**.

(2) Select **Recovery Testing** as the restore type, select the point in time of the backup sets that you want to restore. Click **Next**.

![file_restore07](../images/03-file/file_restore07.png)

(3) Set **Restore Target**. It supports restoring files to the source host or different host.

(4) Set **Restore Schedule**. It supports hourly, daily, weekly, and monthly schedule types. Click **Next**.

(5) Set **Restore Options**, including channels, restore location, how to handle files with the same name, dry-run, path conversion type, reconnection time, speed limit, precondition, pre action and post action. Click **Next**.

![file_restore08](../images/03-file/file_restore08.png)

**Channels**: Enter the value of Channels to restore. The maximum value cannot exceed the number of channels in the backup sets.

**Restore location**: Original path or custom path

**How to handle files with the same name**: Overwrite, skip, retain most recent, rename and save.

**Dry-run**: Enable dry-run to simulate execution results without specific testing operations. Dry-run log path can be the original path or custom path.

**Path conversion type**: Strip directory and path transform.

**Reconnection time**: The job continues after the abnormal reset occurs in the network within the set time. The value can be 1 to 60. The unit is minute(s).

**Speed limit**: Set the limit for data transfer speed or disk read and write speed. The unit can be MiB/s or KiB/s. Click the ‘’+‘’ icon to add limits at different times.

**Precondition**: The precondition is executed before the job starts. The job execution is aborted when the precondition is not met and the job will be in idle state.

**Pre/Post action**: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.

(6) Confirm the recovery content and submit the job. Click **Submit**.

(7) Wait for the job cycle to be executed, and the latest backup set of the source host will be restored when the restore job is executed.

## Manage Jobs

On the **Job** page, you can view the backup and restore job information of all agents, start, modify, clone, and delete the jobs.

![file_job01](../images/03-file/file_job01.png)


- Start: Click ![](../images/03-file/file_job02.png) to start the job immediately.
- Modify: Click ![](../images/03-file/file_job03.png) to modify the basic job information, the backup/restore schedule, and the backup/restore options.
- Clone: Click ![](../images/03-file/file_job04.png) to create multiple similar backup jobs.
- Delete: Click ![](../images/03-file/file_job05.png) to access the confirmation window. Click **OK** to delete the job.

## Backup Protection Strategy

###  Backup Schedule Operation

ADPS provides six types of backup schedules. The schedule type selected is only valid for the currently created job.

![](../images/03-file/file_time01.png)

- Immediate: The job immediately starts to run after it is submitted.
- One time: After the job is created, it will be in an idle state and start to run when the specified Start time is reached.
- Hourly: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of hours/minutes within the time range according to the setting. If the unit is Hour, then you can set the value from 1 to 24. If you select the Minute as the unit, then you can set the value from 1 to 60.
- Daily: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of days according to the setting. The value is an integer between 1 and 5.
- Weekly: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of weeks according to the setting. You can specify which day of the week to run the job.
- Monthly: The job runs on the specified days of some months at the specified time. For example, you can set the job to run on January 1 and June 1 at 20:00. Or you can set it to run on the first Monday of every month at 20:00.

>  Example: Run the job every two weeks on Friday at 18:00

> ![](../images/03-file/file_time03.png)

> Actual running hours are as follows:
>
> - If the current time is Friday 17:00, the run time is Friday 18:00 (the current day).
> - If the current time is Thursday 17:00, the run time is Friday 18:00 (the next day).
> - If the current time is Saturday 17:00, the run time will be next Friday 18:00.
> - After the first run is completed, the job will start automatically at 18:00 on Friday every two weeks.

### Backup Strategy Advice

There are four backup methods for File: full backup, incremental backup, cumulative incremental backup, and synthetic backup. Full backup, incremental backup, and cumulative incremental backup can be used together. It is recommended to formulate the following backup strategy according to different situations such as network bandwidth, business data volume, security requirements, and the amount of lost data that you can tolerate:

1. When the application traffic is relatively small, run a **Full Backup** once a week to ensure that you have at least one recoverable RTO every week.
2. After that, you can run an **Incremental Backup** every day to reduce the backup time and ensure that you have at least one recoverable RPO every day.
3. You can run **Cumulative Incremental Backup** on demand.

> Note:
>
> - Avoid using the strategies of all full backups or a full backup followed by cumulative incremental backups.

## Channel Number Configuration

- Channel number for backup jobs:

  File supports up to 255 channels. You can set the number of channels for backup and restore jobs according to the actual environment. A reasonable number can improve job performance. The number of channels is generally recommended to be consistent with that of CPU cores. The efficiency improvement will not be obvious if the number of channels exceeds that of CPU cores.

- Channel number for restore jobs:

  The number of channels used cannot exceed the number of channels configured for the backup job.

## Limitations

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Limitations
---
class: longtable
---
| Function                               | Limitations                                       |
| -------------------------------------- | ------------------------------------------------------------ |
| Synthetic Backup                       | The synthetic backup job of a single file under AIX system cannot be greater than 32Tb. |
| Restore                                | Instant recovery only supports recovering backup sets in unencrypted standard storage pools with single-level storage and file synthesis pools. |
| Instant recovery                       | Only available for Linux storage server. {{ br }}Support the non-encrypted standard storage pools with single-level storage. {{ br }}Support File synthetic pool. {{ br }}Do not support to run the mount recovery job to restore device files and character files. |
| Restore backup sets across OS platform | Support restoring file backup sets from Linux to Windows. {{ br }}Support restoring file backup sets from Windows to Linux. {{ br }}The maximum file name for Windows NTFS file systems is 255 character. {{ br }}The maximum file name for Linux ext4 file systems is 255 character. |
| Recovery testing                       | Do not support restoring backup sets of Linux file to the object storage. {{ br }}Do not support restoring backup sets of Windows file to Hadoop. |
```

## Glossary

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Glossary
---
class: longtable
---
| Term             | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| Fast Compression | Compress data during backup using fast compression algorithms. |
```