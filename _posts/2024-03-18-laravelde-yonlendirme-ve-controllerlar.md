---
layout: post
title: Laravel’de Yönlendirme ve Controller’lar
---

Herhangi bir web uygulama framework’ünün temel işlevi, kullanıcıdan istek almak ve yanıtları, genellikle HTTP(S) aracılığıyla iletmektir. Bu, bir framework’ü öğrenirken ele alınması gereken ilk ve en önemli şeyin uygulamanın rotalarını tanımlamak olduğu anlamına gelir; rotalar olmadan, son kullanıcıyla etkileşim kurma yeteneğiniz çok azdır veya hiç yoktur.

Bu bölümde laravel'de rotaları inceleyeceğiz; bunları nasıl tanımlayacağınızı, yürütmeleri gereken koda nasıl yönlendireceğinizi ve çok çeşitli yönlendirme ihtiyaçlarını karşılamak için Laravel'in yönlendirme araçlarını nasıl kullanacağınızı göreceksiniz.

## MVC, HTTP Fiilleri ve REST'e Hızlı Bir Bakış

Bu bölümde konuşacaklarımızın çoğu, Model-View-Controller (MVC) uygulamalarının nasıl yapılandırıldığına atıfta bulunur ve bakacağımız örneklerin çoğu REST benzeri rota adlarını ve fiillerini kullanır, bu yüzden hızlı bir şekilde başlayalım.

**MVC Nedir?**

MVC'de üç temel konseptiniz vardır:

- **Model** - Tek bir veritabanı tablosunu (veya bu tablodan bir kaydı) temsil eder; "Company" veya "Dog" gibi düşünün.
- **View** - Verilerinizi son kullanıcıya gösteren şablonu temsil eder; "HTML, CSS ve JavaScript ile oluşturulan login sayfası şablonu"nu düşünün.
- **Controller** - Bir trafik polisi gibi, tarayıcıdan HTTP isteklerini alır, veritabanından ve diğer depolama mekanizmalarından verileri alır, kullanıcı girişini doğrular ve sonunda kullanıcıya bir yanıt gönderir.

Şekil 3-1'de, kullanıcının önce tarayıcısını kullanp bir HTTP isteği göndererek denetleyiciyle etkileşim kuracağını görebilirsiniz. Controller, bu talebe yanıt olarak modele (veritabanı) veri yazabilir ve/veya modelden veri çekebilir. Controller daha sonra muhtemelen bir view’a veri gönderecek ve ardından view, tarayıcılarında görüntülenmesi için son kullanıcıya geri dönecektir.

**Şekil 3-1**. MVC'nin temel bir örneği

![MVC’nin temel bir örneği](/assets/images/2024/03/basic-example-of-mvc.png "MVC’nin temel bir örneği"){: class="img-fluid"}

Uygulama mimarisine bu basit bakış açısına uymayan bazı Laravel kullanım durumlarını ele alacağız, bu nedenle MVC'ye takılıp kalmayın. Ancak bu, en azından sizi, view ve controller hakkında konuşurken bölümün geri kalanına hazırlayacaktır.

**HTTP Fiilleri**

En yaygın HTTP fiilleri GET ve POST'tur, ardından PUT ve DELETE gelir. Ayrıca HEAD, OPTIONS ve PATCH ve normal web geliştirmede hemen hemen hiç kullanılmayan iki tane daha var, TRACE ve CONNECT.

İşte hızlı bir özet:

- **GET** - Bir resource (veya bir resource listesi) isteyin.
- **HEAD** - GET response’unun yalnızca başlıklar içeren bir sürümünü isteyin.
- **POST** - resource oluşturun.
- **PUT** - Bir resource’un üzerine yazın.
- **PATCH** - Bir resource’u değiştirin.
- **DELETE** - Bir resource’u silin.
- **OPTIONS** - Sunucuya bu URL'de hangi fiillere izin verildiğini sorun.

Tablo 3-1, bir resource denetleyicide mevcut olan eylemleri gösterir (bunlar hakkında daha fazla bilgi için "Resource Controller" bölümüne bakın). Her eylem, belirli bir fiili kullanarak belirli bir URL pattern’ini çağırmanızı bekler, böylece her fiilin ne için kullanıldığına dair bir fikir edinebilirsiniz.

**Tablo 3-1**. Laravel'in resource controller metotları

| Fiil (Verb) | URL | Controller Metodu | Ad | Açıklama |
| --- | --- | --- | --- | --- |
| GET | tasks | index() | tasks.index | Tüm görevleri göster |
| GET | tasks/create | create() | tasks.create | Görev oluşturma formunu göster |
| POST | tasks | store() | tasks.store | Görev oluşturma formundan form gönderimini kabul eder |
| GET | tasks/{task} | show() | tasks.show | Bir görev göster |
| GET | tasks/{task}/edit | edit() | tasks.edit | Bir görevi düzenle |
| PUT/PATCH | tasks/{task} | update() | tasks.update | Görev düzenleme formundan form gönderimini kabul eder |
| DELETE | tasks/{task} | destroy() | tasks.destroy | Bir görevi sil |

**REST Nedir?**

REST'i ileriki bölümlerde daha ayrıntılı olarak ele alacağız, ancak kısa bir giriş olarak, API oluşturmaya yönelik bir mimari stildir. Bu kitapta REST hakkında konuştuğumuzda, esas olarak aşağıdakiler gibi birkaç özelliğe atıfta bulunacağız:

- Her seferinde tek bir birincil resource etrafında yapılandırılmış olmak (örn. tasks)
- HTTP fiillerini kullanan öngörülebilir URL yapılarıyla etkileşimlerden oluşur (Tablo 3-1'de görüldüğü gibi)
- JSON döndürür ve genellikle JSON ile talep edilir

Daha fazlası var, ancak bu kitapta kullanılacağı şekliyle genellikle RESTful, "görev düzenleme sayfasında GET /tasks/14/edit gibi öngörülebilir çağrılar yapabilmemiz için bu URL tabanlı yapılardan sonra modellenmiş" anlamına gelir. 

Bu önemlidir (API oluşturmazken bile), çünkü Tablo 3-1'de görebileceğiniz gibi, Laravel'in yönlendirme yapıları REST benzeri bir yapıyı temel alır.

## Rota (Route) Tanımları

Laravel uygulamalarında, web rotalarınızı routes/web.php'de ve API rotalarınızı routes/api.php'de tanımlayacaksınız. Web rotaları, kullanıcılarınızın ziyaret edeceği rotalardır; API rotaları, varsa API'niz için olanlardır. Şimdilik, öncelikle routes/web.php içindeki rotalara odaklanacağız.

Rota tanımlamanın en basit yolu, Örnek 3-1'de görüldüğü gibi rotayı (ör. /) bir closure ile eşleştirmektir.

**Örnek 3-1**. Temel rota tanımı

```
// routes/web.php
Route::get('/', function () {
    return 'Hello, World!';
});
```

**Closure Nedir?**

Closure’lar, anonim fonksiyonların PHP versiyonudur. Closure, nesne olarak iletebileceğiniz, bir değişkene atayabileceğiniz, diğer fonksiyon ve metotlara parametre olarak iletebileceğiniz ve hatta serialize edilebilen bir işlevdir.

Artık herhangi bir kullanıcı / (etki alanınızın kökünü) ziyaret ederse, Laravel'in router’ının orada tanımlanan closure’ı çalıştırması ve sonucu döndürmesi gerektiğini tanımladınız.

**Middleware’a Hızlı Bakış**

Merak ediyor olabilirsiniz, "Neden onu yazdırmak yerine ‘Hello, World!’ olarak geri dönüyorum?”

Epeyce cevap var, ancak en basit olanı, Laravel'in request ve response döngüsü etrafında, middleware adı verilen şey de dahil olmak üzere çok sayıda wrapper (sarmalayıcı) olmasıdır. Rota closure veya controller metodumuz tamamlandığında, çıktıyı tarayıcıya gönderme zamanı henüz gelmemiştir; içeriğin döndürülmesi, kullanıcıya geri gönderilmeden önce response yığınından ve middleware’den akmaya devam etmesine olanak tanır.

Pek çok basit web sitesi tamamen routes içinde tanımlanabilir. Örnek 3-2'de gösterildiği gibi, bazı template’ler ile birleştirilmiş birkaç basit GET rotası ile klasik bir web sitesine kolayca hizmet verebilirsiniz.

**Örnek 3-2**. Örnek web sitesi

```
Route::get('/', function () {
    return view('welcome');
});

Route::get('about', function () {
    return view('about');
});

Route::get('products', function () {
    return view('products');
});

Route::get('services', function () {
    return view('services');
});
```

**Static Çağrılar**

PHP ile geliştirme konusunda deneyiminiz varsa, Route sınıfında static çağrılar görmek sizi şaşırtabilir. Bu aslında kendi başına static bir metot değil, daha ziyade Bölüm 11'de ele alacağımız Laravel'in facade’lerini kullanan hizmet konumudur. Facade’lerden kaçınmayı tercih ederseniz, aynı tanımları şu şekilde yapabilirsiniz:

```
$router->get('/', function () {
    return 'Hello, World!';
});
```

**Rota Fiilleri**

Rota tanımlarımızda Route::get() kullandığımızı fark etmişsinizdir. Bu, Laravel'e yalnızca HTTP request’i GET eylemini kullandığında bu rotalar için eşleşmesini söylediğimiz anlamına gelir. Peki ya bu form POST ise veya PUT veya DELETE istekleri gönderen bir JavaScript ise? Örnek 3-3'te gösterildiği gibi, rota tanımında çağrılacak metotlar için birkaç seçenek daha vardır.

**Örnek 3-3**. Rota fiilleri

```
Route::get('/', function () {
    return 'Hello, World!';
});

Route::post('/', function () {
    // Handle someone sending a POST request to this route
});

Route::put('/', function () {
    // Handle someone sending a PUT request to this route
});

Route::delete('/', function () {
    // Handle someone sending a DELETE request to this route
});

Route::any('/', function () {
    // Handle any verb request to this route
});

Route::match(['get', 'post'], '/', function () {
    // Handle GET or POST requests to this route
});
```

**Rota İşleme**

Tahmin ettiğiniz gibi, bir rotanın nasıl çözümleneceğini öğretmenin tek yolu rota tanımına closure geçirmek değildir. Closure’lar hızlı ve basittir, ancak uygulamanız büyüdükçe, tüm yönlendirme mantığınızı tek bir dosyaya koymak kullanışsız hale gelir. Ek olarak, rota closure’larını kullanan uygulamalar, Laravel'in her istekten yüzlerce milisaniyeye kadar tasarruf sağlayabilen rota önbelleğinden yararlanamaz (bununla ilgili daha sonra konuşacağız).

Diğer yaygın seçenek, Örnek 3-4'te olduğu gibi, bir controller adını ve metodunu dize olarak iletmektir.

**Örnek 3-4**. Controller metotlarını çağıran rotalar

```
use App\Http\Controllers\WelcomeController;

Route::get('/', [WelcomeController::class, 'index']);
```

Bu, Laravel'e gelen istekleri App\Http\Controllers\WelcomeController controller’ının index() metoduna iletmesini söylüyor.

**Rota Parametreleri**

Tanımladığınız rotanın parametreleri (URL yapısında değişken olan segmentler) varsa, bunları rotanızda tanımlamak ve closure’a iletmek kolaydır (bkz. Örnek 3-5).

**Örnek 3-5**. Rota parametreleri

```
Route::get('users/{id}/friends', function ($id) {
    //
});
```

Örnek 3-6'da gösterildiği gibi, parametre adından sonra soru işareti (?) ekleyerek rota parametrelerinizi isteğe bağlı yapabilirsiniz. Bu durumda, rotanın karşılık gelen değişkeni için varsayılan değer de sağlamalısınız.

**Örnek 3-6**. İsteğe bağlı rota parametreleri

```
Route::get('users/{id?}', function ($id = 'fallbackId') {
    //
});
```

Örnek 3-7'de olduğu gibi, rotanın yalnızca bir parametrenin belirli gereksinimleri karşılaması durumunda eşleşmesi gerektiğini tanımlamak için regular expressions (regex) kullanabilirsiniz.

**Örnek 3-7**. Regex rota kısıtlamaları

```
Route::get('users/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

Route::get('users/{username}', function ($username) {
    //
})->where('username', '[A-Za-z]+');

Route::get('posts/{id}/{slug}', function ($id, $slug) {
    //
})->where(['id' => '[0-9]+', 'slug' => '[A-Za-z]+']);
```

Tahmin ettiğiniz gibi, rota dizesiyle eşleşen bir yolu ziyaret ederseniz, ancak regex  parametreyle eşleşmezse, eşleştirilmeyecektir. Rotalar yukarıdan aşağıya eşlendiğinden, users/abc Örnek 3-7'deki ilk closure’u atlar, ancak ikinci closure ile eşleştirilir, böylece oraya yönlendirilir. Öte yandan, posts/abc/123 closure’ların hiçbiriyle eşleşmez, dolayısıyla 404 (Not Found) hatası verir.

**Örnek 3-8**. Regex rota kısıtlama yardımcıları

```
Route::get('users/{id}/friends/{friendname}', function ($id, $friendname) {
    //
})->whereNumber('id')->whereAlpha('friendname');

Route::get('users/{name}', function ($name) {
    //
})->whereAlphaNumeric('name');

Route::get('users/{id}', function ($id) {
    //
})->whereUuid('id');

Route::get('users/{id}', function ($id) {
    //
})->whereUlid('id');

Route::get('friends/types/{type}', function ($type) {
    //
})->whereIn('type', ['acquaintance', 'bestie', 'frenemy']);
```

**Rota parametreleri ile closure/controller metot parametreleri arasındaki ilişki**

Örnek 3-5'te görebileceğiniz gibi, rota parametreleriniz ({id}) ve rota tanımınıza enjekte ettikleri metot parametreleri (function ($id)) için aynı adları kullanmak en yaygın olanıdır. Ama bu gerekli mi?

Bu bölümde daha sonra ele alınan route model binding’i kullanmıyorsanız, hayır. Burada görebileceğiniz gibi, hangi rota parametresinin hangi metot parametresiyle eşleştiğini tanımlayan tek şey bunların sırasıdır (soldan sağa):

```
Route::get('users/{userId}/comments/{commentId}', function (
    $thisIsActuallyTheUserId,
    $thisIsReallyTheCommentId
) {
    //
});
```

Bunları farklı yapabiliyor olmanız, yapmanız gerektiği anlamına gelmez. Tutarsız adlandırma nedeniyle tökezleyebilecek gelecekteki geliştiricilerin iyiliği için onları aynı tutmanızı tavsiye ederim.

**Rota Adları**

Uygulamanızın herhangi bir yerinde rotalara atıfta bulunmanın en basit yolu, path’lerine bakmaktır. Gerekirse, view’da bu linki basitleştirmek için url() global helper kullanabilirsiniz; örnek için bkz. Örnek 3-9. Helper, rotanızın önüne sitenizin tam alan adını koyacaktır.

**Örnek 3-9**. url() helper

```
<a href="<?php echo url('/'); ?>">
// Outputs <a href="http://myapp.com/">
```

Ancak Laravel, URL'ye açıkça atıfta bulunmadan ona başvurmanıza olanak tanıyan her bir rotayı adlandırmanıza da izin verir. Bu yararlıdır, çünkü karmaşık rotalara basit takma adlar verebilirsiniz ve ayrıca bunları ada göre bağlamak, yollar değişirse frontend linklerinizi yeniden yazmak zorunda kalmayacağınız anlamına gelir (bkz. Örnek 3-10).

**Örnek 3-10**. Rota adlarını tanımlamak

```
// Defining a route with name() in routes/web.php:
Route::get('members/{id}', [\App\Http\Controller\MemberController::class, 'show'])
    ->name('members.show');
    
// Linking the route in a view using the route() helper:
<a href="<?php echo route('members.show', ['id' => 14]); ?>">
```

Bu metot, başka bir yere başvurmayı kolaylaştırmak için kısa bir takma ad vererek rotayı adlandırmamıza izin verir. Örneğimizde, bu rotayı members.show olarak adlandırdık; ResourcePlural.action, route ve view adları için Laravel içinde yaygın olarak kullanılan bir konvansiyondur.

**Rota İsimlendirme Konvansiyonları**

Rotanızı istediğiniz gibi adlandırabilirsiniz, ancak ortak konvansiyon, resource adının çoğulunun, ardından noktanın ve ardından eylemin kullanılmasıdır. Yani, photo adlı bir resource için en yaygın rotalar şunlardır:

```
photos.index
photos.create
photos.store
photos.show
photos.edit
photos.update
photos.destroy
```

Bu kurallar hakkında daha fazla bilgi edinmek için bkz. "Resource Controller’ları".

Bu örnek ayrıca route() helper’ı da tanıttı. Tıpkı url() gibi, adlandırılmış bir rotaya bağlanmayı basitleştirmek için view’da kullanılması amaçlanmıştır. Rotanın parametresi yoksa, rota adını (route('members.index')) iletebilir ve bir rota dizesi ("http://myapp.com/members") alabilirsiniz. Parametreleri varsa, bunları Örnek 3-10'da yaptığımız gibi ikinci parametre olarak iletin.

Genel olarak, rotalarınıza başvurmak için path yerine rota adlarını kullanmanızı ve bu nedenle url() helper yerine route() helper kullanmanızı öneririm. Bazen biraz kullanışsız olabilir - örneğin, birden fazla alt alanla çalışıyorsanız - ancak daha sonra uygulamanın yönlendirme yapısını büyük bir maliyet olmadan değiştirmek için inanılmaz bir esneklik sağlar.

**Route Helper’a Rota Parametrelerini İletmek**

Rotanız parametrelere sahip olduğunda (ör. users/id), rotaya link oluşturmak için route() helper kullanırken bu parametreleri tanımlamanız gerekir.

Bu parametreleri iletmenin birkaç farklı yolu vardır. users/userId/comments/commentId olarak tanımlanan bir rota düşünelim. User ID 1 ve comment ID 2 ise elimizdeki birkaç seçeneğe bakalım:

**Seçenek 1:**
```
route('users.comments.show', [1, 2])
// http://myapp.com/users/1/comments/2
```

**Seçenek 2:**
```
route('users.comments.show', ['userId' => 1, 'commentId' => 2])
// http://myapp.com/users/1/comments/2
```

**Seçenek 3:**
```
route('users.comments.show', ['commentId' => 2, 'userId' => 1])
// http://myapp.com/users/1/comments/2
```

**Seçenek 4:**
```
route('users.comments.show', ['userId' => 1, 'commentId' => 2, 'opt' => 'a'])
// http://myapp.com/users/1/comments/2?opt=a
```

Gördüğünüz gibi, anahtarsız dizi değerleri sırayla atanır; anahtarlı dizi değerleri, anahtarlarıyla eşleşen rota parametreleriyle eşleştirilir ve geriye kalan her şey query parametresi olarak eklenir.

## Rota Grupları

Genellikle rota grupları belirli bir özelliği paylaşır; kimlik doğrulama gereksinimi, rota öneki veya belki bir controller namespace. Bu paylaşılan özellikleri her rotada tekrar tekrar tanımlamak sıkıcı görünmekle kalmaz, aynı zamanda rota dosyanızın şeklini anlaşılması zor hale getirebilir ve uygulamanızın bazı yapılarını belirsizleştirebilir. 

Rota grupları, birkaç rotayı birlikte gruplamanıza ve yinelemeyi azaltmak için tüm paylaşılan yapılandırma ayarlarını grubun tamamına bir kez uygulamanıza olanak tanır. Ek olarak, rota grupları, gelecekteki geliştiricilere (ve kendi beyninize) bu rotaların birlikte gruplandırıldığına dair görsel ipuçları verir.

İki veya daha fazla rotayı birlikte gruplandırmak için, Örnek 3-11'de gösterildiği gibi rota tanımlarını bir rota grubuyla "sarmalarsınız". Gerçekte, aslında grup tanımına bir closure iletiyorsunuz ve bu closure içinde gruplandırılmış rotaları tanımlıyorsunuz.

**Örnek 3-11**. Rota grubu tanımlama

```
Route::group(function () {
    Route::get('hello', function () {
        return 'Hello';
    });
    Route::get('world', function () {
        return 'World';
    });
});
```

Varsayılan olarak, rota grubu aslında hiçbir şey yapmaz. Örnek 3-11'deki grubu kullanmak ile rotalarınızın bir bölümünü kod açıklamalarıyla ayırmak arasında hiçbir fark yoktur.

**Middleware**

Muhtemelen rota gruplarının en yaygın kullanımı, middleware’i bir rota grubuna uygulamaktır. Bölüm 10'da middleware hakkında daha fazla bilgi edineceksiniz, ancak bunlar, diğer şeylerin yanı sıra, Laravel'in kullanıcıların kimliğini doğrulamak ve misafir kullanıcıların sitenin belirli bölümlerini kullanmasını kısıtlamak için kullandığı yazılımlardır.

Örnek 3-12'de, dashboard ve account view’ları çevresinde bir rota grubu oluşturuyoruz ve auth middleware’i her ikisine de uyguluyoruz. Bu örnekte, kullanıcıların dashboard veya account sayfasını görüntülemek için uygulamada oturum açması gerekir.

**Örnek 3-12**. Bir grup rotayı yalnızca oturum açmış kullanıcılarla kısıtlama

```
Route::middleware('auth')->group(function () {
    Route::get('dashboard', function () {
        return view('dashboard');
    });
    Route::get('account', function () {
        return view('account');
    });
});
```

**Controller’lara Middleware Uygulamak**

Rota tanımı yerine controller’a middleware eklemek genellikle daha net ve doğrudandır. Bunu, controller kurucusunda middleware() metodunu çağırarak yapabilirsiniz. middleware() metoduna ilettiğiniz dize, middleware’in adıdır ve isteğe bağlı olarak, bu middleware’i hangi metotların alacağını tanımlamak için modifier metotları (only() ve except()) zincirleyebilirsiniz:

```
class DashboardController extends Controller
{
    public function __construct()
    {
        $this->middleware('auth');
        $this->middleware('admin-auth')
            ->only('editUsers');
        $this->middleware('team-member')
            ->except('editUsers');
    }
}
```

Çok sayıda "only" ve "except" özelleştirmeleri yapıyorsanız, bunun genellikle istisnai rotalar için yeni bir controller oluşturmanız gerektiğinin bir işareti olduğunu unutmayın.

**Eloquent’e Kısa Bir Bakış**

Eloquent'i, veritabanı erişimini ve Laravel'in query builder’ını Bölüm 5'te derinlemesine ele alacağız, ancak şimdi temel bir kavrayış faydalı olacaktır.

Eloquent, Laravel'in ORM’idir (Object-Relational Mapper), bir Post sınıfını (modelini) posts tablosuyla ilişkilendirmeyi ve Post::all() gibi bir çağrıyla tüm kayıtları almayı kolaylaştırır.

**Path (Yol) Prefix’leri**

Path’lerin bir bölümünü paylaşan bir rota grubunuz varsa (örneğin, sitenizin kontrol paneline /dashboard ön eki verilmişse), bu yapıyı basitleştirmek için rota gruplarını kullanabilirsiniz (bkz. Örnek 3-13).

**Örnek 3-13**. Rota grubunu prefix’leme

```
Route::prefix('dashboard')->group(function () {
    Route::get('/', function () {
        // Handles the path /dashboard
    });
    Route::get('users', function () {
        // Handles the path /dashboard/users
    });
});
```
Her prefix grubunun ayrıca prefix’in kökünü temsil eden bir / rotasına sahip olduğunu unutmayın; Örnek 3-13'te bu /dashboard'dur.

**Subdomain Yönlendirmesi**

Subdomain yönlendirmesi, rota prefix’i ile aynıdır ancak kapsamı rota prefix’i yerine subdomain tarafından belirlenir. Bunun iki temel kullanımı vardır. İlk olarak, uygulamanın farklı bölümlerini (veya tamamen farklı uygulamaları) farklı subdomain’lerde sunmak isteyebilirsiniz. Örnek 3-14 bunu nasıl yapabileceğinizi göstermektedir.

**Örnek 3-14**. Subdomain yönlendirme

```
Route::domain('api.myapp.com')->group(function () {
    Route::get('/', function () {
        //
    });
});
```

İkinci olarak, Örnek 3-15'te gösterildiği gibi subdomain’in bir kısmını parametre olarak kullanmak isteyebilirsiniz. Bu çoğunlukla multitenancy durumlarında yapılır (Slack veya Harvest'i düşünün; burada her şirket, tighten.slack.co gibi kendi subdomain’ini alır).

