# User Guide for OpenStack

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore OpenStack.

The backup and restore features supported by ADPS include:

- Backup types

	Full backup

- Backup sources

   Virtual machines (VMs), virtual disks, domains, and projects

- Backup targets

	Standard storage pool, deduplication storage pool, tape library pool, object storage pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, multiple channels, reconnection, speed limit, and backup retries

- Restore types

   Virtual machine restore, virtual disk restore

- Restore location

   Original project, another project, another host

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup and restore jobs.
```

## Configure the backup host

To configure the backup host, note that:

- Use an independent virtual machine on OpenStack as the backup host. Ensure that the backup host can communicate with the ADPS.
- Ensure that the backup host can access API URL. If the host address in the URL is a domain name, you can configure the domain mapping in `/etc/hosts`.

```{only} scutech
![](../images/Backup_Restore/DBackup3/OpenStack/openstack_host1.png)

![](../images/Backup_Restore/DBackup3/OpenStack/openstack_host2.png)
```

### Verify the compatibility

Ensure that your environment is in the following compatibility list:

````{only} scutech
```{table} Backup host compatibility
| Operating system     | OS bits |
| ----------------  | --------------------- |
| Ubuntu 16.04      | x86_64                |
| Ubuntu 18.04      | x86_64                |
| Ubuntu 20.04      | x86_64                |
| CentOS 7.5        | x86_64                |
| CentOS 7.6        | x86_64                |
| openEuler 22.03   | x86_64                |
| Ubuntu 18.04      | aarch64               |
| CentOS 7.6        | aarch64               |
```
````

````{only} aurreum
```{table} Backup host compatibility
| Operating system     | OS bits |
| ----------------  | --------------------- |
| Ubuntu 16.04      | x86_64                |
| Ubuntu 18.04      | x86_64                |
| Ubuntu 20.04      | x86_64                |
| CentOS 7.5        | x86_64                |
| CentOS 7.6        | x86_64                |
| Ubuntu 18.04      | aarch64               |
| CentOS 7.6        | aarch64               |
```
````

### Install the agent

ADPS agent can be installed on Linux and supports online and offline installation. We recommend online installation.

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

	(2) From the **Component** list, select **OpenStack**. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the feature, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.
6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation.

	```{code-block} python
	root@ubuntu:~# curl -o- "http://192.168.18.112:50305/d2/update/script?modules=openstack&location=http%3A%2F%2F192.168.18.112%3A50305&access_key=61861596d3988682cc2ac008caa9a46c&rm=&tool=curl" | sh
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   100  9211    0  9211    0     0   817k      0 --:--:-- --:--:-- --:--:--  899k
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
   100 52.2M  100 52.2M    0     0  43.3M      0  0:00:01  0:00:01 --:--:-- 93.9M
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
   100 5730k  100 5730k    0     0  38.3M      0 --:--:-- --:--:-- --:--:-- 38.3M
   % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
   0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
   100 5137k  100 5137k    0     0  34.3M      0 --:--:-- --:--:-- --:--:-- 34.3M

   ```

7. Wait for the installation to complete.


## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The backup host with the agent installed appears on the page.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the backup host. Click the **Register** icon. Then the **Activate** window appears.
3. In the **Activate** window, select the resource and click **Submit**.
4. After you activate the license, the **Authorize** window appears. From the **User group** list, select user groups to authorize access to the resource. Click **Submit**.

## Add OpenStack

### Enable OpenStack

To enable the OpenStack features on the console, do the following:

1. Log in to the console as admin.
2. From the menu, click **Settings** > **Settings**. The **Settings** page appears.
3. On the **Resource** tab, select the **OpenStack** checkbox.

   ```{only} scutech
	![](../images/Backup_Restore/DBackup3/OpenStack/openstack_function01.png)
   ```

### Register OpenStack

To add the OpenStack resource, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. From the toolbar, click the "+" icon and select **OpenStack**. The **Add OpenStack** window appears.
3. In the **Add OpenStack** window, do the following:

   ```{only} scutech
   ![](../images/Backup_Restore/DBackup3/OpenStack/openstack_function03.png)
   ```

   - In the **Name** field, enter a name for the resource.
   - In the **Address** field, enter the IP or domain address of the OpenStack host.
   - You can enable **SSL** for secure connection.
   - In the **Port** field, the default value is 5000.
   - In the **Type** field, select **Domain** or **Project**. You can register the resource by using a domain or a project.
   - In the **Domain** or **Project** field, enter the name of the domain or project.

   ```{note}
   - The domain or project will be used to store the temporary resources created during the backup (such as snapshots, images, and disks).
   - If you use **Project**, the temporary resources will be under the project. If you use **Domain**, they will be under any one of the projects in the domain.
   - To use **Domain**, the user must have read, write, and delete permissions on the resource. Only the default user admin has these permissions. Log in to the OpenStack server and execute the `openstack role add --user admin --domain default admin` command.
   ```

   - In the **Username** field, enter the username to log in to the OpenStack server. The user must have read and write permissions on the domain or project.
   - In the **Password** field, enter the password of the user.
   - From the **Backup host** list, select a backup host.
   - The **Advanced options** area is for configuration extension. You can do the following:
      - In the **UUID** field, enter the UUID of the backup host when the Neutron Metadata service is not in effect.
      - In the **User domain name** field, enter the domain name that the user belongs to. The default value is **default**.
      - In the **Project domain name** field, enter the domain name that the project belongs to. The default value is **default**.
      - From the **Directory interface type**, select admin, public, or internal.
      - In the **Compute service** field, enter the URI address of the compute service. When errors occur in the compute service configuration of OpenStack Catalog, you can enter the correct URI address here.
      - In the **Storage service** field, enter the URI address of the compute service. When errors occur in the storage service configuration of OpenStack Catalog, you can enter the correct URI address here.
      - In the **Mirroring service** field, enter the URI address of the compute service. When errors occur in the mirroring service configuration of OpenStack Catalog, you can enter the correct URI address here.
      - In the **Network service** field, enter the URI address of the compute service. When errors occur in the network service configuration of OpenStack Catalog, you can enter the correct URI address here.

4. After you add the OpenStack resource, see [Activate licenses and authorize users](#activate-licenses-and-authorize-users) to activate the resource and authorize user access to the resource.

   ```{note}
   You can enable the **Protected** option in the **Authorize** window to prevent the resource from overwriting.
   ```

### Manage OpenStack

After you add the OpenStack resource, you can manage it on the **Resource** page.

   ```{only} scutech
   ![](../images/Backup_Restore/DBackup3/OpenStack/openstack_function04.png)
   ```

- **Modify**: To change the name and network of the virtualization platform, click the **Modify** icon to edit the above information in the pop-up **Modify OpenStack** window.
- **Configure**: When the registered information of the virtualization platform is changed, you can click the **Configure** icon to update the information in the pop-up **Configure OpenStack** window.
- **Unregister**: If you do not want to back up and restore the OpenStack, click the **Unregister** icon to delete the resource.
- **Tag**: You can add a tag for the resource.


## Backup

ADPS provides full backup for OpenStack.

- Full backup

   Backs up all the disks and configuration files on virtual machines at some point in time.

### Backup policies

ADPS provides six backup schedule types: immediate, one-time, hourly, daily, weekly, and monthly.

- Immediate: ADPS will immediately start the job after it is created.
- One-time: ADPS will perform the job at the specified time once only.
- Hourly: ADPS will perform the job periodically at the specified hour/minute intervals within the time range according to the setting.
- Daily: ADPS will perform the job periodically at the specified time and day intervals.
- Weekly: ADPS will perform the job periodically at the specified time and week intervals.
- Monthly: ADPS will perform the job periodically at the specified dates and times.

You can set an appropriate backup policy based on your situation and requirements. Usually, we recommend the following common backup policy:

Perform a full backup once a week when the application traffic is relatively small (Example: on the weekend) to ensure that you have a recoverable point in time every week.

### Before you begin

Before you back up and restore OpenStack, check the following:

1. Check the resource

   (1) Click **Resource** > **Resource**. The **Resource** page appears.

   (2) Check whether the backup host and the OpenStack resource are on the page with an *Online* state. If no, see [Activate licenses and authorize users](#activate-licenses-and-authorize-users).

2. Check storage pools

   (1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

   (2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.


### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the OpenStack host and resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, do the following:

   (1) From the **Backup type** list, select **Full backup**.

   (2) In the **Backup source** section, click the "+" icon. The **Add backup source** window appears. You can enter the virtual machine name in the search box for accurate search (keyword query is supported) or expand the backup source by domains or projects. Select the virtual machines that you want to back up and click **Submit**.

   ```{note}
   You can use Ctrl and Shift to select multiple items. Confirm the range that you want, select the checkbox of the first item in the range, press Ctrl or Shift, and click the checkbox of the last item in the range. All the items between the first and the last are selected automatically.
   ```

   (3) Confirm the selected backup source. You can use the **Filter** to exclude virtual machines that you do not want to back up. Click **Next**.

4. At the **Backup host** step, select a backup host. Click **Next**.
5. At the **Backup target** step, select a storage pool. Click **Next**.
6. At the **Backup schedule** step, set the job schedule. For details, see [Backup policies](#backup-policies). Click **Next**.

	- Select **Immediate**. ADPS performs the job immediately after it is created.
	- Select **One time** and set the start time for the job.
	- Select **Hourly**. Set the start time, end time, and time interval for job execution. The unit can be hour(s) or minute(s).
	- Select **Daily**. Set the start time and enter the time interval for job execution. The unit is day(s).
	- Select **Weekly**. Set the start time, enter the time interval, and select the specific dates in a week for job execution. The unit is week.
	- Select **Monthly**. Set the start time and months for job execution. You can select the natural dates in one month or select the specific dates in one week.

7. At the **Backup options** step, set the common and advanced options according to your needs. For details, see [Backup options](#backup-options). Click **Next**.
8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
9. After the submission, you will be redirected to the **Job** page automatically. On this page, you can start, modify, and delete the job.

### Backup options

ADPS provides the following backup options:

- Common options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup common options
---
class: longtable
---
|Option|Description|
|---|-----|
|Compression|Fast is enabled by default. Backup data is compressed at the source side for transmission. It can reduce the backup time, improve backup efficiency, and save backup space.|
|Channels|Defines the maximum number of concurrent processes in one job based on the backup host's CPU cores and the virtual load capacity. The default value is 2.|
|Concurrent backup of VMs|The maximum number of virtual machines that can be backed up simultaneously. The default value is 8. We recommend that the value is no more than the number of CPU cores. If it exceeds, the efficiency will not be improved obviously.|
|Concurrent disks per VM|The maximum number of disks per VM that can be processed concurrently. Note that a high value will lead to a high VM IO usage.|
|Concurrent processes per disk|The maximum number of concurrent processes that can be assigned to one disk. Note that a high value will lead to a high VM IO usage.|
|Do not perform backup when|Before taking a snapshot of a virtual machine, the job will check whether the remaining space meets all the specified conditions.|
```


