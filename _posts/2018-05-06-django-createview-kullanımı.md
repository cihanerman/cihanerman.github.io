---
layout: post
title: Django’da CreateView kullanımı
tags: [django, python]
---

Herkese merhaba.  
Bu yazımda django’da ki CreateView’i anlatmaya çalışacağım. CreateView
django’da ki generik ViewClass’lar dan biri ve işleri çok
kolaylaştırıyor. Kayank olarak
[djangoproject](https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-editing/)
sitesinden faydalandım. Hadi başlayalım.

Öncelikle models.py dosyası içerisinde modelimizi oluşturalım.

```python
class Post(models.Model):   
    title = models.CharField()   
    text = models.TextField()
```

İki property’i olan Post ismindeki modelimizi tanımladık. Şimdi forms.py
dosyamıza gidip formumuzu oluşturalım(Bu kısımları önceki yazılarımda
açıkladığım için burada anlatmıyorum, önceki yazılarımı
inceleyebilirsiniz.)

```python
from django import formsfrom .models import Post
```

```python
class PostForm(forms.ModelForm):   
    class Meta:      
        model = Post      
        fields = ['title','text',]
```

ModelForm oluşturmayı bir önceki yazımda anlatmıştım. Burada sadece kodu
göstermekle yetineceğim. Merak edenler bir önceki
[yazımı](https://medium.com/@cihanerman_/django-formview-kullanımı-cf3f4f2f575b)
inceleyebilirler. Şimdide views.py dosyasında create view’i oluşturalım.

```python
from django.views.generic.edit import CreateViewfrom .models import Post
```

```python
class PostAdd(CreateView):   
    model = Post   f
    ields = ['title','text']   
    template_name = "blog/post_add.html"   
    success_url = "/"
```

Burada CreateView’a hangi modeli kullanıcağını belirtiyoruz. Daha sonra
form da kullanacağımız alanları, formu göstereceğimiz html sayfasını ve
son olarak form başarılı bir şekilde kayıt olursa(Burada yeni post
ekleme işlemi)gideci adresin url’ini belirtiyoruz. Şimdi de
post\_add.html sayfasını oluşturalım.

```html
<form method="post"> csrf_token     
    {\{ form.as_p }}    
    <input type="submit" value="Save" />
</form>
```

Sadece bukadar bir önceki
[yazımda](https://medium.com/@cihanerman_/django-formview-kullanımı-cf3f4f2f575b)
bu kod parçasını anlatmıştım, oradan inceleyebilirsiniz. Son olarak
urls.py sayfasına CreateView’ın url’ini belirtelim.
```python
from django.conf.urls import url

from . import views

urlpatterns = [

url(*r*’\^\$’, views.PostAdd.as\_view(), *name*=’post\_add’),

]
```
Görüntü aşağıdaki ne benzer olacaktır(Ben anlatımı basitleştirmek için
kendi yaptığım örnekten çıkarmalar yaptım, resimdeki fazlalıkların
sebebi bu).

![](https://cdn-images-1.medium.com/max/800/1*Wb8wpB5Mk-tbv4nafFljxA.png)

Bir yazının daha sonuna geldik. Yanlış bulduğunuz yada eklemek
istediğiniz bir şey olursa lütfen yorumlarda belirtiniz. Bir sonraki
yazımda updateView’ı anlatıcam o yazıyı bunun devamı gibi
görebilirsiniz, bu yazının bittiği yerden başlayacak. Herkese iyi günler
iyi çalışmalar dilerim.