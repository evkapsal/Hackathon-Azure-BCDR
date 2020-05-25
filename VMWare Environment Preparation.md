![VMware Logo](Pictures/vmware.png)

## Exercise 3: Configure VMWare Environments for failover.
**Contents**

<!-- TOC -->

- [Exercise 3: Configure VMWare Environments for failover](#exercise-3-configure-vmware-environments-for-failover)
    - [Task 1: Prepare on-premises VMWare environment](#task-1-prepare-on-premises-vmware-environment)
    - [Task 2: Deploy the Configuration Server](#task-2-deploy-the-configuration-server)
    - [Task 3: Finalize Azure Environment and Create the First Replication](#task-3-finalize-azure-environment-and-create-the-first-replication)
    - [Task 4: Simulate Test Failover *Optional*](#task-3-simulate-test-failover)


<!-- /TOC -->

Duration: 45 minutes

In this exercise, you will configure the VMWare environments to use BCDR technologies found in Azure. The environment has unique configurations that must be completed to ensure their availability in the event of a disaster.

### Task 1: Prepare on-premises VMWare environment


1. Go to **VMWare VCenter** and create a new *Role* with name **Azure\_Site\_Recovery**

2. Give the following permission to the role:

**Task** | **Role/Permissions** | **Details**
--- | --- | ---
**VM discovery** | At least a read-only user<br/><br/> Data Center object –> Propagate to Child Object, role=Read-only | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object, to the child objects (vSphere hosts, datastores, VMs and networks).
**Full replication, failover, failback** |  Create a role (Azure_Site_Recovery) with the required permissions, and then assign the role to a VMware user or group<br/><br/> Data Center object –> Propagate to Child Object, role=Azure_Site_Recovery<br/><br/> Datastore -> Allocate space, browse datastore, low-level file operations, remove file, update virtual machine files<br/><br/> Network -> Network assign<br/><br/> Resource -> Assign VM to resource pool, migrate powered off VM, migrate powered on VM<br/><br/> Tasks -> Create task, update task<br/><br/> Virtual machine -> Configuration<br/><br/> Virtual machine -> Interact -> answer question, device connection, configure CD media, configure floppy media, power off, power on, VMware tools install<br/><br/> Virtual machine -> Inventory -> Create, register, unregister<br/><br/> Virtual machine -> Provisioning -> Allow virtual machine download, allow virtual machine files upload<br/><br/> Virtual machine -> Snapshots -> Remove snapshots | User assigned at datacenter level, and has access to all the objects in the datacenter.<br/><br/> To restrict access, assign the **No access** role with the **Propagate to child** object, to the child objects (vSphere hosts, datastores, VMs and networks).
	 
3. If you didn't download the `Configuration Server` go to **BCDRCLOUDSRV** Recovery Vault and follow the below steps:

	- Go to **Prepare Infrastructure** > **Source**
	- In **Prepare source**, select **+Configuration server**
	- In **Add Server**, check that **Configuration server for VMware** appears in **Server type**
	- Download the OVA template for the configuration server.

   > [!TIP]
   >You can also download the latest version of the configuration server template directly from the [Microsoft Download Center](https://aka.ms/asrconfigurationserver).


### Task 2: Deploy the Configuration Server

1. Sign in to the VMware vCenter server or vSphere ESXi host by using the VMWare vSphere Client.

2. On the **File** menu, select **Deploy OVF Template** to start the **Deploy OVF Template** wizard.

     ![Deploy OVF Template](Pictures/vmware_2.png)

3. On **Select source**, enter the location of the downloaded OVF.

4. On **Review details**, select **Next**.

5. On **Select name and folder** and **Select configuration**, accept the default settings.

6. On **Select storage**, for best performance select **Thick Provision Eager Zeroed** in **Select virtual disk format**. Use of the thin provisioning option might affect the performance of the configuration server.

7. On the rest of the wizard pages, accept the default settings.

8. On **Ready to complete**:

    * To set up the VM with the default settings, select **Power on after deployment** > **Finish**.
    * To add an additional network interface, clear **Power on after deployment**, and then select **Finish**. By default, the configuration server template is deployed with a single NIC. You can add additional NICs after deployment.

9. From the VMWare vSphere Client console, turn on the **VM**.

10. The VM boots up into a Windows Server 2016 installation experience. `Accept the license agreement`, and enter an **administrator password**.

11. After the installation finishes, sign in to the VM as the administrator.

12. The first time you sign in, within a few seconds the **Azure Site Recovery Configuration tool** starts.

13. Enter a name that's used to register the configuration server with Site Recovery. Then select **Next**.

14. The tool checks that the VM can connect to Azure. After the connection is established, select **Sign in** to sign in to your Azure subscription.</br>
    a. The credentials must have access to the vault in which you want to register the configuration server.</br>
    b. Ensure that the chosen user account has permission to create an application in Azure. To enable the required permissions, follow the guidelines in the section [Azure Active Directory permission requirements](#azure-active-directory-permission-requirements).

15. The tool performs some configuration tasks, and then reboots.

16. Sign in to the machine again. The configuration server management wizard starts automatically in a few seconds.

17. In the configuration server management wizard, select **Setup connectivity**. From the drop-down boxes, first select the NIC that the in-built process server uses for discovery and push installation of mobility service on source machines. Then select the NIC that the configuration server uses for connectivity with Azure. Select **Save**. You can't change this setting after it's configured. Don't change the IP address of a configuration server. Ensure that the IP assigned to the configuration server is a static IP and not a DHCP IP.

18. On **Select Recovery Services vault**, sign in to Microsoft Azure with the credentials used in step 6 of [Register the configuration server with Azure Site Recovery services](#register-the-configuration-server-with-azure-site-recovery-services).

19. After sign-in, select your Azure subscription and the relevant resource group and vault.

20. On **Install third-party software**:

    |Scenario   |Steps to follow  |
    |---------|---------|
    |I want to download and install MySQL through Azure Site Recovery.    |  Accept the license agreement, and select **Download and install**. After installation finishes, proceed to the next step.       |

21. On **Validate appliance configuration**, prerequisites are verified before you continue.

22. On **Configure vCenter Server/vSphere ESXi server**, enter the FQDN or IP address of the vCenter server, or vSphere host, where the VMs you want to replicate are located. Enter the port on which the server is listening. Enter a friendly name to be used for the VMware server in the vault.

23. Enter credentials to be used by the configuration server to connect to the VMware server. Site Recovery uses these credentials to automatically discover VMware VMs that are available for replication. Select **Add** > **Continue**. The credentials entered here are locally saved.

24. On **Configure virtual machine credentials**, enter the user name and password of virtual machines to automatically install mobility service during replication. For **Windows** machines, the account needs local administrator privileges on the machines you want to replicate. For **Linux**, provide details for the root account.

25. Select **Finalize configuration** to complete registration.

26. After registration finishes, open the Azure portal and verify that the configuration server and VMware server are listed on **Recovery Services Vault** > **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.

### Task 3: Finalize Azure Environment and Create the First Replication
### Task 4: Simulate Test Failover *Optional*


### Configure settings



