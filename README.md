# vCenter 8 with AVI (DVS) and multi AZ (Zones/vCenter Clusters)

Versions used in this nested environment:
```
Base System ESXi 6.7u3
Base vCenter 7.0.3
Nested ESXi 7.0.3d
Nested vCenter 8.0.0 Build 20183400 (some sort of alpha/beta version)
Service Installer (Arcas 1.3)
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
  



