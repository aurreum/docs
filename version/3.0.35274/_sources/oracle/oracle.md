# User Guide for Oracle

## Introduction

Aurreum Data Protection Suite (ADPS) provides the capability for the backup and restore of Oracle databases. This guide introduces how to properly use ADPS to back up and restore Oracle databases.

## Features

### Oracle

```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Oracle Features
---
class: longtable
---
| Feature | Description                                            |
|------|------|
| Backup source | Database, tablespace, datafile, archived log |
| Backup type | Full backup, incremental backup, cumulative incremental backup, continuous log backup, synthetic backup |
| Logical backup | Traditional export, Data Pump export |
| Backup target | Standard storage pool, de-duplication storage pool, database synthetic pool, local storage pool, real-time backup pool, tape library pool, object storage service pool, LAN-Free pool |
| Backup compression | None, fast, tunable          |
| Backup strategy | Immediate, one-time, hourly, daily, weekly, monthly |
| Backup authentication | OS authentication (Linux and UNIX), database authentication |
| Reconnection time | The default value is 10 minutes.  |
| Stop jobs   | Backup and restore jobs can be stopped.              |
| Skip files during backup | Inaccessible files, offline files, read-only files, and unrecoverable archived logs can be skipped. |
| Other features | Reserve archive logs, channels, encryption wallet, custom RMAN command, enable ARCHIVELOG mode, enable BCT, continuous log backup, synthetic backup, instant recovery, Live Recovery, modify archived log backup times |
| Restore type | Media restore, timepoint restore (database, tablespace/PDB, table), recovery testing, traditional export, Data Pump export, instant recovery, Live Recovery, restore archived logs, Data Pump recovery testing, traditional recovery testing |
| Restore location | Original path, new path                |
| Restore granularity | Entire database, datafile, tablespace/PDB, table |
| Restore to different hosts | Restoring to a different host is supported. The operating system type and Oracle major version number of that host should be the same as the source host's. |
| Storage pool replication | Oracle backup sets support storage pool replication.  |
| Restore from target pools | Restoring from target storage pools is supported.  |
| Pre/Post action | The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored. |
| Speed limit | The data transfer speed or disk read and write speed can be limited. |
| D2C                | Data can be directly backed up to object storage services. |
| D2T                | Data can be directly backed up to tape libraries. |
| LAN-Free           | The backup to and restore from LAN-Free storage pools are supported. |
| Hot standby | The hot standby backup and restore are supported. But backing up to a LAN-Free pool is not supported. |
| Synthetic backup | The backup server must be Ubuntu 16.04 or Ubuntu 18.04. The agent supports Windows, AIX, and Linux. Synthetic backup for AIX Oracle with 32T data or above is supported. |
| Modify a job's backup target | Modifying a job's backup source and backup target is supported |
```

### Oracle RAC

```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Oracle RAC Features
---
class: longtable
---
| Feature      | Description                                              |
| ------------------ | ------------------------------------------------------------ |
| Backup source | Database, tablespace, datafile, archived log |
| Backup type                         | Full backup, differential incremental backup, cumulative incremental backup, continuous log backup, synthetic backup |
| Logical backup                      | Traditional export, Data Pump export                         |
| Backup target | Standard storage pool, de-duplication storage pool, database synthetic pool, local storage pool, real-time backup pool, tape library pool, object storage service pool, LAN-Free pool |
| Backup compression                  | None, fast, tunable                               |
| Backup strategy                     | Immediate, one-time, minutely, hourly, daily, weekly, monthly |
| Priority of backup jobs | The backup job is first performed on the node that is selected during the job creation. |
| Reconnection time                   | The default value is 10 minutes.                             |
| Skip files during backup            | Inaccessible files, offline files, and read-only files can be skipped |
| Other features                     | Reserve archive logs, channels, custom RMAN command, enable ARCHIVELOG mode, enable BCT, modify archived log backup times |
| Restore type                        | Media restore, timepoint restore (database, tablespace/PDB, table), recovery testing, traditional export, Data Pump export, instant recovery, Live Recovery, restore archived logs, Data Pump recovery testing, traditional recovery testing |
| Restore location                    | Original path, new path                                      |
| Restore granularity                 | Entire database, datafile, tablespace/PDB, table        |
| Restore to different hosts | Restoring to a different host is supported. The operating system type and Oracle major version number of that host should be the same as the source host's. |
| Storage pool replication            | Oracle backup sets support storage pool replication. |
| Restore from target pool            | Restoring from target storage pools is supported             |
| Pre/post action                     | The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored. |
| Speed limit                         | The data transfer speed or disk read and write speed can be limited |
| D2C                                 | Data can be directly backed up to object storage services.   |
| D2T                                 | Data can be directly backed up to tape libraries.            |
| Synthetic backup                    | The backup server must be Ubuntu 16.04 or Ubuntu 18.04. The agent supports Windows, AIX, and Linux. |
| Modify a job's backup target | Modifying a job's backup source and backup target is supported. |
| IPv6               | Data transfer and management over IPv6 are supported.  |
| Access Key login | Access Key login is supported.                        |
```

### Oracle Database Replication

```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Oracle Database Replication Features
| Feature                                                      | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Backup source                                                | Database                                                     |
| Backup type                                                  | Real-time replication, scheduled replication                 |
| Backup schedule                                              | Minutely, hourly                                             |
| Replication authentication                                   | Operating system authentication (Linux and UNIX), database authentication |
| Backup target                                                | Real-time backup pool                                        |
| Switch the primary and standby, rebuild the standby, activate the standby | It is supported to switch the primary and standby, rebuild the standby, and activate the standby. |
| Read-Only Standby, Enable/Disable Replication                | Read-Only Standby and Enable/Disable Replication are supported. |
| Replication location                                         | Original path, new path                                      |
| Replication granularity                                      | Entire database                                              |
| Hot standby                                                  | Hot standby environment supports database replication.       |
```

## Install and Configure Agent

### Verify Compatibility

ADPS supports the backup and restore of Oracle database single instance, hot standby, RAC cluster, Active Data Guard, and Oracle XE. Before you deploy the agent, verify your operating system (OS) and database versions are supported.

#### Single Instance

