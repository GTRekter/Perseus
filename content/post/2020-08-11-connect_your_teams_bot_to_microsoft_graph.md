---
layout: post
title: "Connect Your Teams Bot to Microsoft Graph"
subtitle: ""
description: "This article is a step-by-step instruction on how to configure a bot to work with Microsoft Teams and authenticate it to make Microsoft Graph requests..."
excerpt: ""
date: 2020-08-11T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Bots", "Microsoft Teams", "Software Development"]
URL: "/2020/08/11/connect_your_teams_bot_to_microsoft_graph/"
categories: [ Tech ]
---

This article is a step-by-step instruction on how to configure a bot to work with Microsoft Teams and authenticate it to make Microsoft Graph requests.

Before we start, check that you have completed the steps required to configure your bot to work with Microsoft Teams. Here is a link:


[How to create and configure your bot to work in Microsoft Teams](/how_to_create_and_configure_your_bot_to_work_in_microsoft_teams)


In the following sections we are going to work with:

- Microsoft Azure
- Web App Bot
- .NET Core solution
- Microsoft Teams


# Azure Active Directory

Navigate to the Azure portal, and after logging in with your user, open the **Azure Active Directory** panel. Click **App registration** in the left panel then click **New registration** in the upper left.

![](</images/connect_your_teams_bot_to_microsoft_graph/1.png>)

In the app registration page add the following configurations:

1. **Name**: Enter the name of the bot application you are registering.
2. **Supported account types:** Select **Accounts in any organizational directory and personal Microsoft accounts**.
3. **Redirect URI**: Select **Web**and set the URL to <https://token.botframework.com/.auth/web/redirect>

To complete the registration, click **Register**. Once the registration is completed, Azure will display an overview page for the app registration.

![](</images/connect_your_teams_bot_to_microsoft_graph/2.png>)

Copy the **Application (client) ID** and save it to a file (we will need it later). In the left panel, select **Certificate & secrets**, then click **New client secret**.

![](</images/connect_your_teams_bot_to_microsoft_graph/3.png>)

In the creation page add the following configurations:

1. **Description**: Text used to identify this secret from others.
2. **Expires**: Client secret expiration.

Click **Add**, copy your new client secret and save it to a file. To conclude, in the navigation pane, select **API permissions** and then click **Add a permission**. For the purpose of this demo, select **Microsoft APIs**, then **Microsoft Graph** and add the following permissions:

- openid
- profile
- Mail.Read
- User.Read
- User.ReadBasic.All

Click **Add permissions**.


# Web App Bot

Navigate to your web app bot’s resource page on the Azure portal and click the option **Settings**from the navigation panel. Under the section **OAuth Connection Settings**, click **Add Setting** and insert the following configurations:

