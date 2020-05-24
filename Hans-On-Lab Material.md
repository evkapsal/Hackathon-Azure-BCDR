![Microsoft Hellas Hackathon](Pictures/upper_1.png "Microsoft Hellas Workshop")

<div class="MCWHeader1">
Business continuity and Disaster recovery
</div>

<div class="MCWHeader2">
Hands-on lab step-by-step
</div>

<div class="MCWHeader3">
May 2020
</div>


**Contents**

<!-- TOC -->

- [Business continuity and disaster recovery hands-on lab step-by-step](#business-continuity-and-disaster-recovery-hands-on-lab-step-by-step)
  - [Abstract](#abstract)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
	- [Environment: Azure (Azure)](#environment-azure)
	- [Environment: On Premises (failover to Azure)](#environment-on-premises)
	- [Environment: Azure PaaS (Log Analytics and Azure Monitor)](#environment-azure-paas)
  - [Requirements](#requirements)
  - [Exercise 1: Prepare Azure Infrastructure](#exercise-1-prepare-azure-infrastructure)
	- [Task 1: Create Resource Group](#task-1-create-resource-group)
	- [Task 2: Deploy Virtual Networks](#task-2-deploy-virtual-networks)
	- [Task 3: Deploy VPN Service](#task-3-deploy-vpn-Service)
  - [Exercise 2: Configure BCDR services](#exercise-2-configure-bcdr-services)
	- [Task 1: Create Azure recovery services vault](#task-1-create-azure-recovery-services-vault)
	- [Task 2: Connect your On Premises Environment](#task-2-connect-your-on-premises-environment)

<!-- /TOC -->

# Business continuity and disaster recovery hands-on lab step-by-step 

## Abstract

In this hands-on lab, you will implement a hybrid environment and use Azure BCDR technologies to achieve three objectives for this environment. These objectives include a Disaster Recovery to Azure, Azure On Premises-to-Azure failover, and an Azure Monitor implementation using Log Analytics technologies to ensure continuous monitoring of an application.

At the end of this hands-on lab, you will be able to design and build complex and robust IaaS BCDR solutions.  

## Overview

The Business continuity and disaster recovery hands-on Lab is an exercise that will challenge you to implement a BCDR solution that includes your on premises environment and uses Azure BCDR technologies to achieve three distinct goals for each environment. These objectives include a Disaster Recovery to Azure, Log Analytics implementation for Service Map, and Azure Monitor integration using Log Analytics technologies.


## Solution architecture

Below are diagrams of the solution architecture you will build in this lab. Please study this carefully, so you understand the whole of the solution as you are working on the various components.

### Environment: Azure 

- **Background:** You will prepare the Azure Infrastructure in order to provide Disaster Recovery and Monitoring Services to your On premises Infrastructure. Several Microsoft Azure solutions can be also enabled to provide your Security Insights, Network Monitoring and Dependency Mapping. 

- **Goal using Azure BCDR:** Your goal for this environment will be to enable the Azure Environment to host your Disaster Environment infrastructure. Service enablement, Virtual Networks and Log Analytics workspace.

    ![Diagram of the Azure Services that will be enabled.](Pictures/Azure_Environment.png "Azure Environment diagram")

### Environment: On premises

- **Background:** Your existing environment will be used with Hyper-V or VMWare virtualization. You will choose an existing VM or create a new Linux or Windows VM to protect it through Azure Site Recovery..

- **Goal using Azure BCDR:** Your goal for this environment will be to enable Business Continuity to this server/application to Azure IaaS with a one-direction failover.

    ![Diagram of the on premises infrastructure and connectivity to azure paas services.](Pictures/OnPrem_Environment.png "Azure IaaS failover region to region solution")

### Environment: Azure PaaS Services (Log Analytics Solutions)

- **Background:** You will be able to enable Monitoring Solutions for your on premises and cloud environment through Azure Log Analytics Service. Also Azure Security Center with assess your existing environment and through Azure Monitor you will be able to review your systems insights.

- **Goal using Azure BCDR:** Your goal for this environment is to configure the PaaS services, connect your agents to the service, enable the solutions and review the results.

![Diagram of the Azure PaaS Services and Solutions.](Pictures/Monitor_Environment.png "Azure PaaS high availability with seamless failover diagram")

## Requirements

1. An Azure Subscription with full access to the environment.

## Exercise 1: Prepare Azure Infrastructure

Duration: 30 minutes

In this exercise, you will follow the Step By Step Guide to deploy the Azure Services and configure the environment.

- **Azure Environment:** This environment will consist of two Virtual Networks deployed on one resource group. Services that be will enables will deployed under this Resource Group.

### Task 1: Create Resource Group

1. From **Your Device**, open your favorite browser (**Edge**) and connect to the Azure portal at: <https://portal.azure.com>.

2. Select **+Create a resource** and then search for **Resource Group**.

3. Select **Add**

    ![Add Resource Group.](Pictures/RG_1.png "Resource Group Create")

3. Enter the following values:
	- **Subscription**: Select your **Azure subscription**.
	- **Resource group**: Enter a new resource group name, such **BCDRRG**
	- **Region**: Select an Azure location, such as **West Europe**


    ![Create Resource GRoup.](Pictures/RG_2.png "Resource Group")

4. Select **Review + Create**.

5. Select **Create**. It takes a few seconds to create a resource group.

6. Select **Refresh** from the top menu to refresh the resource group list, and then select the newly created resource group to open it. Or select **Notification** (the bell icon) from the top, and then select **Go to resource group** to open the newly created resource group.

    ![Notification Create Resource Group](Pictures/RG_3.png "Go To Resource Group")


### Task 2: Deploy Virtual Networks

1. From **Your Device**, open your favorite browser (**Edge**) and connect to the Azure portal at: <https://portal.azure.com>.

2. Select **+Create a resource** and then search for **Virtual Networks**.

3. Select **Add**.

4. On the Virtual Network Wizard enter the following.
	- **Subscription**: Select your **Azure subscription**.
	- **Resource group**: Select your existing resource group name,**BCDRRG**
	- **Name**: Enter **Cloud\_VNet\_Production**.
	- **Region**: Select the Azure location, **(Europe) West Europe**

    ![Create Virtual Network.](Pictures/Net_1.png "New Prodution VNet")

5. Select **Next: IP Addresses**.

6. On the IP Address enter **10.200.0.0/16**.

    ![Create Virtual Network-IP Address.](Pictures/Net_2.png "New Prodution Subnet")

6. Select **+ Add subnet**, then on the Subnet Name enter **Cloud\_PR\_Subnet** and for Subnet address range add **10.200.0.0/24**.

7. Select **Add**, then select **Review + create**. Leave the rest as default and select **Create**.

8. In Create virtual network, select **Create**.

9. Use the Above process to create a new Virtual Network and subnet.
	- **Subscription**: Select your **Azure subscription**.
	- **Resource group**: Select your existing resource group name,**BCDRRG**
	- **Name**: Enter **Cloud\_VNet\_Disaster**.
	- **Region**: Select the Azure location, **(Europe) West Europe**
	- **IP Address**: **10.220.0.0/24**
	- **Subnet Name**: **Cloud\_DR\_Subnet**
	- **Address Range**: **10.220.0.0/27**

	![Create DR Virtual Network-IP Address.](Pictures/Net_7.PNG "New Production Subnet")


### Task 3: Deploy VPN Service

1. From **Your Device**, open your favorite browser (**Edge**) and connect to the Azure portal at: <https://portal.azure.com>.

2. Search for and select **Virtual networks**.

3. Select **Cloud\_VNet\_Production**.

4. From the left blade select **Subnets**.
	
	![Select Subnet](Pictures/Net_8.png "Subnet") 

3. Select **+ Gateway Subnet**.

4. On the **Add subnet** dialog box enter the following.
	- **Name**: **Gateway Subnet**.
	- **Address Range**: **10.200.254.0/24** and press **OK**.

	![Create Gateway Subnet.](Pictures/Net_9.png "New Gateway Subnet") 

5. Search for and select **Virtual Network Gateways**.

	![Search VPN.](Pictures/Net_10.PNG "Search Virtual Network Gateways")

6. Select **+ Add**

7. In the **Create Virtual Network Gateway** enter the following details.
	- **Subscription**: Select your **Azure subscription**.
	- **Name**: Enter **Cloud\_VNet\_Production\_VPN**
	- **Region**: Select your Azure location, **(Europe) West Europe**
	- **Gateway Type**: Select **VPN**.
	- **VPN Type**: Select **Route-based**.
	- **SKU**: Select **Basic**.
	- **Generation**: Select **Generation1**.

	![Network Gateway VPN-1.](Pictures/Net_11.png "Create Virtual Network Gateways")

	- **Virtual Network**: Select your Production Network **Cloud\_VNet\_Production**
	- **Subnet Name**: **Cloud\_DR\_Subnet**
	- **Public IP Address**: Select **Create New**
	- **Public IP Address Name**: Enter **VPN\_PROD\_IP**
	- **Enable active-active mode**: Select **Disabled**
	- **Configure BGP ASN**: Select **Disabled**


	![Network Gateway VPN-2.](Pictures/Net_12.png "Create Virtual Network Gateways")

8. Select **Review + create** and select **Create** to deploy.

> **Note: ** This step can take up to 45 minutes to complete, but you can continue to the next exercise.


## Exercise 2: Configure BCDR services

Duration: 30 minutes

In this exercise, you will create and configure the services that will make it possible to failover  the on-premises environment. These will include a Recovery Services Vault used for Azure Site Recovery.

### Task 1: Create Azure recovery services vault

1. From **Your Device**, open your favorite browser (**Edge**) and connect to the Azure portal at: <https://portal.azure.com>.

2. Select **+Create a resource**, then enter **Backup and Site Recovery**, and select **Create**.

    ![Screenshot of the Backup and Site Recovery Screen with the Create button selected.](Pictures/DR_2.png "Backup and Site Recovery Screen Create Button")

3. Complete the **Recovery Services Vault** blade using the following inputs, then select **Create**:

    - **Name**: `BCDRCLOUDSRV`
    - **Resource Group**: Select **BCDRRG**.
    - **Location**: Select **West Europe**.
    - Select **Review + Create**. It takes a few seconds to create the Vault.

	![Create Recovery Services Vault Wizard.](Pictures/DR_5.png "Backup / Site Recovery Wizard")

4. Once the **BCDRRSV** Recovery Service Vault has been created, open it in the Azure portal. Select the "Site Recovery" tab.

    ![Screenshot of the Backup / Site Recovery tabs with Site Recovery tab selected.](Pictures/DR_3.png "Backup / Site Recovery tabs")

5. This is your dashboard for Azure Site Recovery (ASR), for the HOL.

    ![Screenshot of the Backup / Site Recovery tabs with Site Recovery tab selected.](Pictures/DR_4.png "Backup / Site Recovery tabs")


### Task 2: Connect your On Premises Environment

1. For your Hyper-V Environment Follow the **Hyper-V** guide.[Hyper-V Environment](Hyper-V%20Environemt%20Preparation.md) 

2. For your VMWare Environment Follow the **VMWare** guide. [VMWare Environment](VMWare%20Environment%20Preparation.md) 

