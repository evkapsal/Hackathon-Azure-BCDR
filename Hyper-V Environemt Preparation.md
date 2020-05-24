![Hyper-V Logo.](Pictures/hyper.jpeg "Hyper-V Logo")

**Contents**

<!-- TOC -->

- [Exercise 3: Configure Hyper-V Environments for failover](#exercise-3-configure-hyper-v-environments-for-failover)
    - [Task 1: Configure on-premises to Azure IaaS failover](#task-1-configure-on-premises-to-azure-iaas-failover)
    - [Task 2: Register Your On-Premises Hyper-V](#task-2-register-your-on-premises-hyper-v)
    - [Task 3: Finalize Azure Environment and Create the First Replication](#task-3-finalize-azure-environment-and-create-the-first-replication)
    - [Task 4: Simulate Test Failover *Optional*](#task-3-simulate-test-failover)


<!-- /TOC -->

## Exercise 3: Configure Hyper-V Environments for failover

Duration: 45 minutes

In this exercise, you will configure the Hyper-V environments to use BCDR technologies found in Azure. The environment has unique configurations that must be completed to ensure their availability in the event of a disaster.

### Task 1: Configure on-premises to Azure IaaS failover

In this task, your **On Premises VM** will be configured to replicate to Azure and be ready to failover to the **BCDRCLOUDSRV**. This will consist of configuring your Hyper-V host with the ASR provider and then enabling replication of the VM to the Recovery Service Vault.

1. From the Azure portal, open the **BCDRCLOUDSRV** Recovery Services Vault located in the **BCDRRG** resource group.

2. Select **Site Recovery** in the **Getting Started** area of **BCDRCLOUDSRV** blade.

    ![In the Recovery Services vault blade, under Getting Started, Site Recovery is selected.](Pictures/DR_6.png "Recovery Services vault blade")

3. Next, select **Prepare Infrastructure** in the **For On-Premises Machines** section. This will start you down a path of various steps to configure your VM that is running on Hyper-V on-premises to be replicated to Azure.

    ![In the For On-Premises Machines section, Prepare Infrastructure is selected.](Pictures/DR_7.png "For On-Premises Machines section")

4. On **Step 1 Protection Goal** select the following inputs and then select **OK**:

    - **Where are your machines located?**: On-premises
    - **Where do you want to replicate your machines to?**: To Azure
    - **Are you performing a migration?**: No
    - **I understand, but I would like to continue with Azure Site Recovery**: checked
    - **Are your machines virtualized?**: Yes, with Hyper-V  (Your VM is running as a nested VM in Azure).
    - **Are you using System Center VMM to manage your Hyper-V hosts?**: No

    ![Fields in the Protection goal blade are set to the previously defined values.](Pictures/DR_8.png "Protection goal blade")

5. On **Step 2 Deployment planning**, confirm you have completed deployment planning by selecting **Yes, I have done it** then select **OK**.

    > **Note**: You can read more about planning an ASR to deployment here:
    >
    > <https://docs.microsoft.com/en-us/azure/site-recovery/site-recovery-hyper-v-deployment-planner>

    ![In the Deployment planning blade, Yes, I have done it is selected from a dropdown list.](Pictures/DR_9.png "Deployment planning blade")

6. On **Step 3 Prepare source** select **+Hyper-V Site**.

    ![In the top menu of the Prepare source blade, +Hyper-V Site is selected.](Pictures/DR_10.png "Prepare source blade")

7. On the **Create Hyper-V site** blade, enter the name: `OnPremHyperVSite`. Select **OK**.

    ![OnPremHyperVSite is in the Name field on the Create Hyper-V site blade.](Pictures/DR_11.png "Create Hyper-V site blade")

8. The portal will deploy the site providing you notifications. Wait for the creation process to complete, and the ASR portal will update once this is done. Your new site is now shown under **Step 1: Select Hyper-V site**.

    ![The Successfully completed creating Hyper-V site message displays.](Pictures/DR_12.png "Success message")

    ![Under Step 1: Select Hyper-V site, OnPremHyperVSite is selected in the dropdown list.](Pictures/DR_13.png "Step 1: Select Hyper-V site")

9. Next select **+Hyper-V Server**.

    ![In the top menu of the Prepare source blade, Hyper-V Server is selected.](Pictures/DR_14.png "Prepare source blade")

10. A new blade will appear. You will need to download the vault registration key to register the host in the Hyper-V site of ASR. Select the Download button which will save the file to your Downloads folder on the **Hyper-V On Premises Server**.

    ![In the Add Server blade, the Download button is selected.](Pictures/DR_15.png "Add Server blade")

### Task 2: Register Your On-Premises Hyper-V

In this task you will install the Azure Site Recovery Provider and you will register the Provider to the Site Recovery Vault **BCDRCLOUDSRV**.

1. If you not have already download the _Azure Site_Recovery_Provider_ Open Internet Explorer on **HYPERVHOST** and browse to the following URL. This will download the Azure Site Recovery Provider for Hyper-V.

    ```text
    http://aka.ms/downloaddra_cus
    ```

2. Start the **Provider Executable ** or if you are downloading Select **Run**.

    ![When asked if you want to run or save the file, the Run button is selected.](Pictures/hyper_20.png "Run button")

3. Select **On** and then **Next**.

    ![In the Microsoft Update screen, under Microsoft Update, On (recommended) is selected.](Pictures/hyper_21.png "Microsoft Update screen")

4. Select **Install** on the Provider Installation screen.

    ![On the Provider Installation screen, Install is selected.](Pictures/hyper_22.png "Provider Installation screen")

5. Once the Provider has been installed you will come to a screen that will request for you **Register** or **Finish**. Select **Register**.

    ![On the Provider Installation screen, the Register button is selected.](Pictures/hyper_23.png "Provider Installation screen")

6. Locate the vault registration key which you store before in the directory _Downloads_ of **Device**. Right-click the file and copy it. Move back to **Hyper-V Host** and paste the file to the desktop.

    ![In the file manager context menu, Paste is selected.](Pictures/hyper_24.png "Menu")

    ![The file that was pasted is displayed.](Pictures/hyper_25.png "File being pasted")

7. On the Vault Setting screen select **Browse**.

    ![In the Vault Settings Screen, the Browse button is selected.](Pictures/hyper_26.png "Vault Settings Screen")

8. Locate the Vault file on the desktop and select **Open**.

    ![In File Explorer, the vault file is selected.](Pictures/hyper_27.png "File Explorer")

9. The Vault Settings will now be populated. Select **Next**.

    ![Fields in the Vault Settings screen are populated.](Pictures/hyper_28.png "Vault Settings screen")

10. On the Proxy settings screen retain the settings **Connect directly to Azure Site Recovery without a proxy server** and select **Next**.

    ![On the Proxy Settings screen, the radio button is selected for Connect directly to Azure Site Recovery without a proxy server.](Pictures/hyper_29.png "Proxy Settings screen")

11. The provider will then connect and configure the Azure Site Recovery registration for the Hyper-V Server.

    ![On the Registration screen, a message indicates that azure site recovery is being configured for registration.](Pictures/hyper_30.png "Registration screen")

12. After a few minutes, the wizard will be complete with the message **The Server was registered in the Azure Site Recovery vault.** Select **Finish** to close the Provider installation.

    ![The message on the Registration screen now says the server was registered in the Azure Site Recovery vault.](Pictures/hyper_31.png "Registration screen")

### Task 3: Finalize Azure Environment and Create the First Replication

1. You will need to return to the Prepare Source screen and select **+Hyper-V Server**. Notice the warning that it could take up to 30 mins for this server to appear, but in practice you should cancel out of this window by selecting Step 2 and then answer yes again with **I have done it** to the question of Deployment planning.

    ![+Hyper-V Server is selected in the Prepare source blade.](Pictures/hyper_32.png "Prepare source blade")

2. Once the Server appears, select **OK**.

    ![In the Prepare source blade, a call out points to HYPERVHOST under Step 2.](Pictures/hyper_33.png "Prepare source blade")

3. On **Step 4 Target Prepare** review the screen to better understand the various steps. Select **+Storage account** to add a storage account that will be used for the **On Premises Hypervisor** when it is failed over to Azure.

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

    ![Create and Associate is selected in the Replication Policy blade.](Pictures/hyper_37.png "Replication Policy blade")

9. Enter the name: `OnPremVM-POL`. Review the settings that you can configure and then select **OK**.

    ![In the Create and Associate blade, the Name field is set to OnPremVM-POL.](Pictures/hyper_38.png "Create and Associate blade")

10. Azure will run a deployment and start the process of creating and then associating the Hyper-V Site with the replication policy.

    ![In Step 1 of the replication policy, the Replication policy field is empty.](Pictures/hyper_39.png "Step 1 replication policy")

    ![In Step 1 of the replication policy, the Replication policy field is displays OnPremVM-POL.](Pictures/hyper_40.png "Step 1 replication policy")

    > **Note**: This will take a couple of minutes to complete. Please wait until this completes prior to moving on.

    Once complete, select **OK**.

    ![The OK button is selected in the Replication policy blade following messages indicating the replication policy has been successfully created and associated with OnPremHyperVSite.](Pictures/hyper_41.png "Replication policy blade")

11. Select **OK** again, and the process for adding the Hyper-V Server to the Recovery Services Vault will be complete.

    ![The OK button is selected in the Prepare infrastructure blade that indicates all five tasks have been completed.](Pictures/hyper_42.png "Prepare infrastructure blade")

12. Next, select Step 1: **Replicate Application**.

    ![In the Recovery Services vault blade, Step 1: Replicate Application is selected.](Pictures/hyper_43.png "Recovery Services vault blade")

13. On the Source blade select: **Source**: **On-premises**, **Are you performing a migration**: **No**, and **Source location**: **OnPremHyperVSite** and select **OK**.

    ![Fields in the Source blade are set to the previously defined values.](Pictures/hyper_44.png "Source blade")

14. Complete the Target blade, select the following values:

    - **Post-failover resource group**: **BCDRRG**
    - **Storage Account**: Select the account you just created _Example_(**onpremvm8675309**).
    - **Storage Account for replication logs**: Create a *new* one using the prefix **bcdrasrrepllogs**.
    - **Azure network**: Configure now for selected machines.
    - **Post-failover Azure network**: **Cloud\_VNet\_Production**
    - **Subnet**: **Cloud\_PR\_Subnet** _10.200.0.0/24_

    ![Fields in the Target blade are set to the previously defined settings.](Pictures/hyper_45.png "Target blade")

15. Next on the **Select virtual machines** select **Your On Premises Virtual Machine** and then select **OK**.

    ![In the Select virtual machines blade, OnPremVM is selected.](Pictures/hyper_46.png "Select virtual machines blade")

16. Complete the **OnPremVM** selections of the **Configure properties** blade using these inputs and then select **OK**:

    - **OS Type**: Linux or Windows _what you choose in your environment_
    - **OS Disk**: _Disk Name_
    - **Disks to Replicate**: _Disk Name_ \[127.00GB\]

    ![Fields in the Configure properties blade are set to the previously defined settings.](Pictures/hyper_47.png "Configure properties blade")

17. On the **Configure replication settings** blade review the selections and notice that the **OnPremVM-POL** that you created has been selected. Select **OK**.

    ![The Configure replication settings blade displays.](Pictures/hyper_48.png "Configure replication settings blade")

18. On the final screen, select **Enable replication**.

    ![Enable replication is selected on the Enable replication blade.](Pictures/hyper_49.png "Enable replication blade")

19. The deployment will be submitted. You can select **Site Recovery Jobs** to review the process.

    ![In the Recovery Services vault blade, Jobs and Site Recovery Jobs are selected. The Site Recovery jobs blade displays process status.](Pictures/hyper_50.png "Recovery Services vault and Site Recovery jobs blade")

20. After a few minutes, the Enable replication will move to Successful. Select **Overview** and **Site Recovery** on the **BCDRRSV** blade. You should now see that there is one (1) Healthy Replicated Item.

    ![In the Recovery Services vault blade, a call out points to the healthy replicated item.](Pictures/hyper_51.png "Recovery Services vault blade")

21. Select **Healthy 1** and you will see the replicated items list. Notice it shows that the VM is healthy, but the replication has just started, so it shows **0% synchronized**. It will take a few minutes for the VM to replicate.

    ![In the Replicated items blade, a call out points to the status of 0 percent synchronized.](Pictures/hyper_52.png "Replicated items blade")

    > **Note**: If the **OnPremVM** does not immediately go to a healthy replication  state, or ever gets in an unhealthy state, you may need to "Disable Replication" and recreate it using the steps above.

22. Select **OnPremVM**. Review the Replication details for **OnPremVM**. Once the VM has replicated, the selections across the top menu bar of the dashboard will allow you to work with this VM.

    ![In the Replicated items blade, a call out points to the status of Protected.](Pictures/hyper_53.png "Replicated items blade")

    > **Note**: It will take about 15 minutes for the VM to replicate, so you can move on and come back to review the environment later.

23. Scroll down and review the Infrastructure view of the BCDR environment you have created for **OnPremVM**. You can select **Hyper-V Virtual Machine** to review what is protected.

    ![In the Infrastructure view, Hyper-V Virtual machine and Log storage accounts are in On-premises, and connect to Azure Site Recovery and Storage accounts in Azure.](Pictures/hyper_54.png "Infrastructure view")

    ![In the Hyper-V sites blade, OnPremHyperVSite has one Hyper-V host, and one protected VM.](Pictures/hyper_55.png "Hyper-V sites blade")

24. Next, select **Compute and Network**.

    ![In the Replicated items blade, Compute and Network menu item is selected beneath the GENERAL section.](Pictures/hyper_56.png "Replicated items blade")

25. Details of the VM will be shown, and you can make configuration changes. Change the **Size** of the VM to **DS2\_v2,** and then select **Save**.

    ![The Compute properties dialog box displays with size set to the previously mentioned value.](Pictures/hyper_57.png "Compute properties dialog box")

> **Note**: It could take a few minutes for these screens to populate, so be patient. You can come back to this step later to adjust the size if you wish.


### Task 4: Simulate Test Failover _Optional_

On this task you will simulate the and use the **Test Failover Functionality** of Site Recovery vault.


1. From the Azure portal, open the **BCDRCLOUDSRV** Recovery Services Vault located in the **BCDRRG** resource group.

 
2. Open the **BCDRCLOUDSRV** and select **Replicated Items** under the **Protected Items** area. Make sure that **OnPremVM** shows up ad **Replication Heath**: **Healthy.** Select **OnPremVM**.

    ![Under Protected Items, Replicated items is selected.](Pictures/hyper_60.png "Protected Items section")

    ![The OnPremVM status is Healthy.](Pictures/hyper_61.png "OnPremVM status")

3. Right-click **OnPremVM** and then select **Test Failover**.

    ![The OnPremVM Healthy status right-click menu has Failover selected.](Pictures/hyper_62.png "Failover option")

4. Select you your latest **recovery point**  verify that **From** is set to **MyOnPremSite** and **To** is set to **Microsoft Azure**, for **Azure virtual network** choose **Cloud\_DR\_Network** and press **OK**.

    ![Call outs in the Failover blade point to both the From and To fields.](Pictures/hyper_63.png "Failover blade")

5. The Azure portal will provide a notification that the Test failover is starting.

    ![The Starting Failover notification explains that the operation is in progress.](Pictures/hyper_64.png "Starting Failover notification")

6. By selecting Site Recovery Jobs, you can monitor the progress of the failover.

    ![In the Properties section, the status of the various jobs display.](Pictures/hyper_65.png "Properties section")

7. Once the Failover Status is *Successful* in Site Recovery Jobs, go to **Virtual Machines** and you will see your **OnPremVM** with status **Running**.

    ![The OnPremVM Healthy status running.](Pictures/hyper_66.png "Complete Test Faiolver option")

8. In order to access the Virtual Machine **OnPremVM** you need to deploy a Virtual Machine in this network because it is isolated.

9. When you finish your health check go again back to **BCDRCLOUDSRV** Site Recovery vault in the _Replicated items_. You will see your **OnPremVM** with status **Cleanup test ...**. 


10. Go to the left side on **...** and when the new blade appears select **Cleanup test failover**.

    ![In the Resource group blade OnPremVM is selected.](Pictures/hyper_67.png "Resource group blade")

11. Enter your Notes for the testing, Check **Testing is Complete** and press **OK**. This will delete your Test Failover Virtual Machine.




