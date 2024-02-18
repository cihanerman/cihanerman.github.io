---
title: "Django Listview kullanımı"
date: 2018-04-29T11:30:03+00:00
# weight: 1
# aliases: ["/first"]
tags: ["django", "python"]
author: "Cihan Erman"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "ListView Kullanımı"
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

Bu yazımda django’da ki generic view konusundan listview’ı anlatmaya
çalışacağım. Kaynak olarak
[https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-display/](https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-display/)
kullandım. Bu yazı giriş seviyesinde django bildiğinizi var sayar.
Başlayalım.

Önce uygulamamız içindeki model.py dosyasında modelemizi tanımlayalım.

```python
from django.db import models
```

```python
class Post(models.Model):
   yazar = models.ForeignKey() 
   baslik = models.CharField()   
   icerik = models.TextField()
```

İlk satırda modellerimizin kalıtım alacağı models sınıfını models.py
dosyamıza import ediyoruz ve Post modelimizi bu model sınıfını miras
alacak şekilde oluşturuyoruz. Post modelimizin yazar, baslik ve icerik
isimlerinde 3 property’i var.

Şimdi uygulamamızda ki views.py dosyasına Post modelimizi listeleyecek
listview’ı yazalım.

```python
from .models import Postfrom django.views import generic
```

```python
class PostListView(generic.ListView):   
    model = Post   
    template_name = "blog/post_list.html"   
    queryset = Post.objects.all()
```

views.py dosyamıza modelimizi ve listview’ı miras alacağımız için
generic viewları import ediyoruz. Daha sonra PostListView isminde ki
class view’ımızı listview’dan miras alacak şekilde yazıyorız. Burada
model property’sine modelimiz olan Post’u veriyoruz. template\_name
property’sine Post’larımızı göstereceğimiz html dosyasının yolunu
yazıyoruz. queryset property’sine de göstermek istediğimiz Postl’arın
veri tabanı sorgusunu yazıyoruz. Burada bütün postları çekiyoruz.

Şimdi de post\_list.html’i yazalım Post’larımızı bu sayfada
görüntüleyeceğiz.

```html
{\% for post in object_list \%}   
    <hr>   
    <div>    
        <h1>{\{ post.title }}</h1>    
        <p>{\{ post.text|linebreaksbr }}</p>   
    </div>

{\% endfor \%}
#=> not: backslash işaretkeri kullandığım web site generation yüzünden koydum
```

Buradaki object\_list bizim PostListView’den gelen Post’larımızın
listesi (Bu ismi istersek değiştire biliyoruz). post da listedeki her
bir Post’umuzu temsil ediyor. Bütün Post’larımız derleme esnasında
burada yazdırılıyor.

Son olarak uygulamamızın urls.py dosyasına sayfamızın yolunu yazıyoruz.

```python
from django.conf.urls import urlfrom . import views
```

```python
urlpatterns = [   url(r'^$',  views.PostListView.as_view(), name='post_list'),]
```

url’leri yazbilmek için import ediyoruz ve views’larımızı kullanabilmek
içinde views.py dosyamızı da import ediyoruz. Burada siteye bu “/” url
ile gelindiğinde çağırılacak class view’ı belirtiyoruz. Bir class view
kulladığımız için view’ın isminin sonuna .as\_view() eklemesi yapıyoruz.

![](https://cdn-images-1.medium.com/max/800/1*lgGjly9PI1OkQQ_IEcRS4Q.png)

Sonuç bu şekilde bir görsel elde ediyoruz (Tabi burada Bootstrap
kullandım). Butün bu kodlar arasındaki bağlanyı django kendisi yapıyor.

Başta da belirttiğim gibi bu yazı giriş seviyesinde django bildiğiniz
var sayar ve bu yüzden bazı noktalar doğrudan yazının konusu olmadığı
için açıklanmamıştır.

Python ve django da yeniyim ve ilk defa böyle bir blog tuttuyorum,
hatalarım için şimdiden özür dilerim, elimden gelen en iyi şekilde
anlatmaya çalıştım. Umarım birilerine faydası olur. Eklemelere, yazıdaki
eksik ve ya hatalı bir yer varsa düzeltmelere her zaman açığım.

Bir sonraki yazıda görüşmek üzere. Herkese iyi günler, iyi çalışmalar
dilerim.