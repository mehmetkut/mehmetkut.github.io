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

Kurumumuzda hali hazırda **ASP.NET Core Web API** ile yazılmış REST API kaynakları var. Bu kaynaklar **Azure** üzerinde barındırılmakta ve IOS, Android ve çeşitli Backend istemciler tarafından kullanılmakta. Aynı zamanda Azure üzerinde **Azure Active Directory(AAD)** serviside yapılandırılmış durumda.  Ne yazıkki kaynaklarda şu an bir güvenlik sistemi yok. İsteyen herkes **POST** veya **GET** requestleri ile bu kaynaklara erişebilmekte. Kaynakları olabilecek en güvenli ve kısa yoldan koruma altına almak istiyorsunuz.

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

4. **OK** butonuna basıp devam ediyoruz.

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
