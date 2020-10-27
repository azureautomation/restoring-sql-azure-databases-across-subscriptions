Restoring SQL Azure databases across subscriptions
==================================================

            
Description

Demonstrates restoration of SQL Azure databases across subscriptions.

Authors

  *  Ayush Kumar (ayushk) 
  *  Ruchira Dutta (rudut) 
Requirements

  *  You have an Azure subscription, if not sign up here: [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/en-us/pricing/free-trial/) or use an MSDN subscription.

  *  Familiar with PowerShell (no script writing required for this tutorial).

  *  Use of Connection and Certificate assets. 
  *  If you want to scrub your data first, please go to [http://codebox/DataScrubber](http://codebox/DataScrubber) 
Dependencies/Steps

Before running this runbook, make sure the following components are available:


  *  Automation Certificate Asset containing the management certificate loaded to Azure

  *  Automation Connection Asset containing the subscription id and the name of the certificate setting in Automation Assets


Here are the detailed steps on how to get started:


  *  Create an Azure Automation Account 
  *  Create and Export Management Certificate - 
a) Create a Self-Signed Certificate from IIS Manager 
b) Export it to your local as a .cer and a .pfx format
c) Now that you have a new management certificate created and exported, you’ll need to upload the exported .CER file to your Microsoft Azure subscription.

  *  Select **Settings **located on the side navigation panel on the Microsoft Azure Management Portal page.

  *  On the *Settings* page, click on the **Management Certificates** tab. 

  *  On the *Management Certificates* page, click on the **Upload** button located on the bottom black toolbar. When prompted, upload the .CER file.

  *  For your newly uploaded certificate, record the values listed in the **
name* **column for later use. 
  *  Create a Management Certificate asset -
a) On the *Automation* page in the *Azure Management Portal*, click on the name on the new Azure Automation account to drill into the account properties
b) Click on the Assets Tab
c) Click the **Add Setting** button on the bottom black toolbar.  When prompted, select **Add Credential**.
d) On the *Define Credential page*, select **Certificate** in the *Credential Type* list and enter a **name **for this new credential
e) On the *Upload a certificate file* page, browse to the .PFX file in the *File *field and enter the **Password** used to protect the
 private key when previously exporting this file.  
  *  Create an Azure Connection Asset -
a) On the *Assets* tab for your Azure Automation account, click the **Add Setting** button on the bottom toolbar.  When prompted, click **Add Connection**.
b) On the *Configure connection *page, select **Azure** as the *Connection Type* and enter a **Name** that matches your
***name** recorded earlier
c) On the *Configure connection properties* page, enter the name of the **management certificate asset **that you previously uploaded and enter your
**Microsoft Azure subscription ID** that was recorded earlier. 

  *  Create an Azure Credential Asset - // this is for storing the username and password for the target database //
a) On the *Assets* tab for your Azure Automation account, click the **Add Setting** button on the bottom toolbar.  When prompted, click **Add Credential**.
b) On the *Define Credential page*, select **Windows PowerShell Credential **in the *Credential Type* list and enter a name for this new credential.
c) Once done, click next and type in your **Username **and **Password**. 

  *  Import and publish the Restore-AzureDB Runbook. 

 

Script

Within Azure Automation, select the runbook and select **AUTHOR** from the top of the page and paste the following code in the **DRAFT** section.  Make sure you have the appropriate assets created
 and referenced properly or the runbook will not run completely.


 

Problem

**Restoring SQL Azure databases across subscriptions is an unreliable and time consuming process.**


As part of the cloud adoption strategy, to help the ongoing projects, we need a fasttrack and an efficient way to restore our databases across subscriptions.

** **

The current process that is followed involves the following steps:


  *  Export bacpac to Azure storage on prod subscription 
  *  Copy bacpac to dev subscription 
  *  Restore database from bacpac to SQL Azure 

This process is also not very reliable, and could take in excess of 12-18 hours for a complete export-import cycle to complete.


Additionally, due to the way import works, certain indexes could not be created during the import. The solution suggested by support was to import the bacpac to an on premises SQL Server, disable indexes, regenerate a bacpac, restore that to SQL Azure and
 recreate indexes with ONLINE mode. 



Solution


We chose an alternate option of using the Azure PowerShell API’s which allow us to create a database copy in
**less than an hour for a 100GB database**. Since there is no explicit export or import, rather the database copy happens at the Azure fabric layer, the issue with index rebuilds does not arise.


The key steps we perform are


  *  Create a new SQL Azure server on the production subscription 
  *  Create a copy of our production DB on this new server 

Transfer this server to the dev subscription, at which point we can create ad hoc copies as and when required.




This entire process is automated in a powershell script called from an Azure SQL runbook, which can be scheduled just like a SQL Server Job.
The benefits of this process can be listed as follows -


  *  Getting a fresh copy requires just a single click : to kick off the job 
  *  Environment refreshes can be done at a much greater frequency 
  *  Dev can get a prod equivalent DB instance in less than an hour 
  *  **The entire solution runs in Paas**, without the need for having any on prem presence at all



**Hence, the SLA cuts down from a massive 12 -18 hour process to a mere 1 hour process.**


 


** **


** **


** **

** **




        
    
TechNet gallery is retiring! This script was migrated from TechNet script center to GitHub by Microsoft Azure Automation product group. All the Script Center fields like Rating, RatingCount and DownloadCount have been carried over to Github as-is for the migrated scripts only. Note : The Script Center fields will not be applicable for the new repositories created in Github & hence those fields will not show up for new Github repositories.
