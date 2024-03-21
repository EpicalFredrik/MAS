# Fixing problems with Images not being accessible

If you notice that the install hangs and see that pods are not created since the Image can not be pulled, you must add a few registries to the config.openshift.io configuration.  
The official documentation is here,  
https://docs.openshift.com/container-platform/4.13/openshift_images/image-configuration.html
In the Pod that runs MASCLI, connect to the OpenShift cluster if not already done so.  
Type the following,  
`oc edit image.config.openshift.io/cluster`  

Under “spec:”, add the following:  
``spec:  
  allowedRegistriesForImport:  
    - domainName: quay.io  
      insecure: false  
  registrySources:  
    allowedRegistries:  
      - icr.io/cpopen  
      - icr.io  
      - quay.io  
      - registry.redhat.io  
      - 'image-registry.openshift-image-registry.svc:5000'  
      - registry.connect.redhat.com  
      - cp.icr.io  


If there already is an entry for any of these, add it. Otherwise, you could just switch out the whole spec: section.  
Save and wait a couple of minutes for the update to be done.
