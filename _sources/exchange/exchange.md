# User Guide for Microsoft Exchange Server

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore Exchange Server.

The backup and restore features supported by ADPS include:

- Backup sources

	Single or multiple databases and mailboxes

- Backup types

	Snapshot (VSS) backup (full backup and incremental backup), streaming backup (full backup and log backup), and mailbox backup (EWS mailbox backup and MAPI mailbox backup)

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage service pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, multiple channels, reconnection, and speed limit

- Restore types

	Point-in-time restore, EWS mailbox recovery

- Restore targets

	Original host, different host (same version required)

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore Exchange Server, first install the ADPS agent on the host where Exchange Server resides.

### Verify the compatibility

ADPS supports the backup and restore of Exchange Server in standalone mode and Exchange Server Database Availability Group (DAG). Before you install the agent, ensure that the environment of the host where Exchange Server resides is on the Aurreum Data Protection Suite's compatibility lists.

ADPS supports the backup and restore of Exchange Server, including:

- Exchange Server 2000/2003/2007/2010/2013/2016/2019

### Install the agent

ADPS agent can be installed on Windows.

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
7. At the **Select components** step, select **Microsoft Exchange Server** from the component list. Click **Next**.
8. At the **Configure Aurreum Data Protection Suite agent** step, enter the following:

	```{only} scutech
	![](../images/Common/exchange/exchange_agent01.png)
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

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Exchange Server, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. On the **Resource** page, select the host where Exchange Server resides. Click the **Register** icon. Then the **Activate** window appears.

3. In the **Activate** window, select the Exchange Server backup license, and click **Submit**. After the activation, the **Authorize** window appears.

	```{note}
	If you are prompted with "No enough licenses", contact the administrator to add licenses.
	```

4. In the **Authorize** window, select user groups to authorize access to the resource. Click **Submit**.

```{only} scutech
![](../images/Common/exchange/exchange_license01.png)
```

```{note}
With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup 

### Backup types

ADPS provides four backup types for Exchange Server: full backup, incremental backup, log backup, and mailbox backup (EWS mailbox backup, MAPI mailbox backup).

- Full backup

	Backs up all data in one or multiple databases.

- Incremental backup

	Backs up only the data that has changed since the last backup with a full backup or incremental backup as the baseline.

- Log backup

	Backs up transaction logs with a full backup as the baseline. It is possible to specify a point in time when transaction logs are used for restore. Compared to full backups, backing up transaction logs is more efficient in terms of time and space. In addition, the transaction logs are truncated to prevent log files from getting huge.

- Mailbox backup (EWS mailbox backup and MAPI mailbox backup).

	EWS mailbox backup only backs up the folders visible in Outlook Top of Information Store, while MAPI mailbox backup can back up the entire mailbox directory.

### Backup policies

ADPS provides six backup schedule types: immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

1. Snapshot (VSS) backup (full backup): Perform a **full backup** once a week when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.
2. Snapshot (VSS) backup (incremental backup): Perform an **incremental backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.
3. Streaming backup (full backup): Perform a **full backup** once a week if your database supports streaming backup when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.
4. Streaming backup (log backup): Perform a **log backup** hourly if your database supports log backup to ensure that you have a second-level point in time available everyday.
5. Mailbox backup: Perform a **mailbox backup** every day when the application traffic is relatively small (Example: at 2 a.m.) to ensure that you have a recoverable point in time every day, which can save storage space and backup time.

### Exchange versions for backup types

