---
title: "How to create and configure your bot to work in Microsoft Teams"
date: 2020-08-02T02:09:16+09:00
description: "This article is a step-by-step guide on how to create a Bot from scratch using Microsoft Bot Framework and how to configure it to work with Microsoft Teams."
tags: ["CSharp", "Bots", "Microsoft Teams", "Software Development"]
thumbnail: /how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/0.png
---

This article is a step-by-step guide on how to create a Bot from scratch using Microsoft Bot Framework and how to configure it to work with Microsoft Teams.


# Prerequisites

1. Office 365 Tenant
2. Azure Subscription with Azure Bot Service, App Service
3. Visual Studio


# Prepare the Azure resources

Navigate and log in to Azure Portal. Create a new resource group then add a new Web App Bot (You can type “bot” in the search bar to filter your results).

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/1.png>)

After you click on the Create button, you will be redirected to the configuration page of your resource.

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/2.png>)

Since you added the resource directly from the resource group, some properties will be automatically set to the resource group values (like Resource group, Location, and Subscription).

Let’s fill up the remaining properties as follow:

- Bot handle: Unique identifier for your bot.
- Pricing tier: F0 (Fee up to 10k messages without premium channels) or S1 ($0.50 per 1,000 messages and the possibility to create premium channels)
- App name: This will form the bot’s Endpoint Url.
- Bot template: Currently it is possible to use the SDK for C# and Node.JS to implement two different templates: Echo Bots (a simple bot that echoes back the user’s message) and Basic Bot (bot template that contains Language Understanding and Bot Analytics services).

To complete the configuration and create the resource click Create and wait a few seconds to allow Azure to complete the task in the background. From the Channels tab under Bot Management, click on the Microsoft Teams icon to add the MS Teams channel to the bot.

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/3.png>)


# Create an empty bot

Open Visual Studio and create a new empty .NET Core web application project. It’s possible to start from a Skill Template but it comes with many pre-added features (like CosmosDb, Monitoring, Multilanguage, and many more) which might confuse you and for the purpose of this demo I prefer to start with a barebone bot.


# Add dependencies

Open the Package Manager Console and execute the following instruction to add the required dependencies:

```bash
    Install-Package Microsoft.AspNetCore.Mvc.NewtonsoftJson
    Install-Package Microsoft.Bot.Builder
    Install-Package Microsoft.Bot.Builder.Integration.AspNet.Core
```

# Configure the application

Now it’s time to add services to our collection and make them available in our application through dependency injection and configure the middleware pipeline:

```bash
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Pegasus.Bots;
    namespace Pegasus
    {
       public class Startup
       {
           public IConfiguration Configuration { get; }
           public Startup(IConfiguration configuration)
           {
               Configuration = configuration;
           }
           public void ConfigureServices(IServiceCollection services)
           {
               services.AddControllers().AddNewtonsoftJson();
               services.AddSingleton<IBotFrameworkHttpAdapter, AdapterWithErrorHandler>();
               services.AddTransient<IBot, PegasusBot>();
           }
           public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
           {
               if (env.IsDevelopment())
               {
                   app.UseDeveloperExceptionPage();
               }
               app.UseDefaultFiles()
                   .UseStaticFiles()
                   .UseWebSockets()
                   .UseRouting()
                   .UseAuthorization()
                   .UseEndpoints(endpoints =>
                   {
                       endpoints.MapControllers();
                   });
           }
       }
    }
```

Next, create an adapter to handle the bot framework HTTP requests that will show users a message if there is an exception during the bot’s execution.

```bash
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Bot.Builder.TraceExtensions;
    using Microsoft.Bot.Connector.Authentication;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using System;
    namespace Pegasus
    {
       public class AdapterWithErrorHandler : BotFrameworkHttpAdapter
       {
           public AdapterWithErrorHandler(IConfiguration configuration, ILogger<BotFrameworkHttpAdapter> logger)
               : base(configuration, logger)
           {
               OnTurnError  = async (turnContext, exception) =>
               {
                   logger.LogError(exception, $"[OnTurnError] unhandled error : {exception.Message}");
                   await turnContext.SendActivityAsync("The bot encountered an error or bug.");
                   await turnContext.SendActivityAsync("To continue to run this bot, please fix the bot source code.");
               };
           }
       }
    }
```

