# System Initialization

## Overview

This document provides a quick start guide for users to help them quickly understand the basic operations and functions of ADPS.

## Initialize the system

When you first access the ADPS console, the license request page appears. After importing the license, log in to ADPS and follow the guides to initialize the system. The guides provide a series of initialization operations, such as configuring license, registering storage servers, creating users and user groups, and setting up storage pools.

To access the ADPS console, enter the IP or domain name of the backup server in the browser address bar to access the login page. ADPS supports multiple browsers, including:

- Edge 80 above

- Firefox 60 above

- Chrome 60 above

```{note}
Only the system administrator (admin) can access the initialization guides; other users have no permissions to access it.
```

### Apply for a license

1. Select Aurreum Aurreum Data Protection Suite to activate ADPS. Click **Next**.

2. Select the license edition: Trial, Official, Lease, and Channel. Click **Next**. The **Options** page appears.

- Trial: You can try the software for 30 days. To continue the use, purchase the Official edition.

- Official: You can use the software permanently and enjoy free upgrade service during the software maintenance period.

- Lease: You can use the software during the lease period and upgrade it to the official edition.

- Channel: For small and medium companies. You can use the software permanently and enjoy free upgrade service during the software maintenance period

   ```{only} scutech
    ![](../images/system_initialization/sys_license_01.png)
   ```


3. Set the license options. Click **Next**. The **Modules** page appears.

​	(1) Options for the Trial license:

```{only} scutech
 ![](../images/system_initialization/sys_license_02.png)
```

- Trial license renewal: By requesting the trial license renewal when the license expires or will expire in 7 days, you can keep using ADPS. When the option is enabled, the setting page shows expired resources. You need to bind the expired resource to the license that requires a renewal. The license renewal won’t be valid if you remove the default loaded expired resource and replace it by adding a module.

- Trial days: The trial period is 30 days by default. After the expiration, you can’t continue using the software. To continue the use, apply for the trial license renewal or purchase the officially licensed editions.

- Number of each module: The number of licenses required for each resource module. 5 is selected by default. The option only appears when the **Trial license renewal** option is disabled.

  - Custom: The default number is 5, but you can increase or decrease the number as needed.


  - Unactivated: ADPS will automatically list all associated items.


​	(2) Options for the Official license:

```{only} scutech
 ![](../images/system_initialization/sys_license_03.png)
```

- Licensing mode: You can choose to license by module or by storage space.

     - If you choose **By module**, re-request the license after the module has been used before you may add another module.


     - If you choose **By storage space**, the use of modules will not be restricted. When the storage space is used up, you need to reapply for storage space before the Operator can continue to back up data.


- Maintenance time: You can choose the maintenance time including six months, one year, two years, three years and five years.

  - Duration: The default duration is 12 months, but you can increase or decrease the duration as needed.

  - Support: Select **All Time (7 x 24)** or **Work Time (5 x 8)**.
- Number of each module: The number of licenses required for each resource module. 5 is selected by default.

  - Custom: The default number is 5, but you can increase or decrease the number as needed.
  - Unactivated: ADPS will automatically list all associated items.


- Storage space: The activated storage space for users. When the activated storage space is used up, the Operator cannot back up data to the storage server. To expand the storage space, apply for a new license. Storage space unit includes GiB, TiB, and PiB.

     - Disk storage: Used for local storage pool, standard storage pool, de-duplication storage pool, file synthetic pool, real-time backup pool, block device de-duplication pool, database synthetic pool, block device copy pool, and LAN-free pool.
     - Object storage: Used for object storage service pool.
     - Tape storage: Used for tape storage pool.
     - CD storage: Used for CD-ROM tower storage pool.


​	(3) Options for the Lease license:

```{only} scutech
 ![](../images/system_initialization/sys_license_04.png)
```

- Licensing mode: You can choose to license by module or by storage space.

     - If you choose **By module**, re-request the license after the module has been used before you may add another module.


     - If you choose **By storage space**, the use of modules will not be restricted. When the storage space is used up, you need to reapply for storage space before the Operator can continue to back up data.


- Lease duration: The lease term of the authorized use.
- Number of each module: The number of licenses required for each resource module. 5 is selected by default.

  - Custom: The default number is 5, but you can increase or decrease the number as needed.
  - Unactivated: ADPS will automatically list all associated items.


- Storage space: The activated storage space for users. When the activated storage space is used up, the Operator cannot back up data to the storage server. To expand the storage space, apply for a new license. Storage space unit includes GiB, TiB, and PiB.

     - Disk storage: Used for local storage pool, standard storage pool, de-duplication storage pool, file synthetic pool, real-time backup pool, block device de-duplication pool, database synthetic pool, block device copy pool, and LAN-Free pool.
     - Object storage: Used for object storage service pool.
     - Tape storage: Used for tape storage pool.
     - CD storage: Used for CD-ROM tower storage pool.

