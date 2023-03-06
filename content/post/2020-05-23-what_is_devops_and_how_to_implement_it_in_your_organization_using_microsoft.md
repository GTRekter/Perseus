---
layout: post
title: "What is DevOps and how to implement it in your organization using Microsoft"
subtitle: ""
description: "DevOps is a hot topic today, but ask someone what it actually is and you’ll hear crickets. According to research conducted by Gartner, a global research and advisory firm in the United States, 90% of interviewed organizations describe themselves as DevOps..."
excerpt: ""
date: 2020-05-23T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["Office 365", "DevOps", "Microsoft Teams", "Azure"]
URL: "/2020/05/23/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/"
categories: [ Tech ]
---

_“DevOps is the union of people, processes, and products to enable continuous delivery of value to end-users.”_

Donovan Brown, Microsoft DevOps PM.

DevOps is a hot topic today, but ask someone what it actually is and you’ll hear crickets. According to research conducted by Gartner, a global research and advisory firm in the United States, 90% of interviewed organizations describe themselves as DevOps advocates while 44% openly admit that they are still trying to find out what it means. In this article, I will introduce you to the fundamental concepts of DevOps and give you a guideline on how to apply DevOps using Microsoft 365 Suite and Microsoft Azure.


# What is DevOps?

The term DevOps is a combination of Development and Operations and has its roots in the manufacturing methodologies of Toyota Production System, the Agile software methodology, and John Boyd’s OODA Loop. DevOps creates a culture of collaboration between teams that historically functioned in relative silos where the developers created applications and the IT operations teams deployed them into the infrastructure that they managed. This separation into silos, which included separate responsibilities and procedures, resulted in significant delays and inaccuracies. For example, having to create a ticket everytime you wanted to communicate with other teams.

Implementing DevOps typically involves the adoption of cloud computing, but to be effective it also requires organizational restructuring and software architecture changes. From a mindset persepctive it’s also necessary to start thinking of testing and the test code as part of the product and building in self-monitoring capabilities by leveraging powerful Continuous Integration and Continuous Delivery (CI/CD) tools.


# Where does DevOps come from?

In 1930, Toyota introduced a new production method based on the operative model “The Toyota Way” called lean production. This model is based on 14 principles and aims to provide the tools for people to continually improve their work. The most important of these principles are **Kaizen** and **Jidoka**.


## Kaizen

Kaizen means to “improve” or “to get better” in Japanese. Because every process can be improved, this principle incentivizes each employee to look for ways to improve operations at every level in the company. To succeed, Kaizen requires the setting of clear objectives and targets. It’s important to highlight that Kaizen is not just based on improvements being developed and implemented only by experts or management, but everyone.


## Jidoka

The Jidoka principle highlights quality over everything else and empowers operators to immediately stop work if they detect abnormal conditions. Toyota employees will pull a cord if there is a problem with production. This cord is nothing more than a rope, but when pulled, it instantly stops all work on the assembly line. Once all production is stopped, a team leader will immediately investigate the reason the rope was pulled and then, together with the team, solve the problem and restart production. Even if this might sound crazy and unproductive, Toyota realized that unaddressed problems on the assembly line create huge complications if left unattended.


## DevOps and OODA Loop

Devops also has its roots in the American military. Back in the 1950s, the United States Air Force’s Colonel John Boyd developed the OODA Loop to help people to work successfully in unpredictable and chaotic environments. The OODA Loop focuses on empowering people to make on-the-ground decisions with speed and decisiveness and is composed of four stages:

· Observe

· Orient

· Decide

· Act

Today we can see the OODA Loop in the Agile method of software development. Rather than allowing one design to make its way to final production, Agile advocates creating a minimum viable product as quickly as possible so that it can be tested and swiftly redesigned to better adapt to the market and its competitors.


# Implementing DevOps

Now that we know what DevOps is and the benefits of implementing DevOps practices in our companies, let’s take a look at how the implementation process would actually work. In this section, I will show you how to apply DevOps to your organization by using the following technology stack:

· Microsoft Office 365

· Microsoft Azure

· Microsoft Azure Boards

· Microsoft Azure Pipelines

· Microsoft Azure Repos

· Microsoft Azure Test Plans


# Microsoft Office 365 vs. Google Suite

Although it may be tempting to start this process with the documents you have stored on your Google Suite, I highly recommend switching to Microsoft Office 365 for the following reasons:

· Office 365 provides a wide range of desktop applications that are also available for use in a web browser. Google Suite, on the other hand, is solely web-based.

