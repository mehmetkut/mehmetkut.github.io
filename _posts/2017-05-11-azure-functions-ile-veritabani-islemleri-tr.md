---
layout: post
title: Azure Function App ile veritabanı işlemleri
comments: true
tags: [Türkçe, Microsoft, Azure, Azure Functions, Function App, Veritabanı,  Database, Database Operations]
description: Azure Function App ile veritabanı işlemleri.
---

Bu yazımda basit bir senaryo üzerinden **Azure Function** ile veritabanı işlemleri nasıl yapılır gösteriyor olacağım.

### Senaryo 

Bizim için önemli bir görevi yerine getiren bir **Azure Function** var. Bu Azure Function çeşitli uygulamalar tarafından çağırılmakta.
Bizde Azure Function her çağrıldığında Azure SQLDB de log tablomuza bir kayıt atıp, olan biteni kayıt etmek istiyoruz.

### Gerekenler 

1. Azure SQL DB
2. Azure Function App

### Azure SQL DB

Öncelikle Azure Function ile veri yazacağımız veritabanını yapılandırmamız gerekiyor. 

![AzureSQLDB01](/assets/images/posts/2017051101/sc01.png){: class="jslghtbx-thmb jslghtbx-animate-init"  data-jslghtbx="" }

1. Azure Portal'de Sol menüden **SQL Databases** i seçiyoruz.
2. Açılacak olaran kısımdan **Add** butonuna tıklıyoruz.

-----

![AzureSQLDB02](/assets/images/posts/2017051101/sc02.png)

1. Başlangıç olarak yeni bir **Server** oluşturuyoruz. Burada daha önce bir **Server** yapılandırdıysanız hazır olanlardan seçebilirsiniz. Tabi hazır seçtiğiniz sunucuya bağlantı için kullanıcı adı ve şifreye ihtiyacınız olacak.

2. **Create a new server** ile yeni sunucu oluşturma işlemine başlıyoruz.

3. **Server name**, alanına sunucu adımızı giriyoruz.

4. **Server admin login**, bu sonra sunucumuza bağlanmak için kullanacağımız kullanıcı adı olacak. 

5. **Password**, bu alanda aynı şekilde bağlantı bilgilerindeki erişim şifremiz olacak.

6. **Confim password**, şifremizi tekrar giriyoruz.

7. **Location**, West Europe seçiyoruz.

8. **Select** butonuna tıklıyoruz ve sunucumuz hazır. 👍

-----

![AzureSQLDB03](/assets/images/posts/2017051101/sc03.png)

1. **Database name**, Loglarımızı tutacağımız veritabanı adı. Ben LogDB olarak girdim. Siz farklı bir isimlendirme standardınız varsa kullanabilirsiniz.

2. **Subscription**, Veritabanını hangi Azure Subscription içerisinde oluşturacağınızı seçin.

3. **Resource Group**, Kullanılacak resource group seçimi yapın. Ben daha önce WestEurope ta yapılandırmış olduğum bir resource group seçtim. Eğer daha önceden tanımlı bir resource group yoksa **Create new** e basarak yeni bir resource group oluşturabilirsiniz.

> Resource group oluştururken veya başka bir Azure servisi oluşturuken Hedef kitleniz Türkiye'de ise mutlaka **West Europe** lokasyonunu seçiniz.

4. **Select source**, Boş bir veritabanı istediğimiz için Blank database seçiyoruz.

5. **Server**, Sunucuyu daha önceki adımlarda ayarlamıştık.

6. **Want to use SQL elastic pool?**, Şimdilik, Not now diyerek ilerleyebiliriz.

7. **Pricing tier**, Fiyat kademesini seçiyoruz. Ben gerçek bir senaryoda kullanılmayacağı için **Basic: 5 DTU, 100MB** seçtim.

8. **Collation**, Kulllanılacak veritabanı Collation olarak SQL_Latin1_General_CP1_CI_AS kalabilir.

9. **Create**, butonuna tıklayıp veritabanımızı oluşturuyoruz.

-----

![AzureSQLDB04](/assets/images/posts/2017051101/sc04.png)

1. Oluşturduğumuz veritabanı üzerinde çalışmak için en soldaki menüden **SQL databases** seçeneğine tıklıyoruz.

2. Gelen kısımdan, **LogDB** seçiyoruz.

