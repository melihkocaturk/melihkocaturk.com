---
layout: post
title: Neden Laravel?
---

Dinamik web'in ilk günlerinde, web uygulaması yazmak bugün olduğundan çok daha farklı görünüyordu. O zaman geliştiriciler, yalnızca uygulamalarımızın benzersiz iş mantığı için değil, aynı zamanda sitelerde çok yaygın olan kullanıcı kimlik doğrulaması, input validation, veritabanı erişimi, templating vb. bileşenlerin her biri için kod yazmaktan sorumluydu.

Bugün programcılar düzinelerce uygulama geliştirme framework’üne ve kolayca erişilebilen binlerce bileşene ve kütüphaneye sahiptir. Programcılar arasında yaygın bir nakarat, siz bir framework’ü öğrendiğiniz zaman, onu değiştirmek niyetiyle daha yeni (ve sözde daha iyi) üç framework’ün ortaya çıkmasıdır.

"Sırf orada olduğu için" bir dağa tırmanmak geçerli bir gerekçe olabilir, ancak belirli bir framework kullanmayı seçmek için daha iyi nedenler vardır. Şu soruyu sormaya değer, neden framework? Daha spesifik olarak, neden Laravel?

## Neden Framework Kullanmalısınız?

PHP geliştiricileri için mevcut olan bireysel bileşenleri veya paketleri kullanmanın neden faydalı olduğunu görmek kolaydır. Paketlerde, iyi tanımlanmış bir işi olan yalıtılmış kod parçasının geliştirilmesinden ve sürdürülmesinden bir başkası sorumludur ve teoride o kişi, bu tek bileşen hakkında sizin sahip olabileceğinizden daha derin bir anlayışa sahiptir.

Laravel ve Symfony, Lumen ve Slim gibi framework’ler, yapılandırma dosyaları, servis sağlayıcılar, öngörülen dizin yapıları ve uygulama ön yüklemeleri gibi özel framework "yapıştırıcısı" ile birlikte third-party bileşenlerin bir koleksiyonunu önceden paketler. Bu nedenle, genel olarak bir framework kullanmanın yararı, birinin yalnızca sizin için tek tek bileşenler hakkında değil, aynı zamanda bu bileşenlerin nasıl bir araya getirilmesi gerektiği konusunda da karar vermesidir.

Diyelim ki bir framework’ün avantajı olmadan yeni bir web uygulaması başlattınız. Nereden başlayacaksınız? Muhtemelen HTTP request’lerini yönlendireceksiniz, bu nedenle şimdi mevcut tüm HTTP request ve response kütüphanelerini değerlendirmeniz ve birini seçmeniz gerekir. Daha sonra bir router seçmeniz gerekecek. Ve muhtemelen bir tür route yapılandırma dosyası oluşturmanız gerekecek. Hangi söz dizimini kullanmalı? Nereye gitmeli? Peki ya controller? Nerede tutulacak ve nasıl yüklenecekler? Controller ve bağımlılıklarını çözmek için muhtemelen dependency injection container’a ihtiyacınız olacak. Fakat hangisi?

Ayrıca, tüm bu soruları yanıtlamak için zaman ayırırsanız ve uygulamanızı başarıyla oluşturursanız, bir sonraki geliştirici üzerindeki etkisi ne olur? Dört veya bir düzine özel framework tabanlı uygulamanız olduğunda ve controller’ların nerede olduğunu veya router sözdiziminin ne olduğunu hatırlamanız gerektiğinde ne olur?

**Tutarlılık ve Esneklik**

Framework’ler, "Burada hangi bileşeni kullanmalıyız?" sorusuna dikkatle düşünülmüş bir yanıt vererek bu sorunu ele alır ve seçilen belirli bileşenlerin birlikte iyi çalışmasını sağlar. Ek olarak, framework’ler, projede yeni olan bir geliştiricinin anlaması gereken kod miktarını azaltan kurallar sağlar; örneğin, bir Laravel projesinde router’ın nasıl çalıştığını anlarsanız, tüm Laravel projelerinde nasıl çalıştığını anlarsınız.

