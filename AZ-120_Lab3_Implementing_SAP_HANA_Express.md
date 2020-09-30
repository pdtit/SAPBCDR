# AZ 120 Module 10: Maintaining Azure for SAP Workloads
# Lab 3: Implementing SAP HANA Backup and Disaster Recovery

Estimated Time: 120 minutes

All tasks in this lab are performed from the Azure portal (including the Bash Cloud Shell session)  

   > **Note**: When not using Cloud Shell, the lab virtual machine must have Azure CLI installed [**https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest**](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli-windows?view=azure-cli-latest) and include an SSH client e.g. PuTTY, available from [**https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html**](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).

Lab files: none

## Scenario
  
This lab introduces you to integrating Azure core Disaster Recovery Services into an SAP HANA landscape scenario, by deploying and managing Azure Backup and Azure Site Recovery. You use the Free SAP HANA Express image from the Azure Marketplace as the Virtual Machine to use for the DR scenario. 

## Objectives
  
After completing this lab, you will be able to:

-   Deploy SAP HANA Express Edition from the Azure MarketPlace

-   Configure Azure Virtual Machine Backup for SAP HANA infrastructure

-   Configure Azure Site Recovery Failover for SAP HANA infrastructure

## Requirements

-   A Microsoft Azure subscription with the sufficient number of available DSv3 vCPUs (2 x 4) and DSv2 (1 x 1) vCPUs

-   A lab computer with a recent browser (Microsoft Edge, Chrome, Safari,...) version 


## Exercise 1: Provision an SAP HANA Express Edition Virtual Machine from the Azure MarketPlace

Duration: 15 minutes

In this exercise, you deploy an SAP HANA Express VM, validate the running service and configure the SAP HANA workload. This machine is the baseline for the remaining exercises in this lab.

### Task 1: Deploy SAP HANA Express VM

1.  From the lab computer, start a Web browser, and navigate to the Azure portal at *https://portal.azure.com*

1.  If prompted, sign in with the work or school or personal Microsoft account with the owner or contributor role to the Azure subscription you will be using for this lab.

1.  In the Azure portal, click **+ Create a resource**.

1.  From the **New** blade, in the search field, type **SAP HANA**, and select **SAP HANA, Express Edition** from the list of resolved images.   

1. From the **SAP HANA, Express edition** blade, choose **SAP HANA, express edition (Server + Applications) and Confirm the provisioning by pressing the **Create** button.  
  
1. From the **Create a virtual machine** blade, complete the following deployment parameters:

    -   Subscription: *the name of your Azure subscription*

    -   Resource group: *a new resource group named* **az12003-RG**

    -   Virtual machine name: **SAPHXE-vm0**

    -   Region: *an Azure region where you can deploy Azure VMs and which is closest to the lab location*

    -   Availability options: **No infrastructure redundancy required**

    -   Image: **SAP HANA, Express Edition (Server + Applications) - Gen1** 
    
    -   Azure Spot Instance: **No**

    -   Size: **Standard_D2s_v3 - 2 vcpus, 8GiB Memory**

    -   Authentication type: **Password**

    -   Username: **student**

    -   Password: **Pa55w.rd1234**
    
1. Click **Next:Disks** to continue to the Disk configuration parameters 

    -   OS Disk Type: **Premium SSD**
    
    -   Public inbound ports: **Allow selected ports**

    -   Encryption Type: **(Default) Encryption at-rest with a platform managed key**
    
1. Click **Next: Networking** to continue to the Network parameters configuration:

    -   Selected inbound ports: **SSH (22)**

    -   OS disk type: **Premium SSD**

    -   Virtual network: *a new virtual network named* **az12003a-RG-vnet**

    -   Subnet name: **(new)default (172.16.0.0/24)**

    -   Public IP address: *a new IP address named* **SAPHXE-vm0-ip**

    -   NIC network security group: **Advanced**

    -   Configure network security group: *a new network security group named* **SAPHXE-vm0-nsg** *with the default settings (allowing inbound SSH connections)*

    -   Accelerated networking: **Off**

    -   Place this virtual machine behind an existing load balancing solutions: **No**
    
1. Click **Next:Management** to continue to the Management parameters section: 

    -   Azure Security Center 
        / Enable basic plan for free: **No**

    -   Monitoring / Enable detailed monitoring: **Off* 
    
    -   Boot diagnostics: **Disable**

    -   System assigned managed identity: **Off**

    -   Enable auto-shutdown: **Off**
    
1. Click **Next: Advanced** to continue:

    -   Extensions: *None*

    -   Tags: **None**
    
1. **Confirm** all defined parameters by clicking the **Review+Create** button.

1. Validate the parameters for deployment, and confirm by clicking the **Create** button.