3. **Overview** e tıklıyoruz.

4. **Tools** butonuna tıklıyoruz.

-----

![AzureSQLDB05](/assets/images/posts/2017051101/sc05.png)

1. Veritabanına bağlanmak için aslında bir kaç seçeneğimiz mevcut mesela Visual Studio, SQL Server Management Studio gibi. Ancak ben şu an preview aşamasında olan **Query editor** ü seçeceğim. Böylece Azure Portal ortamından çıkmadan işime devam edebiliyor olacağım. Bu kısımdan **Query editor** e tıklayıp devam edeceğiz. Ön izleme aşamasında olduğundan size kullanmak istediğinize emin misiniz diye bir soru sorabilir bu soruya olumlu cevap verip devam edebilirsiniz.

-----

![AzureSQLDB06](/assets/images/posts/2017051101/sc06.png)

1. **Login** butonuna tıklıyoruz. Ekranda giriş işlemleri yabileceğimiz bir kısım açılacak.

2. **Authorization type**, olarak **SQL server authentication** seçiyoruz.

3. **Login** SQL sunucusu oluştururken tanımlamış olduğumuz kullanıcı adını giriyoruz.

4. **Password**, kullanıcı için tanımladığımız şifreyi giriyoruz.

5. **OK**, butonuna tıklayıp sunucuya bağlanıyoruz.

-----

![AzureSQLDB07](/assets/images/posts/2017051101/sc07.png)

Herşey yolunda gittiyse yukarıdaki ekran görüntüsündeki gibi **Authenticated as sizin_kullanıcı_adınız** mesajını görmelisiniz.

-----

![AzureSQLDB08](/assets/images/posts/2017051101/sc08.png)

1. Log tutacak tablomuzu oluşturuyoruz. Oluşturmak için gerekli kodu bu alana yazıyoruz. 

Tablodaki alanlarımız.

**Id** Primary Key alanımız.

**LogMessage** Log mesajlarımızı bu alanda saklayacağız.

**CreateDate** Log ne zaman oluşturuldu verisi burada saklayacağız.

```sql
CREATE TABLE [dbo].[Logs](
	[Id] [INT] IDENTITY(1,1) NOT NULL,
	[LogMessage] [NVARCHAR](MAX) NULL,
	[CreateDate] [DATETIME] NULL,
    CONSTRAINT [PK_Logs] PRIMARY KEY CLUSTERED  ([Id] ASC)
 )
```

2. **Run** butonuna tıklayıp kodumuzu çalıştırıyoruz.

3. Messages sekmesinde, **Query succeed** yazıyorsa kodumuz başarılı şekilde çalışmıştır. 

-----

![AzureSQLDB09](/assets/images/posts/2017051101/sc09.png)

1. Tablomuzun oluştuğunu görmek için sorgu editörüne select sorgumuzu yazıyoruz.

```sql
SELECT * FROM Logs
```

2. **Run** butonuna tıklayıp kodumuzu çalıştırıyoruz.

3. **Result** sekmesine tıklıyoruz.

4. Gördüğümüz gibi kolonlarımız geldi. Henüz log olmadığı için **No results** yazması normal.

Bu adımla beraber veritabanımız hazır. 👍

### Azure Function App

Azure Function App servisimizi Azure Portal'i kullanarak yapılandırıyoruz.

![AzureSQLDB10](/assets/images/posts/2017051101/sc10.png)

1. Sol menüden New seçeneğine tıklıyoruz.

2. Arama kısmına Function App yazıyoruz.

3. Everything bölümünde **Function App** tıklıyoruz.

-----

![AzureSQLDB11](/assets/images/posts/2017051101/sc11.png)

1. Function App kısmından **Create** butonuna tıklayarak Function App oluşturma işlemine başlıyoruz.

-----

![AzureSQLDB12](/assets/images/posts/2017051101/sc12.png)

1. **App name**, uygulamamızın adı 

2. **Subscription**, Function App hangi Azure Subscription içerisinde oluşturulmasını istiyorsanız onu seçin.

3. **Resource Group**, kullanılacak resource group seçimi yapın. Daha önce oluşturduğunuz resource grouplardan seçebilir veya **Create new** e basarak yeni bir resource group oluşturabilirsiniz. Ben yeni bir tane oluşturmayı tercih ettim.

