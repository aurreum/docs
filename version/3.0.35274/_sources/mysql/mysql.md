# User Guide for MySQL
## Introduction

Aurreum Data Protection Suite (ADPS) provides the capability for the backup and restore of MySQL databases. This guide introduces how to properly use ADPS to back up and restore MySQL databases.

## Features
```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Features
---
class: longtable
---
| Feature                                 | Description                              |
| --------------------------------------- | ---------------------------------------- |
| Backup source                           | Database (single, multiple), instance    |
| Backup type                             | Full backup: Back up an entire copy of the data source.{{ br }}Incremental backup: Back up data blocks that have changed since the last full backup.{{ br }}Log backup: Back up the binary logs of the database instance. {{ br }}Continuous log backup: Back up binary log files to a real-time backup pool continously. {{ br }}Logical backup: Back up one or more databases. {{ br }}Synthetic backup: Full backup for the first time, followed by incremental backups. |
| Backup target                           | Standard storage pool, de-duplication storage pool, database synthetic pool, local storage pool, tape library pool, object storage service pool, LAN-Free pool, real-time backup pool |
| Backup compression                      | None, fast                               |
| Channels                                | A positive integer between 1-255.        |
| Backup schedule                         | Immediate, one-time, minutely, hourly, daily, weekly, monthly |
| Clone backup jobs                       | Cloning backup jobs is supported.           |
| Acquiring database global lock timeout  | Available for full backups, incremental backups, and synthetic backups. The default value is 1 minute. |
| Lock retry count                        | Available for full backups, incremental backups, and synthetic backups. The default value is 1. |
| Long query threshold                    | Available for full backups, incremental backups, and synthetic backups. The default value is 0, indicating no delay. |
| Long query timeout                      | Available for full backups, incremental backups, and synthetic backups. The default value is 0, indicating no delay. |
| Log copy interval                       | Available for full backups, incremental backups, and synthetic backups. The default value is 1000 milliseconds. |
| Backup retry count                      | Available for full backups, incremental backups, and synthetic backups. The default value is 1. |
| Backup retry interval                   | Available for full backups, incremental backups, synthetic backups. The default value is 30 minutes. |
| Restore type                            | Timepoint restore: Restore database instance to a specific timepoint state including backup state (shortest recovery time), point in time, and GTID.{{ br }}Logical recovery: Restore one or more databases. {{ br }}Instant recovery (mounting recovery): Mount synthetic backup sets to reduce the recovery time.{{ br }}Log restore: Download the backed-up logs to a specified directory on the agent.{{ br }}Recovery testing: Periodically restore the latest backup sets to another database or instance. |
| Restore location                        | Original path, custom path               |
| Restore target                          | Overwrite the source database, create a new database |
| Restore granularity                     | Entire database (single, multiple), instance, log |
| Restore to different hosts              | Restore to a different host with the same version or from an old version to a new version. |
| Pre/Post action                         | The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored. |
| Speed limit                             | Limit data transfer speed or disk read and write speed. Synthetic backup and instant recovery do not support this function. |
| Reconnection time                       | The job continues after the abnormal reset occurs in the network within the set time. The set time is 10 minutes by default. Synthetic backup and instant recovery do not support this function. |
| Stop jobs                               | Stop the backup or restore of a job.     |
| Storage pool replication                | Replicate backup sets from the source storage pool to another pool. |
| Restore from target pool                | Restore backup sets from the target storage pool. |
| Custom database startup/stop script     | Custom start and stop scripts is supported. |
| Skip grant tables while applying binlog | Available for timepoint restore and instant recovery. |
| Force applying binlog                   | Available for timepoint restore and instant recovery. |
| Keep the mount point even if job fails  | Available for instant recovery.                |
| MySQL service startup timeout           | Available for timepoint restore and instant recovery. |
| D2C                                     | Back up data directly to object storage. |
| D2T                                     | Back up data directly to tape library.   |
| LAN-Free                                | Back up to and restore from LAN-Free storage pools. |
| Cluster                                 | Master-slave replication and dual-master replication is supported. |
| Modify the backup source and target     | Modify the backup source and backup target of a job. |
| IPv6                                    | Transfer and manage data over IPv6 network. |
| Add MySQL instance                      | Manually adding MySQL instance is supported.  |
```

> Note:
>
> - Physical backup and synthetic backup only support the Linux MySQL 5.1.38 or later (with the MySQL InnoDb Plugin installation package).

## Install and Configure Agent

### Verify Compatibility

ADPS supports the backup and restore of single instance, multiple instances, master-slave, dual-master replication cluster of MySQL databases. Before deploying the agent, it's necessary to check whether the operating system (OS) and database version are supported. For details, please refer to *Compatibility List*.

#### Compatibility List

