---
layout: post
title: "State Management in ASP.NET"
subtitle: ""
description: "Whenever you visit a web application, your browser will communicate with the respective server through HTTP or HTTPs protocol, but because its stateless nature, if you visit the same web application again after closing the web browser, there will be no trace of your previous visit..."
excerpt: ""
date: 2020-03-12T02:09:16+09:00
author: "Ivan Porta"
image: ""
tags: ["CSharp", "Dotnet", "Web Development", "Software Development"]
URL: "/2020/03/12/state_management_in_aspnet/"
categories: [ Tech ]
---

Whenever you visit a web application, your browser will communicate with the respective server through HTTP or HTTPs protocol, but because its stateless nature, if you visit the same web application again after closing the web browser, there will be no trace of your previous visit.

Attempting to manage state in web applications goes against the fundamental design principles to provide a scalable medium for sharing information on the web. The cause of that is the reduction of scalability caused by the fact that the pages shown to a specific user will be different from those shown to another user and cannot be reused or cached.

However, many web applications may need to memorize the state to function properly (i.e. eCommerce). To achieve this goal, ASP.NET provides:

1. Client Side State Management.
2. Server Side State Management.


# Client Side State Management

The information of the calls between the browser and the server are stored in the HTML page, in an HTTP request, or on the disk of the client computer. The server resources aren’t utilized and it uses the client information received each call to rebuild the state of the prior invocation.

An example of client-side state management techniques are:

- Cookie state
- View State


# Cookie state

A cookie is a data received from a web application which is stored by the browser. All cookies are stored in a single file and will be included in the HTTP header on each request made to the same server. When a web application sets a cookie, it can provide an expiration date, a duration, apply restrictions to a specific domain and path to limiting where the cookie is sent and so on.

```bash
    HTTP/2.0 200 OK
    Content-type: text/html
    Set-Cookie: first_cookie=hello; Domain=example.com
    Set-Cookie: second_cookie=world; Expires=Wed, 21 Oct 2015 07:28:00 GMT;[page content]
```

Each cookie can have one o more of the following attributes:

1. **Expires**: makes the cookie expire at a specific date
2. **Max-Age**: makes the cookie expire after a specific length of time
3. **Secure**: the cookie will be sent to the server only with an encrypted request over the HTTPS protocol.
4. **HttpOnly**: makes the cookie inaccessible to JavaScript’s Document.cookie API.
5. **Domain**: specifies the allowed hosts to receive the cookie. If unspecified, it defaults to the host of the current document location (subdomains excluded).
6. **Path**: indicates a URL path that must exist in the requested URL in order to send the Cookie header.
7. **SameSite**: let servers require that a cookie shouldn’t be sent with cross-site requests.

The amount of data sent through cookies differ from browser to browser but 4,096 bytes are guaranteed.

ASP.NET and cookies

In order to manage cookie data more easily, ASP.NET provides the **HttpCookie**class. Both request and response objects expose the collection of cookies through the **HttpCookieCollection**and each cookie is a name/value pair that are accessible through the Values collection of the **HttpCookie**class or indirectly through the default indexer provided by the class. To request that a client set a cookie, add a new **HttpCookie**instance to the response cookie collection before your page rendering.

```bash
    protected void Page_Load(Object sender, EventArgs E) 
    { 
       int cookieValue = 0;
       if (Request.Cookies["Test"] == null)
       {
           HttpCookie cookie = new HttpCookie("Test");
           cookie.Value = "Hello world";
           Response.Cookies.Add(cookie);
       }
       else
       {
           cookieValue = Convert.ToInt32(Request.Cookies["Test"].Value);
       }
    }
```

# View state

View state is a mechanism introduced by ASP.NET which stores data in a hidden field called **\_\_VIEWSTATE** on each ASP.NET page and each time a page is posted to itself, the content is sent as part of the post.

View state supports storing any type that is serializable and since the Page class is derived from the Control base class, it is accessible directly from within your pages and indirectly through server-side controls. The view state for control is loaded just before the Load event firing, and it is flushed just before the Render method being invoked.

```bash
    protected void Page_Load(Object sender, EventArgs E) 
    { 
       ArrayList cart = (ArrayList)ViewState["Cart"];
    }
```

# Server Side State Management

This kind of management use the server resources (i.e. memory) to store the state information on the server machine.

An example of server-side state management techniques are:

- Application State
- Session State


# Application State

The application state is a repository for global data in a web application. It’s important to mention that its data are replicated with each application instance and the application state maintains its data until the web application is shut down or we release the data manually by assigning null or call the Clear() method.

The application state is accessed through the **Application**property of the **HttpApplication**class, which returns an instance of class **HttpApplicationState**. This class holds data of any type as part of a key/value pair and provide protection from potential concurrent access (since multiple clients can access the same application state) through the **HttpApplicationStateLock**class.

The HttpApplicationStateLock mechanism allows concurrently multiple readers and restricts access only when a request tries to write. However, in case of updating a shared piece of state is necessary explicitly call the **Lock()**and** UnLock()** methods.

```bash
    protected void Page_Load(Object sender, EventArgs E) 
    { 
       Application.Lock();
       if (Application["Test"] != null)
       {
           Application["Test"] = "Hello World";
       }
       else
       {
           Application["Test"] = "Hello World";
           Application.UnLock();
       }
    }
```

# Session State

The session state is maintained in the same process and AppDomain as your web application and can store any data type. Every time a new user interacts with the web application, a new session key is generated and it’s sent to the user through a cookie named **ASP.NET_SessionId**.

Initially, the session key was valorized using GUID, but because it’s deterministic nature, ASP.NET moved to a cryptographic service provider and its encoding algorithm.

```bash
    RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider(); 
    byte[] key = new byte[15]; 
    rng.GetBytes(key); 
    string sessionKey = SessionId.Encode(key);
```

Session state is maintained in an instance of the **HttpSessionState**class and is accessible through the **Session**property of both the **Page**and **HttpContext**classes.

```bash
    protected void Page_Load(Object sender, EventArgs E) 
    { 
       ArrayList cart = (ArrayList)Session["Cart"];
    }
```

The behavior of the session state can be configured through the following elements in the **web.config**:

- **cookieless**: determine if pass the session key via cookies or URL mangling. Its possible values are True and False.
- **mode**: determine if the session state is enabled and where to store its data. Its possible values are **Off**(disable the session state for the current application), **InProc**(stores the session value in the process in which the ASP.NET application is running), **SQLServer**(stores the session in SQL Server instead of the server’s memory) and **StateServer**(store the session into a separate Windows Service which runs in a different process).
- **stateConnectionString**: specify the server name and port in case the mode is **StateServer**.
- **sqlConnectionString**: specify the SQLServer connection in case the mode is **SQLServer**.
- **timeout**: Specify the session state timeout value (in minutes)

```xml
    <configuration>
     <system.web>
       <sessionState cookieless="true" />
     </system.web>
    </configuration>
```