- Advanced options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup advanced options
---
class: longtable
---
|Option|Description|
|---|-----|
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|
|Request retry|- Request timeout seconds: Specifies the maximum time in seconds for a request to complete. If a request times out, it will be terminated.{{ br }}- Request try times: Specifies the maximum number of request retries to connect to OpenStack when a request fails. If the retries exceed the value, the request will be terminated.|
|Backup retry|- Backup retry count: Refers to the number of attempts to run the backup job if it fails to back up some virtual machines. Only the failed virtual machines are processed during a job retry. 0 means no retries. {{ br }}- Backup retry interval: Refers to the time interval between job retries if it fails to back up some virtual machines. Only the failed virtual machines are processed during a job retry.{{ br }}Manually stopping the backup job will not initiate the retry.|
```

## Restore

ADPS provides several restore types for OpenStack, including:

- Virtual machine restore

   When a disaster occurs in the virtual machine, a virtual machine restore job can help you to restore the entire machine. It supports restoring to another project, another host, the original path, and a new path.

- Virtual disk restore

   When a disaster occurs in some disks of the virtual machine, a virtual disk restore job can restore those disks to the original or another virtual machine.

### Before you begin

1. You have a completed backup job. See [Create a backup job](#create-a-backup-job).
2. To restore to another OpenStack platform, deploy the backup host, register and activate the resource, and authorize user access to the resource.

### Create a virtual machine restore job

To create a virtual machine restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the OpenStack host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual machine restore**.

   (3) In the **Restore source** section, expand the resource and select points in time under virtual machines for the restore. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Backup host** step, select a backup host. Click **Next**.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   For the **Restore location** option:
   - Original path: The virtual machine will be restored to its original path with its original name and configuration.
   - New path: The virtual machine will be restored to a customized path with its configuration modified. If you click the icon beside **New path**, a wizard will appear.
      1. At the **Virtual machine** step, configure a new virtual machine name, the target availability zone, target project, a key pair, a security group, and an instance type ID. Click **Next**.
      2. At the **Datastore** step, configure the volume type. Click **Next**.
      3. At the **Network** step, configure the MAC address, IP address, and NIC. Click **Next**.
   ```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