```{tabularcolumns} |\Y{0.18}|\Y{0.1}|\Y{0.14}|\Y{0.14}|\Y{0.18}|\Y{0.1}|\Y{0.16}|
```
```{table} Single Instance Compatibility List
---
class: longtable
---
| Database Version | Patch    | Database Bits | OS                          | CPU Architecture | OS Bits | Note                  |
| ---------------- | -------- | ------------- | --------------------------- | ---------------- | ------- | --------------------- |
| 8i               | 8.1.7    | 32            | Windows 2003 SP2            | x86              | 32      |                       |
| 9i               | 9.2.0.4  | 64            | SUSE 9.3                    | x86              | 64      |                       |
| 9i               | 9.2.0.4  | 32            | Red Hat 4                   | x86              | 32      |                       |
| 9i               | 9.2.0.4  | 32            | Red Hat 5                   | x86              | 32      |                       |
| 9i               | 9.2.0.4  | 32            | Windows 2000                | x86              | 32      |                       |
| 9i               | 9.2.0.4  | 32            | Windows 2003                | x86              | 32      |                       |
| 9i               | 9.2.0.1  | 64            | AIX 5.1                     | POWER            | 64      |                       |
| 9i               | 9.2.0.1  | 64            | AIX 5.3                     | POWER            | 64      |                       |
| 9i               | 9.2.0.1  | 64            | SLES 9                      | x86              | 64      |                       |
| 9i               | 9.2.0.8  | 64            | CentOS 4.5                  | x86              | 32      |                       |
| 10g              | 10.2.0.1 | 32            | Red Hat 9                   | x86              | 32      |                       |
| 10g              | 10.2.0.1 | 32            | Red Hat 4                   | x86              | 32      |                       |
| 10g              | 10.2.0.5 | 64            | Red Hat 4                   | x86              | 64      |                       |
| 10g              | 10.2.0.1 | 32            | Red Hat 5                   | x86              | 32      |                       |
| 10g              | 10.2.0.1 | 64            | Red Hat 5                   | x86              | 64      |                       |
| 10g              | 10.2.0.1 | 64            | Red Hat 5.5                 | x86              | 64      |                       |
| 10g              | 10.2.0.5 | 64            | Red Hat 5.5                 | x86              | 64      |                       |
| 10g              | 10.2.0.1 | 32            | Windows 2003                | x86              | 32      |                       |
| 10g              | 10.2.0.2 | 32            | Windows 2003                | x86              | 32      |                       |
| 10g              | 10.2.0.3 | 32            | Windows 2003                | x86              | 32      |                       |
| 10g              | 10.2.0.4 | 32            | Windows 2003                | x86              | 32      |                       |
| 10g              | 10.2.0.2 | 64            | Windows 2003                | x86              | 64      |                       |
| 10g              | 10.2.0.3 | 64            | Windows 2003                | x86              | 64      |                       |
| 10g              | 10.2.0.4 | 64            | Windows 2003                | x86              | 64      |                       |
| 10g              | 10.2.0.5 | 64            | Windows 2003                | x86              | 64      |                       |
| 10g              | 10.2.0.2 | 32            | Windows 2003                | x86              | 32      |                       |
| 10g              | 10.2.0.1 | 32            | Windows 2000                | x86              | 32      |                       |
| 10g              | 10.2.0.4 | 32            | Windows 2008                | x86              | 32      |                       |
| 10g              | 10.2.0.4 | 64            | Windows 2008                | x86              | 64      |                       |
| 10g              | 10.2.0.4 | 64            | Windows 2008 R2             | x86              | 64      |                       |
| 10g              | 10.2.0.1 | 64            | Solaris 9                   | SPARC            | 64      |                       |
| 10g              | 10.2.0.1 | 64            | Solaris 10                  | SPARC            | 64      |                       |
| 10g              | 10.2.0.1 | 64            | Solaris 11                  | SPARC            | 64      |                       |
| 10g              | 10.2.0.1 | 64            | HP-UX B.11.11               | PA-RISC          | 64      |                       |
| 10g              | 10.2.0.1 | 64            | HP-UX B.11.23               | PA-RISC          | 64      |                       |
| 10g              | 10.2.0.1 | 64            | HP-UX B.11.31               | IA64             | 64      |                       |
| 10g              | 10.2.0.1 | 64            | CentOS 4.6                  | IA64             | 64      |                       |
| 10g              | 10.2.0.1 | 64            | SUSE 11 SP4                 | POWER Linux      | 64      | (Big Endian)          |
| 10g              | 10.2.0.1 | 64            | Red Hat 6.5                 | POWER Linux      | 64      | (Big Endian)          |
| 11g              | 11.2.0.1 | 32            | Red Hat 5                   | x86              | 32      |                       |
| 11g              | 11.2.0.1 | 64            | Red Hat 5                   | x86              | 64      |                       |
| 11g              | 11.2.0.1 | 32            | Red Hat 6                   | x86              | 64      |                       |
| 11g              | 11.2.0.1 | 64            | Red Hat 6                   | x86              | 64      |                       |
| 11g              | 11.2.0.1 | 64            | CentOS 6.4                  | x86              | 64      |                       |
| 11g              | 11.2.0.1 | 64            | Red Hat 6.5                 | x86              | 64      |                       |
| 11g              | 11.2.0.2 | 64            | Red Hat 6.5                 | x86              | 64      |                       |
| 11g              | 11.2.0.3 | 64            | CentOS 6.5                  | x86              | 64      |                       |
| 11g              | 11.2.0.4 | 64            | CentOS 6.5                  | x86              | 64      |                       |
| 11g              | 11.2.0.4 | 64            | Oracle Enterprise Linux 6.7 | x86              | 64      |                       |
| 11g              | 11.2.0.1 | 64            | Red Hat 7                   | x86              | 64      |                       |
| 11g              | 11.2.0.4 | 64            | Windows 2008 R2             | x86              | 64      |                       |
| 11g              | 11.2.0.4 | 64            | Windows 2012 R2             | x86              | 64      |                       |
| 11g              | 11.2.0.1 | 64            | AIX 7.1                     | POWER            | 64      | Single instance + ASM |
| 11g              | 11.2.0.4 | 64            | AIX 6.1                     | POWER            | 64      | Single instance + ASM |
| 11g              | 11.2.0.4 | 64            | AIX 7.1                     | POWER            | 64      | Single instance + ASM |
| 11g              | 11.2.0.4 | 64            | Red Hat 6.0                 | x86              | 64      |                       |
| 12c              | 12.1.0.1 | 64            | Windows 2008 R2             | x86              | 64      |                       |
| 12c              | 12.1.0.1 | 64            | Red Hat 6.5                 | x86              | 64      |                       |
| 12c              | 12.1.0.2 | 64            | Solaris 10                  | SPARC            | 64      |                       |
| 12c              | 12.1.0.1 | 64            | Red Hat 6.5                 | x86              | 64      |                       |
| 12c              | 12.2.0.1 | 64            | Red Hat 7.5                 | x86              | 64      |                       |
| 18c              | 18.3.0.0 | 64            | Red Hat 7.0                 | x86              | 64      |                       |
| 18c              | 18.3.0.0 | 64            | Windows 2008 R2             | x86              | 64      |                       |
| 19c              | 19.3.0.0 | 64            | Oracle Enterprise Linux 7.0 | x86              | 64      |                       |
| 21c              | 21.3.0.0 | 64            | CentOS 8                    | x86              | 64      |                       |
```

#### Oracle RAC

```{tabularcolumns} |\Y{0.18}|\Y{0.1}|\Y{0.14}|\Y{0.14}|\Y{0.18}|\Y{0.1}|\Y{0.16}|
```
```{table} Oracle RAC Compatibility List
---
class: longtable
---
| Database Version | Patch      | Database Bits | OS                        | CPU Architecture | OS Bits | Note                           |
| ---------------- | ---------- | ------------- | ------------------------- | ---------------- | ------- | ------------------------------ |
| 9i               | 9.2.0.4    | 64            | SUSE 9.3                  | x86              | 64      | Two nodes                      |
| 9i               | 9.2.0.8    | 64            | Red Hat 4                 | x86              | 64      | Two nodes                      |
| 10g              | 10.2.0.1   | 64            | Red Hat 4                 | x86              | 64      | Two nodes                      |
| 10g              | 10.2.0.1   | 64            | Red Hat 5                 | x86              | 64      | Two nodes                      |
| 10g              | 10.2.0.1   | 64            | Windows 2008 R2           | x86              | 64      | Two nodes                      |
| 10g              | 10.2.0.5   | 64            | AIX 6.1                   | POWER            | 64      | Two nodes                      |
| 11g              | 11.2.0.1   | 64            | HP-UX B.11.31             | IA64             | 64      | Two nodes                      |
| 11g              | 11.2.0.1   | 64            | AIX 7.2                   | POWER            | 64      | Two nodes                      |
| 11g              | 11.2.0.3   | 64            | Windows 2008 R2           | x86              | 64      | Two nodes                      |
| 11g              | 11.2.0.3   | 64            | Solaris 10                | SPARC            | 64      | Two nodes                      |
| 11g              | 11.2.0.4   | 64            | Red Hat 5                 | x86              | 64      | Two nodes                      |
| 11g              | 11.2.0.4   | 64            | Oracle Linux 6.4          | x86              | 64      | Two nodes                      |
| 11g              | 11.2.0.4   | 64            | Red Hat 6.5               | x86              | 64      | Two nodes                      |
| 11g              | 11.2.0.4   | 64            | iSoft Server 3.0          | x86              | 64      | Two nodes                      |
| 11g              | 11.2.0.4   | 64            | Windows 2008 R2           | x86              | 64      | Three nodes/multiple instances |
| 11g              | 11.2.0.4   | 64            | Solaris 10                | SPARC            | 64      | Two nodes                      |
| 11g              | 11.2.0.4   | 64            | AIX 7.1                   | POWER            | 64      | Two nodes                      |
| 12c              | 12.1.0.2   | 64            | CentOS 6                  | x86              | 64      | Two nodes                      |
| 12c              | 12.1.0.2   | 64            | Red Hat 6.5               | x86              | 64      | Two nodes                      |
| 12c              | 12.1.0.2   | 64            | Windows 2008 R2           | x86              | 64      | Two nodes                      |
| 12c              | 12.2.0.1   | 64            | CentOS 6.7                | x86              | 64      | RAC One Node                   |
| 12c              | 12.2.0.1   | 64            | Oracle Enterprise Linux 6 | x86              | 64      | Two nodes                      |
| 18c              | 18.3.0.0   | 64            | Windows 2008 R2           | x86              | 64      | Two nodes                      |
| 19c              | 19.3.0.0.0 | 64            | Red Hat 7.6               | x86              | 64      | Two nodes                      |
```

