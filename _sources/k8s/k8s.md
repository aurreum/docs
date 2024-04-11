# User Guide for Kubernetes

## Overview

This guide introduces how to install and configure the ADPS agent, and how to properly use ADPS to back up and restore Kubernetes.

The backup and restore features supported by ADPS include:

- Backup sources

  Namespaces, workloads

- Backup type

	Full backup

- Backup targets

	Standard storage pool, deduplication storage pool, tape library pool, object storage pool, and LAN-free pool

- Backup schedules

	Immediate, one-time, hourly, daily, weekly, and monthly

- Data processing

	Data compression, data encryption, multiple channels, reconnection, speed limit, and replication

- Restore types

  Point-in-time restore

- Restore targets

  Original host, different host

## Planning and preparation

Before you install the agent, check the following prerequisites:

1. You have already installed and configured other backup components, including the backup server and the storage server.
2. You have created a user with roles of operator and administrator on the ADPS console. Log in to the console with this user to back up and restore the resource.

```{note}
The administrator role can install and configure agents, activate licenses, and authorize users. The operator role can create backup/restore jobs.
```

## Install and configure the agent

### Verify the compatibility

Before you install the agent, ensure that your Kubernetes version is on the Aurreum Data Protection Suite's compatibility lists.

- Kubernetes 1.17/1.18/1.19/1.20/1.21/1.22/1.23/1.26

### Install the agent

ADPS supports the backup and restore of Kubernetes clusters on Linux.

The agent can be installed through images. To install the agent, do the following:

1. Take the master node for example. If you cannot operate on the master node, copy the `admin.conf` file from the master node to the target node.

  ```shell
  scp /etc/kubernetes/admin.conf root@<node IP>:/etc/kubernetes/
  echo "export KUBECONFIG=/etc/kubernetes/admin.conf" >> ~/.bash_profile
  source ~/.bash_profile
  ```

2. Load the image (all nodes require this step).

  ```
  docker load -i agent-k8s-version.tar
  ```

3. Check the image.

  ```
  sudo docker images| grep k8s # Or use the ctr images ls | grep k8s command
  ```

4. Create a namespace and specify the `hostid`.

  ```
  kubectl create ns backup

  uuidgen -r | sed "s/-//g"   # Specify the hostid in the configmap by yourself
  ```

5. Configure the `cluster.yaml` as follows:

  ```
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


  kubectl apply -f cluster.yaml

  ```

