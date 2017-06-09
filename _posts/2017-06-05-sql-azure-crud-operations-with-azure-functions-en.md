---
layout: post
title: SQL Azure CRUD operations with Azure Functions
comments: true
tags: [Microsoft, Azure, Azure Functions, Function App,  Database, Database Operations, SQL Azure, SQL]
description:  SQL Azure CRUD operations with Azure Functions.
---


In this article I will show you how to do database operations with **Azure Function**.

### Scenario 

We have **Azure Function** that performs an important task for us. This Azure Function is called by several applications.
Every time we call the Azure Function, we want to insert a record to log table.

### What We Need? 

1. Azure SQL DB
2. Azure Function App

### Azure SQL DB

First we need to configure the database to insert data with Azure Function.

![AzureSQLDB01](/assets/images/posts/2017051101/sc01.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select **SQL Databases** from left menu.
2. On this blade, Click on the **Add** button.

-----

![AzureSQLDB02](/assets/images/posts/2017051101/sc02.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. To start, we are creating a new **Server**. If you have configured a **Server** here before, you can choose from available ones. you will need the username and password to connect to the server you selected.

2. **Create a new server**, we are starting to create a new server. 

3. Type server name in  **Server name** field

4. **Server admin login**, this will be username. We will use to connect to our server

5. **Password**, this will be password. We will use to connect to our server

6. **Confim password**, again for confirm password.

7. **Location**, select nearest Azure location. From my location **West Europe** data center is nearest. This will be change depends on your location.

8. Click on **Select** button and our Azure DB Server is ready. 👍

-----

![AzureSQLDB03](/assets/images/posts/2017051101/sc03.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Database name**, The database name we will insert our logs. I typed it as **LogDB**. 

2. **Subscription**, Choose your Azure Subscription.

3. **Resource Group**, Select the resource group to be used. I chose a resource group that I had previously configured at WestEurope location. If you don't have a previously defined resource group, you can create a new resource group by pressing **Create new**. 

> Select nearest Azure location.

4. **Select source**, select **Blank database** because we want an empty database. 

5. **Server**, We have set up the server in the previous steps.

6. **Want to use SQL elastic pool?**, For now, we can select **"Not now"**

7. **Pricing tier**,  I selected **Basic: 5 DTU, 100MB** because I would not use it at real scenario.

8. **Collation**, Leave as SQL_Latin1_General_CP1_CI_AS

9. Click on **Create**, button to create our database.

-----

![AzureSQLDB04](/assets/images/posts/2017051101/sc04.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. To work on the database we created, we click on **SQL databases** from the left menu. 

2. Click on **LogDB**

3. Click on **Overview** 

4. Finally Click on **Tools** button.

-----

![AzureSQLDB05](/assets/images/posts/2017051101/sc05.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1.There are actually a several options to connect to the database, such as Visual Studio or SQL Server Management Studio. However, I will select the **Query editor** which is currently in preview stage. So I will be able to continue to work without leaving the Azure Portal. In this blade, click on  **Query editor** and continue. 

-----

![AzureSQLDB06](/assets/images/posts/2017051101/sc06.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click **Login** button.

2. Choose **SQL server authentication** as **Authorization type**.

3. **Login** type the username that we defined while creating the SQL server.

4. **Password** type the password that we defined while creating the SQL server.

5. Click on **OK**, button and connect to server.

-----

![AzureSQLDB07](/assets/images/posts/2017051101/sc07.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

If everything goes well, you should see **Authenticated as your_user_name** as in the screenshot above.

-----

![AzureSQLDB08](/assets/images/posts/2017051101/sc08.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. W ewill create a our log table.

Columns.

**Id** Primary Key field.

**LogMessage** we store log message in this column.

**CreateDate** log create date.

```sql
CREATE TABLE [dbo].[Logs](
	[Id] [INT] IDENTITY(1,1) NOT NULL,
	[LogMessage] [NVARCHAR](MAX) NULL,
	[CreateDate] [DATETIME] NULL,
    CONSTRAINT [PK_Logs] PRIMARY KEY CLUSTERED  ([Id] ASC)
 )
```

2. Click on **Run** button to run our code.

3. On the Messages tab, if you see **Query succeed**, our code worked successfully.

-----

![AzureSQLDB09](/assets/images/posts/2017051101/sc09.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. To see if your table is created, we type our select query in the query editor.

```sql
SELECT * FROM Logs
```

2. Click on **Run** button to run code.

3. Click on **Result** tab.

4. As we have seen, our columns appear on screen. **No results** because there is no dat in log table yet.

Our database is ready with this step. 👍

### Azure Function App

We are configuring our Azure Function App service using the Azure Portal.

![AzureSQLDB10](/assets/images/posts/2017051101/sc10.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click on **New** from left menu.

2. Type  **Function App** in search field.

3. Click on **Function App** item.

-----

![AzureSQLDB11](/assets/images/posts/2017051101/sc11.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click the **Create** button on the Function App to start creating the Function App.

-----

![AzureSQLDB12](/assets/images/posts/2017051101/sc12.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **App name**, name of our application.

2. **Subscription**, Select Azure Subscription.

3. **Resource Group**, Select the resource group to be used. I chose a resource group that I had previously configured at WestEurope location. If you don't have a previously defined resource group, you can create a new resource group by pressing **Create new**. 

4. **Hosting Plan**, here you can select to **Consumption Plan**.

5. **Location**, select nearest Azure location. From my location **West Europe** data center is nearest. This will be change depends on your location.

6. **Storage**, You can create a new storage or choose from the available ones.

7. **Application Insights**, You can leave **Off** for now.

8. Click on **Create** button to create **Function App** service.

-----

Open the Function App we have created.

![AzureSQLDB13](/assets/images/posts/2017051101/sc13.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click on **All resources** from left menu.

2. Find the **App name** and click on it.

> If there are lot of resource you can filter. 

Function App screen will look like this:

![AzureSQLDB14](/assets/images/posts/2017051101/sc14.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Function App status **Running** ✅

Now, we are ready to create a new Azure Function.😄

-----

![AzureSQLDB15](/assets/images/posts/2017051101/sc15.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Here we expand the arrow mark next to mylogapp.

2. Click on **+** icon.

3. Click on **create your own custom function.** link.

> Actually, you can click here Webhook + API, CSharp and Create this function button. However, the name of our function will be given automatically. We have not yet been able to rename the function on the portal so do it like step 3.

-----

![AzureSQLDB16](/assets/images/posts/2017051101/sc16.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Language** select C # as the language.

2. **Scenario** Big thanks to Function Apps team for this feature. We have logical groups.

3. **GenericWebHook-CSharp** this is most suitable scenario right now.

4. Here we give the name of our function. As I mentioned earlier, it is difficult to change the function name later. Give it carrefully. I typed it as **MyImportantFunction**. I told you before it is important function. 😄

5. Click on  **Create** button to create our fuction.

![AzureSQLDB17](/assets/images/posts/2017051101/sc17.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

After we have our function, the code editor will look like the above.

-----

![AzureSQLDB18](/assets/images/posts/2017051101/sc18.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click on the **View files** panel to make it visible.

-----

![AzureSQLDB19](/assets/images/posts/2017051101/sc19.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


1. To use the required nuget packages, we click **Add** button in **View files**.

2. Create a file named project.json**.

-----

![AzureSQLDB20](/assets/images/posts/2017051101/sc20.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


1. **project.json** appears empty in editor.

```json

{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Dapper": "1.42.0",
        "System.Data.SqlClient":"4.1.0",
        "Microsoft.WindowsAzure.ConfigurationManager":"3.2.1"
      }
    }
   }
}

```

In **project.json** file, type the json above.

2. Click on **Save** button to save **project.json**.

3. We click on the up arrow icon to the right of **Logs**. So we can see what's happening when we run the code.

-----

![AzureSQLDB21](/assets/images/posts/2017051101/sc21.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


1. Click on **Save and run** button to run our code.

2. You can see that the nuget packages in **Logs** section.

![AzureSQLDB22](/assets/images/posts/2017051101/sc22.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


-----

![AzureSQLDB23](/assets/images/posts/2017051101/sc23.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. When we see  **Packages restored.** and **Compilation succeeded** in **Logs** section, we have installed the nuget packages. 👍

-----

Now, we need the **connection string** information to connect to the database we have created.

![AzureSQLDB24](/assets/images/posts/2017051101/sc24.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click on **SQL databases**.

2. Select **LogDB**.

3. Select **Overview**.

4. On this blade, click on **Show database connection strings** link.


-----

![AzureSQLDB25](/assets/images/posts/2017051101/sc25.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. We copy the **connection string** information on **ADO.NET** tab to notepad.

> Server=tcp:log-server.database.windows.net,1433;Initial Catalog=LogDB;Persist Security Info=False;User ID=logserveradmin;Password=LogServer1234;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Your connection string information will be similar to the above.
Replace {your_username} and {your_password} with your **username** and **password**, your **connection string** information will be ready.

-----

We should have to define our **connection string** to use in our function.

![AzureSQLDB26](/assets/images/posts/2017051101/sc26.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Click on **All Resources** from left menu.

2. Find the Function App we are working on and click on it.

3. Select Function App

4. Click on **Platform fetures** tab.

5. Select **Application settings** item.

-----

![AzureSQLDB27](/assets/images/posts/2017051101/sc27.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. In **Name** field type, **SqlConnection**

2. Paste **connection string** text to **Value** field from Notepad.

3. Choose database as **SQL Database**.

4. Click on **Save** button.

-----

![AzureSQLDB28](/assets/images/posts/2017051101/sc28.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Open editor to write **code.**

1. From left menu select **All resources** 

2. Find the Function App we are working on and click on it.

3. Select Function App

4. Click on **Functions** 

5. Finally Click on **MyImportantFunction** and open the code editor.

![AzureSQLDB29](/assets/images/posts/2017051101/sc29.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Expand **View files** section.

1. Finally write our code!

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using Newtonsoft.Json;
using Dapper;
using System.Data.SqlClient;
using System.Configuration;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"Fonksiyon başlatıldı! RequestUri={req.RequestUri}");

    //Very important tasks here :)
    //.....
    //.....
    
    //We retrieve the userName field, which comes as a parameter to the function, by deserializing req.Content.
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    //If there is no username, we return the error message.
    if (data.userName == null) {
        return req.CreateResponse(HttpStatusCode.BadRequest, new {
            error = "Lütfen fonksiyonu çağıran kullanıcı adını giriniz."
        });
    }

    //Azure SQLDB Log 
    var logAdded = true;
    try
    {

        //We get the Connection String in the Function App Settings section we defined.
        var connectionString  = ConfigurationManager.ConnectionStrings["SqlConnection"].ConnectionString;
        

        using(var connection = new SqlConnection(connectionString))
        {
            //Opens Azure SQL DB connection.
            connection.Open();
            
            var logMessage = $"Fonksiyon {data.userName} tarafından {DateTime.UtcNow} tarihinde çağırılmıştır."; 
            
            // Insert Log to database.
            connection.Execute("INSERT INTO [dbo].[Logs] ([LogMessage], [CreateDate]) VALUES (@logMessage, @createDate)", new { logMessage, createDate =  DateTime.UtcNow} );
            log.Info("Log kaydı başarılı şekilde veritabanına eklenmiştir!");
        }
    }
    catch
    {
        logAdded = false;
    }

   // We complete our function. According to its success status, it will display the message.
   return !logAdded
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Something went wrong!")
        : req.CreateResponse(HttpStatusCode.OK, "Azure Function worked successfully and this is sooo cooool!");
}
```

2. We need to give the username as a parameter to our function. Click on the **Test** tab and define **Request body** as JSON.

```json
{
    "userName": "Mehmet Kut"
}
```

3. Click on the **Save** button to save function.

> Do not forget to make visible **Logs** tab to see result.

4. Click on **Run** button and run our code!

![AzureSQLDB30](/assets/images/posts/2017051101/sc30.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

If there is no error after the code run is finished, **Logs** will have similar results as above.

-----

Kodumuz başarılı şekilde çağırıldı peki tablomuza kayıtlar yansıdımı kontrol edelim.

Let's check that our code was successfully runned. Check our table.

![AzureSQLDB31](/assets/images/posts/2017051101/sc31.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. From left menu select **SQL databases**

2. Select Log database.

4. Click on **Overview** item.

5. Click on **Tools** button.

-----

![AzureSQLDB32](/assets/images/posts/2017051101/sc32.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Select the **Query editor** 

1. Click on the **Login** button. Ekranda giriş işlemleri yabileceğimiz bir kısım açılacak.

2. Choose **SQL server authentication** as **Authorization type**.

3. **Login** type the username that we defined while creating the SQL server.

4. **Password** type the password that we defined while creating the SQL server.

5. Click on **OK**, button and connect to server.

![AzureSQLDB33](/assets/images/posts/2017051101/sc33.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

-----

1. Write our query to code ediyor. 

```sql
SELECT * FROM Logs
```

2. Click on **Run** button.

3. Go to **Result** tab. As you see our records appeared.👍

-----

### Conclusion

Congratulations if you follow the article step by step. Now you know the answers to the following questions. 😄  


- How to configure Azure SQL server via Azure Portal?

- How to add a database to Azure SQL server and how to insert a table in this database?

- How to query a table in Azure SQL database?

- How do I create an Azure Function App?

- How to use NuGet packages in Azure Function?

- How to make Azure SQL DB connection within Azure Function?

- How to add record to Azure SQL DB using Azure Functions?

One last note, probably you already notice we only used our **browser**. 

**Azure Portal** is on its way to becoming a more powerful tool day by day. 👍