Please refer to the following table for the available backup types corresponding to different Exchange versions:

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Exchange versions for backup types
---
class: longtable
---
|Backup type |Exchange versions|
| --- | --- |
|VSS backup|Available for Exchange Server 2003 and above.{{ br }} Snapshot (VSS) incremental backup is not available for Exchange Server 2003.|
|Streaming backup|Only available for Exchange Server 2000, Exchange Server 2003 and Exchange Server 2007. {{ br }}Exchange Server 2007 and newer versions no longer support the Streaming API.|
|MAPI mailbox backup|Only available for Exchange Server 2007, Exchange Server 2010 and Exchange Server 2013.{{ br }}Exchange Server 2007 and newer versions no longer support the MAPI API.|
|EWS mailbox backup|Available for Exchange Server 2010 and above.|
```
### Exchange Server backup and restore account

To configure a backup and restore account of Exchange Server, do the following:

1. Create a new mailbox as the backup and restore account. After creation, the mailbox will automatically become a new account in the domain. 

  - For Exchange Server 2010:

	(1) Log in to the host where the Exchange Server resides, open the Exchange Server management console, expand the **Recipient Configuration**, right-click **Mailbox**, and select **New Mailbox…**. 

	(2) Create a new Exchange mailbox according to the prompts and set a username such as xxxxBackupUser.

  - For Exchange Server 2013 and above versions:

	(1) Log in to the Exchange Web Admin Center. On the **Recipients** page, click **+** and select **User mailbox**

	(2) Create a new Exchange mailbox according to the prompts and set a username such as xxxxBackupUser.

2. To log in to Exchange Server on the ADPS console, the account must be a member of Domain Admins. On the domain controller, open **Active Directory Users and Computers**, double-click the new account, select **Member of**, and click **Add** to add this user to Domain Admins. Click **OK**.

3. To add Exchange Server credentials in the host properties, create a new role group and add the new account to the group. Open Exchange Management Shell and do the following:

	(1) Create a role group: Create a role group named xxxxBackupRoles.

	```{code-block} shell
	New-RoleGroup -Name xxxBackupRoles -Roles @（"Database Copies"，"Databases"，"Exchange Servers"，"Monitoring"，"Mail Recipient Creation"，"Mail Recipients"，"Recipient Policies"，"View-Only Configuration"）
	```

	(2) Add a role group member: Add xxxxBackupUser into the xxxxBackupUser role group.
	
	```{code-block} shell
	Add-RoleGroupMember -Identity xxxBackupRoles -Member xxxBackupUser
	```

4. To back up EWS mailboxes, do the following on the Exchange Management Shell:

	(1) Create management roles: Based on the parent role ApplicationImpersonation, create a management role named xxxEWSImpersonationRole.

	```{code-block} shell
	New-ManagementRole -Name xxxEWSImpersonationRole -Parent ApplicationImpersonation
	```

	(2) Assign management roles: Assign xxxEWSImpersonationRole to xxxBackupUser.

	```{code-block} shell
	New-ManagementRoleAssignment -Role xxxEWSImpersonationRole -User xxxBackupUser -Name "xxxBackupUser-EWSImpersonation"
	```

	(3) Create a new throttling policy for Exchange 2010: Create an unlimited EWS throttling policy.

	```{code-block} shell
	New-ThrottlingPolicy -Name "xxxEWSThrottlingPolicy" -EWSPercentTimeInCAS $null -EWSPercentTimeInAD $null -EWSMaxConcurrency $null -EWSPercentTimeInMailboxRPC $null -PowerShellMaxConcurrency $null
	```

	(4) Create a new throttling policy for Exchange 2013 and later: Create an unlimited EWS throttling policy

	```{code-block} shell
	New-ThrottlingPolicy -Name "xxxEWSThrottlingPolicy" -EwsCutoffBalance "Unlimited" -EwsMaxBurst "Unlimited" -EwsMaxConcurrency "Unlimited" -ExchangeMaxCmdlets "Unlimited" -MessageRateLimit "Unlimited" -PowerShellCutoffBalance "Unlimited" -PowerShellMaxBurst "Unlimited" -PowerShellMaxCmdlets "Unlimited" -PowerShellMaxConcurrency "Unlimited" -PowerShellMaxOperations "Unlimited" -RecipientRateLimit "Unlimited" -ThrottlingPolicyScope "Regular"
	```

	(5) Set the mailbox policy: Assign xxxxEWSThrottlingPolicy to xxxxBackupUser

	```{code-block} shell
	Set-Mailbox -Identity xxxBackupUser -ThrottlingPolicy "xxxEWSThrottlingPolicy"
	```
   
5. Enable the Basic Identity Authentication for EWS mailbox backup: IIS -> Default Web Site -> EWS -> Identity Authentication -> Enable Basic Identity Authentication.

6. Open the Exchange Management Console, expand **Recipient Configuration**, click **Mailboxes**, and select the mailbox that you want to back up. Right-click the mailbox and **select Manage Full Access Permission**. Add xxxxBackupUser that you create in the first step. Click **OK**. This operation is required for MAPI mailbox backup but not for EWS mailbox backup.

### Before you begin

Before you back up and restore Exchange Server, check the following:

1. Check the Exchange Server instance status

	(1) Run Windows PowerShell as administrator on Windows.

	(2) Enter the following command in Windows PowerShell windows.

	```{code-block} shell
	Get-Service *Exchange*
	```
	This command will display all the relevant services of Exchange Server along with their status. If all the services are running, you should see the status of each service as "Running".
	
2. Check the circular logging is not enabled for Exchange Server database on Windows.

3. Check storage pools

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If there is no storage pool, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.


### Log in to the resource

Before you create a backup or restore job, log in to the Exchange Server resource on the console. 

To log in to the resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

2. From the host list, find the host where Exchange Server resides. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.

3. Click **Login** beside the resource. The **Login** window appears.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Exchange/exchange_login01.png)
	```
	
