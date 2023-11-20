# Agent Installation Guide

## Overview

This guide describes how to properly install the ADPS agent so that the ADPS backup server can perform unified operation and management of resources.

It includes the following topics:

1. Verify system requirements and platform support.
2. Prepare the packages and environment.
3. Install the packages.
4. Uninstall the agent or some modules from a host.

For some resources such as VMware and Hadoop, the environment may require specific configuration before or after the agent installation. See the corresponding manuals for more details.

## Verify system requirements and platform support

Before installation, check whether your operating systems and resources are supported by ADPS. Unsupported environments could result in problems such as installation failure, abnormal server startup, and unrecognized resources.

### System requirements

1. Windows

   - Windows 2000 / 2003 / 2008 / 2012 / 2016 / 2019 / 2022
   - Windows XP SP3 / 7 / 8 / 10

2. Linux

   - Red Hat Enterprise Linux 3.9 / 4 / 5 / 6 / 7 / 8
   - Oracle Enterprise Linux 4 / 5 / 6 / 7 / 8
   - CentOS 3.9 / 4 / 5 / 6 / 7 / 8
   - SUSE Linux Enterprise Server 9 / 10 / 11 / 12
   - openSUSE 10.3 / 11.4
   - Ubuntu Server 12.04 / 14.04 / 16.04 / 18.04 / 20.04
   - Debian 6.0 / 7.0 / 8.0 / 9.0
   ```{only} scutech
   - Red Flag Asianux 2 / 3 / 7
   - Red Flag DC 4.1 / 5.0
   - NeoKylin Linux 4 / 5 / 6 / 7
   - Inspur K-UX
   - BC-Linux 7.3
   - i-Soft Server OS 3.0 / 4.0 / 5.0
   - YHKylin V4 / V10
   - NFS-CHINA OS 3.1 / 4.0
   - deepin 15.2 / 16
   - Linx OS (linx-6.0.60.4)
   - Linx Rocky OS (rocky 4.2.35 / rocky 4.2.40 / rocky 6.0.42.41)
   - EulerOS 2.0 / 2.8 / 2.10
   - UOS V20
   - AlmaLinux OS 8.5
   - openEuler 20.03 / 22.03
   - TencentOS 2.4
   - FusionOS 22
   ```

3. Unix

   - AIX 5.1 / 5.3 / 6.1 / 7.1 / 7.2
   - HP-UX 11.11 / 11.23 / 11.31
   - Solaris 9 / 10 / 11

4. Kubernetes

   - Kubernetes 1.17 / 1.18 / 1.19 / 1.20 / 1.21 / 1.22 / 1.23 / 1.26

### Platform support for backups and restores

1. Databases

   - Oracle 8i / 9i / 10g / 11g / 12c / 18c / 19c / 21c
   - SQL Server 2000 / 2005 / 2008 / 2012 / 2014 / 2016 / 2017 / 2019 / 2022
   - MySQL 4 / 5 / 6 / 8
   - MariaDB 5.5 / 10.0 / 10.1 / 10.2 / 10.3 / 10.4 / 10.5 / 10.6
   - Percona Server for MySQL 5.5 / 5.6 / 5.7
   - DB2 8.1 / 8.2 / 9.1 / 9.5 / 9.7 / 10.1 / 10.5 / 11.1 / 11.5
   - Sybase ASE 12.5 / 15.5 / 15.7 / 16.0 / 16.5
   - PostgreSQL 8.1 / 8.4 / 9.2 / 9.3 / 9.4 / 9.5 / 9.6 / 10 / 11 / 11.7 / 12.1 / 12.2 / 12.3 / 13 / 13.4 / 13.5 / 13.8 / 14 / 14.1 / 14.2 / 14.3 / 14.4 / 14.5 / 14.6 / 15.0 / 15.1
   - Informix 11.5 / 11.7 / 12.1 / 14.10
   - MongoDB 2.4 / 2.6 / 3.0 / 3.2 / 3.4 / 3.6 / 4.0 / 4.2 / 4.4 / 5.0 / 6.0
   - Caché 2010 / 2016 / 2017 / 2018
   - SAP HANA 1.00.112 / 1.00.122 / 2.00.030 / 2.00.045 / 20.00.056 / 2.00.060 / 2.0.062
   ```{only} scutech
   - GaussDB 100(1.0.1) / 100(1.1.0) / A(6.5.1) / A(8.0.1) / DWS(8.0.1 / 8.1.1 / 8.1.3) / T(1.0.1 / 1.1.0)
   - GaussDB (503.1.0.SPC0200 / 503.1.0.SPC1200)
   - TDSQL 5.7 / 8.0
   - DM 6.0 / 7.1 / 7.6 / 8.0 / 8.1 / 8.2
   - KingbaseES V7 / V8 / V8R2 / V8R3 / V8R6 / V8R7
   - GBase 8a / 8s / 8t
   - ShenTong 7.0 / 7.1
   - openGauss 1.0 / 2.1 / 3.0 / 5.0
   - FusionDB 22.0.1
   - HighGo DB 4.3 / 4.5 / 5.0 / 5.4 / 5.6
   - UXDB 2.0 / 2.1
   - SG-RDB-PG 3.0
   - GoldenDB V5 / V5P3 / V6.1.03T94
   - HotDB 2.5.7
   - MogDB 3.0.3
   - AntDB-PG 11.10 / AntDB-PG 6.3.8 centralized cluster / AntDB-MySQL 10.4.8
   - TBase 2.09 / 2.15
   - SinoDB 12.10
   - Vastbase G100 V2.2
   ```