· Even if both Google and Microsoft office suites allow us to create documents, spreadsheets, and presentations, the Google ecosystem has limitations compared to Microsoft, especially for complicated Excel files with a significant number of relationships between worksheets.

· With Microsoft you can manage user and group permissions easily from the administration console as well as software licenses (figure 1).

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/1.png>)

**Figure 1: An example of Office 365 Admin Central**

· Another significant advantage of switching to Office 365 is Microsoft Teams, the collaboration hub that integrates users, conversations, online meetings, contents, and tools in a unique application. In addition to the widely used applications from Microsoft, this software offers a wide range of 3rd party extensions that are possible to integrate into Teams. For example, the development team can use the **Visual Studio Team System** (VSTS) tab to have a specific dashboard or even a Kanban board directly on Microsoft Teams. This tool will allow the team members to be updated in real-time about the status of their projects without having to navigate between websites (figure 2).

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/2.png>)

**Figure 2: An example of integration between Microsoft Teams and Microsoft VSTS**

Having every collaborative application in a single interface, along with the easy accessibility of documents viaOneDrive and SharePoint, results in an increase in performances and a decrease of wasted of time. For example, if a new team member is assigned to an existing project, it is enough to add him/her to Office 365 groups related to those projects from the Office 365 Admin Central. This will allow her/him to immediately access every repository of that project, its documentation on SharePoint or OneDrive, and its teams in Microsoft Teams. This results in a considerable saving of time and a more effective and secure process.

Another significant advantage of using Microsoft Teams is transparency. A big challenge for many organizations is keeping everyone updated on what is going on. Since teams integrate all communications into one unique software, this becomes much easier and less error prone.


# Azure

To get the best out of Azure, I recommend increasing cloud usage as well as adding a daily usage of services like Boards, Pipelines, Repos, and Test Plans (figure 3).

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/3.png>)

**Figure 3: Azure DevOps cloud services**

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/4.png>)

**Figure 4: An example of swapping between Azure development slots**

It’s also important to mention that thanks to the **Azure Active Directory**, if a new member is assigned to a specific Office 365 group, this member will also gain Azure privileges which enable him/her to access particular resources, create a new service, and much more. This feature makes the onboarding of new employees to a project much faster. Azure also offers many tools to develop applications for Linux environments as well as containers like Docker.


# Azure Boards


## Why Microsoft Azure and not AWS or Google Cloud?

AWS and the Google Cloud are also popular cloud platforms, but they are not quite as integrative or comprehensive as Azure.

· Microsoft has developed Azure DevOps to help corporations easily migrate to the DevOps culture with specific services like Board, Repos, Pipeline, Test Plan, and Artifacts.

· Microsoft Azure is highly integrated with other applications like SharePoint, Dynamics 365, and Office 365.

· It offers high availability and redundancy in data centers on a global scale, which allows it to provide an SLA (service level agreement) of 99.95%.

· Microsoft Azure offers a security control system based on the **DADSC** (Detect, Assess, Diagnose, Stabilize, and Close) approach, which provides reliable protection against data loss and is recognized by Gartner as the industry leader regarding Cloud IaaS (Infrastructure as a service).

· While its competitors have minimal and confusing documentation available online, Azure offers thousands of walkthroughs online as well as very extensive documentation that helps newcomers quickly learn the Platform.

Azure Boards is a web service that makes it easier for teams to manage their workload by splitting entire developments into several user stories (or backlog items), each one composed of small tasks and related issues. The application has visual tools like **Kanban boards** (figure 4) and **backlogs** that help teams to see and share progress easily.

Likewise, these tools help project managers to track the status and the trends of the projects thanks to its custom dashboard and integrated reporting. This service also offers various templates that differ from each other according to the related **software development framework** (Basic, Agile, Scrum, and CMMI). All of them are easy to scale as the business grows.


## Azure Boards and DevOps

Thanks to its ability to facilitate product development by incremental releases, Kanban is well suited to DevOps. Because Kanban cycle times are measured in terms of hours or a day, big and complicated developments are broken into small and easier to understand tasks (often represented by cards) arranged on a board in columns, each representing a different state. The focus in Kanban is on completing tasks, limiting work in progress, and delivering immediate value.

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/5.png>)

**Figure 4: An example of Azure Boards**


# Azure Repos

Another significant improvement is the introduction of Azure Repos. Thanks to this service it’s possible to easily access and share the code between each engineer assigned to the project. Since Azure Repos supports both **GIT** (distributed version control) and **Team Foundation Version Control** (centralized version), each project can use the **version control system** (VCS) that best fits its needs. The benefit of using a VCS are many, here a few examples:

· **Collaboration**: Everybody on the team can work freely on any file at any time. The VCS allows you to merge all the changes into a standard version removing any question of which is the latest version.

