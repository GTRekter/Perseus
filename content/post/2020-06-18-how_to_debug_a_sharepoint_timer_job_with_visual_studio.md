---
layout: post
title: "How to debug a SharePoint timer job with Visual Studio"
subtitle: ""
description: "Timer jobs are an effective solution to handle a situation where a specific process needs to run periodically. The following steps describe how to debug a Microsoft SharePoint 2010 timer job using Visual Studio."
excerpt: ""
date: 2020-06-1802:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["SharePoint","Troubleshooting", "Visual Studio"]
URL: "/2020/06/18/how_to_debug_a_sharepoint_timer_job_with_visual_studio/"
categories: [ Tech ]
---

Timer jobs are an effective solution to handle a situation where a specific process needs to run periodically. The following steps describe how to debug a Microsoft SharePoint 2010 timer job using Visual Studio.

1\. Deploy your solution containing your timer job to SharePoint by clicking Build and then Deploy Solution

![](/images/how_to_debug_a_sharepoint_timer_job_with_visual_studio/1.png)

Figure 1: Visual studio build dropdown

2\. Open the Control Panel. Click System and Security, click Administrative Tools and then open Windows Services.

3\. Select the service named SharePoint 2010 Timer and ensures that it’s running.

![](/images/how_to_debug_a_sharepoint_timer_job_with_visual_studio/2.png)

Figure 2: Windows Services

4\. From Visual Studio, select Debug and Attach to Process and attach the debugger to the process named OWSTIMER.EXE. N.B In case you don’t see that process, tick the checkbox Show processes from all users.

![](/images/how_to_debug_a_sharepoint_timer_job_with_visual_studio/3.png)

Figure 3: Attach the debugger to the process named OWSTIMER.EXE

5\. Open the Central Administration. Click on Monitoring and then select Review job definitions to see a list of all time jobs.

![](/images/how_to_debug_a_sharepoint_timer_job_with_visual_studio/4.png)

Figure 4: SharePoint 2010 Central Administration

6\. Select your timer job and click Run Now

Your timer job will now execute and will hit any breakpoints you have set within Visual Studio.