Birisi her yeni proje için kendi framework’ünüzü oluşturmanızı tavsiye ettiğinde, gerçekte savundukları şey, uygulamanızın temeline neyin girip neyin girmediğini kontrol etme yeteneğidir. Bu, en iyi framework’lerin size yalnızca sağlam bir temel sağlamakla kalmayıp, aynı zamanda ihtiyacınıza uygun olarak özelleştirme özgürlüğü vereceği anlamına gelir. Ve bu, size kitabın geri kalanında göstereceğim gibi, Laravel'i bu kadar özel yapan şeyin bir parçasıdır.

## Laravel'i Bu Kadar Özel Yapan Nedir?

Peki Laravel'i farklı kılan nedir? Neden herhangi bir zamanda birden fazla PHP çerçevesine sahip olmalıyız? Zaten hepsi Symfony'nin bileşenlerini kullanıyor, değil mi? Laravel'i "harekete geçiren" şeylerden biraz bahsedelim.

**Laravel’in Felsefesi**

Değerleri anlamak için yalnızca Laravel marketing materyallerini ve README'leri okumanız yeterlidir. Taylor, "Illuminate" ve "Spark" gibi ışıkla ilgili sözcükler kullanır. Bir de şunlar var:  “Artisans”, “Elegant”. Ayrıca bunlar: "Breath of fresh air" "Fresh start". Ve son olarak: "Rapid" “Warp speed”.

Framework’ün en güçlü şekilde iletilen iki değeri, geliştirici hızını ve geliştirici mutluluğunu artırmaktır. Taylor, "Artisan" dilini kasıtlı olarak daha faydacı değerlere zıt olarak tanımladı. Bu tür bir düşüncenin doğuşunu, StackExchange'teki 2011 tarihli sorusunda görebilirsiniz: "Bazen kodun 'güzel görünmesini' sağlamak için anlamsız miktarda zaman (saatler) harcıyorum" - sadece kodun kendisine bakma konusunda daha iyi bir deneyim uğruna. Ayrıca geliştiricilerin fikirlerini hayata geçirmelerini kolaylaştırmanın ve hızlandırmanın, harika ürünler yaratmanın önündeki gereksiz engelleri ortadan kaldırmanın değerinden sık sık bahseder.

Laravel, özünde, geliştiricileri donatmak ve harekete geçirmek ile ilgilidir. Amacı, geliştiricilerin hızlı bir şekilde öğrenmesine, başlamasına, geliştirmesine, ve basit, anlaşılır, kalıcı kodlar yazmalarına yardımcı olmaktır.

Geliştiricileri hedefleme kavramı, Laravel materyallerinde açıktır. Dökümantasyonda "Happy developers make the best code" yazıyor. “Developer happiness from download to deploy” bir süredir resmi olmayan sloganıydı. Tabii ki, herhangi bir araç veya framework, geliştiricilerin mutlu olmasını istediğini söyleyecektir. Ancak geliştirici mutluluğunun ikincil değil birincil kaygı olarak görülmesi Laravel'in tarzı ve karar verme süreci üzerinde büyük bir etkiye sahip. Diğer framework’lerin, birincil hedef olarak mimari saflığı veya enterprise geliştirme ekiplerinin amaç ve değerleriyle uyumluluğu hedefleyebildiği durumlarda, Laravel'in birincil odak noktası bireysel geliştiriciye hizmet etmektir. Bu, Laravel'de mimari olarak saf veya enterprise kullanıma hazır uygulamalar yazamayacağınız anlamına gelmez, ancak kod tabanınızın okunabilirliği ve anlaşılırlığı pahasına olmak zorunda da değildir.

**Laravel Geliştirici Mutluluğunu Nasıl Sağlar?**

Geliştiricileri mutlu etmek istediğinizi söylemek bir şeydir. Bunu yapmak başka bir şeydir ve bir framework’te geliştiricileri mutsuz etme ve onları mutlu etme olasılığı en yüksek olanı sorgulamanızı gerektirir. Laravel'in geliştiricilerin hayatlarını kolaylaştırmaya çalıştığı birkaç yol vardır.

