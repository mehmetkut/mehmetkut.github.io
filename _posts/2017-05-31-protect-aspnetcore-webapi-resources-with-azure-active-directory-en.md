---
layout: post
title: Protect ASP.NET Core Web API resources with Azure Active Directory.
comments: true
tags: [ASP.NET, ASP.NET Core, Microsoft Azure, Azure, ASP.NET Core Web API, Azure Active Directory, Azure AD, Active Directory, JSON Web Token]
description: Protect ASP.NET Core Web API resources with Azure Active Directory.
---

In this article I will show you how to protect your **ASP.NET Core Web API** resources with **Azure Active Directory** through a real scenario.

-----

### Scenario

We currently have REST API resources written in **ASP.NET Core Web API**. These resources are hosted on **Azure** and are consumed by IOS, Android and various backend clients. At the same time, **Azure Active Directory (AAD)** is configured on the our Azure subscription. Unfortunately, there is no security in the our REST API right now. Anyone can access these resources with **POST** or **GET** requests easily. So, we want to protect resources from public and this should be done in safest and shortest way.

-----

### Possible Solutions

- You can write your own security system for this scenario. However, this is a time-consuming and unsecured solution. 
- You can purchase a third party software. So, this option will be costly.
- You can use, your already configured and ready **Azure Active Directory (AAD)** Service!

Using  **AAD** service, you can control access and protect your resources with **JSON Web Token (JWT)**.

-----

### What We Need? 

1. ASP.NET Core Web API
2. Azure Active Directory

-----

### ASP.NET Core Web API

In this phase, we will create a sample REST API with the ASP.NET Core Web API and publish it as **App Service** on the **Azure**.
I will create the project with Visual Studio 2017.

-----

