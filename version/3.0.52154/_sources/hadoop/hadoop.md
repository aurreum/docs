# User Guide for Hadoop

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore Hadoop.

The backup and restore features supported by ADPS include:

- Backup sources

	Single or multiple directories and files on Hadoop Distributed File System (HDFS)

- Backup types

	Full backup, incremental backup, and synthetic backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, file synthetic pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly.

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, and replication

- Restore types

	Point-in-time restore, instant recovery, and recovery testing

- Restore targets

	Original host, different host, different system (restore files between Hadoop and Linux), and different architecture (restore files from Hadoop to an operating system or object storage)

- Restore options

	Incremental restore, restore location (original path or customized path), and process invalid paths


## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore Hadoop, first install the ADPS agent on a host that can communicate with Hadoop.

### Verify the compatibility

Before you install the agent, ensure that the Hadoop environment is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of Hadoop of multiple versions, including:

- Hadoop 2.2.x/2.6.x/2.7.x/2.8.x/2.9.x/3.0.x/3.1.x/3.2.x
- CDH 6.0/6.1/6.2/6.3
```{only} scutech
- FusionInsight HD V100R002C50/V100R002C60/V100R002C70/V100R002C80
```

### Install the agent

The ADPS agent can be installed on Linux and supports online and offline installation. We recommend online installation.

1. Online installation:
	ADPS provides `curl` and `wget` commands for installation.
2. Offline installation:
	See [Offline installation](../agent_install/agent_install.md#offline-installation) in Aurreum Data Protection Suite Agent Installation Guide.

Before you install the Hadoop backup agent, install the Hadoop runtime environment on the backup host.

- Unzip the Hadoop runtime environment offline package to a directory (C, in this example):

	```
	$ sudo tar -axf hadoop-2.10.0.tar.xz -C <dir>
	```

	`hadoop-2.10.0` appears in directory C.

- Install OpenJDK:

	```
	$ sudo tar -axf Ubuntu20.04-OpenJDK11-AMD64.tar.gz
	```

	`openjdk11` appears in the directory.

	Only install the `openjdk-11-jre-headless` environment:

	```
	$ sudo dpkg -i openjdk11/*.deb
	```

```{note}
Fill in the JRE directory and version. The default directory is `/usr/lib/jvm/jre`.
```

To install the agent online, do the following:

1. Log in to the ADPS console.
2. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
3. From the toolbar, click the **Install agent** icon. The **Install agent** window appears.
4. In the **Install agent** window, do the following:

	(1) From the **Select system** list, select **Linux**.

	(2) From the **Component** list, select **Hadoop**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

	```{code-block} shell
	root@ubuntu:~# curl "http://192.168.17.90:50305/d2/update/script?modules=hadoop&location=http%3A%2F%2F192.168.17.90%3A50305&access_key=929a401135dc8f06efbc29c3ea86e3f9&rm=yes&tool=curl" | sh
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
	100  9205    0  9205    0     0  1797k      0 --:--:-- --:--:-- --:--:-- 2247k
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
	0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 52.3M  100 52.3M    0     0   297M      0 --:--:-- --:--:-- --:--:--  297M
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
	0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 5730k  100 5730k    0     0  81.1M      0 --:--:-- --:--:-- --:--:-- 81.1M
	% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
	0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
	100 1576k  100 1576k    0     0  7690k      0 --:--:-- --:--:-- --:--:-- 7690k
	```

7. Wait for the installation to complete.

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Hadoop, register the host, activate the Hadoop backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the backup host. Click the **Register** icon. After the registration, the **Activate** window appears. Click **Submit**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/register_hadoop_proxy.png)
	```

3. After you activate the license, the **Configure** window appears. In the **Configure** window, do the following:

	(1) In the **Name** field, enter a name for the host.

	(2) From the **Data network** list, select a network from those added on the **Network** page as the data network.

	(3) In the **Preferred egress network** field, enter an IP address for the preferred network traffic egress of the host's backup data. IPv4 and IPv6 are supported.

	(4) From the **User group** list, select user groups to authorize access to the resource.

    ```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/configure_hadoop_proxy.png)
  	```

	```{note}
	1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
	2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
	```

## Add and activate a Hadoop cluster

### Add a Hadoop cluster

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the toolbar, click the "+" icon and select **Hadoop cluster**. The **Add Hadoop cluster** window appears.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_01.png)
	```