##### Single Instance
```{tabularcolumns} |\Y{0.2}|\Y{0.1}|\Y{0.2}|\Y{0.2}|\Y{0.1}|\Y{0.2}|
```
```{table} Single Instance
---
class: longtable
---
| Database Version | Database Bits | OS | CPU Architecture | OS Bits | Note         |
| ---------------- | ------------- | -------------------- | ---------------- | ------- | ------------ |
| 4.1.22           | 32            | Windows 2003         | x86              | 32      |              |
| 4.1.22           | 64            | Windows 2003         | x86              | 64      |              |
| 4.1.22           | 64            | Windows 2008 R2      | x86              | 64      |              |
| 4.1.22           | 64            | Windows 2012         | x86              | 64      |              |
| 4.1.22           | 64            | CentOS 4.6           | IA64             | 64      |              |
| 4.1.22           | 64            | Red Hat 6.5          | POWER Linux      | 64      | (Big Endian) |
| 4.1.22           | 64            | SUSE 11 SP4          | POWER Linux      | 64      | (Big Endian) |
| 5                | 64            | Red Hat 5.0          | x86              | 64      |              |
| 5                | 64            | Red Hat 6.0          | x86              | 64      |              |
| 5                | 32            | Windows 2000         | x86              | 32      |              |
| 5                | 32            | Windows 2003         | x86              | 64      |              |
| 5                | 64            | Windwos 2003         | x86              | 64      |              |
| 5                | 32            | Windows 2008         | x86              | 64      |              |
| 5                | 64            | Windwos 2008         | x86              | 64      |              |
| 5                | 64            | Ubuntu 12.10         | x86              | 64      |              |
| 5                | 64            | SLSE 10              | x86              | 64      |              |
| 5                | 64            | Ubuntu 11.10         | x86              | 64      |              |
| 5.0.67           | 64            | Red Hat 5            | x86              | 32      |              |
| 5.0.86           | 64            | Red Hat 6.5          | POWER            | 64      | (Big Endian) |
| 5.0.86           | 64            | SUSE 11 SP4          | POWER            | 64      | (Big Endian) |
| 5.1.17           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 5.1.39           | 64            | Red Hat 6.5          | POWER            | 64      | (Big Endian) |
| 5.1.39           | 64            | SUSE 11 SP4          | POWER            | 64      | (Big Endian) |
| 5.1.40           | 64            | SUSE 11 SP4          | POWER            | 64      | (Big Endian) |
| 5.1.40           | 64            | Red Hat 6.5          | POWER            | 64      | (Big Endian) |
| 5.1.71           | 64            | Red Hat 6.0          | x86              | 64      |              |
| 5.1.73           | 32            | Windows 2008 R2      | x86              | 64      |              |
| 5.1.73           | 64            | Windows 2008 R2      | x86              | 64      |              |
| 5.4.2            | 64            | Red Hat 6.5          | POWER            | 64      | (Big Endian) |
| 5.4.2            | 64            | SUSE 11 SP4          | POWER            | 64      | (Big Endian) |
| 5.5              | 64            | Ubuntu 12.04         | x86              | 64      |              |
| 5.5              | 64            | Ubuntu 14.04         | x86              | 64      |              |
| 5.5              | 64            | Debian 7.8           | x86              | 64      |              |
| 5.5              | 64            | Debian 8.3           | x86              | 64      |              |
| 5.5.48           | 64            | CentOS 6.0           | x86              | 64      |              |
| 5.5.21           | 64            | Windows 2008 R2      | x86              | 64      |              |
| 5.5.48           | 64            | CentOS 6.0           | x86              | 64      |              |
| 5.5.58           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 5.6.25           | 64            | Windows 2008 R2      | x86              | 64      |              |
| 5.6.25           | 64            | Windows 2008 R2      | x86              | 64      |              |
| 5.6.30           | 32            | Red Hat 5.0          | x86              | 32      |              |
| 5.6.30           | 64            | Red Hat 5.0          | x86              | 64      |              |
| 5.6.30           | 64            | Red Hat 6.0          | x86              | 64      |              |
| 5.6.30           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 5.6.31           | 64            | Ubuntu 14.04         | x86              | 64      |              |
| 5.6.36           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 5.6.36           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 5.6.31           | 64            | CentOS 6.0           | x86              | 64      |              |
| 5.6.39           | 64            | CentOS 7.2           | x86              | 64      |              |
| 5.7.10           | 64            | Red Hat 6.0          | x86              | 64      |              |
| 5.7.13           | 64            | Red Hat 7.0          | x86              | 64      |              |
| 5.7.16           | 64            | CentOS 7.0           | x86              | 64      |              |
| 5.7.19           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 5.7.18           | 64            | Ubuntu 16.04         | x86              | 64      |              |
| 5.7.21           | 64            | CentOS 7.2           | x86              | 64      |              |
| 5.7.21           | 64            | Red Hat 7.0          | x86              | 64      |              |
| 6                | 32            | Windows 2003         | x86              | 64      |              |
| 6                | 64            | Windows 2012         | x86              | 64      |              |
| 6.0.11           | 64            | Windows 2008 R2      | x86              | 64      |              |
| 8.0.0            | 64            | Windows 2008 R2      | x86              | 64      |              |
| 8.0.0            | 64            | CentOS 6.7           | x86              | 64      |              |
| 8.0.11           | 64            | Red Hat 6.5          | x86              | 64      |              |
| 8.0.11           | 64            | CentOS 7.0           | x86              | 64      |              |
| 8.0.19           | 64            | CentOS 7.6           | x86              | 64      |              |
| 8.0.19           | 64            | Windows 2016         | x86              | 64      |              |
| 8.0.21           | 64            | CentOS 7.6           | x86              | 64      |              |
| 8.0.21           | 64            | Windows 2016         | x86              | 64      |              |
| 8.0.22           | 64            | CentOS 7.6           | x86              | 64      |              |
| 8.0.23           | 64            | CentOS 7.6           | x86              | 64      |              |
| 8.0.24           | 64            | CentOS 7.6           | x86              | 64      |              |
| 8.0.25           | 64            | CentOS 7.6           | x86              | 64      |              |
| 8.0.26           | 64            | CentOS 7.5           | x86              | 64      |              |
```