​	(4) Options for the Channel license:

```{only} scutech
 ![](../images/system_initialization/sys_license_05.png)
```

- Maintenance time: You can choose the maintenance time including six months, one year, two years, three years and five years.

     - Duration: The default duration is 12 months, but you can increase or decrease the duration as needed.

     - Support: Select **All Time (7 x 24)** or **Work Time (5 x 8)**.
- Number of each module: The number of licenses required for each resource module. 5 is selected by default.

  - Custom: The default number is 5, but you can increase or decrease the number as needed.
  - Unactivated: ADPS will automatically list all associated items.


- Storage space: The activated storage space for users. When the activated storage space is used up, the Operator cannot back up data to the storage server. To expand the storage space, apply for a new license. Storage space unit includes GiB, TiB, and PiB.
     - Disk storage: Used for local storage pool, standard storage pool, de-duplication storage pool, file synthetic pool, real-time backup pool, block device de-duplication pool, database synthetic pool, block device copy pool, and LAN-Free pool.


4. On the **Modules** page, select the modules, the number of modules, and the OS architectures and resource types based on the agent. Click **Next**. The **Advanced** page appears.

```{only} scutech
 ![](../images/system_initialization/sys_license_06.png)
```

5. On the **Advanced** page, choose the relevant options to enable the corresponding advanced features. If your edition is a Trial license, apply again when the advanced features expire. No application of the advanced features for the officially licensed editions is required once the advanced features are enabled.

```{only} scutech
 ![](../images/system_initialization/sys_license_07.png)
```

```{note}
Multi-tenant advanced feature: ADPS establishes an account mechanism for enterprises and organizations. Each account is a tenant, and a private backup and restore system is provided for each tenant in a shared backup environment. You can use the Multi-Tenant option to provide individual service for each tenant, and configure independent data storage space to achieve data isolation via the separate management mechanism.
```

### Import a license

Click **Import license** to upload the acquired license. When the upload is successful, the license page will show details about the uploaded license.

```{note}
The license is for one-time use only, and it is required to correspond to the last generated license request file. If you upload the license generated from the old request file, the license-related operation will fail.
```

### Log in to the system

1. Enter **Username** and **Password**, both of which are "admin" by default. Click **Login**.

  ```{note}
  If a user repeatedly enters the error password, the browser IP address will be added to the Login IP Address Blocklist. The user must apply to the system administrator for a release from the Login IP Address Blocklist.
  ```

2. The **Modify password** window displays. The first login involves a mandatory password change. Click **Submit**.

3. Successfully logged into ADPS and accessed the **Guides** page for initialization configuration.

### Initialization guides

This section helps you to use the initialization guides for a quick configuration.

Configurations include:

- Add user
- Register storage server
- Add storage pool
- Activate resource
- Backup catalog

```{note}
Only the system administrator can access the initialization guides; other users has no permission to access it.
```

#### Add user

Some functions including backup, restore, and disaster recovery are executed by the operator, hence a user with the operator role must be created. Users with the administrative privilege, or other users with system monitor, monitor, and pool replication user roles can be created as needed.


To create a user with the operator role, do the following:

1. On the **Add user** page, enter the required fields: **Username**, **Password**, and **Confirm password**. The fields for the **Organization**, **Department**, **Nickname**, **Email**, and **Telephone** are optional. You can choose whether to enable the **Maximum password age** option.

   ```{only} scutech
    ![](../images/system_initialization/sys_user_01.png)
   ```

2. In the **Roles** section, select **Operator**. From the **User group** list, the **Create user group with same name** is selected by default. Click **Submit**.


3. After the user role is successfully added, click **Next**. The **Register storage server** page appears.

   ```{note}
   To add more users, add them on the **User** page. For details, see [User management](../manager/manager.md#user-management) in Aurreum Data Protection Suite Administrator's Guide.
   ```

#### Register storage server

After the storage server is installed and registered, you can add a storage pool, set up network, and execute other operations.

1. On the **Register storage server** page, select a storage server, click **Register**. A pop-up windows appears. Click **OK**.

   ```{only} scutech
    ![](../images/system_initialization/sys_storage_01.png)
   ```

2. After the registration, a pop-up window appears confirming the setup of management network, which is used for the control and management between backup server and storage server. Click **OK**. An **Add network** window appears.

3. The **Network** option selects **New** by default. Set the **Name** of the network, select **Management Network** as **Purposes**, set the **Address**, **SSL**, and **Port** of the network between the storage server and the backup server.

   ```{note}
   To add more networks, add them on the **Storage** > **Network** page. For details, see [Network](../manager/manager.md#network) in Aurreum Data Protection Suite Administrator's Guide.
   ```

