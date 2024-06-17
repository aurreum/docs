# Administrator’s Guide

## Overview

This guide introduces how to properly use ADPS as an administrative user.

## System component

ADPS system components contain Backup Server and Console, Storage Server, and Agent.

- Backup Server and Console

	A backup server is a machine on which the ADPS Server is installed. It provides the Backup Console and is responsible for accessing the clients and storage servers, monitoring and managing the business information such as the backup and restore of resources on each client.

- Storage Server

	A storage server is a machine on which the ADPS Storage is installed. It is responsible for receiving and storing backup data (backup sets, CDP data, etc.) from clients. Storage servers can be used to build distributed deduplication clusters, achieving load balancing of backup storage.

- Agent

	The agent can be installed on the production server or a separate server (backup host), responsible for receiving the job scheduling from the backup server and performing the backup/restore jobs.

## Feature

ADPS supports the following features:
```{tabularcolumns} |\Y{0.15}|\Y{0.85}|
```
```{table} Feature supported
---
class: longtable
---
|Feature|Description|
| --- | --- |
|Backup and restore|File backup and restore{{ br }}Database backup and restore{{ br }}Virtual machine backup and restore{{ br }}Cloud platform backup and restore{{ br }}Office software backup and restore{{ br }}Operating system backup and restore|
|Backup and disaster recovery|Database replication{{ br }}Continuous log protection{{ br }}Offsite disaster recovery|
|Data management|Effective data compression{{ br }}Accurate deduplication{{ br }}Customized backup speed limit within user-defined time intervals{{ br }}Synthetic backups{{ br }}Various copy data management strategies{{ br }}Self-serve BaaS (Backup as a Service){{ br }}Encryption for data transmission and data storage|
|Operations and maintenance management|Powerful label capabilities{{ br }}Various reports and log export{{ br }}Various user roles and access control permissions{{ br }}Data transfer in weak network environment{{ br }}Distributed multi-level management and unified monitoring{{ br }}Periodic recovery testing{{ br }}Multi-factor authentication|
```

## User roles

The ADPS user system consists of multiple roles, including pre-configured users and custom-created users. This system aims to achieve functions such as system management, backup services, unified monitoring, while ensuring isolation and checking and balancing among the different users. Through this design, ADPS ensures that the actions of each user can be supervised and accidents can be traced when they occur.

- Pre-configured users

	After the successful installation, the roles of the three pre-configured users are: System Administrator (admin), Audit Administrator (audit), and System Security (security).

	Their privileges are as shown in the following table:

   ```{tabularcolumns} |\Y{0.15}|\Y{0.85}|
   ```
   ```{table} Pre-configured users
   ---
   class: longtable
   ---
   |Role|Privilege|
   | --- | --- |
   |System Administrator (admin)|Responsible for the system configuration, user management, storage management, license management, resource management, operations and maintenance, etc. Default username and password is “admin/admin”.|
   |Audit Administrator (audit)|Record and manage the logging and retention of user's operational activities. Default username and password is “audit/audit”.|
   |System Security (security)|When the Enable security user option is checked, the security user will be responsible for operations such as creating, deleting, modifying, and locking user accounts. Default username and password is “security/security”.|
   ```

- Custom-created users

	The system administrator can create six user roles: administrator, system monitor, monitor, operator, pool replication user, and tenant.

	Their privileges are as shown in the following table:

  ```{tabularcolumns} |\Y{0.15}|\Y{0.85}|
  ```
  ```{table} Custom-created users
  ---
  class: longtable
  ---
  |Role|Privilege|
  | --- | --- |
  |Administrator|Responsible for system administration, excluding the management of critical data such as storage servers. Its privilege is second only to the system administrator.|
  |System monitor|Responsible for monitoring the system's operational state. It can monitor all system information, including the system and job state of sub-servers.|
  |Monitor|Responsible for monitoring the operation system state. All system information can be monitored, but the system and job state of sub-servers are not included.|
  |Operator|Responsible for managing backup and restore jobs.|
  |Pool replication user|Responsible for pool replication jobs.|
  |Tenant|Responsible for managing its own independent resources.|
  ```

## Storage planning

### Various storage types

ADPS supports popular types of storage media, including disks, tapes, optical discs, and cloud. ADPS allows the creation of different storage pools based on the specific storage media types. In a multi-tenant environment, tenants can add standard storage pools, deduplication storage pools, block device deduplication pools, and object storage service pools.

- Disk

	ADPS mounts disks to the operating system on the storage server, formats the file system as XFS/ZFS, and store the backup data on the file system.

	The supported storage pool types of disk storage are as follows:

 ```{tabularcolumns} |\Y{0.2}|\Y{0.25}|\Y{0.55}|
 ```
  ```{table} Disk-based storage pool
  ---
  class: longtable
  ---
  |Storage pool type|Description|Note|
  | --- | --- | --- |
  |Standard storage pool|Used to store the backup data of traditional backup types.|-|
  |Local storage pool|Used for scenarios where the storage media is the local space on the client.|Only users with the administrator and operator privileges can create this kind of pool.|
  |Deduplication storage pool|Used to store the backup data of traditional backup types. It utilizes source-side deduplication for data sharding and fingerprint calculation, only storing data blocks with different fingerprints.|We recommend using the XFS format for the disk's file system.|
  |File synthetic pool|Used to store the backup data of File, Hadoop and OBS synthetic backups. Storage Server mounts the backup sets to Agent via NFS for a instant recovery.|Instant recovery requires adps-nfsd module installed|
  |Real-time backup pool| Used to store the backup data of Oracle and MySQL continuous log backups.|-|
  |Block device deduplication pool|Used for managing and optimizing disk storage. It maximizes the utilization of disk space and improves backup storage efficiency and performance by reclaiming and reusing deleted blocks.|We recommend using the XFS format for the disk's file system.{{ br }}The backup server requires the installation of server module.|
  |Data synthetic pool|Used to store the backup data of Oracle, SQL Server, MySQL, and VMware synthetic backups. Storage Server provides block devices for Agent via iSCSI/FC Target, takes snapshots of the block devices, and mounts the snapshots directly to Agent for a fast restore.|The file system of the disk requires the ZFS format.{{ br }}The installation of adps-storaged-lanfree module is required for instant recovery.|
  |Block device synthetic pool|Used to combine multiple storage devices into a virtual device, offering greater capacity, improved performance, and data protection.|-|
  |LAN-free pool|Used to store the backup data transferring over LAN-free links. Storage Server mounts block devices to the Agent via iSCSI/FC Target to write or read data directly.|The file system of the disk requires the ZFS format.{{ br }}Installation of adps-controller module is required|
  ```

- Cloud

	Depending on the type of cloud storage, an object storage service pool can be created by inputting parameters such as AK and SK. Backup jobs can then be performed to back up data to the object storage.

  ```{tabularcolumns} |\Y{0.2}|\Y{0.25}|\Y{0.55}|
  ```
  ```{table} Cloud-based storage pool
  ---
  class: longtable
  ---
  |Storage pool type|Description|Note|
  | --- | --- | --- |
  |Object storage service pool|Used to store the backup data of traditional backup types. The data is backed up directly to the cloud (D2C).|-|
  ```

- Tape

	Tape libraries or virtual tape libraries can be used as storage media. After a tape library is connected to a tape controller, a scan can be performed on the ADPS console to identify connected tape libraries, and backup data can be stored in the created tape pools.

  ```{tabularcolumns} |\Y{0.2}|\Y{0.25}|\Y{0.55}|
  ```
  ```{table} Tape-based storage pool
  ---
  class: longtable
  ---
  |Storage pool type|Description|Note|
  | --- | --- | --- |
  |Tape library pool |Used to store backup data from traditional backup types. The data is backed up directly to tape library (D2T).|To manager tape libraries, the installation of adps-controller module is required.|
  ```

- Optical disc

	By utilizing the optical disc as a storage media, ADPS can easily manage backup data, perform quick restore and retrieval when needed. Optical disc storage also offers portability, allowing backup data to be conveniently archived and stored.

  ```{tabularcolumns} |\Y{0.2}|\Y{0.25}|\Y{0.55}|
  ```
  ```{table} Optical disc storage pool
  ---
  class: longtable
  ---
  |Storage pool type|Description|Note|
  | --- | --- | --- |
  |CD-ROM storage pool|Store and manage backup data using CD-ROMs as storage media|-|
  ```

### Flexible pool recycling strategies

The data in storage pools have a well-tried life cycle management. You can set the backup set retention policy according to the storage time, space and other conditions to achieve the recycling of storage space effectively.

Supported backup set retention policies are as the followings:
  ```{tabularcolumns} |\Y{0.2}|\Y{0.2}|\Y{0.25}|\Y{0.35}|
  ```
  ```{table} Backup set retention policy
  ---
  class: longtable
  ---
  |Retention policy|Parameter|Description|Note |
  | --- | --- | --- |--- |
  |Time policy|Backup sets retention days|Under the condition that each resource has a complete and available backup set, backup sets in the storage pool that exceed the retention period will be marked as expired.{{ br }}The default value is 30 days. 0 means do not use the Backup set retention days rule to delete backup sets.||
  |Space policy|Backup sets retention quota|If the total size of backup sets in the storage pool exceeds the configured retention quota, the earliest full backup sets, as well as the dependent incremental and log backup sets built upon them, will be marked as expired together.{{ br }}10 GiB is retained by default. 0 means do not use the Backup set retention quota rule to delete backup sets.||
  |Time and space policy|Backup sets retention days, backup sets retention quota| If you have set the retention days and retention quota, the backup sets will be marked as expired as long as either set condition is met.|The Backup sets retention policy displayed on the Storage pool page depends on the values entered: if both fields are 0, it shows as "Unlimited"; if one field is 0, it displays the entered value; and if values are entered in both fields, it shows as "XX days or XX GiB/PiB" entered.|
  ```