##### Cluster
```{tabularcolumns} |\Y{0.2}|\Y{0.1}|\Y{0.2}|\Y{0.2}|\Y{0.1}|\Y{0.2}|
```
```{table} Cluster
---
class: longtable
---
| Database Version | Database Bits | OS | CPU Architecture | OS Bits | Note |
| ---------------- | ------------- | ----------- | ---------------- | ------- | ---------------------------------------- |
| 5.7.21           | 64            | Red Hat 7.0 | x86              | 64      | One master, two slaves                   |
| 5.7.21           | 64            | Red Hat 6.5 | x86              | 64      | One master, two slaves                   |
| 5.7.21           | 64            | Red Hat 7.0 | x86              | 64      | Dual masters                             |
| 5.7.21           | 64            | Red Hat 6.0 | x86              | 64      | One master, two slaves                   |
| 5.7.25           | 64            | CentOS 7.6  | x86              | 64      | Single node cluster                      |
| 5.6.39           | 64            | CentOS 7.0  | x86              | 64      | One master, two slaves                   |
| 5.6.39           | 64            | CentOS 6.5  | x86              | 64      | One master, two slaves                   |
| 5.6.39           | 64            | Red Hat 7.0 | x86              | 64      | One master, two slaves                   |
| 5.6.39           | 64            | Red Hat 6.0 | x86              | 64      | One master, two slaves                   |
| 8.0.13           | 64            | CentOS 7.0  | x86              | 64      | One master, two slaves                   |
| 8.0.13           | 64            | CentOS 7.0  | x86              | 64      | Dual masters                             |
| 5.7.30           | 64            | CentOS 7.0  | x86              | 64      | (MySQL InnoDb Cluster) One master, two slaves |
| 5.7.30           | 64            | CentOS 7.0  | x86              | 64      | (MySQL InnoDb Cluster) Multiple masters  |
| 8.0.16           | 64            | CentOS 7.6  | x86              | 64      | (MySQL InnoDb Cluster) One master, two slaves |
| 8.0.16           | 64            | CentOS 7.6  | x86              | 64      | (MySQL InnoDb Cluster) Multiple masters  |
```


### Download Agent Package

Open a browser and log in to ADPS as the admin. Click **Resource** -> **Install Agent** icon. You can download the installation packages according to your needs.

![agent-1](../images/mysql/01-agent/agent-1.png)

### Install and Configure Agent on Windows

#### Download Installation Package

Select **Windows** and click **Download Windows agent**.

![agent-2](../images/mysql/01-agent/agent-2.png)


#### Install Agent on Windows

 1. Upload the installation package to the target host.

 2. Double-click the package to launch the setup wizard and click **Next**.

 3. This installation package is a collection of components. It checks the database resources, files, or applications installed on the host by default. Check the **MySQL** component and click **Next**.

    ![agent-3](../images/mysql/01-agent/agent-3.png)

 4. Set the **Backup Server Host**, **Backup Server Port**, and **Access Key**. Click **Next**.

    ![agent-4](../images/mysql/01-agent/agent-4.png)



> Note:
>
> - The Access Key is used to identify users of the agent. On Personal Settings -> Settings -> Home page, you can view the user account’s Access Key.
> - If the backup server is a non-tenant version, it is not required to configure Access Key. If the backup server is a tenant version, then an Access Key is required. If you leave it blank, the resource will be bound to the admin. If you enter the Access Key, the resource will be bound to the corresponding tenant.

5. Select **Destination Folder** and click **Next** to install the software. Wait for the installation to complete.

###  Install and Configure Agent on Linux

1. Select **Linux** and **MySQL**. Copy an installation command.

![agent-5](../images/mysql/01-agent/agent-5.png)

2. Paste the command on the command line and press Enter to execute the installation.

   ![agent-6](../images/mysql/01-agent/agent-6.png)

### Check Successful Installation

After the successful installation, you can log in to ADPS as the admin and see that the host is on the **Resource** list.

![license-1](../images/mysql/02-license/license-1.png)

## Activate License and Assign Authorization

This chapter is applicable to configuring one agent. If you have multiple agents, you can deploy them first and then carry out activation and authorization in batches.

### Register Host

Log in to ADPS as the admin, go to **Resource**, and find the host that you need to activate. Click **Register** icon.

![license-2](../images/mysql/02-license/license-2.png)



### Activate License

In the pop-up **Activate** window, select the resource you want to activate. Click **Submit**.

![license-3](../images/mysql/02-license/license-3.png)

### Assign Authorization

After the successful activation, you can authorize users to operate the resource in the pop-up **Authorize** window.

![license-4](../images/mysql/02-license/license-4.png)

## Before You Begin

### Check Database State

Check the MySQL service state of the agent. The MySQL database service should be in the "Started" state for the backup and restore.

- Check the MySQL service on Windows:

![database-1](../images/mysql/03-mysql/database-1.png)

- Check the MySQL service on Linux:


![database-2](../images/mysql/03-mysql/database-2.png)

### Check Resource

Log in to ADPS as the operator, go to **Resource**, and you can see the activated and authorized resource on the list and its state is "Online". If the resource is not available, please check *Activate License and Assign Authorization*.

![login-1](../images/mysql/03-mysql/login-1.png)

### Check Storage Pool

Log in to ADPS as the operator, go to **Storage Pool**, and check whether there is any storage pool. If a storage pool is not listed, please contact the admin to create one and assign permissions to the operator.

![storaged-1](../images/mysql/03-mysql/storaged-1.png)

## Login Resource

### Login Instance for the First Time

1. Before creating the first backup and restore job for the MySQL databases, you need to **Login** to MySQL first.

![login-2](../images/mysql/03-mysql/login-2.png)


2. Enter the MySQL username and password.

![login-3](../images/mysql/03-mysql/login-3.png)

### Add MySQL Instance Manually

If MySQL resource is not available on the **Resource** page, you can add MySQL instance manually.

This method is applicable to the following scenarios: the agent cannot be installed in some environments, and the agent instance cannot be recognized, resulting in backup failure, such as:

- MySQL instance in container;
- Unrecognized MySQL instance;
- MySQL derivative version;
- Instance without port open;
- Inaccurate configuration of recognized MySQL instance;

- ...

> Note:
>
> - Only the admin can manually add MySQL instances.

1. Click **Resource** on the left-side menu, select the MySQL database host, and click the "**+**" icon to add MySQL instance.

![login-4](../images/mysql/03-mysql/login-4.png)


2. Enter the username and password of the host in the pop-up window.

![login-5](../images/mysql/03-mysql/login-5.png)


3. Enter **Add MySQL Instance** page. The first step is **Database Connection**. You can select **TCP/IP** or **Local Socket** to connect the database.

- **TCP/IP**

  If you select **TCP/IP**, you need to fill in the host IP, database port, database username and password.

![login-6](../images/mysql/03-mysql/login-6.png)