3. ADPS supports two authentication methods for Hadoop clusters: Simple and Kerberos. If you have configured your Hadoop cluster with the Kerberos authentication service, select Kerberos when adding the cluster. If your Hadoop cluster does not have the Kerberos service, you can use the default Simple authentication. You can click the "+" icon in the bottom left corner of the window to add multiple NameNodes.

	In the **Add Hadoop cluster** window, do the following:

	(1) Create the Hadoop resource.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_02.png)
	```

	- In the **Name** field, enter a name for the cluster.
	- From the **Backup host** list, select a host to list backup content and as the default host for backups and restores.
	- In the **Host** field, enter the IP or the name of the host where the NameNode resides. If you have created Principal using the hostname when configuring the Kerberos authentication, then enter the hostname in the **Name** field, and add the IP and corresponding hostname in the `hosts` file on the selected backup host.
	- Enable **SSL** only when the Hadoop cluster has configured and enabled the HTTPS service. Otherwise, deselect the option.
	- In the **REST API port** field, enter the port number. The default number for HTTP is 50070, and for HTTPS is 50470. Modify the port number according to your cluster configuration. You can check whether a port number is configured for the `dfs.namenode.http(s)-address` parameter in the `hdfs-site.xml` file. If no port number is configured, use the default number in this field.
	- In the **RPC API port** field, enter the port number. The default number is 8020. Modify the port number according to your cluster configuration. You can check whether a port number is configured for the `fs.defaultFS` parameter in the `core-site.xml` file. If no port number is configured, use the default number in this field.
	- In the **User** field, enter an HDFS user. If HDFS is configured with the Kerberos authentication, enter the user of Principal authenticated in the `keytab` file. Example: There is a Principal user `test@HADOOP.COM`. Then enter `test` in this field.

	(2) Simple authentication

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_03.png)
	```

	- From the **Authentication method** list, select **Simple**.
	- In the **`core-site.xml` file** field, upload the `core-site.xml` file of the cluster. This option is not required for the **Simple** authentication.
	- In the **`hdfs-site.xml` file** field, upload the `hdfs-site.xml` file of the cluster. This option is not required for the **Simple** authentication.

	(3) Kerberos authentication

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_04.png)
	```

	- From the **Authentication method** list, select **Kerberos**.
	- In the **Realm name** field, enter the Realm name that you configured for Kerberos.
	- In the **Realm KDC server** field, enter the IP or hostname of the Realm KDC server. The default port number is 88. Add the number if your port is different from the default number.
	- In the **Realm Admin server** field, enter the IP or hostname of the Realm Admin server. The default port number is 88. Add the number if your port is different from the default number.
	- In the **RPC API Principal** field, enter the name of the PRC API Principal. You can check the name in the `keytab` file. Example: `klist -k -t test.keytab`.
	- In the **REST API Principal** field, enter the name of the REST API Principal. You can check the name in the `keytab` file. Example: `klist -k -t test.keytab`.
	- In the **UDP preference limit** field, set the maximum value for the UDP transport package. When the data package exceeds this value, TCP will be used for the transport. The default value is 1, with which TCP is used for the transport. Adjust this value according to the parameter in the `/etc/krb5.conf` file of the KDC service.
	- For the **`krb5.keytab` file** field, get the `keytab` file and copy it to a safe location on the host that has access to the ADPS console.
	- In the **`core-site.xml` file** field, upload the `core-site.xml` file of the cluster. This option is required for the **Kerberos** authentication.
	- In the **`hdfs-site.xml` file** field, upload the `hdfs-site.xml` file of the cluster. This option is required for the **Kerberos** authentication.

### Activate the Hadoop license

To activate the Hadoop license and authorize users, do the following:

1. After you add the Hadoop cluster, a window to activate the Hadoop license appears. Click **Activate**.

2. After activation, click **Authorize** beside the Hadoop resource. The **Authorize** window appears.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_05.png)
	```

3. In the **Authorize** window, do the following:

    (1) From the **User group** list, select user groups to authorize access to the resource.

    (2) Set the resource as **Protected** or not. The **Protected** resource cannot be the restore target or the data replication target unless the administrator removes the **Protected** label.

    ```{only} scutech
    ![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_06.png)
    ```

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

If the parameters of the added cluster have changed, including the IP, port, and authentication method, you can click **Configure** to modify the configuration.