#### Oracle Hot Standby

```{table} Oracle Hot Standby Compatibility List
| Database Version | Patch    | Database Bits | OS              | CPU Architecture | OS Bits |
| ---------------- | -------- | ------------- | --------------- | ---------------- | ------- |
| 11g              | 11.2.0.1 | 64            | Red Hat 6.5     | x86              | 64      |
| 11g              | 11.2.0.1 | 64            | Red Hat 6       | x86              | 64      |
| 11g              | 11.2.0.1 | 64            | Windows 2008 R2 | x86              | 64      |
| 11g              | 11.2.0.1 | 64            | AIX 6.1         | POWER            | 64      |
```

#### Oracle Data Guard

```{table} Oracle Data Guard Compatibility List
| Database Version | Patch    | Database Bits | OS                        | CPU Architecture | OS Bits |
| ---------------- | -------- | ------------- | ------------------------- | ---------------- | ------- |
| 11g              | 11.2.0.1 | 64            | CentOS 6.4                | x86              | 64      |
| 11g              | 11.1.0.1 | 64            | CentOS 6.5                | x86              | 64      |
| 11g              | 11.2.0.1 | 64            | Red Hat 6                 | x86              | 64      |
| 11g              | 11.2.0.1 | 64            | Windows 2008 R2           | x86              | 64      |
| 11g              | 11.1.0.6 | 64            | Windows 2008 R2           | x86              | 64      |
| 12c              | 12.2.0.1 | 64            | Oracle Enterprise Linux 6 | x86              | 64      |
```

#### Oracle XE

```{table} Oracle XE Compatibility List
| Database Version | Patch    | Database Bits | OS              | CPU Architecture | OS Bits |
| ---------------- | -------- | ------------- | --------------- | ---------------- | ------- |
| 11g              | 11.2.0.1 | 64            | Windows 2012 R2 | x86              | 64      |
| 11g              | 11.1.0.1 | 64            | CentOS 6.6      | x86              | 64      |
```

### Download Agent Package

Open a browser and log in to ADPS as the admin. Click **Resource** -> **Install Agent** icon. You can download the installation packages according to your needs.

![](../images/01-agent/oracle/oracle-01.png)

### Install and Configure Agent on Windows

#### Download Installation Package

Select **Windows** and click **Download Windows agent**.

![](../images/01-agent/oracle/oracle-02.png)

#### Install Agent on Windows

1. Upload the installation package to the target host.

2. Double-click the package to install it according to the setup wizard and click **Next**.

3. The package chooses the database resources, files, or applications installed on the host by default. Select the **Oracle** component and click **Next**.

   ![](../images/01-agent/oracle/oracle-03.png)

4. Set the **Backup Server Host**, **Backup Server Port**, and **Access Key**. Click **Next**.

   ![](../images/01-agent/oracle/oracle-04.png)

5. Select **Destination Folder** and click **Next** to install the software. Wait for the installation to complete.

### Install and Configure Agent on Linux

1. Select **Linux** and **Oracle**. Copy an installation command.

   ![](../images/01-agent/oracle/oracle-05.png)

2. Paste the command on the command line and press Enter to execute the installation.

   ```
   [root@rhel69 ~]# curl -o- "http://192.168.18.60:50305/d2/update/script?modules=oracle&location=http%3A%2F%2F192.168.18.60%3A50305&access_key=0daed99203711fe2c50555c8a8334904&rm=&tool=curl" | sh
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
   101  7898    0  7898    0     0   815k      0 --:--:-- --:--:-- --:--:-- 1285k
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
   100 57.1M  100 57.1M    0     0  40.0M      0  0:00:01  0:00:01 --:--:-- 36.9M
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
   100 6151k  100 6151k    0     0  28.5M      0 --:--:-- --:--:-- --:--:-- 28.5M
     % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
   100 12.2M  100 12.2M    0     0  54.6M      0 --:--:-- --:--:-- --:--:-- 54.6M
   ```

### Install and Configure Agent on Solaris

1. Select the corresponding installation package according to the Solaris system, bit and Oracle version. Take the Solaris 9 SPARC 64-bit as an example. Execute the unzip command with root privileges:

   ```
   sudo gunzip adps-version-sol9-sparc64-local.gz
   ```

2. Execute the following installation command:

   ```
   sudo pkgadd -d /tmp/adps-version-sol9-sparc64-local
   ```

3. Select and enter the module to be installed such as: AURREUMadps-common, AURREUMadps-agent, AURREUMadps-agent-oracle.

4. Input y or press Enter to go to the next step according to the prompts.

5. After the installation is completed, execute the following configuration command:

   ```
   sudo /etc/init.d/adps-agent config
   ```

   Follow the prompts to enter the IP address and port of the Backup Server.

6. Configure Oracle Home directory:

   ```
   sudo /etc/init.d/adps-agent config oracle
   ```

### Install and Configure Agent on HP-UX

1. Unzip adps-version-ia64.depot.gz to get installation files. Execute the unzip command with root privileges:

   ```
   sudo gunzip /tmp/adps-version-ia64.depot.gz
   ```

2. Install the agent and Oracle module. For example, the absolute path of the unzipped package is /tmp/adps-version-ia64.depot. Execute the command with root privileges as follows:

   ```
   swinstall -s /tmp/adps-version-ia64.depot  adps.agent-oracle
   ```

3. After the installation is completed (or when the current backup server needs to be changed), execute the following configuration command:

   ```
   sudo /sbin/init.d/adps-agent config
   ```

4. If the ORACLE_HOME cannot be recognized, execute the configuration commands and follow the prompts:

   ```
   sudo /sbin/init.d/adps-agent config oracle
   ```

   ```
   sudo /sbin/init.d/adps-agent restart
   ```

### Check Successful Installation

After the successful installation, log in to ADPS as the admin. The host is listed on the **Resource** page.

![](../images/01-agent/oracle/oracle-07.png)

## Activate License and Authorize User

This chapter is applicable to the operation of a single agent. If you have multiple agents, you can deploy them first and then carry out batch activation and authorization. See *Batch Register/Activate/Authorize* in the *Administrator's Guide* for more details.

### Register Host

Log in to ADPS as the admin and go to **Resource**. Select the host that you need to activate and click the **Register** icon.

![](../images/01-agent/oracle/oracle-08.png)

### Activate License

In the pop-up **Activate** window, select the resource you want to activate. Click **Submit**.

![](../images/01-agent/oracle/oracle-09.png)

### Authorize User

After the successful activation, authorize users to operate the resource in the pop-up **Authorize** window.

![](../images/01-agent/oracle/oracle-10.png)

## Before You Begin

### Check Database State

Check the Oracle instance state on the agent. The Oracle instance should be in the "OPEN" or "mount" state for the backup and restore.

![](../images/01-agent/oracle/oracle-11.png)

### Check Resource

Log in to ADPS as the operator and go to **Resource**. You can see that the activated and authorized resource is on the list and its state is "Online". If the resource is not available, check *Activate License and Authorize User*.

![](../images/01-agent/oracle/oracle-12.png)

### Check Storage Pool

Log in to ADPS as the operator, go to **Storage Pool**, and verify a storage pool is available. If a storage pool is not present, contact the admin to create one and assign permissions to the operator.

![](../images/01-agent/oracle/oracle-13.png)

## First Time Login

1. Before creating the first backup and restore, click **Login** beside the resource to log in to Oracle first.

   ![](../images/01-agent/oracle/oracle-14.png)

2. Oracle supports three authentication methods: **Database**, **OS**, and **Access Key**.