2. Applications

   - Exchange 2000 / 2003 / 2007 / 2010 / 2013 / 2016 / 2019
   - SharePoint 2007 / 2010 / 2013 / 2019
   - Domino 6.0 / 6.5 / 8.0 / 8.5 / 9.0

3. Distributed files

   - Hadoop (HDFS) 2.2 / 2.6 / 2.6.5 / 2.7.6 / 2.8.3 / 2.9 / 3.0.0 / 3.0.2 / 3.1.0 / 3.2.1 / 3.2.2
   - CDH (HDFS) 6.0 / 6.1 / 6.2 / 6.3
   ```{only} scutech
   - FusionInsight HD (HDFS) V100R002C50 / V100R002C60 / V100R002C70 / V100R002C80
   ```

4. NDMP protocols

   - V3
   - V4

5. Virtual infrastructure

   - VMware vSphere 4.1 / 5.0 / 5.1 / 5.5 / 6.0 / 6.5 / 6.7 / 7.0 / 8.0
   - Hyper-V (2008 R2 / 2012 / 2012 R2 / 2016 / 2019 / 2022) ~ Hosted Architecture
   - Hyper-V Server 2008 R2 / 2012 R2 / 2016 / 2019 ~ Bare Metal Architecture
   - OpenStack Kilo / Liberty / Mitaka / Newton / Ocata / Pike / Queens / Rocky / Stein / Train / Ussuri / Victoria / Wallaby / Xena / Yoga / Zed
   ```{only} scutech
   - KVM (qemu 0.9.8 - 2.8.0, libvirt 0.10 - 4.20)
   - Xen 4.0 and above, libvirt 1.2.8 and above
   - Citrix XenServer 6.2 / 6.5 / 7.0 / 7.1 / 7.2 / 7.3 / 7.4
   - Citrix Hypervisor 7.1 / 8.0 / 8.1 / 8.2
   - FusionSphere V100R005 / V100R006 (5.0 / 5.1 / 6.0 / 6.1 / 6.3 / 6.5)
   - FusionCompute 6.3 / 6.3.1 / 6.5.1 / 8.0 / 8.1 / 8.2 / 8.3
   - FusionCloud 6.1 / 6.3
   - Huawei Cloud Stack 6.5 / 6.5.1 / 8.0 / 8.1 / 8.2
   - H3C CAS E0218 / E0222 / E0225 / E0301 / E0303 / E0306 / E0511 / E0526 / E0530 / E0535 / E0706 / E0710 / E0730 / E0760 / E0780
   - H3C UIS E0712 / E0716 / E0720 / E0721 / E0742 / E0750
   - CNware 6.0 / 6.1 / 7.0 / 7.1 / 7.3
   - CNware WinSphere 8.2 / 9.2
   - ZStack 3.6 / 3.9 / 3.10 / 4.3 / 4.4
   - SmartX 3.5 / 4.0 / 5.0
   - BingoCloud V7 / V9
   - RHV 4.1 / 4.2 / 4.3
   - TStack 6.3 / 7.0
   - StackCube K, CloudView 4.3 / 5.0
   - InCloud Sphere 5.6 / 5.8 / 6.0 / 6.5, ICP V3.5 / ICOS V6.0
   - EasyStack 5.0 / 6.0 / 6.1
   - Alibaba Cloud ECS 3.12 / 3.14
   - SVMMS 2.3
   - KylinCloud 2.3
   - Yunion 3.8
   - Alibaba Public Cloud
   - Hubei Medical Cloud
   - Wo Cloud
   ```