```{only} scutech
![](../images/Backup_Restore/DBackup3/Hadoop/add_hadoop_resources_07.png)
```

## Backup

### Backup types

ADPS provides two common backup types (full backup and incremental backup) and one advanced (synthetic backup) for Hadoop.

- Full backup

	Backs up the Hadoop directories or files and copies all the directories and files at some point in time.

- Incremental backup

	Backs up only the files that have changed since the last backup (including the full backup and the incremental backup) with a full backup as the baseline.

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

To use the advanced synthetic backup, we recommend the following backup policy:

- Perform a **synthetic backup** every day to ensure that you have a recoverable point in time every day.

### Before you begin

Before you back up and restore Hadoop, check whether any storage pools have been created and authorized.

1. From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.
2. Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

	```{note}
	To use synthetic backups, ensure that your environment fulfills the following requirements:
	- You have advanced licenses: Hadoop Synthetic Backup and Hadoop Copy Data Management.
	- You have created a file synthetic pool for the current user.
	```

```{only} scutech
![](../images/Common/storage_pool.png)
```

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the Hadoop host and resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	```{note}
	For incremental backups, the **Backup source** step only requires a full backup selected as their baseline and there is no need to select the directories and files again.
	```

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_backup_01.png)
	```

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, click + to expand folders and select the files or folders that you want to back up.

	(3) If you want to filter the selected files and folders in the **Backup source**, click **Filter** below the **Backup source** field. The **Filter** window appears.

	- The **Exclusion** option is disabled by default. If you want to exclude some directories or files from the backup job, enter the directories and files in this field.
	- You can select the **Inclusion** checkbox and enter directories and files in case their parent directories are listed in the **Exclusion** field.

	```{note}
	For example, there are directories `/data` and `/test`. `/test` has hundreds of files. Some are `.txt`, some are `.dat`, and so on. The whole directory `/data` and all the `.txt` files under the directory `/test` need to be backed up.
	1. First select `/test` and `/data` in the **Backup source**. Then open the **Filter** window.
	2. Enter `/test` in the **Exclusion**.
	3. Select the **Inclusion** checkbox and enter `*.txt` in the field.
	4. The backup result will be `/data` with all the data and `/test` with only `.txt` files.
	```

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_backup_02.png)
	```

	Here is an example of using the wildcard `*` in **Filter**. Assume that the backup source includes the following directories and files:

	```{code-block} shell
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

4. At the **Backup host** step, select a backup host and resource. The wizard goes to the next step automatically.
5. At the **Backup target** step, select a storage pool. Click **Next**.

	```{note}
	Incremental backups do not have the **Backup target** step because their backup target is the same as the selected full backup at the **Backup source** step.
	```

6. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

7. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_backup_03.png)
	```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
9. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for Hadoop:

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
|Snapshot|Enable this option to perform Hadoop snapshot backups. It is disabled by default.|Only available for full backup and synthetic backup jobs. The setting of incremental backups is the same as that of the baseline full backup.|
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
```

## Restore

### Restore types

For different needs, ADPS provides several restore types for Hadoop, including:

- Point-in-time restore

	Restores the Hadoop folders or files to a specified point in time. The restore target can be the original host, a different host, the original path, and a customized path.

- Instant recovery

	Achieves fast recovery by mounting the Hadoop backup sets from the storage server with the following advantages: fast recovery speed, little resource consumption, reduced disk space, and improved availability of backup sets.

- Recovery testing

	Restores the latest backup sets to another path on the original host or a different host hourly, daily, weekly, and monthly.

### Before you begin

To restore Hadoop to a different host, install the agent on that host or register the Hadoop resource, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the Hadoop host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_timepoint_restore_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Point-in-time restore**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) In the **File** section, select files for the restore job. All the files in the backup set will be selected by default. You can select or deselect the files.

    ```{note}
    Files in backup sets from local storage pools and LAN-free pools cannot be listed in the **File** field.
    ```

	(5) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.

	```{note}
	If you select a Hadoop or object storage resource as the restore target, the wizard will go to the **Backup host** step. After you select a backup host, the wizard goes to the next step automatically.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create an instant recovery job

```{note}
1. The `adps-nfsd` package should be installed on the storage server for Hadoop instant recovery.
2. Hadoop instant recovery only supports backup sets from standard storage pools (with neither **Multi-storage** nor **Data storage encryption** enabled) and file synthetic pools.
```

