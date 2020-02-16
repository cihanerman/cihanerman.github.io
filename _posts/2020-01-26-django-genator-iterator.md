---
title: "Büyük oyunu gör. Django'da yaratıcılar ve yineleyiciler."
subtitle: "Django tips and tricks, #1"
author: "Cihan Erman"
tags: [python, django, generators, iterators, tips and tricks]
date: '26 Ocak 2020'
layout: post
---

# Başlamadan Önce

Herkese merhaba. Bu yıl ki ilk yazıma başlamadan önce birkaç şey söylemek istiyorum.
Bu sene daha fazla yazı yazmayı planlıyorum. Bu seneki yazılarıma djangoda pek fazla Türkçe
kaynak bulamadığım bir kaç tips and tricks tadında yazılarla başlayacağım. Fikirlerinizi, bildiklerinizi,
yapıcı eleştirilerinizi yorumlarda benimle her zaman paylaşabilirsiniz, bundan mutluluk duyarım.
Hadi başlayalım.

# Ve python 'ışık olsun' dedi.
Bahsedeceğim konu yeni bir konu değil, ama fark yaratan bir konu ve henüz bilmeyenler ya da
yeni başlayanlara yardımcı olcağını düşündüm bir konu. Bu yazı python daki [generator ve iterator](https://medium.com/python-yaz-lar/python-generator-ve-i-teratorler-a53e59f7c5b1)
ları anlatan bir yazı değil. Linke tıklarsanız ayrıntılı ve güzel bir yazı var, oradan öğrenebilirsiniz. Bu yazımda 
djangoda generator ve iteratorların kullanımından bahsetmeye çelışacağım.

## Bu karanlık ve soğuk evrende Generator'ler var
Generators'lar yeni bir konu değil ama cennetten kovulmuş gibiler. Hiç duymayanlar var,
duymuş olup hiç kullanmayalar var ve azda olsa bilen ve kullananlar var. Eğer özel olarak bu konuyu aramıyor
sanız ve ya (Buraya dikkat) performans gelistirmek için arama yapmıyorsanız, internette bile pek karşınız çıkmaz. En azından benim çıkmadı.

Python'da genarators nasıl oluşturulu ? İki türlü;
1. tür:
```python
def xgenerator(list1):
    for l in list1:
        yield l
```

2.tür:
```python
list2 = (x for x in list1)
```
Evet, python'da bu iki şekilde generator oluşturabiliyoruz. Bence çok kolay.
Python ve kolaylaştırnız, zorlaştırmayınız felsefesi.

## Eee hacı, sadede gel
Generatorlar diğer itere olabilen yapılardan daha hızlı çalışır (Özellikle listelerden) ve daha az bellek kullanılır (Özellikle listelere kıyasla).
Performans ve bellek kullanımı azaltmak istiyorsanız, django da belki her zaman olmaz ama (Kullanmaktan çekinmeyin) olabildiğince kullanırsanız daha hızlı bir uygulamaya sahip olursunuz.
Tabi daha az bellek kullanacağınız için kaynaklarınız daha verimli kullanmış olursunuz.

## Tips and tricks bir şeyler diyordun ?
Django da bir sorgunu sonuna `.iterator()` derseniz, örneğin `Books.objects.all().iterator()` gibi. Bir queryset değil bir genarator elde edersiniz.

```python
for book in Books.objects.all().iterator():
    # birşeyler

```
Yukarıdaki kullanımda eğer bir sorguyu sadece bir döngüde kullanacaksanız çok iyidir. Hem hızlıdır hem de <mark>bellekte hiç yer kaplamaz.</mark>

# Son söz
Bence generatorlar generic fonksiyonlar kadar güzel bir yapı. Tanıdığım yazılımcılara sorduğumda ve ya internette özellikle aranmadığında pek deyinilmeye bir konu olması
sebebiyle genarators'lar cennetten kovulmuş gibiler. Pythonda kullanımı çok kolay, django açısından bakarsak çok faydalı. Eğer generator nedir bilmiyorsanız hemen öğrenin, en fazla
5-10 dakkanızı alır. Anladıktan sonrada kullanmaktan çekinmeyin.

Umarım yardım olabilmişimdir. Ben generators'ların faydasını görmeye başladığım için bunu sizlere öneriyorum. Django tutorial'larda pek göremeyeceğiniz bir konu.
Kendinize iyi bakın ve iyi çalışmalar.