# vCenter 8 with AVI (DVS) and multi AZ (Zones/vCenter Clusters)

Versions used in this nested environment:
```
Base System ESXi 6.7u3
Base vCenter 7.0.3
Nested ESXi 7.0.3d
Nested vCenter 8.0.0 Build 20183400 (some sort of alpha/beta version) (sorry internal right now only build web)
Service Installer (Arcas 1.3) (https://marketplace.cloud.vmware.com/services/details/service-installer-for-vmware-tanzu-1?slug=true)
AVI 22.1.1-9052 (https://portal.avipulse.vmware.com/software/vantage)
```

## What is covered in this write up:
```
The nested test environment is built via William Lam (https://williamlam.com/nested-virtualization) Power Shell script
  Deploys one vCenter
  Deploys 9 ESXi server into 3 vCenter clusters with vSAN and Networks
Arcas (Service Installer)
  Deploys AVI (some what since Arcas 1.3 is not totaly ready for vCenter 8)
AVI set up 
  Done by hand 
WCP enablement 
  Done by hand
Workload cluster
  Done by hand 
```

### The Nested deploy via William Lams Power Shell Script

Here is the script modified for 3 vCenter clusters and 9 ESXi hosts

  https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/PSAutomationScript


Script kick off: 

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/kickoff1.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Kickoff2.png)

Result:

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/vCenterOutcome1.png)

### Make sure datastores are tagged

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/tag1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/tag2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/tag2.png)

### Make sure content library is creatged with AVI image

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/cl1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/cl2.png)

### Service Installer (Arcas) deployment

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcas1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcas2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcas3.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcas4.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcas5.png)

### Start ova and go to IP of Arcas and port 8888

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar3.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar4.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar5.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar6.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar7.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar8.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar9.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar10.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar11.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar12.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar13.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar14.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar15.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ar16.png)







### Save ARCAS json installer file to the Arcas VM
```
/opt/vmware/arcas/src/vsphere-dvs-tkgs-wcp.json
```
 
### ssh to arcas vm as root (ssh root@192.168.1.39) and run the AVI install
```
arcas --env vsphere --file  /opt/vmware/arcas/src/vsphere-dvs-tkgs-wcp.json --avi_configuration
```
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arr1.png)