- Database authentication: The login account should have the SYSDBA privilege and the roles of RESOURCE and CONNECT. You can log in to Oracle 12c and higher versions as users with the SYSBACKUP privilege.
- OS authentication: You can use root or Oracle installation user to log in to the databases. OS authentication is not supported for Oracle databases on Windows. Logical import/export and table restore are not supported when you use OS authentication. If you use root to log in to the server, it will automatically log in to all the database instances on that server. If you use the Oracle installation user, it will only log in to the instances that the user installs.
- Access Key authentication: You can use the Access Key of the user who has been assigned the resource to log in to the databases. Jobs can be performed without a password, which has potential data security risks.

3. Select **Authentication**. Enter **User** and **Password**. Click **Login**.

   ![](../images/01-agent/oracle/oracle-15.png)

### Oracle XE

To log in to Oracle XE, see *First Time Login*.

### Oracle RAC

After the Oracle RAC resource is activated, use the admin to bind the Oracle RAC nodes into a cluster on the **Cluster** page and log in to the Oracle resources on all nodes. You can log in to all resources on the same cluster, or log in to the resources one by one on the **Backup** page.

### Oracle Hot Standby

After the Oracle hot standby is activated, use the admin to bind the nodes into a hot standby cluster on the **Cluster** page and log in to the Oracle resources on all nodes. To log in to Oracle hot standby, please refer to the *First Time Login*.

### Oracle One Node

After the Oracle One Node is activated, use the admin to bind the nodes into a cluster on the **Cluster** page and log in to the Oracle resources of all nodes. To log in to Oracle One Node, please refer to the *First Time Login*.

### Oracle Data Guard

Only the backup and restore of the primary database of Oracle Data Guard are supported. To log in to Oracle Data Guard, please refer to the *First Time Login*.

### Oracle Active Data Guard

The backup and restore of the primary and standby databases of Oracle Active Data Guard are supported. To log in to Oracle Active Data Guard, please refer to the *First Time Login*.

> Note: To use Access Key authentication, the admin should enable the **Access Key login instance** option in the **Settings** -> **Security**.

## Create Backup Jobs

This chapter introduces how to back up the Oracle databases.

> Note: The same database's full backups, cumulative incremental backups, and log backups cannot be stored in different storage pools.

### Prerequisites

- The agent has been installed. For installation, please refer to *Install and Configure Agent*.
- The license has been activated and the resource has been assigned to users. For details, see *Activate License and Authorize User*.
- Log in to ADPS as the *operator*.

### Create Full Backup Jobs

1. Go to the **Backup** page. Select the Oracle host and instance.

2. Select **Full** as the backup type and choose databases to be backed up.

   ![](../images/01-agent/oracle/oracle-16.png)

3. Select a **Backup Target**. You can choose a standard storage pool, de-duplication storage pool, tape library pool, object storage service pool, and LAN-Free pool.

4. Select a **Backup Schedule** to set the execution time of the backup job. For details, see *Backup Schedule Operation*. We recommend running a full backup on a weekly basis.

5. Set **Backup Options** including common and advanced options.

   - **Common options**:

   ![](../images/01-agent/oracle/oracle-17.png)

   **Compression**: The **Fast** option is enabled by default.

   > - None: During the backup, the data will not be compressed.
   >
   > - Tunable: You can customize the compression level. This option requires an activated feature of Advanced Compression.
   >
   > - Fast: During the backup, the data will be compressed by fast compression algorithms.

   **Channels**: This option can improve backup efficiency. The default number is 1 and the range is 1 to 255. For details, please refer to *Channel Number Configuration*.

   > Note:
   > - When Oracle is backed up to a tape library pool, the **Channels** option will provide a default value based on the number of drives in the tape library, the number of tapes, and whether the backup source is a RAC. The default value is the maximum number that can be entered.
   > - Restoring Oracle from a tape library pool only supports one channel and the number cannot be modified.

   **When the database is in NOARCHIVELOG mode**:

   - Send an alert message and cancel the job: When the database is in NOARCHIVELOG mode, ADPS will cancel the backup job and send an alert message.
   - Allow offline backup: When the database is in NOARCHIVELOG mode, ADPS will send an alert message and perform the backup job offline.
   - Enable ARCHIVELOG: When the database is in NOARCHIVELOG mode, ADPS will enable the ARCHIVELOG mode according to the customized archive log directory or the database default directory, send an alert message, and then perform the backup job.

   **BCT**: BCT (Block Change Tracking) is a feature introduced in Oracle 10g. It mainly serves the RMAN incremental backups. You can enable BCT to improve the incremental backup performance. After enabling BCT, you can customize the BCT directory or use the database default BCT directory.

   **Minimal supplemental logging**: The log files generated after this option is enabled can be used for LogMiner. This operation is a one-time operation.

   **Force logging**: You can enable the force logging mode of the database.

   **Reserve archive logs**: Specify whether to delete the archive logs in the database after the backup. The **Delete all archive logs** option is enabled by default.

   - Delete all archive logs: Delete the backed-up archive logs after the backup.
   - Reserve archive logs for N day(s)/hour(s): Delete the backed-up archive logs that exist for longer than the specified N days/hours.
   - Do not delete archive logs: Do not delete the backed-up archive logs after the backup.

   **Encryption wallet password**: If you enable Encryption Wallet, you are required to enter the encryption wallet password when performing database backups.

   - **Advanced options**:

   ![](../images/01-agent/oracle/oracle-18.png)

   **Reconnection time**: 1-60 are supported. The unit is minute.

   **Speed limit**: You can limit the data transfer speed or data read and write speed. The unit is MiB/s.

   **Precondition**: The precondition is checked before the job starts. The job execution is aborted and it becomes idle when the precondition is invalid.

   **Pre/Post action**: The pre action is executed after the job starts and before the resource is backed up or restored. The post action is executed after the resource is backed up or restored.

   **Archive log backup times**: You can set the archive log backup times. 1-5 are supported.

   **Custom RMAN commands**: You can customize RMAN commands and insert them between the allocate channel command and the backup command. For example, you can use "set maxcorrupt for datafile xxx to N;" to ignore bad blocks of some files.

   **Skip**: You can set this option to skip inaccessible files, offline files, as well as read-only files, and send alerts when these files are skipped.

6. Set **Job Name** and confirm the job information. Click **Submit**.

### Create Incremental/Cumulative Incremental Backup Jobs

We recommend running full database backups regularly (such as weekly) and incremental/cumulative incremental backups at short intervals (such as daily). You can use incremental/cumulative incremental backups to back up data that has changed since the last backup so that you have at least one recoverable point in time every day.

- Creating an incremental/cumulative incremental backup is the same as creating a full backup. Select **Incremental** or **Cumulative Incremental Backup** as the backup type and choose databases.

![](../images/01-agent/oracle/oracle-19.png)

> Note:
> - If the database has not been fully backed up, or has not been fully backed up after restore, the first cumulative incremental backup will be run as a full backup by default.

### Create Logical Backup Jobs

Logical backup jobs include: Data Pump Export and Traditional Export. ADPS supports the export of database, tablespace, and schema. The export of database and tablespace requires a user with DBA privileges (SYSDBA cannot be used), and the export of schema requires the corresponding user account.

> Note:
>
> - Data Pump Export will be hidden when the Oracle version is older than 10g.
> - When one of the following conditions occurs, Data Pump Export will be disabled:
>   - The Oracle database authentication method is OS authentication.
>   - The current instance status is STARTED or MOUNTED.
>   - The database OPEN_MODE status is READ ONLY.
>   - The Oracle database is a standby.

1. Creating a logical backup is similar to creating a full backup. Select **Data Pump Export** or **Traditional Export** as the backup type and choose databases.

   ![](../images/01-agent/oracle/oracle-20.png)

   > - When Traditional Export is selected and the Oracle major version is newer than or equal to 11, the job may fail.
   > - When the database OPEN_MODE status is READ WRITE, it is recommended to use Data Pump Export.

2. Select a **Backup Source**:

   - Databases: Select the database name, and the entire database will be exported. Select the tablespace under the database name, and the tablespace will be exported.

   - User: Select users under the user list, and the schemas will be exported.

3. Select a **Backup Target**. The data will be exported to the selected storage pool.

