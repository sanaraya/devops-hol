![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

<div class="MCWHeader1">
OSS DevOps 
</div>

<div class="MCWHeader2">
Before the hands-on lab setup guide    
</div>

<div class="MCWHeader3">
August 2018
</div>


Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at <https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx> are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [OSS DevOps before the hands-on lab setup guide](#oss-devops-before-the-hands-on-lab-setup-guide)
  - [Requirements](#requirements)
  - [Before the hands-on lab](#before-the-hands-on-lab)
    - [Task 1: Create a virtual machine to execute the lab](#task-1-create-a-virtual-machine-to-execute-the-lab)
    - [Task 2: Install Chrome](#task-2-install-chrome)
    - [Task 3: Install the MySQL Workbench](#task-3-install-the-mysql-workbench)

<!-- /TOC -->

# OSS DevOps before the hands-on lab setup guide 

## Requirements

1.  An Azure Subscription

2.  A GitHub account

## Before the hands-on lab

Duration: 30 Minutes

### Task 1: Create a virtual machine to execute the lab

1.  Launch a browser and navigate to <https://portal.azure.com>. Once prompted, login with your Microsoft Azure credentials. If prompted, choose whether your account is an organization account or just a Microsoft Account.

2.  Click on **+Create a resource**, and in the search box type in **Visual Studio Community 2017 on Windows Server 2016 (x64)** and press enter. Click the Visual Studio Community 2017 image running on Windows Server 2016 and with the latest update.

3.  In the returned search results, click the image name.

4.  Click **Create**.

5.  Set the following configuration on the Basics tab and click **OK**:

    -   Subscription: **If you have multiple subscriptions, choose the subscription to execute your labs in.**

    -   Resource Group: **OPSLABRG**

    -   Virtual machine name: **LABVM**
   
    -   Region: **Choose the closest Azure region to you.**

    -   VM disk type: **SSD**

    -   Size: **DS1\_V2 Standard**

        >**Note**: You may have to click the View All link to see the instance sizes.
    
    -   Username: **demouser**

    -   Password: **demo\@pass123**

    -   Select the **Allow selected ports** radio button and check **RDP (3389)**.

6.  Accept the  default values on the remaining blades and click **Review + create**. On the Review + create page click **Create**. The deployment should begin provisioning. It may take 10+ minutes for the virtual machine to complete provisioning.

    >**Note**: Please wait for the LABVM to be provisioned prior to moving to the next step.

7. Move back to the Portal page on your local machine and wait for **LABVM** to show the Status of **Running**. Click **Connect** to establish a new Remote Desktop Session.

8. Depending on your remote desktop protocol client and browser configuration you will either be prompted to open an RDP file, or you will need to download it and then open it separately to connect.

9. Log in with the credentials specified during creation:

    a.  User: **demouser**

    b.  Password: **demo\@pass123**

16. You will be presented with a Remote Desktop Connection warning because of a certificate trust issue. Click **Yes** to continue with the connection.

17. When logging on for the first time you will see a prompt on the right asking about network discovery. Click **No**.

18. After a few moments, notice that Server Manager opens by default. Close this window.

19. On the right side of the pane, click **Off** next to **IE Enhanced Security Configuration**.

20. Change to **Off** for Users and click **OK**.

### Task 2: Install Chrome

1.  While logged into **LABVM** via remote desktop, open Internet Explorer and navigate to: <https://www.google.com/chrome/>.

2.  Click the **Download Chrome** button.

3.  Press **Accept and Install** when prompted.

4.  Press **Run** when prompted to download and begin the installation.

### Task 3: Install the MySQL Workbench

1.  While logged into **LABVM** via remote desktop, open Chrome and navigate to: <https://downloads.mysql.com/archives/workbench/>. 

    Select "Microsoft Windows" from the "Operating System:" dropdown and download the executable package. After the download is finished, click **Run** to execute it.

2.  Follow the directions of the installer to complete the installation of MySQL Workbench.

3.  After the installation is complete, **reboot** the machine.

You should follow all steps provided *before* attending the Hands-on lab.