- **Name**: Enter a name for your connection.
- **Service Provider**: Select **Azure Active Directory**.
- **Client id**: Enter the **Application (client) ID** that you saved during the registration of your application.
- **Client secret**: Enter the secret that you previously created to grant the bot access to the Azure Active Directory app.
- **Grant Type**: Enter authorization code.
- **Login URL**: Enter <https://login.microsoftonline.com.>
- **Tenant ID**: Enter the **Directory (tenant) ID**that your recorded earlier for your Azure AD app.
- **Resource URL**: Enter [https://graph.microsoft.com/.](https://graph.microsoft.com/)
- **Scopes**: Leave it blank.

![](</images/connect_your_teams_bot_to_microsoft_graph/4.png>)

Click **Save**to complete the configuration.


# .NET Core Web Application

Starting from the code that we wrote in the previous tutorial, change the **PegasusBot**as following:

```bash
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Builder.Teams;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;namespace Pegasus.Bots
    {
       public class PegasusBot<T> : TeamsActivityHandler where T : Dialog
       {
           protected readonly BotState ConversationState;
           protected readonly Dialog Dialog;
           protected readonly ILogger Logger;
           protected readonly BotState UserState;public PegasusBot(ConversationState conversationState, UserState userState, T dialog, ILogger<PegasusBot<T>> logger)
           {
               ConversationState = conversationState;
               UserState = userState;
               Dialog = dialog;
               Logger = logger;
           }
           public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
           {
               await base.OnTurnAsync(turnContext, cancellationToken);
               await ConversationState.SaveChangesAsync(turnContext, false, cancellationToken);
               await UserState.SaveChangesAsync(turnContext, false, cancellationToken);
           }
           protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
           {
               await Dialog.RunAsync(turnContext, ConversationState.CreateProperty<DialogState>(nameof(DialogState)), cancellationToken);
           }
       }
    }
```

Then add a new bot called **AuthBot**. This Bot handles the reception of a token/response and because we are using an OAuthPrompt and forward the Activity to the current Dialog.

```bash
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;namespace Pegasus.Bots
    {
       public class AuthBot<T> : PegasusBot<T> where T : Dialog
       {
           public AuthBot(ConversationState conversationState, UserState userState, T dialog, ILogger<PegasusBot<T>> logger)
               : base(conversationState, userState, dialog, logger)
           {
           }
           protected override async Task OnTokenResponseEventAsync(ITurnContext<IEventActivity> turnContext, CancellationToken cancellationToken)
           {
               await Dialog.RunAsync(turnContext, ConversationState.CreateProperty<DialogState>(nameof(DialogState)), cancellationToken);
           }
           protected override async Task OnTeamsSigninVerifyStateAsync(ITurnContext<IInvokeActivity> turnContext, CancellationToken cancellationToken)
           {
               await Dialog.RunAsync(turnContext, ConversationState.CreateProperty<DialogState>(nameof(DialogState)), cancellationToken);
           }
       }
    }
```

Create a new folder called **Dialogs**and add the following class:

```bash
    using System;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.Graph;namespace Pegasus.Dialogs
    {
       public class MainDialog : ComponentDialog
       {
           protected readonly ILogger _logger;
           protected string ConnectionName { get; private set; }
           public MainDialog(IConfiguration configuration, ILogger<MainDialog> logger)
               : base(nameof(MainDialog))
           {
               _logger = logger;
               AddDialog(new OAuthPrompt(
                   nameof(OAuthPrompt),
                   new OAuthPromptSettings
                   {
                       ConnectionName = configuration["ConnectionName"],
                       Text = "Please login",
                       Title = "Login",
                       Timeout = 120000,
                   }));
               AddDialog(new TextPrompt(nameof(TextPrompt)));
               AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
               {
                   PromptStepAsync,
                   LoginStepAsync,
                   CommandStepAsync,
                   ProcessStepAsync
               }));
               InitialDialogId = nameof(WaterfallDialog);
           }
           protected override async Task<DialogTurnResult> OnBeginDialogAsync(DialogContext innerDc, object options, CancellationToken cancellationToken)
           {
               var result = await InterruptAsync(innerDc, cancellationToken);
               if (result != null)
               {
                   return result;
               }
               return await base.OnBeginDialogAsync(innerDc, options, cancellationToken);
           }
           protected override async Task<DialogTurnResult> OnContinueDialogAsync(DialogContext innerDc, CancellationToken cancellationToken)
           {
               var result = await InterruptAsync(innerDc, cancellationToken);
               if (result != null)
               {
                   return result;
               }
               return await base.OnContinueDialogAsync(innerDc, cancellationToken);
           }
           private async Task<DialogTurnResult> InterruptAsync(DialogContext innerDc, CancellationToken cancellationToken)
           {
               if (innerDc.Context.Activity.Type == ActivityTypes.Message)
               {
                   var text = innerDc.Context.Activity.Text.ToLowerInvariant();
                   if (text == "logout")
                   {
                       var botAdapter = (BotFrameworkAdapter)innerDc.Context.Adapter;
                       await botAdapter.SignOutUserAsync(innerDc.Context, ConnectionName, null, cancellationToken);
                       await innerDc.Context.SendActivityAsync(MessageFactory.Text("You have been signed out."), cancellationToken);
                       return await innerDc.CancelAllDialogsAsync();
                   }
               }
               return null;
           }
           private async Task<DialogTurnResult> PromptStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
           {
               return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
           }
           private async Task<DialogTurnResult> LoginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
           {
               var tokenResponse = (TokenResponse)stepContext.Result;
               if (tokenResponse != null)
               {
                   await stepContext.Context.SendActivityAsync(MessageFactory.Text("You are now logged in."), cancellationToken);
                   return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Would you like to do? (type 'me', or 'email')") }, cancellationToken);
               }
               await stepContext.Context.SendActivityAsync(MessageFactory.Text("Login was not successful please try again."), cancellationToken);
               return await stepContext.EndDialogAsync();
           }
           private async Task<DialogTurnResult> CommandStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
           {
               stepContext.Values["command"] = stepContext.Result;
               return await stepContext.BeginDialogAsync(nameof(OAuthPrompt), null, cancellationToken);
           }
           private async Task<DialogTurnResult> ProcessStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
           {
               if (stepContext.Result != null)
               {
                   var tokenResponse = stepContext.Result as TokenResponse;
                   if (tokenResponse?.Token != null)
                   {
                       var command = ((string)stepContext.Values["command"] ?? string.Empty).ToLowerInvariant();
                       if (command == "me")
                       {
                           await ListMeAsync(stepContext.Context, tokenResponse);
                       }
                       else if (command.StartsWith("email"))
                       {
                           await ListEmailAddressAsync(stepContext.Context, tokenResponse);
                       }
                       else
                       {
                           await stepContext.Context.SendActivityAsync(MessageFactory.Text($"Your token is: {tokenResponse.Token}"), cancellationToken);
                       }
                   }
               }
               else
               {
                   await stepContext.Context.SendActivityAsync(MessageFactory.Text("We couldn't log you in. Please try again later."), cancellationToken);
               }           return await stepContext.EndDialogAsync(cancellationToken: cancellationToken);
           }
           private static async Task<User> GetUserAsync(ITurnContext turnContext, TokenResponse tokenResponse)
           {
               if (turnContext == null)
               {
                   throw new ArgumentNullException(nameof(turnContext));
               }
               if (tokenResponse == null)
               {
                   throw new ArgumentNullException(nameof(tokenResponse));
               }
               var client = new GraphClient(tokenResponse.Token);
               return await client.GetMeAsync();
           }
           public static async Task ListMeAsync(ITurnContext turnContext, TokenResponse tokenResponse)
           {
               var user = await GetUserAsync(turnContext, tokenResponse);
               await turnContext.SendActivityAsync($"You are {user.DisplayName}.");
           }
           public static async Task ListEmailAddressAsync(ITurnContext turnContext, TokenResponse tokenResponse)
           {
               var user = await GetUserAsync(turnContext, tokenResponse);
               await turnContext.SendActivityAsync($"Your email: {user.Mail}.");
           }
       }
    }
```

Now, create a client to manage the calls to Microsoft Graph.

```bash
    using System;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;
    using Microsoft.Graph;namespace Pegasus
    {
       public class GraphClient
       {
           private readonly string _token;
           public GraphClient(string token)
           {
               if (string.IsNullOrWhiteSpace(token))
               {
                   throw new ArgumentNullException(nameof(token));
               }
               _token = token;
           }
           public async Task<User> GetMeAsync()
           {
               var graphClient = GetAuthenticatedClient();
               var me = await graphClient.Me.Request().GetAsync();
               return me;
           }
           private GraphServiceClient GetAuthenticatedClient()
           {
               var graphClient = new GraphServiceClient(
                   new DelegateAuthenticationProvider(
                       requestMessage =>
                       {
                           requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", _token);
                           requestMessage.Headers.Add("Prefer", "outlook.timezone=\"" + TimeZoneInfo.Local.Id + "\"");
                           return Task.CompletedTask;
                       }));
               return graphClient;
           }
       }
    }
```

Now let’s update the startup file by adding:

- As the state should be stored somewhere, we need to add a storage layer to the bot. In this case, we’ll be using in-memory storage by `storageservices.AddSingleton<IStorage, MemoryStorage>();`
- The user state which will be used in this bot’s Dialog implementation `services.AddSingleton<UserState>();`
- The conversation state which will be used by the Dialog system itself `services.AddSingleton<ConversationState>();`
- A transient lifetime services, which represent out bot, and that is created each time they’re requested from the service container by `services.AddTransient<IBot, AuthBot<MainDialog>>();`

```bash
    using Microsoft.AspNetCore.Builder;
    using Microsoft.AspNetCore.Hosting;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Integration.AspNet.Core;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Hosting;
    using Pegasus.Bots;
    using Pegasus.Dialogs;namespace Pegasus
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
               services.AddSingleton<IStorage, MemoryStorage>();
               services.AddSingleton<UserState>();
               services.AddSingleton<ConversationState>();
               services.AddSingleton<MainDialog>();
               services.AddTransient<IBot, AuthBot<MainDialog>>();
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
Add a new property to appsettings.json called **ConnectionName**. Its values should be the name of the OAuth connection name we’ve added to the bot.

```bash
    {
     "MicrosoftAppId": "MICROSOFT-APP-ID",
     "MicrosoftAppPassword": "MICROSOFT-APP-PASSWORD",
     "ConnectionName": "OAUTH-CONNECTION-NAME"
    }
```

# Microsoft Teams

Open Microsoft Teams and click on **App Studio**. Under **Manifest Editor** select your bot and then click **Edit**.

![](</images/connect_your_teams_bot_to_microsoft_graph/5.png>)

Select **Domains and permission** from the left panel and add _token.botframework.com_ as a custom domain. To finish the configuration grant permission to open an external link to the app and reinstall it by clicking on **Test and distribute** and the **Install**.

![](</images/connect_your_teams_bot_to_microsoft_graph/6.png>)

Congratulations you are all set. Now your bot is connected to Microsoft Graph.

![](</images/connect_your_teams_bot_to_microsoft_graph/7.png>)


# References

- **.NET Core official documentation:** <https://docs.microsoft.com/en-us/aspnet/core/?view=aspnetcore-3.1>
- **Microsoft Graph Dev center:** <https://developer.microsoft.com/en-us/graph/>
