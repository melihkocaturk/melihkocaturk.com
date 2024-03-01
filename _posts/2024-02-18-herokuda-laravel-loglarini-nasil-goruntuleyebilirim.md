---
layout: post
title: Heroku'da Laravel Loglarını Nasıl Görüntüleyebilirim?
---

Laravel varsayılan olarak hata ve mesajları diskteki bir dizine kaydeder; bu ideal değildir. Çünkü Heroku geçici (ephemeral) dosya sistemi kullanır; Dyno çalışırken dosya sisteminde yapılan herhangi bir değişiklik dyno kapatılana veya yeniden başlatılana kadar devam eder. Her dyno, en son deploy edilen dosya sisteminin temiz bir kopyasıyla yüklenir. Bu, Docker gibi container tabanlı sistemlerin yapısına benzer.

app/config/logging.php dosyasında çeşitli log kanallarını yapılandırabilirsiniz. Varsayılan olarak, yapılandırma dosyası “errorlog” kanalı için bir tanım içerir; bu, PHP'nin mesajları log’a kaydetmek için kendi hata yönetimini kullanmasına neden olur. Bunlar daha sonra otomatik olarak heroku loglarına gönderilir.

Dosyanın üst kısmında, varsayılan kanal LOG_CHANNEL environment değişkeninden okunacak şekilde ayarlanmıştır:

```
'default' => env('LOG_CHANNEL', 'stack'),
```

Bu, Heroku'da doğru şekilde log tutmak için LOG_CHANNEL environment değişkenini “errorlog” olarak ayarlayabileceğiniz anlamına gelir:

```
heroku config:set LOG_CHANNEL=errorlog
```

![Log Channel Config Var](/assets/images/2024/02/heroku-env-log-channel-errorlog.png "Heroku'da Laravel Loglarını Nasıl Görüntüleyebilirim?"){: class="img-fluid"}

Artık logları canlı olarak takip edebilirsiniz:

```heroku logs -t```

Okuduğunuz için teşekkürler.