6. Object storage

   - Amazon Web Services (AWS S3) <https://aws.amazon.com/cn/s3/>
   - Microsoft Azure (Blob storage) <https://www.azure.cn/home/features/storage/>
   - Google Cloud Storage <https://cloud.google.com/storage/ss>
   - IBM Cloud Object Storage <https://www.ibm.com/cloud/object-storage>
   - Backblaze B2 <https://www.backblaze.com/b2/cloud-storage.html>
   - Ceph
   ```{only} scutech
   - Alibaba Cloud (OSS) <https://www.aliyun.com/product/oss>
   - Baidu Cloud (BOS) <https://cloud.baidu.com/product/bos.html>
   - Huawei Cloud (OBS) <http://www.hwclouds.com/product/obs.html>
   - Tencent Cloud (COS) <https://cloud.tencent.com/product/cos>
   - Sina Cloud (SCS) <https://www.sinacloud.com/scs.html>
   - China Mobile Cloud (ECloud) <https://ecloud.10086.cn/product-introduction/onest>
   - CT-OOS <http://www.ctyun.cn/product/oos>
   - Kingsoft Cloud (KS3) <https://www.ksyun.com/proservice/storage_service>
   - Qiniu Cloud (Qiniu S3) <https://www.qiniu.com>
   - Meituan Cloud (MSS) <https://www.mtyun.com/product/mss>
   - UCloud (UFile) <https://www.ucloud.cn/site/product/ufile.html>
   - QingCloud (QingStor) <https://www.qingcloud.com/products/storage>
   - Inspur Cloud (OSS) <https://cloud.inspur.com/product/InFile/>
   - BingoCloud
   - XSKY Enterprise Object Storage
   ```
   - Other S3-compatible object storage

7. Operating systems

   - Windows 2003 (Basic disk) / 2008 / 2008 R2 / 2012 / 2012 R2 / 2016 / 2019
   - Windows XP SP3 (Basic disk)
   - Windows 7 / 8 / 10
   - Red Hat Enterprise Linux 4 / 5 / 6 / 7 / 8
   - Oracle Enterprise Linux 4 / 5 / 6 / 7
   - CentOS 4 / 5 / 6 / 7
   - Scientific Linux 4 / 5 / 6
   - SUSE Linux Enterprise 10 / 11 / 12
   - openSUSE 10.3 / 11.4
   - Ubuntu 12.04 / 14.04 / 16.04 / 18.04
   - Debian 6.0 / 7.0 / 8.0 / 9.0
   ```{only} scutech
   - Red Flag Linux DC 5 (Red Flag Asianux 2), Asianux 3 / 4 / 7
   - NeoKylin Linux Server 5 / 6 / 7
   - Kylin V4 / V10
   - i-Soft Server OS 3.0 / 5.0
   - Linx OS (linx-6.0.60.4)
   - Linx Rocky OS (rocky 4.2.35 / rocky 4.2.40 / rocky 6.0.42.41)
   - UOS V20
   - EulerOS 2.0
   - TencentOS 2.4
   ```

### Resource support for database replications

1. Oracle
   - Operating system requirements
      - Windows 2003 / 2008 / 2012 / 2016
      - Red Hat Enterprise Linux 4 / 5 / 6 / 7 / 8
      - CentOS 4 / 5 / 6 / 7 / 8
      - AIX 5.1 / 7.1 / 7.2
      - Solaris 10 / 11
      - HP-UX 11.31
   - Oracle versions
      - Oracle 9i / 10g / 11g / 12c / 18c / 19c / 21c

2. Microsoft SQL Server
   - Operating system requirements
      - Windows 2000 / 2003 / 2008 / 2012
   - SQL Server versions
      - Microsoft SQL Server 2000 / 2005 / 2008 / 2012 / 2014 / 2016 / 2017 / 2019


## Prepare installation packages

```{only} scutech
You can contact ADPS technical support to get ADPS software packages. The version of the ADPS agents must be the same as the version of the ADPS backup server software. Mixed-version installation and use will lead to installation failure and abnormal function. Different operating systems require different agent packages.
```

```{only} aurreum
You can get ADPS software packages from the Aurreum official website. The version of the ADPS agents must be the same as the version of the ADPS backup server software. Mixed-version installation and use will lead to installation failure and abnormal function. Different operating systems require different agent packages.
```

### Packages for Windows

```{tabularcolumns} |\Y{0.50}|\Y{0.50}|
```
```{table} Packages for Windows
---
class: longtable
---
| Software package             | Description                                     |
| -------------------------------------- | ---------------------------------------- |
| adps_version.dbg.exe | A plugin pack that contains the backup and restore modules for various resources on Windows, including file, Oracle, Microsoft SQL Server, MySQL, DB2, Informix, PostgreSQL, MongoDB, Sybase, SharePoint, Domino, and Exchange. You can select the resources and install the corresponding modules according to your needs. |
```

### Packages for Linux