1.  Wait for the provisioning to complete. This should take only a few minutes. You can follow along from the *Deployment in Progress* blade, as well as from the *Notification Area* or by selecting the Resource Group "AZ12003-RG" and seeing the resources getting created. 

This completes the task in which you deployed the SAP HANA Express Edition Virtual Machine.

### Task 2: Run the SAP HANA Express configuration

1.  Once the Virtual Machine Deployment is completed successfully, navigate to the Virtual Machine instance in the Azure Portal. From the *Overview* blade, identify the **Public IP Address** and click **copy to clipboard** to copy the Public IP Address. 

1.  From the Azure Portal, **Launch** *Cloud Shell* (Look at the ">" icon next to the Azure search field in the menu ribbon), and select **Bash**

1. Open an SSH connection to the SAP HANA VM, logging on with the preconfigured HANA Express edition credentials, as follows:

```
ssh hxeadm@<Paste_SAPHANAVM_Public_IpAddress_here>, e.g. hxeadm@52.173.150.103
```
1.  Type **yes** when you are asked if you are sure you want to continue connecting.

1.  Provide the password (HXEHana1)

1.  You are prompted to change this password; start with providing the current password once more (HXEHana1), and hit Enter; next, provide the new password (Pa55w.rd1234), hit enter, and retype the new password once more. 

1.  This launches the SAP HANA Express Edition setup, where you are asked to provide a new HANA Database master password: (Pa55w.rd1234), and confirm this password once more. 

1. Next, you are asked a question *about Proxy Server access to connect to the internet.* Type **N**

1.  Next, you are prompted to *wait for the XSA configuration to finish* Type **N**

1.  Next, Type **Y** to proceed with the configuration as listed.

1.  Wait for the HXE setup process to complete successfully. This should take only a few minutes. You are prompted with a message, informing you the SAP HANA express edition 2.0 is ready for use. You have also switched to the Linux Path /usr/sap/HXE/HDB90>

1.  From a new internet browser session, connect to **http://<PublicIPAddress>:8090**, which loads an image, saying the SAP XSEngine is up and running.

This completes the task, in which you completed the SAP HANA Express Edition configuration. The Virtual Machine is now ready for the next exercise. 

> **Result**: After you completed this exercise, you have provisioned an SAP HANA Express Edition Virtual Machine.

## Exercise 2: Deploying and Managing Azure Backup for SAP HANA Virtual Machines

Duration: 30 minutes

In this exercise, you deploy an Azure Recovery Services Vault for Azure Backup, as well as configuring a daily backup job for the SAP HANA Virtual Machine.

### Task 1: Create and configure Azure Recovery Services Vault

1.  In the Azure Portal, click **Create New Instance**; from the **New** blade, search for **Backup and Site Recovery".

1. Click **Create** to launch the deployment steps.

1. From the **Create Recovery Services Vault** blade, complete the following deployment parameters:

    -   Subscription: *the name of your Azure subscription*

    -   Resource group: *a new resource group named* **az12003-vault-RG**

    -   Vault name: **az12003backupvault**

    -   Region: *the same Azure region where the SAP HANA Virtual Machine is deployed*

1.  Click **Review+Create** to confirm the deployment. Validate the parameters, and confirm by clicking **Create**. 

1.  Once the Vault resource got deployed successfully, click **Go to Resource**, to open the **az12003backupvault** blade. 

1.  From the **Getting Started** section, click **Backup**

    -   Where is your workload running: **Azure**
    
    -   What do you want to backup: **Virtual Machine**
    
1.  Click **Backup**

1.  Review the settings for the **DefaultPolicy** (Daily 8:00 AM UTC, Instant Restore 2 days, Retention 30 days); feel free to *Create a new Policy* and modify any of the settings. 

1.  Under the *Virtual Machine* section, click **Add**, and **select** the *SAPHXE-vm0* from the list of VMs. Confirm with **OK**.

1.  Click **Enable Backup**; wait for the confirmation of this task completion in the Notification Area.

1.  From the **Protected Items** section, click **Backup Items**

1.  In the *"Backup Management Type"* list, **notice** *Azure Virtual Machine* , showing **1** as Backup Item Count.

1.  Select **Azure Virtual Machine**, which opens the **Backup Items** blade, showing the SAPHXE-vm0 machine. **Notice the warning** regarding a *Pending Initial backup*; Click on the *elypsis* (...) and select **Backup Now**. Accept the retention date, and confirm by clicking **Backup**. 

1.  This *triggers* the backup job, and will run the actual VM backup. While this runs in the back-end, you can continue with the next exercise. Later on, you come back to this backup job to run a Virtual Machine restore of the HANA server.

> **Result**: After you completed this exercise, you have configured a daily backup job to backup the SAP HANA Express Edition Virtual Machine.