To create an instant recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the Hadoop host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_instant_recovery_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Instant recovery**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) Click **Next**.

4. At the **Export** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_instant_recovery_02.png)
	```

	(1) In the **Export** field, set the mount point for the export. The path must begin with / and can be 2-30 characters with letters or numbers.

	(2) In the **Access control list** field, add the agents that can mount and access the backup set. It supports IP addresses and network segments. * means all agents can access the backup set.

	(3) From the **Conversion path coding** list, select **None**, **GBK**, **GB18030**, or **BIG5**. **None** is selected by default, in which UTF8 is used for instant recovery.

	(4) Expand the **Advanced options**. From the **Bridge** list, select whether to use a network bridge or not. **None** is selected by default. You can use the network bridge to export backup sets to avoid conflicts with the NFS service of the operating system.

    ````{note}
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
	````

5. At the **Finish** step, confirm the job information and click **Submit**.
6. After the submission, you will be redirected to the help page. Mount the files manually according to the procedure. On the **CDM** page, a copy with a mounted state is added below the selected point in time. For more details, see [View a copy](#view-a-copy).

### Create a recovery testing job

To create a recovery testing job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the Hadoop host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_recovery_testing_01.png)
	```

	(1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. **Default** refers to the one where the selected backup sets reside.

	(2) From the **Restore type** list, select **Recovery testing**.

	(3) In the **Restore source** section, select a point in time for the restore job.

	(4) In the **File** section, select files for the restore job. All the files in the backup set will be selected by default. You can select or deselect the files.

    ```{note}
    Files in backup sets from local storage pools and LAN-free pools cannot be listed in the **File** section.
    ```

	(5) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.

	```{note}
	If you select a Hadoop or object storage resource as the restore target, the wizard will go to the **Backup host** step. After you select a backup host, the wizard goes to the next step automatically.
	```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Hourly**. Set the start time, end time, and time interval to specify the time range for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for Hadoop:

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
|Incremental restore|Only when you select an incremental backup set for the restore will this option become available. It is disabled by default. If you enable this feature, the job will only restore the incremental data at the selected point in time.|Only available for point-in-time restore jobs.|
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
|Process invalid paths|- Do not check and convert paths{{ br }}- Ignore paths with illegal characters{{ br }}- Erase illegal characters{{ br }}- Escape illegal characters||
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
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_cdm_01.png)
	```

### Clone a copy

You can click the **Clone copy** icon to create an instant recovery job for the synthetic copy to create a new mounted copy.

To clone a copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. On the display area, click a full copy under the resource. The **Clone copy** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_cdm_02.png)
	```

4. Click the **Clone copy** button. You will be redirected to the **Backup sets** step. See [Create an instant recovery job](#create-an-instant-recovery-job) to configure the job.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_cdm_03.png)
	```

5. After the instant recovery, a copy with a mounted state is added below the selected point in time on the **CDM** page.

### Unmount a copy

You can click the **Unmount** icon to unmount the mounted copies. This operation will make the mounted directory on the restore target inaccessible.

To unmount a copy, do the following:

1. From the menu, click **CDM**. The **CDM** page appears.
2. From the toolbar, select the resource and the period when copies are created. The display area shows the copies in this period.
3. Expand the full backup copy and select a mounted copy. The **Unmount** icon appears on the right of the copy.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Hadoop/hadoop_cdm_04.png)
	```

4. Click the **Unmount** icon. A confirmation window appears.
5. Confirm the warning and enter the verification code. Click **OK**.
6. After the unmounting, you can see no such mounted copy record under the full copy.

## Limitations

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Limitations
---
class: longtable
---
|Feature|Limitations|
| --- | --- |
|Point-in-time restore|Hadoop backup sets cannot be restored to Windows and vice versa.|
|instant recovery|Only Linux storage server supports instant recovery. {{ br }}It supports standard storage pools with neither **Data storage encryption** nor **Multi-storage** enabled.{{ br }}It supports file synthetic pools.|
|Recovery testing|It does not support recovering Hadoop backup sets to Windows.{{ br }}It does not support recovering Hadoop backup sets to object storage.|
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
|cross-system restore|A restore method that restores files from Hadoop to Linux and vice versa.|
|different-architecture restore|A restore method that restores files from Hadoop to an operating system or object storage.|
```