4. Select a **Backup Schedule** and set the execution time of the backup job.

5. Set **Backup Options** according to your needs. The options include compression, channels, when the database is in NOARCHIVELOG mode, and reserve archive logs.

   ![](../images/01-agent/oracle/oracle-21.png)

6. Set a **Job Name** and check whether the job information is correct. Click **Submit**.

### Create Log Backup Jobs

Based on periodic full, incremental, and cumulative incremental backups, you can also add frequent log backups to back up only the archive logs of the database. The appropriate log backup frequency depends on the balance between your tolerance for the risk of work loss and the number of log backups you can store, manage, and potentially restore. Performing a log backup every 15 to 30 minutes may be sufficient. But if your business requires minimizing the risk of work loss, consider taking more frequent log backups. More frequent log backups also increase the frequency of log truncation, resulting in smaller log files.

The log backup is classified into **Log** and **Continuous Backup**.

- **Log**: It backs up Oracle archive logs. When you select periodic backup schedules, you can set the backup conditions at the **Automatically backup archive logs when** option.
- **Continuous backup**: Oracle continuous log backup means that the backup system continuously backs up Oracle redo logs and archive logs to the real-time backup pool. After a continuous log backup job is created, it is always running (except for job suspension caused by external factors).

> Note:
>
> - Before you create continuous log backups, perform a full backup to ensure that the storage pool has an available full backup set.
> - Continuous log backups can only back up to the real-time backup pool. Replications between real-time backup pools are supported.
> - You can only create one continuous log backup job for one database instance. To create a new continuous log backup, the old one should be stopped or deleted.

1. Creating a log backup is similar to creating a full backup. Select **Log** or **Continuous Backup** as the backup type and choose databases.

   ![](../images/01-agent/oracle/oracle-22.png)

2. Select a **Backup Target**. The logs will be backed up to the selected storage pool.

3. Select a **Backup Schedule** to set the execution time of the backup job. You do not need to set the backup schedule for continuous log backups.

4. Set **Backup Options** according to your needs. The options include compression, reserve archive logs, channels, reconnection time, speed limit, precondition and pre/post action. Only compression and pre action are allowed for continuous log backups.

5. Set a **Job Name** and check whether the job information is correct. Click **Submit**.

### Create Synthetic Backup Jobs

The first Oracle synthetic backup job will be a full backup and the subsequent ones will be incremental. Each incremental backup will be synthesized with the last backup to get a new full backup. Then the synthetic backup uses the snapshot function of the database synthetic pool to generate a snapshot point. Oracle synthetic backups support:

- Linux, AIX, as well as Windows 2008 and later platforms;
- Oracle 10g and later versions;
- FC and iSCSI storage types.

1. Creating a synthetic backup is similar to creating a full backup. Select **Synthetic Backup** as the backup type and choose databases.

   ![](../images/01-agent/oracle/oracle-23.png)

2. Select a **Data Pool** and set the **Staging Disk Size**. The datafiles will be backed up to the selected database synthetic storage pool. In the advanced options, you can fill in the ratio between the incremental storage disk and the real-time size of the database to set the volume size for incremental backup set storage. The default value is 100%, and the range is 0-100%. Select a **Log Pool**. Files except for datafiles, including archive logs, control files, and PFILE/SPFILE, will be backed up to the selected storage pool. If the storage type of the database synthesis pool is iSCSI, you need to install the iSCSI initiator on the agent. If it is FC, the agent server needs to be equipped with an HBA card and installed the corresponding HBA API Driver.

   ![](../images/01-agent/oracle/oracle-24.png)

3. Select a **Backup Schedule** to set the execution time of the backup job.

4. Set **Backup Options**. The options include compression, reserve archive logs, BCT, channels, speed limit, precondition, pre/post action and custom RMAN commands. You can set them according to your needs. If the database has encrypted tablespaces, you need to enter the correct wallet password in the **Encryption wallet password** option.

   ![](../images/01-agent/oracle/oracle-25.png)

   - **Advanced options**:

   ![](../images/01-agent/oracle/oracle-26.png)

   **Direct I/O**: To avoid using system cache, this option will not change the filesystem_options parameter of the database.

   **Disable synchronous I/O**: It can speed up the backup speed.

   **Synthesis method**:

   - Synthesize by Oracle RMAN: This method is selected by default.
   - Frontend pipeline synthesis: Synthesize the datafile immediately after its incremental backup is completed.

   > Note: When you select **Synthesize by Oracle RMAN** and the Oracle database parameter DISK_ASYNCH_IO is set to false, ADPS will send an alert message and automatically change the synthesis method to **Frontend pipeline synthesis**.

   **Scan FC ports**: The FC ports should be scanned if you select database synthetic pool with an FC protocol for synthetic backups. To avoid scanning all ports, you can select to scan a specified FC port.

5. Set a **Job Name** and check whether the job information is correct. Click **Submit**.

## Create Restore Jobs

This chapter introduces how to restore Oracle databases. ADPS provides various restore methods for different needs, including media restore, timepoint restore, logical import, restore archived logs, instant recovery, and recovery testing.

### Prerequisites

- The database has a completed backup job. See *Create Backup Jobs*.
- To restore to another host, install an agent on that host, activate its license, and assign permissions to users.

### Create Media Restore Jobs

When a media error occurs in Oracle databases such as datafile corruption or accidental deletion, you can restore the databases or some datafiles through media restore jobs. If the redo logs and archive logs are intact, running media restores will not lose data. Before you create restore jobs, start the database instance but do not need to mount it.

1. Select the Oracle host and instance. Click **Next**.

2. Select **Media** as the restore type and choose databases to be restored. You can modify the path for restored files. Click **Next**.

   ![](../images/01-agent/oracle/oracle-27.png)

3. Select a **Restore Schedule**. Only immediate and one-time schedules are supported. Click **Next**.

4. Set **Restore Options** including reset log, channels, reconnection time, speed limit, precondition and pre/post action. If the database has encrypted tablespaces, you need to enter the correct wallet password in the **Encryption wallet password** option. You can disable synchronous I/O to speed up the restore job.

   ![](../images/01-agent/oracle/oracle-29.png)

5. After confirming the restore information, click **Submit** and wait for the execution.

### Create Database Restore Jobs

When a logical error or disaster occurs in Oracle databases, you can restore the databases to a specified point-in-time state through timepoint restore jobs. Database timepoint restore supports restoring to the source or a different host. Before you create the restore job, start the target database instance but do not need to mount the database.

1. Select the Oracle database host and instance. Click **Next**.

2. Select **Databases** under the **Timepoint Restore** and set the restored database information. Specify the restore point in time by Restore to the latest time, Restore to point in time, Restore to SCN, Restore to backup state (shortest recovery time), and Analyze redo log files to find the exact SCN to restore to. Click **Next**.

   ![](../images/01-agent/oracle/oracle-28.png)

3. Select the restore point in time by dragging the slider control or entering a specific point in time or SCN. For the restore content, you can select datafiles, control files, redo logs, and temporary files. PFILE/SPFILE, password files, Listener configuration, and TNSNames configuration. You can redirect the restored data via the **Modify the path of restore files** option.

4. Select a **Restore Target**. You can restore to the source or a different host. Or you can restore to a database on a different host with the same operating system and Oracle version (major version number). Click **Next**. (It allows restoring to another instance with the same version on the source host. But the source instance should be shut down during the restore.)

5. Select a **Restore Schedule**.

6. Set **Restore Options**.

   ![](../images/01-agent/oracle/oracle-30.png)

   ![](../images/01-agent/oracle/oracle-31.png)

   The options include reset log, channels, reconnection time, speed limit, precondition, and pre/post action. You can set them according to your needs.  If the database has encrypted tablespaces, you need to enter the correct wallet password in the **Encryption wallet password** option. You can disable synchronous I/O to speed up the restore.

   If you enable continuous log backups, you can set **Download buffer size** and **Reserve free disk space** when recovering continuous archive logs.

   - Download buffer size: If the size of archive logs is greater than or equal to the value of this option, the log download will be stopped and then continue after the previously downloaded logs are applied and deleted.
   - Reserve free disk space: If the free space of the disk (the target disk that save downloaded archive logs) is less than or equal to the value of this option, the log download will be stopped and then continue after the previously downloaded logs are applied and deleted.

   > Note: If the parameters are not filled properly, the recovery of logs will be slowed down.

