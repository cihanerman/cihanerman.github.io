---
title: "R, Keras ve Gan Gan Gan"
date: 2019-09-21T11:30:03+00:00
# weight: 1
# aliases: ["/first"]
tags: ["r", "keras", "gans", "machine learning", "deep learning", "ai"]
author: "Cihan Erman"
# author: ["Me", "You"] # multiple authors
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
description: "R'da keras kullanarak gan modeli oluşturma"
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
Giriş
=====

Herkese merhaba. Bu yazımda [r](https://www.r-project.org/) dilinde
[keras](https://keras.rstudio.com/) kütüphanesini kullanarak bir gan
modeli oluşturmaya çalışacağım. İde olarak [r
studio](https://www.rstudio.com/) kullanacağım.

Gan mı?
-------

Gan ya da gans Türkçeye “Çekişmeli Üretici Ağlar” olarak çevriliyor.
Başka çevirileride var, siz istediğiniz isimlendirmeyi seçebilirsiniz.
İngilizcede “Generative Adversarial Networks” olarak geçiyor. Ben yapay
zeka konusunda yeni bir yazılımcıyım, ganları kendi anladığım şekliyle
sizlere anlatacağım. Bu şekilde anlatmamın benim gibi bu konulara yeni
başlayanlara daha faydalı olacağını düşünüyorum. Bu konuda daha uzun
süre çalışmalar yapmış insanlarında yazılarını okumanızı tavsiye ederim.

> Basit, karmaşıktan iyidir.

Ganları anladığım şekilde basitçe anlatmaya çalışayım;  
Ganların olayı ismindende anlaşılacağı gibi üretmek. İki yapılı, bir
üreticimiz( Generator ), bir de denetleyicimiz( Discriminator ) var. İki
yapılı bir ağ. Örnek üzerinden gidelim. Üreticimizi gerçek fotoğraf ya
da resimlerle eğitiyoruz, üreticmiz bu öğrendikleri ile bir fotoğraf ya
da resim üretiyor. Dentleyicimizde bu oluşan fotoğraf ya da resimlerin
gerçek olup olmadığını denetliyor. Üreticimiz denetleyiciden geçebilecek
fotoğraf ya da resim oluşturana kadar backpropagation yapıyoruz.
Buradaki önemli nokta gan’ın öğrendikleri üzerinden aslında var olmayan
fotoğraf ya da resim üretmesi.

Ganlar var olmayan insan yüzleri üretilebiliyor. Var olmayan resim,
fotoğraf üretebiliyor, müzik besteleyebiliyor. Örnekler çoğaltılabilir
ama burada birazda hayal etmenizi isterim. Ganlar anlatılırken genelde
şu örnek üzerinden anlatılır, bilginiz olsun diye anlatıyorum; Üretici
kalpazan oluyor, denetçide dedektif. Kalpazan, dedektifi kandırabilcek
kalitede sahte resim ya da fotoğraf üretmeye çalışıyor. Kandımayı başara
bildiği zaman modelimizin eğitimi tamamlanmış oluyor.

Elimizi Kirletmeye Başlayalım
=============================

R ve keras kullanarak bir gans modeli oluşturmaya çalışacağı. [Bu
kaynaktan](https://blogs.rstudio.com/tensorflow/posts/2018-08-26-eager-dcgan/)
yararlanacağım. [Mnist](https://en.0wikipedia.org/wiki/MNIST_database)
veri setini kullanacağım.

Kütüphaneler
------------

Çalışmamıza kullanacağımız kütüphaneleri eklemekle başlayalım.

    library(keras)
    library(tfdatasets)

Veri Seti ve Ön İşleme
----------------------

Veri setimizi içe aktarıp gerekli bazı ön işlemlerimizi yapalım.
Verimizdeki değerleri normalize etmek gibi.

    data <- dataset_mnist()
    str(data)

    ## List of 2
    ##  $ train:List of 2
    ##   ..$ x: int [1:60000, 1:28, 1:28] 0 0 0 0 0 0 0 0 0 0 ...
    ##   ..$ y: int [1:60000(1d)] 5 0 4 1 9 2 1 3 1 4 ...
    ##  $ test :List of 2
    ##   ..$ x: int [1:10000, 1:28, 1:28] 0 0 0 0 0 0 0 0 0 0 ...
    ##   ..$ y: int [1:10000(1d)] 7 2 1 0 4 1 4 9 5 9 ...

    summary(data)

    ##       Length Class  Mode
    ## train 2      -none- list
    ## test  2      -none- list

    c(train_img, train_label) %<-% data$train
    c(test_img, test_label) %<-% data$test

    train_img <- (train_img - 127.5) / 127.5
    train_img <- array_reshape(train_img, c(60000,28*28))

Veri seti insan elyazılarıyla yazılmış rakam resimlerinden oluşuyor.
Bizim üreticimiz, denetleyiciden geçebilecek kalitede sahte resimler
üretmeyi öğrenecek( umarım ). Aşağıda veri setinden bazı örenek
resimlere yer veriyorum.

    par(mfcol=c(6,6))
    par(mar=c(0, 0, 3, 0), xaxs='i', yaxs='i')
    for (idx in 1:36) { 
        im <- test_img[idx,,]
        im <- t(apply(im, 2, rev)) 
        image(1:28, 1:28, im, 
              xaxt='n', main=paste(test_label[idx]))
    }

![](/unnamed-chunk-4-1.png)

Üretici
-------

Üreticimizi oluşturacak foksiyonu yazalım.

    create_generator <- function(){
        generator <- keras_model_sequential()
        
        generator %>%
            layer_dense(512, input_shape = 100) %>%
            layer_activation_relu() %>%
            layer_dense(512) %>%
            layer_activation_relu() %>%
            layer_dense(1024) %>%
            layer_activation_relu() %>%
            layer_dense(784, activation = "tanh")
        
        generator %>% compile(
            loss= "binary_crossentropy",
            optimizer= optimizer_adam(lr= 0.0001, beta_1 = 0.5)
        )
            
       return(generator) 
    }

    generator <- create_generator()
    summary(generator)

    ## Model: "sequential"
    ## ___________________________________________________________________________
    ## Layer (type)                     Output Shape                  Param #     
    ## ===========================================================================
    ## dense (Dense)                    (None, 512)                   51712       
    ## ___________________________________________________________________________
    ## re_lu (ReLU)                     (None, 512)                   0           
    ## ___________________________________________________________________________
    ## dense_1 (Dense)                  (None, 512)                   262656      
    ## ___________________________________________________________________________
    ## re_lu_1 (ReLU)                   (None, 512)                   0           
    ## ___________________________________________________________________________
    ## dense_2 (Dense)                  (None, 1024)                  525312      
    ## ___________________________________________________________________________
    ## re_lu_2 (ReLU)                   (None, 1024)                  0           
    ## ___________________________________________________________________________
    ## dense_3 (Dense)                  (None, 784)                   803600      
    ## ===========================================================================
    ## Total params: 1,643,280
    ## Trainable params: 1,643,280
    ## Non-trainable params: 0
    ## ___________________________________________________________________________

Denetleyici
===========

Denetleyicimizi oluşturacak fonksiyonu yazalım.

    create_discriminator <- function(){
        discriminator <- keras_model_sequential()
        
        discriminator %>%
            layer_dense(1024, input_shape = 784) %>%
            layer_activation_relu() %>%
            layer_dropout(0.4) %>%
            layer_dense(512) %>%
            layer_activation_relu() %>%
            layer_dropout(0.4) %>%
            layer_dense(256) %>%
            layer_activation_relu() %>%
            layer_dense(1, activation = "sigmoid")
        
        discriminator %>% compile(
            loss= "binary_crossentropy",
            optimizer= optimizer_adam(lr= 0.0001, beta_1 = 0.5)
        )
            
       return(discriminator) 
    }

    discriminator <- create_discriminator()
    summary(discriminator)

    ## Model: "sequential_1"
    ## ___________________________________________________________________________
    ## Layer (type)                     Output Shape                  Param #     
    ## ===========================================================================
    ## dense_4 (Dense)                  (None, 1024)                  803840      
    ## ___________________________________________________________________________
    ## re_lu_3 (ReLU)                   (None, 1024)                  0           
    ## ___________________________________________________________________________
    ## dropout (Dropout)                (None, 1024)                  0           
    ## ___________________________________________________________________________
    ## dense_5 (Dense)                  (None, 512)                   524800      
    ## ___________________________________________________________________________
    ## re_lu_4 (ReLU)                   (None, 512)                   0           
    ## ___________________________________________________________________________
    ## dropout_1 (Dropout)              (None, 512)                   0           
    ## ___________________________________________________________________________
    ## dense_6 (Dense)                  (None, 256)                   131328      
    ## ___________________________________________________________________________
    ## re_lu_5 (ReLU)                   (None, 256)                   0           
    ## ___________________________________________________________________________
    ## dense_7 (Dense)                  (None, 1)                     257         
    ## ===========================================================================
    ## Total params: 1,460,225
    ## Trainable params: 1,460,225
    ## Non-trainable params: 0
    ## ___________________________________________________________________________

Ve Gan
------

Artık gan modelimizi oluşturmaya hazırız.

    create_gan <- function(discriminator, generator){
        discriminator.trainable <- F
        gan_input <- layer_input(shape = c(100))
        g <- generator(gan_input)
        gan_output <- discriminator(g)
        gan <- keras_model(inputs = gan_input, outputs = gan_output)
        gan %>% compile(loss= "binary_crossentropy", optimizer= optimizer_adam())
        return(gan)
    }

    gan <- create_gan(discriminator, generator)
    summary(gan)

    ## Model: "model"
    ## ___________________________________________________________________________
    ## Layer (type)                     Output Shape                  Param #     
    ## ===========================================================================
    ## input_1 (InputLayer)             [(None, 100)]                 0           
    ## ___________________________________________________________________________
    ## sequential (Sequential)          (None, 784)                   1643280     
    ## ___________________________________________________________________________
    ## sequential_1 (Sequential)        (None, 1)                     1460225     
    ## ===========================================================================
    ## Total params: 3,103,505
    ## Trainable params: 3,103,505
    ## Non-trainable params: 0
    ## ___________________________________________________________________________

Eğitim Şart
-----------

Oluşturduğumuz gan modelini eğitmeye hazırız(?).

    epochs <- 50
    batch_size <- 256
    traning_shape <- 60000
    for(e in 0:epochs){
        for(b in 0:batch_size){
            noise <- runif(n = batch_size, min = 0, max = 1) %>%
                    matrix(nrow = batch_size, ncol = 100)
            generated_img <- generator %>% predict(noise)
            img_batch <- train_img[floor(runif(n= batch_size, min= 0, max= 60000)),]
            # print(nrow(img_batch))
            while(nrow(img_batch) != 256 ){
              img_batch <- train_img[floor(runif(n= batch_size, min= 0, max= 60000)),]  
            }
            # img_batch <- train_img[sample(0:traning_shape,batch_size),]
            concatenate <- rbind(img_batch, generated_img)
            y_dis <- matrix(0, batch_size*2)
            y_dis[0:batch_size] <- 1
            discriminator.trainable <- T
            train_on_batch(discriminator, concatenate,y_dis)
            noise <- runif(n = batch_size*100, min = 0, max = 1) %>%
                    matrix(nrow = batch_size, ncol = 100)
            y_gen <- matrix(1, batch_size)
            discriminator.trainable <- F
            train_on_batch(gan, noise, y_gen)
        }
        print(c("epochs: ",e))
    }

Kayıt Önemli
------------

Oluşturduğumuz modelim ağırlıklarını kayıt edelim.

    save_model_weights_hdf5(gan, "gan.h5", overwrite = T)
    save_model_weights_hdf5(generator, "generator.h5", overwrite = T)

Görselleştirme
--------------

Yukarıda orjinal fotoğrafların bir kısmını götermiştik. Şimdide
modelimizin oluşturduğu fotoğrafları görme zamanı.

    noise <- runif(n = 12, min = 0, max = 1) %>%
                    matrix(nrow = 100, ncol = 100)

    gen_img <- generator %>% predict(noise)
    gen_img <- array_reshape(gen_img, c(100,28,28))

    par(mfcol = c(3, 4))
    par(mar = c(0.5, 0.5, 0.5, 0.5),
      xaxs = 'i',
      yaxs = 'i')
    for (idx in 1:12) { 
        im <- gen_img[idx,,]
        im <- t(apply(im, 2, rev)) 
        image(1:28, 1:28, im, 
              xaxt='n',yaxt = 'n')
    }

![](/unnamed-chunk-10-1.png)

Sonuç
=====

Keras ve r’ın birlikte kullanımına dağir basit bir örnek yaptık.
Başarılı bir model oluşturamadığımın farkındayım :( Sürekli kendimi
geliştirmeye çalışıyorum. R ve keras’ı birlikte kullanmak isteyenler için
bir giriş yazısı yazmaya çalıştım, umarım faydalı olmuştur. Kendinize
iyi bakın, iyi çalışmalar dilerim.