**Örnek 3-15**. Parametreli subdomain yönlendirmesi

```
Route::domain('{account}.myapp.com')->group(function () {
    Route::get('/', function ($account) {
        //
    });
    Route::get('users/{id}', function ($account, $id) {
        //
    });
});
```

Grup için herhangi bir parametrenin, gruplandırılmış rotaların metotlarına ilk parametre(ler) olarak aktarıldığını unutmayın.

**Ad Prefix’leri**

Rota adlarının miras zincirini yansıtması yaygındır; bu nedenle, users/comments/5, users.comments.show adlı bir rota tarafından sunulur. Bu durumda, user.comments kaynağının altındaki tüm rotaların etrafında bir rota grubu kullanmak yaygın bir uygulamadır.

Tıpkı URL segmentlerini prefix lediğimiz gibi, dizeleri de rota adına prefix olarak ekleyebiliriz. Rota grubu adı prefix’iyle, bu gruptaki her rotanın adının önünde belirli bir dize olması gerektiğini tanımlayabiliriz. Bu bağlamda her rota adının önüne "users.", ardından "comments." koyuyoruz. (bkz. Örnek 3-16).

**Örnek 3-16**. Rota grubu adı prefixleri

```
Route::name('users.')->prefix('users')->group(function () {
    Route::name('comments.')->prefix('comments')->group(function () {
        Route::get('{id}', function () {
            // ...
        })->name('show'); // Route named 'users.comments.show'
        Route::destroy('{id}', function () {
        })->name('destroy');
    });
});
```

