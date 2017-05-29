---
layout: post
title: ASP.NET Core Web API kaynaklarını Azure Active Directory ile koruma.
comments: true
tags: [ASP.NET, ASP.NET Core, Microsoft Azure, Azure, ASP.NET Core Web API, Azure Active Directory, Azure AD, Active Directory, JSON Web Token]
description: ASP.NET Core Web API kaynaklarını Azure Active Directory ile koruma.
---

Bu yazımda gerçek bir senaryo üzerinden **ASP.NET Core Web API** kaynaklarımızı **Azure Active Directory** ile nasıl koruma altına alırız gösteriyor olacağım.

-----

### Senaryo

Kurumumuzda hali hazırda **ASP.NET Core Web API** ile yazılmış REST API kaynakları var. Bu kaynaklar **Azure** üzerinde barındırılmakta ve IOS, Android ve çeşitli Backend istemciler tarafından kullanılmakta. Aynı zamanda Azure üzerinde **Azure Active Directory(AAD)** serviside yapılandırılmış durumda.  Ne yazık ki kaynaklarda şu an bir güvenlik sistemi yok. İsteyen herkes **POST** veya **GET** requestleri ile bu kaynaklara erişebilmekte. Kaynakları olabilecek en güvenli ve kısa yoldan koruma altına almak istiyorsunuz.

-----

### Çözüm Arayışı

- Bu iş için kendi güvenlik sisteminizi yazabilirsiniz. Ancak bu oldukça vakit alıcı ve güvenliği kanıtlanmamış bir çözüm olacaktır. 
- Üçüncü parti bir yazılım satın alabilirsiniz. Bu seçenekte maliyetli olacaktır.
- Hali hazırda bulunan **AAD** servisinizi kullanarak güvenliği sağlarsınız.

Daha önce belirttiğim gibi **AAD** servisiniz varsa bu servisi kullanarak **JSON Web Token(JWT)** ile erişimi kontrol edebilir ve kaynaklarınızı koruyabilirsiniz.

-----

### Gerekenler 

1. ASP.NET Core Web API
2. Azure Active Directory

-----

### ASP.NET Core Web API ###

Bu aşamada ASP.NET Core Web API ile örnek bir REST API oluşturup, **Azure** ortamında **App Service** olarak yayınlayacağız. 
Projeyi Visual Studio 2017 ile oluştaracağım. 

-----

![ASPNETCOREAADJWT01](/assets/images/posts/2017052901/sc01.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Visual Studio 2017 yi çalıştırıyoruz.

1. **File** menüsünü seçiyoruz.

2. **New** seçeneği üzerine geliyoruz.

3. **Project...** seçeneğine tıkılıyoruz.

-----

![ASPNETCOREAADJWT02](/assets/images/posts/2017052901/sc02.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Sol taraftan **.NET Core** seçeneğini seçiyoruz. 

2. Gelen seçeneklerden **ASP.NET Core Web Application (.NET Core)** seçeneğini işaretliyoruz.

3. Projemizin adını yazıyoruz. Ben **CorpAPI** olarak adlandırdım.

4.  **OK** butonuna basıp devam ediyoruz.

-----

![ASPNETCOREAADJWT03](/assets/images/posts/2017052901/sc03.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **ASP.NET Core 1.1** seçiyoruz.

2. Projemizin tipini **Web API** olarak seçiyoruz.

3. **Authentication** kısmının **No Authentication** olduğuna emin oluyoruz. Değilse Change Authentication butonuna basıp değiştiriyoruz.

4. **OK** butonuna tıklayıp projemizi oluşturuyoruz.

-----

Projemiz oluştuktan sonra aşağıdakine benzer bir ekran görüntüsü karşımıza gelecek.

![ASPNETCOREAADJWT04](/assets/images/posts/2017052901/sc04.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


-----

**Azure Portal** üzerinden uygulamamızın çalışacağı **Azure App Service**'i yapılandırıyoruz.

![ASPNETCOREAADJWT05](/assets/images/posts/2017052901/sc05.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Sol menüden **New** seçeneğini seçiyoruz.

2. Açılacak kısımdan **Web + Mobile** seçeneğini seçiyoruz.

3. **Web App** seçeneğini işaretliyoruz.

4. **App Name** kısmına **corpapi** olarak uygulamamızı adlandırıyoruz. Azure yazdığınız adı kabul etmezse bir ad yazabilirsiniz.

5. **Subscription** seçeneğinden uygun Azure aboneliği seçiyoruz.

6. **Resource Group** olarak yeni bir Resource Group oluşturmak istedik. **Create new** seçeneğini seçip Resource Group adımı **RG-CorpApi** olarak yazıyoruz.

7. **App Service Plan** olarak bu demo senaryosu olduğu için free app service plan seçtik. Senaryoya göre bu değişecektir.

8. **Create** butonuna tıklayarak **App Service**'i oluşturuyoruz.

-----

![ASPNETCOREAADJWT06](/assets/images/posts/2017052901/sc06.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Oluşturduğumuz **App Service** i açıyoruz.

1.  Sol menüden **Resource groups** seçeneğini seçiyoruz.

2. Oluşturduğumuz **Resource group** adına yani **RG-CorpApi** ye tıklıyoruz.

3. Açılan kısımdan **Overview** seçeneğine tıklıyoruz.

4. **AppService** adını açılan kısımda görmemiz gerek. Adını **corpapi** olarak belirlemiştik. **corpapi** ye tıklayarak servisimizi açıyoruz.

-----

![ASPNETCOREAADJWT07](/assets/images/posts/2017052901/sc07.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Local ortamda bulunan projemizi, Azure üzerinde çalıştırmak için gerekli ayarların bulunduğu dosyayı ediniyoruz.

1. **Overview** seçeneğini seçiyoruz.

2. **Get publish profile** butonuna tıklıyoruz.

3. Tarayıcımız **corpapi.PublishSettings** adında bir dosya indirdi. Bu dosyayı localdeki uygulamamızı **Azure** ortamına göndermek için kullanıyor olacağız.

-----

![ASPNETCOREAADJWT08](/assets/images/posts/2017052901/sc08.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **CoreAPI** projemize sağ tıklıyoruz.

2. Açılan menüden **Publish...** seçeneğine tıklıyoruz.

-----

![ASPNETCOREAADJWT09](/assets/images/posts/2017052901/sc09.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Ekrandan **Import profile** seçeneğini işaretliyoruz.

2. **Publish** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT10](/assets/images/posts/2017052901/sc10.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Import Publish Settings File** penceresinden daha önceden **Azure Portal** üzerinden indirdiğimiz dosyayı seçiyoruz. Bendeki dosya adı **corpapi.PublishSettings**  sizde  farklı olabilir.

2. **Open** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT11](/assets/images/posts/2017052901/sc11.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Projemiz **Azure** ortamına gönderilirken. **Output** kısmında benzer bilgilendirmeler görebilirsiniz.

2. Gönderme işlemi bitince **Output** kısmında **Publish: 1  succeeded...** yazısı görmeniz gerekiyor. 

-----

![ASPNETCOREAADJWT12](/assets/images/posts/2017052901/sc12.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Tarayıcımızı açıyoruz.

1. Adres satırına http://corpapi.azurewebsites.net/api/values yazıp enter a basıp sayfayı açıyoruz. Bu adres sizin app service adınıza göre değişecektir.

2. API çalışıyorsa ekrandaki gibi bir JSON çıktısı olacak.

Şu an için API hazır. Ancak senaryomuzda belirttiğimiz gibi kaynaklar herkese açık bir ortamda duruyor. 


*makale henüz bitmedi*