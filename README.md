# vCenter 8 with AVI (DVS) and multi AZ (Zones/vCenter Clusters)

Versions used in this nested environment:
```
Base System ESXi 6.7u3
Base vCenter 7.0.3
Nested ESXi 7.0.3d
Nested vCenter 8.0.0 Build 20183400 (some sort of alpha/beta version) (sorry internal right now only on build web)
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

## The Nested deploy via William Lams Power Shell Script

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

## Service Installer (Arcas) deployment

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

### Small problem.... 

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arrp1.png)

### Update the version 
```
Before:
grep "20.1.7" /opt/vmware/arcas/src/common/operation/constants.py
    AVI_VERSION_UPDATE_THREE = "20.1.7"
    AVI_VERSION_UPDATE_TWO = "20.1.7"

vi /opt/vmware/arcas/src/common/operation/constants.py

After:
grep "21.1.1" /opt/vmware/arcas/src/common/operation/constants.py
    VSPHERE_AVI_VERSION = "21.1.1"
    VMC_AVI_VERSION = "21.1.1"
    AVI_VERSION_UPDATE_THREE = "21.1.1"
    AVI_VERSION_UPDATE_TWO = "21.1.1"

arcas --env vsphere --file  /opt/vmware/arcas/src/vsphere-dvs-tkgs-wcp.json --cleanup

systemctl restart arcas

arcas --env vsphere --file  /opt/vmware/arcas/src/vsphere-dvs-tkgs-wcp.json --avi_configuration
```
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arrp2.png)

FYI - this will still result in an API error, but AVI is deployed and now needs to be set up (if Arcas would support 21.x.x then this would not be an issue) 

Example of Arcas 1.3 json file: https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/vsphere-dvs-tkgs-wcp.json


## AVI setup

One way to tell that Arcas was unable to talk to the API is that the admin password is not set on the AVI appliance. 

### AVI password

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi3.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi4.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi5.png)

Note default cloud is green by default - not connected to anything and AKO pod in Tanzu will be looking for default-cloud

### AVI Setup Change the default cloud type

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi6.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi7.png)

### AVI default-cloud setup 

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi8.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi9.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi10.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi11.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi12.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi13.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi14.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi15.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi16.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi17.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi18.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi19.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi20.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi21.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi22.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi23.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi24.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi25.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi26.png)

### AVI cert setup 

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi27.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi28.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi29.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi30.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi31.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi32.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi33.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi34.png)

Cert for WCP enablement

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/avi35.png)

## WCP enablement with zones

Set up Zones
Make sure storage policy has zonal
WCP enablement 

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp3.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp4.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp5.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp6.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp7.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp8.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp9.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp10.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp11.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp12.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp13.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp14.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp15.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp16.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp17.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp18.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp19.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp20.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp21.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp22.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/wcp23.png)

## WCP log in to API end point

```
/usr/local/bin/kubectl-vsphere login --vsphere-username administrator@vsphere.local --server=https://192.168.4.71 --insecure-skip-tls-verify
```

Problem can't log in... 
Trouble shooting starts...
Can I ping 192.168.4.71 -> no  hmmm is AVI green? Do I have SE's?  Lets check! 

No Se's found in vCenter - that is a problem! (There should be 2) 
Sup VM's (3) have the correct number of IPs assigned 4,4,5

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ok1.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ok2.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ok3.png)

Lets check AVI for red stuff