**Rota Grubu Controller’ı**

Aynı controller tarafından sunulan rotaları gruplarken, örneğin kullanıcıları gösterirken, düzenlerken ve silerken, rota grubu controller() metodunu kullanabiliriz.

**Örnek 3-17**. Rota grubu controller’ı

```
use App\Http\Controllers\UserController;

Route::controller(UserController::class)->group(function () {
    Route::get('/', 'index');
    Route::get('{id}', 'show');
});
```

**Fallback Rotaları**

Laravel'de eşleşmeyen tüm istekleri yakalamak için bir "fallback route" (rota dosyanızın sonunda tanımlamanız gereken) tanımlayabilirsiniz:

```
Route::fallback(function () {
    //
});
```

## İmzalı Rotalar

Birçok uygulama düzenli olarak tek seferlik eylemler (şifre sıfırlama, davet kabul etme vb.) hakkında bildirimler gönderir ve bu eylemlerin gerçekleştirilmesi için basit linkler sağlar. Alıcının e-posta listesine kayıt olmak istediğini onaylayan bir e-posta gönderdiğimizi hayal edelim.

Bu linki göndermenin üç yolu vardır:

- Bu URL'i herkese açık hale getirin ve başka hiç kimsenin onay URL'sini keşfetmemesini umun.
- Eylemi kimlik doğrulamanın arkasına yerleştirin, eyleme link verin ve henüz oturum açmadıysa kullanıcının oturum açmasını zorunlu kılın (bu durumda, birçok kullanıcı üye olmadığı için bu mümkün olmayabilir).
- Linki, kullanıcının oturum açmasına gerek kalmadan e-postasından linki aldığını benzersiz bir şekilde kanıtlayacak şekilde "imzalayın"; şunun gibi http://myapp.com/invitations/5816/yes?signature=030ab0ef6a8237bd86a8b8.

Son seçeneği gerçekleştirmenin basit bir yolu, signed URL adı verilen ve kimliği doğrulanmış linkler göndermek için imza kimlik doğrulama sistemi oluşturmayı kolaylaştıran bir özelliği kullanmaktır. Bu linkler, URL'in gönderildikten sonra değiştirilmediğini (ve dolayısıyla hiç kimsenin bir başkasının bilgisine erişmek için URL'i değiştirmediğini) kanıtlayan bir "imza" eklenmiş normal rota linkinden oluşur.

**Rota İmzalama**

Belirli bir rotaya erişmek amacıyla imzalı URL oluşturmak için rotanın bir adı olmalıdır:

```
Route::get('invitations/{invitation}/{answer}', InvitationController::class)
    ->name('invitations');
```

Bu rotaya link oluşturmak için, daha önce ele aldığımız gibi, route() helper kullanabilirsiniz, ancak aynı şeyi yapmak için URL facade kullanabilirsiniz: URL::route('invitations', ['invitation' => 12345, 'answer' =>'yes']). Bu rotaya imzalı bir link oluşturmak için bunun yerine signedRoute() metodunu kullanmanız yeterlidir. Süresi dolan imzalı bir rota oluşturmak istiyorsanız, temporarySignedRoute() metodunu kullanın:

```
// Generate a normal link
URL::route('invitations', ['invitation' => 12345, 'answer' => 'yes']);

// Generate a signed link
URL::signedRoute('invitations', ['invitation' => 12345, 'answer' => 'yes']);

// Generate an expiring (temporary) signed link
URL::temporarySignedRoute(
    'invitations',
    now()->addHours(4),
    ['invitation' => 12345, 'answer' => 'yes']
);
```

**now() Helper Kullanımı**

Laravel, Carbon::now()'a eşdeğer bir now() helper sunmaktadır; tam bu anda, bugünün bir Carbon nesnesini döndürür.

Eğer aşina değilseniz Carbon, Laravel'e dahil olan bir tarih-saat kütüphanesidir.

**İmzalı Linklere İzin Vermek İçin Rotaları Düzenlemek**

Artık imzalı rotanıza bir link oluşturduğunuza göre, imzasız erişimlere karşı koruma sağlamanız gerekir. En kolay seçenek, signed middleware uygulamaktır.

```
Route::get('invitations/{invitation}/{answer}', InvitationController::class)
    ->name('invitations')
    ->middleware('signed');
```

İsterseniz signed middleware kullanmak yerine, Request nesnesindeki hasValidSignature() metodunu kullanarak manuel olarak doğrulama yapabilirsiniz:

```
class InvitationController
{
    public function __invoke(Invitation $invitation, $answer, Request $request)
    {
        if (!$request->hasValidSignature()) {
            abort(403);
        }
        //
    }
}
```

## View

Şu ana kadar incelediğimiz rota closure’larının birkaçında, view('account')‘a benzer şeyler gördük. Burada neler oluyor?

MVC modelinde, view (veya template), belirli bir çıktının nasıl görünmesi gerektiğini açıklayan dosyadır. JSON, XML veya e-mail için view’larınız olabilir, ancak bir web framework’teki en yaygın view’lar HTML çıktısı verir.

Laravel'de kullanabileceğiniz iki view formatı vardır: düz PHP veya Blade template. Aradaki fark dosya adındadır: about.php PHP motoruyla işlenecek ve about.blade.php Blade motoruyla işlenecektir.

**View Yüklemenin Üç Yolu**

Bir görünümü döndürmenin üç farklı yolu vardır. Şimdilik sadece view() ile ilgilenin, ancak View::make() görürseniz, bu aynı şeydir veya isterseniz Illuminate\View\ViewFactory'i enjekte edebilirsiniz.