````{only} scutech
```{tabularcolumns} |\Y{0.55}|\Y{0.45}|
```
```{table} Packages for Linux
---
class: longtable
---
| Software package             | Description                                     |
| -------------------------------------- | ---------------------------------------- |
| adps-common-version-.dbg_architecture.suffix         | A dependent package that must be installed           |
| adps-agent-version-.dbg_architecture.suffix          | A base package that must be installed     |
| adps-agent-file-version-.dbg_architecture.suffix     | A package for file backups and restores                  |
| adps-agent-bmr-version-.dbg_architecture.suffix      | A package for Linux operating system (OS) backups and restores             |
| adps-agent-db2-version-.dbg_architecture.suffix      | A package for DB2 backups and restores                 |
| adps-agent-domino-version-.dbg_architecture.suffix   | A package for Domino backups and restores              |
| adps-agent-informix-version-.dbg_architecture.suffix | A package for Informix backups and restores          |
| adps-agent-mongodb-version-.dbg_architecture.suffix  | A package for MongoDB database backups and restores          |
| adps-agent-mysql-version-.dbg_architecture.suffix    | A package for MySQL backups and restores            |
| adps-agent-openstack-version-.dbg_architecture.suffix   | A package for OpenStack backups and restores                |
| adps-agent-hana-version.dbg.architecture.suffix   | A package for SAP HANA backups and restores                |
| adps-agent-oracle-version-.dbg_architecture.suffix   | A package for Oracle backups and restores           |
| adps-agent-mssql-version-.dbg_architecture.suffix    | A package for Microsoft SQL Server backups and restores |
| adps-agent-sybase-version-.dbg_architecture.suffix   | A package for Sybase backups and restores           |
| adps-agent-postgres-version-.dbg_architecture.suffix | A package for PostgreSQL backups and restores       |
| adps-agent-obs-version-.dbg_architecture.suffix      | A package for OBS backups and restores              |
| adps-agent-hadoop-version-.dbg_architecture.suffix   | A package for Hadoop backups and restores           |
| adps-agent-ndmp-version-.dbg_architecture.suffix     | A package for NDMP backups and restores             |
| adps-agent-exchange-version-.dbg_architecture.suffix   | A package for Exchange backups and restores                |
| adps-agent-vmware-version-.dbg_architecture.suffix   | A package for VMware backups and restores                |
| adps-agent-cachedb-version-.dbg_architecture.suffix   | A package for Caché backups and restores                |
| adps-agent-dmdb-version-.dbg_architecture.suffix   | A package for DMDB backups and restores                |
| adps-agent-gaussdb-version-.dbg_architecture.suffix   | A package for GaussDB backups and restores                |
| adps-agent-oscar-version-.dbg_architecture.suffix   | A package for ShenTong backups and restores                |
| adps-agent-fusioncompute-version-.dbg_architecture.suffix   | A package for FusionCompute backups and restores                |
| adps-agent-h3c-cas-version-.dbg_architecture.suffix   | A package for H3C CAS backups and restores                |
```
````

````{only} aurreum
```{tabularcolumns} |\Y{0.55}|\Y{0.45}|
```
```{table} Packages for Linux
---
class: longtable
---
| Software package             | Description                                     |
| -------------------------------------- | ---------------------------------------- |
| adps-common-version-.dbg_architecture.suffix         | A dependent package that must be installed           |
| adps-agent-version-.dbg_architecture.suffix          | A base package that must be installed     |
| adps-agent-file-version-.dbg_architecture.suffix     | A package for file backups and restores                  |
| adps-agent-bmr-version-.dbg_architecture.suffix      | A package for Linux operating system (OS) backups and restores             |
| adps-agent-db2-version-.dbg_architecture.suffix      | A package for DB2 backups and restores                 |
| adps-agent-domino-version-.dbg_architecture.suffix   | A package for Domino backups and restores              |
| adps-agent-informix-version-.dbg_architecture.suffix | A package for Informix backups and restores          |
| adps-agent-mongodb-version-.dbg_architecture.suffix  | A package for MongoDB database backups and restores          |
| adps-agent-mysql-version-.dbg_architecture.suffix    | A package for MySQL backups and restores            |
| adps-agent-openstack-version-.dbg_architecture.suffix   | A package for OpenStack backups and restores                |
| adps-agent-hana-version.dbg.architecture.suffix   | A package for SAP HANA backups and restores                |
| adps-agent-oracle-version-.dbg_architecture.suffix   | A package for Oracle backups and restores           |
| adps-agent-mssql-version-.dbg_architecture.suffix    | A package for Microsoft SQL Server backups and restores |
| adps-agent-sybase-version-.dbg_architecture.suffix   | A package for Sybase backups and restores           |
| adps-agent-postgres-version-.dbg_architecture.suffix | A package for PostgreSQL backups and restores       |
| adps-agent-obs-version-.dbg_architecture.suffix      | A package for OBS backups and restores              |
| adps-agent-hadoop-version-.dbg_architecture.suffix   | A package for Hadoop backups and restores           |
| adps-agent-ndmp-version-.dbg_architecture.suffix     | A package for NDMP backups and restores             |
| adps-agent-exchange-version-.dbg_architecture.suffix   | A package for Exchange backups and restores                |
| adps-agent-vmware-version-.dbg_architecture.suffix   | A package for VMware backups and restores                |
| adps-agent-cachedb-version-.dbg_architecture.suffix   | A package for Caché backups and restores                |
```
````