### Create a virtual disk restore job

To create a virtual disk restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the OpenStack host and resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

   (1) From the **Storage pool** list, select a storage pool with backup sets, including the source and target pools with pool replication relation. With the **Default** option, the **Restore source** section shows the backup sets from all storage pools.

   (2) From the **Restore type** list, select **Virtual disk restore**.

   (3) In the **Restore source** section, expand the resource and select disks under points in time for the restore. You can search the name or UUID to find the virtual machine.

   (4) Click **Next**.

4. At the **Restore target** step, select a restore target. The wizard automatically goes to the next step.
5. At the **Backup host** step, select a backup host. Click **Next**.
6. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

7. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.

   ```{note}
   To set the **Restore location**, select a target virtual machine.
   ```

8. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.

### Restore options

ADPS provides the following restore options:

- Common options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Restore common options
---
class: longtable
---
|Option|Description|
|---|-----|
|Channels|Defines the maximum number of concurrent processes in one job based on the backup host's CPU cores and the virtual load capacity. The default value is 1.|
|Restore location|See the respective job section for more details. |
|Start virtual machine after restore|The restored virtual machines are automatically started after the successful restore job.|
```

- Advanced options:

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Restore advanced options
---
class: longtable
---
|Option|Description|
|---|-----|
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Resumption buffer size|Specifies the resumption buffer size. The default value is 10 MiB. The bigger the resumption buffer size is, the more physical storage will be consumed. However, a bigger resumption buffer size can prevent data loss when the throughput of the business system is high.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is restored. The post-script is executed after the resource is restored.|
|Request retry|- Request timeout seconds: Specifies the maximum time in seconds for a request to complete. If a request times out, it will be terminated.{{ br }}- Request try times: Specifies the maximum number of request retries to connect to OpenStack when a request fails. If the retries exceed the value, the request will be terminated.|
|Target virtual machine|You can create a new user or modify the password of an existing user for the target VM. If you leave it blank, only the original user information will be restored by default. To ensure that the settings can take effect, the tool `cloudbase-init` (Windows) or `cloud-init` (Linux) must be installed on the original VM before backup. Without the tool, the settings will not take effect and only the original user information will be restored.|
|Operating system|Please confirm whether the VM is Windows or not. If you do not select the check box, the VM will be regarded as a Linux VM. Because Windows and Linux have different methods of setting the username and password, a wrong selection will lead to the invalidity of the settings.|
```