view() helper ile bir view’ı "yükledikten" sonra, onu basitçe geri döndürebilirsiniz (Örnek 3-18'de olduğu gibi), bu, eğer view controller’dan gelen herhangi bir değişkene dayanmıyorsa düzgün çalışacaktır.

**Örnek 3-18**. Basit view() kullanımı

```
Route::get('/', function () {
    return view('home');
});
```

Bu kod, resources/views/home.blade.php veya resources/views/home.php dosyasında bir view arar ve içeriğini yükler ve view çıktısını elde edene kadar tüm satır içi PHP veya kontrol yapılarını ayrıştırır. Geri gönderdiğinizde response yığınına aktarılır ve sonunda kullanıcıya iletilir.

Peki ya değişkenleri aktarmanız gerekirse? Örnek 3-19'a göz atın.

**Örnek 3-19**. Değişkenleri view’a aktarma

```
Route::get('tasks', function () {
    return view('tasks.index')
        ->with('tasks', Task::all());
});
```

Bu closure, resources/views/tasks/index.blade.php view’ı yükler ve ona Task::all() metodunun sonucunu içeren, tasks adlı değişkeni iletir.

**Basit Rotaları Route::view() ile Doğrudan Döndürme**

Bir rotanın hiçbir özel veri olmadan yalnızca bir view döndürmesi çok yaygın olduğundan, Laravel, rota tanımına bir closure veya controller/metot referansı bile geçirmeden, rotayı "view" rotası olarak tanımlamanıza izin verir. Örnek 3-20.

**Örnek 3-20**. Route::view()

```
// Returns resources/views/welcome.blade.php
Route::view('/', 'welcome');

// Passing simple data to Route::view()
Route::view('/', 'welcome', ['User' => 'Michael']);
```

**Değişkenleri Tüm View’lar ile Paylaşmak için View Composer Kullanma**

Bazen aynı değişkenleri tekrar tekrar iletmek güçlük yaratabilir. Sitedeki her view için veya belirli bir view sınıfı veya dahil edilen belirli bir subview için (örneğin, tasks ilgili tüm view’lar veya header kısmı) erişilmesini istediğiniz bir değişken olabilir.

Aşağıdaki kodda olduğu gibi belirli değişkenleri her template ile veya yalnızca belirli template’ler ile paylaşmak mümkündür:

```
view()->share('variableName', 'variableValue');
```

## Controller

Controller’lar dan birkaç kez bahsettim ancak şu ana kadar örneklerin çoğunda rota closure’ları görüldü. MVC pattern’in de controller’lar esas olarak bir veya daha fazla rotanın mantığını tek bir yerde birlikte düzenleyen sınıflardır. Controller’lar, özellikle uygulamanız geleneksel olarak CRUD benzeri bir formatta yapılandırılmışsa, benzer rotaları bir arada gruplandırma eğilimindedir; Bu durumda, controller belirli bir resource üzerinde gerçekleştirilebilecek tüm eylemleri gerçekleştirebilir.

**CRUD Nedir?**

CRUD, web uygulamalarının bir resource’ta en yaygın olarak sağladığı dört temel işlem olan create, read, update, delete anlamına gelir. Örneğin yeni bir blog yazısı oluşturabilir, o yazıyı okuyabilir, güncelleyebilir veya silebilirsiniz.

Uygulamanın tüm mantığını controller’a sıkıştırmak cazip gelebilir, ancak controller’ları uygulamanızın etrafında HTTP isteklerini yönlendiren trafik polisleri olarak düşünmek daha iyidir. İsteklerin uygulamanıza gelebileceği başka yollar da (cron jobs, Artisan komut satırı çağrıları, queue jobs vb.) olduğundan, çok fazla davranış için controller’a güvenmemek akıllıca olacaktır. Bu, bir controller’ın birincil işinin HTTP isteğinin amacını yakalamak ve onu uygulamanın geri kalanına iletmek olduğu anlamına gelir.

O halde bir controller oluşturalım. Bunu yapmanın kolay bir yolu Artisan komutunu kullanmaktır; terminal’de aşağıdaki komutu çalıştırın:

```
php artisan make:controller TaskController
```

**Artisan**

Laravel, Artisan adlı bir komut satırı aracıyla birlikte gelir. Artisan, migration’ları yürütmek, kullanıcıları ve diğer veritabanı kayıtlarını manuel olarak oluşturmak ve diğer birçok manuel, tek seferlik görevi gerçekleştirmek için kullanılabilir. 

make namespace altında Artisan, çeşitli sistem dosyaları için iskelet dosyalar oluşturmaya yönelik araçlar sağlar.

Bu, örnek 3-21'de gösterilen içeriğe sahip, app/Http/Controllers'da TaskController.php adında yeni bir dosya oluşturacaktır.

**Örnek 3-21**. Default olarak oluşturulan controller

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class TaskController extends Controller
{
    //
}
```

Bu dosyayı Örnek 3-22'de gösterildiği gibi değiştirin ve index() adı verilen yeni bir public metot oluşturun. Burada sadece bir metin döndüreceğiz.

**Örnek 3-22**. Basit controller örneği

```
<?php

namespace App\Http\Controllers;

class TaskController extends Controller
{
    public function index()
    {
        return 'Hello, World!';
    }
}
```

Daha sonra, Örnek 3-23'te gösterildiği gibi ona bir rota bağlayacağız.

**Örnek 3-23**. Basit controller için rota

```
// routes/web.php
<?php

Route::get('/', [TaskController::class, 'index']);
```

Bu kadar. / rotasını ziyaret ettiğinizde “Hello, World!” kelimelerini göreceksiniz.

O halde bir controller metodunun en yaygın kullanımı, Örnek 3-19'daki route closure ile aynı işlevselliği sağlayan Örnek 3-24 gibi bir şey olacaktır.

**Örnek 3-24**. Ortak controller metodu örneği

```
// TaskController.php
...
public function index()
{
    return view('tasks.index')
        ->with('tasks', Task::all());
}
```

Bu controller metodu, resources/views/tasks/index.blade.php view’ını yükler ve ona, Task::all() Eloquent metodunun sonucunu içeren, tasks adlı değişkeni iletir.

**Resource Controller Oluşturmak**

create() ve update() gibi tüm temel rotaları otomatik olarak oluşturulmuş metotlara sahip bir resource controller oluşturmak istiyorsanız, php artisan make:controller'ı kullanırken --resource flag’ini iletebilirsiniz:

```
php artisan make:controller TaskController --resource
```

**Kullanıcıdan Girdi Almak**

Bir controller metodunda gerçekleştirilecek en yaygın ikinci eylem, kullanıcıdan girdi almak ve buna göre hareket etmektir. Birkaç yeni konsepti göreceğiz, o yüzden örnek koda bakalım ve yeni parçaları gözden geçirelim.

**Örnek 3-25**. Temel form action’larını bağlama

```
// routes/web.php
Route::get('tasks/create', [TaskController::class, 'create']);
Route::post('tasks', [TaskController::class, 'store']);
```

tasks/create (yeni bir task oluşturmak için form gösterir) GET action’ını ve tasks/ (yeni bir task oluşturduğumuzda formun gönderileceği yer burasıdır) POST action’ını bağladığımıza dikkat edin. create() metodunun yalnızca bir form gösterdiğini varsayabiliriz, o halde Örnek 3-26'daki store() metoduna bakalım.

**Örnek 3-26**. Form giriş controller metodu

```
// TaskController.php
...
public function store()
{
    Task::create(request()->only(['title', 'description']));
    return redirect('tasks');
}
```

Bu örnekte Eloquent modelleri ve redirect() kullanılmaktadır ve bunlar hakkında daha sonra konuşacağız, şimdilik verilerimizi buraya nasıl aldığımız hakkında konuşalım.

HTTP isteğini temsil etmek için request() helper kullanıyoruz (bu konuya daha sonra değineceğiz) ve yalnızca kullanıcının gönderdiği title ve description alanlarını çekmek için only() metodunu kullanıyoruz.

Daha sonra bu verileri Task modelimizin create() metoduna aktarıyoruz; bu, Task’in yeni bir örneğini oluşturur. Son olarak tüm task’leri gösteren sayfaya yönlendiriliyoruz.

Burada, birazdan ele alacağımız birkaç soyutlama katmanı iş başındadır, ancak only() metodundan gelen verilerin, request nesnesinde kullanılan tüm yaygın metotların - all() ve get() dahil - aynı havuzundan geldiğini bilin. Bu metotların her birinin çektiği data - query parametreleri veya POST değerleri - kullanıcı tarafından sağlanan tüm verileri temsil eder. Böylece kullanıcımız "add task" sayfasında iki alanı doldurdu: “title” ve “description”.

Soyutlamayı kırmak için, request()->only() girdi (input) adlarının ilişkisel bir dizisini alır ve bunları döndürür:

```
request()->only(['title', 'description']);
// returns:
[
    'title' => 'Whatever title the user typed on the previous page',
    'description' => 'Whatever description the user typed on the previous page',
]
```

Ve Task::create() ilişkisel bir dizi alır ve ondan yeni bir task oluşturur:

```
Task::create([
    'title' => 'Buy milk',
    'description' => 'Remember to check the expiration date this time, Norbert!',
]);
```

Bunları bir araya getirmek, kullanıcı tarafından sağlanan “title” ve “description” alanlarını içeren bir task oluşturur.

**Bağımlılıkları Controller’lara Eklemek**

Facade ve global helper’lar, Laravel'in kod tabanındaki en kullanışlı sınıflara basit bir arayüz sunar. Mevcut istek ve kullanıcı girişi, session, cache ve çok daha fazlası hakkında bilgi alabilirsiniz.

Ancak bağımlılıklarınızı enjekte etmeyi tercih ediyorsanız veya facade ya da helper’ı olmayan bir servis kullanmak istiyorsanız, bu sınıfların örneklerini controller’ınıza getirmenin bir yolunu bulmanız gerekir.

Bu, Laravel'in servis container’ı ilk karşılaşmamız. Şimdilik, eğer bu konu size tanıdık gelmiyorsa, bunu biraz Laravel büyüsü olarak düşünebilirsiniz; veya gerçekte nasıl çalıştığı hakkında daha fazla bilgi edinmek istiyorsanız Bölüm 11'e geçebilirsiniz.

Tüm controller metotları (kurucular dahil) Laravel'in container’ı dışında çözümlenir; bu, container’ın nasıl çözüleceğini bildiğini düşündüğünüz her şeyin otomatik olarak enjekte edileceği anlamına gelir.

**Typehinting**

PHP'de “Typehinting”, bir sınıfın veya arayüzün adını, yöntem imzasındaki bir değişkenin önüne koymak anlamına gelir.

```
public function __construct(Logger $logger) {}
```

Bu ipucu PHP'ye, metoda iletilen her şeyin arayüz ya da sınıf olabilecek Logger türünde olması gerektiğini söyler.

Bir örnek olarak, global helper kullanmak yerine Request nesnesinin örneğini kullanmayı tercih ederseniz ne olur? Örnek 3-27'deki gibi, metot parametrelerinize Illuminate\Http\Request yazmanız yeterlidir.

**Örnek 3-27**. Typehinting ile controller metodu enjeksiyonu

```
// TaskController.php
...
public function store(\Illuminate\Http\Request $request)
{
    Task::create($request->only(['title', 'description']));
    return redirect('tasks');
}
```

Böylece, store() metoduna iletilmesi gereken bir parametre tanımladınız. Bunu siz yazdığınız için ve Laravel bu sınıf adını nasıl çözümleyeceğini bildiği için, Request nesnesi metodunuzda kullanılmak üzere hazır hale gelir. Açık bir binding yok, başka bir şey yok; $request değişkeni olarak kullanıma hazırdır.

Örnek 3-26 ile Örnek 3-27'yi karşılaştırdığınızda, request() helper ve Request nesnesinin tam olarak aynı şekilde davrandığını görebilirsiniz.

**Resource Controller**

Bazen controller’daki metotları adlandırmak, controller yazmanın en zor kısmı olabilir. Neyse ki Laravel, geleneksel REST/CRUD controller (Laravel'de “resource controller” olarak adlandırılır) rotaları için bazı kurallara sahiptir; ek olarak, bir kod generator ve tüm resource controller’ı aynı anda bağlamanıza olanak tanıyan uygun bir rota tanımıyla birlikte gelir.

Laravel'in resource controller’dan beklediği metotları görmek için komut satırından yeni bir controller oluşturalım:

```
php artisan make:controller MySampleResourceController --resource
```

Şimdi app/Http/Controllers/MySampleResourceController.php dosyasını açın. Pek çok metotla önceden doldurulmuş olarak geldiğini göreceksiniz. Her birinin neyi temsil ettiğini gözden geçirelim. Örnek olarak Task kullanacağız.

**Resource Controller Metotları**

Tablo 3-1’i hatırlıyor musunuz?, Laravel'in resource controller’larında oluşturulan HTTP fiilini, URL'i, controller metot adını ve bu varsayılan metotların her birinin adını gösterir.

**Resource Controller’ı Bağlamak**

Bunların Laravel'de kullanılacak konvansiyonel rota adları olduğunu ve ayrıca bu varsayılan rotaların her biri için metotlar içeren bir resource controller oluşturmanın kolay olduğunu gördük. Neyse ki, istemiyorsanız bu controller metotlarının her biri için elle rota oluşturmanıza gerek yok. Bunun için resource controller binding adı verilen bir numara var. Örnek 3-28'e bir göz atın.

**Example 3-28**. Resource controller binding

```
// routes/web.php
Route::resource('tasks', TaskController::class);
```

**artisan route:list**

Kendinizi mevcut uygulamanızın hangi rotalara sahip olduğunu merak ettiğiniz bir durumda bulursanız, bunun için bir araç kullanabilirsiniz: komut satırından, php artisan route:list komutunu çalıştırdığınızda tüm rotaların listesini göreceksiniz (bkz. Şekil 3-2). php artisan route:list --except-vendor seçeneğini tercih ediyorum, böylece bağımlılıkların çalışması için kaydettiği tuhaf rotaları görmüyorum.

![php artisan route:list](/assets/images/2024/03/php-artisan-route-list.png "php artisan route:list"){: class="img-fluid"}

**API Resource Controller**

RESTful API'lerle çalışırken, resource’taki potansiyel action’ların listesi, HTML resource controller’daki ile aynı değildir. Örneğin, resource oluşturmak için API'ye POST isteği gönderebilirsiniz, ancak API'de gerçekten "bir formu gösteremezsiniz".

create ve edit action’larını hariç tutması dışında, aynı yapıya sahip bir controller olan API resource controller oluşturmak için, controller oluştururken --api flag’ini iletin:

```
php artisan make:controller MySampleResourceController --api
```

**API Resource Controller’ı Bağlamak**

API resource controller’ı bağlamak için, Örnek 3-29'da gösterildiği gibi, resource() metodu yerine apiResource() metodunu kullanın.

**Örnek 3-29**. API resource controller’ın bağlanması

```
// routes/web.php
Route::apiResource('tasks', TaskController::class);
```

**Single Action Controller**

Uygulamalarınızda bir controller’ın yalnızca tek bir rotaya hizmet vermesi gereken zamanlar olacaktır. Kendinizi bu rota için controller metodunu nasıl adlandıracağınızı merak ederken bulabilirsiniz. Neyse ki, metodu adlandırmakla uğraşmadan, tek bir controller’a tek bir rotayı yönlendirebilirsiniz.

Bildiğiniz gibi, __invoke() metodu, bir sınıfı fonksiyon gibi ele almanıza ve çağırmanıza olanak tanıyan PHP magic metodudur. Bu, Örnek 3-30'da görebileceğiniz gibi, Laravel'in single action controller’larının tek bir controller’a rota tanımlanıza olanak sağlamak için kullandığı araçtır.

**Örnek 3-30**. __invoke() metodunu kullanmak

```
// \App\Http\Controllers\UpdateUserAvatar.php
public function __invoke(User $user)
{
    // Update the user's avatar image
}

// routes/web.php
Route::post('users/{user}/update-avatar', UpdateUserAvatar::class);
```

## Route Model Binding

En yaygın yönlendirme pattern’lerinden biri, herhangi bir controller metodunun ilk satırının, Örnek 3-31'deki gibi, verilen kimliğe sahip resource’u bulmaya çalışmasıdır.

**Örnek 3-31**. Her rota için bir resource alma

```
Route::get('conferences/{id}', function ($id) {
    $conference = Conference::findOrFail($id);
});
```

Laravel, route model binding adı verilen bu pattern’i basitleştiren bir özellik sağlar. Bu, belirli bir parametrenin (örneğin, {conference}) rota çözümleyiciye (route resolver), bu id ile bir Eloquent veritabanı kaydı araması gerektiğini ve ardından sadece id yerine onu parametre olarak iletmesi gerektiğini tanımlamanıza olanak tanır. İki tür route model binding vardır: implicit (örtülü) ve custom (özel veya açık).

**Implicit Route Model Binding**

Route model binding kullanmanın en basit yolu, rota parametrenizi o modele özgü bir şekilde adlandırmak (örneğin, $id yerine $conference olarak adlandırmak), ardından bu parametreyi closure/controller metoduna yazmak ve orada aynı değişken adını kullanmaktır. Göstermek tarif etmekten daha kolaydır; bu nedenle Örnek 3-32'ye bir göz atın.

**Örnek 3-32**. Implicit route model binding kullanımı

```
Route::get('conferences/{conference}', function (Conference $conference) {
    return view('conferences.show')->with('conference', $conference);
});
```

Rota parametresi ({conference}) metot parametresi ($conference) ile aynı olduğundan ve metot parametresi Conference türünde (Conference $conference) tanımlandığından, Laravel bunu route model binding olarak görür. Bu rota her ziyaret edildiğinde uygulama, URL'e {conference} yerine iletilen her şeyin, Conference aramak için kullanılması gereken bir id olduğunu varsayacaktır ve ardından ortaya çıkan model örneği, closure veya controller metoduna aktarılacaktır.

**Custom Route Model Binding**

Route model binding’i manuel olarak yapılandırmak için, Örnek 3-33'teki gibi bir satırı App\Providers\RouteServiceProvider'daki boot() metoduna ekleyin.

**Örnek 3-33**. Route model binding ekleme

```
public function boot()
{
    // Perform the binding
    Route::model('event', Conference::class);
}
```

Artık, Örnek 3-34'te gösterildiği gibi, bir rotanın tanımında {event} adlı bir parametre olduğunda, rota çözümleyicinin o URL parametresinin id’si ile Conference sınıfının bir örneğini döndüreceğini belirttiniz.

**Örnek 3-34**. Custom route model binding kullanımı

```
Route::get('events/{event}', function (Conference $event) {
    return view('events.show')->with('event', $event);
});
```

## Rota Önbelleği (Route Cache)

Yükleme sürenizin her milisaniyesini sıkıştırmak istiyorsanız route caching’e göz atmak isteyebilirsiniz. Laravel'in cache’in birkaç yüz milisaniyeye kadar sürebilen parçalarından biri, routes/* dosyalarının ayrıştırılmasıdır ve rotaları önbelleğe almak bu süreci önemli ölçüde hızlandırır.

Rota dosyanızı önbelleğe almak için tüm controller, redirect, view ve resource rotalarını kullanmanız gerekir (rota closure hariç). Uygulamanız herhangi bir rota closure kullanmıyorsa, php artisan route:cache komutunu çalıştırabilirsiniz; Laravel, routes/* dosyalarınızın sonuçlarını serialize edecektir. Önbelleği silmek istiyorsanız php artisan route:clear komutunu çalıştırın.

Dezavantajı şu: Laravel artık gerçek routes/* dosyalarınız yerine rotaları önbelleğe alınmış dosyayla eşleştirecek. Rota dosyalarınızda değişiklikler yapabilirsiniz ve bunlar, route:cache'i tekrar çalıştırana kadar etkili olmaz. Bu, her değişiklik yaptığınızda yeniden ön belleğe almanız gerekeceği anlamına gelir; bu da çok fazla karışıklık potansiyeli doğurur.

Bunun yerine şunu önerebilirim: Git varsayılan olarak rota önbellek dosyasını yok saydığından, yalnızca production sunucunuzda rota önbelleği kullanmayı düşünün ve her yeni kodu deploy ettiğinizde (Git post-deploy hook aracılığıyla, Forge deploy komutu aracılığıyla veya kullandığınız diğer deploy sistemlerinin bir parçası olarak) php artisan route:cache komutunu çalıştırın. Bu şekilde, kafa karıştırıcı local geliştirme sorunlarıyla karşılaşmazsınız, ancak remote environment yine de rota önbelleğinden faydalanacaktır.

## Form Method Spoofing

Bazen bir formun hangi HTTP fiili ile gönderilmesi gerektiğini manuel olarak tanımlamanız gerekir. HTML formları yalnızca GET veya POST'a izin verir, dolayısıyla başka türde bir fiil istiyorsanız bunu kendiniz belirtmelisiniz.

**Laravel'de HTTP Fiilleri**

Daha önce gördüğümüz gibi, Route::get(), Route::post(), Route::any() veya Route::match() kullanarak rotanın hangi fiillerle eşleşeceğini tanımlayabilirsiniz. Ayrıca Route::patch(), Route::put() ve Route::delete() ile de eşleştirebilirsiniz.

Peki web tarayıcısıyla GET dışında bir istek nasıl gönderilir? İlk olarak, HTML formundaki method özelliği, HTTP fiilini belirler: formunuzda "GET" metodu varsa, query parameter ve GET metodu aracılığıyla gönderilir; formunuzda "POST" metodu varsa, post body ve POST metodu aracılığıyla gönderilir.

JavaScript frameworkleri, DELETE ve PATCH gibi diğer isteklerin gönderilmesini kolaylaştırır. Ancak Laravel'de HTML formlarını GET veya POST dışındaki fiillerle göndermeniz gerektiğini düşünüyorsanız, form method spoofing kullanmanız gerekir; bu, bir HTML formunda HTTP metodunu yanıltmak anlamına gelir.

**HTML Formlarında HTTP Method Spoofing**

Şu anda gönderdiğiniz formun POST dışında bir şey olarak ele alınması gerektiğini Laravel'e bildirmek için, "PUT", "PATCH" veya "DELETE" değerine sahip _method adlı bir hidden input ekleyin. Laravel bu form gönderimini sanki o fiille yapılan bir istekmiş gibi eşleştirecek ve yönlendirecektir.

Örnek 3-35'teki form, Laravel'e "DELETE" metodunu ilettiğinden, Route::delete() ile tanımlanan rotalarla eşleşecek ancak Route::post() ile tanımlanan rotalarla eşleşmeyecektir.

**Example 3-35**. Form method spoofing

```
<form action="/tasks/5" method="POST">
    <input type="hidden" name="_method" value="DELETE">
    <!-- or: -->
    @method('DELETE')
</form>
```

## CSRF Koruması

Örnek 3-35'teki de dahil olmak üzere, Laravel uygulamasında form göndermeyi denediyseniz, muhtemelen TokenMismatchException ile karşılaşmışsınızdır.

Varsayılan olarak, Laravel'deki “read-only” rotalar (GET, HEAD veya OPTIONS kullananlar) dışındaki tüm rotalar, _token adlı input biçiminde bir "token" gerektirerek cross-site request forgery (CSRF) saldırılarına karşı korunur. Bu token her session başlangıcında oluşturulur ve read-only olmayan her rota, gönderilen _token'ı session token’ı ile karşılaştırır.

**CSRF Nedir?**

Cross-site request forgery (Siteler arası istek sahteciliği), bir web sitesinin başka bir web sitesi gibi davranmasıdır. Amaç, birisinin, oturum açmış kullanıcının tarayıcısı aracılığıyla kendi web sitesinden web sitenize formlar göndererek kullanıcılarınızın web sitenize erişimini ele geçirmesidir.

CSRF saldırılarını önlemenin en iyi yolu, tüm rotaları (POST, DELETE, vb.) Laravel'in yaptığı gibi bir token ile korumaktır.

Bu CSRF hatasını aşmak için iki seçeneğiniz var. İlk ve tercih edilen yöntem, gönderimlerinizin her birine _token input’unu eklemektir. Örnek 3-36'da görebileceğiniz gibi HTML formlarında bunu yapmanın basit bir yolu vardır.

**Örnek 3-36**. CSRF token

```
<form action="/tasks/5" method="POST">
    @csrf
</form>
```

JavaScript uygulamalarında biraz daha fazla çalışma gerekir, ancak çok fazla değil. JavaScript framework’lerini kullanan siteler için en yaygın çözüm, token’ı her sayfada bunun gibi bir <meta> etiketinde saklamaktır:

```
<meta name="csrf-token" content="<?php echo csrf_token(); ?>">
```

Token’ı <meta> etiketinde saklamak, onu doğru HTTP header’a bağlamayı kolaylaştırır; bunu, Örnek 3-37'de olduğu gibi, JavaScript framework'ün den gelen tüm istekler için global olarak bir kez yapabilirsiniz.

**Örnek 3-37**. CSRF için global olarak header bağlama

```
// In jQuery:
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});

