# User Guide for Linux OS

## Overview

This guide introduces how to install and configure the ADPS agent and how to properly use ADPS to back up and restore Linux OS.

The backup and restore features supported by ADPS include:

- Backup sources

	Linux OS partition

- Backup types

	Full backup, incremental backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, reconnection, speed limit, and handling sparse files

- Restore types

	File-level restore, OS-level recovery

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

To back up and restore Linux OS, first install the ADPS agent on the host where Linux OS resides.

### Verify the compatibility

Before you install the agent, ensure that the environment of the host where Linux OS resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of various Linux distributions, such as Debian, RedHat, and their derivatives. Supported Linux distributions include:

- Red Hat 4/5/6/7/8/9
- Ubuntu 12.04/14.04/16.04/18.04/20.04/22.04
- SLES 9/10/11/12 SP1
- openSUSE 10.3/11.4/15.5
- Debian 6.0/7.0/7.5/7.6/7.8/8.0/9.0
- Scientific Linux 4/5/6
- AlmaLinux 8.5
```{only} scutech
- Kylin Phytium 1500A 4.02
- Kylin V10 SP3
- NeoKylin Loongson 3A 6.0/7.0
- NeoKylin Sunway/Loongson 3B 6.0
- NeoKylin 7.0
- i-soft Loongson 3B 5.0
- TencentOS 2.4
- Asianux Server 3 SP4/4 SP4/4 SP5/7 SP3
- Rocky 4.2/6.0/9.3
- RedFlag-DC-Server 5.0 SP4
- Linx 6.0.60.4
- openEuler 20.03/22.03
```


### Install the agent

The ADPS agent can be installed on Linux. You can select the installation method according to your environment.

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

	(2) From the **Component** list, select **OS**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** checkbox.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

  ```{code-block} python
  [root@centos7 ]# curl -o- "http://192.168.18.252:80/d2/update/script?modules=bmr&ignore_ssl_error=&access_key=7dc57757b7e675f2ec5495180f90ac70&rm=&tool=curl" | sh
  ```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Linux OS, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. On the **Resource** page, select the host where Linux OS resides. Click the **Register** icon. After the registration, the **Activate** window appears.

3. In the **Activate** window, select the Linux OS backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup types

ADPS provides two common backup types for Linux OS.

- Full backup

	Backs up the disk partition data of the operating system.

- Incremental backup

	Backs up all data that has changed since the last backup with a full backup as the baseline.

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

Before you back up and restore Linux OS, check the following:

1. Check storage pools

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

### Log in to the instance

Before you create a backup or restore job, log in to the Linux OS instance on the console. ADPS provides two authentication methods for Linux OS:

- OS authentication

	Use an operating system user to log in the resource.

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
2. From the host list, find the host where the Linux OS resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.

4. In the **Login** window, select an authentication method according to your needs.

	- Select **OS authentication**, enter the **Username** and **Password** of the OS user, and click **Login**.
	- Select **Access key**, enter the access key of the current ADPS user, and click **Login**.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.


## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where Linux OS resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the disk partitions that you want to back up.

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

ADPS provides the following backup options for Linux OS:

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
|Handle sparse files|The option is enabled by default. Sparse files will be handled during a backup job.||
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

For different needs, ADPS provides several restore types for Linux OS, including OS-level recovery and file-level restore.

### Create an OS-level recovery job

````{note}
Before you creates an OS-level recovery job, please check:

- The hard disk space of the restore target is greater than or equal to that of the source server.
- If the source server has multiple hard disks, the restore target requires the same number of hard disks.
- With LVM configuration, you can recover the Linux OS to a smaller volume group, but make sure that there is enough space to store the data.
- The architectures currently supported by the OS-level recovery job are:
	-  x86-64 architecture
	-  i686 architecture
	-  arm64 architecture
	-  alpha architecture
	-  mips64el architecture
```{only} scutech
  -  loongarch64 architecture.
```
````
To create an OS-level recovery job, do the following:

1. Use burning software to burn adps_bootable_recovery-version.x86_64.iso to a media such as CD or DVD.

