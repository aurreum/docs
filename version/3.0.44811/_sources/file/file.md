# User Guide for File

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore files.

The backup and restore features supported by ADPS include:

- Backup sources

	Single or multiple directories and files

- Backup types

	Full backup, incremental backup, cumulative incremental backup, and synthetic backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, file synthetic pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore, instant recovery, and recovery testing

- Restore targets

	Original host, different host, different operating system (restore between Windows and Linux), and different architecture (restore files from an operating system to object storage or Hadoop)

- Restore options

	Incremental restore, restore location (original path or customized path), dry-run, and handle files with the same name


## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore files, first install the ADPS agent on the host where files reside.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where files reside is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of files on multiple systems, including:

- Windows XP/7/8/10/2000/2003/2008/2012/2016/2019
- Ubuntu 12.04/14.04/16.04/18.04/20.04
- Red Hat 4/5/6/7/8/9
- CentOS 3/4/5/6/7
- AIX 5/6/7
- SLES 9/10/11/12
- Solaris 9/10/11
- HP-UX B.11
- Debian 6/7/8/9
- openSUSE 10/11
```{only} scutech
- Kylin V4/V10
- NeoKylin 4/6/7
- UOS V20
- Asianux Server 3/4/7
- openEuler 20.03
- Linx, Inspur, iSoft, NFS-CHINA
```

### Install the agent

The ADPS agent can be installed on Windows, Linux, and other operating systems. You can select the installation method according to your environment.

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
7. At the **Select components** step, select **File** from the component list. Click **Next**.
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

	(2) From the **Component** list, select **File**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

	```{code-block} python
	root@ubuntu:~# curl -o- "http://192.168.17.31:50305/d2/update/script?modules=file&location=http%3A%2F%2F192.168.17.31%3A50305&access_key=572bd4dbb395fd320a30fe9729a21db8&rm=&tool=curl" | sh
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	100  9204    0  9204    0     0  2247k      0 --:--:-- --:--:-- --:--:-- 2996k
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 51.1M  100 51.1M    0     0   141M      0 --:--:-- --:--:-- --:--:--  141M
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 5684k  100 5684k    0     0  48.6M      0 --:--:-- --:--:-- --:--:-- 99.0M
	  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
	                                 Dload  Upload   Total   Spent    Left  Speed
	  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100  733k  100  733k    0     0  11.5M      0 --:--:-- --:--:-- --:--:-- 11.5M
	```

7. Wait for the installation to complete.

#### Other operating systems

