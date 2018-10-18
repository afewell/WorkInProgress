# NSX-T 2.3 Installation

## Overview

The following installation guide follows the implementation of a functional NSX-T 2.3 Installation configured for PKS 1.2 in a vSphere nested lab environment. This implementation uses variables that function in the lab environment. Anyone is welcome to build a similar lab environment and follow along with the lab exercises, but please note you will need to replace any variables such as IP addresses and FQDNs and replace them with the appropriate values for your lab environment.

The steps provided in this lab guide are intended for a lab implementation and do not necessarily align with best practices for production implementiations. While the instructions provided in this lab guide did work for the author in their lab environment, VMware and/or any contributors to this Guide provide no assurance, warranty or support for any content provided in this guide.

## Installation Notes

Anyone who implements any software used in this lab must provide their own licensing and ensure that their use of all software is in accordance with the software's licensing. This guide provides no access to any software licenses.

For those needing access to VMware licensing for lab and educational purposes, we recommend contacting your VMware account team. Also, the [VMware User Group's VMUG Advantage Program](https://www.vmug.com/Join/VMUG-Advantage-Membership) provides a low-cost method of gaining access to VMware licenses for evaluation purposes.

### NSX-T

This lab follows the standard documentation, which includes additional details and explanations: [NSX-T 2.3 Installation Guide](https://docs.vmware.com/en/VMware-NSX-T/2.2/com.vmware.nsxt.install.doc/GUID-3E0C4CEC-D593-4395-84C4-150CD6285963.html)

#### Install NSX-T Manager

Overview of Tasks Covered in Lab 1

- Step1: Deploy NSX Manager (= NSX Unified Appliance)
- Step 2: Deploy NSX Controllers
- Step 3: Create NSX Cluster (Management Plane and Cluster Control Plane)
- Step 4: Deploy NSX Edges
- Step 5: Register NSX Edge with NSX Manager
- Step 6:  Enable repository service on NSX Manager
- Step 7: Create TEP IP Pool
- Step 8: Create Transport Zone Overlay (TZ-Overlay)
- Step 9: Create Transport Zone for VLAN (TZ-VLAN)
- Step 10: Create Uplink Profile for Edge Node
- Step 11: Create Edge Transport Node
- Step 12: Create Edge Cluster
- Step 13: Create T0 router

NOTE: NSX Manager OVA cannot be installed via HTML5 client, so for installation labs please use the vSphere web client (Flash-based).

This section follows the standard documentation, which includes additional details and explanations: [NSX Manager Installation](https://docs.vmware.com/en/VMware-NSX-T/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html)

### Step 1:  Deploy NSXT Manager using OVF Install Wizard

1.1 In the vSphere web client (not the HTML5 Client), From the Hosts and Clusters view, right click on the RegionA01-MGMT01 Cluster and select `Deploy OVF Template'

<Details><Summary>Screenshot 1.1</Summary>
<img src="Images/2018-10-16-23-43-44.png">
</Details>
<br/>

1.2 On the `Select Template` step, select `Local File` and Navigate to the NSXT Manager OVA file. In the reference lab, this is located on the E:/Downloads Directory

<details><summary>Screenshot 1.2</summary>
<img src="Images/2018-10-16-23-48-52.png">
</details>
<br/>

1.3 On the `Select name and location` step, use the name `nsxmgr-01a` and select RegionA01 Datacenter as the location

<details><summary>Screenshot 1.3</summary>
<img src="Images/2018-10-17-00-06-33.png">
</details>
<br/>

1.4 On the `Select a Resource` step, select RegionA01-MGMT

<details><summary>Screenshot 1.4</summary>
<img src="Images/2018-10-16-23-54-05.png">
</details>
<br/>

1.5 On the `Review Details` step, verify details and click `Next`

<details><summary>Screenshot 1.5</summary>
<img src="Images/2018-10-16-23-55-58.png">
</details>
<br/>

1.6 On the `Select Configuration` step, select a Small Configuration

<details><summary>Screenshot 1.6</summary>
<img src="Images/2018-10-16-23-57-38.png">
</details>
<br/>

1.7 On the `Select Storage` step, set the virtual disk format to `Thin Provision` and select the `RegionA01-ISCSI01-COMP01` datastore

<details><summary>Screenshot 1.7</summary>
<img src="Images/2018-10-16-23-59-52.png">
</details>
<br/>

1.8 On the `Select Networks` step, set the Destination Network to `VM-RegionA01-vDS-MGMT

<details><summary>Screenshot 1.8</summary>
<img src="Images/2018-10-17-00-02-21.png">
</details>
<br/>

1.9 On the `Customize Template` step, enter the following variables:

- System Root User Password: VMware1!
- CLI Admin User Password: VMware1!
- CLI Audit User Password: VMware1!
- Hostname: nsxmgr-01a
- Rolename: nsx-manager
- Default Gateway: 192.168.110.1
- Management Network IPv4 Address: 192.168.110.42
- Management Network Netmask: 255.255.255.0
- DNS Server List: 192.168.110.10
- Domain Search List: corp.local
- NTP Server: 192.168.100.1
- Enable SSH: True
- Allow Root SSH Logins: True
- All other options were left as default values

<details><summary>Screenshot 1.8</summary>
<img src="Images/2018-10-17-00-12-14.png">
</details>
<br/>

1.9 Complete the Deploy OVF Template Wizard

<details><summary>Screenshot 1.9</summary>
<img src="Images/2018-10-17-00-13-27.png">
</details>
<br/>

1.10 In the vSphere web client go to the task console and verify that the Status for Deploy OVF Template is "Completed" before proceeding
<br/>

<details><summary>Screenshot 1.10</summary>
<img src="Images/2018-10-17-00-51-10.png">
</details>
<br/>

1.11 In the vSphere web client go to Hosts and Clusters and on the `Actions` menu for `nsxmgr-01a`, select `Edit Settings`

<details><summary>Screenshot 1.11</summary>
<img src="Images/2018-10-17-02-19-08.png">
</details>
<br/>

1.12 Expand the `Memory` section and set the `Reservation` to `0`

NOTE: This step is provided to help limit the requirements for resource constrained lab environments, if your lab environment has ample hardware resources, you may skip this step

<details><summary>Screenshot 1.12</summary>
<img src="Images/2018-10-17-02-22-04.png">
</details>
<br/>

1.13 In the vSphere web client power on the nsxmgr-01a VM, wait a few minutes for NSX Manager to fully boot up before proceeding to the next step

NOTE: If the option to power on the nsxmgr-01a VM is not available, log out and then log back in to the vSphere web client

<details><summary>Screenshot 1.13</summary>
<img src="Images/2018-10-17-01-23-08.png">
</details>
<br/>

1.14 Using the IP address you assigned to NSX Manager in the Deploy OVF Template Wizard, open a web browser connection to NSX Manager, for example:

`https://nsxmgr-01a.corp.local/login.jsp`

<br/>
NOTE: On your first login, you will be prompted to accept the EULA
<br/>
<br/>

<details><summary>Screenshot 1.14</summary>
<img src="Images/2018-10-17-01-34-33.png">
</details>
<br/>
This completes the NSX Manager installation, please proceed on to the Controller installation section below

#### 2.0 NSX-T Controller Installation

2.1 In the vSphere web client (not the HTML5 Client), From the Hosts and Clusters view, right click on the RegionA01-MGMT01 Cluster and select `Deploy OVF Template'

<details><summary>Screenshot 2.1</summary>
<img src="Images/2018-10-16-23-43-44.png">
</details>
<br/>

2.2 On the `Select Template` step, select `Local File` and navigate to the NSX Controller OVA file

<details><summary>Screenshot 2.2</summary>
<img src="Images/2018-10-17-01-49-19.png">
</details>
<br/>

2.3 On the `Select name and location` step, enter `nsxc-01a` as the name and select `RegionA01` as the datacenter

<details><summary>Screenshot 2.3</summary>
<img src="Images/2018-10-17-01-51-42.png">
</details>
<br/>

2.4 On the `Select a resource` step, select the `RegionA01-MGMT01` cluster

<details><summary>Screenshot 2.4</summary>
<img src="Images/2018-10-17-01-53-43.png">
</details>
<br/>

2.5 On the `Review details` step, review the details and click `Next`

<details><summary>Screenshot 2.5</summary>
<img src="Images/2018-10-17-01-56-05.png">
</details>
<br/>

2.6 In the NSX Manager web UI, expand the System panel, select the "Components" page, and select "Add Controllers"

<details><summary>Screenshot 2.6</summary>
<img src="Images/2018-10-17-01-56-56.png">
</details>
<br/>

2.7 On the `Select Configuration` step, select a `Small` configuration

<details><summary>Screenshot 2.7</summary>
<img src="Images/2018-10-17-01-58-35.png">
</details>
<br/>

2.8 On the `Select Storage` step, select `Thin Provision` as the virtual disk format and `RegionA01-ISCSI01-COMP01` as the datastore

<details><summary>Screenshot 2.8</summary>
<img src="Images/2018-10-17-02-00-58.png">
</details>
<br/>

2.9 On the `Select networks` step, set the `Destination Network` to `VM-RegionA01-vDS-MGMT`

<details><summary>Screenshot 2.9</summary>
<img src="Images/2018-10-17-02-02-54.png">
</details>
<br/>

2.10 On the `Customize template` step, enter the following details:

- CLI Admin User Password: VMware1!
- CLI Audit User Password: VMware1!
- System Root User Password: VMware1!
- DNS Server List: 192.168.110.10
- Domain Search List: corp.local
- Default Gateway: 192.168.110.1
- Hostname: nsxc-01a
- Management IP: 192.168.110.31
- Management Netmask: 255.255.255.0
- Allow root SSH logins: True
- Enable SSH: True
- NTP Server List: 192.168.100.1
- Use defaults for any parameters that arent specified

<details><summary>Screenshot 2.10</summary>
<img src="Images/2018-10-17-02-11-04.png">
</details>
<br/>

2.11 On the `Ready to complete` step, review details and click `Finish`. 

<details><summary>Screenshot 2.11</summary>
<img src="Images/2018-10-17-02-13-04.png">
</details>
<br/>

2.12 In the vSphere web client, monitor the OVF deployment on the `Tasks` or `Recent Tasks` screen and wait for the status of the `Deploy OVF template` task to change to `Completed` befor proceeding

<details><summary>Screenshot 2.12</summary>
<img src="Images/2018-10-17-02-35-17.png">
</details>
<br/>

2.13 In the vSphere web client, power on the nsxc-01a VM and wait a few minutes for the controller to fully boot up before proceeding

<details><summary>Screenshot 2.13</summary>
<img src="Images/2018-10-17-02-38-37.png">
</details>
<br/>

2.14 SSH to the controller VM and login to verify deployment

<details><summary>Screenshot 2.14</summary>
<img src="Images/2018-10-17-03-12-04.png">
</details>
<br/>

#### Step 3: Create NSX Cluster (Management Plane & Cluster Control Plane)

3.1 Open and SSH or console session with NSX Manager and gather the API thumbrint with the command `get certificate api thumbprint`

<details><summary>Screenshot 3.1</summary>
<img src="Images/2018-10-17-16-19-50.png">
</details>
<br/>

3.2 Open and SSH or console session with the NSX Controller(s) and enter the following command using the NSX Manager thumbprint from your environment to join the controller to the managment cluster:

`join-management-plane 192.168.110.42 thumbprint ef409e15750f89be5b88b141201ddd35179b7e6af86bf08a2683c0c3f3a58557 username admin password VMware1!`

<details><summary>Screenshot 3.2</summary>
<img src="Images/2018-10-17-16-26-35.png">
</details>
<br/>

3.3 From the NSX Controller CLI enter the following command to configure the control cluster shared secret:

`set control-cluster security-model shared-secret secret VMware1!`

<details><summary>Screenshot 3.3</summary>
<img src="Images/2018-10-17-20-54-46.png">
</details>
<br/>

3.4 From the NSX Controller CLI intialize the control cluster with the command `initialize control-cluster`

<details><summary>Screenshot 3.4</summary>
<img src="Images/2018-10-17-21-01-49.png">
</details>
<br/>

3.5 From the NSX Controller CLI activate the control cluster with the command `activate control-cluster`

<details><summary>Screenshot 3.5</summary>
<img src="Images/2018-10-17-21-03-57.png">
</details>
<br/>

3.6 From the NSX Controller CLI confirm connectivity to the NSX Manager with the command `get managers`

<details><summary>Screenshot 3.6</summary>
<img src="Images/2018-10-17-21-07-04.png">
</details>
<br/>

3.7 From the NSX Controller CLI check control cluster details and status with the command `get control-cluster status`

<details><summary>Screenshot 3.7</summary>
<img src="Images/2018-10-17-21-14-09.png">
</details>
<br/>

3.8 Resume your SSH session with NSX Manager and check the management cluster status with the command `get management-cluster status`

<details><summary>Screenshot 3.8</summary>
<img src="Images/2018-10-17-22-12-17.png">
</details>
<br/>

3.9 Open a browser session, log in to the NSX Manager UI and select System > Components to monitor the management and control cluster status

<details><summary>Screenshot 3.9</summary>
<img src="Images/2018-10-17-22-23-30.png">
</details>
<br/>

#### Step 4: Deploy NSX Edges

4.1 Open a browser to the NSX Manager web UI and select Fabric > Nodes > Edges > Add Edge VM

<details><summary>Screenshot 4.1</summary>
<img src="Images/2018-10-10-01-45-24.png">
</details>
<br/>

4.2 In the Add Edge VM dialogue box, on the Name and Description tab, enter the following variables:

- Name: nsx-edge
- Host Name/FQDN: nsx-edge.corp.local
- Form Factor: Large

<details><summary>Screenshot</summary>
<img src="Images/2018-10-10-01-57-27.png">
</details>

4.3 In the Add Edge VM dialogue box, on the Credentials tab, enter the following variables:

- CLI Password: VMware1!
- System Root Password: VMware1!

<details><summary>Screenshot 4.3</summary>
<img src="Images/2018-10-10-02-00-54.png">
</details>

4.4 In the Add Edge VM dialogue box, on the Configure Deployment tab, enter the following variables:

- Compute Manager: vcsa-01a.corp.local
- Cluster: RegionA01-MGMT01
- Resource Pool: Null
- Host: Null
- Datastore: RegionA01-ISCS01-COMP01

<details><summary>Screenshot 4.4</summary>
<img src="Images/2018-10-10-02-03-25.png">
</details>

4.5 In the Add Edge VM dialogue box, on the Configure Ports tab, enter the following variables:

- Ip Assignment: Static
- Management IP: 192.168.100.112/24
- Default Gateway: 192.168.100.1
- Management Interface: VM-RegionA01-vDS-MGMT
- Datapath Interface #1: VM-RegionA01-vDS-MGMT

<details><summary>Screenshot 4.5</summary>
<img src="Images/2018-10-10-02-10-03.png">
</details>
<br/>

4.6 Wait for the deployment to complete, your deployment status may say "Power On Failed" if you exceed the memory allocation of your management cluster. Whether you recieved this error or not, it is a good practice in lab environments only to reduce the memory reservation on the nsx-edge VM to limit resource requirements for lab environments

4.7 In the vSphere web client (flash-based), edit the settings of the nsx-edge VM so there is no memory reservation
<details><summary>Screenshot 4.7</summary>
<img src="Images/2018-10-10-02-42-22.png">
</details>
<br/>

4.7.1 If your nsx-edge VM is not powered on, power it on now

4.8 Return to the Fabric > Nodes > Edges screen in the NSX Manager web UI, verify the deployment status is "Node Ready" and the Manager Connection is "Up" before proceeding. This may take several minutes after powering on the Edge VM

<details><summary>Screenshot 4.8</summary>
<img src="Images/2018-10-10-02-45-21.png">
</details>

#### NSX-T Host Preparation

This section follows the standard documentation, which includes additional details and explanations: [Host Preparation](https://docs.vmware.com/en/VMware-NSX-T/2.2/com.vmware.nsxt.install.doc/GUID-FCC5390E-3489-47E8-ABE6-2F7FD43775BD.html)

4.0 Add compute cluster ESXi hosts to the NSX-T fabric

4.1 

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>

<details><summary>Screenshot</summary>

</details>







<details><summary>Screenshot</summary>

</details>