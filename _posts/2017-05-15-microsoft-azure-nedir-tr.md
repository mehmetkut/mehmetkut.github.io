---
layout: post
title: Microsoft Azure nedir?
comments: true
tags: [Microsoft, Azure, Microsoft Azure]
description: Microsoft Azure nedir?
---

### Microsoft Azure

Ne olduğunu açıklamak aslında oldukça kısa gibi görünebilir. 
Kısa olan açıklaması: "**Microsoft Azure**, **Microsoft**'un bulut platformudur."

-----

Bir adım daha sadeleştirelim, **Microsoft Azure** aslında **Azure** olarak tek kelime ile ifade edilebilir. 
Neydi 1 karakter 8 bit(ASCII)'e eşitti yani 8bit * 5 karakter = 40bitlik bir ad altına Microsoft'un neleri sığdırığına gözatalım. 😄

-----

Kuş bakışı olarak **Azure** aşağıdakine benzer hizmetler bütünüdür.

![SC01](/assets/images/posts/2017051501/sc01.png){: class="jslghtbx-thmb jslghtbx-animate-transition"  data-jslghtbx="" }

Gördüğünüz gibi oldukça fazla hizmet var. Gözünüz korkmasın, size hepsi değil sadece **ihtiyacınız olanlar** gerekecek.

-----

### Bulut Bilişim

**Azure** kelimesini kavramak için aslında biraz **bulut bilişim** konusuna değinmek gerekiyor.

Bir senaryo eşliğinde anlatmak istiyorum.

-----

### Senaryo

Haftalık olarak finans dergisi yayınlayan bir şirket. Sunucularının giderleri düşürmek istiyor. Şirkete ait 3 adet sunucu var. 

-----

### Mevcut Durum

- İlk sunucu şirket web sitesini.
- İkinci sunucu şirket veritabanı.
- Son sunucu yedekleri barındırıyor.

Bu sunucuların barındırıldığı yer şirketin bodrum katı. 

Mecvut durumda sunucuları barındırmak için gerekenlerden bazıları,

- Sunucuları koymak için şirkette büyükçe bir yer.
- Sunucu donanımlarını arada bir yenilemek.
- Sunucular oldukça fazla elektrik tüketiyor.
- Arada bir olan elektrik kesintileri sebebiyle kesintisiz güç kaynağı.
- Sunucular çok ısındığından salon tipi bir klima.
- Sunucuya yazılımlar kurmalı, yapılandırılmalı ve güvenlik yamalarını periyodik olarak uygulanmalı.
- Web sitesini sunmak için hızlı bir internet altyapısı.
- Yazılım lisans yönetimi.
- Temizlikçi abla bodrumun tozunu alırken, yanlışlıkla sunucuyu kapatmasın diye bir kağıdın üzerine **dokunmayın** yazılmalı.😄


Maddeler çoğaltılabilir. Burada anlatmak istediğim bu şirket **finans dergisi** hazırlayıp, yayımlayan bir şirket uzmanlık alanı **bilgi teknolojileri** olmamasına rağmen bu işlerle vakit ve para kaybetmek zorunda.

-----

### Mevcut Durum İyileştirmesi (IAAS)

Sıraladığım maddeleri yapmanın daha kolay bir yolu olmalı diye düşünürken, sadece bu alanlarda uzmanlaşmış bir **teknoloji** şirketi çıka gelir ve teklifte bulunur. 
Sunucularını al benim **veri merkezime** koy sen uzaktan erişim ile sunucularına bağlan. Soğutma, elektrik, yer sorunlarını çözeyim, ücrette kullandığın kadar öde olsun der.

Böylece bodrum katından, **veri merkezi** dönüşümü başlar. Kazanım olarak artık sunucularımızın enerji tüketimi, donanım maliyeti, soğutulması ve kapladığı alan ilgili sorunlarımız uçup gitmiş olur. Bu işlerle ilgilenen uzman personele devretmiş olduk.

-----

### Daha Fazla İyileştirme (PAAS)

Donanım ve ağ ile sorunlarımız gitmiş olsada, yazılımları hala biz kurup güvenlik güncelleştirmeleri yapmaktayız. Bu aşamada **teknoloji** şirketi farklı bir çözümle gelir. İşletim sistemi, web sunucusu ve veritabanı sunucusu için uzaktan erişim yapmana gerek yok. Bir web sitesi hazırladım bu site üzerinden ihtiyacın olan bu hizmetleri kullanabilirsin der. Bizde sağlanan hizmetleri kullanarak web sitemizi yayımlar ve veritabanımızı tutarız. 

Buradaki şirket kazanımı, işletim sistemi, antivirüs, veritabanı ve diğer yazılımların kurulum, bakım gibi uzmanlık alanı dışındaki işleri uzmanların yönetimine devretti. **teknoloji** şirketinin sunduğu hizmetlerle hız kazandı maliyetleri düşürüp, risklerini azalttı.

*makale henüz bitmedi*