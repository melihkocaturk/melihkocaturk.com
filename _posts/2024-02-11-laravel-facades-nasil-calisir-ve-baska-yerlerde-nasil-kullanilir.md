---
layout: post
title: Laravel Facades Nasıl Çalışır ve Başka Yerlerde Nasıl Kullanılır?
---

Facade pattern, nesne yönelimli programlamada sıklıkla kullanılan bir design pattern’dir. Facade aslında karmaşık bir kütüphaneyi daha basit ve daha okunabilir bir arayüz sağlamak için saran bir sınıftır. Facade pattern’i aynı zamanda bir grup karmaşık ve kötü tasarlanmış API'ye birleşik ve iyi tasarlanmış bir API sağlamak için de kullanılabilir.

![Facade Pattern](/assets/images/2024/02/facade-pattern.jpg "Facade Pattern"){: class="img-fluid"}

Laravel framework, Facades olarak da adlandırılan bu pattern’e benzer bir özelliğe sahiptir. Bu eğitimde Laravel "Facades"i diğer frameworklere nasıl taşıyacağımızı öğreneceğiz. Devam etmeden önce IoC (Inversion Of Control) container hakkında temel bir anlayışa sahip olmanız gerekir.

Öncelikle Laravel facades içeride nasıl çalıştığını inceleyelim, ardından bu özelliği diğer ortamlara nasıl uyarlayabileceğimizi tartışacağız.

## Laravel'de Facades

Laravel facade, container içindeki servislere static benzeri arayüz sağlayan bir sınıftır. Dokümantasyona göre facades, container servislerinin temel uygulamasına erişim için bir vekil görevi görüyor.

Ancak PHP topluluğunda bu adlandırmayla ilgili pek çok tartışma var. Bazıları, Facade pattern’i tam olarak uygulamadığından, geliştiricilerin kafa karışıklığını önlemek için terimin değiştirilmesi gerektiğini savundu. Bu adlandırma kafanızı karıştırıyorsa, size uygun olanı söylemekten çekinmeyin, ancak kullanacağımız temel sınıfın Laravel framework’te Facade olarak adlandırıldığını lütfen unutmayın.

### Laravel'de Facades Nasıl Uygulanır?

Muhtemelen bildiğiniz gibi container içindeki her servisin benzersiz bir adı vardır. Laravel uygulamasında, bir servise doğrudan container’dan erişmek için App::make() metodunu veya app() helper fonksiyonunu kullanabiliriz.

<pre>
<?php

App::make('some_service')->methodName();
</pre>

Daha önce de belirtildiği gibi Laravel, servisleri geliştiricinin kullanımına daha okunaklı bir şekilde sunmak için facade sınıflarını kullanır. Facade sınıfı kullanarak aynı şeyi yapmak için yalnızca aşağıdaki kodu yazmamız gerekir:

<pre>
// ...
someService::methodName();
// ...
</pre>

Laravel'de tüm servislerin bir facade sınıfı vardır. Bu facade sınıfları, Illuminate/Support paketinin bir parçası olan base Facade sınıfını genişletir. Uygulamaları gereken tek şey, container içindeki servis adını döndüren getFacadeAccessor metodudur.

Yukarıdaki sözdiziminde someService facade sınıfını ifade eder. methodName aslında container’daki orjinal servisin bir metodudur. Bu sözdizimine Laravel bağlamı dışında bakarsak, someService adında bir sınıfın methodName() adında static bir metodu çağırdığı anlamına gelir, ancak Laravel bu arayüzü bu şekilde uygulamaz. Bir sonraki bölümde Laravel'in base Facade sınıfının perde arkasında nasıl çalıştığını göreceğiz.

### Base Facade

Facade sınıfı, servis container’a referans saklayan $app adında bir private property’e sahiptir. Laravel dışında facades kullanmanız gerekiyorsa, setFacadeApplication() metodunu kullanarak container’i açıkça ayarlamamız gerekir. Bunu kısa süre içinde inceleyeceğiz.

Base facade sınıfının içinde, gerçekte var olmayan static metotların çağrılmasını yönetmek için __callStatic sihirli metodu uygulandı. Laravel facade sınıfından static bir metot çağırdığımızda, facade sınıfı bu metodu uygulamadığından __callStatic metodu çağrılır. Sonuç olarak, __callStatic ilgili servisi container’dan alır ve bu metodu çağırır.

Base facade sınıfında __callStatic metodunun uygulanması:

<pre>
<?php
// ...
    /**
     * Handle dynamic, static calls to the object.
     *
     * @param  string  $method
     * @param  array   $args
     * @return mixed
     */
    public static function __callStatic($method, $args)
    {
        $instance = static::getFacadeRoot();

        switch (count($args)) {
            case 0:
                return $instance->$method();

            case 1:
                return $instance->$method($args[0]);

            case 2:
                return $instance->$method($args[0], $args[1]);

            case 3:
                return $instance->$method($args[0], $args[1], $args[2]);

            case 4:
                return $instance->$method($args[0], $args[1], $args[2], $args[3]);

            default:
                return call_user_func_array([$instance, $method], $args);
        }
    }
</pre>

Yukarıdaki metotta getFacadeRoot() servisi container’dan alır.

### Facade Sınıfının Anatomisi

Her facade sınıfı base sınıfı genişletir. Uygulamamız gereken tek şey getFacadeAccessor() metodudur. Bu metot, servisin adını container’a döndürmekten başka bir şey yapmaz.

<pre>
<?php

namespace App\Facades;

use Illuminate\Support\Facades\Facade as BaseFacade;

class SomeServiceFacade extends BaseFacade
{

    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor()
    {
        return 'some.service';
    }
}
</pre>

### Takma adlar

Laravel facades PHP sınıfları olduğundan, kullanmadan önce bunları içe aktarmamız gerekir. PHP'deki namespace ve autoloading desteği sayesinde, tüm sınıflara tam adlarıyla eriştiğimizde otomatik olarak yüklenirler. PHP ayrıca use ifadesini kullanarak sınıf takma adlarını da destekler:

<pre>
use App\Facades\SomeServiceFacade

SomeServiceFacade:SomeMethod();
</pre>

Ancak bunu, söz konusu facade sınıfına ihtiyaç duyduğumuz her senaryoda yapmak zorundayız. Laravel, bir alias loader kullanarak facade’lerin takma adlarını kendi yöntemiyle ele alır.

### Facade’ler Nasıl Takma Adlandırır?

Tüm takma adlar, /config dizininde bulunan app.php yapılandırma dosyası içindeki aliases dizisinde tutulur.

Diziye göz attığımızda her bir takma adın tam nitelikli bir sınıf adıyla eşlendiğini görebiliriz. Bu, facade sınıfı için istediğimiz herhangi bir adı kullanabileceğimiz anlamına gelir:

<pre>
// ..
'aliases' => [
    // ...
    'FancyName' => 'App\Facades\SomeServiceFacade',
],
</pre>

Şimdi Laravel'in bu diziyi nasıl kullandığını görelim. Önyükleme aşamasında Laravel, Illuminate\Foundation paketinin parçası olan AliasLoader adlı bir servisi kullanır. AliasLoader alias dizisini alır, tüm öğeleri yineler ve PHP'nin spl_autoload_register'ını kullanarak __autoload fonksiyonlarından oluşan bir kuyruk oluşturur. Her __autoload fonksiyonu, PHP'nin class_alias fonksiyonunu kullanarak ilgili facade sınıfı için bir takma ad oluşturmaktan sorumludur.

Sonuç olarak, normalde use ifadesi ile yaptığımız gibi, sınıfları kullanmadan önce içe aktarmamız ve takma ad vermemiz gerekmeyecek. Yani ne zaman var olmayan bir sınıfa erişmeye çalışsak, PHP uygun autoloader’ı bulmak için __autoload kuyruğunu kontrol edecektir. O zamana kadar AliasLoader zaten tüm __autoload fonksiyonlarını kaydetmiştir. Her autoloader süslü bir sınıf adı alır ve onu alias dizisine göre orijinal sınıf adına çözümler. Son olarak o sınıf için bir takma ad oluşturur. Aşağıdaki metot çağrısını göz önünde bulundurun:

<pre>
<?php

// FancyName is resolved to App\Facades\SomeServiceFacade according to the aliases array

FancyName::someMethod()
</pre>

Perde arkasında FancyName, App\Facades\SomeServiceFacade olarak çözümlendi.

## Diğer Framework’lerde Facade Kullanımı

Artık Laravel'in facade’leri ve takma adlarını nasıl ele aldığına dair iyi bir anlayışa sahip olduğumuza göre, Laravel'in facade yaklaşımını diğer ortamlara uyarlayabiliriz. Bu yazımızda Silex framework’te facade’leri kullanacağız. Ancak aynı konsepti takip ederek bu özelliği diğer framework lere de uyarlayabilirsiniz.

Silex'in kendi container’ı vardır. Container içindeki bir servise erişmek için $app nesnesini şu şekilde kullanabiliriz:

