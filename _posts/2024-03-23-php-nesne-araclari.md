---
layout: post
title: PHP - Nesne Araçları
---

Gördüğümüz gibi PHP, sınıflar ve metotlar gibi dil yapıları aracılığıyla nesne yönelimli programlamayı destekler. Dil ayrıca nesnelerle çalışmanıza yardımcı olmak için tasarlanmış fonksiyon ve sınıflar aracılığıyla daha geniş destek sağlar.

Bu bölümde, nesneleri ve sınıfları düzenlemek, test etmek ve değiştirmek için kullanabileceğiniz bazı araçlara ve tekniklere bakacağız.

Bu bölüm aşağıdaki araçları ve teknikleri kapsayacaktır:

- **Namespace**: Kodunuzu ayrı paket benzeri bölümler halinde düzenleyin
- **Include path**: Kod kütüphaneleriniz için merkezi erişilebilir konumları ayarlama
- **Sınıf ve nesne fonksiyonları**: Nesneleri, sınıfları, property ve metotları test etmeye yönelik fonksiyonlar
- **Reflection API**: Çalışma zamanında sınıf bilgilerine benzeri görülmemiş erişim sağlayan yerleşik sınıflardan oluşan güçlü bir paket
- **Nitelikler (Attributes)**: PHP'nin annotation uygulaması - sınıfların, metotların, property ve parametrelerin kodda etiketler kullanılarak zengin bilgilerle geliştirilebildiği bir mekanizma

## PHP ve Paketler

Bir paket, genellikle bir şekilde birlikte gruplandırılmış, ilgili sınıflar ve fonksiyonlar kümesidir. Paketler, bir sistemin parçalarını birbirinden ayırmak için kullanılabilir. Bazı programlama dilleri, paketleri resmi olarak tanır ve onlara farklı namespace’ler (ad alanları) sağlar. PHP'nin native bir paket kavramı yoktur, ancak PHP 5.3'ten itibaren namespace’leri tanıttı. Bu özelliği bir sonraki bölümde inceleyeceğiz. Ayrıca, sınıfları paket benzeri yapılar halinde organize etmenin eski yoluna da bir göz atacağım.

### PHP Paketleri ve Namespace’ler

PHP paket kavramını özünde desteklemese de, geliştiriciler geleneksel olarak kodlarını paket benzeri yapılar halinde düzenlemek için hem adlandırma şemalarını hem de dosya sistemini kullandılar.

PHP 5.3'e kadar, geliştiriciler dosyalarını paylaşılan bir bağlamda adlandırmak zorundaydılar. Başka bir deyişle, bir sınıfa ShoppingBasket adını verirseniz, sisteminizde anında kullanılabilir hale gelir. Bu iki büyük soruna neden oldu. Birincisi ve en zarar verici olanı, çakışmaları adlandırma olasılığıydı. Sonuçta, tek yapmanız gereken tüm sınıflarınıza benzersiz isimler vermeyi unutmamak, değil mi? Sorun şu ki, hepimiz kod kütüphanelerine giderek daha fazla güveniyoruz. Bu elbette iyi bir şey çünkü kodun yeniden kullanımını teşvik ediyor. Ancak projenizin şunu yaptığını varsayalım:

```
require_once __DIR__ . "/../useful/Outputter.php";

class Outputter
{
    // output data
}
```

Şimdi, dahil edilen dosyayı useful/Outputter.php adresinde birleştirdiğinizi varsayalım:

```
class Outputter
{
    //
}
```

Peki, ne olacağını tahmin edebiliyorsun, değil mi? Şu olur:

```
PHP Fatal error: Cannot declare class Outputter because the name is
already in use in /var/popp/src/ch05/batch01/useful/Outputter.php on
line 4
```

Namespace’lerin tanıtılmasından önce, bu soruna geleneksel bir geçici çözüm vardı. Cevap, paket adlarını sınıf adlarının başına eklemekti, böylece sınıf adlarının benzersiz olması garanti altına alındı:

```
// my/Outputer.php
require_once __DIR __ . "/../useful/Outputter.php";

class my_Outputter
{
    // output data
}
```

```
// useful/Outputter.php
class useful_Outputter
{
    //
}
```

Buradaki sorun, daha fazla proje dahil oldukça, sınıf isimlerinin daha da uzamasıydı. Çok büyük bir sorun değildi, ancak kodun okunabilirliğiyle ilgili sorunlara yol açtı ve çalışırken sınıf isimlerini kafanızda tutmayı zorlaştırdı. Kodlama için harcanan zamanın bir kısmı yazım hataları nedeniyle kaybedildi.

Eski kodunuzu koruyorsanız, bu kuralı izleyen kodlar görmeye devam edebilirsiniz. Bu nedenle, bu bölümün ilerleyen kısımlarında paketleri işlemenin eski yöntemine kısaca geri döneceğim.

**Hayat Kurtaran Namespace**

PHP 5.3, namespace’leri tanıttı. Özünde namespace, sınıflarınızı, fonksiyonlarınızı ve değişkenlerinizi yerleştirebileceğiniz bir kovadır. Namespace içinde, bu öğelere nitelik olmadan erişebilirsiniz. Dışarıdan, içerdiği öğelere erişmek için namespace’i içe aktarmalı veya ona başvurmalısınız.

Kafanız mı karıştı? Bir örnek yardımcı olabilir. Burada, namespace kullanarak önceki örneği yeniden yazıyorum:

```
namespace my;
require_once __DIR__ . "/../useful/Outputter.php";

class Outputter
{
    // output data
}
```

```
namespace useful;

class Outputter
{
    //
}
```

namespace anahtar sözcüğüne dikkat edin. Tahmin edebileceğiniz gibi, bu anahtar sözcük bir namespace oluşturur. Bu özelliği kullanıyorsanız, namespace bildirimi, dosyadaki ilk ifade olmalıdır. İki namespace’lerimi oluşturdum: my ve useful. Bununla birlikte, tipik olarak, daha derin namespace’lere sahip olmak isteyeceksiniz. Bir organizasyon veya proje tanımlayıcısı ile başlayacaksınız. PHP, iç içe namespace bildirmenize izin verir. Bunu yapmak için, her seviyeyi bölerken ters eğik çizgi karakteri kullanmanız yeterlidir:

```
namespace popp\ch05\batch04\util;

class Debug
{
    public static function helloWorld(): void
    {
        print "hello from Debug\n";
    }
}
```

Bir repository tanımlamak için genellikle bir ürün veya organizasyonla ilgili bir ad kullanırsınız. Domain’lerimden birini kullanabilirim: örneğin getinstance.com. Bir domain, sahibine özel olduğundan, bu, Java geliştiricilerinin genellikle paket adları için kullandıkları bir numaradır. Domain adlarını en genelden en özele doğru çalışacak şekilde ters çevirirler. Alternatif olarak, bu kitaptaki kod örnekleri için seçtiğim namespace’leri kullanabilirim: kitap adı için popp. Repomu belirledikten sonra, paketleri tanımlamaya devam edebilirim. Bu durumda, bölümü ve ardından numaralandırılmış bir grubu kullanıyorum. Bu, örnek gruplarını ayrı kovalar halinde düzenlememe izin veriyor. Yani bölümün bu noktasında, popp\ch05\batch04'teyim. Son olarak, kodu kategoriye göre de düzenleyebilirim.

Peki metodu nasıl çağırırım? Aslında, çağrıyı nereden yaptığınıza bağlıdır. Metodu namespace içinden çağırıyorsanız, devam edip doğrudan çağırabilirsiniz:

```
Debug::helloWorld();
```

Bu, niteliksiz (unqualified) ad olarak bilinir. Zaten popp\ch05\batch04\util namespace’inde olduğum için, sınıf adının başına herhangi bir yol eklemem gerekmiyor. Sınıfa namespace dışından erişiyor olsaydım, şunu yapabilirdim:

```
\popp\ch05\batch04\Debug::helloworld();
```

Aşağıdaki koddan nasıl bir çıktı alırdım?

```
namespace main;

popp\ch05\batch04\util\Debug::helloworld();
```

Bu hileli bir soru. Aslında, şu benim çıktım:

```
PHP Fatal error: Class 'popp\ch05\batch04\Debug' not found in...
```

Çünkü burada göreceli (relative) bir namespace kullanıyorum. PHP, popp\ch05\batch04\util\Debug için main namespace’inin altına bakıyor ve bulamıyor. Tıpkı bir separatörle başlayarak mutlak (absolute) URL'ler ve dosya yolları oluşturabileceğiniz gibi, namespace’lerde tanımlayabilirsiniz. Örneğin bu sürüm, önceki hatayı düzeltir:

```
namespace main;

\popp\ch05\batch04\util\Debug::helloworld();
```

Bu baştaki ters eğik çizgi, PHP'ye aramaya geçerli namespace’ten değil, kökten başlamasını söyler.

Ancak namespace’lerin kod yazmayı azaltmanıza yardımcı olması gerekmiyor mu? Debug sınıfı bildirimi kesinlikle daha kısadır, ancak bu çağrılar, eski adlandırma kuralında olduğu kadar endişe vericidir. Bunu use anahtar sözcüğü ile çözebilirsiniz. Bu, geçerli namespace içindeki diğer namespace’lere takma ad vermenize olanak tanır. İşte bir örnek:

```
namespace main;

use popp\ch05\batch04\util;

util\Debug::helloWorld();
```

popp\ch05\batch04\util içe aktarılır ve dolaylı olarak util'e takma ad verilir. Başta bir ters eğik çizgi karakteriyle başlamadığıma dikkat edin. Kullanılacak argüman, geçerli namespace’te değil, kök alanda aranır. Bir namespace’e ihtiyacım yoksa, Debug sınıfının kendisini içe aktarabilirim:

```
namespace main;

use popp\ch05\batch04\util\Debug;

Debug::helloWorld();
```

Bu, en sık kullanılan şeklidir. Ancak, çağıran namespace’te zaten bir Debug sınıfım olsaydı ne olurdu?

```
namespace popp\ch05\batch04;

class Debug {
    public static function helloWorld(): void
    {
        print "hello from popp\\ch05\\batch04\\Debug\n";
    }
}
```

Ve burada popp\ch05\batch04 namespace’inden gelen ve her iki Debug sınıfına başvuran kodlar:

```
namespace popp\ch05\batch04;

use popp\ch05\batch04\util\Debug;
use popp\ch05\batch04\Debug;

Debug::helloWorld();
```

Tahmin edebileceğiniz gibi, bu önemli bir hataya neden olur:

```
PHP Fatal error: Cannot use popp\ch05\batch04\Debug as Debug because the
name is already in use in...
```

Bu yüzden, namespace çakışmalarına geri dönerek tam bir daire çizmiş gibiyim. Neyse ki, bu sorunun bir cevabı var. Takma adımı açık hale getirebilirim:

```
namespace popp\ch05\batch04;

use popp\ch05\batch04\util\Debug;
use popp\ch05\batch04\Debug as CoreDebug;

CoreDebug::helloWorld();
```

as ifadesi ile, Debug takma adını coreDebug olarak değiştirebiliyorum.