- **Local Socket**

  If you select **Local Socket**, you need to fill in socket path, database username and password.

![login-7](../images/mysql/03-mysql/login-7.png)


4. After **Database Connection**, configuring parameters is required. You can select **Logical** or **Physical** as the parameter type.

- **Logical**

  If you select **Logical**, fill in instance name, mysql command-line tool path, mysqldump path, mysqlpump path and version. This instance can only be used for logical backup and restore.

![login-8](../images/mysql/03-mysql/login-8.png)




- **Physical**

  If you select **Physical**, fill in instance name, data directory, mysql command-line tool path, mysqldump path, mysql.cnf path, binlog path and version. This instance can be used for logical and physical backup and restore.

![login-9](../images/mysql/03-mysql/login-9.png)


5. After **Parameters**, activating database resource is required. The license type is checked by default. You need to authorize the resources to the corresponding user group. Click **Submit** to add the MySQL instance.

![login-10](../images/mysql/03-mysql/login-10.png)

## Create Backup Jobs

This chapter introduces how to back up the MySQL databases.

### Prerequisites

- You have installed the agent. For installation, please refer to *Install and Configure Agent*.

- You have activated the license and assigned the authorization. For details about the activation and authorization, see *Activate License and Assign Authorization*.

- Log in to ADPS as the *operator*.

- Least privilege for the database user to preform the backup:
  ```{tabularcolumns} |\Y{0.15}|\Y{0.60}|\Y{0.25}|
  ```
  ```{table} Least Privilege for the User
  ---
  class: longtable
  ---
  | Back Type                          | Least Privilege for the User             | Note                              |
  | ---------------------------------- | ---------------------------------------- | --------------------------------- |
  | Physical backup                    | SELECT, RELOAD, PROCESS, LOCK TABLES, REPLICATION SLAVE, REPLICATION CLIENT, (SUPER) | Require BACKUP_ADMIN for MySQL8.0 |
  | Logical backup (Single channel)    | SELECT                                   |                                   |
  | Logical backup (Multiple channels) | SELECT, RELOAD, EXECUTE, SHOW VIEW, EVENT, LOCK TABLES |                                   |
  ```

### Create Full Backup Jobs

(1) Click **Backup**, select the MySQL database host and instance.

(2) Select **Full** as the **Backup Type**, select the database instance to be backed up and click **Next**.

![full_backup-1](../images/mysql/03-mysql/full_backup-1.png)

(3) Select **Backup Target**. The target can be a standard storage pool, de-duplication storage pool, tape library pool, object storage service pool, and LAN-Free pool.

>  Note:
>
>  - It is not supported to store full backups, incremental backups and log backups in different storage pools.

(4) Go to **Backup Schedule** to set the run time of the backup job. For details, see *Backup Schedule Operation*. It is generally recommended to run a full backup on a weekly basis.

(5) Set **Backup Options**, including common options and advanced options.

- **Common options**:

![full_backup-2](../images/mysql/03-mysql/full_backup-2.png)

**Compression**: Fast compression is enabled by default.

**Channels**: This option can improve backup efficiency. The default number of channels is 1 and the range is 1 to 255. For details, please refer to *Channel Number Configuration*.

**binlog**: You can enable this option to restore to a specific time point.  This operation requires to restart the database. Please operate with caution.

**File name**: Set the base name for binary logs. You can specify a path, but MySQL must have access to the specified path.

**Format**: Set the format of binary logs, including ROW, STATEMENT or MIXED.

**Synchronizing binary log number**: In this field, set the number of transactions that need to be committed to synchronize the binary logs from the cache to the disk. When the transaction commits reach the set number, the logs will be flushed to the disk. The value ranges from 0 to 4294967295. 0 indicates that the synchronous flushing is disabled, and the timing of the synchronization is determined by the operating system. The number affects database efficiency and data consistency. The larger the value, the higher the efficiency and the lower the data consistency.

- **Advanced options**:

![full_backup-3_1](../images/mysql/03-mysql/full_backup-3_1.png)

![full_backup-3_2](../images/mysql/03-mysql/full_backup-3_2.png)

**Reconnection time**: This option supports 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time. The unit is minute.

**Speed limit**: Set the limit for data transfer speed or disk read and write speed. The unit can be MiB/s or KiB/s. Click the ‘’+‘’ icon to add limits in different times.

**Precondition**: The precondition is executed before the job starts. The job execution is aborted and in the idle state when the precondition is invalid.

**Pre/Post action**: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.

**Acquiring database global lock timeout**: Set the time to wait for the acquisition of the database global lock. 0 means no timeout. The default unit is minute.

**Long query threshold**: Do not add a lock until the long query over this threshold is finished. 0 means no delay. The default unit is second.

**Long query timeout**: Set the time to wait for the completion of a long query. 0 means no delay.

**Log copy interval**: The time interval for copying transaction logs. The unit is millisecond.

**Backup retry count**: When the DDL operation is not written to the transaction log, backup will be retried for the specified times. 0 means no retry.

**Backup retry interval**: When the DDL operation is not written to the transaction log, backup will be retried for the specified times. The unit is minute.

(6) Set **Job Name**, check whether the job information is correct, and click **Submit**.

### Create Incremental Backup Jobs

It is recommended to create full backup jobs regularly (such as weekly), or create incremental backup jobs at short intervals (such as daily), which ensure that you have at least one recoverable time point every day by only backing up data that has changed since the last backup.

- Creating an incremental backup is the same as creating a full backup. Select **Incremental** as the backup type and choose database instances.

  ![icr_backup-1](../images/mysql/03-mysql/icr_backup-1.png)


  > Note:
  >
  > - If the database has never been fully backed up, or has not been fully backed up after restore, the first incremental backup will be run as a full backup by default.

### Create Log Backup Jobs