<pre>
<?php

$app['some.service']->someMethod()
</pre>

Facade sınıfları yardımıyla Silex servislerimize de static benzeri bir arayüz sağlayabiliriz. Ayrıca bu facade’lere anlamlı takma adlar oluşturmak için AliasLoader servisini kullanabiliriz. Sonuç olarak yukarıdaki kodu şu şekilde yeniden düzenleyebiliriz:

<pre>
<?php

SomeService::someMethod();
</pre>

### Gereksinimler

Temel facade sınıfını kullanmak için, composer kullanarak Illuminate\Support paketini yüklemeniz gerekir:

Bu paket başka servisleri de içeriyor ancak şimdilik sadece base Facade sınıfına ihtiyacımız var.

### Facade Oluşturmak

Bir servise yönelik facade oluşturmak için base Facade sınıfını genişletmemiz ve getFacadeAccessor metodunu uygulamamız yeterlidir.

Bu eğitim için tüm facade’leri src/Facades altında tutalım. Örnek olarak some.service isimli bir servis için facade sınıfı şu şekilde olacaktır:

<pre>
<?php

namespace App\Facades

use Illuminate\Support\Facades\Facade;

class SomeServiceFacade extends Facade {
    /**
     * Get the registered name of the component.
     *
     * @return string
     */
    protected static function getFacadeAccessor()
    {
        return 'some.service';
    }
}

</pre>

Lütfen sınıfı app\facades altına namespace ile yerleştirdiğimizi unutmayın.

Geriye kalan tek şey uygulama container’ını facade sınıfına ayarlamaktır. Daha önce de belirtildiği gibi, facade sınıfı ile static bağlamda bir metot çağırdığımızda, __callStatic tetiklenir. _callStatic, container içindeki servisi tanımlamak için getFacadeAccessor() tarafından döndürülen verileri kullanır ve onu almaya çalışır. Laravel dışında base Facade sınıfını kullandığımızda container nesnesi otomatik olarak ayarlanmaz, dolayısıyla bunu manuel olarak yapmamız gerekecek.

Bunu yapmak için base facade sınıfı, o facade sınıfı için uygulama container’ını ayarlayan setFacadeApplication adlı bir metodu kullanır.

app.php dosyamıza aşağıdaki kodu eklememiz gerekiyor:

<pre>
<?php

Illumiante\Support\Facade::setFacadeApplication($app);
</pre>

Bu, base facade sınıfını genişleten tüm facade’ler için container’ı ayarlayacaktır.

Artık servise container’dan erişmek yerine, az önce oluşturduğumuz facade sınıfını kullanabiliriz, bu da tüm metotları static bağlamda çağırmamıza olanak tanır.

### Takma Adların Uygulanması

Facade sınıflarına takma ad vermek için daha önce tanıttığımız AliasLoader'ı kullanacağız. AliasLoader, illuminate\foundation paketinin bir parçasıdır. Paketin tamamını indirebilir veya kodu ödünç alıp dosya olarak saklayabiliriz.

Eğer sadece kaynak dosyayı kopyalamak istiyorsanız onu src/Facades altında saklamanızı öneririm. AliasLoader sınıfını projenizin mimarisine göre adlandırabilirsiniz.

Bu örnek için, kodu ve namespace’i app/facades altına kopyalayalım.

### Alias Dizisini Oluşturma

config dizinimizde aliases.php adında bir dosya oluşturalım ve alias-facade bağlantısını yapalım:

<pre>
<?php

return [
    'FancyName' => 'App\Facades\SomeService',
];
</pre>

FancyName, App\Facades\SomeService yerine kullanmak istediğimiz addır.

**Takma Adların Kaydedilmesi**

AliasLoader singleton bir servistir. Alias yükleyicisinin örneğini oluşturmak veya almak için, alias dizisini parametre olarak kullanarak getInstance metodunu çağırmamız gerekir. Son olarak tüm takma adları kaydetmek için register metodunu çağırmamız gerekir.

Yine app.php dosyasına aşağıdaki kodu ekleyin:

<pre>
<?php

// ...

$aliases = require __DIR__ . '/../../config/aliases.php';
App\Facades\AliasLoader::getInstance($aliases)->register();
</pre>

Ve hepsi bu kadar! Artık servisi şu şekilde kullanabiliriz:

<pre>
<?php

FancyName::methodName();
</pre>

Okuduğunuz için teşekkürler!

**Kaynak:** https://www.sitepoint.com/how-laravel-facades-work-and-how-to-use-them-elsewhere/