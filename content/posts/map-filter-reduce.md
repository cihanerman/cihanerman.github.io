---
title: "Athos, Porthos ve Aramis"
date: 2020-02-16T11:30:03+00:00
# weight: 1
# aliases: ["/first"]
tags: ["python", "django", "map", "filter", "reduce", "functional programming", "tips and tricks"]
author: "Cihan Erman"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "Python'da map, filter, reduce. Django tips and trick #2"
canonicalURL: "https://cihanerman.github.io/"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
ShowWordCount: true
ShowRssButtonInSectionTermList: true
ShowCodeCopyButtons: true
UseHugoToc: true
cover:
    image: "<image path/url>" # image path/url
    alt: "<alt text>" # alt text
    caption: "<text>" # display caption under cover
    relative: false # when using page bundles set this to true
    hidden: true # only hide on current single page
---
## Başlamadan Önce
Herkese merhaba. Bu yazımda python'da fonksiyonel programalamnın gücünden faydalanmamızı sağlayan üç fonksiyondan bahsedeceğim.
Bu yazı fonksiyonel programlama paradigmasını anlatan bir yazı değil. Bu paradigmanın avantajları; yazması, okuması, konturolü kolay ve tekrar kullanılabilirliği yüksek kod yazılmasını sağlıyor. İnternetten programlama paradigmalarını araştırabilirsiniz. Python yüksek seviyeli bir dil olduğu için birden fazla programlama paradigmanı destekliyor. Python gerçekten çok iyi bir araç. [Burada](https://book.pythontips.com/en/latest/map_filter.html) birazdan anlatacağım üç fonksiyonla ilgili daha fazla bilgi edinebilirsiniz.

Programlamada döngüler kullanırız( hadi ya! ). Ama döngü gibi, if gibi yapılar programı yavaşlatır. Mecbursak elbette bu yapıları kullanacağız. Ama ya o kadar mecbur değilsek. Hadi Harzırsak başlayalım.

### Hein Map !
`map` fonksiyonu pythondaki yüksek seviyeli fonksiyonlardan biri, paremetre olarak başka fonksiyonları alabiliyor. Python'da yerleşik geliyor ve iyi optimize edilmiş bir fonksiyon olduğu için çok hızlı çalışıyor. Bir liste yada itere edilebilen bir veri yapısına sahip olduğumuzu düşünelim.( Bir dünya hayal edin herkes mutlu, herkes şen ) Elimizdeki bu yapının her bir elamanına bir işlem uygulamak istiyoruz. Bu durumda ne yaparız? Cevap; Döngü dönmeyiz. Biliyorum programlamaya başlarken insanlara böyle öğretiyorlar, bana da böyle öğretmişlerdi. Böyle öğretmek muhtemelen daha mantıklıdır. Ama aydınlığa ulaşmak istiyorsanız, çok çalışmalı, mücadele etmeli ve daha fazla öğrenmelisiniz. En azından ben öyle yapıyorum. Evet konumuza dönecek olurak, mümkün olduğunca döngü kullanmıyoruz. Python'da bunu bizze sağlayan fonksiyonlardan birde `map`. Kulanımı basit;
```python
items = [1, 2, 3, 4, 5]
squared = list(map(lambda x: x**2, items))
```
`map`fonksiyonu ilk parametre olarak bir fonksiyon alıyor ki bu fonksiyon herbir elemana yapmak istediğiniz işlemi yapıyor, ikinci parametre olarakda itere olabilen veri yapsını ve fonksiyonu veri yapısına uyguluyor. Tabi bunu işlemi çok hızlı, temiz bir yapıyla yapıyor. Yazması kolay, anlaması kolay ve performanslı. Karmaşık yapılarıda destekliyor.

```python
def multiply(x):
    return (x*x)
def add(x):
    return (x+x)

funcs = [multiply, add]
for i in range(5):
    value = list(map(lambda x: x(i), funcs))
    print(value)

# Output:
# [0, 0]
# [1, 2]
# [4, 4]
# [9, 6]
# [16, 8]
```

### Filter: Sen Gelme Ulan Ayı !
`filter` aynı map gibi çalışıyor. Tek fark foksiyonumuz veri yapımızın her elemanına bir `True False` sonucu döndürüyor. `True` dönenler filtreden geçmiş oluyor. Örnek vermek gerekirse;

```python
number_list = range(-5, 5)
less_than_zero = list(filter(lambda x: x < 0, number_list))
print(less_than_zero)

# Output: [-5, -4, -3, -2, -1]
```

`map`fonksiyonu gibi `filter` fonksiyonu da daha karmaşık işlemleri destekliyor. Ayrıca `lambda` hakkında bilginiz yoksa mutlaka öğrenin.

### Reduce: Benden Tek Bir Şey Bekle !
`reduce` fonksiyonunu kullanmak için python ile birlikte gelen functools kütüphanesinden import etmemiz gerekiyor `from functools import reduce`. `reduce` bir çeşit indirgeme işlemi yapıyor. Bir liste veriyoruz ve bize fonksiyonumuza göre tek bir sonuç dönüyor. Örnek verelim;

```python
from functools import reduce
product = reduce((lambda x, y: x * y), [1, 2, 3, 4])

# Output: 24
```

ya da

```python
from functools import reduce
product = reduce(lambda x, y:  (x[0] + y[0], x[1] + y[1])), [(1, 2), (3, 4), (5,6)])

# Output: (9, 12)
```

Sanırım örneklerle `reduce`'un hangi amaçla kullanılacağını anlatabilmişimdir.

### Bunlardan Başka Var mı?

numpy, functools, itertools, operator, collections gibi kütüphaneleri öğrenip daha aktif kullanmaya çalışabilriz. Bu kütüphaneler genellikle bir topluluk tarafından geliştiriler ve epey optimize kütüphanelerdir. Ayrıca generators kullanımı ve fonksiyonel programlama hakkında daha fazla şey öğrenmenizide öneririm.

## Sonuç
Bu üç fonksiyon özellikle büyük veri kümelerinde çalışıyorsanız çok işinize yarayacaktır. Hem hızlı işlem, hem daha okunaklı ve temiz bir kod yazmış olursunuz. Ben bir for yazmam gerektiğinde kendime ilk sorduğum soru; Bu işi bu üç fonksiyonla ya da başka benzeri bir fonksiyonla yazabilirmiyim diye sorguluyorum. Böyle fonksiyonları kendinizde yazabilirsiniz. Başlangıçta bu fonksiyonları kullanmak çok fayda sağlayacaktır. Bu yazınında sonuda geldik. Sorularınızı, eklemek istediklerinizi ve yapıcı eleştirilerinizi yorumlarda benimle paylaşırsanız sevinirim. Hepinize kolay gelsin, iyi çalımalar.