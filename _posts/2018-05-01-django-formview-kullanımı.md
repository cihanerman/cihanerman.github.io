---
layout: post
title: Django FormView kullanımı 
tags: [django, python]
---

Herkese merhaba.  
Bu yazımda django’da ki FormView’in nasıl kullanıldığını anlatmaya
çalışacağım. Kaynak olarak
[https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-editing/](https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-editing/)
kulladım.  
Not: Bu yazı giriş seviyesinde django bildiğinizi kabul eder.

Önce models.py içinde modelimizi oluşturalım.

```ptyhon
from django.db import modelsclass Post(models.Model):    title = models.CharField()    context = models.TextField()
```

Şimdide forms.py dosyamızın içinde formumuzu oluşturalım.

```ptyhon
from django import forms
```

```ptyhon
from .models import Post
```

```ptyhon
class PostForm(forms.ModelForm):   
    class Meta:      
    model = Post      
    fields = ['title', 'context',]
```

Burada PostForm formumuzu temsil ediyor ve ModelForm’dan inherit
ediyoruz. model kısmında modelimiz belirtiyoruz ve fields kısmında
modelimizde ki form ile doldurulacak alanları yazıyoruz.(Post’un
oluşturulma tarihi olabilir ve oluşturulduğu tarih otomatik
doldururabiliriz, bu durumda bunu PostForma eklememiz gerekmez, bu işi
model kısmında halledilebilir)

views.py dosyamıza formumuzu oluşturacak class view’ı yazalım.

```ptyhon
from myapp.forms import PostFormfrom 
django.views.generic.edit import FormViewclass 

PostView(FormView):    t
    emplate_name = 'post_form.html'    
    form_class = PostForm    
    success_url = '/'
```

PostForm ve FormView’i impor ediyoruz. template\_name formumuzu
görüntüleyeceğimiz html sayfası(birazdan yazacağız) form\_class’a
ModelForm’u veriyoruz(burada PostForm oluyor), success\_url’de form
gönderme işlemi başarılı olursa yönlendireceğimiz adresin url’ini
veriyoruz.

Şimdide post\_form.html’i yazalım.

```html
<form action="" method="post">csrf_token
    {\{ form.as_p }}    
    <input type="submit" value="Send" />
</form>
```

Burada fields larımızı form öğesi olarak yerleştiren kısım {{ form.as\_p
}} süslü parantezler ekranda gösterimi sağlayan django template
syntax’ı. .as\_p her öğeyi paragraf gibi değerlendirmesini
söylüyoruz.(form inputlarını alt alta sıralamak için)

Ve son olarak form sayfamızın urls.py içerisine from’un url’ini
ekliyoruz.

``` {#c7a5 .graf .graf--pre .graf-after--p name="c7a5"}
from django.conf.urls import urlfrom . import views
```

``` {#7dd4 .graf .graf--pre .graf-after--pre name="7dd4"}
urlpatterns = [   url(r'^post/new/$', views.PostView.as_view(), name='post_new'),]
```

Bundan sonra bu url’de form doldurulup, send butonuna basıldığında form
gönderilir. Formun veri tabanına kaydedilmesi bu yazının konusu olmadığı
için burada anlatmıyorum. Veri tabanı kodlarınızda bir sıkıntı yoksa
poslar veri tabanına kaydedilir.

![](https://cdn-images-1.medium.com/max/800/1*v5b5y-BypTmeOFrpNvBFhg.png)

Görüntü buna benziyor. Ben yazı gereksiz uzamasın diye bazı kısımları
çıkardım(çok büyük bir far yok).

Django da işleri epey kolaylaştıran bir yöntem. Daha güzel ve kolay bir
yöntemde var ileriki yazımda onada değineceğim.

Bir yazının daha sonuna geldik.(Daha 3. yazım :) )Yapıcı eleştirilere,
yazıya katkılara her zaman açığım. Eklemek istedikleriniz ve ya yanlış
olduğunu düşündüğünüz yerleri yorumlarda belirtebilirsiz.

Bir sonraki yazıda buluşmak üzere. Herkese iyi günler, iyi çalışmalar
dilerim.