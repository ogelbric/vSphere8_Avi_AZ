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
Sup VM's (3) have the correct number of IPs assigned 4,4,5 so that is right.

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ok1.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ok2.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ok3.png)

## Lets check AVI for red stuff

Looks like the service engins are not selecting a vCenter cluster

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/AVISEG1.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/AVISEG2.png)

Found strange lincens errors in AVI

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/AVILic1.png)
![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/AVILic2.png)

Lets ssh to vCenter and then to supervisor cluster and check on AKO pod

```
ssh root@192.168.1.50
shell
/usr/lib/vmware-wcp/decryptK8Pwd.py

Cluster: domain-c8:054497f1-f9a5-4e84-87e2-173a09498aad
IP: 192.168.1.80
PWD: [N~Xt15,5]DK|V9h
------------------------------------------------------------

ssh root@192.168.1.80

k get pods -A                  # everthing is running good.... 
k get pods -A | grep -i AKO    # the AKO pod
k logs -n vmware-system-ako vmware-system-ako-ako-controller-manager-58fbd65b89-62j4j  # look at the log
k logs -n vmware-system-ako vmware-system-ako-ako-controller-manager-58fbd65b89-62j4j  -f  # look at running log
 
 ```

## AVI Final fix - added the workload to front route to the global VRF context

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/GlobalVRFContext1.png)

I should would have added the route to the front end network but global is attached by default

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Fix2.png)