// With Axios: it automatically retrieves it from a cookie. Nothing to do!
```

Laravel her istekte X-CSRF-TOKEN'i (ve Axios ve Angular'ın kullandığı X-XSRF-TOKEN'i) kontrol edecek ve buradan iletilen geçerli tokenlar CSRF korumasını karşılanmış olarak işaretleyecektir.

## Yönlendirmeler

Şu ana kadar controller metodundan veya rota tanımından geri dönmek hakkında açıkça konuştuğumuz tek şey view oldu. Ancak tarayıcıya nasıl davranacağına ilişkin talimatlar vermek için geri kullanabileceğimiz birkaç yapı daha var.

İlk önce yönlendirmeyi (redirect) ele alalım. Bunlardan birkaçını zaten diğer örneklerde gördünüz. Yönlendirme oluşturmanın iki yaygın yolu vardır; Burada redirect() global helper’ını kullanacağız, ancak siz facade tercih edebilirsiniz. Her ikisi de bir Illuminate\Http\RedirectResponse örneği oluşturur, bunun üzerinde bazı metotlar uygular ve ardından onu geri döndürür. Bunu manuel olarak da yapabilirsiniz, ancak biraz daha fazla işi kendiniz yapmanız gerekecektir. Yönlendirmeyi geri döndürmenin birkaç yolunu görmek için Örnek 3-38'e bakın.

**Örnek 3-38**. Yönlendirmeyi döndürmenin farklı yolları

```
// Using the global helper to generate a redirect response
Route::get('redirect-with-helper', function () {
    return redirect()->to('login');
});