### Packages for AIX

```{tabularcolumns} |\Y{0.55}|\Y{0.45}|
```
```{table} Packages for AIX
---
class: longtable
---
| Software package             | Description                                     |
| -------------------------------------- | ---------------------------------------- |
| adps-common-version-.dbg_architecture.rpm         | A dependent package that must be installed           |
| adps-agent-version-.dbg_architecture.rpm          | A base package that must be installed                |
| adps-agent-file-version-.dbg_architecture.rpm     | A package for file backups and restores              |
| adps-agent-db2-version-.dbg_architecture.rpm      | A package for DB2 backups and restores               |
| adps-agent-domino-version-.dbg_architecture.rpm   | A package for Domino backups and restores            |
| adps-agent-informix-version-.dbg_architecture.rpm | A package for Informix backups and restores          |
| adps-agent-oracle-version-.dbg_architecture.rpm   | A package for Oracle backups and restores            |
| adps-agent-sybase-version-.dbg_architecture.rpm   | A package for Sybase backups and restores            |
| adps-agent-cachedb-version-.dbg_architecture.rpm  | A package for Caché backups and restores             |
```

### Packages for Solaris

```{tabularcolumns} |\Y{0.50}|\Y{0.50}|
```
```{table} Packages for Solaris
---
class: longtable
---
| Software package              | Description                                                         |
| ---------------------------------- | ------------------------------------------------------------ |
| adps-version-sol9-sparc64-local.gz | A plugin pack that contains the backup and restore modules of various resources on Solaris such as file and Oracle. You can select the resources and install the corresponding modules according to your needs. |
```

### Packages for HP

```{tabularcolumns} |\Y{0.50}|\Y{0.50}|
```
```{table} Packages for HP
---
class: longtable
---
| Software package      | Description                                                        |
| -------------------------- | ------------------------------------------------------------ |
| adps-version-ia64.depot.gz | A plugin pack that contains the backup and restore modules of various resources on HP such as file and Oracle. You can select the resources and install the corresponding modules according to your needs. |
```

### Packages for Kubernetes

```{tabularcolumns} |\Y{0.50}|\Y{0.50}|
```
```{table} Packages for Kubernetes
---
class: longtable
---
| Software package             | Description                                     |
| -------------------------------------- | ---------------------------------------- |
| adps-agent-k8s_version-architecture.tar   | A package for Kubernetes (K8s) backups and restores                |
```

## Install the agent

### Install the agent on Windows

To install the agent on Windows, do the following:

1. Copy the package to the Windows host.
2. Log in to the Windows host as a user with administrative privileges. Double-click the package and open the installation wizard. Click **Next**.
3. At the **Select components** step, select components from the list. Click **Next**.
4. At the **Configure Aurreum Data Protection Suite agent** step, enter the following:

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

5. Confirm the **Destination folder** or specify another folder.
6. Wait for the installation to complete.
7. After the installation, log in to the ADPS console as *admin*. The host appears on the **Resource** page.

### Install the agent on Linux

#### Offline installation

Upload the installation package to the target host for manual installation and configuration. Install the package with root privileges in the directory where the package resides.

##### Install the agent on CentOS & Red Hat

To install the agent on CentOS & Red Hat, do the following:

1. Install the dependent and base packages.

   ```{code-block} python
   rpm -ivh adps-common-version.x86_64.rpm adps-agent-version_x86_64.rpm
   ```

2. Install packages for resource backups and restores according to your needs. Add the corresponding packages in the following command. Take the file resource as an example:

   ```{code-block} python
   rpm -ivh adps-agent-file-version_x86_64.rpm
   ```

3. After the installation, configure the agent. Enter the IP address, port, SSL protocol, and access key to connect to the backup server. The **Access key** field is optional and blank by default. If your backup server adopts multi-tenancy, you must enter the access key of the tenant for the agent.

   ```{note}
	To get the access key of the user/tenant:
	1. Log in to the ADPS console.
	2. On the upper right corner, click your avatar, and go to **Personal settings** > **Account settings**.
	3. On the **Preference** tab, click **View** to get the access key of the current user/tenant.
	```

   ```{code-block} python
   # /etc/init.d/adps-agent config
   Please input Aurreum Data Protection Suite Backup Server host[]: 192.168.18.110
   Please input Aurreum Data Protection Suite Backup Server port[50305]:
   Does Aurreum Data Protection Suite Backup Server enable SSL protocol? [N]:
   Please input Aurreum Data Protection Suite Backup Server access key[]:
   Saving adps-agent config                               [  OK  ]
   Do you want to restart adps-agent? [Y]:
   Stopping adps-agent                                    [  OK  ]
   Starting adps-agent                                    [  OK  ]
   ```