4. **Hosting Plan**, burada **Consumption Plan** olarak ilerleyebilirsiniz. Bu seçenek detayları farklı bir blog yazısı konusu.

5. **Location**, Lokasyon tabiki **West Europe**

6. **Storage**, Yeni bir storage oluşturabilir veya mevcut olanlarından seçebilirsiniz.

7. **Application Insights**, Şimdilik **Off** konumda bırakabilirsiniz.

8. **Create** butonuna tıklayıp **Function App** servisimizi oluşturmaya başlıyoruz.

-----

Oluşturmuş olduğumuz Function App i açıyoruz.

![AzureSQLDB13](/assets/images/posts/2017051101/sc13.png)

1. Sol menüden **All resources** seçeneğini tıklıyoruz.

2. Gelen listeden daha önce verdiğimiz **App name** bulup Function App'i açıyoruz.

> Liste çok kabarıksa üst taraftaki filtre seçenekleri kullanılabilir.

Function App ekranımız aşağıdaki gibi olacaktır.  

![AzureSQLDB14](/assets/images/posts/2017051101/sc14.png)

Status bölümü **Running** ✅

Yeni bir Azure Function oluşturmaya hazırız.😄

-----

![AzureSQLDB15](/assets/images/posts/2017051101/sc15.png)

1. Burada mylogapp'in yanındaki ok işaretini aşağı doğru genişletiyoruz.

2. **Functions** seçeneğinin sağ tarafındaki **+** ikonuna tıklıyoruz.

3. Sağ taraf ekranda gördüğünüz **create your own custom function.** linkine tıklıyoruz. 

> Aslında burada Webhook + API, CSharp ve Create this function butonuna tıklayabilirsiniz. Ancak fonksiyonumuzun adı otomatik olarak verilecek. Henüz portal üzerinde yeniden fonksiyon adlandırma yapılamadığından ve son adımda yazdığım gibi ilerliyoruz.

-----

![AzureSQLDB16](/assets/images/posts/2017051101/sc16.png)

1. **Language** Kullanacağımız dil olarak C# seçiyoruz.

2. **Scenario** Bir güzellik Function Apps ekibi senaryo senaryo yapılabilecekleri gruplamış.

3. **GenericWebHook-CSharp** şu an için bize en uygun senaryo.

4. **Name your function** Burada fonksiyonumuzun adını veriyoruz. Daha öncede belirttiğim gibi fonksiyon adı daha sonradan değiştirmek zor. İyice düşünerek verin. Ben **MyImportantFunction** olarak yazdım. Yazımızın başında önemli olduğunu söylemiştim. 😄

5. **Create** butonu ile fonksiyonumuzu oluşturuyoruz.

![AzureSQLDB17](/assets/images/posts/2017051101/sc17.png)

Fonksiyonumuz oluştuktan sonra kod editörü yukarıdaki gibi görünecektir.

-----

![AzureSQLDB18](/assets/images/posts/2017051101/sc18.png)

1. **View files** paneline tıklayarak görünür hale getiriyoruz.

-----

![AzureSQLDB19](/assets/images/posts/2017051101/sc19.png)


1. Gerekli paketleri kullanabilmek için **View files** kısmından **Add** butonuna tıklıyoruz.

2. **project.json** adında bir dosya oluşturuyoruz. 

-----

![AzureSQLDB20](/assets/images/posts/2017051101/sc20.png)


1. **project.json** dosyamızın için boş editörde boş gözükmekte.

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

Kod editörümüze yukarıdaki json'u yazıyoruz.

2. **Save** butonuna tıklayıp içerimizi **project.json** dosyasına kayıt ediyoruz.

3. **Logs** kısmının sağındaki yukarı ok ikonuna tıklıyoruz. Böylece kodu çalıştırdığımızda olan biteni görebileceğiz.

-----

![AzureSQLDB21](/assets/images/posts/2017051101/sc21.png)


1. **Save and run** butonuna tıklayıp kodumuzu çalıştırıyoruz.

2. **Logs** kısmında yazmış olduğumuz paketlerin NuGet ile yüklendiğini görebilirsiniz. 

![AzureSQLDB22](/assets/images/posts/2017051101/sc22.png)


-----

![AzureSQLDB23](/assets/images/posts/2017051101/sc23.png)