```
 /usr/local/bin/kubectl-vsphere login --vsphere-username administrator@vsphere.local --server=https://192.168.4.71 --insecure-skip-tls-verify


Password: 
WARN[0003] Error occurred during HTTP request: Post https:///wcp/login: http: no Host in request URL 
ERRO[0003] Login failed: Post https:///wcp/login: http: no Host in request URL 
Logged in successfully.

You have access to the following contexts:
   192.168.4.71

If the context you wish to use is not in this list, you may need to try
logging in again later, or contact your cluster administrator.

To change context, use `kubectl config use-context <workload name>`
[root@centosrouter ~]# kubectl config use-context 192.168.4.71
Switched to context "192.168.4.71".
[root@centosrouter ~]# kubectl get pods -A
NAMESPACE                                   NAME                                                              READY   STATUS      RESTARTS        AGE
kube-system                                 coredns-6c4df6457c-82xbc                                          1/1     Running     0               154m
kube-system                                 coredns-6c4df6457c-dkr7v                                          1/1     Running     0               154m
kube-system                                 coredns-6c4df6457c-h66dn                                          1/1     Running     11 (145m ago)   154m
kube-system                                 docker-registry-423504fa3cc22672ae5940b795a73cc1                  1/1     Running     0               147m
kube-system                                 docker-registry-423529efa0c292d73ba45a30b7f9248b                  1/1     Running     0               153m
kube-system                                 docker-registry-423549790cec910c94b298df760c3e23                  1/1     Running     0               144m
kube-system                                 etcd-423504fa3cc22672ae5940b795a73cc1                             1/1     Running     0               148m
kube-system                                 etcd-423529efa0c292d73ba45a30b7f9248b                             1/1     Running     0               153m
kube-system                                 etcd-423549790cec910c94b298df760c3e23                             1/1     Running     0               145m
kube-system                                 kube-apiserver-423504fa3cc22672ae5940b795a73cc1                   1/1     Running     1 (142m ago)    145m
kube-system                                 kube-apiserver-423529efa0c292d73ba45a30b7f9248b                   1/1     Running     1 (142m ago)    144m
kube-system                                 kube-apiserver-423549790cec910c94b298df760c3e23                   1/1     Running     1 (142m ago)    145m
kube-system                                 kube-controller-manager-423504fa3cc22672ae5940b795a73cc1          1/1     Running     1 (143m ago)    147m
kube-system                                 kube-controller-manager-423529efa0c292d73ba45a30b7f9248b          1/1     Running     2 (147m ago)    153m
kube-system                                 kube-controller-manager-423549790cec910c94b298df760c3e23          1/1     Running     1 (146m ago)    147m
kube-system                                 kube-proxy-8n8gc                                                  1/1     Running     0               154m
kube-system                                 kube-proxy-r2qhl                                                  1/1     Running     0               145m
kube-system                                 kube-proxy-z2jwz                                                  1/1     Running     0               144m
kube-system                                 kube-scheduler-423504fa3cc22672ae5940b795a73cc1                   2/2     Running     1 (146m ago)    147m
kube-system                                 kube-scheduler-423529efa0c292d73ba45a30b7f9248b                   2/2     Running     2 (147m ago)    153m
kube-system                                 kube-scheduler-423549790cec910c94b298df760c3e23                   2/2     Running     0               147m
kube-system                                 kubectl-plugin-vsphere-423504fa3cc22672ae5940b795a73cc1           1/1     Running     3 (142m ago)    147m
kube-system                                 kubectl-plugin-vsphere-423529efa0c292d73ba45a30b7f9248b           1/1     Running     4 (141m ago)    154m
kube-system                                 kubectl-plugin-vsphere-423549790cec910c94b298df760c3e23           1/1     Running     3 (142m ago)    147m
kube-system                                 wcp-authproxy-423504fa3cc22672ae5940b795a73cc1                    1/1     Running     0               147m
kube-system                                 wcp-authproxy-423529efa0c292d73ba45a30b7f9248b                    1/1     Running     0               140m
kube-system                                 wcp-authproxy-423549790cec910c94b298df760c3e23                    1/1     Running     0               147m
kube-system                                 wcp-fip-423504fa3cc22672ae5940b795a73cc1                          1/1     Running     0               147m
kube-system                                 wcp-fip-423529efa0c292d73ba45a30b7f9248b                          1/1     Running     0               153m
kube-system                                 wcp-fip-423549790cec910c94b298df760c3e23                          1/1     Running     0               143m
svc-tmc-c8                                  tmc-agent-installer-27666562-rhsh8                                0/1     Completed   0               42s
vmware-system-ako                           vmware-system-ako-ako-controller-manager-58fbd65b89-62j4j         1/1     Running     1 (146m ago)    154m
vmware-system-appplatform-operator-system   kapp-controller-77b6f76d45-tpffk                                  2/2     Running     0               144m
vmware-system-appplatform-operator-system   vmware-system-appplatform-operator-mgr-0                          1/1     Running     0               154m
vmware-system-capw                          capi-controller-manager-85944789c7-6mct8                          2/2     Running     0               152m
vmware-system-capw                          capi-controller-manager-85944789c7-9lhvt                          2/2     Running     4 (146m ago)    152m
vmware-system-capw                          capi-controller-manager-85944789c7-r9wcd                          2/2     Running     0               152m
vmware-system-capw                          capi-kubeadm-bootstrap-controller-manager-58c5cfc5b9-q8fnh        2/2     Running     0               152m
vmware-system-capw                          capi-kubeadm-bootstrap-controller-manager-58c5cfc5b9-x8bc2        2/2     Running     4 (146m ago)    152m
vmware-system-capw                          capi-kubeadm-bootstrap-controller-manager-58c5cfc5b9-xxwx2        2/2     Running     0               152m
vmware-system-capw                          capi-kubeadm-control-plane-controller-manager-855568b448-f2fn7    2/2     Running     2 (146m ago)    152m
vmware-system-capw                          capi-kubeadm-control-plane-controller-manager-855568b448-kxltn    2/2     Running     0               152m
vmware-system-capw                          capi-kubeadm-control-plane-controller-manager-855568b448-scqdl    2/2     Running     0               152m
vmware-system-capw                          capv-controller-manager-5d5ffb7f64-fcfw8                          1/1     Running     3 (147m ago)    152m
vmware-system-capw                          capv-controller-manager-5d5ffb7f64-gw9rc                          1/1     Running     0               152m
vmware-system-capw                          capv-controller-manager-5d5ffb7f64-hc5rt                          1/1     Running     0               152m
vmware-system-capw                          capw-controller-manager-57d6fdbcd9-9d2xx                          2/2     Running     2 (147m ago)    152m
vmware-system-capw                          capw-controller-manager-57d6fdbcd9-ch8ts                          2/2     Running     0               152m
vmware-system-capw                          capw-controller-manager-57d6fdbcd9-httvr                          2/2     Running     0               152m
vmware-system-capw                          capw-webhook-65697b47bd-5jntb                                     2/2     Running     1 (144m ago)    152m
vmware-system-capw                          capw-webhook-65697b47bd-9qg8n                                     2/2     Running     0               152m
vmware-system-capw                          capw-webhook-65697b47bd-nkz6g                                     2/2     Running     0               152m
vmware-system-cert-manager                  cert-manager-6779666b47-5pxwr                                     1/1     Running     2 (145m ago)    154m
vmware-system-cert-manager                  cert-manager-cainjector-6d9bbf6f76-hfz5s                          1/1     Running     1 (146m ago)    154m
vmware-system-cert-manager                  cert-manager-webhook-559885c4b9-bdk9n                             1/1     Running     2 (146m ago)    154m
vmware-system-csi                           vsphere-csi-controller-848dfdbd6c-lkk8d                           6/6     Running     21 (143m ago)   151m
vmware-system-csi                           vsphere-csi-controller-848dfdbd6c-n7x24                           6/6     Running     6 (142m ago)    151m
vmware-system-csi                           vsphere-csi-controller-848dfdbd6c-sz5gh                           6/6     Running     1 (142m ago)    151m
vmware-system-csi                           vsphere-csi-webhook-84f76cc89b-gm6f2                              1/1     Running     0               151m
vmware-system-csi                           vsphere-csi-webhook-84f76cc89b-kqdcf                              1/1     Running     0               151m
vmware-system-csi                           vsphere-csi-webhook-84f76cc89b-r7mtj                              1/1     Running     0               151m
vmware-system-kubeimage                     image-controller-588856b898-g6x8v                                 1/1     Running     1 (150m ago)    154m
vmware-system-license-operator              vmware-system-license-operator-controller-manager-7bc9747bplftv   1/1     Running     0               151m
vmware-system-license-operator              vmware-system-license-operator-controller-manager-7bc9747bx2nhb   1/1     Running     0               151m
vmware-system-license-operator              vmware-system-license-operator-controller-manager-7bc9747bxn9j9   1/1     Running     0               151m
vmware-system-logging                       fluentbit-44r7x                                                   1/1     Running     0               147m
vmware-system-logging                       fluentbit-dzpp5                                                   1/1     Running     0               154m
vmware-system-logging                       fluentbit-j22qr                                                   1/1     Running     0               147m
vmware-system-netop                         vmware-system-netop-controller-manager-6665df4fbb-8vcwx           2/2     Running     3 (146m ago)    154m
vmware-system-netop                         vmware-system-netop-controller-manager-6665df4fbb-dmbhg           2/2     Running     0               154m
vmware-system-netop                         vmware-system-netop-controller-manager-6665df4fbb-hs85v           2/2     Running     0               154m
vmware-system-nsop                          vmware-system-nsop-controller-manager-545f5cf8b4-92t7r            1/1     Running     0               151m
vmware-system-nsop                          vmware-system-nsop-controller-manager-545f5cf8b4-f64p8            1/1     Running     0               151m
vmware-system-nsop                          vmware-system-nsop-controller-manager-545f5cf8b4-p4dc6            1/1     Running     4 (143m ago)    151m
vmware-system-pinniped                      pinniped-concierge-86dc54c4b7-4g5fk                               1/1     Running     0               141m
vmware-system-pinniped                      pinniped-concierge-86dc54c4b7-xkqfx                               1/1     Running     0               141m
vmware-system-pinniped                      pinniped-concierge-86dc54c4b7-ztstj                               1/1     Running     0               141m
vmware-system-pinniped                      pinniped-concierge-kube-cert-agent-6cd9dc84f5-f9xcn               1/1     Running     2 (33m ago)     141m
vmware-system-pinniped                      pinniped-supervisor-567bcc757c-8bzkm                              1/1     Running     0               141m
vmware-system-pinniped                      pinniped-supervisor-567bcc757c-9j85q                              1/1     Running     0               141m
vmware-system-pinniped                      pinniped-supervisor-567bcc757c-jptnd                              1/1     Running     0               141m
vmware-system-registry                      vmware-registry-controller-manager-556b44bf5f-rmv9l               2/2     Running     1 (142m ago)    154m
vmware-system-tkg                           masterproxy-tkgs-plugin-96frx                                     1/1     Running     0               141m
vmware-system-tkg                           masterproxy-tkgs-plugin-g7fsc                                     1/1     Running     0               141m
vmware-system-tkg                           masterproxy-tkgs-plugin-gnntc                                     1/1     Running     0               141m
vmware-system-tkg                           tanzu-addons-controller-manager-dfc9fdc88-7p4pm                   1/1     Running     0               142m
vmware-system-tkg                           tanzu-auth-controller-manager-788666998b-m7bmc                    1/1     Running     0               142m
vmware-system-tkg                           tanzu-capabilities-controller-manager-6cf5dc5ff5-vn849            1/1     Running     0               142m
vmware-system-tkg                           tanzu-featuregates-controller-manager-5c56bf7b56-tsrhj            1/1     Running     0               142m
vmware-system-tkg                           tkgs-plugin-server-7c8664bc5d-dc2mp                               1/1     Running     0               141m
vmware-system-tkg                           tkgs-plugin-server-7c8664bc5d-mj2hs                               1/1     Running     0               141m
vmware-system-tkg                           tkgs-plugin-server-7c8664bc5d-xwptl                               1/1     Running     0               141m
vmware-system-tkg                           tkr-conversion-webhook-manager-5b85dd74d8-t2b74                   1/1     Running     1 (40m ago)     142m
vmware-system-tkg                           tkr-resolver-cluster-webhook-manager-74cf98b999-tcmbs             1/1     Running     0               142m
vmware-system-tkg                           tkr-status-controller-manager-c7697b479-gqzxh                     1/1     Running     0               142m
vmware-system-tkg                           vmware-system-tkg-controller-manager-7f978db6c9-bdf2b             2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-controller-manager-7f978db6c9-dlfdn             2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-controller-manager-7f978db6c9-qxrwq             2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-state-metrics-75487495c5-fjvhg                  2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-state-metrics-75487495c5-r7sw4                  2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-state-metrics-75487495c5-tdh5s                  2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-webhook-548c575967-cf8zj                        2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-webhook-548c575967-ktmh7                        2/2     Running     0               141m
vmware-system-tkg                           vmware-system-tkg-webhook-548c575967-xnj8j                        2/2     Running     0               141m
vmware-system-ucs                           upgrade-compatibility-service-f8674555f-9f5nt                     1/1     Running     0               152m
vmware-system-ucs                           upgrade-compatibility-service-f8674555f-t9l55                     1/1     Running     0               152m
vmware-system-ucs                           upgrade-compatibility-service-f8674555f-wq4pb                     1/1     Running     0               152m
vmware-system-vmop                          vmware-system-vmop-controller-manager-7b9c46654-6djpq             2/2     Running     0               151m
vmware-system-vmop                          vmware-system-vmop-controller-manager-7b9c46654-846g4             2/2     Running     3 (147m ago)    151m
vmware-system-vmop                          vmware-system-vmop-controller-manager-7b9c46654-nqd6j             2/2     Running     0               151m
vmware-system-vmop                          vmware-system-vmop-hostvalidator-6d8467cbdf-nst8d                 1/1     Running     0               151m
[root@centosrouter ~]# 
```

### Things are green (left it overnight for the yellow/orange circles to turn green)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Green1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Green2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Green3.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Green4.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/Green5.png)


## WCP / Arcas Namespace creation

Using Arcas to set up namespace (read in previous arcas json file and add a few things to generate NS file)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns2.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns3.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns4.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns5.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns6.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/arcasns7.png)


### 
```
 arcas --env vsphere --file  /opt/vmware/arcas/src/vsphere-dvs-tkgs-namespace.json --create_supervisor_namespace
```
Unfortunately this results in an error and Arcas 1.3 is not ready for vCenter 8.0 so I have to set it up manually
Turns out it is partially set up and the only item missing are the permissions. 

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ns1.png)

![Version](https://github.com/ogelbric/vSphere8_Avi_AZ/blob/main/ns2.png)




