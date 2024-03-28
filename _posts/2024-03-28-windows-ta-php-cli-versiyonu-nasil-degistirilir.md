---
layout: post
title: Windows'ta PHP CLI versiyonu nasıl değiştirilir?
---

Son zamanlarda Laravel 10 ile bir uygulama geliştiriyorum. Laravel 10 ile çalışabilmek için PHP CLI'mı yükseltmem gerekiyor. Şu anda bilgisayarımda PHP 7.0.33 (cli) yüklü. Terminalde kendi PHP CLI'nizi aşağıdaki gibi kontrol edebilirsiniz.

```
php -v
PHP 7.0.33 (cli) (built: Dec  5 2018 21:22:29) ( ZTS )
Copyright (c) 1997-2017 The PHP Group
Zend Engine v3.0.0, Copyright (c) 1998-2017 Zend Technologies
```

Şimdi Ortam Değişkenleri altında Sistem değişkenleri bölümündeki PHP CLI yolunu değiştireceğim. Bu şekilde PHP CLI, composer, nodejs'e ve daha fazlasına bilgisayarımın her yerinden erişebilirim.

## Ortam Değişkenlerine Erişim

Ortam değişkenlerine erişmek için Başlat menüsüne tıklayıp "ortam değişkenleri" yazmanız yeterlidir. Ortam değişkenlerine şuradan erişebilirsiniz:

![Ortam Değişkenleri](/assets/images/2024/03/ortam-degiskenleri.png "Ortam Değişkenleri"){: class="img-fluid"}

"Ortam Değişkenleri" butonuna tıkladıktan sonra aşağıdaki popup pencereyi göreceksiniz.

![Sistem Değişkenleri](/assets/images/2024/03/sistem-degiskenleri.png "Sistem Değişkenleri"){: class="img-fluid"}

Ortam değişkenlerini düzenleyin.

## PHP CLI Sürümünü Değiştirin

Path değişkenini düzenleyin. İstediğiniz PHP CLI sürümünü Path değişkenine eklemeniz yeterlidir.

![Ortam Değişkenini Düzenle](/assets/images/2024/03/ortam-degiskenini-duzenle.png "Ortam Değişkenini Düzenle"){: class="img-fluid"}

WampServer'da birden fazla PHP sürümüne sahibim. PHP CLI sürümünü değiştirmek için 7.0.33 olan PHP verisyonunu 8.1.8 olarak düzenledim.

Path değişkenimi, "C:\wamp64\bin\php\php7.0.33" yerine "C:\wamp64\bin\php\php8.1.8" olarak değiştiriyorum.

Açık olan tüm terminalleri ve VSCode'u kapatın. Daha sonra aşağıdaki gibi PHP CLI verisyonunu kontrol edin. Şimdi değiştirdiğiniz PHP CLI sürümünü görmelisiniz.

```
PS C:\Users\melih> php -v
PHP 8.1.8 (cli) (built: Jul  5 2022 23:04:29) (ZTS Visual C++ 2019 x64)
Copyright (c) The PHP Group
Zend Engine v4.1.8, Copyright (c) Zend Technologies
```