7. Confirm the job information is correct and submit the job.

### Create Tablespace Restore Jobs

Oracle tablespace timepoint restore can restore one or more non-SYSTEM tablespaces to a specified point in time without affecting other tablespaces. The Oracle 12c PDB timepoint restore can restore a PDB to a specified point in time without affecting other PDBs. Only databases in ARCHIVELOG mode and with Oracle 10.2.0.5 as well as later versions are supported for Tablespace Restore.

Limitations of tablespace timepoint restore:

- 11g R1 and earlier versions do not support restoring tablespaces that are deleted by DROP or restoring renamed tablespaces to a point in time before the rename operation.
- If the tables in the tablespace to be restored have constraints to other tables in other tablespaces, the related tablespaces must be restored together.
- A tablespace with SYS user objects cannot be restored.
- A tablespace with table replications cannot be restored.
- Snapshot logs and snapshot tables cannot be restored.
- VARRAY columns, nested tables, and external files cannot be restored.
- A tablespace with rollback segments cannot be restored.

> Note: Since different versions of Oracle have different limitations on tablespace timepoint restore, please refer to the official Oracle documentation for details.

1. Go to the **Restore** page. Select the source host and Oracle instance. Select **Tablespace** as the restore type.

   ![](../images/01-agent/oracle/oracle-32.png)

2. Select a **Restore Target**. You can only choose the source host and instance as the restore target.

3. Select a **Restore Schedule** to set the execution time of the job.

4. Set **Restore Options**. Enter **Auxiliary destination**. It stores the temporary files required for auxiliary instances. Ensure that its space is larger than the size of the tablespace or PDB to be restored and the Oracle database user has the read and write permissions. If the database contains encrypted tablespaces, enter the correct wallet password in the **Encryption wallet password** option.

   ![](../images/01-agent/oracle/oracle-33.png)

5. Submit the restore job and wait for the completion.

### Create Table-Level Restore Jobs

Oracle table-level restore supports exporting a user's single table to a point-in-time state. Oracle 10.2.0.5 and later versions are supported.

> Note: Since table-level restore is performed on the source host, ADPS should connect to the source database. Please ensure that the database starts the listener, and the agent can be connected to the database with a net service name. You can use the tnsping or sqlplus command to confirm whether the database can be connected.

1. Go to the **Restore** page. Select the Oracle database host and instance. Select **Table** under **Timepoint Restore** as the restore type. Click **Next**.

   ![](../images/01-agent/oracle/oracle-34.png)

2. Select a **Restore Schedule** to set the execution time of the job.

3. Set **Restore Options** including common and advanced options.

   You can set common options including channels, auxiliary directory, and data cache directory. **Auxiliary directory** stores the temporary files required for auxiliary instances. Please ensure that its space is larger than the size of the tablespace or PDB to be restored and the Oracle database user has the read and write permissions. **Data cache directory** stores the export files of tables. If the database contains encrypted tablespaces, you also need to enter the correct wallet password in the **Encryption wallet password** option.

   ![](../images/01-agent/oracle/oracle-35.png)

   You can set advanced options including reconnection time, speed limit, precondition and pre/post action.

   ![](../images/01-agent/oracle/oracle-36.png)

4. Set a **Job Name** and check whether the job information is correct. Click **Submit**.

### Create Logical Import Jobs

1. Go to the **Restore** page. Select the host and Oracle resource.

2. Select **Data Pump Import** or **Traditional Import** as the restore type. If you have not performed **Data Pump Export** or **Traditional Export**, you cannot select the corresponding import.

   ![](../images/01-agent/oracle/oracle-37.png)

   - Select a point in time under the database list. The whole database will be imported.
   - Select a point in time under the tablespace list. A tablespace will be imported.
   - Select a point in time of a certain user under the user list. A schema will be imported. Multi-user import is supported.

3. Select a **Restore Target**. We recommend restoring to a target with the same version as the source database. Limitations may exist among different versions and the import may fail.

4. Set the **Restore Schedule**.

5. Set **Restore Options**. Select a user, enter the password, and set the data cache directory. You can set reconnection time, speed limit, precondition, and pre/post action according to your needs.

   ![](../images/01-agent/oracle/oracle-38.png)

6. Confirm the job information, click **Submit** and wait for the job completion.

### Create Log Restore Jobs

This restore type is only applicable to the ARCHIVELOG mode. It can restore the archived logs and online logs in a specified range to the archive log directory in the database on the source or a different host. Without an archive log directory, the logs will be restored to the db_recovery_file_dest directory. The log range includes the logs in continuous log backup jobs and the time periods selected in log backups. All archive logs must be backed up in the same storage pool.

1. Select the Oracle host and instance. Select **Restore Archived Logs** and click **Next**.

   ![](../images/01-agent/oracle/oracle-39.png)

2. Set the **Restore Target**. You can restore to the source database or a database on a different host with the same operating system and Oracle version (major version number). The target database should be at least in the MOUNTED state.

3. Set the **Restore Schedule**. Immediate and one-time schedules are supported. Click **Next**.

4. Set **Restore Options** including channels, archived logs destination, reconnection time, speed limit, precondition, and pre/post action. You can set them according to your needs. Click **Next**.

5. Confirm the job information and click **Submit**.

### Create Recovery Testing Jobs

Oracle Recovery Testing can be used to create periodic restore jobs. It can restore the Oracle backup sets to a specified target host to verify the availability of the backup sets.

1. Select the Oracle host and instance. Click **Next**.

2. Select **Recovery Testing** as the restore type. You do not need to select the point in time of backup sets. The job will recover the latest backup sets of the source host.

   ![](../images/01-agent/oracle/oracle-40.png)

3. Select a **Restore Target**. The target cannot be the source instance. The database version should be the same as the source database version.

4. Set the **Restore Schedule**. It supports hourly, daily, weekly, and monthly schedule types. Click **Next**.

5. Set **Restore Options** including channels and reset log. If you do not enable the **Open database with "resetlogs" after restore** option, then in the next run, the datafile will not be restored, while the archived logs will be used to restore the database to the latest backup state.

6. Confirm the job information and click **Submit**.

7. Wait for the periodic execution. The job will recover the latest backup sets of the source host.

### Create Logical Recovery Testing Jobs

Oracle Logical Recovery Testing can be used to create periodic logical import jobs. It can logically import the Oracle backup sets to a specified target host to verify the availability of backup sets.

1. Select the Oracle database host and instance. Click **Next**.

2. Select **Data Pump Recovery Testing** or **Traditional Recovery Testing**. The job will recover the latest backup sets of the source host.

   ![](../images/01-agent/oracle/oracle-41.png)

3. Select a **Restore Target**. We recommend restoring to a target with the same version as the source database. Limitations may exist between different versions and the import may fail.

4. Set the **Restore Schedule**. It supports hourly, daily, weekly, and monthly schedule types. Click **Next**.

5. Set **Restore Options**. Select the user, enter the password, and set the data cache directory. You can also set the reconnection time, speed limit, precondition, and pre/post action according to your needs.

6. Confirm the job information and Click **Submit**.

7. Wait for the periodic execution. The job will recover the latest backup sets of the source host.

### Create Instant Recovery Jobs

Only databases with complete Oracle synthetic backups are supported for Instant Recovery. This restore type can reduce the recovery time by mounting the synthetic backup set at the corresponding point in time in the database synthetic pool.

1. Select the Oracle database host and instance. Click **Next**.

2. Select **Instant Recovery**. It supports restoring to a specified point in time, SCN, and the backup state (shortest recovery time).

   ![](../images/01-agent/oracle/oracle-42.png)

   To restore the control files and log files to different directories on the source database, you can click **Modify the path of restore files** and edit the path of each file. However, when creating Instant Recovery, you cannot modify the path of datafiles, but can modify the path of other files.

3. Select **Restore Target**. It supports restoring to the source database and a database on a different host with the same operating system and Oracle version. The target database should be at least in the STARTED state.

4. Set **Restore Schedule**. You can set the execution time of the job.

