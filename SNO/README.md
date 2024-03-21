# Single Node Openshift (SNO) cluster

A Single Node Openshift cluster with 16 CPUs, 64G memory and 300 Gig diskspace can support one Maximo Application Suite and a smaller Manage install.  
In this case we can set parameters to have a smaller install than a full cluster.  

## Installing Maximo Application Suite

To install MAS we need the following parameters set in the pod.  

```
export IBM_ENTITLEMENT_KEY=[Your IBM entitlement key]
export STORAGE_CLASS_BLOCK=sat-ocs-cephrbd-gold
export STORAGE_CLASS_FILE=sat-ocs-cephfs-gold

export MAS_INSTANCE_ID=[masinstanid]
export MAS_CONFIG_DIR=~/masdir
export SLS_LICENSE_FILE=/opt/app-root/src/masconfig/license.dat
export UDS_CONTACT_EMAIL=fredrik.safstrom@epicalgroup.com
export UDS_CONTACT_FIRSTNAME=Fredrik
export UDS_CONTACT_LASTNAME=Safstrom
export UDS_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export PROMETHEUS_ALERTMGR_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export PROMETHEUS_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export PROMETHEUS_USERWORKLOAD_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export GRAFANA_INSTANCE_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export MONGODB_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export MONGODB_REPLICAS=1
```

The storage classes need to match the correct Storage classes in the cluster, I have included the ones we use for the Fusion box, this usually varies between clusters though.  

You can list the available StorageClasses if needed,  
`oc get StorageClasses`

The output will depend on your cluster here is one example,  

```
[fredrik@fedora ~]$ oc get StorageClasses
NAME                          PROVISIONER                             RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
localblock                    kubernetes.io/no-provisioner            Delete          WaitForFirstConsumer   false                  34d
ocs-storagecluster-ceph-rbd   openshift-storage.rbd.csi.ceph.com      Delete          Immediate              true                   34d
ocs-storagecluster-cephfs     openshift-storage.cephfs.csi.ceph.com   Delete          Immediate              true                   34d
sat-ocs-cephfs-gold           openshift-storage.cephfs.csi.ceph.com   Delete          Immediate              true                   34d
sat-ocs-cephfs-gold-metro     openshift-storage.cephfs.csi.ceph.com   Delete          WaitForFirstConsumer   true                   34d
sat-ocs-cephrbd-gold          openshift-storage.rbd.csi.ceph.com      Delete          Immediate              true                   34d
sat-ocs-cephrbd-gold-metro    openshift-storage.rbd.csi.ceph.com      Delete          WaitForFirstConsumer   true                   34d
sat-ocs-cephrgw-gold          openshift-storage.ceph.rook.io/bucket   Delete          Immediate              false                  34d
sat-ocs-noobaa-gold           openshift-storage.noobaa.io/obc         Delete          Immediate              false                  34d
[fredrik@fedora ~]$ 
```

The MAS_INSTANCE_ID can be any name, use lower case letters and short names. I don’t know the maximum length for this parameter. Same thing with MAS_WORKSPACE_ID, keep it reasonable long.  
Edit and copy all the exports to your pod.  
When ready, run the following command to install MAS,  
`ansible-playbook ibm.mas_devops.oneclick_core `

Somewhere in the log you are going to see something like,
```
  - Maximo Application Suite is Ready, use the superuser credentials to authenticate
  - Admin Dashboard ... https://admin.fusionmas.roks4max2-3a46a35ebf0515fbaca1e87e4fcb2122-0000.eu-de.containers.appdomain.cloud
  - Username .......... PlMmwPiF98Y2tsR0Uki3c4FVa78N58bX
  - Password .......... J5eKizPiigvcMOPq8gIpgwSfydCWxBzq
```

Save this information, this is used to log into MAS.

## Installing Manage

For manage we have a few more parameters to set,

```
export CPD_BLOCK_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export CPD_ENTITLEMENT_KEY=$IBM_ENTITLEMENT_KEY
export CPD_STORAGE_CLASS=$STORAGE_CLASS_FILE
export CPD_OPERATORS_NAMESPACE=ibm-cpd-operators
export CPD_INSTANCE_NAMESPACE=ibm-cpd
export CPD_INSTALL_COGNOS="false"
export CPD_INSTALL_WSL="false"
export DB2_CHANNEL=v110508.0
export DB2_ACTION_SYSTEM=none
export DB2_ACTION_MANAGE=install
export DB2_NAMESPACE=db2u
export DB2_SCHEMA=maximo
export DB2_META_STORAGE_ACCESSMODE=ReadWriteOnce
export DB2_BACKUP_STORAGE_ACCESSMODE=ReadWriteOnce
export DB2_LOGS_STORAGE_ACCESSMODE=ReadWriteOnce
export DB2_DATA_STORAGE_ACCESSMODE=ReadWriteOnce
export DB2_CPU_REQUESTS=300m
export DB2_CPU_LIMITS=6000m
export DB2_MEMORY_REQUESTS=8Gi
export DB2_MEMORY_LIMITS=12Gi
export DB2_META_STORAGE_CLASS=$STORAGE_CLASS_FILE
export DB2_META_STORAGE_SIZE=10Gi
export DB2_BACKUP_STORAGE_CLASS=$STORAGE_CLASS_FILE
export DB2_BACKUP_STORAGE_SIZE=10Gi
export DB2_LOGS_STORAGE_CLASS=$STORAGE_CLASS_FILE
export DB2_LOGS_STORAGE_SIZE=10Gi
export DB2_TEMP_STORAGE_CLASS=$STORAGE_CLASS_FILE
export DB2_TEMP_STORAGE_SIZE=10Gi
export DB2_DATA_STORAGE_CLASS=$STORAGE_CLASS_FILE
export DB2_DATA_STORAGE_SIZE=20Gi

export MAS_INSTANCE_ID=[masinstanid]
export MAS_WORKSPACE_ID=[workspaceid]
export MAS_CONFIG_DIR=~/masdir
export MAS_APPWS_COMPONENTS="base=latest"
export MAS_APP_ID=manage
export MAS_APP_SETTINGS_DEMODATA=true
export MAS_APP_SETTINGS_TABLESPACE=MAXDATA
export MAS_APP_SETTINGS_INDEXSPACE=MAXINDEX
export MANAGE_AIO_FLAG=false
```

This will install Manage, I have specified the DEMODATA parameter to be true since I want demo data to be created in the database. Set this to false if you do not want the demo data. 
When you are ready, run the following command,
`ansible-playbook ibm.mas_devops.oneclick_add_manage`

This will add Manage to your MAS instance.