ADPS also offers multiple options to configure different storage pools with various settings.

**Common options**

1. Delay delete backup sets

	You can delay the deletion of expired backup sets by checking this option.

   - When the option is checked, the earliest expired backup sets will be deleted to free up space when the disk space usage exceeds 90% (The percentage can be modified in **Storage** > **Storage server** > **Modify**).

   - When the option is unchecked, backup sets in the storage pool will be deleted to free up space when they become expired.

2. Min full backups retention

	Set the minimum number of full backup sets retained for each resource in the storage pool. The default minimum is 1. Incremental and differential backup sets that depend on these full backups are retained at the same time. When the number of full backup sets exceeds the set value, the recycling will start according to the configured retention policies of the storage pool.

**Advanced option**

- Data storage encryption: Enabling encryption for backup set transmission and storage requires the selection of an encryption algorithm, cipher length, and encryption mode.

- Encryption algorithm: AES and SM4 are available.

- Cipher Length: The AES encryption algorithm supports cipher lengths of 128, 192, and 256.

- Encryption Mode: For encryption mode, AES encryption algorithm supports CTR and OFB; SM4 encryption algorithm supports OFB.

- Specify UUID: Specifies the UUID for the pool. It is used for rebuilding the storage pool in case of accidental deletion.


## Dashboard

The **Dashboard** provides users with a comprehensive overview of the backup system's operation state, allowing them to quickly access the overall system information.

The display modules on the Dashboard include:

- Operations and maintenance (O&M) statistics
- Overall storage capacity usage
- Licensed storage capacity usage
- Storage pool capacity usage
- Data export


### Operations and maintenance (O&M) statistics

This section shows the state of hosts, resources, storage pools, and counts pool replication, jobs, histories, and alert logs.

- **Hosts**: List the total number of registered hosts including the number of online and offline hosts.

- **Resources**: List the total number of registered host resources including the number of the online and offline resources.

- **Storage pools**: List the total number of storage pools including the number of the online and offline storage pools.

- **Storage pool replication**: List the total number of pool replication jobs including the number of the jobs with the “Success”, “Failure” and “Running” states. The statistics can be counted over a period of 24 hours, 7 days, 30 days, or 90 days.

- **Jobs**: List the total number of backup and restore jobs created including the number of jobs with the “One time”, “Cycle”, and “Running” states.

- **Histories**: List the total number of jobs executed including the number of jobs that ended up with the “Success”, “Failure” and “Cancelled” states. The statistics can be counted over a period of 24 hours, 7 days, 30 days, or 90 days.

- **Alerts**: List the total number of alerts generated by the client, storage pools, and data synchronization including the number of alerts with the “Error” and “Fatal” states. The statistics can be counted over a period of 24 hours, 7 days, 30 days, or 90 days.

  ```{note}
  Users may access the appropriate pages by clicking on the names on the display modules.
  ```

### Overall storage capacity usage

The overall disk storage capacity usage is presented as a pie chart.

- The number in the center shows the total capacity of the disk storage.
- The red area shows the amount of storage capacity used.
- The grey area shows the amount of storage capacity free.

### Licensed storage space usage

Licensed Storage Space refers to the activated storage space, and users can only use the activated storage space. Backup to the storage server is not supported if the activated storage capacity is entirely utilized. To expand the storage space, users are required to reapply for a new license.

### Storage pool capacity usage

The usage of each disk storage capacity is presented as a pie chart, and the storage pool usage under each disk is presented as a bar graph.

The progress bars in different colors represent the varying storage capacity utilization of the current storage pools.

- Blue: The pool usage is below 80% of the total pool capacity.

- Orange: The pool usage is above 80% but below 90%. An alert email will be sent to users.

- Red: The pool usage is greater than 90% of the total pool capacity. An alert email will be sent to users.

### Dashboard toolbar

- **Download**: Dashboard data can be exported to a CSV file. To export these data in CSV format, from the toolbar, click the **Download** icon. The exported data will include the visual data of the current page. Additionally, the **Dashboard** also provides the options to refresh and customize settings.

- **Refresh**: From the toolbar, click the **Refresh** icon to update the entire dashboard data. To avoid frequent refreshing, the icon will be locked for 3 seconds during the refresh process. If the **Automatic refresh** is enabled in the Settings, the icon will spin slowly, and a progress indicator below the icon will indicate the next refresh trigger.

- **Setting**: From the toolbar, click the **Setting** icon.
	- Automatic refresh: Specifies the auto-refresh frequency for 30 seconds, 1 minute, 3 minutes, or 5 minutes
	- Refresh after page ready: Refreshes the cache data
	- Show/Hide: Choose the desired module to display it on the Dashboard. When a module is hidden, the corresponding data will be excluded from the downloaded CSV file.
	- Enable the number increment animation: Creates a dynamic effect where the statistics of each module gradually increase as you revisit the page.

## User management

This chapter will introduce how the system administrator (admin) can create users with various roles and assign resource and storage pool permissions to users based on user groups. Contents include:

- Add user
- Assign user group

### Add user

System administrator can create users with various roles, including administrator, monitor, system monitor, operator, pool replication user, and tenant. The operator role is responsible for jobs such as backup, restore, and disaster recovery. Therefore, it is essential to create users with the operator role. Users with roles such as administrator, system monitor, monitor, pool replication user, and tenant can be created on demand.

From the menu, click **User** > **User**. The **User** page appears. On this page, you can view and manage the user information. Users can access the User page to perform operation such as:

- Add user roles
- Modify users
- Lock/unlock users
- Delete users
- Add to IP address blocklist
- Covert to tenant


  ```{note}
  Creating users with tenant and system monitor roles, as well as enabling the conversion to a tenant, requires a multi-tenant environment.
  ```

1. To add the Operator user, do the following:

	(1) From the menu, click **User** > **User**. The **User** page appears.

	(2) From the toolbar, click the **Add** icon. The **Add user** window appears.

	(3) In the **Roles** field, select **Operator**, and fill in the required fields: **Username**, **Password**, **Confirm New Password**. Other fields are optional.

	(4) Set the **User group**. Click **Submit**. The adding of the operator is complete.

	```{note}
	The steps for creating users with other roles are the same.
	```

2. To modify users:

	In display area, click **Modify** icon to modify nickname, password, email, telephone and other information.

3. To lock/unlock users:

	In display area, select the user with "Normal" status, and click **Lock** icon to disable the user. The user will be unable to log in to the ADPS console.

4. To delete users:

	In display area, click the **Delete** icon to delete the user account. If you want to batch delete users, click the **Modify** icon from the toolbar, select **Delete all record** to delete all user accounts except system administrator and tenants.

5. Add to IP address blocklist

	In display area, click the **Login IP address blocklist** icon. The Login IP address blocklist window appears. In this window, you can check the IP addresses used and add them to the blocklist. Unless the administrator removes the IP address from the blocklist, the user won't be able to log in to the account using browsers on the associated IP system.

	To view all blocked IP address, click **Login IP address blocklist** from the toolbar. To remove the blocked IP address, click the **Delete** icon in the display area.

	```{note}
	You can also add or remove IP addresses from the login IP address blocklist in the **Avatar** > **Personal settings**.
	```

6. Session management

	From the toolbar, click the **Login IP address blocklist** icon and select **Session management**. The Session management page appears. On this page, you can view the blocked IP addresses and their related information, such as the user and authentication method. You can also delete the blocked IP address.

7. Covert to tenant

	To convert an operator or monitor user into a tenant, select the user you want to convert and click the **Convert to tenant** icon. An verification window appears. Enter the verification code and click OK.

	The conversion to tenant requires the multi-tenant environment.

### Assign user group

From the menu, click **User** > **User group**. The **User group** page appears. This page allows you to view and manage users within user groups, as well as assign permissions to users for operating resources and storage pools. Supported operations are as follows:

- Add user group
- Modify user group
- Delete user group

1. To add the user group, do the following:

	(1) From the menu, click **User group** > **User group**. The **User group** page appears.

	(2) From the toolbar, click the **Add** icon. The **Add user group** window appears.

	(3) Set the **Name** for the user group. Select **Users**, **Resources**, and **Storage pools**.

	(4) Click **Submit** to add the user group. Only users within the same user group can access and operate the assigned resources and storage pools.

2. To modify user groups:

	In display area, click **Modify** icon to modify the user group name, and users, resources, and storage pools.

3. To delete user groups:

	In display area, click the **Delete** icon to delete the user group.

## Storage management

This chapter introduces how to manage various types of storage media from different aspects. Storage management include:

- Storage server
- Storage pool
- Network
- Pool replication job
- SCSI target
- Tape library
- RAID card
- Software RAID

### Storage server

Operations such as configuring networks and adding storage pools can only be performed after the storage server software has been installed and registered.

From the menu, click **Storage** > **Storage server**. The **Storage server** page appears. Users can access this page to perform operation such as:

- Register/unregister the storage server
- Add external storage
- Export storage server data
- Health check
- Set deduplication recycling
- Refresh space
- Configure network
- Modify storage server


1. Register/unregister the storage server

	(1) Select a storage server, click the **Register** icon.

	(2) After the registration, a pop-up window appears confirming the setup of management network used for the communication between the backup server and storage server. Click **Submit**. The **Add network** window appears.

	(3) Only one Management Network can be created. In the **Add network** window, check **Management network** as the purpose, set the name of the Network, Address, and Port, click **Submit**.

	(4) On the **Storage server** page, the storage server is successfully registered and the state is Online. Click the **Unregister** icon to unregister the storage server. When you unregister a storage server, the backup and restore jobs on this storage server will fail. If the unregistration is an accidental deletion, you can re-register the storage server with the backup server again, and the backup and restore jobs associated with the storage server can continue as usual.