## Exercise 3: Deploying and Managing Azure Site Recovery for SAP HANA Virtual Machines

Duration: 15 min

Exercise: In this exercise, you configure a DR scenario for the SAP HANA Virtual Machine, by failing over to a secondary Azure region during the disaster.

### Task 1: Set up Azure Site Recovery

1.  In the Azure Portal, click **Create New Instance**; from the **New** blade, search for **Backup and Site Recovery".

1. Click **Create** to launch the deployment steps.

1. From the **Create Recovery Services Vault** blade, complete the following deployment parameters:

    -   Subscription: *the name of your Azure subscription*

    -   Resource group: *a new resource group named* **az12003-ASRvault-RG**

    -   Vault name: **az12003ASRvault**

    -   Region: *select a **different** Azure region then where the SAP HANA Virtual Machine is deployed*

1.  Click **Review+Create** to confirm the settings, and click **Create** to kick-off the provisioning of the Azure Recovery Service Vault.

1. Once the provisioning is complete, click **Go to Resource**, which opens the *az12003-ASRvault* blade.

### Task 2: Configure ASR Protection for the SAP HANA Virtual Machine 

1. From the *Getting Started* section, select **Disaster Recovery**

1.  Under the Azure Virtual Machine section, click **1. Enable Replication**

1.  Complete the **Source** settings:

- Source Location: *Source location where the SAP HANA Server is running*

- Azure Virtual Machine Deployment Model: **Azure Resource Manager**

- Source Subscription: *your Azure subscription*

- Source Resource Group: *select the Resource Group that holds your SAP HANA VM (az12003-RG)*

- Disaster Recovery between Availability Zones: **No**

1. Click **Next** to get to the list of Virtual Machines

1. **Select** the SAP HANA VM (SAPHXE-vm0), and click **Next**

1.  Select a **Target Location** from the list, and accept all other default settings:

- Target Resource Group: (new az12003-RG-asr)

- Target Virtual Network: (new az12003-RG-vnet-asr)

- Cache Storage Account: (new xxxxxyyyy12003asrcache)

- Replica Managed Disks: 1 premium disk, 0 standard disk

1. Click on **Replication Policy** Customize

1. Validate the settings for the Replication Policy, having 

- a Recovery Point retention of 24 hours, 

- and App consistent retention of 4 hours.  

1. Close the Replication Policy settings by clicking **OK**.

1. You are returning to the *Enable Replication* blade, where you can confirm the configuration by clicking **Enable Replication**

**NOTE: Keep this blade open during the creation background process, which shows different popups in the *Notification Area* for the different actions happening. After a while, you are returned to the az12003-ASRvault Site Recovery main blade.

1. In the *Monitoring* section, select **Site Recovery Jobs**; here you can follow the different tasks getting processed to set up the replication. This process will take several (5-10 avg) minutes in total.

- Create Replication Policy
- Create a site
- Create Protection Container
- Map Networks
- Associate Replication Policy
- Enable Replication

1. Once the **Enable Replication** step is running, navigate to **Replicated Items** under the *Protected Items* section in the left menu. Here you notice the SAPHXE-vm0 in a **Healthy** Replication Health and having a status of **Enabling Protection**. 

1. Click **Enabling Protection**; this opens the more detailed job view on the replication job itself. 

- Prerequisite checks
- Installing Mobility Service and Preparing Target
- Enable Replication
- Starting Initial Replication
- Updating the provider states

From here, the SAP HANA VM will replicate across the 2 regions, which will take some time (20-30min on average). You will return to this process later on to initiate a failover. If you want to check back on the process every now and then, use the following steps:

1. Once the **Enable Replication** step is running, navigate to **Replicated Items** under the *Protected Items* section in the left menu. Here you notice the SAPHXE-vm0 in a **Healthy** Replication Health and having a status of **XX %**. as long as this is not 100%, or not showing **Protected**, it means the replication is not complete yet, and not allowing you to initiate a failover. 

> **Result**: After you completed this exercise, you have provisioned Azure Site Recovery Vault and configured a Disaster Recovery setup for the SAP HANA Virtual Machine.

## Exercise 4: Use Azure Backup to restore an SAP HANA Virtual Machine

Duration: 30 minutes

In this exercise, you will initiate a Virtual Machine restore out of Azure Backup.

### Task 1: Run a restore job from a successful VM Backup

1. From the Azure Portal, navigate to the Azure Recovery Services Vault you created in exercise 2 (az12003backupvault)

1. Navigate to *Protected Items* in the left menu, and select **Backup Items**

1. From the Backup Management Type list, select **Azure Virtual Machine** which redirects you to the *Backup Items* blade.

