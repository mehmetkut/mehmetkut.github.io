---
layout: post
title: Office 365 App Compatibility Kit ile Office 365 App Doğrulama
comments: true
tags: [Türkçe, Office Mağazası, Office 365, Office Store,  Office Apps, Oack, Office App Compatibility Kit]
description: Office 365 eklentilerini Office mağazasına göndermeden önce doğlulayın.
---

> İndir: [Office App Compatibility Kit](https://www.microsoft.com/en-us/download/details.aspx?id=46831){:target="_blank"}


Office Store'a uygulama göndermeden önce Office App Compatibility Kit ile  göndereceğimiz uygulamayı doğrulamamız gerekiyor.
Aslında doğrulama zorlunlu değil ancak daha hızlı onay almamızı sağlayacaktır. **Bu sebepten önemli.**


### Doğrulama 

1. Herhangi bir kurulumu yok zip dosyasını açtıktan sonra, Microsoft.Oack.Console.exe konsol uygulamasını çalıştırıyoruz.

2. Açılan konsol uygulamasına Run  APP.xml yazıyp enter'a basıp doğrulamayı başlatıyoruz. Buradaki ​APP.xml Visual Studio içinden paketlediğimiz app'in xml'i.

![Oack](/assets/images/posts/2017050401/oack.png)

### Sonuçlar

Doğrulama bittikten sonra Oack kendi klasörü içerisine raporu html formatında oluşacaktır. ​Bu raporu kullanarak uygulamanızı Office Store'a yollamadan doğrulayabilirsiniz.