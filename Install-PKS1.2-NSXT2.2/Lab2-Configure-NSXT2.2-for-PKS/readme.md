# NSX-T 2.2 Configuration for PKS

## Overview

The following installation guide follows the implementation of a functional NSX-T 2.2 Installation configured for PKS 1.2 in a vSphere nested lab environment. This implementation uses variables that function in the lab environment. Anyone is welcome to build a similar lab environment and follow along with the lab exercises, but please note you will need to replace any variables such as IP addresses and FQDNs and replace them with the appropriate values for your lab environment.

The steps provided in this lab guide are intended for a lab implementation and do not necessarily align with best practices for production implementiations. While the instructions provided in this lab guide did work for the author in their lab environment, VMware and/or any contributors to this Guide provide no assurance, warranty or support for any content provided in this guide.

## Installation Notes

Anyone who implements any software used in this lab must provide their own licensing and ensure that their use of all software is in accordance with the software's licensing. This guide provides no access to any software licenses.

For those needing access to VMware licensing for lab and educational purposes, we recommend contacting your VMware account team. Also, the [VMware User Group's VMUG Advantage Program](https://www.vmug.com/Join/VMUG-Advantage-Membership) provides a low-cost method of gaining access to VMware licenses for evaluation purposes.

5.0 Create an IP Pool for Tunnel Endpoint IP Addresses

5.1 In the NSX Manager UI, go to Inventory > Groups > IP Pools and click "Add"

<details><summary>Screenshot5.1</summary>
<img src="Images/2018-10-11-15-52-41.png">
</details>
<br/>
5.2 Create an IP pool with the following details:

- Name: ip-pool-teps
- Click "Add" to add a subnet
  - IP Range 192.168.130.51 - 192.168.130.75
  - Gateway: 192.168.130.1
  - CIDR: 192.168.130.0/24
  - DNS Servers: 192.168.110.10
  - DNS Suffix: corp.local

<details><summary>Screenshot 5.2</summary>
<img src="Images/2018-10-11-16-38-42.png">
</details>
<br/>

5.3 To complete NSX-T Installation, NSX-T manager needs to be configured with two uplink profiles, one for hosts and one for edges.

5.3.1 In the NSX Manager UI, go to Fabric > Profiles > Uplink Profiles

<details><summary>Screenshot 5.3.1</summary>
<img src="Images/2018-10-11-18-03-48.png">
</details>
<br/>

5.3.2 Verify that the edge network uplink profile matches the configuration in Screenshot 1.3.2 below. If for any reason the edge network profile does not already exist, add a network profile per Screenshot 1.3.2

<details><summary>Screenshot 5.3.2</summary>
<img src="Images/2018-10-11-18-02-42.png">
</details>
<br/>

5.3.3 Verify that the host network uplink profile matches the configuration in Screenshot 1.3.3 below. If for any reason the host network profile does not already exist, add a network profile per Screenshot 1.3.3

<details><summary>Screenshot 5.3.3</summary>
<img src="Images/2018-10-11-18-09-55.png">
</details>
<br/>

5.4 Add a transport zone for overlay networks

5.4.1 In the NSX Manager UI, go to Fabric > Transport Zones and click "Add"

<details><summary>Screenshot 5.4.1</summary>
<img src="Images/2018-10-13-15-19-11.png">
</details>
<br/>

5.4.2 Add an overlay transport zone with the following details:

- Name: tz-verlay
- N-VDS Name: hs-overlay
- N-VDS Mode: Standard
- Traffic Type: Overlay

<details><summary>Screenshot 5.4.2</summary>
<img src="Images/2018-10-13-15-25-47.png">
</details>
<br/>

5.5 Add a transport zone for VLAN networks

5.5.1 In the NSX Manager UI, go to Fabric > Transport Zones and click "Add"

<details><summary>Screenshot 5.5.1</summary>
<img src="Images/2018-10-13-15-19-11.png">
</details>
<br/>

5.5.2 Add an vlan transport zone with the following details:

- Name: tz-vlan
- N-VDS Name: hs-vlan
- N-VDS Mode: Standard
- Traffic Type: VLAN

<details><summary>Screenshot 5.5.2</summary>
<img src="Images/2018-10-13-15-29-13.png">
</details>

5.6 Configure all each esxi hosts that may host VMs that connect to an NSX-T Overlay or VLAN network and your NSX Edge vm as Transport Nodes using the following procedure.

For the lab environment used in this example, configure all esxi hosts and your NSX Edge VM as transport nodes

5.6.1 In the NSX Manager UI, go to Fabric > Nodes > Transport Zones and click "ADD"

<details><summary>Screenshot 5.6.1</summary>
<img src="Images/2018-10-13-16-10-27.png">
</details>
<br/>

5.6.2 Complete the Add Transport Node for each relevant esxi host and NSX Edge vm. The esxi hosts should be added to the tz-overlay transport zone, and the NSX Edge VM should be added to both the tz-overlay and tz-vlan transport zones.

After you fill in the variables in the General tab of the Add Transport Node wizard, click "ADD". You do not need to make any edits to the N-VDS tab of the Add Transport Zone wizard

5.6.2.1 Add Transport Node for esxi hosts with the following details:

- Name: esx-01a
  - Update the name for each esxi host you add
- Node: esx-01a.corp.local
  - Update the Node for each esxi host you add
- Transport Zones: tz-overlay
  - Select tz-overlay from available transport zones and click the arrow to move tz-overlay to the "Selected" section
- Click on the N-VDS
- N-VDS Name: hs-overlay
- NIOC Profile: nsx-default-nioc-hostswitch-profile
- Uplink Profile: nsx-default-uplink-hostswitch-profile
- IP Assignment: Use IP Pool
- IP Pool: ip-pool-teps
- Physical NICS:
  - Column 1: vmnic1
  - Column 2: uplink-1

<details><summary>Screenshot 5.6.2.1.1</summary>
<img src="Images/2018-10-13-16-26-06.png">
</details>

<details><summary>Screenshot 5.6.2.1.2</summary>
<img src="Images/2018-10-13-16-42-06.png">
</details>

<details><summary>Screenshot 5.6.2.1.3</summary>
<img src="Images/2018-10-13-17-05-06.png">
</details>
<br/>

5.6.2.2 Add Transport Node for NSX Edge VM

- Name: nsx-edge
- Node: nsx-edge
- Transport Zones: tz-overlay & tz-vlan
  - Select tz-overlay & tz-vlan from available transport zones and click the arrow to move them to the "Selected" section
- Click on the N-VDS - you will add a node switch for both transport zones
- N-VDS Name: hs-overlay
- Uplink Profile: nsx-default-edge-vm-uplink-profile
- IP Assignment: Use IP Pool
- IP Pool: ip-pool-teps
- Physical NICS:
  - Column 1: fp-eth0
  - Column 2: uplink-1
- Click "ADD N-VDS" to add an additional VDS definition for the tz-vlan transport zone
- N-VDS Name: hs-vlan
- Uplink Profile: nsx-default-edge-vm-uplink-profile
- Physical NICS:
  - Column 1: fp-eth1
  - Column 2: uplink-1

 Note that once added, it can take up to a few minutes before the transport node status changes to "Up"

<details><summary>Screenshot 5.6.2.2.1</summary>
<img src="Images/2018-10-13-17-09-31.png">
</details>

<details><summary>Screenshot 5.6.2.2.2</summary>
<img src="Images/2018-10-13-17-20-32.png">
</details>

<details><summary>Screenshot 5.6.2.2.3</summary>
<img src="Images/2018-10-13-17-21-22.png">
</details>

<details><summary>Screenshot 5.6.2.2.4</summary>
<img src="Images/2018-10-13-18-23-37.png">
</details>
<br/>

5.6.3

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







<details><summary>Screenshot</summary>

</details>