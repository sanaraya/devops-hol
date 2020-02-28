<div class="MCWHeader1">
  <h3>DevOps - Hands-on lab step-by-step</h3>
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2020 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [OSS DevOps hands-on lab step-by-step](#oss-devops-hands-on-lab-step-by-step)
  - [Abstract and learning objectives](#abstract-and-learning-objectives)
  - [Overview](#overview)
  - [Solution architecture](#solution-architecture)
  - [Requirements](#requirements)
    - [Help References](#help-references)
  - [Exercise 1: Deploy the Web Application and Database to Azure](#exercise-1-deploy-the-web-application-and-database-to-azure)
    - [Task 1: Create the MySQL database](#task-1-create-the-mysql-database)
    - [Task 2: Restore the osTicket database to MySQL PaaS](#task-2-restore-the-osticket-database-to-mysql-paas)
    - [Task 3: Create the Web App](#task-3-create-the-web-app)
    - [Task 4: Configure the Tailspin SpaceGame Web App](#task-4-configure-the-tailspin-web-app)
    - [Task 5: Configure Deployment Center](#task-5-configure-deployment-center)
    - [Task 6: Configure a staging slot](#task-6-configure-a-staging-slot)
    - [Summary](#summary)
  - [Exercise 2: Configure local Git repository](#exercise-2-configure-local-git-repository)
    - [Task 1: Clone a GitHub repository locally](#task-1-clone-a-github-repository-locally)
    - [Summary](#summary-1)
  - [Exercise 3: Configure Git and Jenkins for continuous integration, delivery and deployment](#exercise-3-configure-git-and-jenkins-for-continuous-integration-delivery-and-deployment)
    - [Task 1: Deploy a Jenkins server in Azure](#task-1-deploy-a-jenkins-server-in-azure)
    - [Task 2: Post-deployment configuration of the Jenkins server](#task-2-post-deployment-configuration-of-the-jenkins-server)
    - [Task 3: Configure Jenkins staging deployment](#task-3-configure-jenkins-staging-deployment)
    - [Task 4: Create a GitHub Personal Access Token](#task-4-create-a-github-personal-access-token)
    - [Task 5: Configure your GitHub repo to notify Jenkins of changes](#task-5-configure-your-github-repo-to-notify-jenkins-of-changes)
    - [Task 6: Check in a change to trigger Jenkins job](#task-6-check-in-a-change-to-trigger-jenkins-job)
    - [Task 7: Manually deploy to production](#task-7-manually-deploy-to-production)
    - [Summary](#summary-2)
  - [After the hands-on lab](#after-the-hands-on-lab)
    - [Task 1: Delete Resources](#task-1-delete-resources)

<!-- /TOC -->


# OSS DevOps hands-on lab step-by-step

## Abstract and learning objectives 

In this hands-on lab, you will implement a migration of a popular OS Support Ticket system from virtual machines, to an Azure PaaS solution with Web Apps, Azure Database for MySQL, as well as deploy Jenkins in an Azure virtual machine.

At the end of this hands-on lab, you will be better able to implement solutions that use complex open-source software (OSS) workloads using Azure.

## Overview

The scenario will challenge you to setup continuous integration and delivery of an application using open source tools such as Jenkins and GitHub along with automated deployments to Azure App Services. You will learn about continuous deployment and the benefits of staged publishing.

## Solution architecture

![Solution architecture to setup continuous integration and delivery of an application using open source tools such as Jenkins and GitHub with automated deployments to Azure App Services.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image2.png "Solution architecture diagram")

## Requirements

1.  An Azure subscription

2.  A GitHub account

### Help References
|    |            |       
|----------|:-------------:|
| **Description** | **Links** |
| Jenkins Documentation | <https://jenkins.io/doc/> |
| GitHub Documentation | <https://help.github.com/> |
| Azure Web Apps Documentation | <https://azure.microsoft.com/en-us/services/app-service/web/> |
| Azure Database for MySQL | <https://docs.microsoft.com/en-us/azure/mysql/> |
| Azure Database for MySQL | <https://docs.microsoft.com/en-us/azure/mysql/howto-create-manage-server-portal/> |
| Azure Database for MySQL | <https://docs.microsoft.com/en-us/azure/mysql/howto-manage-firewall-using-portal/> |
| Connect Azure Web App to Azure Database for MySQL | <https://docs.microsoft.com/en-us/azure/mysql/howto-connect-webapp/> |
| App Service for Linux | <https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-intro/> |
| Azure CLI | <https://docs.microsoft.com/en-us/cli/azure/install-azure-cli/> |


## Exercise 1: Deploy the Web Application and Database to Azure

Duration: 60 minutes

In this exercise, you will deploy the web application and database to Azure using Azure App Service and Azure Database for MySQL. The first steps will be to build the MySQL DB and then import the data using MySQL Workbench. Then, you will create the Azure Web App and connect it to GitHub to download the app using a Docker Container with PHP 7.0.

### Task 1: Create the MySQL database

1.  From the Azure portal, select on the Cloud Shell icon on the top navigation. Select Bash and mount a storage account if you already haven't done so.

    ![Screenshot of the Launch Cloud shell icon.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image17.png "Launch Cloud shell icon")

2.  Execute the following command to create a resource group to contain the MySQL DB:

    ```
    az group create --name OsTicketPaaSRG --location "East US"
    ```

3.  Execute the following command to create a MySQL Database:

    >**Note**: You must choose a unique name for the MySQL server. Replace **osticketsrv01** with a more unique value.

    ```
    az mysql server create --resource-group OsTicketPaaSRG --name osticketsrv01 --location "East US" --admin-user demouser --admin-password demo@pass123 --sku-name B_Gen5_2 --storage-size 51200 --ssl-enforcement Disabled
    ```
    
4.  Add an open firewall rule to the database by executing the following command. Ensure you replace the server name with the unique value from the previous step.
    
    ```
    az mysql server firewall-rule create --resource-group OsTicketPaaSRG --server-name osticketsrv01 --name Internet --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255
    ```
5.  Once the MySQL database has been deployed, locate and open it from the **OsTicketPaaSRG** resource group using the Azure Portal.

6.  Choose **Connection Strings**.

    ![Under Settings, Connection strings is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image18.png "Settings section")

7.  Locate the Web App script, and press the **Select the copy** button.

    ![The Web App script\'s copy button is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image19.png "Web App script")

8.  Open a new notepad window and paste this into a new file to retain this string and more information in the next few steps. Update the **database** section to **osTicket** and the **password** section to **demo\@pass123**.

9. Select overview for the MySQL server.

    ![On the Azure Database for MySQL Server blade, Overview is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image21.png "Azure Database for MySQL Server blade")

10. Notice the **Server name** and **Server Admin Login** name. 

    ![Under Essentials, the Server name and Server admin login name are circled. The Server name is osticketmysql.mysql.database.azure.com, and the login name is demouser\@osticketmysql](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image22.png "Essentials section")

11. Here is a sample of what your connection string will be similar to.

    ![In the Notepad window, the Web App Script displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image20.png "Notepad window")

11. Scroll down, and notice that there are currently four databases that are running on your server.

    ![In the Databases section, MySQL databases has a number 4.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image23.png "Databases section")

### Task 2: Restore the osTicket database to MySQL PaaS

1.  Navigate to the following URL and download the associated file to a local folder **C:\\HOL**:

    <https://cloudworkshop.blob.core.windows.net/oss-devops/mysqlcluster.sql>

2.  On the **LABVM**, choose Start and then, locate the MySQL Workbench.

    ![On the Start menu, MySQL Workbench 6.3 CE is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image24.png "Start menu")

3.  Choose the Plus sign next to MySQL Connections on the Workbench.

    ![The plus sign next to MySQL Connections is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image25.png "MySQL Connections option")

4.  Enter the following information to configure to connect to your Server:

    -   Connection Name: **\<enter your MySQL Server DNS Name -- found in the connection string>**

    -   Connection Method: **Standard TCP/IP**

    -   MySQL Hostname: **\<enter your MySQL Server DNS Name -- found in the connection string \>**

    -   MySQL Server Port: **3306**

    -   Username: **\<enter your user name -- found in the connection string\>**

    -   Password: **Select Store in Vault: demo\@pass123**

        ![Fields in the Setup New Connection dialog box are set to the previously defined settings.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image26.png "Setup New Connection dialog box")

5.  Once configured, choose the Test Connection Button.

    ![Screenshot of the Test Connection button.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image27.png "Test Connection button")

6.  If configured correctly you will receive a message: **Successfully made the MySQL Connection**, select **OK**.

    ![A MySQL Workbench success popup displays, informing you that the MySQL connection was successfully made.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image28.png "MySQL Workbench success popup")

7.  Choose **OK** to save the connection that you just configured.

8.  The Connection will appear. Double-click to start a session with the MySQL database server running on the Azure PaaS.

    ![Screenshot of the Connection option.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image29.png "Connection option")

9.  Once the Workbench loads, choose **Server Status**. Review the details of the MySQL PaaS Server.

    ![On the MySQL Workbench, under Management, Server Status is selected. In the pane, details for osticketmysql.mysql.database.azure.com display.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image30.png "MySQL Workbench")

10. This is the part of the lift and shift where we will restore the existing database for the application. Choose the **Data Import/Restore** button.

    ![Under Management, Data Import/Restore is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image31.png "Management section")

11. On the Data Import screen, select the **Import from Self-Contained File**, and select the **c:\\HOL\\mysqlcluster.sql** datafile (or wherever you stored the file you previously downloaded).

    ![On the Data Import page, on the Import from Disk tab, under Import options, Import from Self-Contained File is selected, and the location is C:\\HOL\\mysqlcluster.sql.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image32.png "Data Import page")

12. Choose **New...**, next to the **Default Schema to be Imported To**.

    ![On the Data Import page, on the Import from Disk tab, the New button is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image33.png "Data Import page")

13. On the Create Schema menu, type **osTicket** and select OK.

    ![Name of schema to create field, osticket is typed.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image34.png "Name of schema to create field")

14. MySQL Workbench will create the Schema (database), on the server for you and select it as the Default Target Schema for the restore.

    ![The Default Target Schema field is set to osticket.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image35.png "Default Target Schema field")

15. Choose **Start Import** after reviewing the screen.

    ![On the Data Import page, on the Import from Disk tab, the Start Import button is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image36.png "Data Import page")

16. Once the restore is completed, the following screen will appear:

    ![On the Data Import page, Import Progress tab, the status shows as import completed.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image37.png "Data Import page")

17. Move back to the Schemas area of the MySQL Workbench, and choose the refresh icon.

    ![Under Schemas, both osticket and the refresh icon are selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image38.png "Schemas section")

18. The tables of the database now appear since they have been restored. Locate the **ost\_user** table, **right-click**, and select **Select Rows -- Limit 1000**.

    ![In the List of database tables, ost\_user is selected, and from its right-click menu, Select Rows - Limit 1000 is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image39.png "List of database tables")

19. This will launch a query in the Workbench and list all the users on the system.

    ![On the ost\_user tab, the query displays in the pane above, and results display in the Results Grid below.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image40.png "ost_user query results")

20. Move back to the Azure portal, and choose Overview for the MySQL server.

    ![On the Azure Database for MySQL server blade, Overview is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image21.png "Azure Database for MySQL server blade")

21. Scroll down and notice now, there are five databases and the addition of the **osticket**.

    ![In th eDatabases section, under MySQL databases, the number 5 now displays. Under Name, the following five databases are listed: information\_schema, mysql, osticket, performance\_schema, and sys.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image41.png "Databases section")

### Task 3: Create the Web App

1.  From the Azure portal, choose on the Cloud Shell icon on the top navigation.

    ![Screenshot of the Launch Cloud shell icon.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image17.png "Launch Cloud shell icon")

2.  Execute the following command to create a Linux-based App Service Plan for the new web app:

    ```
    az appservice plan create -n OsTicket -g OsTicketPaaSRG --is-linux -l "East US 2" --sku S1 --number-of-workers 1
    ```

3.  Execute the following command to create a new web app configured for PHP 7.0 inside of the new app service plan. The name of the web app must be unique, so specify some numbers at the end to make it a more unique value.

    ```
    az webapp create -n osTicketsystem -g OsTicketPaaSRG -p OsTicket -r "php|7.0"
    ```

4.  Once the deployment has completed, open the **OsTicketPaaSRG** resource group. Notice there are now three objects: **Azure Database for MySQL server, App Service plan** and the **App Service** web app.

    ![On the Resource group blade, Overview is selected. Under Essentials, a callout points to the OsTicket App Service plan.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image42.png "Resource group blade")

### Task 4: Configure the osTicket Web App

1.  Open the Web App using the Azure portal to click on the App Service. Notice the details of the application including the **URL**.

    ![On the App Service blade, Overview is selected. Under Essentials, a callout points to the URL live link http://osticketsystem.azurewebsites.net.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image43.png "App Service blade")

2.  If you select the **URL**, the default webpage will load indicating our App Service app is up and running.

3.  In the Azure portal, select **Application settings** in the Settings area

    ![Under Settings, Application settings is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image44.png "Settings section")

4.  Locate the Connection Strings section. Choose **+ Add new connection string** and enter the name **osticket** and copy the connection string from notepad into the **value area**. Select **MySQL** in the **TYPE** dropdown list next to the string. Select **Save**.

    ![Under Connection strings, the osticket connection string displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image45.png "Connection strings section")

5.  Open a new browser tab and connect to <https://github.com/opsgility/osTicket>. This is a public repo for the OsTicket software. Sign in to your GitHub account or create a new one.

    ![On the GitHub webpage, a code tab displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image46.png "GitHub webpage")

6.  On this page locate and then select the **Fork** button (upper corner).

    ![Screenshot of the Fork button.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image47.png "Fork button")

7.  If prompted, select your personal account when prompted with **"Where should we fork this repository?"**

    ![Under Forking opsgility/osticket, a refresh button displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image48.png "Refresh icon")

8.  After the repo is forked to your GitHub account, scroll down and locate the **include** folder and select it.

    ![In a list of folders, the Include folder is selected..](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image49.png "List of folders")

9.  Once in the **include** folder, scroll down and locate the file named **ost-config.php**.

    ![In a list of files, ost-config.php is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image50.png "list of files")

10. The file will open in the browser. Select the **Pencil** icon to edit this file.

    ![Screenshot of the Pencil (edit) icon.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image51.png "Edit icon")

11. The file with open in an editor. Scroll down to the Database options area of the file. Update the text in this file with your MySQL database settings from your notepad file. The **DBHOST** name and the **DBUSER** should be updated. See below for the before and after comparison.

    Before:

    ![Screenshot of the Github Before window. At this time, we are unable to capture all of the information in the Github window. Future versions of this course should address this.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image52.png "Github Before window")

    After:

    ![Screenshot of the Github After window. At this time, we are unable to capture all of the information in the Github window. Future versions of this course should address this.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image53.png "Github After window")

1. Once you have updated the text, scroll down enter a command and choose **Commit changes**.

    ![The text field under Commit changes reads, \"Updated MySQL Server Settings.\" The option to Commit directly to the master branch is selected, as is the Commit Changes button at the bottom. ](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image54.png "Commit changes section")

### Task 5: Configure Deployment Center

Since you will be leveraging Jenkins to deploy the source code, you must first configure Deployment Center.

1.  In the Azure portal, choose **Resource groups \> OsTicketPaaSRG** and then select the **App Service** to open the settings of the App Service.
   
2.  Select **Deployment Center** and click on **FTP**.

3.  Click the **Dashboard** button at the bottom of the screen.

4.  The Application Credentials will be-auto generated and your configuration will be similar to the one shown below.

    ![Screenshot showing how to configure the Deployment Center FTP credentials that will be used by Jenkins.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image107.png "Deployment Center FTP credentials blade")

5.  Close the FTP blade.

### Task 6: Configure a staging slot

1.  In the Azure portal, select **Resource groups \> OsTicketPaaSRG** and select the App Service to open the settings of the App Service.

2.  Choose **Deployment slots (Preview)** under the **Deployment** category.

    ![Screenshot showing where the Deployment slots menu item is located in the Azure Portal.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image56.png "Deployment slots")

3.  Choose **Add Slot**.

    ![Screenshot showing how to add a deployment slot.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image57.png "Adding a slot")

4.  Name the slot **Staging**, specify the **osTicketsystem** as the **CClong settings from** (this will copy over all the variables and their values we defined previously as well as the connection string), and select **Add** to create the deployment slot.

5.  Click **Close** to close the **Add a slot** panel.

6.  Select on the deployment slot once it shows up in the list.
    
    ![Screenshot of the deployment slots.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image58.png "Deployment slots")

7.  Once the staging slot has been created, select its name. On the **Overview** link, choose on the **URL** from the staging slot's essentials pane.

    ![Screenshot of the link to the staging deployment slot.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image59.png "Overview blade")

8.  At this time, no code has been deployed to either production or the staging slot we just created. Both URLs will have the default website like the one below. You will be leveraging Jenkins and GitHub in the exercises that follow to deploy the website.

    ![Screenshot of the default App Service deployment.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image60.png "Default app service page")

### Summary

In this exercise, you used several Azure Platform as a Service (PaaS) components to configure a Web Application. The Web App will use Azure Storage as well as MySQL for data. You also configured a staging slot for the Web Application with duplicate settings. The actual deployment of the Web Application will happen in an upcoming exercise.

## Exercise 2: Configure local Git repository

Duration: 10 minutes

In this exercise, you will use the forked GitHub repository from the previous exercise and clone it locally so that you can configure your web app.

### Task 1: Clone a GitHub repository locally

1.  Browse to <https://github.com/>, and login with your GitHub credentials.

2.  Navigate to the **osTicket** repository that you forked in the previous exercise.

3.  Clone the site locally on your computer for future changes by executing the commands and starting a console/terminal session containing the Git client.

    ```
    mkdir repos

    cd repos

    git clone https://github.com/[**YOUR_GITHUB**_**USERNAME**]/osTicket
    ```

### Summary

In this exercise, you forked a GitHub repository and cloned it locally, so you can configure your web app.

## Exercise 3: Configure Git and Jenkins for continuous integration, delivery and deployment

Duration: 90 minutes

In this exercise, you will configure a Jenkins server in Azure and leverage it along with Git to setup continuous integration & delivery of your Web Application. You will be pulling source code from a GitHub repository and configuring Jenkins to build and deploy the code to your Staging slot before it is pushed to production (manually).

### Task 1: Deploy a Jenkins server in Azure

Jenkins is an open source continuous integration tool written in Java. It provides continuous integration services for software development. It is a server-based system running in a servlet container such as Apache Tomcat. In this exercise, you will deploy a Jenkins Server in Azure leveraging a prebuilt virtual machine image from the Azure marketplace.

1.  If you are leveraging a virtual machine in Azure as your lab machine, first connect to it via RDP Otherwise to deploy a Jenkins Server instance in Azure, browse to <https://portal.azure.com>. Choose **+Create a resource**, type **Jenkins** in the search box, and hit **Enter**.

    ![In the Azure Portal, Create a resource is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image61.png "Azure Portal")

2.  You will notice that there are numerous preconfigured Jenkins servers available in the marketplace, for the purposes of the labs, choose the one submitted by **Microsoft** and select **Create**.

    ![In the Everything blade, Jenkins is selected under Results.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image62.png "Everything blade")

3.  Specify the following on the **Basics** blade, and choose **OK:**

    -   Name: **jenkins**

    -   User name: **demouser**

    -   Password: **demo\@pass123**

    -   Resource group: **JenkinsRG**

    -   Location: **Location nearest you.**

        ![In the Basics blade, fields are set to the previously defined settings.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image63.png "Basics blade")

4.  This will present the **Additional Settings** blade. Select **Size** and choose the **DS1\_V2 Standard** size for the VM, and choose **Select.**
    
    ![In the additional settings blade, specifying the VM size.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image64.png)

5.  Choose the **Configure subnets** field to view the existing subnet configuration and leave the values to the defaults and select **OK**. Then, enter a unique name in the **Domain name label field**, and choose **OK** on the **Additional** **Settings** blade.

    ![In the configure subnets blade, specifying the subnet address space.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image65.png)

6.  On the **Integration Settings** blade, leave the default values, and select **OK**.

7.  Choose **OK** on the **Summary** blade, and then select **Create** on the **Buy** blade to start the provisioning of your Jenkins server.

    ![The Deploying Jenkins image displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image66.png "Deploying Jenkins image")

### Task 2: Post-deployment configuration of the Jenkins server

Go to the Overview page of the Jenkins VM, copy the DNS name, and open the URL in the browser (for example, [http://osTicketjenkins.southcentralus.cloudapp.azure.com/](http://osticketjenkins.southcentralus.cloudapp.azure.com/)). The Jenkins console is inaccessible through unsecured HTTP so instructions are provided on the page to access the Jenkins console securely from your computer using an SSH tunnel. After that, you will update the OS and Jenkins to the latest as well as install other tools needed for our scenario.

1.  Using the FQDN you defined in the previous exercise, browse to your Jenkins portal.

    ![The Jenkins on Azure Log In window displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image67.png "Jenkins on Azure Log In window")

2.  Set up the tunnel by opening a PowerShell command prompt on your local computer and copying the ssh command on the browser page from the command line replacing username with the name of the virtual machine admin user chosen earlier.

    ```
    ssh -L 127.0.0.1:8080:localhost:8080 demouser@{yourvmname}.{yourvmregion}.cloudapp.azure.com
    ```

3.  When prompted, enter the password you previously chose. Keep the command line window open.

4.  Get the initial password needed for the next step by running the following command in the command line while still connected through SSH:

    ```
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
5.  Copy the password shown to your clipboard, as you will need it to initially login to the Jenkins interface.

6.  Open a web browser, and navigate to <http://localhost:8080/> on your local machine. If a **Getting Started** window is presented, choose the **X** in the top of the screen to close it. Then, select the **Start using Jenkins** button.

    ![Screenshot of Jenkins immediately after logging in.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image68.png "Jenkins home page")

7.  Once logged into the portal, notice the version of Jenkins (**2.150.1** as of the writing of this lab). Choose **Manage Jenkins** for more details.

    ![On the Jenkins dashboard, Manage Jenkins is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image69.png "Jenkins dashboard")

8.  Here, you will get more information on any update that may be available for the server instance itself or for Plugins.

9.  Select **Manage Jenkins** on the left-hand side followed by **Manage Plugins**.

    ![The previously defined options are selected on the Jenkins dashboard.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image70.png "Jenkins dashboard")

10. There are three Plug-Ins you need to install for the next exercises. Choose the Available tab, and type **github** in the Filter field. Select the check box for **GitHub**, and choose **Install without restart**. Its version is at 1.29.3 as of this writing. You may need to scroll down to find it.

    ![A screenshot showing searching by GitHub](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image71.png "Filter box")

11. Next, choose the Available tab and type **Workspace Cleanup**. Choose the check box for **Workspace Cleanup** and select **Install without restart**.

    ![The previously defined options are selected on the Available tab.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image72.png "Available tab")

12. Next, choose the Available tab, and type **ftp**. Select the check box for **Publish Over FTP**, and choose **Download now and install after restart.**

    ![On the Available tab, the previously defined options are selected. ](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image73.png "Available tab")

13. Check the box to Restart Jenkins.

    ![The Installing Plugins/Upgrades page displays with Publish Over FTP and the checkbox for Restart Jenkins selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image74.png "Installing Plugins/Upgrades page")

14. Once the restart has completed, you will be redirected to the login page once again. (*You may need to refresh the browser page to update the install status.*)

15. Log back into Jenkins portal with the **admin** account using the password you copied earlier.

16. Navigate to **Manage Jenkins** followed by **Global Tool Configuration**.

    ![On the Jenkins dashboard, the previously defined options are selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image75.png "Jenkins dashboard")

17. Under **Git**, ensure the **Name** is set to ***Default***, and the **Path to Git executable** is set to **git**.

18. Select **Save**.

### Task 3: Configure Jenkins staging deployment

You are now ready to define your staging deployment job.

1.  Login to your Jenkins portal with the **admin** account.

2.  You will first configure your FTP Plugin with the information from your Azure App Service. Choose **Manage Jenkins** followed by **Configure System.**

    ![On the Jenkins dashboard, the previously defined options are selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image76.png "Jenkins dashboard")

3.  Scroll down to the section titled **Publish over FTP**, and choose **Add**.

    ![In the Publish over FTP section, the Add button is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image77.png "Publish over FTP section")

4.  The information that is needed here can be obtained from the staging slot Web App in the Azure portal.

    ![Screenshot showing the deployed resources including the staging slot from the Azure portal.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image108.png "Staging slot")

5.  After selecting the staging slot, click on **Deployment Center** and then **FTP** and then click the **Dashboard** button.

    ![Screenshot showing the FTP credentials to be used from the Azure portal.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image107.png "FTP credentials")

6.  Update the Plug-In settings with the following information, and click **Save** (after confirming the connection is successful by clicking **Test Configuration**):

    -   Name: **Staging Slot for Web App**

    -   Hostname: \<**ftp hostname from web app staging slot settings**\>

        >**Note**: Be sure not to include the **ftps://** portion of the hostname.

    -   Username: \<**username from web app staging slot settings**\>

    -   Password: \<**password from the web app staging slot settings**\>

    -   Remote Directory: \<**remove any path after the hostname above and paste it into this field (i.e. /site/wwwroot**>

        >**Note**: Be sure to remove any path information from the **Hostname** field and place it into the **Remote Directory** field.

        ![Fields in the Publish over FTP dialog box are set to the previously mentioned settings.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image79.png "Publish over FTP dialog box")

7.  On the Welcome page, select the **create new jobs** link.

    ![The create new jobs link is selected on the Jenkins dashboard.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image80.png "Jenkins dashboard")

8.  Choose **Freestyle project** and name the project **Deploy to Staging**, and choose **OK**.

    ![The previously mentioned options are selected on the Jenkins New Item page.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image81.png "Jenkins New Item page")

9.  On the **Source Code Management** tab, choose Git and specify your GitHub repository URL. Choose **Add** to configure your credentials.

    ![Screenshot of the Source Code Management panel.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image82.png "Source code management")

    ![Fields in the Jenkins Credentials Provider dialog box are set to the following settings: Domain, Global credentials (unrestricted); Kind, username with password; Password, use your GitHub password);](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image83.png "Jenkins Credentials Provider dialog box")

    ![Screenshot of the configured Jenkins credentials.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image84.png "Jenkins credentials")

10.  In Build Triggers, check **GitHub hook trigger for GITScm polling**.

    ![The previously mentioned checkbox is selected in the Build Triggers section.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image85.png "Build Triggers section")

11. In the **Build** section, from the **Add build step** dropdown list, choose **Send files over FTP**. Be sure to choose the FTP you defined earlier, specify **\*\*** for the **Source files** and **/site/wwwroot** for the **Remote directory**.

    ![Fields in the Send files over FTP section are set to the previously defined settings.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image86.png "Send files over FTP")

12. Next, let's add a verification step after our website deploys. Click the **Add build step** dropdown button and select the **Execute shell** option.

    ![Add an additional build step for verification that our website deployed successfully.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image115.png "Add a build step")

13. In the **Command** window, copy and paste the following text. Replace the **{staging-url}** with the staging URL of your site.

    ```bash
    response=$(curl -s -o /dev/null -w "%{http_code}\n" https://{staging-url})
    if [ "$response" != "200" ]
    then
      exit 1
    fi
    ```

    ![An example of the command.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image116.png "Command example")

14. Choose **Save** to save your changes.

    ![Screenshot of the Save button.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image87.png "Save button")

15. Although you specified to run this project whenever a check-in was done in GitHub, you can force the job to run. Choose **Build Now** on the project page.

    ![Build Now is selected on the Jenkins Deploy to Staging page.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image88.png "Jenkins Deploy to Staging page")

16. You will see an entry in Build History with your build number. Hover over the number to get a dropdown list of options. Choose **Console Output**.

    ![In the Build History section., the menu for number one displays with Console Output selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image89.png "Build History section.")

17. Review the Console Output for any errors etc. You can see in the Console Output where the call to your GitHub repo is made and where it is synced on your Jenkins server. You can also see the commands specified and their output.

    ![Screenshot showing the console output of the Jenkins deployment.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image90.png "Console output")

18. Once the job has completed, you can verify the deployment by browsing to the URL of the staging slot.

    ![Screenshot showing how to navigate to the staging version of the website.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image91.png "App service overview blade")

19. You will notice the site has been deployed.

    ![Screenshot showing the deployed website.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image92.png "Deployed website")

20. Choose the Sign in link.

    ![Screenshot of the Sign in link.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image93.png "Sign in link")

21. Locate **I'm an agent**, and select the **sign in here** link.

    ![On the Sign in to Microsoft Cloud Workshop page, next to I\'m an agent, the link to sign in here is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image94.png "Sign in page")

22. At the OsTicket screen, enter the **username** and **password** and choose **Log In**.

    -   Username: ***demouser***

    -   Password: ***demo\@pass123***

    ![The osTicket log in webpage displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image95.png "osTicket log in webpage")

23. Once logged into the OsTicket system, select **My Tickets**.

    ![On the osTicket page, tickets tab, My Tickets (4) is selected.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image96.png "osTicket page, tickets tab")

24. On the **My Tickets** screen, move through to one of the tickets. Congratulations, you have successfully deployed the application.

### Task 4: Create a GitHub Personal Access Token

For an upcoming task, you will need to generate a GitHub Personal Access Token. This token will allow GitHub to send Jenkins data to automatically trigger builds. 

1.  Open a new browser tab and log into your GitHub repo ([https://github.com/\<username\>/osTicket](https://github.com/%3cusername%3e/osticket)), and choose your profile (top-right portion of the screen) followed by **Settings**.

    ![Screenshot of GitHub profile Settings menu.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image109.png "GitHub profile settings")

2.  Select the **Developer settings** item from the left-side menu.

    ![Screenshot of personal settings menu.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image110.png "Developer settings")

3.  When the Developer settings appear, select the **Personal access tokens** item from the left-side menu. Then, click the **Generate new token** button at the top of the screen to generate a new token.

    ![Screenshot of Developer settings menu.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image111.png "Personal access tokens")

4.  On the **New personal access token** screen, enter a name such as **Jenkins Builds** in the **Token description field** and be sure to check the box for **admin:org_hook** under the **Selected scopes** section. 

    ![Screenshot of Personal access tokens page.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image112.png "Personal access tokens page")

5.  Then click the **Generate token** button at the bottom of the page to complete the token generation process.

6.  The page will refresh, and your new Personal access token will be displayed near the top. Be sure to copy this token (yours will be different) and paste it into a notepad for later use as you will not be able to retrieve it from GitHub once you leave this page.

    ![Screenshot of the new Personal access token.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image113.png "New Personal access token")

### Task 5: Configure your GitHub repo to notify Jenkins of changes

You will now configure your GitHub repository to notify your Jenkins server when a change has occurred, so the Jenkins Job is kicked off automatically.

1.  Log into your GitHub repo ([https://github.com/\<username\>/osTicket](https://github.com/%3cusername%3e/osticket)), and choose **Settings** followed by **Webhooks**.

    ![Screenshot of GitHub repository Settings showing where Webhooks menu option is located.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image97.png "Gitub settings")

2.  Select **Add webhook**, and set the following fields:

    - **Payload URL** - This is the URL to your Jenkins environment appended with **/github-webhook/**. For example: **http://jenkins99.southcentralus.cloudapp.azure.com/github-webhook/**

        >**Note the trailing slash (/)** -- make sure it is included in your URL:

    - **Content type** - Set this value to **application/json**.

    - **Secret** - Paste your Personal access token that you saved from the previous task.

    ![Add webhook page.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image98.png "Add webhook")

3.  Finally, click the **Add webhook** button to finalize the creation of the new webhook.
  
4.  The page will refresh, and if we've done everything correctly, you'll see a confirmation that the webhook tested successfully as indicated by the green checkmark.

    ![Successful webhook confirmation.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image114.png "Successful webhook confirmation")

### Task 6: Check in a change to trigger Jenkins job

You will now check in a change to your Web Application code that will trigger your Jenkins job by editing the file that updates the home page.

1.  Open the following file in an editor such as Visual Studio Code **osTicket\\include\\client \\footer.inc.php**.

2.  Choose Ctrl+F, and find the following HTML code:

    ```
    All rights reserved.</p>
    ```

3.  Make a modification to the text and select FileSave.

    ```
    All rights reserved. Run on Azure App Services!</p>
    ```

4.  Move to a **Git Shell**, and execute the following git commands from the directory where the repo resides to push the update to your repository in GitHub.

    ```
    git config user.name "Your Name"

    git config user.email "your@email.com"

    git add -A

    git commit -m "updated model"

    git push
    ```

>**Note**: You may be required to authenticate using your github.com username and password.

5.  Now that a change has been checked in, there are various places to check that the process has worked. Start by checking that the staging website has been updated. 

>**Note**: The deployment will take a few moments.

   ![Screenshot showing the updated homepage with the edited content.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image100.png "Support center home page")

6.  You can also check logs in Jenkins. From the home page of your Jenkins portal, select the drop-down of your Jenkins job, and choose **GitHub Hook Log**.

    ![On the Jenkins portal homepage, the URL for the page is selected. In addition, Deploy to Staging is selected, and from its drop-down menu, GitHub Hook Log is selected. ](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image101.png "Jenkins portal homepage")

7.  You can also check the Build History log and Console Output of the Jenkins project.

    ![On the Jenkins portal homepage, Build History displays.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image102.png "Jenkins portal homepage")

### Task 7: Manually deploy to production

Up to this point, you have automated the integration and delivery to your staging slot. You will now move those changes into the production slot *manually*. Leveraging the tools you have configured so far, you could ultimately automate this last step to get continuous deployment.

1.  To push the changes from your staging slot to production, in the Azure portal (<https://portal.azure.com>), select **Resource groups \> OsTicketPaaSRG** and then select your App Service.

    ![Screenshot showing how to navigate to the app service.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image103.png "Choosing the web app")

2.  Select **Swap** at the top of the screen.

    ![Screenshot showing how to navigate to swap the deployment slots](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image104.png "Swapping the web app")

3.  Be sure that **Staging** is listed as the **Source** and **production** as the **Destination**, and choose **OK**.

    ![The previous fields are set to the previous settings in the Swap blade.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image105.png "Swap blade")

4.  Once the Swap has completed, verify the changes have been pushed to your production slot by browsing to your production URL.

    ![Screenshot of the production url version of the website.](images/Hands-onlabstep-by-step-OSSDevOpsimages/media/image106.png "Production url")

### Summary

In this exercise, you leveraged Azure, Jenkins and GitHub to setup continuous integration, delivery, and deployment for your web site. You built a scenario where your code changes were automatically pushed out to a staging slot after collecting assets from GitHub.

## After the hands-on lab 

Duration: 10 minutes

### Task 1: Delete Resources

1.  Now that the Hands-on lab is complete, go ahead and delete all the Resource Groups that were created for this lab. You will no longer need those resources and it will be beneficial to clean up your Azure Subscription.

You should follow all steps provided *after* attending the Hands-on lab.