1. **Logs** kısmında **Packages restored.** ve **Compilation succeeded** yazdığını gördüğümüzde paketler yüklenmiştir. 👍


-----

Oluşturmuş olduğumuz veritabanına bağlanmak için **Connection string** bilgisine ihtiyacımız var.

![AzureSQLDB24](/assets/images/posts/2017051101/sc24.png)

1. Sol menüden **SQL databases** tıklıyoruz.

2. **LogDB** yi seçiyoruz.

3. **Overview** tıklıyoruz.

4. Açılan kısımdan **Show database connection strings** linkine tıklıyoruz.


-----

![AzureSQLDB25](/assets/images/posts/2017051101/sc25.png)

1. **ADO.NET** sekmesinde olan **connection string** bilgisini hızlı erişebileceğimiz (notepad gibi) bir yere kopyalıyoruz.

> Server=tcp:log-server.database.windows.net,1433;Initial Catalog=LogDB;Persist Security Info=False;User ID=logserveradmin;Password=LogServer1234;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Connection string bilginiz yukardakine benzer olacaktır.

{your_username} ve {your_password}** bölümlerini kendi kullanıcı adı ve şifrenizle değiştirdiğinizde **connection string** bilginiz hazır olacaktır.

-----

**Connection string** bilgimizi fonksiyonumuzda kullanmak için tanımlamamız gerekiyor.

![AzureSQLDB26](/assets/images/posts/2017051101/sc26.png)

1. Sol menüden **All Resources** tıklıyoruz.

2. Üzerinde çalıştığımız Function App  bulup tıklıyoruz.

3. Function App i seçiyoruz.

4. Üst taraftaki sekmelerden **Platform fetures** sekmesini seçiyoruz.

5. **Application settings** seçeneğine tıklıyoruz.


-----

![AzureSQLDB27](/assets/images/posts/2017051101/sc27.png)

Açılacak olan **Application settings** kısmında **Connection strings** bölümü biraz aşağıda kaldığından sayfayı aşağı kaydırmanız gerekiyor. **Connection strings** kısmına veritabanı bağlantılarında kullanmak üzere yeni bir satır tanımlayacağız.

1. **Name** kısmına **SqlConnection** yazın.

2. **Value** olarak daha önceden hazırladığımız ve notepad e kopyaladığımız **Connection string** i yazıyoruz.

3. Veritabanının tipini seçmemiz istenen açılır kutudanda **SQL Database** i seçiyoruz.

4. **Save** butonuna tıklayıp ayarlarımız kayıt ediyoruz.

-----

![AzureSQLDB28](/assets/images/posts/2017051101/sc28.png)

Kodumuzu yazmak için editörümüzü açmamız gerekiyor.

1. Sol menüden **All resources** seçeneğini tıklıyoruz.

2. Gelen listeden daha önce verdiğimiz **App name** bulup Function App'i açıyoruz.

3. Function App seçiyoruz.

4. **Functions** bölümüne tıklıyoruz.

5. **MyImportantFunction** a tıklayıp kod editörümüzü açıyoruz.

![AzureSQLDB29](/assets/images/posts/2017051101/sc29.png)

Sağ taraftan **View files** bölümünü genişletiyoruz.