4. The storage server is successfully registered and the status is "Online". Click **Next**. The **Add storage pool** page.

   ```{note}
   To add more storage servers, add them on the **Storage** > **Storage Server** page. For details, see [Storage management](../manager/manager.md#storage-management) in Aurreum Data Protection Suite Administrator's Guide. See Storage Server in this guide.
   ```

#### Add storage pool

After you register the storage server, it is necessary to create storage pools and assign them to users for storing data.

Taking the standard storage pool as an example, to create a storage pool, do the following:

1. On **Add storage pool** page, from the **Type** list, select **Standard storage pool**. Select a storage server. Enable **Multi-storage** as needed. Click **Next**.

   ```{only} scutech
    ![](../images/system_initialization/sys_pool_01.png)
   ```

   - Multi-storage: The storage pool space can be extended by adding storage devices.
   - Allocate storage space: If the **Multi-storage** option is enabled, it is required to allocate storage space to the storage pool. Specify the maximum storage capacity that backup sets can utilize. Once the allocated storage space is fully utilized, further backups to that storage will not be possible.

2. Configure the backup set retention policy and encryption-related options. Click **Next**.

   ```{only} scutech
    ![](../images/system_initialization/sys_pool_02.png)
   ```

   - Backup set retention policy:

	```{tabularcolumns} |\Y{0.2}|\Y{0.2}|\Y{0.35}|\Y{0.25}|
	```
	```{table} Backup set retention policy
   ---
   class: longtable
   ---
   |Retention policy|Parameter|Description|Note |
   | --- | --- | --- |--- |
   |Time policy|Backup sets retention days|Under the condition that each resource has a complete and available backup set, backup sets in the storage pool that exceed the retention period will be marked as expired.{{ br }}The default value is 30 days. 0 means do not use the Backup set retention days rule to delete backup sets.||
   |Space policy|Backup sets retention quota|If the total size of backup sets in the storage pool exceeds the configured retention quota, the earliest full backup sets, as well as the dependent incremental and log backup sets created upon them, will be marked as expired together.{{ br }}10 GiB is retained by default. 0 means do not use the Backup set retention quota rule to delete backup sets.||
   |Time and space policy|Backup sets retention days, backup sets retention quota| If you have set the retention days and retention quota, the backup sets will be marked as expired as long as either set condition is met.|The Backup sets retention policy displayed on the Storage pool page depends on the values entered: If both fields are 0, it shows as "Unlimited"; If either the retention days or retention quota parameter is set to 0, the retention policy will display the non-zero value; If values are entered in both fields, it shows as "XX days or XX GiB/PiB" entered.|
   ```

   - Advanced options：
     - Data storage encryption: If the option is enabled, select **Encryption algorithm**, **Cipher length**, and **Encryption mode**.
     - Specify UUID: You can specify the UUID for the pool you add. This option is for rebuilding the storage pool in case of accidental deletion.

   ```{only} scutech
   ![](../images/system_initialization/sys_pool_03.png)
   ```

   ```{note}
   To add more storage pools, add them on the **Storage** > **Storage pool** page. For details, see [Storage management](../manager/manager.md#storage-management) in Aurreum Data Protection Suite Administrator's Guide.
   ```

3. After the options are configured successfully, click **Next**. The **Activate resource** page appears.

#### Activate resource

After the agent is installed and registered, activate the license and authorize users before the authorized users can perform the backup and restore.

   ```{only} scutech
    ![](../images/system_initialization/sys_agent_01.png)
   ```

1. On the **Activate resource** page, click **Register** next to the host to register the host.

2. In the pop-up **Activate** window, click **Submit** to activate resources.

3. Click **Authorize** to authorize users under the user group before they have permissions to operate the resources on this agent.

   ```{note}
   To register and activate more resources and hosts, register and license in batches on the **Resources** page. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
   ```

4. After the authorization is successful, click **Next**. The **Backup catalog** page appears.

#### Backup catalog

The backup server can be self-protected by backing up catalogs. With backed up catalogs, it can be used with storage pool replication for scenarios like backup server on-premises or offsite disaster recovery and migration. You can use the disaster recovery to reduce the loss when users misoperate, the backup server is abnormal or reinstalled.

1. On the **Backup catalog** page, set the **Name** of the catalog backup job, **Storage pool**, **Retention days**, **Start time** and **Execution interval**.

  ```{only} scutech
    ![](../images/system_initialization/sys_catalog_01.png)
  ```

2. Click **Next**. The system initialization is complete.

   ```{only} scutech
   For specific application scenarios for backup and restore, see [Catalog backup](../manager/manager.md#catalog-backup) in Aurreum Data Protection Suite Administrator's Guide.
   ```