4. After the configuration, log in to the ADPS console as *admin*. The host appears on the **Resource** page.

##### Install the agent on Ubuntu & Debian

To install the agent on Ubuntu & Debian, do the following:

1. Install the dependent and base packages.

   ```{code-block} python
   dpkg -i adps-common-version_amd64.deb adps-agent-version_amd64.deb
   ```

2. Install packages for resource backups and restores according to your needs. Add the corresponding packages behind the following command. Take the file resource as an example:

   ```{code-block} python
   dpkg -i adps-agent-file-version_amd64.deb
   ```

3. After the installation, configure the agent. Enter the IP address, port, SSL protocol, and access key to connect to the backup server. The **Access key** field is optional and blank by default. If your backup server adopts multi-tenancy, you must enter the access key of the tenant for the agent.

   ```{note}
	To get the access key of the user/tenant:
	1. Log in to the ADPS console.
	2. On the upper right corner, click your avatar, and go to **Personal settings** > **Account settings**.
	3. On the **Preference** tab, click **View** to get the access key of the current user/tenant.
	```

   ```{code-block} python
   # sudo /etc/init.d/adps-agent config
   # /etc/init.d/adps-agent config
   Please input Aurreum Data Protection Suite Backup Server host[192.168.18.110]: 192.168.18.110
   Please input Aurreum Data Protection Suite Backup Server port[50305]:
   Does Aurreum Data Protection Suite Backup Server enable SSL protocol? [N]:
   Please input Aurreum Data Protection Suite Backup Server access key[]:
   Saving adps-agent config                               [  OK  ]
   Do you want to restart adps-agent? [Y]:
   Restarting adps-agent (via systemctl):                 [  OK  ]
   ```

4. After the configuration, log in to the ADPS console as *admin*. The host appears on the **Resource** page.

#### Online installation

To install the agent online, do the following:

1. Log in to the ADPS console.

2. From the menu, click **Resource** > **Resource**. The **Resource** page appears.

3. From the toolbar, click the **Install agent** icon. The **Install agent** window appears.

4. In the **Install agent** window, do the following:

   (1) From the **Select system** list, select **Linux**.

	(2) From the **Component** list, select resources that you want to back up. The `curl` and `wget` commands appear in the window.

	(3) If you want to delete the downloaded package automatically after the installation, select the **Delete installation package** check box.

	(4) If you enable **Ignore SSL errors**, the installation will ignore certificate errors and so on. If you disable the option, the installation will prompt you to enter Y/N to continue or discontinue the process when an error occurs.

5. Click the **Copy** icon to copy the `curl` or `wget` command.

6. Log in to the Linux host as user *root*. Paste the command in the terminal and press Enter to start the installation. Example:

   ```{code-block} python
   [root@rhel69 ~]# curl -o- "http://192.168.18.20:50305/d2/update/seript?modules=oracle&location=http83A82F82F192.168.18.20%3A50305&accesskey=22446ab0094d412£84fcc6bf3bd8fele&xm=&tool=curl" | sh
   ```

#### Optional configuration

##### Reconfigure the agent

When the Oracle, Informix, Sybase, PostgreSQL, Domino, and Caché modules are installed but the instances cannot be detected automatically, you may need to reconfigure the agent. Run the following commands:

- Configure Oracle Home directory

  ```{code-block} python
  /etc/init.d/adps-agent config oracle
  ```

- Configure Informix Home directory

  ```{code-block} python
  /etc/init.d/adps-agent config informix
  ```

- Configure Sybase username

  ```{code-block} python
  /etc/init.d/adps-agent config sybase
  ```

- Configure PostgreSQL HOME directory

  ```{code-block} python
  /etc/init.d/adps-agent config postgres
  ```

- Configure Domino config home and Domino program home

  ```{code-block} python
  #/etc/init.d/adps-agent config domino

  Please input Domino config home[]:/local/notesdata/

  Please input Domino program home[]:/opt/ibm/domino/notes/latest/linux
  ```

- Configure Caché HOME directory

  ```{code-block} python
  #/etc/init.d/adps-agent config cachedb

  Please input InterSystems Caché home[]:/opt/cache
  ```

### Install the agent on AIX

To install the agent on AIX, do the following:

1. Install the dependent and base packages.

   ```{code-block} python
   rpm -ivh --nodeps --ignorearch adps-common--version_pcc64.rpm adps-agent-version_pcc64.rpm
   ```