5. Set **Restore Options** including common and advanced options.

   ![](../images/01-agent/oracle/oracle-43.png)

   In the common options, you can set channels, reset log, and archived logs destination.

   About **Archived logs destination**:

   - **Separate volume**: A separate volume in the storage pool to store archived logs. The volume will be deleted after the mounted data is unmounted.
   - **Same volume as database copy**: Shared volume for backup set snapshots, i.e. same volume as backup set.
   - **Local directory**: Local file system directory. It can list the space usage of the directory and help you to determine the required space of archived logs.

   ![](../images/01-agent/oracle/oracle-44.png)

   Advanced options include precondition, pre/post action, and storage protocol. In the **Storage protocol** option, you can create a new FC Target or iSCSI Target for the restore job. It supports restoring the backup sets from the iSCSI database synthetic pool to the FC and vice versa. This option requires the operator has the admin permissions. You can enable the **Disable synchronous I/O** option to speed up the recovery.

6. Confirm the job information and click **Submit**.

7. After the instant recovery job is completed, you can find a corresponding record on the **CDM** interface. To delete the instance, you can click the **Delete** icon. The agent will shut down the instance and delete the mounted datafiles.

### Create Oracle Live Recovery Jobs

Oracle Live Recovery is designed to migrate the mounted database back to the source database smoothly after the source database undergoes emergency instant recovery due to a crash or other reasons.

You can create Oracle Live Recovery jobs in two ways:

- Go to the **Restore** page. Select the host and Oracle instance where the mounted database resides.
- Go to the **CDM** page and find the instant recovery job record. Click the + icon beside the record to start Live Recovery.

1. Select the Oracle host and instance. Click **Next**.

2. Select **Live Recovery** as the restore type. The default restore destination is the original file path of the synthetic backup host. To restore the control files and log files to different directories, you can click **Modify the path of restore files** and edit the path of each file.

   ![](../images/01-agent/oracle/oracle_live_recovery-01.png)

3. Select a **Restore Target**. It supports restoring to the source database and a database in a different host with the same operating system and Oracle version. The target database should be at least in the STARTED state.

4. Set the **Restore Schedule**. You can set the execution time of the job.

5. Set **Restore Options** including common and advanced options.

   ![](../images/01-agent/oracle/oracle_live_recovery-02.png)

   Common options: Switch instance, host address of the mounted instance, target host address, and delete synced logs of the mounted instance.

   **Switch instance**:

   - **Manual**: After the recovery, you should switch the instances manually.
   - **Immediate**: After the recovery, ADPS will switch the instances immediately.
   - **Specify time**: When the specified time is reached, ADPS will shut down the source instance and open the target instance.
   - **Delay**: After the recovery, ADPS will switch the instances after the set time.

   **Sync period**: Set the periodic time for archived log synchronization before switching instances.

   **Archived logs cache directory for the target host**: Specify the cache directory for downloading archived logs.

   ![](../images/01-agent/oracle/oracle_live_recovery-03.png)

   Advanced options include reconnection time, resumption buffer size, speed limit, precondition, and pre/post action. You can enable the **Disable synchronous I/O** option to speed up the recovery speed.

6. Confirm the job information and click **Submit**.

> Note: If the **Add job approval** option is enabled, the job should be approved by the admin before the execution.

## Manage Jobs

On the **Job** page, you can view the backup and restore job information of all agents, start, modify, and delete the jobs.

![](../images/01-agent/oracle/oracle-45.png)

- Start: Click ![](../images/01-agent/oracle/oracle-46.png) to start the job immediately.
- Modify: Click ![](../images/01-agent/oracle/oracle-47.png) to modify the basic job information, backup/restore schedule, and backup/restore options.
- Delete: Click ![](../images/01-agent/oracle/oracle-48.png) to access the confirmation window. Click **OK** to delete the job.

## Backup Protection Strategy

### Backup Schedule Operation

ADPS provides six types of backup schedules. The schedule type selected is only valid for the currently created job.

![](../images/01-agent/oracle/oracle-53.png)

- Immediate: The job immediately starts to run after it is submitted.
- One Time: After the job is created, it will be in an idle state and start to run when the specified Start time is reached.
- Hourly: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of hours/minutes within the time range according to the setting. If the unit is Hour, then you can set the value from 1 to 24. If you select the Minute as the unit, then you can set the value from 1 to 60.
- Daily: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of days according to the setting. The value is an integer between 1 and 5.
- Weekly: After the job is created, the first run will be initiated at the specified Start Time. The next run will be executed after a specified number of weeks according to the setting. You can specify which day of the week to run the job.
- Monthly: The job runs on the specified days of some months at the specified time. For example, you can set the job to run on January 1 and June 1 at 20:00. Or you can set it to run on the first Monday of every month at 20:00.

**Example: Run the job at 18:00 every two weeks on Friday**

![](../images/01-agent/oracle/oracle-49.png)

**The execution time will be:**

- If the current time is 17:00 on Friday, the execution time will be 18:00 on Friday (the current day).
- If the current time is 17:00 on Thursday, the execution time will be 18:00 on Friday (the next day).
- If the current time is 17:00 on Saturday, the execution time will be 18:00 on next Friday.
- After the first run is completed, the job will start automatically at 18:00 on Friday every two weeks.

### Backup Strategy Advice

There are six Oracle backup types: full backup, incremental backup, cumulative incremental backup, logical backup, log backup, and synthetic backup. It is recommended to formulate the following backup strategy according to different situations such as network bandwidth, business data volume, security requirements, and the amount of data loss that you can tolerate.

1. When the application traffic is relatively small, run a **Full Backup** once a week to ensure that you have at least one recoverable point in time every week.
2. After that, you can run an **Incremental Backup** or **Cumulative Incremental Backup** every day to reduce the backup time and ensure that you have at least one recoverable point in time every day.
3. If the database supports log backup, you can run a **Log Backup** every few hours to ensure that the RPO can reach the second level. For example, you can run a log backup every 2 hours.
4. You can use other backups according to application scenarios.

> Avoid using the following strategies:
>
> - Perform only full backups.
> - Perform a full backup followed by cumulative incremental or log backups.

## Channel Number Configuration

1-255 channels are supported.

When Oracle is backed up to a tape library pool, the channel number will be given a default value based on the number of drives in the tape library, the number of tapes, and whether the Oracle database is a RAC cluster. The default value is the maximum number that can be filled.

Restoring Oracle from a tape library pool only supports one channel and the number cannot be modified.

## Oracle Cluster

ADPS supports Oracle RAC, Oracle hot standby, Oracle Data Guard, Oracle Active Data Guard, Oracle XE, and Oracle One Node. Before the backup and restore, you should cluster the Oracle nodes.

> Note: Each node should be deployed according to *Install and Configure Agent* as well as *Activate License and Authorize User*.

### Cluster Binding

1. Go to **Resource** -> **Cluster**. Click the **Cluster Binding** icon.

   ![](../images/01-agent/oracle/oracle-51.png)

2. Customize the cluster name. Select the active node as the **Primary Node**, the corresponding Oracle cluster type as the **Type**, and the inactive nodes as the **Nodes**. Click **Submit**.

   ![](../images/01-agent/oracle/oracle-52.png)

### Backup

#### Oracle XE

To create a backup job, go to the **Hosts and Resources** page and select the Oracle XE instance as the backup source. The remaining steps are similar to the single instance backup. However, Oracle XE does not support logical backups including Data Pump export and traditional export.

#### Oracle RAC

To create a backup job, go to the **Hosts and Resources** page where you can find all Oracle RAC nodes. Select the Oracle under any node as the backup source. The remaining steps are similar to single instance backup.

The Oracle RAC backup job will be performed on the active node. If the active node goes down, the remaining nodes will take over the backup job according to the node settings in cluster binding. After the downed node is normal, it will take over the backup job again.

When running Oracle RAC synthetic backup, please note that:

- The Target will be mounted to the active node during the backup and the backup job will be performed on the active node. Jobs cannot be performed on the inactive node.
- During the synthetic backup, please do not run tablespace restores and single table restores. Otherwise, the subsequent instant recovery will fail.

#### Oracle One Node