// Using the global helper shortcut
Route::get('redirect-with-helper-shortcut', function () {
    return redirect('login');
});

// Using the facade to generate a redirect response
Route::get('redirect-with-facade', function () {
    return Redirect::to('login');
});

// Using the Route::redirect shortcut
Route::redirect('redirect-by-route', 'login');
```

redirect() helper’ın redirect facade ile aynı metotları kullandığını ancak aynı zamanda bir kısayolu olduğunu unutmayın; Parametreleri zincirleme metotlar yerine doğrudan helper’a iletirseniz, bu, to() yönlendirme metodunun kısayoludur.

Ayrıca Route::redirect() route helper’ın - isteğe bağlı - üçüncü parametresinin, yönlendirmenizin status kodu (örn. 302) olabileceğini unutmayın.

**redirect()->to()**

Yönlendirmeler için to() metodunu imzası şuna benzer:

```
function to($to = null, $status = 302, $headers = [], $secure = null)
```

$to geçerli bir dahili yoldur, $status HTTP status’tür (varsayılan olarak 302'dir), $headers yönlendirmenizle birlikte HTTP header’ları tanımlamanıza olanak tanır ve $secure varsayılan http ve https seçimini geçersiz kılar (normalde URL'e göre ayarlanır). Örnek 3-39 bunun kullanımına ilişkin bir örneği göstermektedir.

**Örnek 3-39**. redirect()->to()

```
Route::get('redirect', function () {
    return redirect()->to('home');
    // Or same, using the shortcut:
    return redirect('home');
});
```

**redirect()->route()**

route() metodu to() metofuyla aynıdır ancak belirli bir yola işaret etmek yerine belirli bir rota adına işaret eder (bkz. Örnek 3-40).

**Örnek 3-40**. redirect()->route()

```
Route::get('redirect', function () {
    return redirect()->route('conferences.index');
});
```

Bazı rota adları parametre gerektirdiğinden parametre sırasının biraz farklı olduğunu unutmayın. route(), rota parametreleri için isteğe bağlı ikinci bir parametreye sahiptir:

```
function route($to = null, $parameters = [], $status = 302, $headers = [])
```

Yani, onu kullanmak biraz Örnek 3-41'e benzeyebilir.

**Örnek 3-41**. parametre ile redirect()->route()

```
Route::get('redirect', function () {
    return redirect()->route('conferences.show', ['conference' => 99]);
});
```

**Redirect to_route Helper**

to_route() helper’ı, redirect()->route() metodu için takma ad olarak kullanabilirsiniz. İkisinin de imzası aynıdır.

```
Route::get('redirect', function () {
    return to_route('conferences.show', ['conference' => 99]);
});
```

**redirect()->back()**

Laravel'in session uygulamasının bazı yerleşik kolaylıkları nedeniyle, uygulamanız her zaman kullanıcının daha önce ziyaret ettiği sayfanın ne olduğu bilgisine sahip olacaktır. Bu, kullanıcıyı geldiği sayfaya yönlendiren redirect()->back() yönlendirmesi fırsatını verir. Bunun için genel bir kısayol da vardır: back().

**Diğer Yönlendirme Metotları**

Yönlendirme servisi, daha az kullanılan ancak yine de kullanılabilen şu metotları sağlar:

- refresh() - Kullanıcının o anda bulunduğu sayfaya yönlendirir.
- away() - Varsayılan URL doğrulaması olmadan harici bir URL'ye yönlendirmeye izin verir.
- secure() - Secure parametresinin "true" olarak ayarlandığı to() gibi.
- action() - Bir controller’a ve metoda iki yoldan biriyle bağlanmanıza olanak tanır: dize olarak (redirect()->action('MyController@myMethod')) veya dizi olarak
(redirect()->action([MyController::class, 'myMethod']))).
- guest() - Kimlik doğrulama sistemi tarafından dahili olarak kullanılır; Bir kullanıcı, kimlik doğrulamasının yapılmadığı bir rotayı ziyaret ettiğinde, bu rotayı yakalar ve ardından kullanıcıyı (genellikle bir oturum açma sayfasına) yönlendirir.
- intended() - Ayrıca kimlik doğrulama sistemi tarafından dahili olarak da kullanılır; Başarılı bir kimlik doğrulamanın ardından, guest() metodu tarafından saklanan "amaçlanan" URL'i alır ve kullanıcıyı oraya yönlendirir.

**redirect()->with()**

redirect() ile çağırabileceğiniz diğer metotlara benzer şekilde yapılandırılmış olsa da with() metodu, nereye yönlendirme yaptığınızı değil, yönlendirmeyle birlikte hangi verileri ilettiğinizi tanımlaması bakımından farklıdır. Kullanıcıları farklı sayfalara yönlendirirken genellikle belirli verileri de onlarla birlikte iletmek istersiniz. Verileri session’a manuel olarak aktarabilirsiniz, ancak Laravel'in size bu konuda yardımcı olacak bazı kullanışlı metotları vardır.

En yaygın olarak, Örnek 3-42'de olduğu gibi with() kullanarak bir anahtar ve değer dizisini veya tek bir anahtar ve değeri iletebilirsiniz. Bu, verilerinizi yalnızca bir sonraki sayfa yüklemesi için session’a kaydeder.

**Örnek 3-42**. Data ile yönlendirme

```
Route::get('redirect-with-key-value', function () {
    return redirect('dashboard')
        ->with('error', true);
});