Bir namespace’e kod yazıyorsanız ve kök (namespace olmayan) alanda bulunan bir sınıf, trait veya arayüze (örneğin, Exception, Error, Closure gibi PHP'nin çekirdek sınıfları) erişmek istiyorsanız, ters eğik çizgi ile adını kullanın. İşte kök uzayda bildirilen bir sınıf:

```
class TreeLister
{
    public static function helloWorld(): void
    {
        print "hello from root namespace\n";
    }
}
```

İşte bazı namespace’li kodlar:

```
namespace popp\ch05\batch04\util;

class TreeLister
{
    public static function helloWorld(): void
    {
        print "hello from " . __NAMESPACE__ . "\n";
    }
}
```

```
namespace popp\ch05\batch04;

use popp\ch05\batch04\util\TreeLister;

TreeLister::helloWorld(); // access local
\TreeLister::helloWorld(); // access from root
```

Namespace’li kod, kendi TreeLister sınıfını bildirir. Yukarıdaki kod, use ifadesiyle tam yolu belirterek local versiyonu kullanır. Ters eğik çizgi ile nitelendirilen bir ad, kök namespace’te benzer şekilde adlandırılmış bir sınıfa erişir.

İşte önceki kodun çıktısı:

```
hello from popp\ch05\batch04\util
hello from root namespace
```

Bu çıktı, `__NAMESPACE__` sabitinin işleyişini gösterdiği için gösterilmeye değer. Bu, mevcut namespace’i verir ve hata ayıklamada kullanışlıdır.

Daha önce gördüğünüz sözdizimini kullanarak aynı dosyada birden fazla namespace bildirebilirsiniz. Ayrıca, namespace anahtar sözcüğüyle parantez kullanan alternatif bir sözdizimi de kullanabilirsiniz:

```
namespace com\getinstance\util {
    class Debug
    {
        public static function helloWorld(): void
        {
            print "hello from Debug\n";
        }
    }
}

namespace other {
    \com\getinstance\util\Debug::helloWorld();
}
```

Birden çok namespace’i aynı dosyada birleştirmeniz gerekiyorsa, önerilen uygulama budur. Ancak genellikle, namespace’leri dosya bazında tanımlamak en iyi uygulama olarak kabul edilir.

**Not**: Parantez ve satır namespace sözdizimlerini aynı dosyada kullanamazsınız. Birini seçmeli ve ona bağlı kalmalısınız.

**Paketleri Simüle Etmek İçin Dosya Sistemini Kullanmak**

Hangi PHP sürümünü kullanırsanız kullanın, sınıfları bir tür paket yapısı sağlayan dosya sistemini kullanarak düzenlemelisiniz. Örneğin, util ve business dizinleri oluşturabilir ve require_once() ifadesiyle sınıf dosyalarını şu şekilde dahil edebilirsiniz:

```
require_once('business/Customer.php');
require_once('util/WebTools.php');
```

Aynı şekilde include_once() fonksiyonunu da kullanabilirsiniz. include() ve require() ifadeleri arasındaki tek fark, hataları ele alma biçimleridir. require() kullanılarak çağrılan bir dosya, bir hatayla karşılaştığınızda tüm sürecinizi çökertir. include() çağrısı sırasında karşılaşılan aynı hata, yalnızca bir uyarı oluşturacak, dahil edilen dosyanın yürütülmesini sonlandıracak ve çağıran kodu devam edecek şekilde bırakacaktır. require() ve require_once() kütüphane dosyalarını dahil etmek için güvenlidir ve include() ve include_once() template oluşturma gibi işlemler için kullanışlıdır.

**Not**: require() ve require_once() aslında ifadedir, fonksiyon değildir. Bu, parantezleri kullanırken atlayabileceğiniz anlamına gelir. Şahsen, ben yine de parantez kullanmayı tercih ederim, ama aynı şeyi yaparsanız, hatanızı açıklamaya hevesli ukalalar tarafından sıkılmaya hazırlıklı olun.

![Dosya sistemi kullanılarak düzenlenen PHP paketleri](/assets/images/2024/03/php-packages-organized-using-the-file-system.png "Dosya sistemi kullanılarak düzenlenen PHP paketleri"){: class="img-fluid"}

PHP söz konusu olduğunda, bu yapı hakkında özel bir şey yoktur. Kütüphanelerinizi farklı dizinlere yerleştirmeniz yeterlidir. Temiz organizasyona katkıda bulunur ve namespace veya bir adlandırma kuralı ile paralel olarak kullanılabilir.

**PEAR Yolunun Adlandırılması**

Namespace’ler tanıtılmadan önce, geliştiriciler sınıf adı çakışmalarını önlemek için kurallara başvurmak zorunda kalıyorlardı. Gördüğümüz gibi bunlardan en yaygın olanı, PEAR geliştiricileri tarafından sağlanan sahte namespace’ti.

Not: PEAR, PHP uzantısını ve uygulama reposunu ifade eder. PHP'nin işlevselliğine katkıda bulunan paketlerin ve araçların resmi olarak tutulan arşividir. Çekirdek PEAR paketleri PHP dağıtımına dahildir ve diğerleri basit bir komut satırı aracı kullanılarak eklenebilir. PEAR paketlerine [http://pear.php.net](http://pear.php.net) adresinden göz atabilirsiniz.

PEAR, tarif ettiğim gibi paketlerini tanımlamak için dosya sistemini kullanır. Namespace’ler tanıtılmadan önce, her sınıf, paket yoluna göre, her dizin adı bir alt çizgi karakteriyle ayrılmış olarak adlandırılıyordu.

Örneğin, PEAR, bir RPC alt paketine sahip olan XML adlı bir paket içerir. RPC paketi, Server.php adlı bir dosya içerir. Server.php içinde tanımlanan sınıf, tahmin edebileceğiniz gibi Server olarak adlandırılmaz. Namespace olmadan, bu er ya da geç PEAR projesinin herhangi bir yerindeki veya bir kullanıcının kodundaki başka bir Server sınıfıyla çakışır. Bunun yerine sınıf, XML_RPC_Server olarak adlandırılır. Bu yaklaşım, çekici olmayan sınıf adları için yapılmıştır. Bununla birlikte, sınıf adı her zaman kendi bağlamını tanımladığı için kodun okunmasını kolaylaştırdı.

**Include Path**

Bileşenlerinizi düzenlerken aklınızda bulundurmanız gereken iki bakış açısı vardır. Dosyaların ve dizinlerin dosya sistemine yerleştirildiği ilk konuyu ele aldım. Ancak bileşenlerin birbirine erişme şeklini de göz önünde bulundurmalısınız. Bu bölümde şu ana kadar include path konusuna değindim.

Bir dosya eklediğinizde, geçerli çalışma dizininden göreli bir yol veya dosya sistemindeki mutlak bir yol kullanarak ona başvurabilirsiniz.

**Not**: Include path’in nasıl çalıştığını ve gerekli dosyalarla ilgili sorunları anlamak önemli olsa da, birçok modern sistemin artık sınıf düzeyinde require deyimlerine dayanmadığını akılda tutmak da önemlidir. Bunun yerine, autoload (otomatik yükleme) ve namespace’in bir kombinasyonunu kullanırlar. Autoload’ı daha sonra ele alacağım ve ardından 15. ve 16. bölümlerde pratik autoload önerilerine ve araçlarına daha ayrıntılı olarak bakacağım.

Şimdiye kadar gördüğünüz örnekler, zaman zaman zorunlu ve gerekli dosyalar arasında sabit bir ilişki belirledi:

```
require_once __DIR__ . "/../useful/Outputter.php";
```

Bu, dosyalar arasındaki ilişkinin hardcoding olması dışında oldukça iyi çalışır. Çağıran sınıfın bulunduğu dizinin yanında her zaman bir useful dizini olmalıdır.

Belki de en kötü yaklaşım, dolambaçlı göreceli yoldur:

```
require_once('../../projectlib/business/User.php');
```

Bu problemlidir çünkü burada belirtilen yol, require_once ifadesini içeren dosyaya göre değil, yapılandırılmış bir çağrı bağlamına (çoğu zaman, ancak her zaman değil, geçerli çalışma dizini) bağlıdır. Bunun gibi dosya yolları kafa karışıklığına yol açar (ve deneyimlerime göre neredeyse her zaman bir sistemin diğer alanlarda da önemli ölçüde iyileştirmeye ihtiyaç duyacağının bir işaretidir).

Elbette mutlak bir yol kullanabilirsiniz:

```
require_once('/home/john/projectlib/business/User.php');
```

Bu, tek bir örnek için işe yarayacaktır - ancak kırılgandır. Yolları bu kadar ayrıntılı belirterek, kütüphane dosyasını belirli bir bağlamda dondurursunuz. Projeyi yeni bir sunucuya her yüklediğinizde, tüm gerekli ifadelerin yeni dosya yolunu hesaba katmak için değiştirilmesi gerekecektir. Bu, kütüphanelerin yerinin değiştirilmesini zorlaştırabilir ve kopyalamadan projeler arasında paylaşılması pratik olmayabilir. Her iki durumda da, tüm ek dizinlerde paket fikrini kaybedersiniz. business paketi mi, yoksa projectlib/business paketi mi?

Dosyaları kodunuza manuel olarak dahil etmeniz gerekiyorsa, en temiz yaklaşım, çağıran kodu kütüphaneden ayırmaktır:

```
require_once('business/User.php');
```

Bunun gibi bir yol kullanan önceki örneklerde, dolaylı olarak göreli yol kullandık. Başka bir deyişle, business/User.php işlevsel olarak ./business/User.php ile aynıdır. Ancak, önceki require ifadesi sistemdeki herhangi bir dizinden çalıştırılabilir? Bunu include path ile yapabilirsiniz. Bu, PHP'nin bir dosya bulmaya çalışırken aradığı dizinlerin listesidir. include_path yönergesini değiştirerek bu listeye ekleyebilirsiniz. include_path genellikle PHP'nin merkezi yapılandırma dosyası php.ini'de ayarlanır. Unix benzeri sistemlerde iki nokta üst üste ve Windows sistemlerinde noktalı virgülle ayrılmış bir dizin listesi tanımlar:

```
include_path = ".:/usr/local/lib/php-libraries"
```

Apache kullanıyorsanız, yapılandırma dosyasında (genellikle httpd.conf olarak adlandırılır) veya dizindeki bir Apache yapılandırma dosyasında (genellikle .htaccess olarak adlandırılır) include_path'i şu sözdizimiyle de ayarlayabilirsiniz:

```
php_value include_path value .:/usr/local/lib/php-libraries
```

**Not**: .htaccess dosyaları, sunucu ortamına çok sınırlı erişim sağlayan bazı hosting şirketlerinin sağladığı web alanında özellikle kullanışlıdır.

Geçerli çalışma dizinine göre var olmayan mutlak olmayan bir yolla fopen() veya require() gibi bir dosya sistemi fonksiyonu kullandığınızda, include path’teki dizinler listedeki ilk dizinden başlayarak otomatik olarak aranır (fopen() durumunda, bu özelliği etkinleştirmek için parametre listesine flag eklemelisiniz). Hedef dosya ile karşılaşıldığında arama sona erer ve fonksiyon görevini tamamlar.

require() ifadelerinizde yalnızca paketlere ve dosyalara başvurmanız gerekir.

Kendi kütüphanenizi kullanabilmeniz için include_path'e bir dizin eklemeniz gerekebilir. Bunu yapmak için php.ini dosyasını düzenleyebilirsiniz (Düzenledikten sonra sunucunuzu yeniden başlatmanız gerektiğini unutmayın).

php.ini dosyasıyla çalışmak için gerekli ayrıcalıklara sahip değilseniz, set_include_path() fonksiyonunu kullanarak kodunuzda include path’i ayarlayabilirsiniz. set_include_path() include path’i (php.ini'de göründüğü gibi) kabul eder ve yalnızca geçerli proses için include_path ayarını değiştirir. php.ini dosyası muhtemelen include_path için kullanışlı bir değer tanımlıyor, yani üzerine yazmak yerine get_include_path() fonksiyonunu kullanarak ona erişebilir ve kendi dizininizi ekleyebilirsiniz. Geçerli include path’e nasıl dizin ekleyebileceğiniz aşağıda açıklanmıştır:

```
set_include_path(get_include_path() . PATH_SEPARATOR . "/home/john/phplib/");
```

PATH_SEPARATOR sabiti, Unix sisteminde iki nokta üst üste ve Windows platformunda noktalı virgül olarak çözümlenecektir. Taşınabilirlik nedeniyle, kullanımı en iyi uygulama olarak kabul edilir.

### Autoload (Otomatik Yükleme)

require_once'ı include path ile birlikte kullanmak zarif olsa da, birçok geliştirici require ifadelerini ortadan kaldırıyor ve bunun yerine autoload’a güveniyor. 

**Not**: Bu kitabın önceki baskılarında, bu bölümde tartışılan işlevselliğin basit bir versiyonunu sağlayan __autoload() adlı yerleşik bir fonksiyon ele alınmıştır. Bu işlev php 7.2.0'den itibaren kullanımdan kaldırılmıştır ve php 8'de tamamen kaldırılmıştır.

Bunu yapmak için, sınıflarınızı her biri kendi dosyasında yer alacak şekilde düzenlemelisiniz. Her sınıf dosyası, içerdiği sınıfın adıyla ilişki olmalıdır, bu nedenle ShopProduct.php adlı bir dosyada, sınıfın namespace’teki öğelere karşılık gelen dizinlerle bir ShopProduct sınıfı tanımlayabilirsiniz.

PHP 5, sınıf dosyalarının dahil edilmesini otomatikleştirmeye yardımcı olmak için autoload işlevini tanıttı. Varsayılan destek oldukça basit ama yine de kullanışlı. Parametre içermeyen spl_autoload_register() fonksiyonu çağrılarak etkinleştirilebilir. Ardından, autoload işlevi bu şekilde etkinleştirildiyse, bilinmeyen bir sınıfı başlatmaya çalıştığınızda,
PHP, spl_autoload() adlı yerleşik fonksiyonu çağırır. Bu, `<classname>.php` veya `<classname>.inc` adlı dosyalar için (burada `<classname>` bilinmeyen sınıfın adıdır) include path’i ararken sağlanan sınıf adını (küçük harfe dönüştürülür) kullanır.

İşte basit bir örnek:

```
spl_autoload_register();
$writer = new Writer();
```

Henüz Writer nesnesi içeren bir dosya eklemediğimi varsayarsak, bu örnekleme başarısız olmaya mahkûm görünüyor. Bununla birlikte PHP, autoload’ı ayarladığım için, write.php veya write.inc adlı bir dosyayı eklemeye çalışacak ve ardından örneklemeyi ikinci kez deneyecek. Bu dosyalardan biri varsa ve Writer adlı bir sınıf içeriyorsa, o zaman her şey yolunda olacaktır.

Bu varsayılan davranış, namespace’leri destekler:

```
spl_autoload_register();
$writer = new util\Writer();
```

Önceki kod, util adlı dizinde write.php (küçük harfli isme dikkat edin) adlı bir dosya bulacaktır.

Sınıf dosyalarımı büyük/küçük harfe bağlı olarak adlandırırsam ne olur? Yani, onları büyük harfle adlandırırsam ne olur? Writer sınıfını Writer.php adlı bir dosyaya yerleştirmiş olsaydım, varsayılan uygulama onu bulamazdı.

Neyse ki, farklı kuralları işlemek için kendi özel fonksiyonumu kaydedebilirim. Bunu yapabilmek için spl_autoload_register()'a özel bir fonksiyon başvurusu iletmeliyim. Autoload fonksiyonum tek bir parametre gerektirmelidir. Daha sonra, PHP bilinmeyen bir sınıfı başlatma girişimiyle karşılaşırsa, bu fonksiyonu çağırır ve ona bilinmeyen sınıf adını bir dize olarak iletir. Kayıp sınıf dosyasını bulmak ve ardından dahil etmek için bir strateji tanımlamak autoload fonksiyonuna bağlıdır. Autoload çağrıldığında, PHP sınıfı bir kez daha başlatmaya çalışır.

İşte yüklenecek bir sınıfla birlikte basit bir autoload fonksiyonu:

```
class Blah
{
    public function wave(): void
    {
        print "saying hi from root";
    }
}
```

```
$basic = function (string $classname) {
    $file = __DIR__ . "/" . "{$classname}.php";
    if (file_exists($file)) {
        require_once($file);
    }
};

\spl_autoload_register($basic);
$blah = new Blah();
$blah->wave();
```

Başlangıçta Blah'ı başlatmayı başaramayan PHP, spl_autoload_register() fonksiyonuyla autoload fonksiyonu kaydettiğimi görecek ve ona "Blah" dizesini iletecek. Benim uygulamam basitçe Blah.php dosyasını dahil etmeye çalışır. Bu, elbette, yalnızca dosya, autoload fonksiyonunun bildirildiği dosyayla aynı dizindeyse çalışır. Gerçek dünyada, include path yapılandırmasını autoload mantığımla birleştirmem gerekir (bu tam olarak Composer'ın autoload uygulamasının yaptığı şeydir).

Old school desteği sağlamak istersem, PEAR paketi şunları içerir:

```
class util_Blah
{
    public function wave(): void
    {
        print "saying hi from underscore file";
    }
}
```

```
$underscores = function (string $classname) {
    $path = str_replace('_', DIRECTORY_SEPARATOR, $classname);
    $path = __DIR__ . "/$path";
    if (file_exists("{$path}.php")) {
        require_once("{$path}.php");
    }
};

\spl_autoload_register($underscores);
$blah = new util_Blah();
$blah->wave();
```

Gördüğünüz gibi, autoload fonksiyonu verilen $classname içindeki alt çizgileri eşleştirir ve her birini DIRECTORY_SEPARATOR karakteriyle (Unix sistemlerinde /) değiştirir. Sınıf dosyasını (util/Blah.php) dahil etmeye çalışıyorum. Sınıf dosyası varsa ve içerdiği sınıf doğru bir şekilde adlandırılmışsa, nesne hatasız olarak başlatılmalıdır. Elbette bu, yazılımcının  paketleri böldüğü durumlar dışında, bir sınıf adında alt çizgi karakterini yasaklayan bir adlandırma kuralına uymasını gerektirir.

Peki ya namespace’ler? Varsayılan autoload fonksiyonunun namespace’leri desteklediğini gördük. Ancak bu varsayılanı geçersiz kılarsak, namespace desteği sağlamak bize düşer. Bu sadece ters eğik çizgi karakterlerini eşleştirme ve değiştirme meselesidir:

```
namespace util;

class LocalPath
{
    public function wave(): void
    {
        print "hello from " . get_class();
    }
}
```

```
$namespaces = function (string $path) {
    if (preg_match('/\\\\/', $path)) {
        $path = str_replace('\\', DIRECTORY_SEPARATOR, $path);
    }
    if (file_exists("{$path}.php")) {
        require_once("{$path}.php");
    }
};

\spl_autoload_register($namespaces);
$obj = new util\LocalPath();
$obj->wave();
```

Autoload fonksiyonuna iletilen değer, başında ters eğik çizgi olmadan her zaman tam nitelikli bir ada normalleştirilir, bu nedenle örnekleme noktasında takma ad veya göreli ad alanları hakkında endişelenmenize gerek yoktur.

Bu çözümün mükemmel olmadığını unutmayın. file_exists() fonksiyonu, include path’i hesaba katmaz, bu nedenle, require_once'ın mükemmel çalışacağı tüm koşulları doğru bir şekilde yansıtmaz. Bunun çeşitli çözümleri var. Kendi path’e duyarlı file_exists() sürümünüzü kullanabilir veya dosyayı try ifadesi ile kullanabilirsiniz (bu durumda Error ‘u yakalayabilirsiniz, Exception'ı değil). Neyse ki PHP, stream_resolve_include_path() fonksiyonunu sağlar. Bu, verilen path’in mutlak dosya adını temsil eden bir dize döndürür veya bizim amaçlarımız için çok önemli bir şekilde, dosya include path’te bulunamazsa false döndürür.