To create a backup job, go to the **Hosts and Resources** page. Only the active node of Oracle One Node is displayed. Select the Oracle under the node as the backup source. The remaining steps are similar to single instance backup. Note that:

- The Oracle One Node environment must be clustered. Please contact the admin to complete the *Cluster Binding* operation if necessary.
- After the Oracle One Node switchover, the new active node will take over the backup job.

#### Oracle Hot Standby

To create a backup job, go to the **Hosts and Resources** page. Only the active node of Oracle Hot Standby is displayed. Select the Oracle under the node as the backup source. The remaining steps are similar to single instance backup. Note that:

- The Oracle Hot Standby environment must be clustered. Please contact the admin to complete the *Cluster Binding* operation if necessary.
- After the Oracle Hot Standby switchover, the new active node will take over the backup job.

#### Oracle Data Guard

To create a backup job, go to the **Hosts and Resources** page. Only the host of Oracle Data Guard is displayed. Select the Oracle primary database under the host as the backup source. The remaining steps are similar to single instance backup. Note that:

- The backup of Oracle Data Guard primary database is supported. After the switchover between the primary and standby databases, the backup job will not be taken over by the new primary database automatically. You need to recreate a backup job.

#### Oracle Active Data Guard

To create a backup job, go to the **Hosts and Resources** page. Both the primary and standby databases can be backed up and restored. The remaining steps are similar to single instance backup. Please note that:

- Continuous log backup of primary and standby databases is supported.

### Restore

#### Oracle XE

The **Restore Target** step displays the Oracle XE host and resource. Select the Oracle XE instance as the restore target. The remaining steps are similar to the single instance restore. However, Oracle XE does not support Logical Import including tablespace and table timepoint restore.

#### Oracle RAC

The **Restore Target** step displays all the nodes of Oracle RAC. Select the Oracle under any Oracle RAC node as the restore target. The remaining steps are similar to the single instance restore.

After the creation, the restore will be performed on the node selected as the restore target. Oracle instances of other nodes will be closed. After the single node restore is complete, Oracle instances of the other nodes will be started.

During the instant recovery, the disk will be mounted to the active node and then ADPS will recover the disk's data according to the single instance instant recovery. After the recovery, only the active node is available while others are not.

#### Oracle One Node

The **Restore Target** step displays the active node of Oracle One Node. Select the Oracle under the Oracle One Node active node as the restore target. The remaining steps are similar to the single instance restore.

#### Oracle Hot Standby

The **Restore Target** step displays the active node of Oracle Hot Standby. Select the Oracle under the Oracle Hot Standby active node as the restore target. The remaining steps are similar to the single instance restore. Note that:

- The Oracle Hot Standby environment must be clustered. Please contact the admin to complete the *Cluster Binding* operation if necessary.
- After the Oracle Hot Standby switchover, only the Oracle active node will be displayed on the **Hosts and Resources** page.

#### Oracle Data Guard

The **Restore Target** step displays the primary database of Oracle Data Guard. Select the Oracle under the primary database of Oracle Data Guard as the restore target. The remaining steps are similar to the single instance restore. Note that:

- The restore of Oracle Data Guard primary database is supported. After the switchover between the primary and standby databases, you should install the backup agent on the new primary database, connect it to the backup server, and authorize the license so that the restore can be performed on it.

#### Oracle Active Data Guard

The **Restore Target** step displays the standby database of Oracle Active Data Guard. Select the Oracle under the standby database of Oracle Active Data Guard as the restore target. The remaining steps are similar to the single instance restore. Note that:

- Before you restore data to the standby database, close its log application.
- If you enable the **Reset log** option, the standby database will become the primary after the restore.
- When the restore to the standby database is completed, check the Oracle database alert logs after opening the database. If alerts like "ORA-00338: log 5 of thread 1 is more recent than control file" are reported, you can execute "alter database clear unarchived logfile group 5;".

## Limitations

```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Limitations
---
class: longtable
---
| Function           | Limitations                                           |
| ------------------------------- | ------------------------------------------------------------ |
| OS authentication   | Windows platforms are not supported. |
| Access key authentication | Table restore is not supported.            |
| Backup                    | Compression: If you select to back up to a de-duplication pool, the Compression option will not be provided. {{ br }}RMAN command: It will be inserted between the allocate channel command and backup command. For example, you can use "set maxcorrupt for datafile xxx to N;" to skip bad blocks of some files. {{ br }}Reserve archive logs: When Oracle backup and data synchronization exist at the same time, you should select the Do not delete archive logs option when creating backups. Otherwise, the backup jobs will delete unsynchronized archive logs, leading to the failure of data synchronization.{{ br }}Hide Data Pump export: When the Oracle version is older than 10, Data Pump export will be hidden. {{ br }}Data Pump export is not supported when one of the following conditions occurs: the database is logged in with OS; the instance status is STARTED or MOUNTED; the database OPEN_MODE is READ ONLY; or the database is a standby. {{ br }}Traditional export is not supported when one of the following conditions occurs: the database is logged in with OS; or the instance status is STARTED or MOUNTED.{{ br }}Log backup is not supported when the database is in NOARCHIVELOG mode.{{ br }}The continuous log backup of Oracle RAC One Node is not supported when the database is in NOARCHIVELOG mode.{{ br }}Synthetic backup is not supported for HP-UX PA-RISC, HPUX-IA64, Solaris SPARC, and Solaris-x86.{{ br }}When you select Traditional Export and the Oracle database major version is newer than or equal to 11, the job may fail. When the database OPEN-MODE is READ WRTIE, Data Pump export is recommended. <br/>LogMiner of 18c is not supported.<br/>The full backup and log backups should be in the same pool. Otherwise, the restore will fail. |
| Synthetic backup     | The agent supports Linux (Red Hat 4.9 and later), AIX and Windows 2008 and later platforms. The database version supports Oracle 10g and later versions. {{ br }}Disable synchronous I/O option only supports Linux platforms.{{ br }}If iSCSI is used, iscsi-initiator-utils should be installed on the agent. {{ br }}The database for AIX Oracle synthetic backup cannot be larger than 128T. {{ br }}If the Oracle does not enable AsyncIO, ADPS will not automatically switch the synthesis method to Frontend Pipeline Synthesis. {{ br }}Databases under Red Hat 4.0 for synthetic backups cannot be larger than 16T.{{ br }}Synthetic pools under Red Hat 4.0 should use the ext3 filesystem format.{{ br }}You should create a synthetic backup job first and then create a log backup. |
| Live Recovery                   | You should create an instant recovery job first before using Live Recovery. {{ br }}Live Recovery is not supported for the instant recovery of synthetic backups that use the synthesis method of ASM disk group. |
| Analyze redo log files to find the exact SCN to restore to | Only the logs generated after the first backup can be used for LogMiner. {{ br }}When you select the logs for analysis, the logs should reside in the same restore branch as the database. It means that if you select the logs in other restore branches for analysis, you should perform a timepoint restore first and then perform the LogMiner. {{ br }}Only the analysis of the source database is supported.{{ br }}To set or modify the data dictionary file directory, you need to restart the database. {{ br }}The analysis progress cannot be displayed or stopped. {{ br }}DDL SQL statements (CREATE, ALTER, DROP, etc.) cannot be undone. |
|Oracle RAC|The multi-node concurrent backup of a database cannot be achieved with OS authentication. {{ br }}The continuous log backup of Oracle RAC One Node is not supported.|
|Oracle XE|Database logical backup and restore are not supported. {{ br }}Tablespace and table timepoint restore are not supported.|
```

## Glossary

```{tabularcolumns} |\Y{0.28}|\Y{0.72}|
```
```{table} Glossary
| Term                      | Description                                              |
| ----------------------------- | ------------------------------------------------------------ |
| Fast compression     | During the backup, the data can be compressed by fast compression algorithms. |
|Oracle RAC|RAC is the abbreviation of Real Application Clusters. It is a new technology adopted in Oracle databases with new versions and a kind of high availability. RAC is also the core technology that Oracle databases support grid computing environment.|
|Oracle XE|Oracle XE is a small database that can be used freely.|
|Oracle Data Guard |Oracle Data Guard is the most efficient and comprehensive solution of data availability, data protection, and disaster recovery for enterprise databases.|
```