4. In the **Login** window, enter the **User** and **Password** of the backup account.

5. If your information is correct, you will be prompted that you have logged in to the resource successfully.


## Create a database backup job

To create a database backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.

2. At the **Hosts and resources** step, select the host where Exchange Server resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the databases that you want to back up.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Exchange/exchange_backup01.png)
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
![](../images/Backup_Restore/DBackup3/Exchange/exchange_backup02.png)
```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

## Create a mailbox backup job

To create a mailbox backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.

2. At the **Hosts and resources** step, select the host where Exchange Server resides and select the resource. The wizard goes to the next step automatically.

3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the mailboxes that you want to back up.

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Exchange/exchange_backup03.png)
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
   ![](../images/Backup_Restore/DBackup3/Exchange/exchange_backup02.png)
	```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Create a DAG backup job

The steps to create a DAG database backup job are similar to [Create a database backup job](#create-a-database-backup-job).

```{note}
Exchange DAG database backup is performed on the node where the Exchange MBX role is installed. The node should be configured with an account for the backup and restore. The account configuration is the same as [Exchange Server backup and restore account](#exchange-server-backup-and-restore-account).
```

### Create a DAG mailbox backup job

The steps to create a DAG mailbox backup job are similar to [Create a mailbox backup job](#create-a-mailbox-backup-job).

```{note}
Exchange DAG mailbox backup is performed on the node where the Exchange MBX role or the CAS role is installed. The node should be configured with an account for the backup and restore. The account configuration is the same as the [Exchange Server backup and restore account](#exchange-server-backup-and-restore-account).
```

### Backup options

ADPS provides the following backup options for Exchange Server:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Use the fast compression algorithms to compress data during the backup job.||
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 255.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.{{ br }}|Not available for MAPI backup jobs. |
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
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|Not available for synthetic backup jobs.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|Not available for synthetic backup jobs.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
|Perform consistency check|Verifies the integrity of the database and log files before the backup job starts. If the consistency check fails, the backup job will be automatically terminated. By default, this feature is disabled.|Only available for snapshot (VSS) backup jobs.|
|Shadow copy provider|Specifies a shadow copy provider in the host to perform the synthetic backup job.|Only available for snapshot (VSS) backup jobs.|
```

## Restore

### Restore types

For different needs, ADPS provides several restore types for Exchange Server, including:

- Point-in-time restore

	When a logical error or disaster occurs in a Exchange Server database, you can perform a point-in-time restore job to restore the database to a specified point-in-time state. Restoring to the original or a different host is supported.

- Mailbox recovery

	When a disaster occurs in a Exchange Server mailbox, you can restore specific mailboxes or emails from a valid mailbox backup set using the mailbox recovery feature. To recover folders in Outlook Top of Information Store, select EWS mailbox recovery; to recover the entire mailbox directory, select MAPI mailbox recovery.

### Before you begin

To restore Exchange Server to a different host, install the agent on that host, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Exchange Server resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Exchange/exchange_restore1.png)
	```

	(1) From the **Restore type** list, select **Point-in-time restore**.

	(2) From the **Database** list, select a database for the restore job.

	(3) In the **Restore point in time** section, specify a point in time for the restore job.

	(4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

```{note}
Restoring to a different host requires the same Exchange versions.
```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a mailbox recovery job

To create a mailbox recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Exchange Server resides and select the instance. The wizard goes to the next step automatically.

3. At the **Backup sets** step, do the following:

	```{only} scutech
	![](../images/Backup_Restore/DBackup3/Exchange/exchange_restore2.png)
	```

	(1) From the **Restore type** list, select **EWS mailbox recovery** or **MAPI mailbox recovery**.
	
	(2) From the **Mailbox** list, select the mailboxes that you want to restore.
	
	(3) From the **Restore point in time** list, select a point in time for the restore job.
	
	(4) In the **Backup sets** section, the mailbox directory appears. Select the folders or records that need to be restored.
	
	(5) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.

```{note}
Restoring to a different host requires the same Exchange versions.
```

5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Create a DAG point-in-time restore job

The steps to create a DAG point-in-time restore job are similar to [Create a point-in-time restore job](#create-a-point-in-time-restore-job).

```{note}
Exchange DAG point-in-time restore is performed on the node where the Exchange MBX role is installed. 
```
#### Before you begin

Before you create a DAG point-in-time restore job, do the following:

1. Ensure that the backed-up database does not have circular logging enabled.
2. Ensure that the database exists and has enabled the **This database can be overwritten by a restore** option in the properties. If the database already exists, remove it. If the database does not exist, create it first.
3. It is recommended to remove any replicated databases before a database restore is performed. Next, back up the contents in the folder where the database resides (It refers to the folder of xx.edb. You can right-click the properties of the database to find the folder) to other location, while also emptying the folder. 

### Create a DAG mailbox recovery job

The steps to create a DAG mailbox recovery job are similar to [Create a mailbox recovery job](#create-a-mailbox-recovery-job).

```{note}
Exchange DAG mailbox recovery is performed on the node where the Exchange CAS role is installed. 
```

### Restore options

ADPS provides the following restore options for Exchange Server:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore common options
---
class: longtable
---
|Option|Description|Limitations|
| --- | --- | --- |
|Restore database|Enter the name of the database that you want to restore. The original database is restored if you leave it blank. To restore to a different target database, create the target database before creating a point-in-time restore job.|Available for point-in-time restore jobs.|
|If the database cannot be overwritten|ADPS provides two options if the Exchange Server database cannot be overwritten: {{ br }}- Send an alert message and cancel the job {{ br }}- Allow overwriting the database|Available for point-in-time restore jobs.|
|Channels|It can improve restore efficiency. The default value is 1. The value cannot exceed the maximum channel number of backup sets.|Not available for MAPI backup jobs. |
|Automatically mount database|After the point-in-time restore job is completed, the database is mounted automatically. It is enabled by default.|Available for point-in-time restore jobs.|
|Target mailbox|Select a target mailbox for restore. The default target is the original mailbox.|Available for EWS mailbox recovery jobs.|
|Restore directory|Select a directory for restore. The default target is the original directory.|Available for EWS and MAPI mailbox recovery jobs.|
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