```
$namespaces = function (string $path) {
    if (preg_match('/\\\\/', $path)) {
        $path = str_replace('\\', DIRECTORY_SEPARATOR, $path);
    }
    if (\stream_resolve_include_path("{$path}.php") !== false) {
        require_once("{$path}.php");
    }
};

\spl_autoload_register($namespaces);
$obj = new util\LocalPath();
$obj->wave();
```

Hem PEAR tarzı sınıf adlarını hem de namespace’leri desteklemek istersem ne olur? Autoload uygulamalarımı tek bir fonksiyonda birleştirebilirim. Veya spl_autoload_register()‘ın autoload fonksiyonlarını istiflediği gerçeğini kullanabilirim:

```
$underscores = function (string $classname) {
    $path = str_replace('_', DIRECTORY_SEPARATOR, $classname);
    $path = __DIR__ . "/$path";

    if (\stream_resolve_include_path("{$path}.php") !== false) {
        require_once("{$path}.php");
    }
};

$namespaces = function (string $path) {
    if (preg_match('/\\\\/', $path)) {
        $path = str_replace('\\', DIRECTORY_SEPARATOR, $path);
    }
    if (\stream_resolve_include_path("{$path}.php") !== false) {
        require_once("{$path}.php");
    }
};

\spl_autoload_register($namespaces);
\spl_autoload_register($underscores);
$blah = new util_Blah();
$blah->wave();
$obj = new util\LocalPath();
$obj->wave();
```

Bilinmeyen bir sınıfla karşılaştığında, PHP motoru sırayla (kaydedilme sırasına göre) autoload fonksiyonlarını çağırır, başlatma mümkün olduğunda veya tüm seçenekler tükendiğinde durur.

Açıkça bu şekilde istiflemenin bir yükü vardır, peki PHP bunu neden destekliyor? Gerçek dünyadaki bir projede, muhtemelen namespace ve alt çizgi stratejilerini tek bir fonksiyonda birleştirirsiniz. Ancak, büyük sistemlerdeki ve kütüphanelerdeki bileşenlerin kendi autoload mekanizmalarını kaydetmesi gerekebilir. Yığınlama (stacking), bir sistemin birden çok bölümünün autoload stratejilerini birbirinin üzerine yazmadan bağımsız olarak kaydetmesine olanak tanır. Aslında, autoload mekanizmasına yalnızca kısa bir süre ihtiyaç duyan bir kütüphane, autoload fonksiyonunun adını spl_autoload_unregister()'a ileterek kendi arkasını temizleyebilir!

## Sınıf ve Nesne Fonksiyonları