1. Here, **select** the SAP HANA VM (SAPHXE-vm0)

1. In meantime, you should have a successful backup job, allowing you to initiate a VM restore. To do that, click **Restore VM** in the upper menu.

1. Select the **most recent Restore Point** available.

1. Complete the remaining parameters to run the restore job:

- Restore Type: **Create new Virtual Machine**
- Virtual Machine Name: **RestoredSAPHXE-vm0**
- Resource Group: select **az12003-RG**
- Virtual Network: select **az12003-RG-vnet**
- Subnet: **default**
- Staging Location: **vmstoragexxxyyy*

1. Confirm the settings by clicking **Restore**; this triggers the Restore Job, as notified from the notification area. 

1. From the **notification area**, click on **triggering restore for SAPHXE-vm0**

1. This opens the *Backup Jobs* blade, showing more details regarding the running restore job. 

Wait for this job to complete. This should only take a few minutes.

1. Once the restore job is completed successfully, browse to **Virtual Machines**, and notice the restored Virtual Machine, in a running state. 

1. Select the RestoredSAPHXE-vm0 machine, and identify its **Public IP address** from the *Overview* blade.

1. From a new browser tab, connect to http://<RestoredVM Public IP Address>:8090

1. The SAP XSEngine is up and running image will show up, confirming you successfully restored the SAP HANA VM in a running state.

1. **Stop** the RestoredSAPHXE-vm0 Virtual Machine by selecting **STOP** in the upper menu. 

> **Result**: After you completed this exercise, you have successfully restored an SAP HANA Virtual Machine and validated the SAP HANA Services were running. 

## Exercise 5: Performing a DR Failover Scenario for SAP HANA Virtual Machines

Duration: 30 minutes

In this exercise, you will perform a DR failover scenario from the primary region to your disaster recovery region for the SAP HANA Virtual Machine instance.

```
Note: you can only start this exercise when the SAPHXE-vm0 is in "protected" status in Azure Recovery Vault. 
```

### Task 1: Initiate Failover

1. From the Azure Portal, browse to the Recovery Services Vault az12003-vault. Here, select **Replicated Items** under the Protected Items section.

1. Select the SAPHXE-vm0, to open the detailed blade.

1. Click **Failover** from the top menu. 

1. Given the lab scenario, we decide to skip the test failover. Therefore, select **I understand the risk. Continue Failover**.

1. Validate the failover from the primary region to the secondary region, and ** select the latest processed (low RTO) Recovery Point** , and keep the setting **Shut down machine before beginning failover** flagged. 

1. Confirm the failover by clicking **OK**. This kicks off the process, and shows the status in the notification area. Click on the status balloon **Starting failover**, to retrieve more details:

- Prerequisites Check for failover
- Shut down the virtual machine
- Synchronizing the latest changes
- Start Failover
- Start the replica virtual machine

1. Wait for the failover process to complete successfully. Close the Failover status window.

1. Once the failover process is complete, browse back to the **Virtual Machines** section of the Azure Portal, and notice the recovery SAPHXE-vm0 is in a running state in the secondary region, while the VM in the primary region is shut down.  

1. Browse to the **Disaster Recovery** option in the recovery Virtual Machine Operations section. 

1. Notice the **Commit** button in the top menu, which confirms the failover process and identifies the recovery VM as primary now. This informs ASR that - when the primary VM should come online - no synchronization should happen (to avoid data conflicts during your failover scenario). It will also remove any recovery points for the recovery VM. (!!Use this Commit action with cause in a production environment!!)

1. Confirm the *Commit* action with **OK**

1. Last, we finish this exercise with performing a failback scenario, simulating a restore to the situation before the disaster happened. From the **Azure Recovery Services Vault** AZ12003-ASRvault, browse to Protected Items / **Replicated Items** and select the SAPHXE-vm0 machine. 

1. Next, from the top menu, select **Re-Protect** 

1. Notice the replication direction goes now from the secondary region back to the first region. Accept all the default settings, since they reflect the original settings from the primary Virtual Machine replica. Confirm with **OK**.

1. You can follow along the process by clicking on the **Re-protecting** message in the notification area. Wait for this process to complete successfully. This process will take several minutes. 

1. Once complete, initiate a new **failover**, which will execute a failback, shutting down the secondary VM, and starting up the primary VM again.


> **Result**: After you completed this exercise, you have simulated a disaster recovery failback scenario. 

## Lab Summary

In this lab, you learned the following:

- Deploying a dev/test SAP HANA Express Edition from the Azure Marketplace image.

- Deployment and configuration of Azure Backup to protect an Azure Virtual Machine.

- Deployment and configuration of Azure Site Recovery to allow for a disaster recovery failover and failback of Azure Virtual Machines.