2. Install packages for resource backups and restores according to your needs. Add the corresponding packages behind the following command. Take the file resource as an example:

   ```{code-block} python
   rpm -ivh --nodeps --ignorearch adps-agent-file-version_pcc64.rpm
   ```

3. After the installation, configure the agent. Enter the IP address, port, SSL protocol, and access key to connect to the backup server. The **Access key** field is optional and blank by default. If your backup server adopts multi-tenancy, you must enter the access key of the tenant for the agent.

   ```{note}
	To get the access key of the user/tenant:
	1. Log in to the ADPS console.
	2. On the upper right corner, click your avatar, and go to **Personal settings** > **Account settings**.
	3. On the **Preference** tab, click **View** to get the access key of the current user/tenant.
	```

   ```{code-block} python
   #/etc/init.d/adps-agent config
   Please input Aurreum Data Protection Suite Backup Server host[]: 192.168.18.110
   Please input Aurreum Data Protection Suite Backup Server port[50305]:
   Does Aurreum Data Protection Suite Backup Server enable SSL protocol? [N]:
   Please input Aurreum Data Protection Suite Backup Server access key[]:
   Saving adps-agent config                               [  OK  ]
   Do you want to restart adps-agent? [Y]:
   Stopping adps-agent
   0513-044 The adps-agent Subsystem was requested to stop.
   Waiting for adps-agent to stop.
   Starting adps-agent
   0513-059 The adps-agent Subsystem has been started. Subsystem PID is 18284820.
   ```

4. After the configuration, log in to the ADPS console as *admin*. The host appears on the **Resource** page.

### Install the agent on HP-UX

To install the agent on HP-UX, do the following:

1. Log in to the HP-UX host as user *root*.

2. Run the following command to unzip `adps–version-ia64.depot.gz`:

   ```{code-block} python
   gunzip /tmp/adps-version-ia64.depot.gz
   ```

3. Enter the absolute path of the unzipped package (Example: `/tmp/`) and add the module name that you want to install. Take the Oracle resource as an example:

   ```{code-block} python
   swinstall -s /tmp/adps-version-ia64.depot adps.agent-oracle
   ```

4. After the installation (or to change the backup server), connect the agent to the backup server. Run the following configuration command:

   ```{code-block} python
   /sbin/init.d/adps-agent config
   ```

5. After the configuration, log in to the ADPS console as *admin*. The host appears on the **Resource** page.

### Install the agent on Solaris

To install the agent on Solaris, do the following:

1. Log in to the Solaris host as user *root*.

2. Select the corresponding installation package according to the Solaris system, bits, and resources. Take the Solaris 9 SPARC 64-bit as an example. Run the unzip command:

   ```{code-block} python
   sudo gunzip adps-version-sol9-sparc64-local.gz
   ```

3. Run the following installation command:

   ```{code-block} python
   sudo pkgadd -d /tmp/adps-version-sol9-sparc64-local
   ```

4. Select the modules that you want to install. Example: `AURREUMadps-common`, `AURREUMadps-agent`, `AURREUMadps-agent-oracle`.

5. Enter y or press Enter to the next step according to the prompts.

6. After the installation, connect the agent to the backup server. Run the following configuration command:

   ```{code-block} python
   sudo /etc/init.d/adps-agent config
   ```

7. If you install the Oracle module, you need to configure the `ORACLE_HOME` directory.

   ```{code-block} python
   sudo /etc/init.d/adps-agent config oracle
   ```

8. After the configuration, log in to the ADPS console as *admin*. The host appears on the **Resource** page.

### Install the agent on Kubernetes

To install the agent on Kubernetes, do the following:

1. Upload the `adps-agent-k8s_version-architecture.tar` to all the K8s nodes and run the following command to load the image.

   ```{code-block} python
   docker load -i adps-agent-k8s_version-architecture.tar
   ```

   Load the image on all K8s nodes. After loading, you can check the result by using the `docker images` or `ctr image ls` command.