2. Add external storage

	(1) On the **Storage server** page, from the toolbar, click the **Add external storage** icon. The **Add external storage** window appears.

	(2) Set the name, type, storage server, mount point, address, port, user, password for the external storage. Click **Submit**.

3. Export storage server data

	From the toolbar, click the **Download** icon. Select a format and extension to export the data.

4. Health check

	The icon will only be displayed if the storage server is using the ZFS file system and has created a deduplication storage pool. Only the system administrator have the permission to view and configure this.

	(1) In display area, click the **Health check** icon. The Health check page appears.
	- When the storage server is using the ZFS file system and has created a data synthetic pool, you can view the Zpool Status. The Zpool status will trigger an alert if there are any abnormalities in the status of the Zpool pool or hosts. The default interval for checking the ZFS status is 2 minutes.
	- When the storage server is using the XFS file system and has created a deduplication storage pool, you can view the deduplication storage pool state. Additionally, you have the option to perform the following operations on the deduplication pool.

      - Refresh: The state and progress of the deduplication pool can be refreshed by clicking the **Refresh** icon.

      - Reshard: Allows re-sharding of the fingerprint library of the deduplication pool. The number of shards must be a power of 2, with a minimum value of 4. If the input value is not a power of 2, the nearest power of 2 will be automatically selected as the number of shards.

      - Rebuild fingerprint library: Rebuilds the fingerprint library by scanning the data files of the deduplication pool. When there is physical or logical damage to the metadata or data files of the deduplication pool, the fingerprint library can be rebuilt to align the metadata and data files. Only deduplication pools in "Consistent" and "Inconsistent" states are allowed to rebuild the fingerprint library. Rebuilding the fingerprint library does not modify the data files of the deduplication pool.

5. Set deduplication recycling

	The **Deduplication recycling settings** icon only appears when a deduplication storage pool exists on the storage server. Only the system administrator have the permission to view and configure this.

	(1) In display area, click the **Deduplication recycling settings** icon. The **Deduplication recycling settings** window appears.

	(2) The green button **Run immediately** allows for performing the immediate recycling process. Scheduled recycling policy is also supported.
      - Schedule: You can schedule the recycling process to run daily or hourly. For daily scheduling, you need to specify the start time. For hourly scheduling, you need to specify the execution interval, which can be set in hours or minutes. The interval should not exceed 24 hours.
      - Threads: It is recommended that the number of threads not be greater than the number of the storage server CPU logical processors

	(3) Submit the configuration.

6. Refresh space

   In display area, click the **Refresh space** icon to refresh the storage server space.

7. Configure network

	In display area, click the **Network** icon. The **Network** page for the corresponding storage server appears.

8. Modify storage server

	In display area, click the **Modify** icon. The **Modify** window appears. In **Modify** window, modifying the storage server name, space usage alert threshold, and reserve space is supported.

      - Space usage alert threshold: The value range is 0~99 and 0 means no alert will be sent. When the space usage exceeds the set value, an alert will be sent.
      - Reserve space: The file system should reserve 5~10% space for storage performance. Only appears when the storage server uses the ZFS file system.

   ```{note}
   Be careful when modifying the configuration of the storage server, as it may result in the inability of the agent to connect to the created storage pools.
   ```

### Storage pool

