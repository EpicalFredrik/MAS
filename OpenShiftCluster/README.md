# OpenShift Cluster

I’m not going to give any specifications; it varies depending on the setup. For example, we have cluster with 96 CPU, 375 GB memory and 1.7 TB disk for MAS, Manage, IOT, Monitor and Cognos.  


For MAS, we use the same parameters but remove the MongoDB_Replicas parameter.  

## Installing Maximo Application Suite
```
export IBM_ENTITLEMENT_KEY=[Your IBM entitlement key]
export STORAGE_CLASS_BLOCK=sat-ocs-cephrbd-gold
export STORAGE_CLASS_FILE=sat-ocs-cephfs-gold

export MAS_INSTANCE_ID=[masinstanid]
export MAS_WORKSPACE_ID=[workspaceid]
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

## Installing IoT


Before we install Manage and Monitor, we can install IoT.
IoT is needed for Monitor but not manage, if you plan to use Monitor, do not skip this step.
There are not that many parameters needed,

```
export MAS_APP_SETTINGS_IOT_FPL_PVC_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export MAS_APP_SETTINGS_IOT_DEPLOYMENT_SIZE=small
```

Make sure you have the previous parameters set, if you close your Pod you might want to run the previous exports again.
The deployment size can be dev, small or large.
When ready, run the following command to install IoT,

`ansible-playbook ibm.mas_devops.oneclick_add_iot`

## Installing Monitor

Monitor is not needed for Manage but as I mentioned above, you need to have installed IoT before installing Monitor.
If you specified all the above parameters, there is only one new export,

`export MAS_APP_SETTINGS_MONITOR_DEPLOYMENT_SIZE=dev`

The deployment size can be dev, small or large.
When ready, run the following command to install Monitor,

`ansible-playbook ibm.mas_devops.oneclick_add_monitor`


## Installing Manage

We don’t need that many parameters, in this case we are going to let the install dimension the database for us so we do not need the database parameters.

```
export CPD_BLOCK_STORAGE_CLASS=$STORAGE_CLASS_BLOCK
export CPD_ENTITLEMENT_KEY=$IBM_ENTITLEMENT_KEY
export CPD_STORAGE_CLASS=$STORAGE_CLASS_FILE
export CPD_OPERATORS_NAMESPACE=ibm-cpd-operators
export CPD_INSTANCE_NAMESPACE=ibm-cpd
export CPD_INSTALL_COGNOS="false"
export CPD_INSTALL_WSL="false"

export MAS_INSTANCE_ID=[masinstanid]
export MAS_WORKSPACE_ID=[workspaceid]
export MAS_CONFIG_DIR=~/masdir
export MAS_APPWS_COMPONENTS="base=latest,health=latest"
export MAS_APP_ID=manage
export MAS_APP_SETTINGS_DEMODATA=true
export MAS_APP_SETTINGS_TABLESPACE=MAXDATA
export MAS_APP_SETTINGS_INDEXSPACE=MAXINDEX
export MANAGE_AIO_FLAG=false
```

Here we will install Manage and Health, if you do not want Health, remove it from the MAS_APPWS_COMPONENTS. The MANAGE_AIO_FLAG is set to false since I don’t care for Asset Investment Optimizer, I have left it to false.  
If you want to AIO, you can set this to true and install it later. I also set the flag for demo data, this is optional.  

When you are ready, run the following command,  

`ansible-playbook ibm.mas_devops.oneclick_add_manage`
