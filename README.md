# <ins>Azure Migrate VM Lab</ins>

For this project I used a windows 11 Machine at home running Hyper-V.  In Hyper V I have a Domain Cntroller, Windows Server acting as a router, File Server, SQL Server & Azure migrate appliances.  The Hyper-V Lab is connected to Azure using a VPN connection which terminates in Azure inside a VWAN Hub.</div>


**Create project In Azure**

Azure Migrate > Servers , Databases and Web apps > create project

<div align="justify">Select a Subscription, Resource group, Project name & Geography
Under advanced chose the connectivity method you would like to use.  The options are Public Endpoint & Private Endpoint. 
The public endpoint will communicate with azure and send data over the internet and the private option will send the data over private connectivity. 


</br>A VPN or express route will be needed for this configuration.  For the purpose of the test a public endpoint was selected.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/17df7bae-fa4e-4577-b368-697e48191317)


If you select Private endpoint you will be given the additional options for configuration, you will need to create a VNET and subnet for this configuration. 

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/d03b37ad-31c8-4e18-bfc5-04310a40efc7)

Next, create you appliance by giving the appliance a name and the generating the key, as you can see from the drop down the appliance needs to be configured for which ever environment you are wanting to assess.  You have the ability to create a discovery appliance for:
	
 VMware environment
	
 Hyper V environment 
	
 Physical + other (AWS, GCP etc)

For the lab I have created you will need to select > **Physical + Other (AWS, GCP etc)**

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/f0625a60-30a5-4e02-9a0b-606347fb8630)


Once the key is generated you can then download the Azure Migrate Appliance.

# <ins>Discovery Appliance</ins>

Copy the AzureMigrateInstaller.zip you have unzip the files you have downloaded and unzip the files on the physical or virtual server that you are going to confure as the discovery appliance.  For my lab I used a virtual machine running on Hyper-V.

To install the software follow the screen shots below:
 
![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/de7ebb2e-ae27-47cf-bc10-93535bf25897)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/39fac481-1cbb-4158-8d65-6aa0f73f38d6)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/5b0f6839-5350-47d5-bdb3-26b4d7048aa4)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/b7dcada0-7f17-479f-a439-2cf07c4a6277)

Once you have logged in, the appliance will launch the landing page, enter the project key that was created earlier in the appliance creation section.  


On the desktop of the server double click > **Azure Migrate Appliance Configuration Manager** this will launch the Appliance configuration manager.  

The appliance will check for updates and may take up to 10 minutes to finish configuring. Paste the key that was provided in the steps earlier and click verify. 

The Appliance will then verify the login with your Azure account.  Please refer to the Azure Migrate documentation for the required permissions.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/5ef0107e-c046-4cd3-bd73-677c2160a251)


If you can’t remember the key you can obtained this in the Azure portal 

# <ins>Server Replication</ins>

To replicate the servers in the lab I deployed a replication appliance.  One of the pre-requisites for this is a new windows server 2016 onwards with sizing according to the guidance  (needs adding to github)

To do this navigate to Azure Migrate home screen and select Servers, databases and web apps.

Select **Discover** under migration tools.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/8e981b43-de53-4643-874c-fa56e367eb70)

  
In the drop down selections select what you want to migrate to, what type of machines they are, target region & if you would like to install a new replication appliance or scale out appliance.  
For my lab the selections below were used.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/cb9d54b2-141e-464b-b6da-f8a99ffaeaa9)


In **step 1** download the replication appliance software & the registration key, copy that to your windows 2016 server and install the software.
After the installation finishes, the Appliance configuration wizard launches automatically. (You can also launch the wizard manually by using the cspsconfigtool shortcut that was created on the appliance desktop)

In this lab, I manually installed the Mobility service on the source VMs to be replicated (covered in next section). 

You need to create a dummy account in this step to proceed. For your dummy account, use "guest" as the friendly name, "username" as the username, and "password" as the password for the account. You use this dummy account in the Enable Replication stage.

After the appliance restarts after setup, in Discover machines, select the new appliance in Select Configuration Server. Then select Finalize registration. The Finalize registration step performs a couple of final tasks to prepare the replication appliance.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/0a1f8b1f-b1e9-49da-a0c9-f7d4c091032c)


**Mobility Agent Replication**
Copy the mobility agent package to a location **C:\program files (x86)\Microsoft Azure Site Recovery\***