6. Configure the agent as follows (You can deploy multiple agents to run multiple jobs. Agents can be switched over among nodes. When you deploy multiple agents, they may be scheduled to the same node.)

  ```
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
  replicas: 1 # Replica number
  template:
  metadata:
    labels:
      app: backup-agent
  spec:
    containers:
      - env:
          - name: BACKUPD_HOST
            value: 172.16.30.197
          - name: BACKUPD_PORT
            value: "50305"
          - name: HOSTID_0  # Specify the hostid from number 0
            value: 9e26580745ab41fea2f80bf96e739186
          - name: BACKUPD_SSL
            value: "false"
          - name: POD_IMAGE
            # Modify the value. You can use the docker images | grep pause command to check the value
            value: registry.aliyuncs.com/k8sxio/pause:3.2
          - name: POD_NAME
            value: backup-pod
          - name: DEPLOY_METHOD
            value: statefulset
          - name: HOSTNAME
            valueFrom:
              fieldRef:
                fieldPath: metadata.name
        # - name: BACKUPD_ACCESS_KEY
        #   value: "42fff1271225cf15198e55a886e78945"
        - name: BACKUP_NODE
            valueFrom:
              fieldRef:
                fieldPath: spec.nodeName
        # Modify the image name. You can use the docker images or ctr image ls command to check the image name
        image: registry.docker.aurreum.com/stable/focal/agent-k8s:version
        imagePullPolicy: IfNotPresent
        name: agent
        resources: {}
        securityContext:
          privileged: true
        volumeMounts:
          - mountPath: /var/log/adps
            name: log-volume
          - mountPath: /var/opt/aurreum/adps/agent
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
    #nodeName: k8s-master-85  # This field is not required by default, but if you use LAN-free pools, you must specify the node.
    #nodeSelector:
    #     kubernetes.io/hostname: k8s-master-85  # This field is not required by default, but if you use LAN-free pools, you must specify the node.
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

7. After the installation, use the `kubectl get pod -n backup` command to check whether the agent is running or not (`backup` is an example of the namespace, change it according to your settings).

  ```
  [root@k8s-master-106 ~]# kubectl get pod -n backup
  NAME             READY   STATUS    RESTARTS   AGE
  backup-agent-0   1/1     Running   3          7h46m
  ```

## Activate licenses and authorize users

After the agent installation, go back to the **Resource** page. The host with the agent installed appears on the page.

To activate licenses and authorize users, do the following:

1. From the menu, click **Resource** > **Resource**. The **Resource** page appears.
2. On the **Resource** page, select the host. Click the **Register** icon. Then the **Activate** window appears.
3. In the **Activate** window, select the resource and click **Submit**.
4. After you activate the license, the **Authorize** window appears. From the **User group** list, select user groups to authorize access to the resource. Click **Submit**.

```{note}
1. If you are prompted with "No enough licenses", contact the administrator to add licenses.
2. With many agents, install them first, then **batch register**, **batch activate**, and **batch authorize** the agents and resources for convenience. For details, see [Batch register/Batch activate/Batch authorize](../manager/manager.md#batch-registerbatch-activatebatch-authorize) in Aurreum Data Protection Suite Administrator's Guide.
```

## Backup

### Backup types

ADPS provides full backup for Kubernetes.

- Full backup

  Backs up all the data on Kubernetes and enough logs to recover the data.

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

Before you back up and restore Kubernetes, check the following:

1. Check the resource status

  (1) Click **Resource** > **Resource**. The **Resource** page appears.

  (2) Check whether the host and the Kubernetes resource are on the page with an *Online* state. If they are offline, check whether the agent service and the Kubernetes service are running.

2. Check storage pools

  (1) From the menu, click **Storage** > **Storage pool**. The **Storage pool** page appears.

  (2) Check whether the display area has any storage pools. If no, create a storage pool and authorize it for the current user. For details, see [Add a storage pool](../manager/manager.md#add-a-storage-pool) in Aurreum Data Protection Suite Administrator's Guide.

  ```{note}
  If you want to use LAN-free pools, do the following:
  1. Run the `modprobe iscsi_tcp` command to load the kernel of all hosts. You can use the `lsmod | grep iscsi_tcp` command to check whether the loading is successful.
  2. Run the `/usr/sbin/iscsid` command in the agent pod.
  ```

3. Check the Kubernetes cluster status

  Here provides the Linux command to check the Kubernetes cluster status.

  (1) Use the `systemctl status kubelet` command to check whether the kubelet service is active (running).

  ```
  [root@k8s-master-106 ~]# systemctl status kubelet
  ● kubelet.service - kubelet: The Kubernetes Node Agent
    Loaded: loaded (/usr/lib/systemd/system/kubelet.service; enabled; vendor preset: disabled)
    Drop-In: /usr/lib/systemd/system/kubelet.service.d
            └─10-kubeadm.conf
    Active: active (running) since Thu 2022-10-20 16:47:15 CST; 53min ago
      Docs: https://kubernetes.io/docs/
  Main PID: 1169 (kubelet)
      Tasks: 29
    Memory: 161.6M
    CGroup: /system.slice/kubelet.service
            └─1169 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernetes/kubelet.conf --config=/var/lib/kubelet/config.yaml --network-plugin...
  ```

  (2) Use the `kubectl get nodes -owide` command to check the version of the Kubernetes cluster and whether all nodes are Ready. Ensure that the versions of all nodes are not older than v1.17.0. Otherwise, the CSI driver is not supported.

  ```
  [root@k8s-master-106 ~]# kubectl get nodes -owide
  NAME             STATUS   ROLES    AGE   VERSION   INTERNAL-IP     EXTERNAL-IP   OS-IMAGE                KERNEL-VERSION                CONTAINER-RUNTIME
  k8s-master-106   Ready    master   30d   v1.19.5   172.16.12.106   <none>        CentOS Linux 7 (Core)   5.4.219-1.el7.elrepo.x86_64   docker://19.3.11
  k8s-node-107     Ready    <none>   30d   v1.19.5   172.16.12.107   <none>        CentOS Linux 7 (Core)   5.4.219-1.el7.elrepo.x86_64   docker://19.3.11
  k8s-node-108     Ready    <none>   30d   v1.19.5   172.16.12.108   <none>        CentOS Linux 7 (Core)   5.4.219-1.el7.elrepo.x86_64   docker://19.3.11
  ```

  (3) Use the `kubectl get pod -A` command to check whether all the pods in the Kubernetes cluster are Running.

  ```
  [root@k8s-master-106 ~]# kubectl get pod -A
  NAMESPACE      NAME                                         READY   STATUS      RESTARTS   AGE
  backup         backup-agent-0                               1/1     Running     3          7h15m
  kube-system    calico-kube-controllers-6c89d944d5-sgspg     1/1     Running     9          30d
  kube-system    calico-node-442p9                            1/1     Running     9          30d
  kube-system    calico-node-hqlx4                            1/1     Running     7          30d
  kube-system    calico-node-srnvz                            1/1     Running     7          30d
  kube-system    coredns-59c898cd69-bcf7r                     1/1     Running     9          30d
  kube-system    coredns-59c898cd69-t97vc                     1/1     Running     10         30d
  kube-system    etcd-k8s-master-106                          1/1     Running     9          30d
  kube-system    kube-apiserver-k8s-master-106                1/1     Running     13         30d
  kube-system    kube-controller-manager-k8s-master-106       1/1     Running     76         30d
  ```

  (4) Use the `kubectl get sc` command to check whether the corresponding StorageClass of the CSI driver exists and whether the RECLAIMPOLICY of the StorageClass is Delete. If the RECLAIMPOLICY is not Delete, the temporary PV and volumes in Ceph created during backup cannot be deleted after the backup.

  ```
  [root@k8s-master-106 ~]# kubectl get sc
  NAME         PROVISIONER        RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
  csi-rbd-sc   rbd.csi.ceph.com   Delete          Immediate           true                   30d
  ```

  (5) Use the `ceph -s` command to check whether the Ceph cluster status is normal and ensure that:

  - the Ceph cluster has enough space
  - the Ceph version is later than v14.0
  - the kernel version is later than or equal to 5.1

  ```
  [root@k8s-master-106 ~]# ceph -s
    cluster:
    id:     948d9908-dd20-4866-beea-e798e82f0252
    health: HEALTH_OK

  services:
    mon: 1 daemons, quorum k8s-master-106 (age 24h)
    mgr: k8s-master-106(active, since 24h)
    osd: 3 osds: 3 up (since 24h), 3 in (since 5d)

  data:
    pools:   1 pools, 128 pgs
    objects: 825 objects, 2.3 GiB
    usage:   9.8 GiB used, 590 GiB / 600 GiB avail
    pgs:     128 active+clean

  io:
    client:   62 KiB/s wr, 0 op/s rd, 4 op/s wr
  ```

```{note}
1. Since Kubernetes v1.20, Kubernetes removes the `metadata.selfLink` by default but some applications still use it, for example, nfs-client-provisioner. To use these applications, enable `metadata.selfLink`. Modify the `/etc/kubernetes/manifests/kube-apiserver.yaml` file, add a line `- --feature-gates=RemoveSelfLink=false` in the start parameter, and run `kubectl apply -f kube-apiserver.yaml`.
2. CSI reference: [ceph-csi.git](https://github.com/ceph/ceph-csi.git)
3. Snapshot reference: [external-snapshotter.git](https://github.com/kubernetes-csi/external-snapshotter.git)
```

### Log in to the resource

Before you create a backup or restore job, log in to the Kubernetes instance and authenticate the identity. ADPS provides one authentication method:

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
2. From the host list, find the host where the files reside. If you have many hosts, use the search bar to find the host quickly. Click the host to expand its resource list.
3. Click **Login** beside the resource. The **Login** window appears.
4. In the **Login** window, enter the access key of the current ADPS user, and click **Login**.
5. If your information is correct, you will be prompted that you have logged in to the resource successfully.

### Create a backup job

To create a backup job, do the following:

1. From the menu, click **Backup**. The backup job wizard appears.
2. At the **Hosts and resources** step, select the host and the Kubernetes resource. The wizard goes to the next step automatically.
3. At the **Backup source** step, select **Full backup**. Click **Next**.
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

ADPS provides the following backup options:

- Common options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup common options
---
class: longtable
---
| Option           | Description                 |
| ------------- |---------- |
|Compression|Fast is enabled by default. Backup data is compressed at the source side for transmission. It can reduce the backup time, improve backup efficiency, and save backup space.|
|Channels|It can improve backup efficiency. The default value is 1 and the value ranges from 1 to 255.{{ br }}We recommend a value the same as the number of CPU cores. If the value exceeds the core number, the efficiency improvement will not be obvious.|
```

- Advanced options

```{tabularcolumns} |\Y{0.4}|\Y{0.6}|
```
```{table} Backup advanced options
---
class: longtable
---
|Option|Description|
| --- | --- |
|Reconnection time|The value ranges from 1 to 60 minutes. The job continues after the abnormal reset occurs in the network within the set time.|
|Speed limit|Limits data transfer speed or disk read/write speed for different time periods. The unit can be KiB/s, MiB/s, and GiB/s.|
|Precondition|Checked before the job starts. The job execution will be aborted and the job state will be idle when the precondition is invalid.|
|Pre-/Post-script|The pre-script is executed after the job starts and before the resource is backed up. The post-script is executed after the resource is backed up.|
```

## Restore

ADPS provides point-in-time restore for Kubernetes.

- Point-in-time restore

  Restores Kubernetes to a specified point in time. The restore target can be the original host or a different host.

### Before you begin

To restore the instance to a different host, install the agent on that Kubernetes cluster, activate the licenses, and authorize user access to the resource.

### Create a point-in-time restore job

To create a point-in-time restore job, do the following:

1. From the menu, click **Restore**. The restore job wizard appears.
2. At the **Hosts and resources** step, select the host where the files reside and select the resource. The wizard goes to the next step automatically.
3. At the **Backup sets** step, do the following:

  (1) From the **Restore type** list, select **Point-in-time restore**.

  (2) In the **Restore source** section, select a backup set for the restore job.

  (3) Click **Next**.

4. At the **Restore target** step, select a host and resource as the target. The wizard goes to the next step automatically.
5. At the **Restore schedule** step, set the job schedule. Click **Next**.

	- Select **Immediate**. ADPS will perform the job immediately after its creation.
	- Select **One time** and set the start time for the job.

6. At the **Restore options** step, set the options according to your needs. See [Restore options](#restore-options). Click **Next**.
7. At the **Finish** step, set the job name and confirm the job information. Click **Submit**.
8. After the submission, you will be redirected to the **Job** page. You can start, modify, and delete the job.

```{note}
During restore, the data may be restored successfully while the job fails because the related configuration resources of the original service have conflicts. In this case, you need to create the service resources and modify the IP and port manually.
```

### Restore options

ADPS provides the following restore options:

- Advanced options

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
```

## Limitations

```{tabularcolumns} |\Y{0.20}|\Y{0.80}|
```
```{table} Limitations
---
class: longtable
---
| Feature  | Limitations  |
| ------------------ | ------------------------------------------------------------ |
|Backup|1. Cannot back up volumes mounted on block devices.{{ br }} 2. Can only back up workloads using Ceph-CSI/CephFS-CSI persistent volumes. {{ br }} 3. Cannot back up a whole cluster.{{ br }} 4. Do not support the NFS file system.|
|Environment|1. The kernel of the Ceph cluster must be later than 5.1.{{ br }} 2. Kubernetes version cannot be older than v1.17.0.{{ br }} 3. Ceph version must be later than v14.0.{{ br }} 4. Do not support IPv6 dual-stack networking.{{ br }} 5. LAN-free pools using iSCSI are supported.|
|Restore|1. Cannot restore the Ceph-CSI driver.{{ br }} 2. Cannot restore agent-related pods.|
```


## Glossary

```{tabularcolumns} |\Y{0.3}|\Y{0.7}|
```
```{table} Glossary
---
class: longtable
---
| Term | Description  |
|--------------------|-------------------------------|
|CustomResourceDefinition|Abbreviated as CRD. A claim for user to define the resource object.|
|namespace|Kubernetes (K8s) supports multiple virtual clusters which rely on the same physical cluster. These virtual clusters are called namespaces. By allocating resources to different namespaces, resources can be logically isolated and more easily managed for different uses. Resources in the same namespace must have unique names, while resources in different namespaces can share the same name.|
|Master|A node that is the main control component of K8s.|
|Node|A component in a K8s cluster which can be a physical or virtual machine. K8S|
|Pod|The smallest deployable units of computing that users can create and manage in Kubernetes. A Pod is a group of one or more containers.|
|Service|A method for exposing a network application that is running as one or more Pods in a cluster.|
|NodePort|A network service that is used to expose the application to the internet.|
|StorageClass|Provides a way for administrators to describe the classes of storage they offer.|
|VolumeSnapshotClass|Provides a way to describe the "classes" of storage when provisioning a volume snapshot.|
|VolumeSnapshotContent|A snapshot taken from a volume in the cluster that has been provisioned by an administrator. It is a resource in the cluster just like a PersistentVolume is a cluster resource.|
|VolumeSnapshot|A request for snapshot of a volume by a user. It is similar to a PersistentVolumeClaim.|
|PersistentVolume|Abbreviated as PV. A piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes.|
|PersistentVolumeClaim|Abbreviated as PVC. A request for storage by a user.|
|Secret|An object that contains a small amount of sensitive data such as a password, a token, or a key.|
|ConfigMap|An API object used to store non-confidential data in key-value pairs|
|ServiceAccount|Provides an identity for processes that run in a Pod, and maps to a ServiceAccount object.|
|LimitRange|A policy to constrain the resource allocations (limits and requests) that you can specify for each applicable object kind (such as Pod or PersistentVolumeClaim) in a namespace.|
|CSI|Container Storage Interface. A industry standard interface rule created by community members including Kubernetes, Mesos, and Docker. CSI is a standard for exposing arbitrary block and file storage systems to containerized workloads on Container Orchestration Systems (COs).|
|Clone|Creates a copy of the existing Kubernetes volumes. The copy can be used as any other standard volumes. Clone only supports CSI driver.|
|ReplicationController|Abbreviated as RC. It can create and manage a specified number of pod replicas.|
|ReplicaSet|Abbreviated as RS. It maintains a stable set of replica Pods running at any given time and can replace RC.|
|Deployment|Can be regarded as a superset of RC. In addition to providing functions such as Pod management, it also provides new features such as rollback and version recording. Generally, we do not create RC/RS directly, but create higher-level Deployment resources to automatically create RC/RS.|
|ApiServer|Provides HTTP Rest interfaces for creating, deleting, reading, updating, and monitoring various K8S resource objects (Pod, RC, Service, etc.), and is the data bus and data center of the entire system.ApiServer|
|metadata|This document uses metadata to define the resource data other than persistent volume storage data in namespaces.|
|DaemonSet|Ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created. |
|StatefulSet|Manages the deployment and scaling of a set of Pods and provides guarantees about the ordering and uniqueness of these Pods.|
|kubectl|The command line tool (CLI) of Kubernetes. It is the requied mangement tool for Kubernetes users and administrators.|
```