Based on periodic full and incremental backups, you can also add frequent log backups to back up only the binary logs of the database. The appropriate log backup frequency depends on the balance between your tolerance for the risk of work loss and the number of log backups you can store, manage, and potentially restore. Performing a log backup every 15 to 30 minutes may be sufficient. But if your business requires minimizing the risk of work loss, consider running more frequent log backups. Frequent log backups also have the advantage of increasing the frequency of log truncation, making log files smaller.

- Creating a log backup is similar to creating a full backup. Select **Log** as the backup type and choose database instances.

  ![log_backup-1](../images/mysql/03-mysql/log_backup-1.png)


  >  Note:
  >
  >  - It's necessary to enable the binlog in MySQL before creating a log backup.

- **Backup Options** -> **Common** provides **Backed-up binary logs** option.

  ![log_backup-2](../images/mysql/03-mysql/log_backup-2.png)

**Delete after backup**: Only log backup and continuous log backup have this backup option.

- Checked: Delete the backed-up binary logs when the log backup is completed or after the continuous log backup is stopped.
- Unchecked: When the log backup is completed or the continuous log backup is stopped, the backed-up binary logs will be automatically deleted by MySQL according to configuration.

### Create Continuous Log Backup Jobs

Continuous log backup can constantly back up MySQL binary log files to the real-time backup pool. It requires the following conditions:

- The binary log is enabled in MySQL.

- server_id of MySQL is configured.

-  The database has a full backup set as the base.

One instance can only run a continuous log backup job at one time. When a full backup job runs, a continuous log backup job will be in the waiting state. After a completed restore job, a full backup job should be first performed on the MySQL host before a continuous log backup job.

- Creating a continuous log backup is similar to creating a log backup. Select **Continuous Backup** as the backup type and choose database instances.

  ![con_backup-1](../images/mysql/03-mysql/con_backup-1.png)


  >  Note:
  >
  >  - Only the real-time backup pool can be selected as the backup target pool for MySQL continuous log backup.


### Create Logical Backup Jobs

Logical backup jobs can back up one or more databases.

- Creating a logical backup is the same as creating a full backup. Select **Logical** as the backup type and choose one or more databases.

  ![logical-1](../images/mysql/03-mysql/logical-1.png)


  > Note:
  >
  > - MySQL on Windows only supports logical backup.


### Create Synthetic Backup Jobs

The first MySQL synthetic backup is a full backup and each subsequent backup is an incremental backup. Each incremental backup is synthesized with its dependent full backup to generate a new synthetic backup set. iSCSI and FC storage types are supported.

 Prerequisites for creating a synthetic backup:

1. The data disk of the storage server requires the ZFS file system. For installation, please refer to the *Server Requirements* in *Installation Guide*.
2. Before use, you need to configure the iSCSI/FC connections between the agent and the storage server.
3. The admin has the advanced **License** of MySQL Synthetic Backup and MySQL Copy Data Management.
4. The admin has created and assigned a database synthetic pool to the operator.

(1) Select **Synthetic Backup** as the backup type and choose database instances.

![merge-1](../images/mysql/03-mysql/merge-1.png)


(2) Select **Backup Target**. The backup target can only be a database synthetic pool. If no database synthetic pool is available, please contact the admin to create one.

![merge-2](../images/mysql/03-mysql/merge-2.png)


(3) Select **Backup Schedule** to set the run time of the backup job. For details, see *Backup Schedule Operation*. It is generally recommended to run a synthetic backup on a daily basis.

(4) Set **Backup Options**, including common options and advanced options. The advanced options are similar to those in full backups but without reconnection time, resumption buffer size, and speed limit.

![merge-3](../images/mysql/03-mysql/merge-3.png)

![merge-4](../images/mysql/03-mysql/merge-4.png)

(5) Set **Job Name**, check whether the job information is correct, and click **Submit**.

## Create Restore Jobs

This chapter introduces how to restore MySQL databases. ADPS provides a variety of restore types including timepoint restore, logical recovery, instant recovery, restore logs and recovery testing.

> Note:
>
> - MySQL timepoint restore jobs will restart the MySQL service and overwrite the data of the source database. Therefore, you need to reset the original scheduled job, and execute a full database backup before the next log backup.
> - If the recovered resource is a MySQL cluster, after the physical restore, check whether the master-slave replication of the cluster can be started normally. If it is not started, you need to start it manually.

### Prerequisites

- You have a completed backup job. See *Create Backup Jobs*.
- To restore to another host, it's required to install the agent on that host, activate its license and assign the authorization.

### Create Timepoint Restore Jobs

When a logical error occurs in a MySQL database or a disaster occurs in the database, you can use timepoint restore to restore the database to a specified point-in-time state. MySQL timepoint restore supports restoring to the source host or a different host.

(1) Select the MySQL database host and instance, and click **Next**.

(2) Select **Timepoint Restore** as the restore type, select the database instance to be restored, and click **Next**. ADPS provides three timepoint types including point in time, GTID, and backup state (shortest recovery time).

**Restore to Point-in-time**

If you have executed a log backup or a continuous log backup job, you can run the timepoint restore job by dragging the slider control or manually entering to specify the point in time of the database that needs to be restored.

![time_restore-1](../images/mysql/03-mysql/time_restore-1.png)

**Restore to GTID**

When you choose **Restore to GTID**, the restore job is performed using the Restore Branch and the specified GTID.

The **Restore to GTID** option is only displayed when the following conditions are met:

- You have enabled GTIDs in MySQL configuration files. For the GTID configuration method, please go to [MySQL official website](https://dev.mysql.com/doc/) and check relevant documents.

- A  log backup or continuous log backup job has been completed.

![time_restore-2](../images/mysql/03-mysql/time_restore-2.png)

**Restore to Backup State (shortest recovery time)**

This option can restore full backup sets, incremental backup sets, and synthetic backup sets, but it cannot restore binlog.

![time_restore-3](../images/mysql/03-mysql/time_restore-3.png)


> Note:
> - If the host to be restored is MySQL cluster, you can select the log backup sets of all nodes in the cluster. Before the log backup starts, the full backup sets and incremental backup sets will be displayed in the drop-down box in the form of time points. After the log backup completes, ADPS will automatically merge the full backup set, incremental backup set and log backup to become a time point. You can restore the instance to data at a specific point in time from the last full or incremental backup to the last log backup by selecting a point in time on the slider control.
> - If the host to be restored is MySQL cluster, the Backup Sets page will have a filter option. When the log restore is performed, you can use the option to filter specific databases.

(3) Set **Restore Target**. The restore target can be MySQL single host or cluster. When the restore target is a cluster, all nodes of the cluster will run the restore job.

(4) Set **Restore Schedule**. Timepoint restore only supports immediate and one-time restore schedules. Click **Next**.

(5) Set **Restore Options**. The restore options have common options and advanced options.

- **Common options**:

  You can set the database username and password of the restore target.

![time_restore-4](../images/mysql/03-mysql/time_restore-4.png)


- **Advanced options**:

  You can set reconnection time, resumption buffer size, speed limit, precondition, pre/post action, database service startup script, database service stop script, skip grant tables while applying binlog, accelerate applying binglog, force apply binlog, MySQL service startup timeout, and cache directory.

![time_restore-5_1](../images/mysql/03-mysql/time_restore-5_1.png)

![time_restore-5_2](../images/mysql/03-mysql/time_restore-5_2.png)

**Skip grant tables while applying binlog**: You can enable this option to avoid the problem of forbidden access to the binary logs due to insufficient permissions when restoring the logs.

**Accelerate applying binlog**: Accelerate the speed of restoring binlog.

**Force apply binlog**: You can enable this option to continue applying binlog when an error occurs.

**Cache directory**: When restoring incremental backup sets, if you set cache directory, timepoint restore will first download the incremental backup sets to the cache directory, then restore the sets. If you do not set cache directory, the incremental backup sets will be directly restored to the data directory of MySQL.

(6) Confirm whether the restored content is correct, and submit the job after confirming that it is correct.

### Create Logical Restore Jobs

When data loss occurs in a MySQL database such as data files damaged or deleted by mistake, the database or some data files can be restored to the latest state through the logical restore function.

> Note:
>
> - The least privileges required for a database user to start a logical restore are CREATE, DROP, SUPER, INSERT.

(1) Select **MySQL** database host and instance. Click **Next**.

(2) Select **Logical Recovery** as the restore type, select the database to be restored, and click **Next**.

![logical_restore-1](../images/mysql/03-mysql/logical_restore-1.png)


(3) Set **Restore Schedule**. Logical restore only supports immediate and one-time restore schedules. Click **Next**.

(4) Set **Restore Options**. The restore options have common options and advanced options.

- **Common options**:

  In common options, you can set restore database methods, and overwrite the original database is set by default, or you can restore to the new database by entering the new database name.

![logical_restore-2](../images/mysql/03-mysql/logical_restore-2.png)


- **Advanced options**:

  In advanced options, you can set reconnnection time, resumption buffer size, speed limit, precondition, pre/post action and additional parameters.

![logical_restore-3](../images/mysql/03-mysql/logical_restore-3.png)


(5) After confirming the restore content, click **Submit** and allow the job to be executed.

### Create Instant Recovery Jobs

nstant recovery can only be performed on the MySQL databases with synthetic backup sets.

>Note:
> - If the storage pool storing synthetic backup sets is of the iSCSI type, then the iSCSI Initiator should be installed on the host where you perform the restore job.
> - If the storage pool storing synthetic backup sets is of the FC type, the HBA API Driver corresponding to the HBA card should be installed on the host where you perform the restore job.
> - When the target of instant recovery is cluster, all nodes of the cluster will run instant recovery job at the same time. ADPS mounts different LUNs on every node.

The interface offers two portals to create instant recovery jobs and clone copies: **Restore** and **CDM**.

#### Create from Restore Menu

(1) Go to the **Restore** page, select MySQL database host and instance, and click **Next**.

(2) Select **Instant Recovery** as the restore type. ADPS provides three timepoint types including point in time, GTID, and backup state (shortest recovery time). Click **Next**.

**Restore to Point-in-time**

If you have executed a log backup or a continuous log backup job, you can run the instant recovery job by dragging the slider control or manually entering to specify the point in time of the database that needs to be restored.

![instant_restore-1](../images/mysql/03-mysql/instant_restore-1.png)

**Restore to GTID**

When you choose **Restore to GTID**, the restore job is performed using the Restore Branch and the specified GTID.

The **Restore to GTID** option is only displayed when the following conditions are met:

- You have enabled GTIDs in MySQL configuration files. For the GTID configuration method, please go to [MySQL official website](https://dev.mysql.com/doc/) and check relevant documents.
- A  log backup or continuous log backup job has been completed.

![instant_restore-2](../images/mysql/03-mysql/instant_restore-2.png)

**Restore to Backup State (shortest recovery time)**

This option can restore full backup sets, incremental backup sets, and synthetic backup sets, but it cannot restore binlog.

![instant_restore-3](../images/mysql/03-mysql/instant_restore-3.png)


(3) Set **Restore Target**. You can restore data to another instance on the source or a different host. Click **Next**.

(4) Set **Restore Schedule**. You can choose immediate or one-time restore schedules. Click **Next**.

(5) Set **Restore Options**, including common options and advanced options.

- **Common options**:

  In common options, you can set the configuration file of MySQL database, and username and password for the backup source.

![instant_restore-4](../images/mysql/03-mysql/instant_restore-4.png)

**Database configuration file**: Click **Browse** or manually enter the MySQL database configuration file path. If you leave it blank, the default configuration file will be used.

**Database username for the backup source**: Enter the username for the backup source database. If you leave it blank, it will use the username of the current logged-in resource (i.e., the target database).

**Database password for the backup source**: Enter the database password for the backup source manually. If you leave it blank, it will use the password of the current logged-in resource (i.e., the target database).

- **Advanced options**:

  In advanced options, you can set precondition, pre/post action, database service startup/stop script, storage protocol, skip grant tables while applying binlog, accelerate applying binlog, force apply binlog, keep the mount point even if job fails, and MySQL service startup timeout.

![instant_restore-5_1](../images/mysql/03-mysql/instant_restore-5_1.png)

![instant_restore-5_2](../images/mysql/03-mysql/instant_restore-5_2.png)

**Database service start script**: You can set the command used to start the MySQL service. The database service script starts with this command by default.

**Database service stop script**: You can set the command used to stop the MySQL service. The database service script stops with this command by default.

**Storage protocol**: You can create a new FC Target or iSCSI Target to run instant recovery. Backup sets that are backed up to an iSCSI database synthetic pool can be restored instantly if the FC is used and vice versa. This function requires that the operator has the administrative permission.

**Keep the mount point even if job fails**: This option allows the job to retain the mount point even if the job fails.

**MySQL service startup timeout**: The duration to wait for the target MySQL database to start when the instant recovery job is running.

(6) Set **Job Name**, check whether the job information is correct, and click **Submit**.

#### Check Successful Recovery

After the instant recovery is completed, enter the **CDM** menu. You can find a recovery history record under the backup point in time of the corresponding host and instance.

![cdm-1](../images/mysql/03-mysql/cdm-1.png)

#### Detach Copy

You can detach the mounted data copies from the agent using the **Detach** icon. Note that if the mounted directory has data files being applied, the files will also be deleted.

- Click the **Detach** icon beside the data copy record.

  ![cdm-2](../images/mysql/03-mysql/cdm-2.png)

- Pay attention to the warning and enter the verification code.

  ![cdm-3](../images/mysql/03-mysql/cdm-3.png)

### Create Log Restore Jobs

Log restore jobs can only be applied when the database has log backups or continuous log backups. They only download the binary logs to the specified directory on the agent but do not perform the database restore.

(1) Select MySQL database host and instance. Click **Next**.

(2) Select **Restore Archived Logs** as the restore type and select **Restore to Point-in-time** or **Restore to GTID**. Click **Next**.

**Restore to Point-in-time**

You can run the restore logs job by dragging the slider control or manually entering to specify the point in time of the database that needs to be restored.

![log_restore-1](../images/mysql/03-mysql/log_restore-1.png)

**Restore to GTID**

When you choose **Restore to GTID**, the restore job is performed using the Restore Branch and the specified GTID.

The **Restore to GTID** option is only displayed when the following conditions are met:

- You have enabled GTIDs in MySQL configuration files. For the GTID configuration method, please go to [MySQL official website](https://dev.mysql.com/doc/) and check relevant documents.
- A  log backup or continuous log backup job has been completed.

![log_restore-2](../images/mysql/03-mysql/log_restore-2.png)


(3) Set **Restore Schedule**. It only supports immediate and one-time schedules. Click **Next**.

(4) Set **Restore Options**, including common options and advanced options.

- **Common options**:

  In common options, you can set binlog destination.

![log_restore-3](../images/mysql/03-mysql/log_restore-3.png)


- **Advanced options**:

  In advanced options, you can set reconnection time, resumption buffer size, speed limit, precondition and pre/post action.

![log_restore-4](../images/mysql/03-mysql/log_restore-4.png)


(5) Confirm the job settings, click **Submit** and wait for the job to be executed.

### Create Recovery Testing Jobs

Recovery testing verifies the integrity and availability of backup sets. With the hourly, daily, weekly, or monthly schedule, it can restore the latest backup sets of MySQL to other instances on target host or source host. It is required that the target host and the storage server where the backup sets are located can communicate with each other.

(1) Select MySQL database host and instance. Click **Next**.

(2) Select **Recovery Testing** as the restore type, select the instance or database to be restored, and click **Submit**. You can click the **Rename** icon and specify the restored database name.

![exercise-1](../images/mysql/03-mysql/exercise-1.png)


(3) Set **Restore Target**. The target can be the source or a different host. Click **Next**.

(4) Set **Restore Schedule**. It supports hourly, daily, weekly, and monthly schedules. Click **Next**.

(5) Set **Restore Options**, including reconnection time, speed limit, pre action and post action. Click **Next**.

(6) Confirm the job settings and click **Submit**.

(7) Wait for the job to be executed. The job will restore the latest backup sets of the source host.

## Manage Jobs

On the **Job** interface, you can view the backup and restore job information of all agents, start, modify, clone, and delete the jobs.

![job-1](../images/mysql/03-mysql/job-1.png)


- Start: Click  ![job-2](../images/mysql/03-mysql/job-2.png) to start the job immediately.
- Modify: Click ![job-3](../images/mysql/03-mysql/job-3.png) to modify the basic job information, the backup/restore schedule, and the backup/restore options.
- Clone: Click ![job-4](../images/mysql/03-mysql/job-4.png) to create multiple similar backup jobs.
- Delete: Click ![job-5](../images/mysql/03-mysql/job-5.png) to access the confirmation window. Click **OK** to delete the job.

## Backup Protection Strategy
###  Backup Schedule Operation
ADPS provides six types of backup schedules. The schedule type selected is only valid for the currently created job.

![time-1](../images/mysql/03-mysql/time-1.png)

- Immediate: The job immediately starts to run after it is submitted.
- One time: After the job is created, it will be in an idle state and start to run when the specified Start time is reached.
- Hourly: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of hours/minutes within the time range according to the setting. If the unit is Hour, then you can set the value from 1 to 24. If you select the Minute as the unit, then you can set the value from 1 to 60.
- Daily: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of days according to the setting. The value is an integer between 1 and 5.
- Weekly: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of weeks according to the setting. You can specify which day of the week to run the job.
- Monthly: The job runs on the specified days of some months at the specified time. For example, you can set the job to run on January 1 and June 1 at 20:00. Or you can set it to run on the first Monday of every month at 20:00.

>  **Example: Perform the job every two weeks on Friday at 18:00**

![time-3](../images/mysql/03-mysql/time-3.png)

> **The actual execution time is:**
>
> - If the current time is Friday 17:00, the run time is Friday 18:00 (the current day).
> - If the current time is Thursday 17:00, the run time is Friday 18:00 (the next day).
> - If the current time is Saturday 17:00, the run time will be next Friday 18:00.
> - After the first run is completed, the job will start automatically at 18:00 on Friday every two weeks.

### Backup Strategy Advice

There are six methods to back up MySQL: full backup, incremental backup, log backup, continuous log backup, logical backup and synthetic backup. Full backup, incremental backup, and log backup can be used together. Synthetic backup and log backup can be used together. It is recommended to formulate the following backup strategy according to different situations such as network bandwidth, business data volume, security requirements, and the amount of lost data that you can tolerate.

1. When the application traffic is relatively small, run a **Full Backup** once a week to ensure that you have at least one recoverable RTO every week.
2. After that, you can run an **Incremental Backup** every day to reduce the backup time and ensure that you have at least one recoverable RPO every day.
3. If the database supports log backup, you can run a **Log Backup** every few hours to reach a second-level RPO. For example, you can run a log backup every 2 hours.

> Note:
>
> Avoid using the following strategies:
>
> - Perform only full backups.
> - Perform a full backup followed by all incremental or log backups.

## Channel Number Configuration

- Channel number for backup jobs

  MySQL supports up to 255 channels. You can set the number of channels for backup and restore jobs according to the actual environment. A reasonable number can improve job performance. The number of channels is generally recommended to be consistent with that of CPU cores. The efficiency improvement will not be obvious if the number of channels exceeds that of CPU cores.

- Channel number for restore jobs

  The channel number for a restore job is the same as that for the backup job. The restore page does not have the Channel option by default.
## MySQL Cluster

ADPS supports MySQL master-slave and dual-master replication. Before the first backup and restore, bind the MySQL nodes into clusters.

### Master-Slave Replication

The deployment of each node in the cluster is required. For details, see *Install and Configure Agent* as well as *Activate License and Assign Authorization*.

##### Cluster Binding

1. Go to **Resource** -> **Cluster**. Click **Cluster Binding**.

![cluster-1](../images/mysql/03-mysql/cluster-1.png)

2. Customize the cluster name. Select the active node as the **Primary Node**, **Master Slave Replication** as the **Type**, and the inactive node as the **Nodes**. You can set the **Keepalived Configuration File** and **Preferred Backup Node** according to your needs. Click **Submit**.

![cluster-2](../images/mysql/03-mysql/cluster-2.png)


##### Backup and Restore

- MySQL master-slave has the same backup and restore types as the single instance.
- On the **Backup** page, you can only see the active nodes of MySQL master-slave cluster. Select the active nodes as the backup resource. The remaining steps are similar to *Create Backup Jobs*.
- If a switchover occurs in the nodes of the MySQL master-slave cluster, the new active node will automatically take over the backup job.

### Master-Master Replication

##### Cluster Binding

1. Go to **Resource** -> **Cluster**. Click the **Cluster Binding** icon.
2. Customize the cluster name. Select a master node in the cluster as the **Primary Node**, **Dual-master Replication** as the **Type**, and other master nodes of the cluster as the **Nodes**. You can set the **Keepalived Configuration File** and **Preferred Backup Node** according to your needs. Click **Submit**.

![cluster-3](../images/mysql/03-mysql/cluster-3.png)

##### Backup and Restore

When creating a backup job, select the primary node of MySQL as the backup source. The remaining steps are similar to *Create Backup Jobs*.

## Limitations
```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Limitation
---
class: longtable
---
| Function | Limitations                |
| -------- | ---------------------------------------- |
| Resource | · Do not support MySQL cluster environment on Windows. {{ br }}· After cluster binding, all nodes should be online so that you can create jobs. {{ br }}· After cluster binding, you cannot change the primary node unless you delete the cluster. |
| Backup   | · MySQL on Windows only supports logical backups.  {{ br }}· To run physical backup on MySQL 5.1, it's necessary to install InnoDb Plugin engine.    {{ br }}· Do not support running incremental backups for databases with MyISAM storage engine. If you run incremental backups on those databases, it will run full backups by default.     {{ br }}· If the type of database synthetic pool is iSCSI, it's necessary to install iscsi-initiator-utils on the agent. If the type of database synthetic pool is FC, it's necessary to insert HBA card and install the corresponding HBA API Driver on the agent.  {{ br }}· Synthetic backups do not support reconnection time, resumption buffer size and speed limit. |
| Restore  | · When ADPS is running physical restore jobs, it's necessary to enter corresponding scripts in the options of Database Start/Stop Scripts if MySQL service is started unconventionally.  {{ br }}· You can set cache directory when use timepoint restore to restore incremental backup sets. {{ br }}· If you select Restore to Backup State (shortest recovery time) on timepoint restore, you can restore full backup sets, incremental backup sets and synthetic backup sets. If you select Restore to Backup State (shortest recovery time) on instant recovery, you can only restore synthetic backup sets. When you select Restore to Backup State (shortest recovery time), you cannot restore binlog. {{ br }}· Instant recovery does not support reconnection time, resumption buffer size and speed limit. {{ br }}· During instant recovery, if apparmor or selinux is enabled on the agent, you need to add "/mnt/iscsi_disk/r, /mnt/iscsi_disk/** rwk, " to the configuration file /etc/apparmor.d/usr.sbin.mysqld, then execute the command "/etc/init.d/apparmor reload".  {{ br }}· When you run a restore job for a MySQL cluster, the filter can only be used for log recovery, and the ignored database must be a database with records in the log backup sets.  {{ br }}· After the MySQL cluster is physically restored, the group replication relationship of the cluster will not be automatically restored. You need to manually execute the command "start group_replication" to start group replication. |
```


## Glossary
```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Glossary
---
class: longtable
---
| Term             | Description                              |
| ---------------- | ---------------------------------------- |
| Fast compression | Compress data during backup using fast compression algorithms. |
```