# User Guide for Microsoft Exchange Online

## Overview

This guide introduces how to install and configure the ADPS agent and how to properly use ADPS to back up and restore Exchange Online.

The backup and restore features supported by ADPS include:

- Backup sources

	Mailbox (single or multiple)

- Backup types

	EWS mailbox backup

- Backup targets

	Standard storage pool, deduplication storage pool, local storage pool, tape library pool, object storage service pool, and LAN-Free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, reconnection, and speed limit

- Restore types

	EWS mailbox recovery

- Restore targets

	Original host

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs and conduct copy data management (CDM).
```

## Install and configure the agent

To back up and restore Exchange Online, first install the ADPS agent on the host where Exchange Online resides.

### Install the agent

ADPS agent can be installed on Windows. You can select the installation method according to your environment.

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
7. At the **Select components** step, select **Microsoft Exchange Online** from the component list. Click **Next**.
8. At the **Configure Aurreum Data Protection Suite agent** step, enter the following:

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

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page. Before you back up and restore Exchange Online, register the host, activate the backup license, and authorize users.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host where Exchange Online resides. Click the **Register** icon. Then the **Activate** window appears.

3. In the **Activate** window, select the Exchange Online Proxy backup license, and click **Submit**. After the activation, the **Authorize** window appears.

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
## Add Office 365 host and authorize users

Before backing up Exchange Online, you need to first add and activate the Office 365 host and authorize users.

### Create Exchange Online backup account

To create an Exchange Online account for backup, do the following: 

1. Register the backup application in Azure AD.

	(1) Use your Microsoft 365 administrative user to log in to the Azure AD.

	(2) Click **App registrations** > **New registration**. The **Register an application** page appears.

	(3) Enter a name for the backup application in the **Name** field. Use the default options for the remaining fields. Click **Register**. 

2. Add permissions for the application.

	(1) On the application **Overview** page, click **API permissions** > **Add a permission**.

	(2) On the **Request API permissions** page, click the **APIs my organization uses** tab, search **Office 365 Exchange Online** in the search box, and then select it from the results.

	(3) On the **Request API permissions** page, click **Application permissions**, search **full_access_as_app** in the **Select permissions** search box, and then select it from the results.

	(4) Return to the step (1), then click **Microsoft APIs** > **Microsoft Graph** > **Application permissions**. Add the following permissions for the application.
	- Directory: Directory.Read.All
	- Group: Group.Read.All
	- MailboxSettings: MailboxSettings.ReadWrite
	- User: User.Read.All

	(5) Back on the application **API permissions** page to verify the permission list for the backup application. Click **Grant admin consent for** > **Yes** to grant permission for backup application.

3. Create a secret for backup application.

	(1) On the backup application page, click **Certificates & secrets** > **Client secrets** > **New client secret**.

	(2) In the **Add a client secret** pane, enter the secret description in the **Description** field. Select **24 months** from the **Expires** list. Click **Add**.
	
	(3) Return to **Certificates & secrets** page. Note that before refreshing the page, copy and save the password, otherwise the password will be masked and you will have to recreate it.
	
	(4) Return to backup application **Overview** page. Save **Application (client) ID** and **Directory (tenant) ID**. These two ID and client secrets will be involved when the backup application is used.

## Add Office 365 host

To add the Office 365 host, do the following:

1. Log in to the ADPS console using a user with administrator privileges.

2. From the menu, click **Resource**. The **Resource** page appears.

3. From the toolbar, click the **Add** icon and click **Office 365**. The **Add Office 365** window appears.

4. In the **Add Office 365** window, do the following:

	(1) From the **Type** list, select **Exchange Online**.

	(2) In the **Tenant email** field, enter the tenant email address for the Exchange Online.

	(3) In the **Tenant ID** field, enter the Directory (tenant) ID for the tenant mailbox. 

	(4) In the **Client ID** field, enter the Application (client) ID for the tenant mailbox.

	(5) In the **Client password** field, enter the client secret for the tenant mailbox.

	(6) From the **Region** list, select the region of Exchange Online.

	(7) In the **Alias** field, set a name for Exchange Online. 

	(8) From the **Backup host** list, select the Exchange Online Proxy host, which is used to list backup source and as the default host for backup and restore.

	(9) Click **Submit**.

5. When the **Resource** list shows the host, the adding of the Office 365 host is successful.

## Backup

### Backup types

ADPS provides EWS mailbox backup for Exchange Online.

- EWS mailbox backup

	Backs up one or more mailboxes.

	```{note}
	Authentication using Basic Auth is not supported. The supported authentication method is OAuth2.
	```
	
### Backup policies

ADPS provides six backup schedule types: immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

1. Perform an **EWS mailbox backup** once a week when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.

### Before you begin

Before you back up and restore Exchange Online, check the following:

1. Check the Exchange Online instance status.

2. Check storage pools.

	(1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

	(2) Check whether the display area has any storage pools. If there is no storage pool, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.


## Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host where Exchange Online resides and select the resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

	(1) From the **Backup type** list, select a backup type.

	(2) In the **Backup source** section, select the mailboxes that you want to back up.

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
![](../images/Backup_Restore/DBackup3/Office365/office365_backup01.png)
```

7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options for Exchange Online:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup common options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Compression|Fast is enabled by default.{{ br }}- None: No compression during the backup job.{{ br }}- Tunable: Customizes the compression level. The Advanced Compression license is required. {{ br }}- Fast: Uses the fast compression algorithms to compress data during the backup job.||
```

- Advanced options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Backup advanced options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.||
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.||
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.||
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.||
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.||
```

## Restore

### Restore types

ADPS provides EWS mailbox recovery for Exchange Online.

- EWS mailbox recovery

	When a disaster occurs in an Exchange Online mailbox, you can create an EWS mailbox recovery job to restore the mailboxs or mails to the latest state of the newest backup set.

### Create an EWS mailbox recovery job

To create an EWS mailbox recovery job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.

2. At the **Hosts and resources** step, select the host where Exchange Online resides and select the resource. The wizard goes to the next step automatically.

```{only} scutech
![](../images/Backup_Restore/DBackup3/Office365/office365_agent01.png)
```
3. At the **Backup sets** step, do the following:

	(1) From the **Restore type** list, select **EWS mailbox recovery**.

	(2) From the **Mailbox** list, select the mailboxes that you want to restore.

	(3) From the **Restore point in time** list, select a point in time for the restore job.

	(4) In the **Backup sets** section, the mailbox directory appears. Select the folders or records that need to be restored.

	(5) Click **Next**.

4. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

5. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

6. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

7. After submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

### Restore options

ADPS provides the following restore options for Exchange Online:

- Common options

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore common options
---
class: longtable
---
|Feature|Description|Limitations|
| --- | --- | --- |
|Backup host|Enter the backup host for the restore job or leave it blank to restore to the host where the backup set selected resides.||
|Target mailbox|Select a target mailbox for restore. The default target is the original mailbox.||
|Restore directory|Select a directory for restore. The default target is the original directory.||
|Channels|It can improve restore efficiency. The default value is 1. The value cannot exceed the maximum channel number of backup sets.||
```

- Advanced options:

```{tabularcolumns} |\Y{0.2}|\Y{0.5}|\Y{0.30}|
```
```{table} Restore advanced options
---
class: longtable
---
|Feature |Description|Limitations|
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