````{note}
- After you install `cloud-init` on the Linux VM, add the following configuration in `/etc/cloud/cloud.cfg`:

   ```shell
   datasource:
      OpenStack:
      metadata_urls: ["http://169.254.169.254"]
      max_wait: -1
      timeout: 10
      retries: 5
      apply_network_config: True
   ```

- After you install `cloudbase-init` on the Windows VM, add the following configuration in `C:\Program Files\Cloudbase Solutions\Cloudbase-Init\conf\cloudbase-init.conf`:

   ```shell
   [DEFAULT]
                  username=Admin
         groups=Administrators
         inject_user_password=true
         config_drive_raw_hhd=true
         config_drive_cdrom=true
         config_drive_vfat=true
         bsdtar_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\bin\bsdtar.exe
         mtools_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\bin\
         verbose=true
         debug=true
         logdir=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\log\
         logfile=cloudbase-init.log
         default_log_levels=comtypes=INFO,suds=INFO,iso8601=WARN,requests=WARN
         logging_serial_port_settings=COM1,115200,N,8
         mtu_use_dhcp_config=true
         ntp_use_dhcp_config=true
         local_scripts_path=C:\Program Files\Cloudbase Solutions\Cloudbase-Init\LocalScripts\
         check_latest_version=true
         metadata_services=cloudbaseinit.metadata.services.configdrive.ConfigDriveService,
         cloudbaseinit.metadata.services.httpservice.HttpService,
         cloudbaseinit.metadata.services.ec2service.EC2Service,
         cloudbaseinit.metadata.services.maasservice.MaaSHttpService
         plugins=cloudbaseinit.plugins.common.mtu.MTUPlugin,
         cloudbaseinit.plugins.common.sethostname.SetHostNamePlugin,
         cloudbaseinit.plugins.windows.extendvolumes.ExtendVolumesPlugin,
         cloudbaseinit.plugins.windows.userdata.UserDataPlugin,
         cloudbaseinit.plugins.windows.setuserpassword.SetUserPasswordPlugin,
         cloudbaseinit.plugins.windows.localscripts.LocalScriptsPlugin
         allow_reboot=false
         stop_service_on_exit=false
   ```
````

## Limitations

```{tabularcolumns} |\Y{0.15}|\Y{0.85}|
```
```{table} Limitations
---
class: longtable
---
| Feature  | Limitations  |
|--------------------|-------------------------------|
| Backup        | Versions older than or equal to Kilo are not supported.{{ br }}Currently, incremental backup and cumulative incremental backup are not supported.{{ br }}Multi-agent scheduling is not supported.{{ br }}Multiple concurrent jobs are not supported.|
| Restore       | File-level restore is not supported.{{ br }}If you want to restore a Windows VM with a new user, log in to the system using the original user and log out so that the new user will be displayed.|
```