2. Put the recovery media into the optical drive of your PC that executes the recovery job. To enter the OS-level recovery console, set the temporary system to boot from the recovery media, select the default item, and press "Enter".

```{note}
To access the OS-level recovery console via the browser, enter the following IP address: http://IP/d2/agent/

Press "ALT + q" on the OS-level recovery console to enter the command line of the temporary system, where you can view the recovery logs and the IP address. You can also use `ssh` to remote access the IP address. The default username and password is root/root.

Use the command `LC_ALL=en.US.UTF8 /opt/ADPS/adps/bin/recovery_ui -qws` to back to the OS-level recovery console from the command line.

If two hosts are running at the same time after the recovery job, modify one of the host’s host_uuid to avoid conflicts caused by the same agent environment.
```

3. After the temporary system starts, you can see the OS-level recovery console. On the console, do the following:

   (1) On the **Basic** page, select **Language**, **Time zone**, **Time**, and click **Next**. The **Network configuration** page appears.

   (2) On the **Network configuration** page, there are two sections:

	The top tab in the left section allows for the selection of the NIC (if the machine is equipped with multiple NICs). By default, the first NIC is enabled and the **MAC address** is automatically retrieved. If the NIC is enabled, the **NIC status** will display a green indicator. The adjacent toggle can be used to **Enable** or **Disable** the selected NIC.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Linux-os/linux-os-06.png)
	```

	If the MAC address is not automatically retrieved, in the right section, under the corresponding network type tab (IPv4, IPv6 Link-Local, IPv6 Global), the **Method** option should be changed to **Manual**. You can manually input the **IP address**, **Netmask**, **Default gateway**, **Preferred DNS**, and **Alternate DNS**. Then, click **Modify** to apply the changes.

	(3) Click **Next**, the **Restore source** page appears. On this page, select a restore source: **Backup server - password authentication**, **Backup server - API key authentication**, and **Local device**.

- **Backup server - password authentication**: Enter the **Address** of the backup server, **Username** and **Password**. The right section automatically displays relative backup sets. Select a backup set. Click **Next**.

- **Backup server - API Key authentication**: Enter the **Address** of the backup server, **Username** and **API key**. Click **OK**. The right section automatically displays relative backup sets. Select a backup set. Click **Next**.

- **Local device**: all disc devices connected to the server will be listed.

	(4) On the **Disk settings** page, the disk is allocated automatically by default. You can also click **Manual** > **Select destination disk** to specify a disk path. Then click **Next**.

	(5) On the **Advanced settings** page, the **Ignore creation of BIOS boot partition** option is not enabled by default. The BIOS boot partition is created by default on a BIOS-based computer when the device is GPT-partitioned. Then from the **Select network** list, select a network. Click **Next**.

	```{only} scutech
	The system on SUNWAY may be prevented from booting. Consider using **Ignore creation of BIOS boot partition** option for SUNWAY.
	```
	(6) On the **Summary** page, check the job configurations. Click **Next**. You will see a progress bar for the recovery job.

	(7) After the job is completed, click **Reboot** and eject the optical disc. Once the server is rebooted, you can enter the operating system.

### Create a file-level restore job

To create a file-level restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where Linux OS resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	(1) In the **Restore source** section, select a backup set point in time for the restore job.

	(2) In the **File** section, select files, folders or disks that you want to restore.

	(3) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step. The restore target supports other instances on the original or a different host.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

  - Select **Immediate**. ADPS will perform the job immediately after its creation.
  - Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for Linux OS:

- Common options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore common options
---
class: longtable
---
|Option |Description|Limitations|
| --- | --- | --- |
|Restore partition information|Enabling this option will restore the entire partition, including all files within the partition, to the default path.|LVM partiton is not supported.|
|Restore location|The option appears when the **Restore partition information** option is enabled. To restore files on the root partition, we recommend selecting a new path. Restoring them to the original path may cause the job to fail.||
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
|Backup|Backing up the btrfs filesystem of the SUSE Linux Enterprise is not supported.|
|File-level restore|The Restore partition information function only supports the recovery of standard partitions, and does not support LVM partitions.|
|OS-level recovery|Do not support recovery from a LAN-free storage pool.|
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