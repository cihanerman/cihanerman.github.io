---
title: "Django'da sorgu optimizastonu #1"
subtitle: "Defer ve only kullanımı. Django tips and trick #3"
author: "Cihan Erman"
tags: [python, django, defer, only, tips and tricks]
date: '11 Nisan 2020'
layout: post
---

## Başlamadan Önce
Herkese merhaba. Django'da performanas sorunları yaşıyor olabiliriz bu gibi durumlarda yapabileceğimiz bir kaç şey var. Bunlardan biri daha önceki bir yazımda açıklamıştım, genarator kullanımıydı. Yapabileceğimiz bir başka şeyde ağır işleri veri tabanına yaptırmak (ki bu yazıda bundan bahsetmeyeceğiz). Yapabileceğimiz bir şey daha var o da, veri tabanından ihtiyacımız kadarını istemek. Bu şekilde kaynakalrımızı daha az tüketip daha fazla hızlanacağız. O zaman başlayalım.
Kaynak olarak django'nun [kendi dökümanın](https://docs.djangoproject.com/en/3.0/ref/models/querysets/) kulanacağım.

## Sen Gelme Ulan Ayı
Django orm ile veri tabanına bir sorgu yazdığımızda objenin bütün özellikleri gelir. Bu özellikleri sayısı fazlaysa ve/ve ya sorgu sonucunda çok fazla obje geliyorsa bu durum bize performas konusunda olumsuz olarak yansır. Böyle bir durumda kendimizi(Yazdığımız kodu) sorgulamamız gerekir. Orada bir sınıfı ağit objelerin bütün özelliklerine ihtiyacımız var mı? "Yo" diyorsanız karşınıda only fonksiyonu(tatata tam :). Sorgumuzu only ile bitirim sadece ihtiyacımız olan özellikleri yazarsak(Sadece bir, iki özelliğe ihtiyacımız olabilir ve modelimizde onlarca özellik bulunabilir) çok daha küçük bir veri, veri tabanından hızlıca gelip django orm tarafından hızlıca size sunulabilir. Ben süre olarak bunu ölçmedim, bunula birlikte kendim bu konuda araştırma yaparken rastladım ve demelerimde gözle görülür sonuçlar aldım. Bir örnek gösterelim:

```python
Entry.objects.only("headline", "body")
```
Burada Entry sınıfının tüm objelerinin sadece headline ve body özellikleri geliyor. Entry sınıfında 20-30 özellik olduğunu düşünsenize. only, hiç ihtiyamız olmayan bilgiler için performans kaybetmemizi önlüyor. Orm'e ve dolayısıyla veri tabanına bize sadece bu özellikler lazım sadece bunları getir demiş oluyoruz.

## İyi hoş da
İyi hoş da ya bize 20 özellikten 15'i lazımsa 15 tanesini tek tek yazacak mıyız ? Aslında bir yazılımcı olarak performans sorunu yaşıyorsan yazacaksınız. Sizin işiniz bu. Belki zamanında yazılmış performanssız kodun sorumlusu siz olmaya bilirsiniz ama şu an böyle bir sorun var ve bu sorun artık sizin sorununuz. Ben bunu sürekli yaşıyorum. Bir şirkette çalışıyor yada bir girişimciyseniz ve yazılım kısmı sizdeyse, insanlar sorunu çözmenizi bekliyor. Sizin durumunuzu anlasalar ya da anlamasalarda ilgilendikleri konu sorunu çözmeniz, yaşadığınız zorluklar değil. Neyseki python ve django "kolaylaştırınız, zorlaştırmayınız" felsefesine sahip. Burada imdadımıza defer yetişiyor. Defer'ı only'in tersi olarak düşüne bilirsiniz. Yani istemediğiniz özellikleri yazıyorsunuz ve onların dışında kalan özellikler geliyor.

```python
Entry.objects.defer("headline", "body")
```
Burada Entry sınıfına ayit tüm objelerin headline ve body dışındaki özellikleri çekiyoruz.

## Son söz
Django orm'de bu iki methodu kullanarak sorgularımızı hızlandıra biliriz. Django'da sorgular performansı etkileyen kritik konuların başında gelir ve performans konusunda iyiliştirme yapmaya sorgularınızdan başlamanızı tavsiye ederim. Bu konuda bir yazı daha yazmayı planlıyorum. Umarım faydalı bir yazı olmuştur. Sorularınız, yorumlarınızı, katkılarınızı, yapıcı eleştirilerinizi benimle paylaşırsanız sevinirim. Teşekkü ederim, iyi çalımalar.

> İhtiyaçtan fazlası israftır.