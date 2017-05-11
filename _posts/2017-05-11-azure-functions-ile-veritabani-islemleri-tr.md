---
layout: post
title: Azure Function App ile veritabanı işlemleri
comments: true
tags: [Türkçe, Microsoft, Azure, Azure Functions, Function App, Veritabanı,  Database, Database Operations]
description: Azure Function App ile veritabanı işlemleri.
---

Bu yazımda basit bir senaryo üzerinden **Azure Function** ile veritabanı işlemleri nasıl yapılır gösteriyor olacağım.

### Senaryo 

Bizim için önemli bir görevi yerine getiren, Azure Function var. Bun Azure Function çeşitli uygulamalar tarafından çağırılmakta.
Bizde Azure Function her çağrıldığında Azure SQLDB de log tablomuza bir kayıt atıp olan biteni kayıt etmek istiyoruz.

### Gerekenler 

1. Azure SQL DB
2. Azure Function App

### Azure SQL DB

Öncelikle Azure Function ile veri yazacağımız veritabanını yapılandırmamız gerekiyor. 

![AzureSQLDB01](/assets/images/posts/2017051101/sc01.png)

1. Azure Portal'de Sol menüden **SQL Databases** i seçiyoruz.
2. Açılacak olaran kısımdan **Add** butonuna tıklıyoruz.

![AzureSQLDB02](/assets/images/posts/2017051101/sc02.png)

1. Başlangıç olarak yeni bir **Server** oluşturuyoruz. Burada daha önce bir **Server** yapılandırdıysanız hazır olanlardan seçebilirsiniz. Tabi hazır seçtiğiniz sunucuya bağlantı için kullanıcı adı ve şifreye ihtiyacınız olacak.

2. **Create a new server** ile yeni sunucu oluşturma işlemine başlıyoruz.

3. **Server name**, alanına sunucu adımızı giriyoruz.

4. **Server admin login**, bu sonra sunucumuza bağlanmak için kullanacağımız kullanıcı adı olacak. 

5. **Password**, bu alanda aynı şekilde bağlantı bilgilerindeki erişim şifremiz olacak.

6. **Confim password**, şifremizi tekrar giriyoruz.

7. **Location**, West Europe seçiyoruz.

8. **Select** butonuna tıklıyoruz ve Sunucumuz hazır :)

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

![AzureSQLDB04](/assets/images/posts/2017051101/sc04.png)

1. Oluşturduğumuz veritabanı üzerinde çalışmak için en soldaki menüden **SQL databases** seçeneğine tıklıyoruz.

2. Gelen kısımdan, **LogDB** seçiyoruz.

3. **Overview** e tıklıyoruz.

4. **Tools** butonuna tıklıyoruz.

![AzureSQLDB05](/assets/images/posts/2017051101/sc05.png)

1. Veritabanına bağlanmak için aslında bir kaç seçeneğimiz mevcut mesela Visual Studio, SQL Server Management Studio gibi. Ancak ben şu an preview aşamasında olan **Query editor** ü seçeceğim. Böylece Azure Portal ortamından çıkmadan işime devam edebiliyor olacağım. Bu kısımdan **Query editor** e tıklayıp devam edeceğiz. Ön izleme aşamasında olduğundan size kullanmak istediğinize emin misiniz diye bir soru sorabilir bu soruya olumlu cevap verip devam edebilirsiniz.

![AzureSQLDB06](/assets/images/posts/2017051101/sc06.png)

1. **Login** butonuna tıklıyoruz. Ekranda giriş işlemleri yabileceğimiz bir kısım açılacak.

2. **Authorization type**, olarak **SQL server authentication** seçiyoruz.

3. **Login** SQL sunucusu oluştururken tanımlamış olduğumuz kullanıcı adını giriyoruz.

4. **Password**, kullanıcı için tanımladığımız şifreyi giriyoruz.

5. **OK**, butonuna tıklayıp sunucuya bağlanıyoruz.


![AzureSQLDB07](/assets/images/posts/2017051101/sc07.png)

> Herşey yolunda gittiyse yukarıdaki ekran görüntüsündeki gibi **Authenticated as sizin_kullanıcı_adınız** mesajını görmelisiniz.

![AzureSQLDB08](/assets/images/posts/2017051101/sc08.png)

1. Log tutacak tablomuzu oluşturuyoruz. Oluşturmak için gerekli kodu bu alana yazıyoruz. 

Tablodaki alanlarımız.

- **Id** Primary Key alanımız.
- **LogMessage** Log mesajlarımızı bu alanda saklayacağız.
- **CreateDate** Log ne zaman oluşturuldu verisi burada saklayacağız.

> Kopyala yapıştır kod aşağıdan alabilirsiniz.

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


![AzureSQLDB09](/assets/images/posts/2017051101/sc09.png)

1. Tablomuzun oluştuğunu görmek için sorgu editörüne select sorgumuzu yazıyoruz.

```sql
SELECT * FROM Logs
```

2. **Run** butonuna tıklayıp kodumuzu çalıştırıyoruz.

3. **Result** sekmesine tıklıyoruz.

4. Gördüğümüz gibi kolonlarımız geldi. Henüz log olmadığı için **No results** yazması normal.

Bu adımla beraber veritabanımız hazır. 

### Azure Function App



### Sonuç