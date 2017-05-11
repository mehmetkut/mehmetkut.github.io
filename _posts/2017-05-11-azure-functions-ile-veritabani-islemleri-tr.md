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
Biz de Azure Function her çağrıldığında Azure SQLDB de log tablomuza bir kayıt atıp olan biteni kayıt etmek istiyoruz.

### Gerekenler 

1. Azure SQL DB
2. Azure Function App

### Azure SQL DB

Öncelikle Azure Function ile veri yazacağımız veritabanını yapılandırmamız gerekiyor. 

![AzureSQLDB](/assets/images/posts/2017051101/sc01.png)

1. Azure Portal'de Sol menüden **SQL Databases** i seçiyoruz.
2. Açılacak olaran kısımdan **Add** butonuna tıklıyoruz.


### Sonuç