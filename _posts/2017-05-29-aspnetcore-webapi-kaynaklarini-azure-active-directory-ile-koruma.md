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

### ASP.NET Core Web API

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

1. Adres satırına https://corpapi.azurewebsites.net/api/values yazıp enter a basıp sayfayı açıyoruz. Bu adres sizin app service adınıza göre değişecektir.

2. API çalışıyorsa ekrandaki gibi bir JSON çıktısı olacak.

Şu an için API hazır. Ancak senaryomuzda belirttiğimiz gibi kaynaklar herkese açık bir ortamda duruyor. 

### Azure Active Directory

Bu aşamadan sonra **AAD** üzerinde **AAD** uygulamaları oluşturup bunları güvenlik için kullanıyor olacağız.

-----

![ASPNETCOREAADJWT13](/assets/images/posts/2017052901/sc13.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Sol menüden **Azure Active Directory** seçeneğini seçiyoruz. Bu seçeneği bulamazsan sol menünün altında bulunan **More services** kısmından bulabilirsiniz.

2. Açılan kısımdan **App registrations** seçeneğine tıklıyoruz.

3. Yeni **AAD Uygulaması** kayıt etmek için, **New application registration** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT14](/assets/images/posts/2017052901/sc14.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. AAD uygulamasının adını **Name** alanına yazıyoruz. Ben **CorpAPI** olarak adlandırdım.

2. **Application type** seçimini **Web app / API** olarak bırakıyoruz. Bu uygulama tipi Web app veya API uygulaması olacağını belirtiyoruz.

3. **Sign-on URL** kısmına şimdilik API adresimizi yazıyoruz.

4. **Create** butonuna tıklayarak uygulamamızı oluşturuyoruz.

-----

![ASPNETCOREAADJWT15](/assets/images/posts/2017052901/sc15.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. AAD kısmında **App registrations** kısmına tıklıyoruz.

2. Açılacak kısımda **CorpAPI** olarak uygulamamızı arıyoruz.

3. Uygulamamıza tıklayarak açıyoruz.

-----

Uygulamamız açılınca aşağıdakine benzer bir ekran göreceksiniz.

![ASPNETCOREAADJWT16](/assets/images/posts/2017052901/sc16.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Bu ekranda önemli nokta **Application ID** bu GUID bizim uygulama tanıtıcımız. Bir köşeye kopyalasak iyi olur. 😄

-----

Sıra geldi gerekli izinleri ayarlamaya.

![ASPNETCOREAADJWT17](/assets/images/posts/2017052901/sc17.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Açık olan settings kısmından **Required Permissions** seçeneğini seçiyoruz.

2. Gelen **Required permissions** kısmında **Add** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT18](/assets/images/posts/2017052901/sc18.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Select an API** seçeneğini seçiyoruz. 

2. Açılan **Select an API** kısmından **Microsoft Graph** seçeneğini işaretliyoruz.

3. **Select** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT19](/assets/images/posts/2017052901/sc19.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Select permissions** seçeneğini seçiyoruz.

2. Bu aşamada **Application Permissions** ve **Delegated Permissions** kısımlarında bulunan **Read and write directory data** iznini seçiyoruz. Daha açıklayıcı olmak gerekirse **Application Permissions** altında bulunan **Read and write directory data** ve **Delegated Permissions**  altında bulunan **Read and write directory data** izinlerini seçiyoruz. Bu izinler dışında seçili varsa seçilileri kaldırıyoruz.

3. **Select** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT20](/assets/images/posts/2017052901/sc20.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. API ve izinlerimizi seçtik **Done** butonuna tıklayıp işlemimizi tamamlıyoruz.

-----

![ASPNETCOREAADJWT21](/assets/images/posts/2017052901/sc21.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Required permission** ekrnaında gördüğünüz gibi **Microsoft Graph** eklendi. Bir **Application Permission** bir de **Delegated Permission** izni var.

2. Varsayılan olarak ekli olan **Windows Azure Active Directory** seçeneğine tıklıyoruz.

3. Daha önce yaptığımız gibi, **Application Permissions** altında bulunan **Read and write directory data** iznini seçiyoruz

4. **Delegated Permissions**  altında bulunan **Read and write directory data** izinlerini seçiyoruz. 


5. Bu izinler dışında seçili varsa seçilileri kaldırıyoruz. **Save** butonuna tıklıyoruz.

-----

İzinleri verdikten sonra **Required permissions** kısmı aşağıdaki gibi gözükecektir.

![ASPNETCOREAADJWT22](/assets/images/posts/2017052901/sc22.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

-----

Benzer şekilde bir native AAD uygulaması daha ekliyeceğiz. Bu sefer uygulamamızın tipi **Native Application** olacak.

![ASPNETCOREAADJWT23](/assets/images/posts/2017052901/sc23.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }


1. Sol menüden **Azure Active Directory** seçeneğini seçiyoruz. Bu seçeneği bulamazsan sol menünün altında bulunan **More services** kısmından bulabilirsiniz.

2. Açılan kısımdan **App registrations** seçeneğine tıklıyoruz.

3. Yeni **AAD Uygulaması** kayıt etmek için, **New application registration** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT24](/assets/images/posts/2017052901/sc24.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. AAD uygulamasının adını **Name** alanına yazıyoruz. Ben **CorpNativeAPI** olarak adlandırdım.

2. **Application type** seçimini **Native** seçiyoruz. 

3. **Sign-on URL** kısmına https://corpnativeapi.azure-mobile.net yazıyoruz.

4. **Create** butonuna tıklayarak uygulamamızı oluşturuyoruz.

-----

![ASPNETCOREAADJWT25](/assets/images/posts/2017052901/sc25.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Uygulamaların listelendiği kısımda **CorpNativeAPI** olarak uygulamamızı arıyoruz.

2. Uygulamamıza tıklayarak açıyoruz.

-----

![ASPNETCOREAADJWT26](/assets/images/posts/2017052901/sc26.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Bu ekranda önemli nokta **Application ID** bu GUID bizim uygulama tanıtıcımız. Bir köşeye kopyalasak iyi olur. 😄

-----

![ASPNETCOREAADJWT27](/assets/images/posts/2017052901/sc27.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Açık olan settings kısmından **Required Permissions** seçeneğini seçiyoruz.

2. Gelen **Required permissions** kısmında **Add** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT28](/assets/images/posts/2017052901/sc28.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Select an API** seçeneğini seçiyoruz. 

2. Açılan **Select an API** kısmından **CorpAPI** olarak WebAPI uygulamamızı arıyoruz.

3. WebAPI uygulamamızı seçiyoruz.

4. **Select** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT29](/assets/images/posts/2017052901/sc29.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Select permissions** seçeneğini seçiyoruz.

2. **Access CorpAPI** iznini seçiyoruz.

3. **Select** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT30](/assets/images/posts/2017052901/sc30.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Done** butonuna tıklıyoruz.

-----

Herşey yolunda gittiyse aşağıdakine benzer bir **Required permissions** ekranı görmeniz gerekiyor.

![ASPNETCOREAADJWT31](/assets/images/posts/2017052901/sc31.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Son olarak yapmamız gereken **Grant Permissions** butonuna tıklayıp gelen soruya **Yes** cevabını veriyoruz. Böylece hesabımıza gerekli yetkileri vermiş olduk. 

-----

Şimdiye kadar AAD üzerinde yaptıklarımızı gözden geçirelim:

- AAD'de sözde bir **Web Application/Web API** oluşturduk ve gerekli izinleri ekledik.
- AAD'de yine sözde bir **Native** uygulama oluşturduk ve gerekli izinleri ekledik.

Burada AAD'de oluşturduğumuz her uygulama için benzersiz bir **Uygulama Kimliği** *(Application ID)* oluşturuldu. Bu benzersiz uygulama kimliklerinin oluşturulmasından AAD sorumlu. **Kaynak Sağlayıcısı** yani **Web Application/Web API** tipindeki uygulama ve **İstemci Uygulaması** yani **Native Application** tipindeki uygulamanın AAD ile "konuşması" gerektiğinde, bu Application ID leri bir şekilde gönderirler. Böylece AAD bunu kimin gönderdiğini belirler.

Azure AD konfigürasyonu olan "en az sevdiğim" bölümü tamamladık. 😄

-----

Şimdi **Postman** uygulamasını kullanarak API bağlantısı sağlaman için **JSON Web Token** üreteceğiz.

-----

Öncelikle token üretmek için **Endpoint** adresimizi bulmamız gerekiyor.

![ASPNETCOREAADJWT32](/assets/images/posts/2017052901/sc32.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Sol menüden **Azure Active Directory** seçiyoruz.

2. **App registrations** seçeneğine tıklıyoruz.

3. Açılan kısımda **Endpoints**  butonuna tıklıyoruz.


-----

![ASPNETCOREAADJWT33](/assets/images/posts/2017052901/sc33.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Gelen ekranda **OAUTH 2.0 TOKEN ENDPOINT** alanı bizim endpoint adresimiz.
Benim senaryomda endpoint adresi aşağıdaki şekilde sizde daha farklı olacaktır.

https://login.windows.net/64114426-b4a0-4e3a-8efb-9ea15136cd2e/oauth2/token

Kısaca bu adres token almak için POST isteklerimizi yapacağımız adres. 

-----

![ASPNETCOREAADJWT34](/assets/images/posts/2017052901/sc34.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Sol menüden **Azure Active Directory** seçiyoruz.

2. **App registrations** seçeneğine tıklıyoruz.

3. **Native** uygulamamız olan **CorpNativeAPI** a tıklıyoruz.

-----

![ASPNETCOREAADJWT35](/assets/images/posts/2017052901/sc35.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Native** uygulamamızın **Application ID** bilgisini alıp bir kenara not ediyoruz. Token isteği yaparken gerekecek.


-----

![ASPNETCOREAADJWT36](/assets/images/posts/2017052901/sc36.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Sol menüden **Azure Active Directory** seçiyoruz.

2. **App registrations** seçeneğine tıklıyoruz.

3. **Web API** uygulamamız olan **CorpAPI**  seçiyoruz.

-----

![ASPNETCOREAADJWT37](/assets/images/posts/2017052901/sc37.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Açıkan kısımdan **Manifest** butonuna tıklıyoruz.

-----

![ASPNETCOREAADJWT38](/assets/images/posts/2017052901/sc38.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Manifest dosyası içerisinde **identifierUris** i buluyoruz. Bu bizim kaynağımızın adı. Bende https://cevizbilgi.com.tr/c97dc3f7-7ae6-4b3d-87c4-ab728339e0ac sizde değişecektir.

-----

Token oluşturmak için gereken bilgiler tamam şimdi isteğimizi oluşturalım.

**Postman** uygulamasını açıyoruz. Siz kendi tercihiniz olan farklı bir uygulama tercih edebilirsiniz.

![ASPNETCOREAADJWT39](/assets/images/posts/2017052901/sc39.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. İstek tipimizi **POST** olarak seçiyoruz.

2. **Endpoints** ten aldığımız adres. 

Benim için bu adres, https://login.windows.net/64114426-b4a0-4e3a-8efb-9ea15136cd2e/oauth2/token **sizde değişecektir.**

3. **Body** sekmesine tıklıyoruz.

4. **x-www-form-urlencoded** seçeneğini işaretliyoruz.

5. **resource** adında bir key ekliyoruz. Değeri ise manifest dosyasından aldığımız url. Bende bu url https://cevizbilgi.com.tr/c97dc3f7-7ae6-4b3d-87c4-ab728339e0ac şeklindeydi.

6. **grant_type** adında bir key ekliyoruz. Değeri **password**

7. **client_id** adında bir key ekliyoruz. Değeri, **Native** AAD uygulamamızdan aldığımız **Application Id** olacak. Benim için bu değer, 74f671cc-e205-48bc-8d68-a3fec58cd0bd

8. **username** adında bir key ekliyoruz. Değeri kullanıcı adımız benim için **mehmet.kut@cevizbilgi.com.tr**

9. **password** adında bir key ekliyoruz. Değeri hesabımızın şifresi.

10. **Send** butonuna basarak isteğimizi gönderiyoruz.

11. İstek başarılıysa ekrandaki gibi çıktı olacak. **access_token** değeri kaynaklarımıza erişmek için kullanacağımız **JSON Web Token**

12. **access_token** belirli bir süre geçerli olan bir token. Yenilemek her defasında kullanıcı adı ve şifre sormaya gerek yok bunun yerine **refresh token** ı kullarak yeni bir **access token** üretebiliriz.

-----

Azure üzerinde barındırılan ASP.NET Core Web API projemize **Postman** ile erişmeye çalışalım.

![ASPNETCOREAADJWT40](/assets/images/posts/2017052901/sc40.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. İstek tipimizi **GET** olarak seçiyoruz.

2. İstek yapacağımız API adresimizi yazıyoruz.

3. **Send** butonuna tıklayarak isteğimizi gönderiyoruz.

4. Görmüş olduğunuz gibi API'a hiç bir güvenlik önlemi olmadan eriştik.

-----

Visual Studio da **CorpAPI** uygulamamıza geri dönelim. WebAPI uygulamamızı güvenli hale getiriyoruz.

-----

![ASPNETCOREAADJWT41](/assets/images/posts/2017052901/sc41.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Package manager Cosole** kısmından NuGet üzerinden **Microsoft.AspNetCore.Authentication.JwtBearer** paketine ihtiyacımız var. **Install-Package Microsoft.AspNetCore.Authentication.JwtBearer** komutu ile paketi projemize ekliyoruz.

-----

![ASPNETCOREAADJWT42](/assets/images/posts/2017052901/sc42.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **appsettings.json** konfigürasyon dosyasını açıyoruz.

2. **AzureAd** konfigürasyon kısmını ekliyoruz.

```json

"AzureAd": {
    "AadInstance": "https://login.microsoftonline.com/{0}",
    "Tenant": "64114426-b4a0-4e3a-8efb-9ea15136cd2e",
    "Audience": "https://cevizbilgi.com.tr/c97dc3f7-7ae6-4b3d-87c4-ab728339e0ac"
}

```
**AadInstance** bu değeri değiştirmenize gerek yok.
**Tenant** bu değeri Token Endpoint adresinizinin içinde bulabilirsiniz.
**Audience** bu değeri yazı içerisinde Manifest dosyasından almıştık.

-----

![ASPNETCOREAADJWT43](/assets/images/posts/2017052901/sc43.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Startup.cs** dosyasını açıyoruz.

2. **ConfigureServices** metodu içerisine aşağıdaki kodu ekliyoruz.

```csharp
services.AddAuthentication();
```

3. **Configure** metodu içerisine aşağıdaki kodu ekliyoruz.

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

1. Controllers klasörü altındaki **ValuesController.cs** dosyasını açıyoruz.

2. Aşağıdaki kodu ekliyoruz.
```csharp
using Microsoft.AspNetCore.Authorization;
```  

3. Güvenlik için class ımızın başına aşağıdaki özelliği ekliyoruz.

```csharp
[Authorize]
```  

-----

Bu aşamada uygulamayı Azure ortamına  **Publish** ediyoruz. Yani göneriyoruz.

![ASPNETCOREAADJWT45](/assets/images/posts/2017052901/sc45.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Projemize sağ tıklıyoruz.

2. **Publish** seçeneğine tıklıyoruz.

3. Açılacak olan ekrandan **Publish** butonuna tıklayarak uygulamamızı gönderiyoruz.

-----

Evet koruma aldığımız API ne alemde Postman kullanarak bakalım.

![ASPNETCOREAADJWT46](/assets/images/posts/2017052901/sc46.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. İstek tipimizi **GET** olarak seçiyoruz.

2. İstek yapacağımız API adresimizi yazıyoruz.

3. **Send** butonuna tıklayarak isteğimizi gönderiyoruz.

4. Görmüş olduğunuz gibi artık API koruma altında. Erişim için gerekli güvenlik bilgilerini sağlamadığımız için **401 Unauthorized** hatası aldık. 😄


-----

Şimdi elimizde daha önce aldığımız **access token** ile nasıl API a erişiz görelim.

![ASPNETCOREAADJWT47](/assets/images/posts/2017052901/sc47.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. İstek tipimizi **GET** olarak seçiyoruz.

2. İstek yapacağımız API adresimizi yazıyoruz.

3. **Authorization** ekliyoruz değeride, **bearer** <access token> . Burada önemli nokta access token ın bir ömrü olduğu ömrü dolduğunda ya refresh token ya da kullanıcı adı ve şifre ile tekrar accec token üretmemiz gerektiği.

5. **Send** butonuna tıklayarak isteğimizi gönderiyoruz.

6. Token'ımız doğrulandı ve kaynağımıza güvenli biçimde erişebildik.👍

-----

Hedefimize ulaştık. 😄 

Şu ana kadar gösterdiklerim:

- ASP.NET Core Web API da nasıl proje oluşturacağımızı gördük.
- Hazırlamış olduğumuz WEB API'yı Azure App Service olarak yayınladık.
- AAD'de bir Web Uygulaması / Web API'sı yapılandırdık.
- AAD'de bir Native Uygulama yapılandırdık.
- Hazırlamış olduğumuz WEB API'yı JWT paketi ile korumaya aldık.
- Korumaya altığımız WEB API'ya almış oldğumuz access token ile erişebildik.

Aslında aralarda anlattığım bir kaç nokta daha olmuştur. Bu konu kolay ama bir kaç adım atlanır veya eksik yapılırsa ne yazıkki olmuyor. Takıldığınız noktalar olursa bana eposta göndermekten çekinmeyin.

Ayrıca bir aydınlanma noktası olarak **AAD** ile sadece ASP.NET Core Web API değil NodeJS, PHP gibi farklı teknolojilerle yazılmış API lara aynı yöntemi kullanarak koruma sağlayabilirsiniz.