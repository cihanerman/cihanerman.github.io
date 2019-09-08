---
layout: post
title: Python’da Veri Manipülasyonuna R Gücü Katmak 
tags: [data manipulation, python, R]
---

### Python’da Veri Manipülasyonuna R Gücü Katmak 

Herkese merhaba. Yoğunluktan dolayı uzun zamandır yazı yazamıyordum ve
bayram tatilini değerlendirirken bir yazı kaleme almak istedim.

Yaklaşık bir buçuk yıl önce ömründe kod görmemiş biriydim. Bir yazılımcı
olmayı başardım ve daha iyi bir yazılımcı olmak için çalışıyorum.
Yazılım dünyasının şuanki en üst noktalarından biri yapay zeka. Bende
kendime bir hedef koydum ve yapay zeka geliştirmek için işten artan
zamanda bu konuda kendimi geliştirmeye çalışıyorum.

Veri bilimi, büyük veri, makine öğrenmesi, yapay zeka konularında python
en çok kullanılan dil olsada, bu konuda R dili daha kuvvetli. Python’nun
bu konularda fazla kullanılmasının en önemli sebebi bence, başka
sistemlerle entegrasyonunun kolay olması. Tabi bir çok başka sebebide
var.

Python’u zaten biliyor ve bu konularda ufakta olsa denemeler yapıyordum.
İçimden bir ses R dilinde öğrenmem gerektiğini söylüyordu ve bende öyle
yaptım.

R istatistiksel bir programlama dili olduğu için, veri ön işleme ve veri
manipülasyonu python’a göre hem daha kolay hem de daha keyifli. Bunu
sağlayan efsane bir R kütüphanesi var;
[dplyr](https://dplyr.tidyverse.org/) .

dplyr kütüphanesi verilari manipüle etmeyi kolaylaştırır. Ben öğrenirken
biraz kullandım, kişisel deneyimin çok iyi ve kullanışlı bir kütüphane
olduğu yönünde. dplyr, R dünyasında boşuna efsane olmamış diyebilirim.

dplyr kütüphanesi az kod yazarak çok iş yapmamızı sağlayan kütüphane.
Örnekler vermek gerekirse;

```R
starwars %>% filter(species == “Droid”)
```

`%\>%` oparatörü soldaki işlemin çıktısını sağtaraftaki işleme girdi
olarak verir. starwars veri setinin ismi. Burada species’i droid
olanları filtreliyor.

| ![](https://cdn-images-1.medium.com/max/800/1*FsCAOuuPbyQlEwaqhB4IrA.png) | 
|:--:| 
| *https://www.rdocumentation.org/packages/dplyr/versions/0.7.8* |

```R
starwars %>% 
    mutate(name, bmi = mass / ((height / 100) ^ 2)) %>% 
    select(name:mass, bmi)
````

Burada bmi adında yeni bir değişkeni veri setinden hesaplayıp, name’den
mass’a kadar olan değişkenleri ve yeni oluşturduğu bmi değişkeni
seçiyor.

| ![](https://cdn-images-1.medium.com/max/800/1*RIRHH4aSHrlEuWKyqWNfIQ.png) | 
|:--:| 
| *https://www.rdocumentation.org/packages/dplyr/versions/0.7.8* |

Daha bir çok uzun işlemi, gruplama, hesaplama vs. çok kolay ve kısa bir
şekilde yazmamızı sağlıyor.

Gelelim yazının başlığına. Pythonun çok tercih edeilmesinden kaynaklı R
dünyasından python dünyasına geçenler R daki bu kolaylığı python
dünyasına getirmeye çalışıyorlar.

### dfply

[dfply](https://github.com/kieferk/dfply/blob/master/README.md)
kütüphanesi bunlar biri ve dplyr ile aynı mantıkta çalışıyor.  
pip install dfply diyerek yükleye biliyoruz. Burada bir not geçmek
gerekirse bu kütüphane pandas DataFrame’leri üzerinde çalışıyor.

dplyr’de ki `%>%` operatörünün yerini `>>` ve `>>=` operataörleri alıyor.
“=” işlemi yaptıktan sonra üzerine kaydetme işlemi yapıyor. Örnek vermek
gerekirse;

```python
from dfply import *

diamonds >> select(X.carat, X.cut) >> head(3)
```

diamonds veri seti, veri setinden carat ve cut değişkenlerini seçip ilk
üçünü gözlemi geri döndüren basit bir kod.

```python
diamonds >> mutate(x_plus_y=X.x + X.y, y_div_z=(X.y / X.z)) >> select(columns_from('x'))
````

Bu kon veri setine iki yeni hesaplanmış değişken ekledikten sonra x
değişkeninde başlayarak en son değişkene kadar olan veri setinin ilgili
kısmını geri döndüren bir kod.

[https://dplyr.tidyverse.org/reference/index.html](https://dplyr.tidyverse.org/reference/index.html)
ve
[https://github.com/kieferk/dfply/blob/master/README.md](https://github.com/kieferk/dfply/blob/master/README.md)  
siteleri ziyaret ederseniz bu kütüphanele hakkında daha fazla bilgi
edinebilir ve daha fazla örneği inceleyebilirsiniz. Ben de referans
olarak bu siteleri kullandım.

Bu konulara ilgiliyseniz veya zaten çalışıyorsanız bu kütüphaneyi
inceleye bilirsiniz. Veri manipülasyonu kısmında size hız ve verim
katacaktır.

Bu yazımda veri bilimi, makine öğrenmesi kounalarında veri manipülasyonu
konusunda işleri hızlandıran bir kütüphaneyi tanıtmaya çalıştım Umarım
faydalı olur. Sizler de veri bilimi, makine öğrenmesi ve yapay zeka
konularında işleri kolaylaştıracak kütüphaneler biliyorsanız, yorumlarda
paylaşa bilirsiniz. Şimdiden teşekkürler.

Herkese iyi günler, iyi çalışmlar.  
Bir sonraki yazımda görüşmek üzere.