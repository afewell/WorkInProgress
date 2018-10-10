# PKS 1.2 NSXT 2.2 Installation Guide

## Overview

The following installation guide follows the implementation of a functional NSX-T 2.2 and PKS 1.2 Installation in a vSphere nested lab environment. This implementation uses variables that function in the lab environment. Anyone is welcome to build a similar lab environment and follow along with the lab exercises, but please note you will need to replace any variables such as IP addresses and FQDNs and replace them with the appropriate values for your lab environment. 

It is possible to build an identical lab environment that can use identical variables to those used in this lab guide. It is our intent to provide a tutorial for building an identical lab environment but it is unclear when that will be completed. In the meantime anyone seeking to build a functionally identical lab can load a live lab environment on VMware Hands on labs to identify any installation variables needed to build a comperable nested lab: [HOL-1931-01-CNA - VMware Pivotal Container Service and Kubernetes](https://www.vmwarelearningplatform.com/HOL/catalogs/catalog/874)

VMware employees who have access to the OneCloud Environment can load a vApp template to provide a pre-built lab environment and follow along with the steps in this guide. For details on accessing the vApp template, please check the CNA TPM Wiki on Confluence.

The steps provided in this lab guide are intended for a lab implementation and do not necessarily align with best practices for production implementiations. While the instructions provided in this lab guide did work for the author in their lab environment, VMware and/or any contributors to this Guide provide no assurance, warranty or support for any content provided in this guide.

## Installation Notes

Anyone who implements any software used in this lab must provide their own licensing and ensure that their use of all software is in accordance with the software's licensing. This guide provides no access to any software licenses.

For those needing access to VMware licensing for lab and educational purposes, we recommend contacting your VMware account team. Also, the [VMware User Group's VMUG Advantage Program](https://www.vmug.com/Join/VMUG-Advantage-Membership) provides a low-cost method of gaining access to VMware licenses for evaluation purposes.

## Table of Contents

### [Lab 1 - NSX-T 2.2 Installation](./Lab1-Install-NSXT2.2)

### [Lab 2 - NSX-T 2.2 Configuration for PKS](./Lab2-Configure-NSXT2.2-for-PKS)

### [Lab 3 - Install and Configure Ops Manager 2.3](./Lab3-InstallandConfigure-OpsMan-for-PKS)