2. Install and configure the agent on K8s nodes as a Pod. Take the master node as an example. Do the following:

   (1) Create a namespace. You can name it as you want (`backup`, in this example).

   ```{code-block} python
   kubectl create namespace backup
   ```

   (2) Create a `hostid` to record the name of the namespace, which will be used in `- name: HOSTID_0` when you configure the `k8s-agent.yaml` file:

   ```{code-block} python
   uuidgen -r | sed "s/-//g"
   ```

   (3) Run `kubectl apply -f cluster.yaml` and apply the following cluster-admin authorization configuration in `cluster.yaml` to the node.

   ```{code-block} python
   # cat cluster.yaml
   apiVersion: rbac.authorization.k8s.io/v1
   kind: ClusterRoleBinding
   metadata:
   name: backup-k8s
   roleRef:
   apiGroup: rbac.authorization.k8s.io
   kind: ClusterRole
   name: cluster-admin
   subjects:
   - kind: ServiceAccount
   name: default
   namespace: backup
   ```

   (4) Run `kubectl apply -f k8s-agent.yaml` and apply the following configuration in `k8s-agent.yaml` to create a Pod for the agent on the node.

   ```{code-block} python
   apiVersion: apps/v1
   kind: StatefulSet
   metadata:
    name: backup-agent
    namespace: backup
   spec:
   selector:
    matchLabels:
      app: backup-agent
   serviceName: backup-agent
   replicas: 1
   template:
    metadata:
      labels:
        app: backup-agent
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchLabels:
                  app: backup-agent
              topologyKey: "kubernetes.io/hostname"
      containers:
        - env:
            - name: BACKUPD_HOST
              value: 172.16.30.33   # Enter the backup server IP
            - name: BACKUPD_PORT
              value: "50305"
            - name: HOSTID_0
              value: 8b5ea24830544c519593624cc2b5a62d   # Modify the HOSTID value
            - name: BACKUPD_SSL
              value: "false"
            - name: POD_IMAGE
              value: registry.aliyuncs.com/k8sxio/pause:3.2  # Modify the value. You can use the docker images | grep pause command to check the POD_IMAGE
            - name: POD_NAME
              value: backup-pod
            - name: DEPLOY_METHOD
              value: statefulset
            - name: HOSTNAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: BACKUP_NODE
              valueFrom:
                fieldRef:
                  fieldPath: spec.nodeName
          image: registry.docker.aurreum.com/stable/focal/agent-k8s:version  # Modify the image name. You can use the docker images or ctr image ls command to check the image name
          imagePullPolicy: IfNotPresent
          resources:
            limits:
              cpu: "1"
              memory: "1Gi"   # Set the CPU and memory usage
            requests:
              cpu: "0.5"
              memory: "512Mi"
          name: agent
          securityContext:
            privileged: true
          volumeMounts:
            - mountPath: /var/log/backup
              name: log-volume
            - mountPath: /var/opt/back/backup/agent
              name: opt-volume
            - mountPath: /var/lib/kubelet/pods
              mountPropagation: HostToContainer
              name: pods-path
            - mountPath: /dev
              mountPropagation: HostToContainer
              name: dev
      hostIPC: true
      hostNetwork: true
      hostPID: true
   #      nodeName: k8s-master-82   # This field is not required by default, but if you use LAN-free pools, you must specify the node.
   #      nodeSelector:
   #          kubernetes.io/hostname: k8s-master-82   # This field is not required by default, but if you use LAN-free pools, you must specify the node.
      volumes:
        - hostPath:
            path: /var/lib/kubelet/pods
          name: pods-path
        - hostPath:
            path: /dev
          name: dev
        - hostPath:
            path: /opt/data/opt_volume
          name: opt-volume
        - hostPath:
            path: /opt/data/log_volume
          name: log-volume
   ```

## Uninstall the agent

### Uninstall the agent from Windows

To uninstall the agent from Windows, do the following:

1. From the **Start** menu, double-click the `uninstall-adps` application.

2. Select the **Agent** module. Click the **Uninstall** button.

3. After the uninstallation, click the **Close** button.

### Uninstall the agent from Ubuntu & Debian

This method applies to Linux with the APT package manager. Run the following commands with root privileges to uninstall modules or all components.

- Uninstall a module. For example, to uninstall the `adps-agent-file` module:

   ```
   sudo apt-get purge adps-agent-file
   ```

- Uninstall all adps components.

   ```{note}
   The command will delete the whole software including configuration files.
   ```

   ```
   sudo apt-get purge adps-common
   ```

### Uninstall the agent from CentOS & Red Hat & AIX

This method applies to Linux with the PRM package manager and AIX. Run the following commands with root privileges to uninstall modules or all components.

- Uninstall a module. For example, to uninstall the `adps-agent-file` module:

   ```
   rpm -e adps-agent-file
   ```

- Uninstall all adps components.

   ```
   rpm -e $(rpm -qa | grep adps)
   ```

### Uninstall the agent from HP-UX

Run the following commands to uninstall modules or all components:

- Uninstall the modules. Add modules behind the following command.

   ```
   swremove adps-agent adps-common
   ```

- Uninstall all adps components.

   ```
   swremove adps*
   ```

### Uninstall the agent from Solaris

Run the command with root privileges. Add the modules in the command according to your needs.

```
pkgrm AURREUMadps-agent AURREUMadps-common
```

### Uninstall the agent from Kubernetes

To uninstall the agent on Kubernetes, do the following with master privileges:

1. Run the following command.

   ```
   kubectl delete -f k8s-agent.yaml
   ```

2. Delete the image.

   ```
   docker rmi TAG (The TAG of the image. Use `docker images` or `ctr image ls` to check the TAG)
   ```