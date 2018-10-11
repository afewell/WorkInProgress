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

NOTE: NSX Manager OVA cannot be installed via HTML5 client, so for installation labs please use the vSphere web client (Flash-based).

This section follows the standard documentation, which includes additional details and explanations: [NSX Manager Installation](https://docs.vmware.com/en/VMware-NSX-T/2.2/com.vmware.nsxt.install.doc/GUID-A65FE3DD-C4F1-47EC-B952-DEDF1A3DD0CF.html)

1.0 Install NSX-T Manager OVA File using OVF Install Wizard

<details><summary>Screenshot 1.1</summary>
<img src="Images/2018-10-09-13-28-29.png">
</details>

<details><summary>Screenshot 1.2</summary>
<img src="Images/2018-10-09-13-29-24.png">
</details>

<details><summary>Screenshot 1.3</summary>
<img src="Images/2018-10-09-13-30-15.png">
</details>

<details><summary>Screenshot 1.4</summary>
<img src="Images/2018-10-09-13-30-35.png">
</details>

<details><summary>Screenshot 1.5</summary>
<img src="Images/2018-10-09-13-30-56.png">
</details>

<details><summary>Screenshot 1.6</summary>
<img src="Images/2018-10-09-13-55-04.png">
</details>

<details><summary>Screenshot 1.7</summary>
<img src="Images/2018-10-09-14-00-22.png">
</details>
<br/>
1.8 On the Customize Template tab, enter the following variables:

- System Root User Password: VMware1!
- CLI Admin User Password: VMware1!
- CLI Audit User Password: VMware1!
- Hostname: nsx-manager
- Rolename: nsx-manager
- Default Gateway: 192.168.100.1
- Management Network IPv4 Address: 192.168.100.110
- Management Network Netmask: 255.255.255.0
- DNS Server List: 192.168.110.10
- Domain Search List: corp.local
- NTP Server: 192.168.100.1
- Enable SSH: True
- Allow Root SSH Logins: True
- All other options were left as default values

<details><summary>Screenshot 1.8</summary>
<img src="Images/2018-10-09-15-57-06.png">
</details>

<br/>
1.9 Complete the Deploy OVF Template Wizard
<br/>
<br/>

<details><summary>Screenshot 1.9</summary>
<img src="Images/2018-10-09-15-57-37.png">
</details>

<br/>
1.10 In the vSphere web client go to the task console and verify that the Status for Deploy OVF Template is "Completed" before proceeding
<br/>
<br/>

<details><summary>Screenshot 1.10</summary>
<img src="Images/2018-10-09-17-00-03.png">
</details>

<br/>
1.11 In the vSphere web client power on the NSX-Manager VM
<br/>
NOTE: If the option to power on the NSX-Manager VM is not available, log out and then log back in to the vSphere web client
<br/>
<br/>

<details><summary>Screenshot 1.11</summary>
<img src="Images/2018-10-09-17-02-52.png">
</details>

<br/>
1.12 Using the IP address you assigned to NSX Manager in the Deploy OVF Template Wizard, open a web browser connection to NSX Manager, for example:

```https://192.168.100.110/login.jsp```

<br/>
NOTE: On your first login, you will be prompted to accept the EULA
<br/>
<br/>

<details><summary>Screenshot 1.12</summary>
<img src="Images/2018-10-09-17-13-37.png">
</details>
<br/>
This completes the NSX Manager installation, please proceed on to the Controller installation section below

#### NSX-T Controller Installation

This section follows the standard documentation, which includes additional details and explanations: [Configure Automated Installation of Controller and Cluster using the NSX Manager UI](https://docs.vmware.com/en/VMware-NSX-T/2.2/com.vmware.nsxt.install.doc/GUID-92843E38-127B-4F85-8B7A-C8027E86175C.html)

As this is a lab environment, we will only be installing a single controller, you can reference the documentation above for instructions on multi-controller installations

2.0 Install NSX-T Controller
<br/>
<br/>
2.1 In the NSX Manager web interface, go to the Compute Managers section in the fabric panel per the image below

<details><summary>Screenshot 2.1</summary>
<img src="Images/2018-10-09-21-21-39.png">
</details>
<br/>

2.2 In the New Compute Manager dialogue box, enter the following variables:

- Name: vcsa-01a.corp.local
- Domain Name: vcsa-01a.corp.local
- Username: administrator@corp.local
- Password: VMware1!
- Leave the SHA-256 Thumbprint Blank

<details><summary>Screenshot 2.2</summary>
<img src="Images/2018-10-09-22-26-22.png">
</details>
<br/>

2.3 In the Invalid Thumbprint dialogue box, click add to populate vCenters thumbprint

<details><summary>Screenshot 2.3</summary>
<img src="Images/2018-10-09-23-05-22.png">
</details>
<br/>

2.4 On the Compute Managers Screen, verify that the registration status for vcsa-01a.corp.local is "Registered". If it is showing up as not registered, please see section 2.5 below.

<details><summary>Screenshot 2.4</summary>
<img src="Images/2018-10-09-23-24-08.png">
</details>
<br/>

<details><summary>2.5 If your computer manager registration status is "Not Registered" expand this section and follow the steps</summary>
<br/>
2.5.1 In the NSX Manager UI on the "Compute Managers" page, click on the "Not Registered" link as shown in the following image:

<img src="Images/2018-10-09-23-35-08.png">
<br/>
<br/>
2.5.2 Select the checkbox next to the error shown, and click "Resolve" as in the image below:
<img src="Images/2018-10-09-23-39-41.png">
<br/>
<br/>
2.5.3 In the Resolve Errors dialogue box, enter the administrator username and password for vCenter and click "Add" as in the image below - Note that it can take a minute or two for the Registration Status to update:

<img src="Images/2018-10-09-23-43-40.png">
</details>
<br/>

2.6 In the NSX Manager web UI, expand the System panel, select the "Components" page, and select "Add Controllers"

<details><summary>Screenshot 2.6</summary>
<img src="Images/2018-10-09-23-50-34.png">
</details>
<br/>

2.7 In the Add Controllers dialogue box on the Common Attributes page, enter the following values and click "Next":

- Compute Manager: vcsa-01a.corp.local
- Enable SSH: True
- Enable Root Access: True
- Join Existing Cluster: False
- Shared Secret: VMware1!
- CLI Password: VMware1!
- Root Password: VMware1!
- All other variables left to default values

<details><summary>Screenshot 2.7</summary>
<img src="Images/2018-10-09-23-58-53.png">
</details>
<br/>

2.7 In the Add Controllers dialogue box on the Controllers page, enter the following values and click "Finish"

- Hostname: nsx-controller
- Cluster: RegionA01-MGMT01
- Resource Pool: Null
- Host: Null
- Datastore: RegionA01-ISCSI01-COMP01
- VM-RegionA01-vDS-MGMT
- Management IP/Netmask: 192.168.100.111/24
- Management Gateway: 192.168.100.1

<details><summary>Screenshot 2.7</summary>
<img src="Images/2018-10-10-00-11-07.png">
</details>
<br/>

2.8 View the controller deployment status near the bottom of the Components page and wait for the controller to finish deploying. If your controller deployment has a power-on error, please see section 2.8 below. 

<details><summary>Screenshot 2.8</summary>
<img src="Images/2018-10-10-00-18-38.png">
</details>
<br/>

<details><summary>2.9 If you have a power-on error for your controller VM, expand this section and follow the steps</summary>
<br/>
2.9.1 The standard PKS lab environment does not have enough RAM in the management cluster to meet the default requirement of the NSX-T 2.2 Controller. If you are using your own lab environment, you can adjust the RAM available in your manamgent cluster if you have capacity. Otherwise we will adjust the RAM used by the NSX controller VM

<img src="Images/2018-10-10-00-24-47.png">
<br/>
2.9.2 In the vSphere web or HTML5 client, select the nsx-controller VM and Edit Settings

<img src="Images/2018-10-10-00-29-51.png">
<br/>
2.9.3 Change the Memory to 8GB and press OK

<img src="Images/2018-10-10-00-32-26.png">
<br/>
2.9.4 Power on the nsx-controller VM in vSphere web or HTML5 Client

</details>
<br/>
2.10 Wait for the controller cluster to appear on the Components page in NSX Manager web UI
<details><summary>Screenshot 2.10</summary>
<img src="Images/2018-10-10-00-36-16.png">
</details>
<br/>
2.11 Open an SSH session to nsx-manager and nsx-controller and confirm controller registration per Screenshot 2.11 below

<details><summary>Screenshot 2.11</summary>
<img src="Images/2018-10-10-00-38-33.png">
</details>

#### NSX-T Edge Installation

This section follows the standard documentation, which includes additional details and explanations: [Automatic Deployment of NSX Edge VMs from NSX Manager](https://docs.vmware.com/en/VMware-NSX-T/2.2/com.vmware.nsxt.install.doc/GUID-8C8A8A1E-3E4F-4C62-A373-B37018145E6E.html)

3.0 Install NSX Edge VM

3.1 Open a browser to the NSX Manager web UI and select Fabric > Nodes > Edges > Add Edge VM

<details><summary>Screenshot 3.1</summary>
<img src="Images/2018-10-10-01-45-24.png">
</details>
<br/>

3.2 In the Add Edge VM dialogue box, on the Name and Description tab, enter the following variables:

- Name: nsx-edge
- Host Name/FQDN: nsx-edge.corp.local
- Form Factor: Large

<details><summary>Screenshot</summary>
<img src="Images/2018-10-10-01-57-27.png">
</details>

3.3 In the Add Edge VM dialogue box, on the Credentials tab, enter the following variables:

- CLI Password: VMware1!
- System Root Password: VMware1!

<details><summary>Screenshot 3.3</summary>
<img src="Images/2018-10-10-02-00-54.png">
</details>

3.4 In the Add Edge VM dialogue box, on the Configure Deployment tab, enter the following variables:

- Compute Manager: vcsa-01a.corp.local
- Cluster: RegionA01-MGMT01
- Resource Pool: Null
- Host: Null
- Datastore: RegionA01-ISCS01-COMP01

<details><summary>Screenshot 3.4</summary>
<img src="Images/2018-10-10-02-03-25.png">
</details>

3.5 In the Add Edge VM dialogue box, on the Configure Ports tab, enter the following variables:

- Ip Assignment: Static
- Management IP: 192.168.100.112/24
- Default Gateway: 192.168.100.1
- Management Interface: VM-RegionA01-vDS-MGMT
- Datapath Interface #1: VM-RegionA01-vDS-MGMT

<details><summary>Screenshot 3.5</summary>
<img src="Images/2018-10-10-02-10-03.png">
</details>
<br/>

3.6 Wait for the deployment to complete, your deployment status may say "Power On Failed" if you exceed the memory allocation of your management cluster. Whether you recieved this error or not, it is a good practice in lab environments only to reduce the memory reservation on the nsx-edge VM to limit resource requirements for lab environments

3.7 In the vSphere web client (flash-based), edit the settings of the nsx-edge VM so there is no memory reservation
<details><summary>Screenshot 3.7</summary>
<img src="Images/2018-10-10-02-42-22.png">
</details>
<br/>

3.7.1 If your nsx-edge VM is not powered on, power it on now

3.8 Return to the Fabric > Nodes > Edges screen in the NSX Manager web UI, verify the deployment status is "Node Ready" and the Manager Connection is "Up" before proceeding. This may take several minutes after powering on the Edge VM

<details><summary>Screenshot 3.8</summary>
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