After the storage server is registered, you can add storage pools and assign them to users to store data. Users can add storage pools according to their needs. See [Flexible pool recycling strategies](#flexible-pool-recycling-strategies) for details.

From the menu, click **Storage** > **Storage pool**. The **Storage** page appears. On this page, the following operations are provided:

- Add, delete, check and modify storage pool
- Set up pool replication
- Space usage chart


```{note}
- If the storage server supports snapshots, it allows for the creation of synthetic pools, but does not allow for the creation of deduplication storage pools. If the storage server does not support snapshots, it allows for the creation of deduplication storage pools, but does not allow for the creation of synthetic pools (except for file synthetic pools). Other types of storage pools are not affected by this restriction.
- To determine if a storage server supports snapshots, access the **Storage server** page. In display area, click the **Column** icon and select **Snapshot**. If the storage server supports snapshots, the **Snapshot** column shows "Supported"; if it does not support snapshots, the **Snapshot** column shows "N/A".
```

#### Add a storage pool

Follow the WEB guidance to create the desired type of storage pool that suits your needs. Here takes the standard storage pool and tape library pool as examples.

1. At the **Select** step, do the following:

   (1) From the menu, click **Storage** > **Storage pool**. The **Storage** page appears.

   (2) From the toolbar, click the **Add** icon. The Add storage pool page appears.

   (3) From the **Type** list, select **Standard storage pool**.

   (4) From the **Storage server** list, select the storage server where the storaged module is installed.

   (5) Select the **Multi-storage** check box as needed.

   (6) Click **Next**.


   - **Multi-storage**: The storage pool space can be extended by adding multiple storage. But some virtual machines and Windows OS do not support backup to multiple storage pools.

   - **Allocate storage space**: Once the Multi-storage is selected, the **Allocate storage space** will appear. This option is used to specify the amount of storage space allowed for the backup set. If the allocated space has been used up, no further backups can be made to the storage server.

 ```{note}
After the storage pool has been successfully created, the configuration for **Multi-storage** and **Allocate storage space** cannot be changed.
 ```

2. At the **Configure** step, see [Flexible pool recycling strategies](#flexible-pool-recycling-strategies) for option configuration details. Click **Next**.

3. At the **Submit** step, do the following:

	(1) Set the **Name** for the storage pool.

	(2) Multi-tenant environment requires to set the **Owner** to assign permission to tenants for use. Only the owner of the storage pool and the operators who are subordinate to the owner have permission to use the storage pool.

	(3) From **User group** list, select users to have operational permissions for the pool.

	(4) Click **Submit**.

###### Add storage

After creating a standard storage pool with multi-storage enabled, you can add multiple storage to merge the storage capacity and expand the storage pool space.

On **Storage** page, select the standard storage pool with multi-storage enabled, and click the **Add storage** icon in display area. The Add Storage window appears. In the window, do the following:

(1) The **Type** selects standard storage pool by default.

(2) In the **Address** field, set the domain name or IP address for the server where the storaged module is installed.

(3) Select the **SSL** check box as needed. When SSL is enabled, the storage uses SSL, and data is secured when it is backed up to this pool.

(4) In the **Port** field, set the port number for the machine. The default port is 50306. If SSL is enabled, the default port is 60306.

(5) In the **Allocate storage space** field, specify the allowed size of backup sets occupying this storage space. If the allocated space has been used up, no further backups can be made to the storage server. It is recommended that the allocated space be less than the physical free space.

(6) Click **Submit**.

##### Add tape library pool

Before creating a tape pool, scan the tape first.

###### Scan the tape

1. From the menu, click **Storage** > **Tape library**. The **Tape library** page appears.


2. The page displays a prompt saying "Tape controller not initialized! add". Click **add**. The Add tape controller window appears. In the window, do the following:

	(1) Set the **Name** for the tape controller.

	(2) In the **Address** and **Port** fields, enter the address and port of the server where the adps-controller module is installed and the tape library is connected.

	(3) Select the **SSL** check box as needed.

	(4) Enter the **Remark** as needed.

	(5) Click **Submit**.

3. After submitted, information about the tape libraries attached to the controller is listed.

4. In display area, click **Initialize**. Tape libraries will be initially scanned. The initial scan lasts for a period of time, depending on the tape loading speed of the tape library drives.

###### Add tape library pool

1. From the menu, click **Storage** > **Storage pool**. The **Storage** page appears.
2. From the toolbar, click the **Add** icon. The Add storage pool page appears.
3. At the **Select** step, do the following:

	(1) From the **Type** list, select **Tape library pool**.

	(2) From the **Storage server** list, select the storage server where the storaged module is installed and the tape library is connected.

	(3) Click **Next**.


4. At the **Configure** step, see [Flexible pool recycling strategies](#flexible-pool-recycling-strategies) for option configuration details. Click **Next**.

5. At the **Submit** step, do the following:

	(1) Set the **Name** for the storage pool.

	(2) Multi-tenant environment requires to set the **Owner**.

	(3) From **User group** list, select users to have operational permissions for the pool.

	(4) Click **Submit**.

###### Allocate tapes

Navigate to the **Storage** page after creating a tape library pool. In display area, click the **+** icon to expand the tape library pool information. Then, click the **Allocate tapes** icon. The **Allocate tapes** window appears, allowing you to reallocate tapes for the storage pool.


#### Modify storage pools, storage and labels

The configuration of storage pool includes two part: the pool configuration and the storage configuration. You can modify the relevant configuration by clicking the Modify icon on different areas.

- Modify a storage pool

	On the **Storage** page, select a storage pool and click the **Modify** icon in display area. The **Modify** window appears. In this window, you can modify configuration for the pool.

```{note}
Please note that when modifying the **Backup set retention quota** and **Backup set retention days**, the changes will take effect during the next backup.
```

- Modify the storage

	On **Storage** page, click the **+** icon on the left side of a specific storage pool in display area. The storage server information appears. Then, click on the **Modify** icon located on the same row as the storage server to modify the storage configuration.

```{note}
Please note that you only need to modify the **Address** if the storage server's IP address has changed. The storage pool's backup sets will remain accessible even the modification has been made. If the pool has been configured a pool replication, it is advised to modify the source pool's IP address first before modifying the target pool's IP address.
```


- Modify a storage pool label

	ADPS supports modifying and adding labels for existing storage pools that have been labeled or have not been unlabeled.

	To modify the storage pool labels, do the following:

	(1) From the toolbar, click the **Modify** icon.

	(2) Select one or more storage pools, and click the **Label as** icon from the toolbar. The Label As window appears.

	(3) In the Label As window, modify the pool label. You can also perform label search, adding and management operations in this window.

  ```{note}
  The system administrator does not have permission to modify the storage pool labels for tenants.
  ```

#### Delete a storage pool

On **Storage** page, click the **Delete** icon in display area. The selected storage pool will be deleted.

- When a pool replication is configured for storage pools, the pool deletion will fail.
- When the server of the storage pool is online, the pool will only be deleted after the resource is recycled.
- When the server of the storage pool is offline, there is a red delete icon providing a forced deletion. The forced deletion may cause part of the authorized space unable to be recycled.
- If an abnormal service occurs during the deletion, the deletion can be continued after the service restarts.

### Storage pool replication

ADPS supports setting pool-to-pool data replication for storage pools. With pool replication configured for storage pools, the backup set is automatically backed up to the target storage pool; If errors occur in the source pool and the related backup set files are lost, restore the backup sets from the target pool is available.

#### Set up pool replication

```{note}
1. Pool replication is not available for LAN-free pools and local storage pools.
2. One-to-many pool replication is not available for data synthetic pools, block device synthetic pools, and real-time backup pools.
```

1. From the menu, click **Storage** > **Storage pool**. The **Storage** page appears.
2. In display area, select a storage pool that needs to be used as the source storage pool, and click the **Set up pool replication** icon. The Set up pool replication window appears.

3. In the **Set up pool replication** window, set the **Target storage pool**, and set the advanced options:
	- **Reconnection time**: The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.

	- **Speed limit**: Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s. You can limit the speed in different time intervals.

	- **Filter**: By default, no filters are used; all data will be replicated. If filters are supplied, the data that satisfies any of the filter rules will be replicated.
	Filter provides two rules: include and exclude. The previously added filter rules can be changed or removed.
	- **Include**: Set the data to be replicated. Include all if not set.
	- **Exclude**: Exclude data from the Include conditions (Optional)

4. Click **Submit**.

#### Pool replication job

From the menu, click **Storage** > **Pool replication job**. The **Pool replication job** page appears. On this page, you can view history of storage pool replication jobs, and perform editing, downloading, searching, and deleting operations.


- Edit: From the toolbar, click the **Edit** icon. The page becomes editable. Select one or more pool replication jobs, and select the operation(Start, Pause, Delete) from the **Edit** list.

- Download: Click the **Download** icon to export the records of the Pool replication job page.

- Search: From the toolbar, select **Search by job** or **Search by minor resource**, enter the name that you want to search.


#### Pool replication list

From the menu, click **Storage** > **Storage pool**. The **Storage** page appears. From the toolbar, click the **Pool replication list** icon. The Pool Replication List page appears. On this page, you can view details of all storage pools configured with pool replication.

Configure: You can configure the following settings:

- Select **Restore from source storage pool** or **Restore from target storage pool**, both of which can achieve direct data restore from offsite pool.
- Set the **Reconnection time** and **Speed limit**.
- Set the **Filter**.

- **Delete**: In display area, click the **Delete** icon. Please be cautious when performing deletion, as it will render you unable to directly recover data from the target storage pool or retrieve backup sets to restore them to the source pool.

- **Download**: From the toolbar, click the **Download** icon to export the records of the page.

- **Pool replication topology diagram**：From the toolbar, click the **Pool replication topology diagram** icon. The "Pool Replication Topology Diagram" page appears. All pool replication relationships can be viewed.


**Repair the source pool**

When the source pool host fails, the Catalog data can be modified between the source pool and the target pool.

(1) Configure the IP address of the source storage pool on a new machine.

(2) Install the storaged module on the new machine.

(3) Re-register the source storage pool on the new machine.

(4) You can restore the connection and synchronization between the source and destination storage pools now.

(5) Click the **Restore from pool replication target** icon on the Pool Replication Job page. The option allows you to retrieve backup sets from the target storage pool to the source storage pool, and continue the backup and restore jobs.

```{note}
If the source pool is an object storage service pool, repairing the source pool is not supported.
```

### Network

From the menu, click **Storage** > **Network**. The Network page appears. On this page, you can view all network information on this page. To distinguish the different IP usage in the case of multi-IP storage server, you can create multiple networks to specify the channels between the storage server and the backup server, the backup and restore on agent, and pool replication. You can modify and delete the network configuration after creation.


#### Add data network

Data Network is used to transfer data between the agent and storage server during the backup and restore. To create a data network, do the following:

1. From the toolbar on the **Network** page, click the **Add** icon. The Add window appears. Enter the name for the network, and select the **Data network** check box. Click **Submit**.

2. In display area, click the **+** icon next to the network. It will expand the information about storage servers and hosts related to the network.
	- Click the **Add** icon next to **Storage servers** to add a storage server and set its configuration.
	- Click the **Bind hosts** icon next to **Hosts**, and select one or more hosts from the list. Click **Submit**.

3. Click **Submit**. The storage server and agent bind the data network successfully. During the backup and restore, the data network is set to the storage server address.

#### Add storage pool replication network

Storage pool replication network is the transfer network during the storage pool replication. To create a storage pool replication network, do the following:

1. From the toolbar on the **Network** page, click the **Add** icon. The Add window appears. Enter the name for the network, and select the **Storage pool replication network** check box. Click **Submit**.

2. In display area, click the **+** icon next to the network. It will expand the information about storage servers related to the network. Click the **Add** icon next to **Storage servers**. In the Add window, select the **Addresses** of the storage servers where the source and target storage pools are located.

3. Click **Submit**. The creation of the network for transferring data between storage pools on the storage server has been completed.

	- In pool replication, the network for pool replication is determined by the destination pool. For retrieving data, the network for pool replication is determined by the source pool.
	- It is recommended to use the same network label for both the source and destination pool nodes. When configuring pool replication, you should select that network label from the available options.

#### Add management network

Management network is used to control and manage the backup server and the storage server, as well as to achieve network isolation between the management and the business.


```{note}
Only one management network is allowed. If there is already a management network, click the **Add** icon next to **Storage servers** and add a new storage server in the Add window.
```
To create a management network, do the following:

1. From the toolbar on the **Network** page, click the **Add** icon. The Add window appears. Enter the name for the network, and select the **Management network** check box. Click **Submit**.

2. In display area, click the **+** icon next to the network. It will expand the information about storage servers related to the network.
	- Click the **Add** icon next to **Storage servers** to add a storage server, select the **Address** used to communicate with the backup sever, and set its configuration.
	- Click the **Bind hosts** icon next to **Hosts**, and select one or more hosts from the list. Click **Submit**.

3. Click **Submit**. The creation of the network for communicating with the backup server has been completed.

### SCSI target

The SCSI target function provides iSCSI target and FC target to the client on the storage server with the storaged-lanfree module installed.

Add a storage server with the storaged-lanfree module installed to the storage pool before using this function.

1. From the menu, click **Storage** > **SCSI Target**. The **SCSI Target** page appears.


2. From the **Storage server** list, select a storage server. The display area list "iSCSI" by default. If the storage server has FC HBA card installed, the page will list FC.

#### iSCSI target

##### Add

1. On **SCSI Target** page, click the **Add target** icon next to iSCSI. A pop-up window appears. Modify the name of the iSCSI target in the text field. Click the **Tick** icon to add the iSCSI target.

2. After adding a target, you need to add one or more LUNs for the target. Click the **Add LUN** icon next to the newly added target. A pop-up window appears. Drag the slider control in the pop-up window or enter a value in the text field to set the size of the LUN.

3. Click the **Tick** icon to add the LUN. The newly added LUN will not allocate the corresponding space on the storage server immediately, but will allocate the space according to the actual usage.

4. Click the **Access Control List (ACL)** icon. The ACL window of the LUN appears. In the window, you can add one or more initiators, and only the initiators in the list can access the LUN. When you run a backup to a LAN-free pool or data synthetic pool, you will be prompted to select a storage device and the initiator will be automatically added to the corresponding LUN after your selection.

##### Delete

Click the **Delete** icon next to the LUN on the SCSI Target page to delete the LUN. Deleting a LUN will erase all the data on the LUN. Make sure the LUN is not in use before deleting it.

#### FC target

To create FC target, install FC HBA card on the storage server first and set the HBA card in target mode. ADPS only supports QLogic HBA card at the moment.

##### Add

The SCSI Target page lists the FC by default based on the number of interfaces on the HBA card. Create one or more LUNs under the corresponding FC target.

1. From the menu, click **Storage** > **SCSI target**. The **SCSI target** page appears. Click the **Add** icon next to the target, and drag the slider control in the pop-up window or enter a value to set the size of the LUN.

2. Click the **√** icon to submit the adding. The newly added LUN will not allocate the corresponding space on the storage server immediately, but will allocate the space according to the actual usage.


### Tape library

The **Tape library** is used for querying and managing the tape library. Before using the tape library, you need to scan, initialize the tapes, and add the tape controller.

To scan and initialize the tapes, do the following:

1. From the menu, click **Storage** > **Tape library**. The Tape library page appears.


2. The page prompts "Tape controller not initialized! add". Click **Add**. The Add tape controller window appears. In the window, do the following:

	(1) Set the **Name** for the controller.

	(2) In the **Address** and **Port** field, enter the IP address and port of the server that has the adps-controller module installed and connected to the tape library.

	(3) Select **SSL** and fill in the **Remark** on demand.

	(4) Click **Submit**.

3. After the submission, the page lists details of the tape library attached to the controller.

4. In display area, click the **Uninitialized** icon to perform an initial scan of the tape library. The scan will last for a period of time, depending on the tape loading speed of the tape drive.

	After scanning and initializing the tapes, the following operations are supported:


- Controller: From the toolbar, click the **Controller** button to access the **Tape library controller** page.

- Rescan: In display area, click the **Rescan** icon to rescan the tape library and update the tape library information.

- Drives: In display area, click the **Drives** icon to access the **Drives** page.

- Media management: In display area, click the **Media management** icon to access the **Media management** page.

- Task management: In display area, click the **Task management** icon to access the **Task management** page.

- Configure: In display area, click the **Configure** icon to access the pop-up window where you can manually configure the tape capacity.

   ```{note}
   In order to enable capacity statistics, the tape capacity configuration is only required when ADPS fails to accurately get the tape capacity.
   ```

#### Tape controller

To add a tape controller, do the following:

1. After scanning and initializing the tapes, click the **Controller** button from the toolbar. The Tape library controller page appears. In display area on this page, click the **+** icon. It will show the tape controller details. You can see the tape library connected to the controller.


2. From the toolbar, click the **Add tape controller** icon. The Add tape controller window appears. In the window, do the following:

	(1) Set the **Name** for the controller.

	(2) In the **Address** and **Port** field, enter the IP address and port of the server that has the adps-controller module installed and connected to the tape library.

	(3) Select **SSL** and fill in the **Remark** on demand.

	(4) Click **Submit**.

3. After you submit the adding of the controller, the page will list the records of the tape libraries connected the controller. Go to the Tape Library page and initialize the controller.

​	On the **Tape library controller** page, you can refresh, modify and delete the corresponding controller.

- Refresh: In display area, click the **Refresh** icon to update records of the tape libraries connected to the controller.

- Modify: In display area, click the **Modify** icon to access the Modify tape controller window and modify its configuration.

- Delete: In display area, click the **Delete** icon to delete the controller.

   ```{note}
    When you delete the controller, the corresponding connected tape libraries will be deleted also. Please operate with caution.
   ```

#### Drives

1. On **Tape library** page, click the **Drives** icon in display area. The Drives page appears. You can view the state of the tape drives and information about the media loaded on the drives.


2. When a failed drive is detected, the **Drives** column is shown in red. The drive is unavailable when it is in a failed state.

3. The failed drive shows a red icon in the **State** column, indicating a fault state.

4. Click the **Test** icon to test if the drive can load and unload tape properly.

5. After the test is passed, the drive shows a green icon in the State column, indicating a “Normal” state, and you can use it normally.

#### Media management

1. On **Tape library** page, click the **Media management** icon in display area. The Media management page appears. You can view the tape media details. When the tape library state is Current, Full, or Expired, the Actions column shows a red Forced Recycling.


2. From the toolbar, click the **Discover new tape** icon to synchronize the latest slot state of the tapes in the library, and new tapes will be added automatically to the list. If the capacity of the old and new tapes are different, you can set the tape capacity manually.

3. Select the tape media, and click the **Tape out** icon to export the selected tape media.

4. When you click the **Tape out** icon, the Tape Out request is submitted and redirects to the Task Management page. The tape media will be moved automatically to the tape library gateway. The tape out request is successful after the tape is removed.

5. When the tape in the "Blank" or "Clean" state successfully out, the Tape Library Media page will delete the corresponding records; when the tape in the "Current" state successfully out, its state will be changed to the "Offline" state.

6. After the tape is out of the library, the relevant backup sets will be in an offline state. You can perform the Retrieve operation on the offline backup sets.


7. To retrieve the tape, select the backup set and click its **Retrieve** icon. The Retrieve window appears. In the Retrieve window, you can view the tape information related to the backup set. Click the Retrieve button and the tape will be added to the library.

   ```{note}
   1. The Retrieve request will detect the in-library tapes and automatically move the tapes to the slot if the required tapes are in the tape library gateway.
   2. Tapes will be checked for expiration when they are retrieved to the library, and the retrieved tapes cannot write data.
   ```

#### Task management

1. On **Tape library** page, click the **Task management** icon in display area. The Task management page appears.


2. In display area, click the **Modify** icon to withdraw the tape media. The Withdraw request will refresh the list, and return the tape to the slot.

3. Click the task name to enter the Tape library task detail page where you can Print the page.

### RAID card

Click **Storage** > **RAID card** to enter the RAID Card page where you can view the RAID card state of storage server, the state of Adaptec and MegaRAID.


In the **Adapter** section, the basic information about the RAID card can be viewed.

In the **Virtual drives** section, the basic information about the logical drives can be viewed. Click the “**+**” icon on the left side of the drive, more detailed information will be displayed.

In the **Physical drives** section, the state and details of the drives on the servers can be viewed.

In the **Connector** (for Adaptec) or **Battery** (for MegaRAID) section, the information about the connector or the battery can be viewed.


### Software RAID

1. Mount disks on the machine where the RAID needs to be created (The machine with the storaged module installed).
2. Log in as the admin. Click **Storage pool** > **RAID card** to enter the Software RAID page.

   ```{note}
   Create a storage pool first before entering the Software RAID page, otherwise the page will show “Unable to get the storage pool!”.
   ```

3. Click **Initialize software RAID** to access the pop-up window. Select the **RAID devices**, **RAID type**, and **Spare devices** (RAID 1, RAID 5, and RAID 6 support the spare devices).


4. Click **Submit**, wait a few moments for the initialization to complete and then the software RAID will be in the synchronization state.


5. After the software RAID data synchronization is completed, the state on the Software RAID shows “Active”, which indicates the software RAID is successfully created and mounted to the /datakist directory.

```{note}
Make sure the host has the mdadm tool properly installed before creating the software RAID, otherwise the creation will fail.
```

## Resource

The Resource menu includes Resource and Cluster. From the menu, click **Resource** > **Resource**. The **Resource** page appears. On this page, the following operations are available：

- Install agent
- Add a host
- Register hosts/activate licenses/authorize licenses
- Batch register/batch activate/batch authorize
- Bind the cluster


#### Install agent

To install the Agent, do the following:

1. From the toolbar on **Resource** page, click the **Install Agent** icon. The Install Agent window appears.

2. From the **Select system** list and **Select file** list, select a system and the package that you want to install. The system selection list includes Windows and Linux.

	```{note}
	Select **Remove installation package** if you want to automatically delete the downloaded installation package after installing the agent on a Linux host.
	```

   - From the **Select system** list, select **Linux**:

	(1) The window displays two installation commands (curl and wget) when you select the required modules.

	(2) Select use curl or wget to install the agent, and click the **Copy** icon to copy the installation command.

	(3) Log in to the Linux host as user root. Paste the command in the terminal and press Enter to start the installation.

	(4) Wait for the installation to complete.

	- From the **Select system** list, select **Windows**:

	(1) From the **Select file** list, select the file starting with adps. Click **Download**.

	(2) Upload the package to the Windows host.

	(3) Log in to the Windows host as a user with administrative privileges. Double-click the package and open the installation wizard. Click **Next**.

	(4) At the **Select components** step, select the desired components from component list. Click **Next**.

	(5) Fill in the required information for the backup server.

	(6) Confirm the **Destination folder** or specify another folder. Click **Next**.

	(7) Wait for the installation to complete.

#### Add a host

##### NDMP host

1. From the toolbar, click the **Add** icon, and select **NDMP Host**. The **Add NDMP host** window appears.

2. In the window, do the following:

	(1) Set the **Name** for the NDMP host.

	(2) In the **Address** field, fill in the NDMP host address.

   ```{note}
   If you are using NetApp Clustered Data ONTAP, the NDMP host address must be the address of the Storage Virtual Machine (SVM).
   ```

	(3) In the **Port** field, enter the port of the NDMP service on the NAS host.

	(4) From the **Authentication method** list, select an authentication method.

	- Select **MD**: Enter the NDMP **User** and **Password**. The password is transmitted in encrypted form.

	- Select **TEXT**: Enter the NDMP **User** and **Password**. The password is transmitted in plain text.

	- Select **NONE**: No user and password entered required.

	(5) Select a **Backup host**. Before selecting, you need to install, register, and activate the corresponding NDMP module on the agent host.

	(6) Click **Submit**.

##### Hadoop cluster

ADPS supports two authentication methods for Hadoop clusters: Simple and Kerberos. If you have configured your Hadoop cluster with the Kerberos authentication service, select Kerberos when adding the cluster. If your Hadoop cluster does not have the Kerberos service, you can use the default Simple authentication.

1. From the toolbar, click the **Add** icon, and select **Hadoop Cluster**. The Add Hadoop Cluster window appears.

2. Set the **Name** for the cluster.

3. Select a **Backup host**. Before selecting, you need to install, register, and activate the corresponding Hadoop module with the appropriate license.

4. In the **Host** field, enter the IP address or hostname of the NameNode.

```{note}
If the Principal is created using a hostname, the field should be filled with the hostname, and the hosts file of the machine where ADPS is located should also include the IP address of that host along with its corresponding hostname.
```

5. To use **SSL**, the Hadoop cluster requires HTTPS service to be configured and enabled for use.

6. Enter the **REST API Port** and **RPC API Port**.

	(1) REST API Port: The default value is 50470. If the cluster is set to a different port, then this value needs to be modified according to the actual port.

	(2) RPC API Port: The default value is 8020. If the cluster is set to a different port, then this value needs to be modified according to the actual port.

7. From the **Authentication method** list, select **Simple** or **Kerberos**.

- Select Simple:

	(1) In the **User** field,  enter the HDFS user.

	(2) core-site.xml File: Upload the cluster's core-site.xml file. Simple authentication method does not require this.

	(3) hdfs-site.xml File: Upload the cluster's hdfs-site.xml file. Simple authentication does not require this.

- Select Kerberos:

	(1) In the **User** field,  select a Hadoop user. Ensure that the user is added to the krb5.keytab file through KDC authentication. Install the `krb5-user` tool locally using the command `sudo apt install krb5-user`. Use the `klist -kt krb5.keytab` command to view the contents of the krb5.keytab file and verify the presence of the user's credentials.

	(2) **Realm name**: Enter the Realm name used when configuring Kerberos.

	(3) **Realm KDC**: Enter the IP address or hostname of the Realm KDC server.

	(4) **Realm Admin Server**: Enter the IP address or hostname of the Realm admin server.

	(5) Enter the **RPC API Principal** name and **REST API Principal** name.

	(6) In the **UDP preference limit** field, set the maximum value for the UDP transport package. When the data package exceeds this value, TCP will be used for the transport. The default value is 1, with which TCP is used for the transport. Adjust this value according to the parameter in the `/etc/krb5.conf` file of the KDC service.

	(7) For the **`krb5.keytab` file** field, get the `keytab` file and copy it to a safe location on the host that has access to the ADPS console. In the **`core-site.xml` file** field, upload the `core-site.xml` file of the cluster. In the **`hdfs-site.xml` file** field, upload the `hdfs-site.xml` file of the cluster.

##### Object storage service

1. From the toolbar, click the **Add** icon, and select **Object Storage Service**. The Add object storage service window appears.

2. Set the **Name** for the object storage service.

3. From the **Vendor** list, select a cloud service provider.

4. From the **Endpoint** list, Select or enter the endpoint address of the cloud server.

5. Select or enter the **Region**.

6. Enable or disable SSL

7. Enter the **Access Key** and **Secret Key**.

8. Select a **Backup host**.

9. Click **Submit**.

For registering hosts for other types of resources, please refer to the relevant user guides. The registration process for hosts may vary depending on the specific resource type. It is recommended to consult the respective user guides or documentation for detailed instructions specific to the resource you are working with. These guides typically provide step-by-step instructions and configuration details to help you register hosts for different types of resources.

### Register hosts/Activate licenses/Authorize licenses

1. On the **Resource** page, click the **Register Host** icon.
2. Click **Submit** to activate all the resources that meet the requirements.
3. Click **Authorize**. Only the users under the specified user group have permission to operate the resources of this agent.


### Batch register/Batch activate/Batch authorize

##### Batch register

If there are multiple agents to register, click the **Batch register** icon in the upper left corner. Check the agent that needs to be registered in the pop-up **Batch register** window.

##### Batch activate

1. Click the **Batch activation** icon on the upper left corner, and check the resources that need to be activated in the pop-up **Batch activation** window.

	```{note}
	If the resource module is installed, but there is no corresponding license, the “Activate” link will not be displayed next to the resource module.
	```

2.  You can assign to the same user group when activating resources. If you need to assign the resources to different user groups, see Assign User Group in this guide for details.
3. The “Unregister” icon is displayed to the right of the registered resource. If there is a change in the resource’s user name or password that ADPS cannot detect in time, the resource cannot be used properly. In this case, you can click the “Unregister” icon to log out and log back in with a new username and password.

### Cluster binding

Click **Resource** > **Cluster** to enter the cluster page where you can bind and manage the cluster on the agent.

  ```{note}
  For specific binding operations of each resource cluster, please refer to the respective resource operation manuals.
  ```


## System management

From the menu, click **Settings** > **Settings**. The **Settings** page appears. On this page, the following operations are available:

- System settings

- Webhook settings

- Approval settings

### System settings

You can configure system settings including common, sever, catalog, security, SMTP, storage, resource, job and LDAP authentication.


#### Common

The **Common** settings include Localization, Agent installation configuration, and License.

Localization settings include Default language and Relative time.

(1) **Default language**: Set the default language for the users. The default setting is Automatic. Available languages include Simplified Chinese, English, Traditional Chinese, and Spanish.

(2) **Relative time**: Set the time display format for pages such as Job, Alert, History, and Report. 3 days is selected by default. None, 1 days, 3 days, 1 week, 1 month and all are available.

Agent installation configuration includes Package download address and Backup server connection configuration.

(1) **Package download address**: Set the address to download the agent installation package. The address displayed in the text field is the default address.

- To use the default address, you need to upload the agent package by clicking the **Upload packages** icon on the Upgrade page.

- To use another address, you need to fill in the server address that can be accessed, and save the agent package on the server.

   ```{note}
    You can refer to the instructions by clicking on the **Help** icon to set the download address.
   ```

(2) Backup server connection configuration: To configure the backup server connection, you need to configure the server address, SSL and port.

- **Server address**: Set the backup server address.
- **SSL**: Enable or disable SSL for the agent.
- **Port**: Set the port that the agent connects to the backup server. The default value is 50305. When the SSL is enabled, the default port value is 60305.

#### License

- **Automatic activation**: Unselected by default. Enabling Automatic Activation will automatically activate all instances connected to the server when licenses are sufficient.

#### Server

Server settings include sever and NIC


1. Server

	(1) **Date & Time**: Click the **Modify** icon. The **Time** window appears. Select or enter the date and time.

	(2) Actions include **Reboot** and **Shutdown**.
	- Reboot: Click the button to restart the server remotely.
	- Shutdown: Click the button to shut down the server remotely.

2. NIC

	In the NIC display area, all the network interfaces bound with all servers are listed.

	Set the connection **Method** of the network interface, **IP address**, **Netmask**, **Default gateway**, **Preferred DNS** and **Alternate DNS** as needed. The modification takes effect immediately after you click **Modify**. Click **Reset** to restore the default value.

#### Catalog

Through catalog backup, user, user group, storage pool, and backup set configuration can be retained. Catalog backup is typically used in conjunction with pool replication to facilitate local or remote disaster recovery of backup servers and backup server migration.


```{note}
For specific backup & restore scenarios, see Catalog backup in this guide for details
```

#### Security

Security settings include Security options, Password policy and Access Control List.

**Security options**

(1) **No action prompt timeout**: Log out after the specified time of inactivity. The page will show “Your session has expired, please sign in again!”

(2) **Maximum login attempts**: Set a limit on the number of unsuccessful login attempts that a user can make. Once the limit is exceeded, the IP address of the current user will be blocked or the user will be locked out. The user must contact administrative users to release the IP address from the blocklist.

(3) **Login failed exceeded limit**: If the maximum number of unsuccessful login is exceeded, you can block the IP address of the current user, or lock the user.

   ```{note}
   To block the IP address of the current user, or lock the user, see Appendix.
   ```

(4) **Lock account after N days of inactivity**: If the user's number of inactive days exceeds the set number of days, the user will be locked out.

(5) **Enable audit user**: Audit account login is only available after enabling it.

(6) **Enable security user**: Transfer the user management permissions (including user modification and user group assignment) of the administrative user to the System Security.

(7) **Enable hierarchical multi-tenancy**: This option is only available in a multi-tenant environment. When creating a tenant, you can set the parent level, and the tenant can also create users with tenant roles. After enabling it, you can also choose the currency format for the tenant.

(8) **Enable operating child tenant's resource**: This option is only available in a multi-tenant environment. You can allocate child tenants’ resource permissions and control their resources.

(9) **One role per user**: If the option is selected, the creation of multi-role users will not be allowed. When a user is added or modified, only one user role can be selected.

(10) **Encrypt job definition**: The job definition files on the servers will be encrypted.

(11) **Enable Upgrade page**: Disabling the Upgrade page will also hide all functions on the Upgrade page and the download function on the login page.

(12) **Access Key login instance**: When it is enabled, the Access Key authentication will be added to the instance login page, so that you can choose to log in with the Access Key of your current user account if you forget your OS password.

(13) **Auto login instance**: It appears when the Access Key login instance option is enabled.

(14) **2-step verification**: Users must enter email OTP when logging in. To ensure the successful sending and receiving of email OTP, SMTP and personal email must be configured properly.

(15) **Enable restore feature for pool replication user**: When it is enabled, the replication users are allowed to use the restore function.

**Password policy**

The section provides the following option: Password length, password character inclusion requirements, historical password repetition limit, maximum password age.

#### Webhook

From the menu, click **Settings** > **Webhook**. The **Webhook** page appears. On the page, the adding, modifying and deleting of Webhook(s) are supported.

#### Approval

From the menu, click **Settings** > **Approval**. The **Approval** page appears.

(1) Approval types include:

  - Add job: The adding of a job by the operator needs to be approved by the specified approver before it can be executed.
  - Add credits: Tenant adding credits needs to be approved by the specified approver before it can be executed.
  - Modify storage pool: The modification of a storage pool by the operator needs to be approved by the specified approver before it can be executed.

(2) Approvers: Users can be granted approval permissions.

(3) Actions: Enable or disable the selected approval types.

## License

From the menu, click **License**. The **License** page appears. It displays the license details including the edition, assignee, module, space and advanced features.

- **Request**: Click the **Request** button to request a license.
- **Import**: Import the license file issued by the vendors.
- **Download**: Export the data from the **License** page, with the default format being CSV.

```{note}
When a trial license or resource has expired, click **Request** > **Trial**, you will see Trial License Renewal option. When it is checked, a “Resource” list will be displayed on the module page to automatically list resources available for trial renewal.
```

## Catalog backup

Through catalog backup, user, user group, storage pool, and backup set configuration can be retained. Catalog backup is typically used in conjunction with pool replication to facilitate local or remote disaster recovery of backup servers and backup server migration.

### Disaster recovery

Catalog backup can be used for the local and offsite disaster recovery, which requires that the target recovery host and the storage server that stores the catalog backup sets can communicate with each other.

#### Offsite disaster recovery

Deploy one server each locally and offsite, the network is made isolated and the servers can communicate with each other. Both servers are installed with the backup server and storage server for recovering the backup sets on the local agent host in offsite.

1. Add two storage pools belonging to local and offsite storage servers respectively, and set a pool replication that replicates from the local host to the offsite host.

2. Set a backup schedule that the Catalog will be backed up periodically to the storage pool on the local host.

3. Create backup jobs to the storage pool on the local host.

4. Recovery

	- Log in to the offsite backup server as the system administrator, go to the **Settings** > **Settings** page and click **Catalog **> **Disaster recovery**.

	- If the Catalog backup sets are stored in a standard storage pool, deduplication storage pool, or tape library pool, you can enter the IP address of the offsite storage server (for example, the storage server where the target storage pool is located) and select Restore by Scanning as the Restore Source. When restoring, select a point in time that needs to recover, which is related to the range of backup sets that the resource is allowed to restore. The resource can only restore the backup sets that have been replicated to the target storage pool prior to this point in time.

	- If backup sets are stored in the object storage service pool, you can restore it by selecting Restore by Entering the URL Directly as the Restore Source. When restoring, you need to fill in the s3 object storage path where the Catalog backup sets are located, and enter the Access Key and Secret Key.

	```{note}
	After the recovery is completed, you need to restart the backup server manually.
	```

5. Apply for a license

	The Catalog disaster recovery only keeps licenses for activated resources and does not recover all applied licenses. After registering the agent host on the offsite backup server, you need to apply the license for the resources on the offsite agent host.

6. Deploy an offsite agent host and associate it with an offsite backup server.

7. Set both the local and offsite agent host to the same user group.

8. Create a restore job

	Log in to the backup server as the operator, go to the **Restore** page, select a local agent host, resources, backup sets, restore target and resource on the offsite host, and create the restore job.

  ```{note}
  For authorized systems, it can be performed on the **Settings** page. For unauthorized systems, it can be performed on the product activation page.
  ```

#### Local disaster recovery

In case a backup server disaster occurs, deploy a new backup server and create a catalog restore job to restore the system to normal state.

1. Deploy a new backup server

	Set the IP as the IP of the backup server that occurs disaster, and ensure that the new backup server can communicate properly with the target storage pool of the pool replication.

	The IP of the new backup server is different from that of the old backup server. Before repairing the source pool, enter the **Storage** > **Storage pool** page, select the storage pool record of the old storage server (old backup server) and modify its configuration, enter the address of the new server and then repair the storage pool.

	After repairing the source pool, all agents need to be reconfigured to the new backup server before the system resumes normal operation.

	It is recommended that the IP be consistent with the old backup server IP to reduce the loss caused by misoperation.

2. Recovery

	Log in to the backup server as the system administrator, go to the **Settings** > **Settings** page and click **Catalog** > **Disaster recovery**.

	- If the Catalog backup sets are stored in a standard storage pool, deduplication storage pool, or tape library pool, you can enter the IP address of the storage server and select **Restore by scanning** as the restore source.

	- If backup sets are stored in the object storage service pool, you can restore it by selecting Restore by Entering the URL Directly as the Restore Source. When restoring, fill in the s3 object storage path where the Catalog backup sets are located, and enter the Access Key and Secret Key.

  ```{note}
  After the recovery is completed, you need to restart the backup server manually.
  ```

3. Repair source pool

	Log in to the backup server as the system administrator, enter the **Storage** > **Storage pool** page, click the **Pool replication list** icon, select the storage pool record of the old storage server (old backup server), click the **Repair source pool** icon to repair the source pool. It is recommended to perform a full backup for all resources after repairing the source pool to ensure the continuity of the backup sets in the future.

   ```{note}
   1. Repairing the source pool only restores the database records of the source pool and does not restore the backup sets. Before creating a restore job, it is recommended to replicate the relevant backup sets using pool replication first.
   2. If the source pool is not repaired, an alternative approach is to perform a replication job by configuring the restore source to be the target pool. This enables direct retrieval of backup sets from the target pool for the restore.
   ```

4. Apply for a license

	The Catalog disaster recovery only keeps licenses for activated resources and does not recover all applied licenses. After registering the agent host on the offsite backup server, you need to apply the license for the resources on the offsite agent host.

5. After completing the above operations, ADPS will resume normal state.

### Backup

After you configure the backup schedule, click the **Backup** button on the Catalog page, and the top right corner will show "Submission succeeded", indicating that a Catalog backup is performed immediately to the storage pool configured in the backup schedule. While the backup is in progress, the **Backup** button will show the loading state.


### Restore

Restore the catalog backup sets of the current backup server.

1. After you configure the schedule, the Catalog page will show the **Restore** button.

2. Click the **Restore** button, you can see the Catalog backup sets of the current backup server.


3. Select a backup set and click **Submit** to restore.

## Operations and maintenance management

### Job

From the menu, click **Job**. The **Job** page appears. It shows all backup and restore jobs, and supports filtering the displayed information columns.


- Filter: Click the **Columns** icon to customize the displayed content on the page. The available filtering options include job, state, host, resource, and more. Every column also supports filtering.
- Search: Search by job or host is supported.
- Job: The column allows users to view backup, restore, or all types of jobs. It also supports sorting the jobs in either ascending or descending order.
- State: The column allows users to view jobs in a single or all states.
- Host: The column allows users to view jobs on all hosts or a specific host.
- Resource: The column allows users to view jobs of all resources or a specific resource.
- Download: Different formats can be selected to export job page data.

	- Format: Readable format, raw format
	- Extension: CSV, XML, UOF

- Setting: From the toolbar, click the **Configure** icon. The **Setting** window appears. In this window, the **Show tips for completed jobs** function can be enabled.

### History

From the menu, click **History**. The **History** page appears. It shows all backup and restore job history. The following operations are provided:

- Modify: Click the **Modify** icon to batch delete all or selected job history.
- Refresh: Click the **Refresh** icon to refresh the page.
- Filter: Click the **Columns**  icon to filter the content for display. Every column also supports filtering.
- Download: Click the **Custom download** icon to export the job history. Different formats can be selected to export job history.

	- Format: Readable format, raw format
	- Extension: CSV, XML, UOF

```{note}
System administrator can export all job histories.
```

In a multi-tenant environment, the page displays the job history from all users by default.

### Alert

#### Alert

From the menu, click **Alert** > **Alert**. The **Alert** page appears. Alert details including alert level, host, module, alert time, message type, and message can be viewed. The page show the latest job alert by default, and support filtered alerts. The following operations are provided:

- Mark: Alert emails can be marked as read or unread. Unread alerts are shown in bold font.
- Delete: Click the **Modify** icon to delete all or selected alert records.
- Filter: Click the **Columns** icon to filter the content for display. Every column also supports filtering.
- Download: Click the **Download** icon to export alert records. Different formats can be selected to export alert records.

	- Format: Readable format, raw format
	- Extension: CSV, XML, UOF

```{note}
System administrator and administrative operators can export all alert records.
```

#### Subscription

From the menu, click **Alert** > **Subscription**. The **Subscription** page appears. On the page, alert levels and subscription types of resources and modules can be viewed.

- Subscribe: By selecting one or more records and clicking the **Subscribe** icon from the toolbar, users can subscribe to various levels of alerts for any resources within their authorized permissions. After successful subscription, any generated alerts will be sent to the user's email address via email notifications.
- Unsubscribe: By selecting one or more records and clicking the **Unsubscribe** icon from the toolbar, users can unsubscribe the alert notification.
- Resource: This column displays resources that are within the current user's permission.
- Subscription level: Info, Warning, Error, Critical and Fatal are available.

	- Info: Feedback on the current state of the system.
	- Warning: The system has detected an abnormal state and can perform remedial work to restore the system to a normal state.
	- Error: The system has detected errors and anomalies that prevent it from completing the target operation normally, but it can still perform remedial work to make the target operation complete normally.
	- Critical: The system has detected errors and anomalies that need to be repaired quickly or a fatal problem will occur.
	- Fatal: The system has detected serious errors and anomalies, at this time, the system should be retained as much as possible valid data and stop running.

- Subscription type: Currently, subscribing by mail is only supported.

```{note}
The subscription requires the use of SMTP server. Successful Alert emails delivery to mailboxes is required to configure SMTP server properly.
```

#### Download log

From the menu, click **Alert** > **Download log**. The **Download log** page appears. Select one or more records, and click the **Download** icon to download logs corresponded to hosts.

#### Upgrade

From the menu, click **Upgrade**. The **Upgrade** page appears. It provides information about state, IP address, operating system version of the currently connected agent hosts.

For upgrade:

- The backup agent supports upgrades on Windows and Linux platforms.
- Backup Server supports upgrades of the backupd module and the common module on Windows and Linux platforms.
- Storage Server supports upgrades of storaged modules on Windows and Linux platforms, provided that the backupd module is already installed on the host.

#### Upload packages

From the toolbar, click the **Upload packages** icon. The **Upload packages** window appears. After a successful package upload, the installation package is located in the directory /var/opt/aurreum/adps/backupd/updates/ on the backup server. The audit log provides a record of the upload status for each installation package, allowing users to review the upload details.

The rpm tool needs to be installed on the Linux server when uploading RPM installation packages. Additionally, the backupd service needs to be restarted. Taking Ubuntu system as an example:

 ```{code-block} shell
 root@ubuntu:~# sudo apt install gnupg rpm
 root@ubuntu:~# sudo systemctl restart adps-backupd
 ```

#### View packages

From the toolbar, click the **View packages** icon. The **View packages** page appears.

- Upload packages: From the toolbar, click the **Upload packages** icon. The **Upload packages** window appears.
- Delete all: From the toolbar, click the **Delete all** icon to delete all packages. Additionally, users can select multiple installation packages and click the **Delete** icon to delete the selected packages.
- Delete: In display area, click the **Delete** icon to delete the corresponding package.
- Search: From the toolbar, search by name or file is supported.

### Statistics

From the menu, click **Statistics**. The **Statistics** page appears. The system administrator can count the global data of the system. In a multi-tenant environment, the system administrator can perform statistical analysis belonging to a specific tenant by selecting an account. Other users can perform statistical analysis by selecting dimensions including storage size, host, resource, job speed, and job duration. The statistical data is presented to users in the form of a line chart.

#### Storage size

Storage size counts the size of all backup jobs in a certain period, and shows the trend of data storage size in the form of a line chart. In addition to counting the size of all backup jobs, it can also count the original size and the backup sets size of backup jobs. See below:

#### Host

Host counts the number of all hosts that have executed jobs in a certain period, and shows the number and trend of active hosts in a line chart. See below:

#### Resource

Resource counts the number of resources that have executed jobs in a certain period, and shows the number and trend of active resources in a line chart. See below:

#### Job status

Job status counts the number of each job per day, the number of executions and the number of successes, and displays the status of each job for that period of time in a bar chart. See below:

#### Job speed

Job speed counts the execution speed of each job in a certain period, and shows the speed trend of each job in a line chart. See below:

#### Job duration

Job duration counts the execution time of each job over a certain period. See below:

#### Export

Export the current statistics in png format.

### Report

From the menu, click **Report**. The **Report** page appears. The report page mainly consists of the functionalities to add reports and manage report jobs. It displays reports based on their name, type, description, and available actions. Users can filter the displayed reports based on their specific requirements.

#### Add report

1. From the toolbar, click the **Add Report** icon. The **Add Report** page appears. On the page, select the **Report info**, **Custom fields**, **Custom filter** for the new report. Custom sorting is supported.

- Report info：The available report types include jobs, Windows OS jobs, VM jobs, job count, job history, backup success rate, alerts, client count, storage billing, storage resources, host storage space usage, storage pool space usage, and storage pool label statistics.
- Custom fields: Select the fields to be displayed for the job report.
- Custom filter: Select fields for report filtering.
- Custom sorting: Sorting the field names by dragging them is supported.

2. Click the **New** icon to submit the new report. On the **Report** page, click the report name to view the report details.

#### Modify report

In display area, click the **Modify** icon to enter the **Modify report** page where you can edit the report information, custom fields and custom filters.

#### Download report

In display area, select a report, click the report name, then from the toolbar, click the **Download** icon to select the format to generate, you can choose the format from **CSV**, **XML**, **UOF**.

  - Report format: Readable format, raw format
  - Report extension: CSV, XML, UOF

```{note}
1. Readable format: the fields such as **Original size** and **Speed limit** are displayed in the same format as the page shows, while fields like **Start time** and **End time** show specific date and time.
2. Raw format: the fields such as **Original size** and **Speed limit** are displayed in bytes, while fields like **Start time** and **End time** are presented in specific date and time formats.
```

#### Print report

The **Report** page and **Print record** page can be printed. If the report has too many fields to print completely, you can set the print layout or modify the print settings.

### Label

From the menu, click **More** > **Label**. The **Label** page appears. This page allows users to perform operations such as adding, deleting, and modifying labels. Once a label is created, it can be assigned to storage pools or hosts on the **Storage pool** page or **Resource** page.

### Approvals

From the menu, click **More** > **Approval**. The **Approval** page appears. The approval history can be viewed through the page.

## Limitations

### Storage pool

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Storage pool limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Standard storage pool|For standard storage pools with multi-storage, recovery from other host is not supported if the primary host goes down.|
|Deduplication storage pool|Recycling frees up space by default. {{ br }}Do not support sparse file system. For example, in XFS (before Linux 2.6.38), spatial reuse is still used for space reclamation.{{ br }}Supported file system: NTFS, XFS (since Linux 2.6.38), ext4 (since Linux 3.0), Btrfs (since Linux 3.7).|
| Real-time backup pool|Only available for Oracle and MySQL continuous log backups|
|File synthetic pool|Only available for storage servers on Linux.{{ br }}File system and kernel version requirements for synthetic backups: XFS(since Linux 2.6.38), ext4(since Linux 3.0). |
|Data synthetic pool|Only available for storage servers on Ubuntu.{{ br }}Available for Oracle, SQL Server, MySQL synthetic backup.{{ br }}Data volumes larger than 1T are only supported for level-one pool replication. |
|LAN-free pool|Only available for storage servers on Ubuntu.{{ br }}Backups to LAN-free pools are not supported in dual-cluster environments.{{ br }}In an Oracle RAC environment, backups to LAN-free pools are not supported.{{ br }}The agent can be installed on Linux (Red Hat 4.0 and above), AIX, and Windows 2008 and above.{{ br }}To use iSCSI, it is required to install iscsi-initiator-utils on the agent first.|
```

### Job replication

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Job replication limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Pool replication|Pool replication are not available for local storage pool, LAN-free pool, and file synthesis pool.|
|Job replication|Deleting replication job records is not supported.{{ br }}During replication execution, the corresponding backup points of the target pool cannot be restored. Similarly, during the target pool recovery execution, the replication of the corresponding backup points cannot be performed.{{ br }}Modifying the network in the pool replication settings is not supported for block device deduplication pools.|
|Source pool repair|Pool replication with the object storage service pool as the source pool does not support repairing the source pool.|
```

### Multi tenancy

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Multi tenancy limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Storage pool usage|Storage pools of the corresponding types can only be used after enabling storage space pricing.|
|Storage pool creation|Users can only create object storage service pools, standard storage pools, deduplication storage pools, and block device storage pools by storage regions.|
```

### Catalog

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Catalog limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Backup |Available for standard storage pools, deduplication storage pools, tape library pools, and object storage service pools.|
|Disaster recovery|To restore backup sets to the agent on a remote backup server, it is necessary to restore the catalog first and then associate the agent with the remote backup server. Failure to follow this sequence will result in the failure to restore the backup sets of local resources.|
```

### License

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} License limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|System initialization|Before system initialization, as the host is not registered yet, if the leased edition is selected, module selection is not available. It is necessary to complete the system initialization first, then associate the agent with the host, and finally authorize the host with the leased edition.|
```

### Approval

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Approval limitation
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Job|Databases, files, and Linux OS are supported.|
```

### Job

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Job limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Job modification|Only support modifying the backup content of file backup jobs and Linux OS backup jobs. For clustered environments (RAC, dual cluster), all nodes need to be online in order to modify jobs.|
|Job stop|The Stop Job function is available for Oracle, SQL Server, MySQL, MongoDB, DB2, Informix, GBase, File. For clustered environments (RAC, dual host), all nodes need to be online to stop the job.|
|Job deletion|For clustered environments (RAC, dual host), all nodes need to be online to delete jobs.|
```

### Upgrade

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Upgrade limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Packages upload |The installation package version must be consistent with the backup server's adps-backupd version. If the backup server's adps-backupd is upgraded, the uploaded installation package version must match the upgraded version of adps-backupd.{{ br }}To perform a successful upgrade, all installed ADPS modules on the backup server or agent host must have their corresponding installation packages uploaded.{{ br }}For automatic upgrade, adps-storaged should be installed on the same host as adps-backupd or adps-agent.{{ br }}For agents on HP-UX, Solaris, and AIX host, manual upgrade is required as they do not support web-based upgrades.|
```

### Access key

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Access key limitations
---
class: longtable
---
|Function|Limitations|
| --- | --- |
|Resource login|Multiple resources including Oracle, DB2, MongoDB, Domino, File, and Linux OS are supported for access key login, but when recovering Oracle to a new instance, access key login to the agent side is not supported.|
```

### Installation package

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Installation packages limitations
---
class: longtable
---
|Function|Limitations    |
| --- | --- |
|Windows|Windows, AIX, Solaris, HP-UX do not support the installation of Backup Server, Storage Server.|
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
|traditional backup|The backup interface of the target application is connected with agent to obtain and protect data on a regular basis, using full backup, incremental backup, log backup and other technology.|
|pre-configured users|Users who are already set up with predefined settings or configurations.|
|custom-created users|Users that can be created by the system administrator with the flexibility to customize roles, permissions, access levels, and settings based on specific requirements or preferences.|
|tape controller|The tape controller connects the central processor and the logical control circuit device of the tape drive. The tape controller receives commands from the central processor, analyzes and monitors the execution of the commands.|
|SMTP|A protocol that provides reliable and efficient email delivery.|
|LDAP authentication|LDAP can be used for permission authentication and system user management and authentication.|
```

## Appendix

**Login failed exceeded limit**

See the following example to release from the login IP address blocklist for pre-configured users such as System Administrator (admin), Audit Administrator (audit)

```{code-block} shell
#Enter the database (Taking MariaDB as an example)
root@ubuntu:/#mysql -u root -p
MariaDB [(none)]> use adps
#Query the blocked ip and id
MariaDB [ADPS]> select id , ip from user_black_ip;
+----+-----------------+
| id | ip              |
+----+-----------------+
|  2 | 192.168.xxx.xxx |
+----+-----------------+
MariaDB [adps]> delete from user_black_ip where id =2;
MariaDB [adps]> flush privileges;
```


**Lock period of failed login**

The option is read-only by default. To modify, it is required to be in the background. See the following example to modify the Lock period of failed login and Auto Unlock options.

     - Access the file `/etc/opt/aurreum/adps/backupd/svc.conf.d/00-base.conf` under the backup server. Add `--non-sysuser-auto-unlock` (for custom-created users to unlock automatically) and  `--user-auto-unlock-wait-seconds` (the unlock waiting time. The unit is second) to BACKUPD Service.