PHP, sınıfları ve nesneleri test etmek için güçlü bir dizi fonksiyon sağlar. Peki bu neden yararlıdır? Ne de olsa, scriptinizde kullandığınız sınıfların çoğunu muhtemelen siz yazdınız.

Aslında, çalışma zamanında kullandığınız sınıflar hakkında her zaman bilgi sahibi olmazsınız. Örneğin, third-party sınıflarla şeffaf bir şekilde çalışacak bir sistem tasarlamış olabilirsiniz. Bu durumda, tipik olarak yalnızca sınıf adı verilen bir nesneyi başlatırsınız. PHP, sınıflara dinamik olarak başvurmak için dizeleri kullanmanıza izin verir, bunun gibi:

```
namespace tasks;

class Task
{
    public function doSpeak()
    {
        print "hello\n";
    }
}

$classname = "Task";
require_once("tasks/{$classname}.php");
$classname = "tasks\\$classname";
$myObj = new $classname();
$myObj->doSpeak();
```

Bu script, $classname öğesine atadığım dizeyi yapılandırma dosyasından veya bir web isteğini bir dizinin içeriğiyle karşılaştırarak alabilir. Daha sonra dizeyi bir sınıf dosyası yüklemek ve bir nesneyi başlatmak için kullanabilirsiniz. Burada bir namespace niteliği oluşturduğuma dikkat edin.

Tipik olarak, sisteminizin kullanıcı tarafından oluşturulan eklentileri çalıştırabilmesini istediğinizde buna benzer bir şey yaparsınız. Gerçek bir projede bu kadar riskli bir şey yapmadan önce, sınıfın var olup olmadığını, beklediğiniz metotlara sahip olup olmadığını vb. kontrol etmeniz gerekir.

**Not**: Güvenlik önlemi almış olsanız bile, third-party eklenti kodunu dinamik olarak yükleme konusunda son derece dikkatli olmalısınız. Kullanıcılar tarafından yüklenen kodu asla otomatik olarak çalıştırmamalısınız. Bu şekilde yüklenen herhangi bir eklenti, genellikle çekirdek kodunuzla aynı ayrıcalıklarla yürütülür, bu nedenle kötü niyetli bir eklenti sisteminize zarar verebilir.

Bu, eklentilerin iyi bir fikir olmadığı anlamına gelmez. Third-party geliştiricilerin çekirdek sistemi geliştirmesine izin vermek, büyük bir esneklik sunabilir. Daha fazla güvenlik sağlamak için eklentilere özel bir dizini destekleyebilirsiniz, ancak kod dosyalarının sistem yöneticisi tarafından doğrudan veya parola korumalı bir yönetim ortamından yüklenmesini isteyebilirsiniz. Yönetici, kurulumdan önce eklenti kodunu kontrol eder veya eklentileri güvenilir bir repodan alır.

Bazı sınıf fonksiyonlarının yerini, bu bölümde daha sonra inceleyeceğim daha güçlü olan Reflection API aldı. Bununla birlikte, basitlikleri ve kullanım kolaylıkları, onları bazı durumlarda ilk bağlantı noktası haline getirir.

### Sınıfları Aramak

class_exists() fonksiyonu, denetlenecek sınıfı temsil eden bir dizeyi kabul eder ve sınıf mevcut ise true, aksi takdirde false döndürür.

Bu fonksiyonu kullanarak önceki kod parçasını biraz daha güvenli hale getirebilirim:

```
$base = __DIR__;
$classname = "Task";
$path = "{$base}/tasks/{$classname}.php";

if (!file_exists($path)) {
    throw new \Exception("No such file as {$path}");
}

require_once($path);
$qclassname = "tasks\\$classname";

if (!class_exists($qclassname)) {
    throw new Exception("No such class as $qclassname");
}

$myObj = new $qclassname();
$myObj->doSpeak();
```

Tabii ki, söz konusu sınıfın kurucu parametrelere ihtiyaç duymadığından emin olamazsınız. Bu güvenlik düzeyi için, bölümün ilerleyen kısımlarında ele alınan Reflection API'ye başvurmanız gerekir. Bununla birlikte, class_exists(), onunla çalışmadan önce sınıfın var olup olmadığını kontrol etmenize izin verir.

Ayrıca get_declared_classes() fonksiyonunu kullanarak kodunuzda tanımlanan tüm sınıfların bir dizisini elde edebilirsiniz:

```
print_r(get_declared_classes());
```

Bu, kullanıcı tanımlı ve yerleşik sınıfları listeler. Yalnızca fonksiyon çağrısı sırasında bildirilen sınıfları döndürdüğünü unutmayın. Daha sonra require() veya require_once() komutunu çalıştırabilir ve böylece kodunuzdaki sınıf sayısını artırabilirsiniz.

### Nesne veya Sınıf Hakkında Bilgi Almak

Bildiğiniz gibi, sınıf tipi ipuçlarını kullanarak metot parametrelerinin nesne türlerini sınırlayabilirsiniz. Bu araçla bile, bir nesnenin türünden her zaman emin olamazsınız.

Bir nesnenin türünü kontrol etmek için kullanılabilecek bir dizi temel araç vardır. Öncelikle get_class() fonksiyonu ile bir nesnenin sınıfını kontrol edebilirsiniz. Bu, herhangi bir nesneyi parametre olarak kabul eder ve sınıf adını bir dize olarak döndürür:

```
$product = self::getProduct();

if (get_class($product) === 'popp\ch05\batch05\CdProduct') {
    print "\$product is a CdProduct object\n";
}
```

Bu kod parçasında, getProduct() fonksiyonundan bir şey alıyorum. Bunun bir CdProduct nesnesi olduğundan kesinlikle emin olmak için get_class() metodunu kullanıyorum.

İşte getProduct() fonksiyonu:

```
public static function getProduct()
{
    return new CdProduct(
        "Exile on Coldharbour Lane",
        "The",
        "Alabama 3",
        10.99,
        60.33
    );
}
```

getProduct() basitçe bir CdProduct nesnesi başlatır ve döndürür. Bu bölümde bu fonksiyonu iyi bir şekilde kullanacağım.

get_class() fonksiyonu çok özel bir araçtır. Genellikle bir sınıfın türü hakkında daha genel bir onay istersiniz. Bir nesnenin ShopProduct ailesine ait olduğunu bilmek isteyebilirsiniz, ancak asıl sınıfının BookProduct veya CdProduct olup olmadığı umurunuzda değildir. Bu amaçla PHP, instanceof operatörünü sağlar.

**Not**: PHP 4, instanceof'u desteklemiyordu. Bunun yerine, PHP 5.0'da kullanımdan kaldırılan ancak PHP 5.3 ile tekrar yüklenen is_a() fonksiyonunu sağladı.

instanceof operatörü, anahtar kelimenin solunda test edilecek nesne ve sağında sınıf veya arayüz adı olmak üzere iki işlenenle çalışır. Nesne verilen türün bir örneğiyse, true olarak çözümlenir:

```
$product = self::getProduct();

if ($product instanceof \popp\ch05\batch05\CdProduct) {
    print "\$product is an instance of CdProduct\n";
}
```

### Sınıfın Tam Nitelikli Dize Referansını Almak

Namespace’ler, nesne yönelimli PHP ile ilgili çirkin olan birçok şeyi temizledi. Artık gülünç derecede uzun sınıf adlarına veya isim çakışmalarına (eski kod bir yana) katlanmamız gerekmiyor. Öte yandan, takma adla ve göreli ad alanı referanslarıyla, bazı sınıf yollarını tam olarak nitelenmiş olacak şekilde çözümlemek angarya olabilir.

Çözülmesi zor sınıf adlarına bazı örnekler:

```
namespace mypackage;

use util as u;
use util\db\Querier as q;

class Local
{
}

// Resolve these:
// Aliased namespace
// u\Writer;
// Aliased class
// q;
// Class referenced in local context
// Local
```

Bu sınıf referanslarının nasıl çözüldüğünü anlamak çok zor değil, ancak her olasılığı yakalamak için kod yazmak acı verici olurdu. Örneğin, u\Writer verildiğinde, otomatik bir çözümleyicinin util'in takma adı olduğunu ve kendi başına bir namespace olmadığını bilmesi gerekir. PHP 5.5, ClassName::class sözdizimini tanıttı. Başka bir deyişle, bir sınıf referansı verildiğinde, tam nitelikli sınıf adını elde etmek için kapsam çözümleme operatörü ve class anahtar sözcüğünü ekleyebilirsiniz:

```
print u\Writer::class . "\n";
print q::class . "\n";
print Local::class . "\n";
```

Önceki kod parçası şunu verir:

```
util\Writer
util\db\Querier
mypackage\Local
```

PHP 8'den itibaren, bir nesne üzerinde ::class'ı çağırabilirsiniz. Örneğin, bir ShopProduct örneği verildiğinde, tam sınıf adını şu şekilde alabilirim:

```
$bookp = new BookProduct(
    "Catch 22",
    "Joseph",
    "Heller",
    11.99,
    300
);

print $bookp::class;
// popp\ch04\batch02\BookProduct
```

Bu kullanışlı sözdiziminin yeni bir işlevsellik sağlamadığını unutmayın; aynı sonucu veren get_class() fonksiyonuyla zaten karşılaştınız.

### Metodlar Hakkında Bilgi Almak

get_class_methods() fonksiyonunu kullanarak bir sınıftaki tüm metotların listesini elde edebilirsiniz. Bu, bir sınıf adı gerektirir ve sınıftaki tüm metotların adlarını içeren bir dizi döndürür:

```
print_r(get_class_methods('\\popp\\ch04\\batch02\\BookProduct'));
```

BookProduct sınıfının var olduğunu varsayarsak, şöyle bir şey görebilirsiniz:

```
Array
(
    [0] => __construct
    [1] => getNumberOfPages
    [2] => getSummaryLine
    [3] => getPrice
    [4] => setID
    [5] => getProducerFirstName
    [6] => getProducerMainName
    [7] => setDiscount
    [8] => getDiscount
    [9] => getTitle
    [10] => getProducer
    [11] => getInstance
)
```

Örnekte, sınıf adını içeren bir dizeyi get_class_methods() fonksiyonuna iletiyorum ve döndürülen diziyi print_r() fonksiyonu ile yazdırıyorum. Alternatif olarak, get_class_methods()'a bir nesne iletebilirdim. Döndürülen listeye yalnızca public metotların adları dahil edilecektir.

Gördüğünüz gibi, bir metot adını değişkende saklayabilir ve onu bir nesneyle birlikte dinamik olarak çağırabilirsiniz, bunun gibi:

```
$product = self::getProduct();
$method = "getTitle"; // define a method name
print $product->$method(); // invoke the method
```

Tabii ki, bu tehlikeli olabilir. Metot yoksa ne olur? Tahmin edebileceğiniz gibi, kodunuz hata vererek başarısız olacaktır. Bir metodun var olduğunu test etmenin yolu ile zaten karşılaştınız:

```
if (in_array($method, get_class_methods($product))) {
    print $product->$method(); // invoke the method
}
```

Çağırmadan önce get_class_methods() tarafından döndürülen dizide metot adının var olup olmadığını kontrol ediyorum.