Birincisi, Laravel hızlı bir uygulama geliştirme framework’üdür. Bu, kolay bir öğrenme eğrisine ve yeni bir uygulama başlatma ile onu yayınlama arasındaki adımları en aza indirmeye odaklandığı anlamına gelir. Veritabanı etkileşimlerinden kimlik doğrulamaya, kuyruklara, e-postaya ve önbelleğe almaya kadar web uygulamaları oluşturmadaki en yaygın görevlerin tümü, Laravel'in sağladığı bileşenler tarafından daha basit hale getirilir. Ancak Laravel'in bileşenleri kendi başlarına mükemmel değildir; tüm framework’te tutarlı bir API ve öngörülebilir yapılar sağlarlar. Bu, Laravel'de yeni bir şey denediğinizde, muhtemelen "... ve işe yarıyor" diyeceğiniz anlamına gelir.

Bu da framework’ün kendisiyle bitmiyor. Laravel, uygulamalar için eksiksiz bir araç ekosistemi sağlar. Yerel geliştirme için Sail, Valet ve Homestead'e, sunucu yönetimi için Forge'a ve gelişmiş deployment için Envoyer ve Vapor'a sahipsiniz. Ayrıca bir dizi eklenti paketi mevcuttur: Ödemeler ve abonelikler için Cashier, WebSockets için Echo, arama için Scout, API kimlik doğrulaması için Sanctum ve Passport, frontend testi için Dusk, sosyal oturum açma için Socialite, kuyrukları izlemek için Horizon, admin panel oluşturmak için Nova ve SaaS'ınızı önyüklemek için Spark. Laravel, benzersiz bir şey yapabilmeleri için geliştiricilerden tekrarlayan işleri almaya çalışıyor.

Daha sonra, Laravel "convention over configuration" a odaklanır - Laravel'in varsayılanlarını kullanmak istiyorsanız, tüm ayarlarınızı bildirmenizi gerektiren diğer framework’lere göre çok daha az iş yapmanız gerekeceği anlamına gelir. Laravel üzerinde inşa edilen projeler, diğer çoğu PHP framework’lerinde inşa edilen projelerden daha az zaman alır.

Lavel ayrıca sadeliğe odaklanır. İsterseniz, Laravel ile dependency injection, Data Mapper pattern ve repository’lerini, Command Query Responsibility Segregation ve diğer her türden daha karmaşık pattern’i kullanmak mümkündür. Ancak diğer framework’ler, bu araçların ve yapıların her projede kullanılmasını önerebilirken, Laravel dökümantasyonu ve topluluğu, mümkün olan en basit uygulamayla başlamaya eğilimlidir - burada global bir fonksiyon, orada bir facade, şurada ActiveRecord. Bu, geliştiricilerin, karmaşık ortamlarda kullanışlılığı sınırlamadan ihtiyaçlarını çözmek için mümkün olan en basit uygulamayı oluşturmalarına olanak tanır.

Laravel'in diğer PHP framework’lerinden ne kadar farklı olduğunun ilginç bir göstergesi, yaratıcısının ve topluluğunun Java'dan çok Ruby, Rails ve fonksiyonel programlama dillerine bağlı ve onlardan ilham almış olmasıdır. Modern PHP'de, PHP'nin Java benzeri yönlerini kucaklayan, ayrıntıya ve karmaşıklığa yönelmek için güçlü bir akım var. Ancak Laravel, dinamik ve basit kodlama uygulamalarını ve dil özelliklerini kucaklayarak diğer tarafta olma eğilimindedir.

**Laravel Topluluğu**

Laravel topluluğu ile ilk kez karşılaşıyorsanız, sabırsızlıkla bekleyeceğiniz özel bir şey var demektir. Laravel'in ayırt edici unsurlarından biri, büyümesine ve başarısına katkıda bulunan etrafını saran misafirperver, öğretici topluluktur. Jeffrey Way'in Laracasts video eğitimlerinden Laravel News'e, Slack, IRC ve Discord kanallarına, Blog yazarlarına, podcast'lere ve Laracon konferanslarına kadar Laravel, ilk günden beri etrafta olan zengin ve canlı bir topluluğa sahiptir. Ve bu bir tesadüf değil:

Laravel'in en başından beri, tüm insanların kendilerini bir şeyin parçası gibi hissetmek istedikleri fikrine sahibim. Benzer düşünen insanlardan oluşan bir gruba ait olmayı ve kabul edilmeyi istemek doğal bir insan içgüdüsüdür. Dolayısıyla, kişiliği bir web framework’e enjekte ederek ve toplulukla gerçekten aktif olarak, bu tür bir duygu gelişebilir.

-- Taylor Otwell, Ürün ve Destek görüşmesi

Taylor, Laravel'in ilk günlerinden itibaren başarılı bir açık kaynak projesinin iki şeye ihtiyacı olduğunu anlamıştı: iyi bir dokümantasyon ve sıcak bir topluluk. Ve bu iki şey artık Laravel'in ayırt edici özellikleri oldu.

Nasıl çalışır

Şimdiye kadar burada paylaştığım her şey tamamen soyuttu. Peki ya kodlar? Laravel ile  çalışmanın gerçekte nasıl olduğunu görebilmeniz için basit bir uygulamaya yapalım.

**Örnek 1-1**. routes/web.php içindeki “Hello, World”

```
Route::get('/', function () {
    return 'Hello, World!';
});
```

Laravel uygulamasında yapabileceğiniz en basit eylem, bir rota tanımlamak ve birisi bu rotayı her ziyaret ettiğinde bir sonuç döndürmektir. Makinenizde yeni bir Laravel uygulaması başlatın, Örnek 1-1’deki rotayı tanımlayın ve ardından siteyi public dizininden çalıştırın, tam olarak işleyen bir “Hello, World” örneğine sahip olacaksınız.

Örnek 1-2'de görebileceğiniz gibi, controller’a çok benziyor (test etmek isterseniz, controller oluşturmak için önce php artisan make:controller WelcomeController komutunu çalıştırın).

**Örnek 1-2**. Controller ile “Hello, World”

```
// File: routes/web.php
use App\Http\Controllers\WelcomeController;

Route::get('/', [WelcomeController::class, 'index']);
```

```
// File: app/Http/Controllers/WelcomeController.php
namespace App\Http\Controllers;

class WelcomeController extends Controller
{
    public function index()
    {
        return 'Hello, World!';
    }
}
```

Ve mesajını bir veritabanında saklıyorsanız, oldukça benzer görünecektir (Örnek 1-3'e bakın).

**Örnek 1-3**. Veritabanı erişimi ile "Hello, World"

```
// File: routes/web.php
use App\Greeting;

Route::get('create-greeting', function () {
    $greeting = new Greeting;
    $greeting->body = 'Hello, World!';
    $greeting->save();
});

Route::get('first-greeting', function () {
    return Greeting::first()->body;
});
```

```
// File: app/Models/Greeting.php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Greeting extends Model
{
    use HasFactory;
}
```

```
// File: database/migrations/2023_03_12_192110_create_greetings_table.php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    /**
     * Run the migrations.
     */
    public function up(): void
    {
        Schema::create('greetings', function (Blueprint $table) {
            $table->id();
            $table->string('body');
            $table->timestamps();
        });
    }
    /**
     * Reverse the migrations.
     */
    public function down(): void
    {
        Schema::dropIfExists('greetings');
    }
};
```

Örnek 1-3 biraz bunaltıcı olabilir ve eğer öyleyse, onu atlayın. Burada olup biten her şeyi sonraki bölümlerde öğreneceksiniz, ancak birkaç satır kodla migration ve modelleri ayarlayabileceğinizi ve veritabanından kayıtları çekebileceğinizi şimdiden görebilirsiniz. Bu kadar basit.

## Öyleyse Neden Laravel?

Çünkü Laravel, modern kodlama standartlarını kullanır, canlı bir toplulukla çevrilidir ve güçlü araçlarla fikirlerinizi kod israfı olmadan gerçeğe dönüştürmenize yardımcı olur. Ve çünkü siz sevgili geliştirici, mutlu olmayı hak ediyorsunuz.

**Kaynak:** “Laravel: Up & Running”, Matt Stauffer