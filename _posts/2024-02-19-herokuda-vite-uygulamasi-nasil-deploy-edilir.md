---
layout: post
title: Heroku’da Vite Uygulaması Nasıl Deploy Edilir?
---

Eğer benim gibi yakın zamanda Laravel'in yeni bir kopyasını indirdiyseniz, muhtemelen Mix’in yerini Vite'ın aldığını fark etmişsinizdir.

Jetstream'in yeni sürümünü yüklediğimde bu bende kafa karışıklığına neden oldu, çünkü hem localde hem de Heroku'ya deploy ettiğimde her şey hizasız görünüyordu.

Localde, <code>npm run dev</code>'i çalıştırmanız gerekiyor ve bu, <code>npm run watch</code>'ın yerini alacak gibi görünüyor.

Ancak Heroku için birkaç değişiklik yapmalısınız.

Nodejs buildpack’i ekleyin ve PHP buildpack’in sonrasına yerleştirin. Bunu PHP'nin önüne koymak ilk hatamdı.

![Heroku Nodejs Buildpack](/assets/images/2024/02/heroku-nodejs-buildpack.png "Heroku Nodejs Buildpack"){: class="img-fluid"}

*package.json* dosyanızda “scripts” altında “build” için bir giriş olup olmadığını kontrol edin. Bu, deploy sırasında Heroku tarafından otomatik olarak bulunacak ve çalıştırılacaktır. Daha önce Heroku'ya özgü bir girişe ihtiyacınız vardı ancak son bir yıldır Heroku, varsa build'i kullanacak.

*package.json* dosyanız şöyle görünmelidir:

<pre>
{
    "private": true,
    "type": "module",
    "scripts": {
        "dev": "vite",
        "build": "vite build"
    },
    "devDependencies": {
        "@tailwindcss/forms": "^0.5.2",
        "alpinejs": "^3.4.2",
        "autoprefixer": "^10.4.2",,
        "axios": "^1.1.2",
        "laravel-vite-plugin": "^0.8.0",
        "postcss": "^8.4.6",
        "tailwindcss": "^3.1.0",
        "vite": "^4.0.0"
    }
}
</pre>

Kodunuzu yeniden deploy edin (buildpack eklediyseniz bu gereklidir) ve build loglarını kontrol edin.

Uygulamanızı yeniden yüklediğinizde CSS artık doğru olmalıdır. Yeni CSS'i yüklemeye zorlamak için browser önbelleğini temizlemeyi unutmayın.

**Kaynak:** https://colinlongworth.medium.com/deploying-compiling-laravel-vite-on-heroku-4816dbcd14f5