PHP bu amaç için daha özel araçlar sağlar. Metot adlarını iki fonksiyon ile kontrol edebilirsiniz: is_callable() ve method_exists(). is_callable(), iki fonksiyondan daha gelişmiş olanıdır. Fonksiyon adını temsil eden bir dize değişkenini ilk parametre olarak kabul eder ve fonksiyon varsa ve çağrılabiliyorsa true değerini döndürür. Aynı testi bir metoda uygulamak için, fonksiyon adı yerine bir dizi iletmelisiniz. Dizi, ilk öğesi olarak bir nesne veya sınıf adı ve ikinci öğesi olarak kontrol edilecek metot adını içermelidir. Metot sınıfta mevcutsa, fonksiyon true değerini döndürür:

```
if (is_callable([$product, $method])) {
    print $product->$method(); // invoke the method
}
```

method_exists() fonksiyonu, bir nesne (veya bir sınıf adı) ve bir metot adı gerektirir ve verilen metot, nesnenin sınıfında varsa true değerini döndürür:

```
if (method_exists($product, $method)) {
    print $product->$method(); // invoke the method
}
```

**Dikkat**: Bir metodun var olmasının, onun çağrılabilir olacağı anlamına gelmediğini unutmayın. method_exists(), private ve protected metotların yanı sıra public metotlar için de true değerini döndürür.

### Property’ler Hakkında Bilgi Almak

Bir sınıfın metotlarını sorgulayabildiğiniz gibi property’lerini de sorgulayabilirsiniz. get_class_vars() fonksiyonu, sınıf adı gerektirir ve bir ilişkisel dizi döndürür. Döndürülen dizi, anahtarları olarak property adlarını ve değerleri olarak property değerlerini içerir. Bu testi CdProduct nesnesine uygulayalım. Açıklama amacıyla, CdProduct::$coverUrl sınıfına bir public property ekliyoruz:

```
print_r(get_class_vars('\\popp\\ch05\\batch05\\CdProduct'));

Array (
    [coverUrl] => cover url
)
```

### Kalıtım Hakkında Bilgi Almak

Sınıf fonksiyonları aynı zamanda kalıtım ilişkilerinin grafiğini çıkarmamıza da izin verir. Bir sınıfın ebeveynini örneğin get_parent_class() ile bulabiliriz. Bu fonksiyon, bir nesne veya sınıf adı gerektirir ve varsa, üst sınıfın adını döndürür. Böyle bir sınıf yoksa, yani test ettiğimiz sınıfın bir üst öğesi yoksa, fonksiyon false değerini döndürür.

```
print get_parent_class('\\popp\\ch04\\batch02\\BookProduct');
```

Tahmin edebileceğiniz gibi bu, üst sınıfı verir: ShopProduct.

is_subclass_of() fonksiyonunu kullanarak bir sınıfın diğerinin alt sınıfı olup olmadığını da test edebiliriz.  Bu, bir alt nesne (veya bir sınıfın adı) ve üst sınıfın adını gerektirir. İkinci parametre birinci parametrenin üst sınıfıysa, fonksiyon true değerini döndürür:

```
$product = self::getBookProduct(); // acquire an object

if (is_subclass_of($product, '\\popp\\ch04\\batch02\\ShopProduct')) {
    print "BookProduct is a subclass of ShopProduct\n";
}
```

is_subclass_of() size yalnızca sınıf kalıtım ilişkileri hakkında bilgi verecektir. Size sınıfın bir arayüz uyguladığını söylemez. Bunun için instanceof operatörünü kullanmalısınız. Veya SPL'nin (Standard PHP Library) parçası olan bir fonksiyonu kullanabilirsiniz. class_implements(), sınıf adını veya nesne referansını kabul eder ve arayüz adları dizisi döndürür:

```
if (in_array('someInterface', class_implements($product))) {
    print "BookProduct is an interface of someInterface\n";
}
```

### Metot Çağırmak

Bir metodu dinamik olarak çağırmak için dize kullandığım bir örnekle zaten karşılaştınız:

```
$product = self::getProduct();
$method = "getTitle"; // define a method name
print $product->$method(); // invoke the method
```

PHP aynı sonuca ulaşmak için call_user_func() metodunu da sağlar. call_user_func() her türlü callable çağırabilir (fonksiyon adı veya anonim fonksiyon gibi). Burada, fonksiyon adını bir dizede ileterek fonksiyon çağırıyorum:

```
$returnVal = call_user_func("myFunction");
```

Bir metodu çağırmak için bir diziyi iletebilirim. Bunun ilk öğesi bir nesne olmalı ve ikincisi çağrılacak metodun adı olmalıdır:

```
$returnVal = call_user_func([$myObj, "methodName"]);
```

call_user_func() fonksiyonuna iletilen diğer tüm parametreler, hedef fonksiyonun veya metodun parametreleri olarak ele alınacak ve şu şekilde aynı sırada iletilecektir:

```
$product = self::getBookProduct(); // Acquire a BookProduct object
call_user_func([$product, 'setDiscount'], 20);
```

Bu dinamik çağrı elbette şuna eşdeğerdir:

```
$product->setDiscount(20);
```

call_user_func() fonksiyonu hayatınızı büyük ölçüde değiştirmeyecek çünkü metot adı yerine doğrudan bir dize kullanabilirsiniz, bunun gibi:

```
$method = "setDiscount";
$product->$method(20);
```

Bununla birlikte, ilgili call_user_func_array() fonksiyonu çok daha etkileyicidir. Bu, hedef metot veya fonksiyonun seçilmesi söz konusu olduğunda call_user_func() ile aynı şekilde çalışır. En önemlisi, hedef metodun gerektirdiği tüm parametreleri bir dizi olarak kabul eder.

**Dikkat**: call_user_func() kullanılarak bir fonksiyon veya metoda iletilen parametreler referans olarak iletilmez.

Peki bu neden yararlıdır? Nadiren, size dizi biçiminde argümanlar verilir. Karşılaştığınız argümanların sayısını önceden bilmiyorsanız, onları başkalarına aktarmak zor olabilir. Bölüm 4'te, delegator sınıfları oluşturmak için kullanılabilecek interceptor metotları inceledik. İşte __call() metodunun basit bir örneği:

```
public function __call(string $method, array $args): mixed
{
    if (method_exists($this->thirdpartyShop, $method)) {
        return $this->thirdpartyShop->$method();
    }
}
```

Gördüğünüz gibi, yazılımcı tarafından tanımsız bir metot çağrıldığında __call() metodu çağrılır. Bu örnekte, $thirdpartyShop adlı property’de bir nesne tutuyorum. Saklanan nesnede $method parametresi ile eşleşen bir metot bulursam, onu çağırırım. Hedef metodun herhangi bir argüman gerektirmediğini varsayıyorum, bu da benim sorunlarımın başladığı yer. __call() metodunu yazarken, çağrıdan çağrıya $args dizisinin ne kadar büyük olabileceğini söylememin hiçbir yolu yok. $args'ı doğrudan delegate metoda iletirsem, beklediği argümanları değil, tek bir dizi argümanı iletirim. call_user_func_array() sorunu mükemmel şekilde çözer:

```
public function __call(string $method, array $args): mixed
{
    if (method_exists($this->thirdpartyShop, $method)) {
        return call_user_func_array(
            [
                $this->thirdpartyShop,
                $method
            ],
            $args
        );
    }
}
```

## Reflection API

Java için java.lang.reflect paketi ne ise, PHP'nin Reflection API'si de PHP için odur. Property, metot ve sınıfları analiz etmek için yerleşik sınıflardan oluşur. Bazı açılardan get_class_vars() gibi mevcut nesne fonksiyonlarına benzer, ancak daha esnektir ve çok daha fazla ayrıntı sağlar. Ayrıca PHP'nin erişim denetimi, arayüzler ve soyut sınıflar gibi nesne yönelimli özellikleriyle, daha eski, daha sınırlı sınıf fonksiyonlarının olmadığı bir şekilde çalışmak üzere tasarlanmıştır.

### Giriş

Reflection API, sınıflardan daha fazlasını incelemek için kullanılabilir. Örneğin, ReflectionFunction sınıfı, belirli bir fonksiyon hakkında bilgi sağlar ve ReflectionExtension, dilde derlenen bir uzantı hakkında fikir verir. Tablo 5-1, API'deki bazı sınıfları listeler.

**Tablo 5-1**. Reflection API'deki anahtar sınıflar