The location of the file is located on the replication appliance: **C:\ProgramData\ASR\home\svsystems\pushinstallsvc\repository***

Run the following command to un pack the file 
**.\Microsoft-ASR_UA*Windows*release.exe /q /x:"C:\Program Files (x86)\Microsoft Azure Site Recovery"**

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/0f83a26d-cc74-4559-9f65-a80b5b0448ae)


Run the below command to setup the agent in legacy mode
.\UnifiedAgentInstaller.exe /CSType CSLegacy
Select Install mobility install

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/f7c973d6-6af2-4333-8fcd-9146fcb6f48e)


Leave the file location default & click **Install**

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/0c244656-b1ce-42a0-bebc-0d91c132642f)


The mobility agent will then be installed, once complete select the **Proceed to Configuration** button 

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/8a46477b-a36d-40d9-9513-73cbf36547cc)


Enter the IP address & Connection Passphrase of the configuration server 

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/a681d3de-e663-4001-9058-54cf60ab0ec1)

 
The Connection Passphrase can be found on the configuration server.  On the desktop you should have a shortcut called Cspsconfigtool. Double click the shortcut to launch the configuration tool.  

Once the configuration tool is launched navigate to the ConfigurationDetails tab.  The Passphrase is protected but can be viewed by running the following command
C:\Program Files (x86)\Microsoft Azure Site Recovery> genpassphrase.exe -n

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/28e5d4ee-3f81-4c5e-80d5-bc71bdd25dea)

  
Once you have entered the passphrase click Finish
 

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/d6025d9c-21a6-441d-8a8a-99f49994b955)


# <ins>VM Replication</ins>

To start the replication process navigate to Azure Migrate in the portal.  Select Servers, databases and web apps 

In the migration tools section select **Replicate** as shown in the diagram bewlow:

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/8b14a970-ab28-4796-8990-583c389fd046)

Follow the screen shots below to setup the replication of the servers.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/a2d5d33b-5783-4cec-9ab0-73452cf0afc4)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/d589685e-abd7-4a71-a1af-166b64c185da)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/ff66675a-f3c2-4609-a0f1-8c1e1ed0f3e0)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/8b0b0947-7899-4c59-b836-ba32e3cf0fb4)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/c8436c95-b879-478c-b869-7f4dd807b3b9)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/8c7306f1-2c9e-4fe6-bf09-0cdf5886ee77)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/a7d43712-f011-4fde-8683-d21588815a15)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/c7888045-00a9-4366-8d4d-53b6720fa0b6)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/9958a446-d28b-4c11-a53e-a9645f1ee13f)

Test Failover

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/e1daa11d-fe4c-465c-981a-1ed3f49e47c2)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/2bd62efe-1cc3-4da0-945e-345735b719ff)

 
Test virtual machine now created in Azure

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/8cac156e-42cf-4cda-9766-217d6c017de5)


Test migration successful  

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/d5d0dce4-b929-46d0-99c4-ac62a65a3149)

Clean up resources 
 
![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/ace9ba96-1739-4d2a-bd47-327eff4a6194)

 
![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/f0c778cf-d124-4b8d-94ef-7bf1070b1517)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/571bd3ee-b782-4cd4-8dab-f218feac9ad6)

# <ins>Migrate VM</ins>

In Azure Migrate navigate to the Migrate option 

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/294ce209-901f-4711-b64c-7554a52f6c8f)



This will take you to a list of server that can be migrated.  Select the machine you would like to migrate.  Notice the option for target OS. 

The Azure Migrate tool also allows you to upgrade the OS as part of the process.  

Additionally take note of the option to select if you want to shut down the machine before the migration to minimise data loss.

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/b9a66823-4645-43a2-b401-71352b08ddca)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/69063a90-343b-432e-be12-f643b9f3911f)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/a4c5126f-18de-4ed2-a0bb-b306c0470933)

![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/6558c73d-3e93-4055-aed5-b1a37aa93ea3) 
 

# <ins>Unable to RDP to Migrated VM</ins> 
 
![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/b695d879-eb74-4bde-8961-7c8bfa3341e3)

# <ins>Test folder access to file share on VM migrated to Azure</ins>
 
![image](https://github.com/philljudge/Azure-Migrate-VM-Lab/assets/131694192/00e1ad08-ebb6-421a-959f-36f622e9ffb0)
</div> 
