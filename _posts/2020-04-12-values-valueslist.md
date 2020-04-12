---
title: "Django'da sorgu optimizastonu #2"
subtitle: "Values ve values_list kullanımı. Django tips and trick #4"
author: "Cihan Erman"
tags: [python, django, values, values_list, tips and tricks]
date: '12 Nisan 2020'
layout: post
---

## Başlamadan Önce
Herkese merhaba. Django'da sorgularımızı optimize etme serisini ikinci yazısındayız. Serinin bir önceki yazısı ile benzer bir yazı olacak. Mantık olarak yine ihtiyacımız kadarını alacağız, farklı olarak bu sefer dönen sonuçlar obje listesi olarak değil dictionary listesi olacak. Hazırsak başlayalım.
Kaynak olarak django'nun [kendi dökümanın](https://docs.djangoproject.com/en/3.0/ref/models/querysets/#django.db.models.query.QuerySet.values) kulanacağım.

## Sen sen ve sen
Django'da sorgularımızı values metodu ile bitiri ve values metoduna istediğimiz alanları verirsek ilgili objelerin sadece istediğimiz özelliklerinin olduğu bir dictionary listesi dönecek. Onlarca özellikli bir sınıfın objelerini çekiyorsak bu bize gözle görülür bir hız kazandıracaktır.

```python
Blog.objects.values('id', 'name')
#=> <QuerySet [{'lower_name': 'beatles blog'}]>
```
Burada `Blob` sınıfına ayit tüm objelerin sadece id ve name özelliklerinin bulunduğu dictionarylarin bir listesi dönecektir. Bize lazım olmayacak bilgilerin taşıma masrafından kurtuluyoruz.

## Hımm
Kulağıma hımm sesleri geliyor. Öyleyse values_list'e geçelim. Kullanımı values ile aynı sadece verdiği çıktı şekili farklı. Her bir obje için istediğimiz özelliklerinin olduğu bir tuple listesi dönüyor.

```python
Blob.objects.values_list('id', 'name').order_by('id')
#=> <QuerySet[(1,'yazı1'), (2, yazı2'), (3, yazı3'), ...]>
```
Burada `Blob` sınıfına ayit bütn objelerin id ve name özelliklerinin tuple olarak bir listesini alıyoruz.

```python
Blob.objects.values_list('id', flat=True).order_by('id')
#=> <QuerySet [1, 2, 3, ...]>
```
Burada ise bütün `Blob` objelerinin id'lerinin bir listesi alıyoruz.

## Son söz
Bu iki methot ile sadece ihtiyacımız olan özellikleri ister bir dictionary listesi, istersekde bu özelliklerin bulunduğu bir tuple listesi ya da bir özelliğin değerlerinde oluşan bir liste alabiliyoruz. Bu bize hem hız kazandırıyor hem de veriyi bu şekilerden birine dönüştürmemiz gerekirse ya da işimizi halledebiliyorsak, ağır işi sonderece optimize, hızlı çalışan veri tabanına yaptırmış oluruz. Django'da sorgu optimizasyonuyla ilgili bir yazı daha yazmayı planlmaktayım. Umarım faydalı bir yazı olmuştur. Sorularınız, yorumlarınızı, katkılarınızı, yapıcı eleştirilerinizi benimle paylaşırsanız sevinirim. Teşekkü ederim, iyi çalımalar.

## Bonus
Django'da queryseti'i values ile çekip pandas'ın `DataFrame` sınıfının `from_records` metodu ile dataframe yapısına dönüştürebilirsiniz. Üzerinde kolayca veri manupülasyonu yapabilir ve `to_excel` metoduyla kolayca excel çıktısı alabilirsiniz.


> "İstediğini değil, gerekeni al; gerekmeyen şey, çok ucuz da olsa senin için pahalıdır." Cato