1. Kodumuzu yazıyoruz.

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

    //Çok önemli kodlar burada :)
    //.....
    //.....
    
    //Fonksiyona parametre olarak gelen userName alanını req.Content deserialize ederek alıyoruz.
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    //Kullanıcı adı yoksa hata mesajı dönüyoruz.
    if (data.userName == null) {
        return req.CreateResponse(HttpStatusCode.BadRequest, new {
            error = "Lütfen fonksiyonu çağıran kullanıcı adını giriniz."
        });
    }

    //Azure SQLDB Loglama 
    var logAdded = true;
    try
    {

        //Tanımladığımız Function App Settings bölümündeki Connection String i alıyoruz.
        var connectionString  = ConfigurationManager.ConnectionStrings["SqlConnection"].ConnectionString;
        

        using(var connection = new SqlConnection(connectionString))
        {
            //Azure SQL DB bağlantımızı açıyoruz.    
            connection.Open();
            
            var logMessage = $"Fonksiyon {data.userName} tarafından {DateTime.UtcNow} tarihinde çağırılmıştır."; 
            
            // Log'u veritabanına yazıyoruz.
            connection.Execute("INSERT INTO [dbo].[Logs] ([LogMessage], [CreateDate]) VALUES (@logMessage, @createDate)", new { logMessage, createDate =  DateTime.UtcNow} );
            log.Info("Log kaydı başarılı şekilde veritabanına eklenmiştir!");
        }
    }
    catch
    {
        logAdded = false;
    }

   // Fonksiyonumuzu bitiriyoruz. Başarı durumuna göre çağıran kullanıcıya mesaj gösterecek.
   return !logAdded
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Hay aksi birşeyler ters gitti!")
        : req.CreateResponse(HttpStatusCode.OK, "Azure Function başarılı şekilde çalştı!");
}
```

2. Fonksiyonumuza parametre olarak kullanıcı adı vermemiz gerekmekte. **Test** sekmesine tıklayıp burada **Request body** kısmına json olarak yazıyoruz.

```json
{
    "userName": "Mehmet Kut"
}
```

3. Save butonuna tıklayıp kodumuzu kayıt ediyoruz.

> Çıktıyı görmek için alt taraftaki **Logs** sekmesini görünür duruma getirmeyi unutmayın!

4. Run butonuna tıklayıp kodumuzu çalıştırıyoruz.

![AzureSQLDB30](/assets/images/posts/2017051101/sc30.png)

Çalışma bittikten sonra bir hata yoksa **Logs** sekmesinde yukarıdakine benzer sonuçlar gözükecektir.

-----

Kodumuz başarılı şekilde çağırıldı peki tablomuza kayıtlar yansıdımı kontrol edelim.

![AzureSQLDB31](/assets/images/posts/2017051101/sc31.png)

1. Sol menüden SQL databases seçiyoruz.

2. Log veritabanımızı seçiyoruz.

4. **Overview** kısmına tıklıyoruz.

5. **Tools** butonuna tıklıyoruz.

-----

![AzureSQLDB32](/assets/images/posts/2017051101/sc32.png)

1. **Query editor** ü seçiyoruz.

1. **Login** butonuna tıklıyoruz. Ekranda giriş işlemleri yabileceğimiz bir kısım açılacak.

2. **Authorization type**, olarak **SQL server authentication** seçiyoruz.

3. **Login** SQL sunucusu oluştururken tanımlamış olduğumuz kullanıcı adını giriyoruz.

4. **Password**, kullanıcı için tanımladığımız şifreyi giriyoruz.

5. **OK**, butonuna tıklayıp sunucuya bağlanıyoruz.

![AzureSQLDB33](/assets/images/posts/2017051101/sc33.png)

-----

1. Log kayıtlarımızın  oluştuğunu görmek için sorgu editörüne select sorgumuzu yazıyoruz.

```sql
SELECT * FROM Logs
```

2. **Run** butonuna tıklayıp kodumuzu çalıştırıyoruz.

3. **Result** sekmesine tıklıyoruz. Gördüğümüz gibi log kayıtlarımız geldi.👍 Ben iki kere test amaçlı kodumu çalıştırmıştım iki satır log kaydım var. 

-----

### Sonuç

Eğer yazıyı adım adım takip ettiyseniz tebrikler. Artık aşağıdaki soruların cevaplarını biliyorsunuz. 😄  

- Azure Portal üzerinden SQL sunucu nasıl yapılandırılır?

- Azure SQL sunucusuna veritabanı ekleme ve bu veritabanı içerisinde nasıl tablo eklenir?

- Azure SQL veritabanında bir tablo nasıl sorgulanır?

- Azure Function App nasıl oluşturulur?

- Azure Function içerisinde NuGet paketlerini nasıl kullanılır?

- Azure Function kodundan, Azure SQL DB ye bağlanmak için gerekli ayarlar nasıl yapılır?

- Azure Function kodu içerisinden Azure SQL DB bağlantısı nasıl yapılır?

- Azure Function üzerinden veriler, Azure SQL DB de istenilen tabloya nasıl eklenir ve görüntülenir?


Ayrıca önemli bir nokta olarak yazı boyunca sadece **tarayıcı**mızı kullandık. Azure Portal'in her geçen gün daha güçlü bir araç olma yolunda ilerlediğini görmüş olduk.