# Fixing the ibm-cpp-config ConfigMap

If you run into problem with the ibm-cpp-config ConfigMap missing you can add it manually.  
Use this as a Template for the ibm-cpp-config.yaml file.  

```yaml
kind: ConfigMap
apiVersion: v1
metadata:
  name: ibm-cpp-config
  namespace: ibm-common-services
  labels:
    ibm-cpp-config/ibm-common-services: secretsharekey
    ibmcpcs.ibm.com/managed-by: secretshare
    manage-by-secretshare: 'true'
data:
  storageclass.default: default
  storageclass.list: >-
    localblock,ocs-storagecluster-ceph-rbd,ocs-storagecluster-cephfs,sat-ocs-cephfs-gold,sat-ocs-cephfs-gold-metro,sat-ocs-cephrbd-gold,sat-ocs-cephrbd-gold-metro,sat-ocs-cephrgw-gold,sat-ocs-noobaa-gold
```

List the available StorageClasses,  
`oc get StorageClasses`

The output will depend on your cluster here are two examples,  

```
[fredrik@fedora ~]$ oc get StorageClasses
NAME                 PROVISIONER   RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
lvms-vg1 (default)   topolvm.io    Delete          WaitForFirstConsumer   true                   55d
[fredrik@fedora ~]$
```

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

Update the list in the yaml file and then apply it,  

```
[fredrik@fedora Documents]$ oc apply -f ibm-cpp-config.yaml
configmap/ibm-cpp-config configured
[fredrik@fedora Documents]$ 
```