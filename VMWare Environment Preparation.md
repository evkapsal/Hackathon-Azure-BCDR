![VMware Logo](Pictures/vmware.png)

## Exercise 3: Configure VMWare Environments for failover.
**Contents**

<!-- TOC -->

- [Exercise 3: Configure VMWare Environments for failover](#exercise-3-configure-vmware-environments-for-failover)
    - [Task 1: Prepare VMWare environment](#task-1-prepare-vmware-environment)
    - [Task 2: Deploy the Configuration Server](#task-2-deploy-the-configuration-server)
    - [Task 3: Finalize Azure Environment and Create the First Replication](#task-3-finalize-azure-environment-and-create-the-first-replication)
    - [Task 4: Simulate Test Failover *Optional*](#task-3-simulate-test-failover)


<!-- /TOC -->

Duration: 45 minutes

In this exercise, you will configure the VMWare environments to use BCDR technologies found in Azure. The environment has unique configurations that must be completed to ensure their availability in the event of a disaster.

### Task 1: Prepare VMWare environment

1. From the Azure portal, open the **BCDRCLOUDSRV** Recovery Services Vault located in the **BCDRRG** resource group.

2. Select **Site Recovery** in the **Getting Started** area of **BCDRCLOUDSRV** blade.

    ![In the Recovery Services vault blade, under Getting Started, Site Recovery is selected.](Pictures/DR_6.png "Recovery Services vault blade")

3. Next, select **Prepare Infrastructure** in the **For On-Premises Machines** section. This will start you down a path of various steps to configure your VM that is running on VMWare on-premises to be replicated to Azure.

	![In the For On-Premises Machines section, Prepare Infrastructure is selected.](Pictures/VMWARE_20.PNG "For On-Premises Machines section")


4. On **Step 1 Protection Goal** select the following inputs and then select **OK**:

    - **Where are your machines located?**: On-premises
    - **Where do you want to replicate your machines to?**: To Azure
    - **Are you performing a migration?**: No
    - **I understand, but I would like to continue with Azure Site Recovery**: checked
    - **Are your machines virtualized?**: `Yes, with VMware vSphere Hypervisor`
	Press **OK**

	![In the For On-Premises Machines section, Prepare Infrastructure is selected.](Pictures/VMWARE_21.PNG "For On-Premises Machines section")

5. In **Deployment planning** `Have you completed deployment planning?`, select **Yes, I have Done it** and click **OK**

    ![Fields in the Protection goal blade are set to the previously defined values.](Pictures/VMWARE_22.PNG "Protection goal blade")

6. In **Prepare Source** click **+ Configuration Server**

	![Fields in the Protection goal blade are set to the previously defined values.](Pictures/VMWARE_23.PNG "Protection goal blade")
7. From here you can download the configuration server.

	![Fields in the Protection goal blade are set to the previously defined values.](Pictures/VMWARE_24.PNG "Protection goal blade")

### Task 2: Deploy the Configuration Server

1. Go to **VMWare VCenter** and create a new *Role* with name **Azure\_Site\_Recovery**

2. Give the following permission to the role:

	**Task** | **Role/Permissions** | **Details**
	--- | --- | ---
	**VM discovery** | At least a read-only user<br/><br/> Data Center object –> Propagate to Child Object, role=Read-only | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object, to the child objects (vSphere hosts, datastores, VMs and networks).
	**Full replication, failover, failback** |  Create a role (Azure_Site_Recovery) with the required permissions, and then assign the role to a VMware user or group<br/><br/> Data Center object –> Propagate to Child Object, role=Azure_Site_Recovery<br/><br/> Datastore -> Allocate space, browse datastore, low-level file operations, remove file, update virtual machine files<br/><br/> Network -> Network assign<br/><br/> Resource -> Assign VM to resource pool, migrate powered off VM, migrate powered on VM<br/><br/> Tasks -> Create task, update task<br/><br/> Virtual machine -> Configuration<br/><br/> Virtual machine -> Interact -> answer question, device connection, configure CD media, configure floppy media, power off, power on, VMware tools install<br/><br/> Virtual machine -> Inventory -> Create, register, unregister<br/><br/> Virtual machine -> Provisioning -> Allow virtual machine download, allow virtual machine files upload<br/><br/> Virtual machine -> Snapshots -> Remove snapshots | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object, to the child objects (vSphere hosts, datastores, VMs and networks).
	
	create a new user **LabLocal** and assign the role to the user.
	
	![User in Role VMWare.](Pictures/vmware_3.png "Assign Role")
	
	Prepare a domain or local account with permissions to install on the VM for the mobility service push deployment.
	
	- **Windows VMs**: To install on Windows VMs if you're not using a domain account, disable Remote User Access
	   control on the local machine. To do this, in the registry > **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, add the
	     DWORD entry **LocalAccountTokenFilterPolicy**, with a value of 1.
	- **Linux VMs**: To install on Linux VMs, prepare a root account on the source Linux server.


 
3. Sign in to the VMware vCenter server or vSphere ESXi host by using the VMWare vSphere Client.

4. On the **File** menu, select **Deploy OVF Template** to start the **Deploy OVF Template** wizard.

     ![Deploy OVF Template](Pictures/vmware_2.png)

5. On **Select source**, enter the location of the downloaded OVF.

6. On **Review details**, select **Next**.

7. On **Select name and folder** and **Select configuration**, accept the default settings.

8. On **Select storage**, for best performance select **Thick Provision Eager Zeroed** in **Select virtual disk format**. Use of the thin provisioning option might affect the performance of the configuration server.

9. On the rest of the wizard pages, accept the default settings.

10. On **Ready to complete**:

    * To set up the VM with the default settings, select **Power on after deployment** > **Finish**.
    * To add an additional network interface, clear **Power on after deployment**, and then select **Finish**. By default, the configuration server template is deployed with a single NIC. You can add additional NICs after deployment.

11. From the VMWare vSphere Client console, turn on the **VM**.

12. The VM boots up into a Windows Server 2016 installation experience. `Accept the license agreement`, and enter an **administrator password**.

13. After the installation finishes, sign in to the VM as the administrator.

14. The first time you sign in, within a few seconds the **Azure Site Recovery Configuration tool** starts.

15. Enter a name that's used to register the configuration server with Site Recovery. Then select **Next**.

16. The tool checks that the VM can connect to Azure. After the connection is established, select **Sign in** to sign in to your Azure subscription.</br>
    a. The credentials must have access to the vault in which you want to register the configuration server.</br>
    b. Ensure that the chosen user account has permission to create an application in Azure. To enable the required permissions, follow the guidelines in the section [Azure Active Directory permission requirements](#azure-active-directory-permission-requirements).

17. The tool performs some configuration tasks, and then reboots.

18. Sign in to the machine again. The configuration server management wizard starts automatically in a few seconds.
	![Deploy OVF Template](Pictures/vmware_4.png)


19. In the configuration server management wizard, select **Setup connectivity**. From the drop-down boxes, first select the NIC that the in-built process server uses for discovery and push installation of mobility service on source machines. Then select the NIC that the configuration server uses for connectivity with Azure. Select **Save**. You can't change this setting after it's configured. Don't change the IP address of a configuration server. Ensure that the IP assigned to the configuration server is a static IP and not a DHCP IP.

20. On **Select Recovery Services vault**, sign in to Microsoft Azure with the credentials used in step 6 of [Register the configuration server with Azure Site Recovery services](#register-the-configuration-server-with-azure-site-recovery-services).

21. After sign-in, select your Azure subscription and the relevant resource group and vault.

22. On **Install third-party software**:

    |Scenario   |Steps to follow  |
    |---------|---------|
    |I want to download and install MySQL through Azure Site Recovery.    |  Accept the license agreement, and select **Download and install**. After installation finishes, proceed to the next step.       |

23. On **Validate appliance configuration**, prerequisites are verified before you continue.

24. On **Configure vCenter Server/vSphere ESXi server**, enter the FQDN or IP address of the vCenter server, or vSphere host, where the VMs you want to replicate are located. Enter the port on which the server is listening. Enter a friendly name to be used for the VMware server in the vault.

25. Enter credentials to be used by the configuration server to connect to the VMware server. Site Recovery uses these credentials to automatically discover VMware VMs that are available for replication. Select **Add** > **Continue**. The credentials entered here are locally saved.

26. On **Configure virtual machine credentials**, enter the user name and password of virtual machines to automatically install mobility service during replication. For **Windows** machines, the account needs local administrator privileges on the machines you want to replicate. For **Linux**, provide details for the root account.

27. Select **Finalize configuration** to complete registration.
	![Deploy OVF Template](Pictures/vmware_5.png)

28. After registration finishes, open the Azure portal and verify that the configuration server and VMware server are listed on **Recovery Services Vault** > **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.

### Task 3: Finalize Azure Environment and Create the First Replication

1. You will need to return to the **Step 2 Prepare Source** screen and select **+vCenter/vSphere host**. Enter the details and Select your **vCenter** host.

    ![+Hyper-V Server is selected in the Prepare source blade.](Pictures/vmware_6.png "Prepare source blade")

2. Select **OK**.

3. On **Step 3 Target Prepare** review the screen to better understand the various steps. Select **+Storage account** to add a storage account that will be used for the **VMWare** when it is failed over to Azure.

    ![+Storage account is selected from the top menu in the Target blade.](Pictures/hyper_34.png "Target blade")

4. Select **Create New** and provide a unique name for your storage account containing the name of the VM **choose a name** with added characters to make it unique. Also, select the Premium tier for the storage account and select **OK**.

    ![In the Choose storage account blade, Create new is selected. In the Create storage account blade, fields are set to the previously defined settings.](Pictures/hyper_35.png "Choose storage account and Create storage account blades")

    > **Note:** Be sure to select **Premium** Performance or you may run into issues later in the lab.

5. Select **+Storage account** again and create a second storage account using the **Standard** performance tier, then select **OK**.

6. The portal will submit a deployment, and you must wait until this completes. It will be created in the **BCDRRG** resource group.
  
    ![Screenshot of the Deployment succeeded message.](Pictures/hyper_36.png "Deployment succeeded message")

7. You will be failing the **On Premises Virtual Machine** into the **Azure** site that was deployed earlier, so you already have a Virtual Networks created. Select **OK** on the Target blade to continue.

    ![The Target blade is shown indicating three steps that need to be completed. Step 1: Select Azure subscription, Step 2: Ensure at least one compatible storage account exists, and Step 3: Ensure that at least one compatible Azure virtual network exists.](Pictures/hyper_36A.png "Target blade")

8. On the **Replication Policy** screen, select the **+Create and Associate** item.

9. Enter the name: `OnPremVM-POL`. Review the settings that you can configure and then select **OK**.

    ![Create and Associate is selected in the Replication Policy blade.](Pictures/vmware_8.png "Replication Policy blade")

	![The OK button is selected in the Replication policy blade following messages indicating the replication policy has been successfully created and associated with OnPremHyperVSite.](Pictures/hyper_41.png "Replication policy blade")

### Task 4: Simulate Test Failover *Optional*


### Configure settings



