---
layout: post
title: Django UpdateView kullanımı
tags: [django, python]
---

Herkese merhaba.  
Bu yazı bir
[önceki](https://medium.com/@cihanerman_/djangoda-createview-kullanımı-a892bb34936a)
yazımın devamıdır, bir önceki yazımda CreateView’i anlatmaya
çalışmıştım. Kaynak olarak
[djangoproject](https://docs.djangoproject.com/en/2.0/ref/class-based-views/generic-editing/)
sitesi kullanıyorum.\
Hadi başlayalım.

Bi önceki yazımızda ModelForm’dan inherit ettiğimiz formumuzu CreatView
ile çok kolay ve kısa bir şekilde veri tabanına kaydetmiştik. Şimdi
kaldığımız yerden bu kaydettiğimiz Post’u UpdateView kullanarak aynı
kolaylıkla poslarımızı güncelleyeceğiz. Bu sebepten bir önceki yazıda
kaldığımız yerden devam ediyoruz.

İlk olarak UpdateView’den inherit edeceğimiz classview’ı oluşturyoruz.

```python
from .models import Postfrom django.views import generic
```

```python
class PostUpdateView(generic.UpdateView):   
    model = Post   
    fields = ['text']   
    template_name = "blog/post_edit.html"   
    success_url = "/"
```

views.py içerisine modelimizi ve generic sınıfımızı impor ediyoruz, daha
sonra classview’ı UpdateView sınıfından inherit ediyoruz. Kullanıcağımız
modeli, model kısmında , güncellemeye izin vereceğimiz kısımları fields
kısmında(Burada sadece text kısmının değiştirlmesine izin veriyoruz)
belirtiyoruz, ClassView’ın kullanacağı html sayfasını template\_name’ de
belirtiyoruz ve ensonda bu işlem başarılı olursa hangi sayfaya
yönlendireceğimizi belirtiyoruz.(Bu örnekte anasayfa). Şimdide html
sayfamızı oluşturalım.

```html
<form action="" method="post"> csrf_token    
    {{ form.as_p }}   
    <input type="submit" value="Send message" />
</form>
```

Evet sadece bukadar. Son olarak urls.py’de sayfanın url’ini
belirtiyoruz.

```python
from django.conf.urls import urlfrom . import views

urlpatterns = [   url(r'^post/update/(?P<pk>[0-9]+)/$',   views.PostUpdateView.as_view(), name='updateblog'),]
```

Herşey bukadar, gerikalanı django kendisi hallediyor.

![](https://cdn-images-1.medium.com/max/800/1*4tEEC8UZa66oRUfR-sr8JQ.png)

Ortaya buna benzer bir görüntü çıkacak.

Bir sonraki yazımda DetailView’i anlatmaya çalışacağım. Eksik yada
yanlış olduğunu düşündüğünüz veya eklemek istediğiniz bir yer olursa
yorumlarda belirtmenizi rica ederim, yapıcı eleştirilere her zaman
açığım.

Bu yazımda basit olarak djangodaki generic view’lerden UpdateView
anlatmaya çalıştım. Bir sonraki yazımda görüşmek üzere iyi günler, iyi
çalışmalar.