Other operating systems include Solaris, AIX, Linux ARM, and domestic operating systems. For more information about installing agents on other operating systems, see [Install the agent](../agent_install/agent_install.md#install-the-agent) in Aurreum Data Protection Suite Agent Installation Guide.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore files, register the host, activate the file backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where the files reside. Click the **Register** icon. After the registration, the **Activate** window appears. Click **Submit**.

	```{only} scutech
	![](../images/Common/register_resources.png)
	```

3. After you activate the license, the **Configure** window appears. In the **Configure** window, do the following:

	(1) In the **Name** field, enter a name for the host.

	(2) From the **Data network** list, select a network from those added on the **Network** page as the data network.

	(3) In the **Preferred egress network** field, enter an IP address for the preferred network traffic egress of the host's backup data. IPv4 and IPv6 are supported.

	(4) From the **User group** list, select user groups to authorize access to the resource.

	(5) Set the resource as **Protected** or not. The **Protected** resource cannot be the restore target or the data replication target unless the administrator removes the **Protected** label.

    ```{only} scutech
	![](../images/Common/configure_resources.png)
  	```

	```{note}
	1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
	2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Backup

### Backup types

ADPS provides three common backup types (full backup, incremental backup, and cumulative incremental backup) and one advanced (synthetic backup) for files.

- Full backup

	Backs up the directories or files on the operating system and copies all the directories and files at some point in time.

- Incremental backup

	Backs up only the files that have changed since the last backup (including the full backup, the incremental backup, and the cumulative incremental backup) with a full backup as the baseline.

- Cumulative incremental backup

	Backs up only the files that have changed since the last full backup with a full backup as the baseline.

- Synthetic backup

	The first synthetic backup is a full backup and the subsequent ones are incremental. When the synthesis condition is reached, the latest full backup and subsequent incremental backup will be synthesized to create a new full backup copy. Synthetic backups can improve the restore performance. You can directly mount the full backup copy to the target host through an instant recovery job without physical copies and additional storage space.

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

Before you back up and restore files, check whether any storage pools have been created and authorized.

1. From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.
2. Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

	```{note}
	To use synthetic backups, ensure that your environment fulfills the following requirements:
	- You have advanced licenses: File Synthetic Backup and File Copy Data Management.
	- You have created a file synthetic pool for the current user.
	```

```{only} scutech
![](../images/Common/storage_pool.png)
```

### Log in to the resource

Before you create a backup or restore job, log in to the file resource. ADPS provides two authentication methods for files:

- OS authentication

	You can use the operating system (OS) user to log in to the resource.

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
2. From the host list, find the host where the files reside. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Common/file_login.png)
	```

4. In the **Login** window, select an authentication method according to your needs.

	- Select **OS authentication**, enter the **Username** and **Password** of the OS user, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where the files reside and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	```{note}
	For incremental and cumulative incremental backups, the **Backup source** step only requires a full backup selected as their baseline and there is no need to select the directories and files again.
	```

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_backup_01.png)
	```

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, click + to expand folders and select the files or folders that you want to back up.

	(3) If you want to filter the selected files and folders in the **Backup source**, click **Filter** below the **Backup source** field. The **Filter** window appears.

	```{note}
	Some directories and files are forced to be excluded from the backup source while some are excluded by default. For those default excluded directories and files, you can click x to remove them from the exclusion so that they can be backed up.

	On Linux:
	- The following directories are forced to be excluded from the **Backup source**: `/proc`, `/sys`, `/etc/opt/aurreum`, `/var/opt/aurreum`.
	- The following directories are excluded by default: `/tmp`, `/var/tmp`, `/var/run`, `/run`, and `/dev/shm`.

	On Windows:
	- The following directories are forced to be excluded from the **Backup source**: `pagefiles`, `System Volume Information`.
	- The following files are excluded by default: `*.tmp`.
	```

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_backup_03.png)
	```

	In the **Filter** window, you can do the following:
	- The **Exclusion** option is enabled. You can deselect the checkbox to disable the filter. If you want to exclude some directories or files from the backup job, enter the directories and files in this field.
	- You can select the **Inclusion** checkbox and enter the directories and files in case their parent directories are listed in the **Exclusion** field.

	```{note}
	For example, there are directories `/data` and `/test`. `/test` has hundreds of files. Some are `.txt`, some are `.dat`, and so on. The whole directory `/data` and all the `.txt` files under the directory `/test` need to be backed up.
	1. First select `/test` and `/data` in the **Backup source**. Then open the **Filter** window.
	2. Enter `/test` in the **Exclusion**.
	3. Select the **Inclusion** checkbox and enter `*.txt` in the field.
	4. The backup result will be `/data` with all the data and `/test` with only `.txt` files.
	```

	Here is an example of using the wildcard `*` in **Filter**. Assume that the backup source includes the following directories and files:

	```{code-block} python
	root@ubuntu:/# tree /backup/
	/backup/
	└── test
    	├── group_1
		│   └── sub_group
    	│       ├── file1.dat
    	│       └── file1.txt
    	├── group_2
    	│   └── sub_group
    	│       ├── file2.dat
    	│       └── file2.txt
    	└── no_group
	```

	```{tabularcolumns} |\Y{0.2}|\Y{0.25}|\Y{0.55}|
	```
	```{table} Filter example
	---
	class: longtable
	---
	|Exclusion|Inclusion|Result|
	| --- | --- | --- |
	|`/backup/*`|`/backup/test/group_*/*`|Directories `group_1` and `group_2` and all their subdirectories are backed up|
	|`/backup/*`|`*.txt`|`.txt` files, their directories, and the directory `no_group` are backed up|
	|`*.txt`||All the directories and files except `.txt` files are backed up|
	```

4. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
	Incremental and cumulative incremental backups do not have the **Backup target** step because their backup target is the same as their baseline full backup selected at the **Backup source** step.
	```

5. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_backup_02.png)
	```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, clone, and delete the job.

### Backup options

ADPS provides the following backup options for files:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.2}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.||
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 255.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.|Only available for full backup and synthetic backup jobs.|
```

- Advanced options

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.2}|
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
|Snapshot|Enabled by default. Snapshots are used for consistent backups when some files are locked.|Only available for Windows OS.|
|Pre-/Post-script for snapshot|The pre-script is executed after the job starts and before a snapshot is taken for the resource. The post-script is executed after a snapshot is taken for the resource. The Ignore errors in snapshots and scripts option is enabled by default.  |Only available for Windows OS.|
|Backup NTFS ADS and ACLs|Enabled by default. BackupRead API will be used to back up NTFS Alternate Data Stream and Security Information. It can prevent CreateFile failures encountered when a CIFS file system without ADS and ACLs is being backed up.|Only available for Windows OS.|
|Backup retry count|Number of retries after the backup fails due to some reasons including network errors. The value ranges from 0 to 10. 0 means no retries.||
|Backup retry interval|Time interval between backup retries after the backup fails due to some reasons including network errors. The value ranges from 0 to 120 minutes. The default value is 30 minutes.||
|Detect sparse files|Auto is selected by default. It will detect sparse files with a small amount of valid data.||
|Cross mount points|Enabled by default. Files in the selected directories will not be skipped. When the option is disabled, the job will only back up files that are stored in the same file system as the parent directory. Example: There are three directories: a, b, and c. Directory a and directory b is in the XFS file system. Directory c is in tmpfs. Their parent directory is in XFS. If you disable the option, the job will only back up directory a and directory b because they are stored in the same file system as their parent directory. ||
|Reparse Point types|Select dedup when Data Deduplication is enabled on Windows OS.|Only available for Windows OS.|
```

## Restore

### Restore types

For different needs, ADPS provides several restore types for files, including:

- Point-in-time restore

	Restores the folders or files to a specified point in time. The restore target can be the original host, a different host, the original path, and a customized path.

- Instant recovery

	Achieves fast recovery by mounting the file backup sets from the storage server with the following advantages: fast recovery speed, little resource consumption, reduced disk space, and improved availability of backup sets.

- Recovery testing

	Restores the latest backup sets to another path on the original host or a different host hourly, daily, weekly, and monthly.

### Before you begin

To restore files to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where the files reside and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_timepoint_restore_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Point-in-time restore**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) In the **File** section, select files for the restore job. All the files in the backup set will be selected by default. You can select or deselect the files, and search files in the search bar. Note that files in backup sets from local storage pools and LAN-free pools cannot be listed in the **File** field.

    ```{note}
    The search bar supports:
	- File name but not directory name
	- Multiple rules at one time
	- `?` and `*`. `?` matches one character, and `*` matches multiple characters

	Example:

	There are five files: `test_1.txt`, `test_2.txt`, `1_test.txt`, `2_test.txt`, and `file.txt`. Enter `test*txt ?_test.txt file.txt` in the search bar will match all the five files.
    ```

	(5) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create an instant recovery job

```{note}
1. The `adps-nfsd` package should be installed on the storage server for file instant recovery.
2. File instant recovery only supports backup sets from standard storage pools (with neither **Multi-storage** nor **Data storage encryption** enabled) and file synthetic pools.
```

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where the files reside and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_instant_recovery_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Instant recovery**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) Click **Next**.

4. At the **Export** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_instant_recovery_02.png)
	```

	(1) In the **Export** field, set the mount point for the export. The path must begin with / and can be 2-30 characters with letters or numbers.

	(2) In the **Access control list** field, add the agents that can mount and access the backup set. It supports IP addresses and network segments. * means all agents can access the backup set.

	(3) From the **Conversion path coding** list, select None, GBK, GB18030, or BIG5. None is selected by default, in which UTF8 is used for instant recovery.

	(4) Expand the **Advanced options**. From the **Bridge** list, select whether to use a network bridge or not. **None** is selected by default. You can use the network bridge to export backup sets to avoid conflicts with the NFS service of the operating system.

    ```{note}
	1. To use a network bridge, enter the IP address, subnet mask, and default gateway. The IP address must be a valid address that is not used in this network segment.
	2. Install `bridge-utils` on the storage server for bridge settings, with which ADPS can recognize the network bridge after it is started. Add the following content to the configuration file `/etc/network/interfaces`:
		```{code-block}
		auto br0
		iface br0 inet static
		address 192.168.88.10
		netmask 255.255.255.0
		gateway 192.168.88.1
		bridge_ports bond0
		bridge_stp off
		bridge_fd 9
		bridge_hello 2
		bridge_maxage 12
		```
	```

5. At the **Finish** step, confirm the job information and click **Submit**.
6. After the submission, you will be redirected to the help page. Mount the files manually according to the procedure. On the **CDM** page, a copy with a mounted state is added below the selected point in time. For more details, see [View a copy](#view-a-copy).

### Create a recovery testing job

To create a recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where the files reside and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_recovery_testing_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Recovery testing**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) In the **File** section, select files for the restore job. All the files in the backup set will be selected by default. You can select or deselect the files, and search files in the search bar. Note that files in backup sets from local storage pools and LAN-free pools cannot be listed in the **File** field.

    ```{note}
    The search bar supports:
	- File name but not directory name
	- Multiple rules at one time
	- `?` and `*`. `?` matches one character, and `*` matches multiple characters

	Example:

	There are five files: `test_1.txt`, `test_2.txt`, `1_test.txt`, `2_test.txt`, and `file.txt`. Enter `test*txt ?_test.txt file.txt` in the search bar will match all five files.
    ```

	(5) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for files:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.2}|
```
```{table} Restore common options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Channels|It can improve restore efficiency. The default value is 1. The value cannot exceed that of the backup set.||
|Restore location|You can set the restore location to the original location or a specified location. To specify a location, enter the path manually or click Browse to select the target folder.||
|Handle files with the same name|Overwrite, skip, retain most recent, rename and save||
|Path conversion type|Only when you specify a restore location can you set the path conversion type, including **Strip directory** and **Path transform**. {{ br }}- **Strip directory levels**: Set directory levels that you want to strip. Example: There is a directory A/B/C in the backup set and the value of **Strip directory levels** is 2. Then A and B will be stripped and only C will be restored. The files under directories A and B will be restored to the specified restore location. {{ br }}- **Path transform**: Rename the directory in the backup set. Example: There is a directory A/B/C. You can change A into D.||
|Incremental restore|Only when you select an incremental backup set for the restore will this option become available. It is disabled by default. If you enable this feature, the job will only restore the incremental data at the selected point in time.|Only available for point-in-time restore jobs.|
|dry-run|For simulating the execution results without specific recovery testing operations. If enabled, it requires a path for dry-run logs.|Only available for recovery testing jobs.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.2}|\Y{0.6}|\Y{0.2}|
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
|Use uid/gid for restore|If enabled, it will use uid/gid for the restore job if the target system does not have user/group names.|Not available for Windows OS.|
```

## Copy data management

On the **CDM** page, you can manage the copies generated by instant recovery and synthetic backup jobs, including viewing, cloning, unmounting, and deleting copies.

### View a copy

To view copies, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows copies of this resource in this period. Copies are named by the creation time.
3. Click the copy name. The details of this copy appear on the right side of the page. Different icons represent different copy types.

	- Full copy: A data copy created by a synthetic backup.
	- Mounted copy: A data copy created by an instant recovery.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_cdm_01.png)
	```

### Clone a copy

You can click the **Clone copy** icon to create an instant recovery job for the synthetic copy to create a new mounted copy.

To clone copies, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. On the display area, click a full copy under the resource. The **Clone copy** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_cdm_02.png)
	```

4. Click the **Clone copy** button. You will be redirected to the **Backup sets** step. See [Create an instant recovery job](#create-an-instant-recovery-job) to configure the job.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_cdm_03.png)
	```

5. After the instant recovery, a copy with a mounted state is added below the selected point in time on the **CDM** page.

### Unmount a copy

You can click the **Unmount** icon to unmount the mounted copies. This operation will make the mounted directory on the restore target inaccessible.

To unmount a copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select a mounted copy. The **Unmount** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/File/file_cdm_04.png)
	```

4. Click the **Unmount** icon. A confirmation window appears.
5. Confirm the warning and enter the verification code. Click **OK**.
6. After the unmounting, you can see no such mounted copy record under the full copy.

## Limitations

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Limitations
---
class: longtable
---
|Feature|Limitations|
| --- | --- |
|Synthetic backup|A file synthetic backup job for the AIX system cannot be larger than 32T.|
|Instant recovery|Only Linux storage server supports instant recovery. {{ br }}It supports standard storage pools with neither Data storage encryption nor Multi-storage enabled.{{ br }}It supports file synthetic pools.{{ br }}It does not support character device files and block device files.|
|Cross-system restore|ADPS supports restoring file backup sets from Linux to Windows.{{ br }}ADPS supports restoring file backup sets from Windows to Linux.{{ br }}The maximum file name for the NTFS file system on Windows is 255 characters.{{ br }}The maximum file name for the ext4 file system on Linux is 255 bytes.|
|Recovery testing|It does not support recovering file backup sets from Linux to object storage.{{ br }}It does not support recovering file backup sets from Windows to Hadoop.|
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
|cross-system restore|A restore method that restores files from Windows to Linux and vice versa.|
|different-architecture restore|A restore method that restores files from operating systems to object storage or Hadoop.|
|dry-run|A method that manipulates execution results without specific recovery testing operations. A path for dry-run logs must be specified.|
```