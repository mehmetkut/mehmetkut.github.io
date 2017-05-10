---
layout: post
title: Azure ortamında Ubuntu sanal makina üzerine OpenVPN Access Server kurulumu
comments: true
tags: [Türkçe, Azure, Ubuntu, OpenVPN, OpenVPN Access Server, OpenVPN Kurulumu, VPN, Putty]
description: Azure ortamında Ubuntu sanal makine üzerine OpenVPN kurulumu ve kullanımı.
---

Bu yazıda, Ubuntu Azure IaaS Sanal Makineye OpenVPN Access Server'ın nasıl kurulacağını anlatacağım. 
OpenVPN, VPN bağlantıları oluşturmaya izin veren açık kaynaklı bir yazılımdır.

Kendi VPN sunucunuzu kurmanın ve kullanmanın avantajı, verilerinizin nereye gideceğini kontrol etmenizdir. 
Kendi kurulumunuz dışında bir VPN servisi satın aldığınızda, hizmeti aldığınız yere güvenmeniz gerekmektedir.

Azure üzerinde yapmanın avantajı,

* Bulut hizmeti kullanmanın avantajı.
* Donanım altyapısıyla ilgili endişelenmenizin gerekmemesi.
* Tüketdiğiniz kadar ödeme yapmanızdır.

Kurulum sonucu ücretsiz kullanım için, OpenVPN lisansının izin verdiği en fazla aynı anda 2 kullanıcıdır.
Daha fazla aynı anda bağlantı isterseniz OpenVPN lisanslarına gözatmanız gerekiyor. Bu yazıdaki amacımız kendi kullanımımız için VPN sunucusu
kurmak olduğundan aynı anda 2 yeterli olacaktır. Siz ve en sevdiğiniz arkadaşınız kullanabilir. 