To complete our bot we have to create two more things: a controller to handle the request and a class to implement our business logic. Create the controller first like so:

```bash
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    namespace Pegasus.Controllers
    {
       [Route("api/messages")]
       [ApiController]
       public class BotController : ControllerBase
       {
           private readonly IBotFrameworkHttpAdapter Adapter;
           private readonly IBot Bot;       public BotController(IBotFrameworkHttpAdapter adapter, IBot bot)
           {
               Adapter = adapter;
               Bot = bot;
           }       [HttpPost, HttpGet]
           public async Task PostAsync()
           {
               await Adapter.ProcessAsync(Request, Response, Bot);
           }
       }
    }
```

When the API receives a Get or Post request, it delegates the processing of the request to the adapter which will invoke the bot that we have specified in the Startup class (the one it received in the constructor through dependency injection.)

Now it’s time to implement our business logic. To do so, let’s create a new class that derives from Microsoft.Bot.Builder.Teams.TeamsActivityHandler which derives itself from ActivityHandler which is an implementation of the Ibot interface. This means that it will have added all the methods related to Microsoft Teams, such as OnTeamsChannelDeletedAsync, OnTeamsFileConsentAcceptAsync, and many more.

For the purpose of this demo, let’s just focus on the messaging activity. Let’s override the OnMessageActivityAsync as follows:

```bash
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Teams;
    using Microsoft.Bot.Schema;
    using Microsoft.Bot.Schema.Teams;
    namespace Pegasus.Bots
    {
       public class PegasusBot : TeamsActivityHandler
       {
           protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
           {
               var team = TeamsInfo.GetTeamDetailsAsync(turnContext);
               await turnContext.SendActivityAsync(MessageFactory.Text($"Team Id: {team.Id}"), cancellationToken).ConfigureAwait(false);
           }
       }
    }
```

To complete our bot we need to add references to our Azure bot in the appsettings.json file as follows:

```bash
    {
     "MicrosoftAppId": "",
     "MicrosoftAppPassword": ""
    }
```

To get this information, select the tab Settings on your Web App Bot resource on Azure Portal and click on the Manage link next to your Microsoft App ID.


# Create a connection between your local environment and teams

In order to create a tunnel to your local environment, you are gonna use ngrok. First download ngrok from ngrok.com, then open the command prompt and navigate to the folder containing ngrok.exe. Start the execution of the bot in Visual Studio and copy the port from the URL address. Execute the following instruction on your command prompt:

```bash
    ngrok http [bot-port] --host-header=localhost
```

Now, on the Azure portal, change the messaging endpoint bot’s configuration to the ngrok endpoint.


# Add Teams App with the Bot

Our bot is ready to work with MS Teams. But MS Teams knows nothing about the bot. We need to register a new app and provide information about the bot. It will allow Microsoft Teams to communicate with the bot.

Open App Studio in your Microsoft Teams client, navigate to the Manifest Editor tab, and click Create a new app. In the configuration page insert the details of your app.

It’s important to mention that the Short Name value will be used to @mention bot in conversations.

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/4.png>)

Then add bot information in the Capabilities section:

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/5.png>)

To finish the configuration, go to the Test and Distribute section and click Install.

If you can’t complete the installation and you get the message “You don’t have permission to add Pegasus to this team,” navigate to <https://admin.teams.microsoft.com/>. Head to the Teams App and select Setup Policies to verify that the upload custom apps is enabled.

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/6.png>)

Now the bot can be tested from your Teams client.

![](</how_to_create_and_configure_your_bot_to_work_in_microsoft_teams/7.png>)