Route::get('redirect-with-array', function () {
    return redirect('dashboard')
        ->with(['error' => true, 'message' => 'Whoops!']);
});
```

Ayrıca, Örnek 3-43'te olduğu gibi, kullanıcının form girişi ile yönlendirme yapmak için withInput() metodunu kullanabilirsiniz; Bu, kullanıcıyı az önce geldiği forma geri göndermek istediğiniz doğrulama hatası durumunda en yaygın kullanımdır.

**Örnek 3-43**. Form girişiyle yönlendirme

```
Route::get('form', function () {
    return view('form');
});

Route::post('form', function () {
    return redirect('form')
        ->withInput()
        ->with(['error' => true, 'message' => 'Whoops!']);
});
```

withInput() ile aktarılan girişi almanın en kolay yolu, tüm eski girişleri almak için kullanılabilecek old() helper’ı kullanmaktır. Bunu genellikle view’da göreceksiniz; bu, formun hem "create" hem de "edit" view’ında kullanılmasına olanak tanır:

```
<input name="username" value="<?=
    old('username', 'Default username instructions here');
?>">
```

Doğrulamadan bahsetmişken, hataları yönlendirme yanıtıyla birlikte iletmek için de yararlı bir metot vardır: withErrors(). Herhangi bir error "provider" iletebilirsiniz; bu bir hata dizesi, bir dizi hata veya yaygın olarak kullanılan Illuminate Validator‘ın bir örneği olabilir.

**Örnek 3-44**. Hatalar ile  yönlendirme

```
Route::post('form', function (Illuminate\Http\Request $request) {
    $validator = Validator::make($request->all(), $this->validationRules);
    
    if ($validator->fails()) {
        return back()
            ->withErrors($validator)
            ->withInput();
    }
});
```

withErrors(), yönlendirdiği sayfanın view’ı ile otomatik olarak bir $errors değişkenini paylaşır, böylece istediğiniz gibi işlem yapabilirsiniz.

## Request’in İptal Edilmesi

Geri dönen view ve yönlendirmelerin yanı sıra, bir rotadan çıkmanın en yaygın yolu iptal (abort) etmektir. İsteğe bağlı olarak HTTP status kodlarını, bir mesajı ve bir başlık dizisini parametre olarak alan, global olarak kullanılabilen birkaç metot (abort(), abort_if() ve abort_unless()) vardır.

Örnek 3-45'te gösterildiği gibi, abort_if() ve abort_unless(), doğruluğu açısından değerlendirilen bir parametreyi alır ve sonuca bağlı olarak iptal işlemini gerçekleştirir.

**Örnek 3-45**. 403 Abort

```
Route::post('something-you-cant-do', function (Illuminate\Http\Request $request) {
    abort(403, 'You cannot do that!');
    abort_unless($request->has('magicToken'), 403);
    abort_if($request->user()->isBanned, 403);
});
```

## Özel Yanıtlar

Geri dönebileceğimiz birkaç seçenek daha var; view, yönlendirme ve iptallerden sonra en yaygın olanları gözden geçirelim. Yönlendirmelerde olduğu gibi, bu metotları da response() helper veya Response facade ile çalıştırabilirsiniz.

**response()->make()**

Manuel olarak bir HTTP response oluşturmak istiyorsanız, verilerinizi response()->make()'in ilk parametresine aktarmanız yeterlidir: örneğin, return response()->make(Hello, World!). Bir kez daha, ikinci parametre HTTP status kodudur ve üçüncüsü ise başlıklarınızdır.

**response()->json() ve ->jsonp()**

JSON kodlu bir HTTP response’u manuel olarak oluşturmak için, JSON uyumlu içeriğinizi (diziler, koleksiyonlar veya başka herhangi bir şey) json() metoduna aktarın: örneğin, return response()->json(User::all()).

**response()->download(), ->streamDownload(), ve ->file()**

Kullanıcıya indirebileceği bir dosya göndermek için, download()'a bir SplFileInfo örneği veya dosya adı iletin: örneğin, return response()->download('file501751.pdf', 'myFile.pdf'), bu da file501751.pdf konumundaki bir dosyayı gönderir ve gönderildiği şekliyle myFile.pdf olarak yeniden adlandırır.

Aynı dosyayı tarayıcıda görüntülemek için (eğer bu bir PDF, resim veya tarayıcının işleyebileceği başka bir şeyse), bunun yerine response->download() ile aynı parametreleri alan response()->file() kullanın.

Doğrudan sunucunuzun diskine yazmak zorunda kalmadan, harici bir servisteki içerikleri indirilebilir hale getirmek istiyorsanız, response()->streamDownload()) metodunu kullanabilirsiniz. Bu metot, parametre olarak bir dizeyi, dosya adını ve isteğe bağlı olarak header dizisini dönen bir closure bekler; bkz. Örnek 3-46.

**Örnek 3-46**. Harici sunuculardan indirilen dosyalar

```
return response()->streamDownload(function () {
    echo DocumentService::file('myFile')->getContent();
}, 'myFile.pdf');
```

## Özet

Laravel'in rotaları, routes/web.php ve routes/api.php'de tanımlanır. Her rota için beklenen yolu, hangi segmentlerin static, hangilerinin parametre olduğunu, hangi HTTP fiillerinin rotaya erişebileceğini ve bunun nasıl çözümleneceğini tanımlayabilirsiniz. Ayrıca rotalara middleware ekleyebilir, bunları gruplandırabilir ve onlara adlar verebilirsiniz.

Rota closure veya controller metodundan döndürülen şey, Laravel'in kullanıcıya nasıl yanıt vereceğini belirler. Bir dize veya view ise kullanıcıya sunulur; eğer başka türden bir veri ise JSON'a dönüştürülür ve kullanıcıya sunulur; ve eğer bu bir yönlendirmeyse, yönlendirmeye zorlar.

Laravel, yönlendirme ile ilgili genel görevleri ve yapıları basitleştirmek için bir dizi araç ve kolaylık sağlar. Bunlar resource controller, route model binding, ve form method spoofing’tir.

**Kaynak:** “Laravel: Up & Running”, Matt Stauffer