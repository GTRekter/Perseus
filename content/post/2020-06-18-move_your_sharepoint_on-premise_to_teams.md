---
layout: post
title: "Move your SharePoint On-Premise to Teams"
subtitle: ""
description: "With the Covid-19 pandemic, remote working has become a must more than a luxury. Many companies decided to adopt Microsoft Teams as a collaboration software between its employees, and others even moved part of their infrastructure to it. This article will explain how Microsoft Teams works and the relationship between it and SharePoint Online..."
excerpt: ""
date: 2020-06-18T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["SharePoint","Office 365", "Powershell"]
URL: "/2020/06/18/move_your_sharepoint_on-premise_to_teams/"
categories: [ Tech ]
---

With the Covid-19 pandemic, remote working has become a must more than a luxury. Many companies decided to adopt Microsoft Teams as a collaboration software between its employees, and others even moved part of their infrastructure to it. This article will explain how Microsoft Teams works and the relationship between it and SharePoint Online.


# What is Microsoft Teams?

Microsoft Teams is a chat-based software that centralizes the way we work on projects, organize information, and communicate. It has robust integrations with other Microsoft and third-party services like PowerPoint, OneNote, and SharePoint Online.


# Key components of Microsoft Teams:

- Teams: Collection of people, tools, and content surrounding different projects and outcomes within an organization.
- Channels: Dedicated sections within a team to keep conversations organized by specific topics, disciplines, and projects. Every channel has an email address and when a team member emails the unique address, it creates a conversation in Teams.
- Tabs: Tabs allow team members to access services like Power BI reports, external links, Azure DevOps, Planner, OneNote, and other content in a dedicated space within a channel or in a chat.


# What is a SharePoint Team Site?

A SharePoint team site is a modern site template of the famous highly configurable Microsoft web-based collaborative platform SharePoint Online.


# Key features of SharePoint:

- Lists: A list is a customizable collection of data that can be shared with team members and other site users. The SharePoint team site comes with five lists: announcements, contacts, events, tasks, and links.
- Document libraries: A document library provides a place to store files that can easily be from any device at any time by the other team members where they can access and work on them together.


# What are Office 365 groups?

Office 365 groups is a service integrated with the other Microsoft Office 365 tools that allow team members to share resources like Outlook inbox, calendar, and document libraries.


# Relationships between SharePoint Online and Microsoft Teams:

- Each Microsoft Teams is connected to a SharePoint team site by an Office 365 Group.
- Every time you create a new team in Teams, you’re also creating a new Office 365 Group, Calendar, Planner, and SharePoint team site
- For every channel you create in Teams, a folder within a SharePoint document library is automatically created for you

![](/images/move_your_sharepoint_on-premise_to_teams/1.png)


# Migrate SharePoint On-Premise to SharePoint Online:

To install the current release download of the official Microsoft SharePoint Migration Tool.

- Start SPMT, and then enter your Microsoft 365 username and password.
- Select Start your first migration.

![](/images/move_your_sharepoint_on-premise_to_teams/2.png)

- Select SharePoint Server.
- Enter the SharePoint Server site URL where your content is located, and then select Next.

![](/images/move_your_sharepoint_on-premise_to_teams/3.png)

- Enter your username and password to the SharePoint Server site; username must use the format of username@domain.com. Select Sign in.
- Select the document library where your files are located. The dropdown contains all your possible choices.
- Enter the URL of the SharePoint site where you want your files migrated.
- Select the document library where your files will be copied.
- Select Add. This task is added to the list. If you want to select another set of data files to migrate, select Add a source.


# Create a Microsoft Team from an existing SharePoint site:


# Using SharePoint Online UI

If you have an existing team site or project and you want more ad hoc chat capability by synchronizing it to Teams, You have to:

1. Navigate to the home page of the existing SharePoint team site.
2. Click the Microsoft Teams icon in the bottom left corner of the screen.
3. Click on Create a team.


# Using PowerShell


# Import and policies

```powershell
#Set-ExecutionPolicy RemoteSigned -Force


#Import-Module Microsoft.Online.SharePoint.PowerShell


# Credentials
    $username = "username@domain.com" 
    $password = "password" 
    $siteAdminUrl = "https://contoso-admin.sharepoint.com"


# Connect to SharePoint Online Central Admin
    $securePassword = ConvertTo-SecureString $password -AsPlainText -Force  
    $credentials = New-Object -TypeName System.Management.Automation.PSCredential -argumentlist $userName, $securePassword 
    Connect-SPOService -Url $siteAdminUrl -Credential $credentials


# Get all Office 365 Groups
    $session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/-Credential $Credentials -Authentication Basic -AllowRedirection
    Import-PSSession $session -DisableNameChecking
    $O365Groups = Get-UnifiedGroup
    foreach($Group in $O365Groups)
    {
       Write-Host "Office 365 Group: '$($Group.DisplayName)' - '$($Group.Id)'" -ForegroundColor Cyan
    }


# Get all site filtering for Teams Site which use Office 365 Groups
    $SPSites = Get-SPOSite -Template GROUP#0
    foreach($site in $SPSites)
    {
     Write-host "`t Processing Site: '$($site.Title)' - '$($site.Url)'" -ForegroundColor Cyan
     New-Team -DisplayName $Site.Title
     Write-host "`t Created new team: '$($site.Title)'" -ForegroundColor Green
    
     $clientContext = New-Object Microsoft.SharePoint.Client.ClientContext($site.Url)
     $clientContext.Credentials = New-Object Microsoft.SharePoint.Client.SharePointOnlineCredentials($username, $securePassword)
     $web = $clientContext.Web
     $clientContext.Load($web.Lists)
     $clientContext.ExecuteQuery()
     # Get Document Libraries
     $documentLibraries = $web.Lists | Where { $_.BaseType -eq "DocumentLibrary" }
     if($documentLibraries)
     {
       foreach($documentLibrary in $documentLibraries)
       {
         Write-host "`t Processing Document Library: '$($documentLibrary.Title)'" -ForegroundColor Cyan
         if($web.SiteGroups)
         {
           Write-host "`t Processing Group: '$($documentLibrary.Title) Owners'" -ForegroundColor Cyan
           $O365Group = $O365Groups | Where { $_.DisplayName -eq "$($documentLibrary.Title) Owners" }
           New-TeamChannel -GroupId $O365Group.ID -DisplayName $List.Title
           Write-host "`t Created new channel: '$($List.Title)'" -ForegroundColor Green
         }
       }
     }
    }
```

# Limitations

Microsoft Teams only works with top-level sites. If you already have SharePoint established and your infrastructure includes a lot of subsites, those subsites won’t work with Teams.

Because Teams runs on a SharePoint Online backend for file sharing, SharePoint limitations apply to the Files section within a Team.

As you can see Sharepoint can do a lot for your business whether it is a pandemic or not.
