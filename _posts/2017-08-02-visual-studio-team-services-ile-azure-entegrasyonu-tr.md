---
layout: post
title: Visual Studio Team Services (VSTS) ile Azure entegrasyonu
comments: true
tags: [Microsoft, Azure, Visual Studio Team Services, VSTS, SPN, Service Principal Name, Entegrasyon]
description: Visual Studio Team Services (VSTS) ile Azure entegrasyonu
---

Bu yazımda çözümlerinizi Visual Studio Team Services (VSTS) ortamından, Azure ortamına dağıtabilmeniz için gerekli entegrasyonu göstereceğim.

VSTS, sistem yöneticileri ve geliştiricilerin Azure üzerinden doğrudan kaynak oluşturmasına ve dağıtılmasına olanak tanır.
Azure kaynağı oluşturmak için VSTS bir Azure Resource Manager(ARM) endpoint tanımına ihtiyaç duyar. 

-----

VSTS ARM endpoint entegrasyonu oluşturmak için iki senaryomuz var. 

-----

### Azure ve VSTS oturum açmak için **aynı hesabı** kullanıyorsanız.

VSTS ve Azure'da oturum açmak için aynı hesabı kullanıyorsanız, entegrasyon oldukça kolay.

![VSTS01](/assets/images/posts/2017080201/sc01.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. VSTS sol üst menüsünü tıklıyoruz.

2. ARM Endpoint eklemek istediğimiz projeyi seçiyoruz.

-----

![VSTS02](/assets/images/posts/2017080201/sc02.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Çark ikonunun üzerine geliyoruz.

2. Açılan menüden **Services** i seçiyoruz.

-----

![VSTS03](/assets/images/posts/2017080201/sc03.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **New Service Endpoint** menüsünü tıklayıp açıyoruz.

2. Azure Resource Manager seçeneğine tıklıyoruz.

-----


![VSTS04](/assets/images/posts/2017080201/sc04.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Connection name** alanına istediğiniz adı yazabilirsiniz. Bu bağlantımızın adı olacak.

2. **Subscription** bu alanda VSTS ile giriş yaptığınız kullanıcıya bağlı Azure abonelikleriniz listelenecek uygun olanı seçin.

>Eğer burada Azure aboneliği görmüyorsanız hesabınıza bağlı bir Azure aboneliğiniz yok. İkinci senaryoyu takip etmelisiniz.

3. **OK** butonuna tıklıyoruz.


Bu noktadan sonra hesabımız eğer seçtiğimi Azure aboneliğinde **Global Admin** yetkisine sahipsek eklenecektir. 

**Global Admin** yetkisine sahip değilsek aşağıdakine benzer bir hata mesajı çıkar.

![VSTS05](/assets/images/posts/2017080201/sc05.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Bu hatayı alırsanız iki seçeneğiniz var. 

1. Hesabınızın rolü Azure üzerinde **Global Admin** değil hesabınızı **Global Admin** e çevirtebiliyorsanız bunu yapın sonra bu senaryodaki adımları tekrarlayın.

2. İkinci senaryoya geçin.

-----

### Azure ve VSTS oturum açmak için **farklı hesap** kullanıyorsanız.

Bu senaryoda Azure Active Directory Service Principal Name (SPN) ile VSTS ve Azure arasındaki bağlantıyı kuracağız. 

-----

### Gerekenler

1. Azure Power Shell

> İndir: [Azure Power Shell](https://azure.microsoft.com/en-us/downloads/){:target="_blank"}

Yukarıdaki linkten Azure PowerShell indirebilirsiniz.

2. SPN oluşturmak için gerekli PowerShell scripti 

> İndir: [Azure Power Shell SPN Script](/assets/images/posts/2017080201/SPNCreation.ps1){:target="_blank"}

-----

Öncelikle ekleyeceğimiz **Subscription Name** i öğrenmemiz gerekiyor.

![VSTS06](/assets/images/posts/2017080201/sc06.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Öncelikle Azure Portal'e giriş yapın.

1. Sol menüden **Subscriptions** a tıklayın. **Subscriptions** seçeneği yoksa sol alttan **More services** menüsünden aratabilirsiniz.

2. Overview seçeneğine tıklayın.

3. **Subscrition name** kısmından aboneliğinizin adını öğrenin ve biryere not edin.

-----

Yeni bir Azure PowerShell açın.

![VSTS07](/assets/images/posts/2017080201/sc07.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Gerekenler kısmında indirmiş olduğunuz **SPNCreation.ps1** PowerShell scriptinin olduğu klasöre giriniz.  

> **.\SPNCreation.ps1**

komutunu yazıp enter tuşuna basın.

-----

![VSTS08](/assets/images/posts/2017080201/sc08.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. PS Script öncelikle **subscriptionName** soracak bunu önceki adımlarda not aldığımız **Subscription Name** yazıyoruz.

2. **password** kısmına entegrasyonda kullanılacak istediğiniz şifreyi yazabilirsiniz.

**Enter** tuşuna basıp devam ediyoruz.

-----

![VSTS09](/assets/images/posts/2017080201/sc09.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

PowerShell script Azure giriş bilgilerinizi teyid etmek için oturum açma penceresi açacak.

1. Azure hesabı kullanıcı adınızı yazın.

2. Azure hesabı şifrenizi yazın.

3. Oturum Aç butonuna tıklayın.

-----

PowerShell script çalıştıktan sonra aşağıdakine benzer bir ekran çıktısı göreceksiniz. Bu ekranı kapatmayın çünkü sonraki adımlarda sorulacak sorulara bu ekrandaki bilgileri gireceğiz.

![VSTS10](/assets/images/posts/2017080201/sc10.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

-----

![VSTS11](/assets/images/posts/2017080201/sc11.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. VSTS sol üst menüsünü tıklıyoruz.

2. ARM Endpoint eklemek istediğimiz projeyi seçiyoruz.

-----

![VSTS12](/assets/images/posts/2017080201/sc12.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Çark ikonunun üzerine geliyoruz.

2. Açılan menüden **Services** i seçiyoruz.

-----

![VSTS13](/assets/images/posts/2017080201/sc13.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **New Service Endpoint** menüsünü tıklayıp açıyoruz.

2. Azure Resource Manager seçeneğine tıklıyoruz.

-----

![VSTS14](/assets/images/posts/2017080201/sc14.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **use the full version of the endpoint dialog.** linkine tıklıyoruz.

-----

![VSTS15](/assets/images/posts/2017080201/sc15.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. **Connection name** alanına bağlantımız için istediğimiz adı yazabiliriz. Ben **AzureSPNIntegration** yazdım.

2. **Enviroment** alanında **Azure Cloud** seçiyoruz.

3. **Subscription ID** bu alanı PowerShell scripti çıktısından bakıp yazıyoruz.

4. **Subscription Name** bu alanı PowerShell scripti çıktısından bakıp yazıyoruz.

5. **Service Principle Client ID** bu alanı PowerShell scripti çıktısından bakıp yazıyoruz.

6. **Service Principle Key** PowerShell scriptini çalıştırdığımızda bizden istediği şifreyi buraya yazıyoruz.

7. **Tenant ID** bu alanı PowerShell scripti çıktısından bakıp yazıyoruz.

8. **Verify connection** linkine tıklıyoruz.

-----

![VSTS16](/assets/images/posts/2017080201/sc16.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Doğrulama sonrası işlem başarılıysa yeşil tik ve **Verified** yazısını görmemiz gerekiyor.

2. **OK** butonuna tıklayıp ARM Service Endpoint ekleme işlemini tamamlıyoruz.

-----

![VSTS17](/assets/images/posts/2017080201/sc17.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

1. Endpoint adımız servis listesine gelmiş.

2. Gördüğünüz gibi **Service Principle** ile entegrasyonu sağladık.


-----

Artık VSTS içinde özellikle Continuous Deployment yaparken endpoint kısmında **AzureSPNIntegration** çıkacak ve Azure hesabımıza kolaylıkla dağıtım yapabileceğiz. 😄