![ASPNETCOREAADJWT01](/assets/images/posts/2017052901/sc01.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Firstly, start Visual Studio 2017

1. Select **File** menu.

2. Move your mouse to **New** menu.

3. Click the **Project...** menu item.

-----

![ASPNETCOREAADJWT02](/assets/images/posts/2017052901/sc02.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **.NET Core** from the left menu.

2. From the options, mark **ASP.NET Core Web Application (.NET Core)**.

3. Type the name of project. I call it **CorpAPI**.

4.  Click **OK** to continue.

-----

![ASPNETCOREAADJWT03](/assets/images/posts/2017052901/sc03.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **ASP.NET Core 1.1**.

2. Select project type as **Web API**.

3. We make sure that **Authentication** is **No Authentication**. If not, click **Change Authentication** button for change it.

4. Click **OK** to create project.

-----

After we have create our project, a screen image will be similar to below image.

![ASPNETCOREAADJWT04](/assets/images/posts/2017052901/sc04.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


-----

Now, we will configure **Azure App Service** from **Azure Portal**.

![ASPNETCOREAADJWT05](/assets/images/posts/2017052901/sc05.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select New from left menu.

2. In this blade select **Web + Mobile**.

3. Select **Web App**.

4. In **App Name** field, type **corpapi** as our project name.  If Azure does not accept the name you typed, you can type different name. This should be Azurewide unique.

5. Select suitable Azure **Subscription**.

6. We wanted to create a new **Resource Group**. Select **Create new** and type Resource Group name. Name it like **RG-CorpApi**.

7. We chose the free **App Service Plan** because it is demo scenario. This can be change according to the scenario.

8. Click **Create** button to start operation.

-----

We are opening **App Service** that we have created.

![ASPNETCOREAADJWT06](/assets/images/posts/2017052901/sc06.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From the left menu select **Resource groups**.

2. Click **RG-CorpApi** resource group.

3. In this blade click **Overview**.

4. Find **corpapi** and click on it.

-----

We have to publish our project to **Azure** as **App Service**. So , we need to publish settings.

![ASPNETCOREAADJWT07](/assets/images/posts/2017052901/sc07.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **Overview**.

2. Click **Get publish profile** button.

3. Our browser will be download publish profile file. My publish file name is **corpapi.PublishSettings**, yours might be different according to your AppService name.

-----

![ASPNETCOREAADJWT08](/assets/images/posts/2017052901/sc08.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Right click **CoreAPI** project.

2. Click **Publish...** menu item.

-----

![ASPNETCOREAADJWT09](/assets/images/posts/2017052901/sc09.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **Import profile** option.

2. Click **Publish** button.

-----

![ASPNETCOREAADJWT10](/assets/images/posts/2017052901/sc10.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. On the **Import Publish Settings File** window, we select the file we downloaded from **Azure Portal**. The file name **corpapi.PublishSettings** may be different for you.

2. Click **Open** button.

-----

![ASPNETCOREAADJWT11](/assets/images/posts/2017052901/sc11.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Our project while being sent to **Azure**. On the **Output** window you can see similar informations.

2. You should see **Publish: 1  succeeded...** on the **Output** window when the publish operation is finished.

-----

Open your browser.

![ASPNETCOREAADJWT12](/assets/images/posts/2017052901/sc12.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. At the address line type https://corpapi.azurewebsites.net/api/values and press enter. This address can be change according to your app service name.

2. If the API is working, it will be a JSON output like on screen.

Our API is ready now. 😄 However, as I mentioned before, It is public and that is not we want to. 💀

### Azure Active Directory

After this phase we will create **Azur Active Directory Applications** on **Azure Active Directory** and will be use them for protect our API.

-----

![ASPNETCOREAADJWT13](/assets/images/posts/2017052901/sc13.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From the left menu, we select **Azure Active Directory**. If you do not find this option, you can find it in **More services**.

2. In this blade we click **App registrations** item.

3. To register for the new **AAD Application**, click on the **New application registration** button.

-----

![ASPNETCOREAADJWT14](/assets/images/posts/2017052901/sc14.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Type the name of the AAD application in the **Name** field. I called it **CorpAPI**.

2. Leave Application type as **Web app / API**. We specify this app type as a Web app or API application.

3. For the **Sign-on URL** section, we currently type our API address.

4. Click **Create** button for create our aad application.

-----

![ASPNETCOREAADJWT15](/assets/images/posts/2017052901/sc15.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. In AAD blade, click **App registrations**

2. Search for **CorpAPI** application.

3. Click on it for open.

-----

When our application opens, you will see a screen similar to the one below.

![ASPNETCOREAADJWT16](/assets/images/posts/2017052901/sc16.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Important point on this screen is **Application ID**. This ID is our application unique ID. Automatically generated and assigned to our application by AAD. We'd better copy it to a somewhere. 😄

-----

Let's set aad application permissions.

![ASPNETCOREAADJWT17](/assets/images/posts/2017052901/sc17.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We select **Required Permissions** in current blade.

2. Click **Add** button.

-----

![ASPNETCOREAADJWT18](/assets/images/posts/2017052901/sc18.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **Select an API** item.

2. In **Select an API** blade, mark **Microsoft Graph** item.

3. Click **Select** button.

-----

![ASPNETCOREAADJWT19](/assets/images/posts/2017052901/sc19.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **Select permissions** item.

2. Choose the **Read and write directory data** permission found in **Application Permissions** and **Delegated Permissions**. More specifically, choose permissions for **Read and write directory data** under **Application Permissions** and **Read and write directory data** under **Delegated Permissions**. If we have selected other than these permissions, just uncheck these permissions.

3. Click on **Select** button.

-----

![ASPNETCOREAADJWT20](/assets/images/posts/2017052901/sc20.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We have chosen our API and permissions. Now click on the **Done** button.

-----

![ASPNETCOREAADJWT21](/assets/images/posts/2017052901/sc21.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. In **Required permission** blade, as you can see on the screen we added **Microsoft Graph**. There is one **Application Permission** and one **Delegated Permission** permission.

2. We click on **Windows Azure Active Directory**, which is come by default.

3. As we did before, we choose the **Read and write directory data** permission under **Application Permissions**.

4. We choose permissions for **Read and write directory data** under **Delegated Permissions**.

5. If we have selected other than these permissions, remove these permissions and than click on the **Save** button.

-----

After granting the permissions, **Required permissions** blade will appear as below.

![ASPNETCOREAADJWT22](/assets/images/posts/2017052901/sc22.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

-----

We will add a native AAD application in a similar way. This time the type of our application will be **Native Application**.

![ASPNETCOREAADJWT23](/assets/images/posts/2017052901/sc23.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From the left menu, select **Azure Active Directory**. 

2. In this blade click **App registrations** item.

3. To register for the new **AAD Application**, click on the **New application registration** button.

-----

![ASPNETCOREAADJWT24](/assets/images/posts/2017052901/sc24.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We type the name of the AAD application in the **Name** field. I called it **CorpNativeAPI**.

2. This time we select the **Application type** as **Native** application.

3. For, **Sign-on URL** type https://corpnativeapi.azure-mobile.net . This might be different according to your native app name.

4. Click **Create** button for create our native aad application.

-----

![ASPNETCOREAADJWT25](/assets/images/posts/2017052901/sc25.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We are searching for **CorpNativeAPI** application and click on it.

-----

![ASPNETCOREAADJWT26](/assets/images/posts/2017052901/sc26.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Again, important point on this screen is **Application ID**. This ID is our application unique ID. Automatically generated and assigned to our application by AAD. We'd better copy it to a somewhere. 😄

-----

![ASPNETCOREAADJWT27](/assets/images/posts/2017052901/sc27.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We select **Required Permissions** in current blade.

2. In this blade click on **Required permissions**.

-----

![ASPNETCOREAADJWT28](/assets/images/posts/2017052901/sc28.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **Select an API** item.

2. In **Select an API** blade, Search **CorpAPI** AAD Application.

3. Mark the **CorpAPI**.

4. Click the **Select** button.

-----

![ASPNETCOREAADJWT29](/assets/images/posts/2017052901/sc29.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **Select permissions** item.

2. Select **Access CorpAPI** permission.

3. Click the **Select** button.

-----

![ASPNETCOREAADJWT30](/assets/images/posts/2017052901/sc30.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click the **Done** button.

-----

If everything ok, you should see a **Required permissions** screen similar to the one below.

![ASPNETCOREAADJWT31](/assets/images/posts/2017052901/sc31.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Lastly we need to do is click on the **Grant Permissions** button and answer the question **Yes**. Thus, we have given the necessary permissions to account.

-----

Take a look at what we have done on the AAD so far:

- We created a **Web Application / Web API** in the AAD and added the necessary permissions.
- We created a **Native** application in the AAD and added the necessary permissions.

Here a unique **Application ID** is created for each application we create on the AAD. The AAD is responsible for the creation of these unique application identities. 

We have completed the Azure AD configuration "least liked" section.. 😄

-----

Now we will generate **JSON Web Token** and try our API using **Postman** application.

-----

First of all we need to find our **Endpoint** address to generate token.

![ASPNETCOREAADJWT32](/assets/images/posts/2017052901/sc32.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From the left menu, we select **Azure Active Directory**. 

2. On this blade we click **App registrations** item.

3. Click on **Endpoints** button.


-----

![ASPNETCOREAADJWT33](/assets/images/posts/2017052901/sc33.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. In blade, **OAUTH 2.0 TOKEN ENDPOINT** is our endpoint address.
In my scenario, the endpoint address is
https://login.windows.net/64114426-b4a0-4e3a-8efb-9ea15136cd2e/oauth2/token
Your endpoint address will be different.

Briefly we will do our POST requests to this address for generate token.

-----

![ASPNETCOREAADJWT34](/assets/images/posts/2017052901/sc34.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From the left menu, we select **Azure Active Directory**.

2. On this blade we click **App registrations** item.

3. Click our **CorpNativeAPI** native application.

-----

![ASPNETCOREAADJWT35](/assets/images/posts/2017052901/sc35.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We take note of the **Application ID** of our native application.

-----

![ASPNETCOREAADJWT36](/assets/images/posts/2017052901/sc36.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From the left menu, we select **Azure Active Directory**.

2. On this blade we click **App registrations** item.

3. Click our **CorpAPI**.

-----

![ASPNETCOREAADJWT37](/assets/images/posts/2017052901/sc37.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. On this blade, click  **Manifest** button.

-----

![ASPNETCOREAADJWT38](/assets/images/posts/2017052901/sc38.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. In the manifest file we search for **identifierUris**. This is the name of our resource. My resource name is https://cevizbilgi.com.tr/c97dc3f7-7ae6-4b3d-87c4-ab728339e0ac. Yours will be different.

-----

Now we have all informations, we need to generate the token. Let's do it! 😄

Open **Postman** application. By the way, you can choose a different application such as Fiddler. That is your own choice.

![ASPNETCOREAADJWT39](/assets/images/posts/2017052901/sc39.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We select our request type as **POST**

2. Type address we have taken from **Endpoints** 

For me it is https://login.windows.net/64114426-b4a0-4e3a-8efb-9ea15136cd2e/oauth2/token **this will change for you.**

3. Click **Body** tab.

4. Select **x-www-form-urlencoded** option.

5. We add a key named **resource**. Value is the url we got from the manifest file. My url is https://cevizbilgi.com.tr/c97dc3f7-7ae6-4b3d-87c4-ab728339e0ac .

6. We add a key named **grant_type**. Value is a **password**

7. We add a key named **client_id**. Value is our **Native** AAD **Application Id**. This value for me, 74f671cc-e205-48bc-8d68-a3fec58cd0bd

8. We add a key named **username**. Value is your account name. My account name is **mehmet.kut@cevizbilgi.com.tr**

9. We add a key named **password**. Value is your account password.

10. Click **Send** button for send our request.

11. If the request is successful, it will be displayed as on screen. We will use this **access_token** to access our resources. 👍

12. Please notice that, **access token** is a token that is valid for a certain period of time. We can regenerate a new **access token** using **refresh token** instead of asking username and password every time.

-----

Now, we will try to access the ASP.NET Core Web API project with Postman. In beginning of article we published this project to **Azure** as an **App Service**

![ASPNETCOREAADJWT40](/assets/images/posts/2017052901/sc40.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We select our request type as **GET**

2. Type our API address for request. 

3. Click **Send** button for send our request.

4. As you can see, we went through the API without any authentication.

-----

Let's go back to Visual Studio **CorpAPI** application. Now, we are making our WebAPI application safe.😄

-----

![ASPNETCOREAADJWT41](/assets/images/posts/2017052901/sc41.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Open **Package manager Console**. We need **Microsoft.AspNetCore.Authentication.JwtBearer** package. Type **Install-Package Microsoft.AspNetCore.Authentication.JwtBearer** command to add the package to the project.

-----

![ASPNETCOREAADJWT42](/assets/images/posts/2017052901/sc42.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Open **appsettings.json** configuration file.

2. We will add **AzureAd** section.

```json

"AzureAd": {
    "AadInstance": "https://login.microsoftonline.com/{0}",
    "Tenant": "64114426-b4a0-4e3a-8efb-9ea15136cd2e",
    "Audience": "https://cevizbilgi.com.tr/c97dc3f7-7ae6-4b3d-87c4-ab728339e0ac"
}

```
**AadInstance** You do not need to change this value.
**Tenant** You can find this value in your Token Endpoint address.
**Audience** This value is in the manifest file. We had taken that value before.

-----

![ASPNETCOREAADJWT43](/assets/images/posts/2017052901/sc43.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Open **Startup.cs** file.

2. In the **ConfigureServices** method, we add the following code.

```csharp
services.AddAuthentication();
```

3. We add the following code in the **Configure** method.

```csharp
// Configure the app to use Jwt Bearer Authentication
app.UseJwtBearerAuthentication(new JwtBearerOptions
{
    AutomaticAuthenticate = true,
    AutomaticChallenge = true,
    Authority = String.Format(Configuration["AzureAd:AadInstance"], Configuration["AzureAD:Tenant"]),
    Audience = Configuration["AzureAd:Audience"],
});
```

-----

![ASPNETCOREAADJWT44](/assets/images/posts/2017052901/sc44.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We open the **ValuesController.cs** file under the Controllers folder.

2. We are adding the following code
```csharp
using Microsoft.AspNetCore.Authorization;
```  

3. For security we add the following attribute to our class.

```csharp
[Authorize]
```  

-----

Publish project to **Azure**

![ASPNETCOREAADJWT45](/assets/images/posts/2017052901/sc45.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Right click **CoreAPI** project.

2. Click **Publish...** menu item.

3. We publish the application by clicking **Publish** button on the screen to be opened.

-----

Let's look at the API we're protecting now 😄


![ASPNETCOREAADJWT46](/assets/images/posts/2017052901/sc46.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We select our request type as **GET**

2. Type our API address for request. 

3. Click **Send** button for send our request.

4. As you can see, the API is now protected. We received a **401 Unauthorized** error because we did not provide the necessary security information for access. 👍


-----

Now let's see how to access the API with **access token** that we have already generated.

![ASPNETCOREAADJWT47](/assets/images/posts/2017052901/sc47.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We select our request type as **GET**

2. Type our API address for request.

3. We are adding **Authorization** key to header section with value "**bearer** accesstoken". The important point here is access token lifetime when it is expired you have to use refresh token or username and password to generate new access token again.

5. Click **Send** button for send our request.

6. Voilà! Our token has been verified by AAD and we accessed our resource securely. 👍

-----

We have reached our goal. 😄 

I've shown you so far:

- We have seen how to build a project in the **ASP.NET Core Web API**.
- We published the WEB API as **Azure App Service** on **Azure**.
- Configured a **Web App / Web API** application in **AAD**
- Configured a **Native** application in **AAD**
- We protect the WEB API with the **JWT** package.
- We were able to access it with our **access token**


In fact, there are a few more things that I have wrote in this article but those are not in real scope.
This topic is easy but if you missed few step it won't work. Do not hesitate to send me an e-mail if you have any problems

In addition, with **AAD** as an enlightenment point, you can add protection not only with the ASP.NET Core Web API but also with APIs written in different technologies such as NodeJS, PHP and others so this is cool 😎