· **Storing Versions**: The VCS keeps all the past versions and variants packed up inside. When you need it, you can request any version at any time or even restore the previous version with a simple click.

· **Impossible to lose**: Since the code is online, it’s impossible to lose it even if the computer breaks.

This service is also fully integrated with Visual Studio, allowing each developer to perform the previous operations directly from Visual Studio (figure 5).

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/6.png>)

**Figure 5: An example of code uploaded on a TFVC repository from Visual Studio 2019**


# Azure Pipelines

The traditional method of code commits and merging with branches after every feature development can become very complex and can even lead to bugs. Azure Pipelines solves this problem by combining Continuous Integration and Continuous Delivery (CI and CD). Employing this web service ensures consistency and quality code by automatically building, testing, and deploying the project’s code on multiple targets (ex. registries, virtual machines, Azure services, and more). Each integration to the shared repository can be verified by an automated build and automated tests, which make the process faster and more secure (figure 6).


## Azure Pipelines and DevOps

Thanks to continuous delivery (CD), code changes are automatically prepared for a release in production. Continuous delivery can then be expanded upon Continuous Integration by automatically deploying the modifications prepared in the build stage (performed by the continuous delivery) to a testing environment or a production environment. By doing so, developers will have a deployment-ready build artifact passed through a standardized test process. The key goals of continuous integration are to improve software quality by reducing the time to find and address bugs and reducing the time to validate and release new software updates.

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/7.png>)

**Figure 6: An example of pipeline developed using Azure Pipeline**


# Azure Test Plans

In case a specific project requires manual testing, Azure Test Plans service provides all the capabilities needed for planning manual testing, user acceptance testing, and thanks to its integration with Azure boards, quickly gather feedback from the stakeholder. The primary purpose and advantage of this web service is to have a tool that keeps track of every test that’s been performed, every test that still needs to be performed, as well as the steps within each test (figure 7).

![](</images/what_is_devops_and_how_to_implement_it_in_your_organization_using_microsoft/8.png>)

**Figure 7: An example of test cases list**


# Devops is a huge return on investment

In short, DevOps is designed to help you deliver higher-quality products faster, which means you’ll have a higher rate of customer satisfaction, and you’ll need fewer engineers assigned to each project. For example, after implementing DevOps culture in its business, Fidelity Worldwide Investments reported a reduction in deployment time from 3 days to 2 hours, which saved them $2 million in 2014. The adoption of software development frameworks like Scrum, Agile, and CMMI will increase productivity, improve the quality of the products, and decrease a company’s workload, which will indirectly create a less stressful work environment. After a reorganization of the development process, Nationwide Insurance recorded a quality improvement of 50%, a 70% reduction in downtime, and improved team productivity.

The adoption of these Microsoft services will allow company to slowly embrace the DevOps culture which means that less time will be wasted, communications will improve, and task management will be organized and streamlined. The code will be more reliable and less prone to errors which will improve the client’s satisfaction with the product as well as lower the chances that the team will have to fix the project again later.

Operating on a cloud-based environment will also preserve files and versions if there is any kind of hardware malfunction and will be more likely to prevent security breaches. The communication between the project owner and the different groups as well with the client will be much easier thanks to Microsoft Office 365 and Microsoft Teams. All of this results in higher client satisfaction, higher quality products, less unnecessary overtime, and money saved for the company.


# References

· Sessions, R (2008). Simple Architecture for Complex Enterprises. Microsoft Press.

· Shah, M. (2017). Shift Left to Make Testing Fast and Reliable. Microsoft.

· Microsoft (n.d). Microsoft Teams documentation. Available at: <https://docs.microsoft.com/en-us/microsoftteams>

· Microsoft (n.d). Microsoft DevOps documentation. Available at: <https://docs.microsoft.com/en-us/azure/devops/>

· Bradley, T. (n.d.). When DevOps is realized: Overcoming traditional dev and ops mindsets. TechBeacon. Available at: <https://techbeacon.com/devops/when-devops-realized.>

· Hexacta. (2019). How to Implement Scrum in 7 Steps and not Die Trying. Available at <https://www.hexacta.com/2019/01/21/how-to-implement-scrum-in-7-steps-and-not-die-trying/>

· Loukides, M. (2017). The evolution of DevOps. O’Reilly.

· Siebel, T. (2019). Digital Transformation: Survive and Thrive in an Era of Mass Extinction. RosettaBooks.

· Jeffrey Liker. (2004). The Toyota Way: 14 Management Principles from the World’s Greatest Manufacturer. Mc Graw Hill India.