| Sınıf | Açıklama |
| --- | --- |
| Reflection | Sınıf bilgilerini özetlemek için static export() metodunu sağlar |
| ReflectionAttribute | Sınıflar, property’ler, sabitler veya parametreler hakkında bağlamsal bilgiler |
| ReflectionClass | Sınıf bilgileri ve araçları |
| ReflectionClassConstant | Sabit hakkında bilgi |
| ReflectionException | Hata sınıfı |
| ReflectionExtension | PHP uzantı bilgisi |
| ReflectionFunction | Fonksiyon bilgileri ve araçları |
| ReflectionGenerator | Generator hakkında bilgi |
| ReflectionMethod | Sınıf metot bilgileri ve araçları |
| ReflectionNamedType | Fonksiyon veya metodun dönüş tipi hakkında bilgi |
| ReflectionObject | Nesne bilgileri ve araçları (ReflectionClass'tan miras alınır) |
| ReflectionParameter | Metot parametre bilgileri |
| ReflectionProperty | Sınıf property bilgileri |
| ReflectionType | Fonksiyon veya metodun dönüş tipi hakkında bilgi |
| ReflectionUnionType | Union tip bildirimi için ReflectionType nesnelerinin koleksiyonu |
| ReflectionZendExtension | PHP Zend uzantısı bilgileri |

Bunların arasında, Reflection API'deki sınıflar, komut dosyalarınız daki nesneler, fonksiyonlar ve uzantılar hakkındaki bilgilere benzeri görülmemiş bir çalışma zamanı erişimi sağlar.

Reflection API'nin gücü ve erişimi, onu genellikle sınıf ve nesne fonksiyonlarına tercih ederek kullanmanız gerektiği anlamına gelir. Yakında sınıfları test etmek için vazgeçilmez bir araç olduğunu göreceksiniz. Örneğin class diyagramları veya dökümantasyon oluşturmak isteyebilirsiniz veya field adlarını çıkarmak için nesnenin accessor (getter ve setter) metotlarını inceleyerek nesne bilgilerini veritabanına kaydetmek isteyebilirsiniz. Şemaya göre modül sınıflarındaki metotları çağıran bir framework oluşturmak, Reflection'ın başka bir kullanımıdır.

### Kolları Sıvamanın Zamanı

Sınıfların niteliklerini incelemek için bazı fonksiyonlarla zaten karşılaştınız. Bunlar faydalıdır ancak çoğu zaman sınırlıdır. İşte işe uygun bir araç. ReflectionClass, ister kullanıcı tanımlı ister dahili bir sınıf olsun, sınıf ile ilgili tüm bilgileri ortaya çıkaran metotlar sağlar. ReflectionClass'ın kurucusu, bir sınıf veya arayüz adını (veya bir nesne örneğini) tek parametre olarak kabul eder:

```
$prodclass = new \ReflectionClass(CdProduct::class);
print $prodclass;
```

ReflectionClass nesnesi oluşturduğunuzda, sınıfa ilişkin her türlü bilgiyi dize olarak aktarabilirsiniz. ShopProduct için ReflectionClass örneğimi yazdırdığımda oluşturulan çıktının kısaltılmış özeti:

```
Class [ <user> class popp\ch04\batch02\CdProduct extends popp\ch04\batch02\ShopProduct ] {
    @@ /var/popp/src/ch04/batch02/CdProduct.php 6-37
    - Constants [2] {
        Constant [ public int AVAILABLE ] { 0 }
        Constant [ public int OUT_OF_STOCK ] { 1 }
}
    - Static properties [0] {
}
- Static methods [1] {
    Method [ <user, inherits popp\ch04\batch02\ShopProduct> static public method getInstance ] {
    @@ /var/popp/src/ch04/batch02/ShopProduct.php 93 - 130
    - Parameters [2] {
        Parameter #0 [ <required> int $id ]
        Parameter #1 [ <required> PDO $pdo ]
    }
    - Return [ popp\ch04\batch02\ShopProduct ]
    }
}
- Properties [3] {
    Property [ private $playLength = 0 ]
    Property [ public $status = NULL ]
    Property [ protected int|float $price ]
}
...
```

**Not**: Yardımcı metot olan Reflection::export(), bir zamanlar ReflectionClass bilgilerini aktarmanın standart yoluydu. Bu, PHP 7.4'te kullanımdan kaldırıldı ve PHP 8.0'da tamamen kaldırıldı.

Gördüğünüz gibi ReflectionClass, bir sınıfla ilgili bilgilere olağanüstü erişim sağlar. Dize çıktısı, property ve metotların erişim kontrolü durumu, her metodun gerektirdiği parametreler ve her metodun script dosyasındaki konumu dahil olmak üzere CdProduct'ın hemen hemen her yönü hakkında özet bilgi sağlar. Bunu daha yerleşik bir hata ayıklama fonksiyonuyla karşılaştırın. var_dump() fonksiyonu, verileri özetlemeye yönelik genel amaçlı bir araçtır. Özet bilgi alabilmeniz için önce nesneyi başlatmanız gerekir ve o zaman bile ReflectionClass tarafından sağlanan kadar ayrıntı vermez:

```
$cd = new CdProduct("cd1", "bob", "bobbleson", 4, 50);
var_dump($cd);

Here's the output:
object(popp\ch04\batch02\CdProduct)#15 (8) {
["playLength":"popp\ch04\batch02\CdProduct":private]=>int(50)
["status"]=>NULL
["title":"popp\ch04\batch02\ShopProduct":private]=>string(3) "cd1"
["producerMainName":"popp\ch04\batch02\ShopProduct":private]=>string(9) "bobbleson"
["producerFirstName":"popp\ch04\batch02\ShopProduct":private]=>string(3) "bob"
["price":protected]=>float(4)
["discount":"popp\ch04\batch02\ShopProduct":private]=>int(0)
["id":"popp\ch04\batch02\ShopProduct":private]=>int(0)
}
```

var_dump() ve onun kuzeni print_r(), verileri açığa çıkarmak için son derece kullanışlı araçlardır. Ancak sınıflar ve fonksiyonlar için Reflection API, işleri tamamen yeni bir düzeye taşıyor.

### Sınıfları İncelemek

ReflectionClass örneğinin ham dökümü, hata ayıklama için çok sayıda yararlı bilgi sağlayabilir, ancak API'yi daha özel yollarla kullanabiliriz. Doğrudan Reflection sınıflarıyla çalışalım.

ReflectionClass nesnesinin nasıl başlatılacağını zaten gördünüz:

```
$prodclass = new \ReflectionClass(CdProduct::class);
```

Daha sonra, CdProduct'ı araştırmak için ReflectionClass nesnesini kullanacağım. Bu ne tür bir sınıf? Bir örnek oluşturulabilir mi? İşte bu soruları cevaplayacak bir fonksiyon:

```
class ClassInfo
{
    public static function getData(\ReflectionClass $class): string
    {
        $details = "";
        $name = $class->getName();
        $details .= ($class->isUserDefined()) ? "$name is user defined\n" : "";
        $details .= ($class->isInternal()) ? "$name is built-in\n" : "";
        $details .= ($class->isInterface()) ? "$name is interface\n" : "";
        $details .= ($class->isAbstract()) ? "$name is an abstract class\n" : "";
        $details .= ($class->isFinal()) ? "$name is a final class\n" : "";
        $details .= ($class->isInstantiable()) ? "$name can be instantiated\n" : "$name can not be instantiated\n";
        $details .= ($class->isCloneable()) ? "$name can be cloned\n" : "$name can not be cloned\n";
        return $details;
    }
}

$prodclass = new \ReflectionClass(CdProduct::class);
print ClassInfo::getData($prodclass);
```

CdProduct sınıfının adını ReflectionClass kurucusuna ileterek ReflectionClass nesnesini oluşturuyorum. $prodclass değişkenini daha sonra bir sınıfı sorgulamak için kullanılabilecek metotlardan bazılarını gösteren ClassInfo::classData() adlı bir metoda iletilir.

Metotlar kendi kendini açıklayıcı niteliktedir, ancak burada bazılarının kısa bir açıklaması verilmiştir:

- ReflectionClass::getName() incelenen sınıfın adını döndürür.
- ReflectionClass::isUserDefined() metodu, sınıf PHP kodunda bildirilmişse true değerini döndürür ve sınıf yerleşikse ReflectionClass::isInternal() metodu true değerini döndürür.
- ReflectionClass::isAbstract() ile sınıfın soyut olup olmadığını ve ReflectionClass::isInterface() ile arayüz olup olmadığını test edebilirsiniz.
- Sınıfın bir örneğini almak istiyorsanız bunun uygulanabilirliğini ReflectionClass::isInstantiable() ile test edebilirsiniz.
- ReflectionClass::isCloneable() metoduyla bir sınıfın kopyalanabilir olup olmadığını kontrol edebilirsiniz.
- Kullanıcı tanımlı bir sınıfın kaynak kodunu bile inceleyebilirsiniz. ReflectionClass nesnesi, kendi sınıfının dosya adına ve dosyadaki sınıfın başlangıç ve bitiş satırlarına erişim sağlar.

İşte sınıfın kaynağına erişmek için ReflectionClass'ı kullanan bir metot:

```
class ReflectionUtil
{
    public static function getClassSource(\ReflectionClass $class): string
    {
        $path = $class->getFileName();
        $lines = @file($path);
        $from = $class->getStartLine();
        $to = $class->getEndLine();
        $len = $to - $from + 1;
        return implode(array_slice($lines, $from - 1, $len));
    }
}

print ReflectionUtil::getClassSource(
    new \ReflectionClass(CdProduct::class)
);
```

ReflectionUtil, tek bir static metot olan ReflectionUtil::getClassSource()'a sahip basit bir sınıftır. Bu metot, tek parametre olarak ReflectionClass nesnesini alır ve başvurulan sınıfın kaynak kodunu döndürür. ReflectionClass::getFileName(), sınıf dosyasının yolunu verir. file() dosyadaki tüm satırların bir dizisini elde eder. ReflectionClass::getStartLine() sınıfın başlangıç satırını, ReflectionClass::getEndLine() sınıfın son satırı bulur. Buradan itibaren istenilen satırları almak için array_slice() fonksiyonunu kullanmak yeterlidir.

İşleri kısa tutmak için, bu kod hata işlemeyi atlar ( @ karakterini file() önüne yerleştirerek). Gerçek dünyadaki bir uygulamada parametreleri ve sonucu kontrol etmek istersiniz.

### Metotları İncelemek

ReflectionClass sınıfları incelemek için kullanıldığı gibi, ReflectionMethod nesnesi de metotları inceler.

ReflectionClass::getMethods() öğesinden ReflectionMethod nesnelerinin dizisini alabilirsiniz. Alternatif olarak, belirli bir metotla çalışmanız gerekiyorsa, ReflectionClass::getMethod() metot adını kabul eder ve ilgili ReflectionMethod nesnesini döndürür.

Ayrıca ReflectionMethod'u doğrudan sınıf/metot dizesi, sınıf adı ve metot adı veya nesne/metot adı ileterek de başlatabilirsiniz. İşte bu varyasyonlar:

```
$cd = new CdProduct("cd1", "bob", "bobbleson", 4, 50);
$classname = CdProduct::class;
$rmethod1 = new \ReflectionMethod("{$classname}::construct");
// class/method string
$rmethod2 = new \ReflectionMethod($classname, "construct");
// class name and method name
$rmethod3 = new \ReflectionMethod($cd, "construct");
// object and method name
```

Burada, ReflectionMethod sınıfını adım adım ilerletmek için ReflectionClass::getMethods()'u kullanıyoruz:

```
$prodclass = new \ReflectionClass(CdProduct::class);
$methods = $prodclass->getMethods();
foreach ($methods as $method) {
    print ClassInfo::methodData($method);
    print "\n----\n";
}

public static function methodData(\ReflectionMethod $method): string
{
    $details = "";
    $name = $method->getName();
    $details .= ($method->isUserDefined()) ? "$name is user defined\n" : "" ;
    $details .= ($method->isInternal()) ? "$name is built-in\n" : "" ;
    $details .= ($method->isAbstract()) ? "$name is an abstract class\n" : "" ;
    $details .= ($method->isPublic()) ? "$name is public\n" : "" ;
    $details .= ($method->isProtected()) ? "$name is protected\n" : "" ;
    $details .= ($method->isPrivate()) ? "$name is private\n" : "" ;
    $details .= ($method->isStatic()) ? "$name is static\n" : "" ;
    $details .= ($method->isFinal()) ? "$name is final\n" : "" ;
    $details .= ($method->isConstructor()) ? "$name is the constructor\n" : "" ;
    $details .= ($method->returnsReference()) ? "$name returns a reference (as opposed to a value)\n" : "" ;
    return $details;
}
```

Kod, ReflectionMethod nesnelerinin dizisini elde etmek için ReflectionClass::getMethods()'u kullanır ve ardından diziyi döngüye sokarak nesneleri methodData()'ya iletir.

methodData()'da kullanılan metotların adları amaçlarını yansıtır: kod, metodun kullanıcı tanımlı, abstract, public, protected, static veya final olup olmadığını kontrol eder. Ayrıca metodun kendi sınıfının kurucusu olup olmadığını ve bir referans döndürüp döndürmediğini de kontrol edebilirsiniz.

Tahmin edebileceğiniz gibi, daha önce ReflectionClass'ta kullanılana benzer bir teknik kullanarak bir metodun kaynak koduna erişebilirsiniz:

```
public static function getMethodSource(\ReflectionMethod $method): string
{
    $path = $method->getFileName();
    $lines = @file($path);
    $from = $method->getStartLine();
    $to = $method->getEndLine();
    $len = $to - $from + 1;
    return implode(array_slice($lines, $from - 1, $len));
}

$class = new \ReflectionClass(CdProduct::class);
$method = $class->getMethod('getSummaryLine');
print ReflectionUtil::getMethodSource($method);
```

### Metot Parametrelerini İncelemek

Artık metot imzaları nesne parametre türlerini sınırlayabildiğinden, metot imzasında bildirilen parametreleri inceleme yeteneği son derece yararlı hale gelir. Reflection API, ReflectionParameter sınıfını yalnızca bu amaç için sağlar. ReflectionParameter nesnesi elde etmek için ReflectionMethod nesnesinin yardımına ihtiyacınız vardır. ReflectionMethod::getParameters() metodu, ReflectionParameter nesnelerinin dizisini döndürür.

Ayrıca ReflectionParameter nesnesini her zamanki gibi doğrudan başlatabilirsiniz. ReflectionParameter kurucusu, çağrılabilir bir parametre ve parametre numarasını temsil eden bir tamsayı (sıfır ile indekslenmiş) veya parametre adını temsil eden bir dize gerektirir.

Yani bu örneklerin dördü de eşdeğerdir. Her biri, CdProduct sınıfının kurucusuna yönelik ikinci parametre için bir ReflectionParameter nesnesi oluşturur.

```
$classname = CdProduct::class;
$rparam1 = new \ReflectionParameter([$classname, "__construct"], 1);
$rparam2 = new \ReflectionParameter([$classname, "__construct"], "firstName");
$cd = new CdProduct("cd1", "bob", "bobbleson", 4, 50);
$rparam3 = new \ReflectionParameter([$cd, "__construct"], 1);
$rparam4 = new \ReflectionParameter([$cd, "__construct"], "firstName");
```

ReflectionParameter size bir parametrenin adını ve değişkenin referans yoluyla (yani, metot bildiriminde öncesinde & işaretiyle) iletilip iletilmediğini söyleyebilir. Ayrıca parametre tipinin gerektirdiği sınıf ve metodun null değer kabul edip etmeyeceğini de söyleyebilir.

ReflectionParameter'ın metot uygulamalarından bazıları şunlardır:

```
$class = new \ReflectionClass(CdProduct::class);
$method = $class->getMethod("__construct");
$params = $method->getParameters();

foreach ($params as $param) {
    print ClassInfo::argData($param) . "\n";
}
```

```
class ClassInfo
{
    public static function argData(\ReflectionParameter $arg): string
    {
        $details = "";
        $declaringclass = $arg->getDeclaringClass();
        $name = $arg->getName();
        $position = $arg->getPosition();
        $details .= "\$$name has position $position\n";
        if ($arg->hasType()) {
            $type = $arg->getType();
            $typenames = [];
            if ($type instanceof \ReflectionUnionType) {
                $types = $type->getTypes();
                foreach ($types as $utype) {
                    $typenames[] = $utype->getName();
                }
            } else {
                $typenames[] = $type->getName();
            }
            $typename = implode("|", $typenames);
            $details .= "\$$name should be type {$typename}\n";
        }
        if ($arg->isPassedByReference()) {
            $details .= "\${$name} is passed by reference\n";
        }
        if ($arg->isDefaultValueAvailable()) {
            $def = $arg->getDefaultValue();
            $details .= "\${$name} has default: $def\n";
        }
        if ($arg->allowsNull()) {
            $details .= "\${$name} can be null\n";
        }
        return $details;
    }
}
```

Kod, ReflectionClass::getMethod() metodunu kullanarak, bir ReflectionMethod nesnesi alır. Daha sonra ReflectionParameter nesnelerinin dizisini elde etmek için ReflectionMethod::getParameters() metodunu kullanır. argData() fonksiyonu, parametre hakkında bilgi edinmek için iletildiği ReflectionParameter nesnesini kullanır.

İlk olarak, ReflectionParameter::getName() ile parametrenin değişken adını alır. ReflectionParameter::getType() metodu, bir tür belirtildiyse ReflectionType nesnesini veya belirtilen tür union ise ReflectionUnionType sınıfını döndürür. Bunlardan hangisi döndürülürse, gerekli türü temsil eden bir dize oluşturulur. Kod daha sonra parametrenin isPassedByReference(); ile referans olup olmadığını kontrol eder. ve son olarak, varsayılan değerin kullanılabilirliğini arar ve bunu daha sonra return dizesine ekler.

### Reflection API’yi Kullanmak

Reflection API'nin temelleri elinizin altında olduğuna göre artık API'yi çalıştırabilirsiniz.

Module nesnelerini dinamik olarak çağıran bir sınıf oluşturduğunuzu hayal edin. Yani third-party tarafından yazılan ve herhangi bir kod değişikliğine ihtiyaç duymadan uygulamaya yerleştirilebilen eklentileri kabul edebilir. Bunu başarmak için, Module arayüzünde veya soyut üst sınıfta executive() metodunu tanımlayabilir ve tüm alt sınıfları uygulamaya zorlayabilirsiniz. Sisteminizin kullanıcılarının Module sınıflarını harici bir XML yapılandırma dosyasında listelemesine izin verebilirsiniz. Sisteminiz bu bilgiyi, her biri için executive() metodunu çağırmadan önce bir dizi Module nesnesini bir araya getirmek için kullanabilir.

Ancak her Module işini yapmak için farklı bilgilere ihtiyaç duyarsa ne olur? Bu durumda, XML dosyası her Module için property anahtar ve değerleri sağlayabilir ve her Module’ün yaratıcısı, her property için setter metorlar oluşturabilir. Bu temel göz önüne alındığında, doğru property adı için doğru setter metodun çağrıldığından emin olmak kodunuza kalmıştır.

Module arayüzü için birkaç uygulama sınıfını burada bulabilirsiniz:

```
class Person
{
    public $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }
}

interface Module
{
    public function execute(): void;
}
```

```
class FtpModule implements Module
{
    public function setHost(string $host): void
    {
        print "FtpModule::setHost(): $host\n";
    }

    public function setUser(string|int $user): void
    {
        print "FtpModule::setUser(): $user\n";
    }

    public function execute(): void
    {
        // do things
    }
}

class PersonModule implements Module
{
    public function setPerson(Person $person): void
    {
        print "PersonModule::setPerson(): {$person->name}\n";
    }
    
    public function execute(): void
    {
        // do things
    }
}
```

Burada PersonModule ve FtpModule'ün her ikisi de executive() metodunun boş uygulamalarını sağlar. Her sınıf ayrıca, çağrıldıklarını bildirmekten başka hiçbir şey yapmayan setter metotları da uygular. Sistem, tüm setter metotların tek bir parametre beklemesi gerektiği kuralını ortaya koyar: ya bir dize ya da tek bir dize parametresiyle örneklenebilecek bir nesne. PersonModule::setPerson() metodu Person nesnesi beklediğinden, örneğime Person sınıfını dahil ediyorum.

PersonModule ve FtpModule ile çalışmak için bir sonraki adım ModuleRunner sınıfı oluşturmaktır. XML dosyasında sağlanan yapılandırma bilgilerini temsil etmek için Module adına göre indekslenen çok boyutlu bir dizi kullanacaktır. İşte o kod:

```
class ModuleRunner
{
    private array $configData = [
        PersonModule::class => ['person' => 'bob'],
        FtpModule::class => [
            'host' => 'example.com',
            'user' => 'anon'
        ]
    ];
    
    private array $modules = [];
    // ...
}
```

ModuleRunner::$configData property’si, iki Module sınıfına referanslar içerir. Her Module öğesi için, bir dizi property içeren alt diziyi tutar. ModuleRunner'ın init() metodu, burada gösterildiği gibi doğru Module nesnelerinin oluşturulmasından sorumludur:

```
class ModuleRunner
{
    public function init(): void
    {
        $interface = new \ReflectionClass(Module::class);
        
        foreach ($this->configData as $modulename => $params) {
            $module_class = new \ReflectionClass($modulename);
            if (!$module_class->isSubclassOf($interface)) {
                throw new Exception("unknown module type: $modulename");
            }
            $module = $module_class->newInstance();
            foreach ($module_class->getMethods() as $method) {
                $this->handleMethod($module, $method, $params);
                // we cover handleMethod() in a future listing!
            }
            array_push($this->modules, $module);
        }
    }
}

$test = new ModuleRunner();
$test->init();
```

init() metodu, ModuleRunner::$configData dizisi boyunca döngü yapar ve her Module öğesi için bir ReflectionClass nesnesi oluşturmaya çalışır. ReflectionClass'ın kurucusu var olmayan bir sınıfın adıyla çağrıldığında exception fırlatılır, bu nedenle buraya daha fazla hata işleme dahil edeceğim. Module sınıfının Module türüne ait olduğundan emin olmak için ReflectionClass::isSubclassOf() metodunu kullanıyorum.

Her Module’ün executive() metodunu çağırmadan önce bir örneğin oluşturulması gerekir. ReflectionClass::newInstance()’ın amacı budur. Bu metot herhangi bir sayıda parametreyi kabul eder ve bunları ilgili sınıfın kurucu metoduna iletir. Her şey yolunda giderse sınıfın örneğini döndürür.

ReflectionClass::getMethods(), sınıf için mevcut olan tüm ReflectionMethod nesnelerinin dizisini döndürür. Dizideki her öğe için kod, ModuleRunner::handleMethod() metodunu çağırır. Daha sonra ona Module örneğini, ReflectionMethod nesnesini ve Module ile ilişkilendirilecek bir dizi parametre iletir. handleMethod(), Module nesnesinin kurucu metotlarını doğrular ve çağırır:

```
class ModuleRunner
{
    public function handleMethod(
        Module $module,
        \ReflectionMethod $method,
        array $params
    ): bool {
        $name = $method->getName();
        $args = $method->getParameters();
        if (count($args) != 1 || substr($name, 0, 3) != "set") {
            return false;
        }
        $property = strtolower(substr($name, 3));
        if (!isset($params[$property])) {
            return false;
        }
        if (!$args[0]->hasType()) {
            $method->invoke($module, $params[$property]);
            return true;
        }
        $arg_type = $args[0]->getType();
        if (!($arg_type instanceof \ReflectionUnionType) && class_exists(
            $arg_type->getName()
        )) {
            $method->invoke(
                $module,
                (new \ReflectionClass($arg_type->getName()))->newInstance(
                    $params[$property]
                )
            );
        } else {
            $method->invoke($module, $params[$property]);
        }
        return true;
    }
}
```

handleMethod() ilk önce metotta geçerli bir setter olup olmadığını kontrol eder. Kodda geçerli bir setter metodun setXXXX() olarak adlandırılması ve yalnızca bir parametre bildirmesi gerekir.

Kod, parametrenin kontrol edildiğini varsayarak, metot adının başlangıcından set öğesini kaldırır ve elde edilen dizeyi küçük harf karakterlere dönüştürerek metot adından property adını çıkarır. Bu dize $params dizi parametresini test etmek için kullanılır. Bu dizi, Module nesnesiyle ilişkilendirilecek, kullanıcı tarafından sağlanan property’leri içerir. $params dizisi bu property’i içermiyorsa, false döndürür.

Module metodundan çıkarılan property adı $params dizisindeki bir öğeyle eşleşiyorsa, devam edip doğru setter metodunu çağırabilirim. Bunu yapmak için, setter metodun ilk (ve tek) gerekli parametresinin türünü kontrol etmesi gerekir. Parametrenin tür bildirimi varsa (ReflectionParameter::hasType()) ve belirtilen tür bir sınıfa çözümleniyorsa, o zaman metodun nesne beklediğini biliyoruz. Aksi halde primitive bir tür beklediğini varsayıyoruz.

Setter metotları çağırmak için yeni bir Reflection API metoduna ihtiyacım var. ReflectionMethod::invoke(), temsil ettiği metoda iletmek için bir nesneye (veya static metot için null) ve herhangi bir sayıda metot parametresine ihtiyaç duyar. ReflectionMethod::invoke(), sağlanan nesnenin metoduyla eşleşmemesi durumunda exception fırlatır. Bu metodu iki yoldan biriyle adlandırıyorum. Setter metot nesne parametresi gerektirmiyorsa, kullanıcının sağladığı property dizesiyle ReflectionMethod::invoke()'u çağırırım. Metot nesne gerektiriyorsa (ki bunu class_exists ile test edebilirim), doğru türde bir nesneyi başlatmak için property dizesini kullanırım. Bu daha sonra setter’a iletilir.

Örnek, gerekli nesnenin, kurucusuna yönelik tek bir dize parametresi ile başlatılabileceğini varsayar. Elbette ReflectionClass::newInstance()’ı çağırmadan önce bunu kontrol etmek en iyisidir.

ModuleRunner::init() metodu yürütüldüğünde, nesne, tamamı verilerle hazırlanmış olan Module nesneleri deposuna sahip olur. Artık sınıfa, Module nesneleri arasında döngü yapması ve her birinde execute()çağırması için bir metot verilebilir.

### Nitelikler (Attributes)

Birçok dil, kaynak dosyalardaki özel etiketlerin kodun kullanımına sunulmasını sağlayan bir mekanizma sağlar. Bunlar genellikle annotation olarak bilinir. PHP paketlerinde (özellikle Doctrine veritabanı kütüphanesi ve Symfony routing bileşeni gibi) PHP 8'e kadar bazı uygulamalar olmasına rağmen, bu özellik için dil düzeyinde destek yoktu. Niteliklerin eklenmesiyle bu durum değişti.

Temel olarak nitelik, bir sınıfa, metoda, property’e, parametreye veya sabite ek bilgi eklemenizi sağlayan özel bir etikettir. Bu bilgi reflection yoluyla sistem tarafından kullanılabilir hale gelir.

Peki annotation’ı ne için kullanabilirsiniz? Tipik olarak bir metot, kullanılmasını beklediği yol hakkında daha fazla bilgi sağlayabilir. Örneğin otomatik olarak çalıştırılması gereken metotları keşfetmek için sınıfı tarayabilirsiniz. İlerledikçe diğer kullanım alanlarından da bahsedeceğim.

Bir annotation bildirelim ve ona erişelim:

```
#[info]
class Person
{
}
```

Yani bir annotation #[ ve ] arasına alınmış bir dize belirteci ile bildirilir. Bu durumda #[info] kullandım. Birçok kod örneğinde, namespace bildirimini hariç tutuyorum çünkü kod, bildirilen namespace veya main içinde eşit derecede iyi çalışacaktır. Ancak bu durumda namespace’e dikkat etmek önemlidir. Bu noktaya geri döneceğim.

Şimdi annotation’a erişmek için:

```
$rpers = new \ReflectionClass(Person::class);
$attrs = $rpers->getAttributes();

foreach ($attrs as $attr) {
    print $attr->getName() . "\n";
}
```

Person'ı inceleyebilmek için ReflectionClass nesnesini başlatıyorum. Sonra getAttributes() metodunu çağırıyorum. Bu, ReflectionAttribute nesnelerinin dizisini döndürür. ReflectionAttribute::getName(), bildirdiğim property’nin adını döndürür.

İşte çıktı:

```
popp\ch05\batch09\info
```

Çıktıda annotation namespace’e sahip. Adın popp\ch05\batch09 kısmı örtülüdür. Sınıfa referans vermek için kullandığımız kurallara ve alias’lara göre bir annotation’a referans verebilirim. Yani popp\ch05\batch09 namespace’inde [#info] bildirmek, başka bir yerde [#\popp\ch05\batch09\info] bildirmeye eşdeğerdir. Aslında göreceğiniz gibi, referans verdiğiniz herhangi bir nitelik için somutlaştırılabilecek bir sınıf bile tanımlayabilirsiniz.

Annotation PHP'nin çeşitli yönlerine uygulanabilir. Tablo 5-2, ilgili Reflection sınıflarıyla birlikte annotation eklenebilecek özellikleri listelemektedir.

**Tablo 5-2**. Annotation’a uygun PHP özellikleri

| Özellik | Metot |
| --- | --- |
| Sınıf | ReflectionClass::getAttributes() |
| Property | ReflectionProperty::getAttributes() |
| Fonksiyon/Metot | ReflectionFunction::getAttributes() |
| Sabit | ReflectionConstant::getAttributes() |

Aşağıda bir metoda uygulanan niteliğin örneği verilmiştir:

```
#[moreinfo]
public function setName(string $name): void
{
    $this->name = $name;
}
```

Şimdi ona erişelim. Süreci oldukça tanıdık bulacaksınız:

```
$rpers = new \ReflectionClass(Person::class);
$rmeth = $rpers->getMethod("setName");
$attrs = $rmeth->getAttributes();

foreach ($attrs as $attr) {
    print $attr->getName() . "\n";
}

// popp\ch05\batch09\moreinfo
```

Şu ana kadar gördüklerinizin zaten bir faydası var. Bir niteliği bir tür flag olarak kullanabiliriz. Örneğin, bir Hata Ayıklama niteliği yalnızca geliştirme sırasında çağrılması gereken metotlarla ilişkilendirilebilir. Bununla birlikte, niteliklerin hala daha fazlası var. Bir tür tanımlayabilir ve argümanlar aracılığıyla daha fazla bilgi sağlayabiliriz. Bu yeni olasılıkların önünü açıyor. Routing kütüphanesinde, metodun eşlenmesi gereken URL endpoint’ini belirtebilirim. Event sisteminde bir nitelik, bir sınıfın veya metodun belirli bir event ile ilişkilendirilmesi gerektiğinin sinyalini verebilir.

Bu örnekte iki argüman içeren bir nitelik tanımlıyorum:

```
#[ApiInfo("The 3 digit company identifier", "A five character department tag")]
public function setInfo(int $companyid, string $department): void
{
    $this->companyid = $companyid;
    $this->department = $department;
}
```

ReflectionAttribute nesnesini edindikten sonra getArguments() metodunu kullanarak argümanlara erişebilirim.

```
$rpers = new \ReflectionClass(Person::class);
$rmeth = $rpers->getMethod("setInfo");
$attrs = $rmeth->getAttributes();

foreach ($attrs as $attr) {
    print $attr->getName() . "\n";
    foreach ($attr->getArguments() as $arg) {
        print " - $arg\n";
    }
}
```

İşte çıktısı:

```
popp\ch05\batch09\ApiInfo
 - The 3 digit company identifier
 - A five character department tag
```

Bahsettiğim gibi, bir niteliği açıkça bir sınıfa eşleyebilirsiniz. İşte basit bir ApiInfo sınıfı:

```
namespace popp\ch05\batch09;

use Attribute;

#[Attribute]
class ApiInfo
{
    public function __construct(public string $compinfo, public string $depinfo)
    {
    }
}
```

Nitelik ile sınıfım arasında düzgün bir ilişki kurabilmek için, Attribute'u kullanmayı ve ayrıca yerleşik [#Attribute]'u sınıfa uygulamayı hatırlamalıyım.

Örnekleme sırasında, niteliğe ilişkin tüm argümanlar otomatik olarak karşılık gelen sınıfın kurucusuna iletilir. Bu durumda, verileri yalnızca ilgili property’lere atadım. Gerçek dünyadaki bir uygulamada, muhtemelen sınıf bildirimini doğrulamak için bazı ek işlemler gerçekleştirir veya ilgili işlevleri sağlardım.

Nitelik sınıfının otomatik olarak çağrılmadığını anlamak önemlidir. Bunu ReflectionAttribute::newInstance() aracılığıyla yapmalıyız. Kodumu yeni sınıfla çalışacak şekilde uyarlıyorum:

```
$rpers = new \ReflectionClass(Person::class);
$rmeth = $rpers->getMethod("setInfo");
$attrs = $rmeth->getAttributes();

foreach ($attrs as $attr)  {
    print $attr->getName() . "\n";
    $attrobj = $attr->newInstance();
    print " - " . $attrobj->compinfo . "\n";
    print " - " . $attrobj->depinfo . "\n";
}
```

Her ne kadar nitelik verilerine ApiInfo nesnesi aracılığıyla erişiyor olsam da buradaki etki aynıdır. ReflectionAttribute::newInstance()'ı çağırıyorum ve ardından doldurulmuş property’lere erişiyorum.

Bu son örneğin potansiyel olarak ölümcül bir kusuru var. Bir metoda birden fazla nitelik eklenebilir. Bu nedenle setInfo() metofuna atanan her niteliğin ApiInfo'nun bir örneği olduğundan emin olamayız. ApiInfo::$compinfo ve ApiInfo::$depinfo'ya bu property erişimleri, ApiInfo türünde olmayan herhangi bir nitelik için başarısız olmaya mahkumdur.

Neyse ki getAttributes()'a bir filtre uygulayabiliyoruz:

```
$rpers = new \ReflectionClass(Person::class);
$rmeth = $rpers->getMethod("setInfo");
$attrs = $rmeth->getAttributes(ApiInfo::class);
```

Artık yalnızca ApiInfo::class için kesin olan eşleşmeler döndürülecek ve böylece kodun geri kalanı güvenli hale getirilecek. İşleri biraz daha gevşetebiliriz:

```
$rpers = new \ReflectionClass(Person::class);
$rmeth = $rpers->getMethod("setInfo");
$attrs = $rmeth->getAttributes(ApiInfo::class, \ReflectionAttribute::IS_INSTANCEOF);
```

İkinci bir parametre olan ReflectionAttribute::IS_INSTANCEOF'u ReflectionAttribute::getAttributes()'a ileterek, filtreyi belirtilen sınıfla, miras alan alt sınıflarla veya arayüzlerle eşleşecek şekilde düzenliyorum.

Tablo 5-3 karşılaştığımız ReflectionAttribute metotlarını listelemektedir.

**Tablo 5-3**. Bazı ReflectionAttribute metotları

| Metot | Açıklama |
| --- | --- |
| getName() | Nitelik için namespace’li bir tür döndürür |
| getArguments() | Başvurulan nitelikle ilişkili tüm parametrelerin dizisini döndürür |
| newInstance() | Herhangi bir argümanı kurucuya ileterek nitelik sınıfının örneğini başlatır ve döndürür |

**Özet**

Bu bölümde kütüphane ve sınıflarınızı yönetmek için kullanabileceğiniz bazı teknik ve araçları ele aldım. PHP’nin namespace özelliğini araştırdım. Sınıflar için esnek bir organizasyon sağlamak amacıyla namespace, autoloading ve dosya sistemini birleştirebileceğimizi gördünüz.

Ayrıca Reflection API ile işleri bir sonraki aşamaya taşımadan önce PHP'nin nesne ve sınıf fonksiyonlarını da inceledik. Reflection'ın sunduğu potansiyel kullanımlardan birini gösteren basit bir örnek oluşturmak için Reflection sınıflarını kullandık. Son olarak Reflection sınıflarını niteliklerle birleştirdik: PHP 8'de yeni ve önemli bir özellik.

**Kaynak:** “PHP 8 Objects, Patterns, and Practice”, Matt Zandstra