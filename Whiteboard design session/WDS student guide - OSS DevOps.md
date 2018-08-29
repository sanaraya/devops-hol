![](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/master/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")


<div class="MCWHeader1">
OSS DevOps
</div>

<div class="MCWHeader2">
 Whiteboard design session student guide
</div>

<div class="MCWHeader3">
August 2018
</div>

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.
© 2018 Microsoft Corporation. All rights reserved.

Microsoft and the trademarks listed at https://www.microsoft.com/en-us/legal/intellectualproperty/Trademarks/Usage/General.aspx are trademarks of the Microsoft group of companies. All other trademarks are property of their respective owners.

**Contents**

<!-- TOC -->

- [OSS DevOps whiteboard design session student guide](#oss-devops-whiteboard-design-session-student-guide)
    - [Abstract and learning objectives](#abstract-and-learning-objectives)
    - [Step 1: Review the customer case study](#step-1-review-the-customer-case-study)
        - [Customer situation](#customer-situation)
        - [Customer needs](#customer-needs)
        - [Customer objections](#customer-objections)
        - [Infographic for common scenarios](#infographic-for-common-scenarios)
    - [Step 2: Design a proof of concept solution](#step-2-design-a-proof-of-concept-solution)
    - [Step 3: Present the solution](#step-3-present-the-solution)
    - [Wrap-up](#wrap-up)
    - [Additional references](#additional-references)

<!-- /TOC -->

#  OSS DevOps whiteboard design session student guide

## Abstract and learning objectives 

In this whiteboard design session, you will design a plan for migrating a Linux based web application to Microsoft Azure that takes advantage of existing open-source software (OSS) skills such as usage of Jenkins. This whiteboard design session will explore options for running Linux, such as virtual machines, or Azure PaaS services, such as Web Apps and Azure Database, for MySQL.

At the end of this whiteboard design session, you will be better able to design solutions that use complex OSS workloads using Azure.

## Step 1: Review the customer case study 

**Outcome** 

Analyze your customer’s needs.
Time frame: 15 minutes 
Directions: With all participants in the session, the facilitator/SME presents an overview of the customer case study along with technical tips. 
1.  Meet your table participants and trainer 
2.  Read all of the directions for steps 1–3 in the student guide 
3.  As a table team, review the following customer case study


### Customer situation

Trey Research is an online vitamins seller that has a presence mainly in the United Kingdom and other countries/regions in Europe. They want to expand into other parts of the world starting with Asia and will soon be setting up distribution centers for their vitamins there. They have no retail stores and thus all their business comes through their website. SEO (Search Engine Optimization) is extremely important to them as is their user website experience with a heavy focus on availability and performance.

The company currently has two application teams: **Development** for their websites and **Operations** for deployments and backend servers. These are all hosted in a commercial data center. The teams have a great deal of experience in developing applications in PHP and WordPress for the LAMP stack (Linux, Apache, MySQL, and PHP). What they lack is the operational expertise and maturity to maintain their applications properly. They struggle with development and design practices, deployments, configuration management, and scaling their application to meet busy seasonal periods.

**Current situation**

Trey Research is currently running 32 web applications on a single physical 64 core server with 256GB of RAM and a multi-terabyte raid array. To facilitate fast configurations of the server, they are running Plesk which allows them to create and configure new domains rapidly.

These applications are running a combination of PHP, node.js, and WordPress. The application code is written and maintained by the developers in house, but the WordPress content that applies the look and feel to the application is maintained by a marketing firm.

On the Data Tier, Trey Research has a single MySQL database server housing several databases ranging in size from 1 GB to 50 GB. The server has 24 cores with 128GB of RAM, and it is a single point of failure. If the database or the server suffers from an outage, the sites that depend on MySQL go down hard. It is a huge customer satisfaction issue, and there have been multiple extended outages where MySQL was down for more than an hour over the past few months. Every hour the site is down costs Trey Research 100,000 EUR.

During the summer periods or extended marketing campaigns, Trey Research is finding they run out of server capacity, and some of their potential web customers experience timeouts. They also find that much of the static content such as PDF product flyers are taking an extended period to download from more remote regions, such as South East Asia, leading to poor user feedback. As they move into Asia, it is a huge concern, as they do not currently have a point of presence there.

Ursula Karalov, VP of Sales for Trey Research Vitamins states, "We just cannot afford to lose anymore sales due to our website being down or slow." She also mentioned their marketing firm determined a large number of shopping cart abandon rates correlate to slow access (greater than 2 seconds) to PDF product descriptions.

**Development practices and Site Operations**

The CTO, David Smith, wants to enable much faster deployment times for their applications and ensure that all servers in the farm have the same configurations and files deployed. "When we deploy our applications, the team manually FTP's files into our production environment." This process makes it quite difficult and is prone to errors given the different environments maintained including Test, Staging, and Production. Mr. Cross says, "This complexity means that the team is reluctant to rapidly implement hotfixes and minor releases. So right now, we release only once a quarter even when we know there are bugs that could and should be fixed in-line with our three-week sprints and biweekly bug bashes."

The in-house developers primarily use Eclipse for their development IDE. They take advantage of a combination of GitHub repos to store code and a Jenkins server with multiple nodes to do builds. They have yet to implement any deployment pipelines, as they do not have a configuration management infrastructure. It means that once Jenkins does their builds, they are rolled out manually to whichever environment they are destined for and that information is prone to human mistakes and oversights.

On the other hand, the marketing firm writes their code in markdown and provides updates to Trey Research via Dropbox, which has been a huge pain point. "It is just another place for things to get confusing with a complex folder structure that seems to be a moving target," says Tim LaMar, Lead Systems Administrator from Trey Research's Operations. "We have to look for emails from either the Project Manager or their Dev team to know that there is an update coming our way for the site." Tim and the rest of the Operations team must then go find and manually move the code into production. The Operations team is constantly being bombarded with trouble-tickets from the marketing firm for broken links, out of date PDFs, and embargoed code being live on the site when it should not yet be deployed.

Their test environment consists of developers testing code changes locally, as well as checking in changes to a GitHub repository. To deploy, Trey Research just pulls the latest bits onto the production server. It has led to numerous outages because of environmental differences on the developer's workstations versus what is on the production server.

The Trey Research CTO is open to taking advantage of Azure as a platform and wants his team to focus on what they do best, which is to write good code. He knows that the Operations team struggles to find solutions to these issues. "We must get to a point where all we do is code. If Azure and these DevOps tools can come together to make that happen, then I am going with Microsoft."

David has also expressed that if the MySQL database goes down again, it will be a resume generating event for many people on the team including him, so he has a keen interest in having better database availability design.

**Existing [www.treyresearch.com](http://www.treyresearch.com) web application solution**

![At a high level, the Current Environment is split by a firewall between Corp and DMZ. Products used include eclipse, nodeJS, php, W, MySQL, and Jenkins. At this time, we are unable to capture all of the information in the diagram. Future versions of this course should address this.](images/Whiteboarddesignsessiontrainerguide-OSSDevOpsimages/media/image2.png "Current Environment diagram")

###  Customer needs 

1.  Deploy and easily scale their web applications written in PHP, node.js, and WordPress

2.  A resilient and scalable MySQL backend configured to support an active-passive deployment

3.  A simpler method of deploying and validating code from their test environment, taking advantage of their investments in Jenkins, Git, and Dropbox

4.  No longer wants to manage build server environment on-premise

5.  Ensure that existing Development tools are retained and compatible with any new solution

6.  Avoid timeouts during busy periods while browsing their product catalog

7.  Decrease the amount of time needed for remote users in areas like Asia to download graphic-heavy, static content, and product catalogs that are PDFs

8.  Lowering the total cost of ownership of the entire solution by minimizing the management surface area required for the solution

### Customer objections 

1.  Why use Microsoft Azure? Surely, Azure is a Windows only cloud because Microsoft sells the Windows operating system.

2.  What does Microsoft know about PHP and the LAMP stack? They cannot offer support of these things whereas I know other companies that have successful deployments of this on Amazon

3.  If we move to the cloud, will we always be ready to take our customers' orders?

4.  We have a few settings in our php.ini and some of the applications use PHP extensions. They are critical to their functionality. Can we make custom settings and enable extensions if we use Azure Web Apps?

5.  We primarily use Eclipse for our development, which is what our team uses to debug issues. Can we debug our applications using Eclipse against Azure Web Apps?

6.  We cannot move to Microsoft SQL Server; we do not want to deal with the issues of the MySQL VMs anymore

### Infographic for common scenarios

![An image that shows icons for virtual machines, virtual networks, marketplace, gateways, CDN, storage and traffic manager.](images/Whiteboarddesignsessiontrainerguide-OSSDevOpsimages/media/image3.png "Common Scenarios")

![An image that shows icons for MySQL, Azure App Service, Web Apps, monitoring and the IDE Eclipse.](images/Whiteboarddesignsessiontrainerguide-OSSDevOpsimages/media/image4.png "Common Scenarios")


## Step 2: Design a proof of concept solution

**Outcome** 
Design a solution and prepare to present the solution to the target customer audience in a 15-minute chalk-talk format. 

Time frame: 60 minutes

**Business needs**

Directions: With all participants at your table, answer the following questions and list the answers on a flip chart. 
1.  Who should you present this solution to? Who is your target customer audience? Who are the decision makers? 
2.  What customer business needs do you need to address with your solution?

**Design** 
Directions: With all participants at your table, respond to the following questions on a flip chart.

**Web app infrastructure and network designs (Operations)**

Using the features of Azure and the requirements from the customer how would you design the application infrastructure and network for Trey Research while lowering the total cost of ownership of the entire solution by designing for the least complexity? Management/updating should be considered for the architecture. The use of VMs should be minimized to lessen the burden of management on the Operations team.

Make sure that your design covers the following items:

**Application**

**Task:** Design a scalable geo-distributed platform for the Trey Research web applications written in PHP, node.js, and WordPress.

1.  The solution should scale automatically and be resilient to failures

2.  Load times in locations around the world should be lessened as compared to today. Static and large files should be placed as close to the user as possible.

3.  Ensure to document (at a high-level) how the new design can handle custom settings in their php.ini and the use of PHP extensions

**Database**

**Task:** How would you design a resilient and scalable MySQL backend configured to span geographically distributed datacenters and support an active-passive configuration?

**Network**

**Task:** How would you design a hybrid network (at a high-level) that will allow you to meet all the customer requirements and support your design for moving Trey Research to Azure?

1.  Design a virtual network in Azure and establish hybrid connectivity between Trey Research on-premises to Azure

2.  Include Azure Networking features in the full design to ensure users connecting to the Web Application are routed to the front-end with the least number of hops. Ensure the load is balanced across these servers.

**DevOps in Azure implementation (Development)**

Use the features of Azure and the requirements to include Dropbox, Git, and Jenkins as a part of the solution. How would you design a DevOps pipeline workflow the Development team will use to move from Test, Staging, to Production? Make sure your design covers the following items:

**Task:** Document the workflow at a high-level to be used to implement a DevOps based Continuous Integration (CI) and Continuous Delivery/Deployment (CD) methodology at Trey Research.

1.  Where is code checked in, and how is the code built? Then, how does it deploy to the Site? Make sure to not only consider the in-house developers, but also the marketing vendors' code. Note that the marketing team is not going to move from using Dropbox.

2.  Design a "slotting" methodology using at least three slots for Test, Staging, and Production. How does code land on each of these sites? Show the full workflow from code check-in, build, deployment, and then promotion to production.

**Task:** How can Trey Research continue to use Eclipse as their development environment?

1.  Document how the tools are compatible with the new design

2.  Determine how their client can debug issues. Can we debug our applications using Eclipse against Azure Web Apps?

3.  Document how Trey Research could build nonproduction Azure Web Apps that are "proof of concepts" so they can implement their alpha code directly to Azure, by-passing the CI/CD infrastructure

**Prepare**

Directions: With all participants at your table:

1.  Identify any customer needs that are not addressed with the proposed solution
2.  Identify the benefits of your solution
3.  Determine how you will respond to the customer's objections

Prepare a 15-minute chalk-talk style presentation to the customer.

## Step 3: Present the solution

**Outcome**
 
Present a solution to the target customer audience in a 15-minute chalk-talk format.

Time frame: 30 minutes

**Presentation** 

Directions:
1.  Pair with another table
2.  One table is the Microsoft team and the other table is the customer
3.  The Microsoft team presents their proposed solution to the customer
4.  The customer makes one of the objections from the list of objections
5.  The Microsoft team responds to the objection
6.  The customer team gives feedback to the Microsoft team
7.  Tables switch roles and repeat Steps 2–6


##  Wrap-up 

Time frame: 15 minutes

Directions: Tables reconvene with the larger group to hear the facilitator/SME share the preferred solution for the case study. 

##  Additional references
|    |            |       
|----------|:-------------:|
| **Description** | **Links** |
| Web Apps overview | <https://azure.microsoft.com/en-us/services/app-service/web/> |
| Web Apps Documentation | <https://azure.microsoft.com/en-us/documentation/articles/app-service-web-overview/> |
| Create a web app from the Azure Marketplace | <https://azure.microsoft.com/en-us/documentation/articles/app-service-web-create-web-app-from-marketplace/> |
| PHP Developer Center | <https://azure.microsoft.com/en-us/develop/php/> |
| Node Developer Center | <https://azure.microsoft.com/en-us/develop/nodejs/> |
| Configure PHP in Azure App Service Web Apps | <https://azure.microsoft.com/en-us/documentation/articles/web-sites-php-configure/> |
| Azure Toolkit for Eclipse | <https://azure.microsoft.com/en-us/documentation/articles/azure-toolkit-for-eclipse/> |
| Debugging Azure Applications in Eclipse | <https://azure.microsoft.com/en-us/documentation/articles/azure-toolkit-for-eclipse-debugging-azure-applications/> |
| Continuous deployment using GIT in Azure App Service | <https://azure.microsoft.com/en-us/documentation/articles/web-sites-publish-source-control/> |
| Azure Database for MySQL | <https://azure.microsoft.com/en-us/services/mysql/> |
| Overview of the Azure Content Delivery Network (CDN) | <https://azure.microsoft.com/en-us/documentation/articles/cdn-overview/> |
| Using Azure CDN | <https://azure.microsoft.com/en-us/documentation/articles/cdn-create-new-endpoint/> |
| Pre-load assets on an Azure CDN endpoint | <https://azure.microsoft.com/en-us/documentation/articles/cdn-preload-endpoint/> |
| Using Azure Storage with a Jenkins Continuous Integration solution | <https://azure.microsoft.com/en-us/documentation/articles/storage-java-jenkins-continuous-integration-solution/> |
| Jenkins and Azure | <https://jenkins.io/blog/2016/05/18/announcing-azure-partnership/> |
| Jenkins Pipeline | <https://jenkins.io/doc/pipeline/>  <https://github.com/jenkinsci/pipeline-plugin/blob/master/TUTORIAL.md/> |
| Dropbox & Jenkins | <https://wiki.jenkins-ci.org/display/JENKINS/Publish+over+Dropbox+Plugin/> |
| GitHub Enterprise in Azure Marketplace | <https://azure.microsoft.com/en-us/marketplace/partners/github/githubenterprise/> |
| Visual Studio Team Services | <https://www.visualstudio.com/team-services/> |
| Visual Studio Team Services Integration with Jenkins | <https://blogs.msdn.microsoft.com/devops/2017/04/25/vsts-visual-studio-team-services-integration-with-jenkins/> | Setting up CI / CD with the TFS Plugin for Jenkins | <http://donovanbrown.com/post/Setting-up-CICD-with-the-TFS-Plugin-for-Jenkins/> |
