---
layout: post
title: Laravel Geliştirme Ortamını Hazırlamak
---

PHP'nin başarısının bir nedeni de, PHP'ye hizmet vermeyen bir web sunucusu bulmanın zor olmasıdır. Ancak, modern PHP araçları geçmişe göre daha katı gereksinimlere sahiptir. Laravel’de geliştirme yapmanın en iyi yolu, kodunuz için tutarlı bir yerel ve uzak sunucu ortamı sağlamaktır ve neyse ki Laravel ekosisteminde bunun için birkaç araç var.

## Sistem Gereksinimleri

Bu bölümde ele alacağımız her şey Windows makinelerde mümkündür, ancak düzinelerce sayfalık özel talimata ve uyarıya ihtiyacınız olacak. Bu talimatları ve uyarıları gerçek Windows kullanıcılarına bırakacağım, bu nedenle buradaki ve kitabın geri kalanındaki örnekler Unix/Linux/macOS geliştiricilerine odaklanacak.

İster yerel makinenize PHP ve diğer araçları yükleyerek web sitenize hizmet vermeyi, ister geliştirme ortamınıza Vagrant veya Docker aracılığıyla sanal bir makineden hizmet vermeyi veya MAMP/WAMP/XAMPP gibi bir aracı kullanmayı seçin, geliştirme ortamınızın şu özelliklere sahip olması gerekir:

- PHP >= 8.1
- OpenSSL PHP extension
- PDO PHP extension
- Mbstring PHP extension
- Tokenizer PHP extension
- XML PHP extension
- Ctype PHP extension
- JSON PHP extension
- BCMath PHP extension

## Composer

Geliştirdiğiniz makine ne olursa olsun, Composer'ın global olarak kurulu olması gerekir. Composer'a aşina değilseniz, çoğu modern PHP geliştirmesinin temelini oluşturan bir araçtır. Composer, Node için NPM (Node Package Manager) veya Ruby için RubyGems gibi, PHP için bir bağımlılık yöneticisidir. Ancak NPM gibi, Composer da testlerimizin çoğunun, yerel script yüklemenin, yükleme scriptlerinin ve çok daha fazlasının temelidir. Laravel'i yüklemek, Laravel'i güncellemek ve harici bağımlılıkları yönetmek için Composer'a ihtiyacınız olacak.

## Yerel Geliştirme Ortamları

Birçok proje için, geliştirme ortamınızı daha basit bir araç seti kullanarak barındırmak yeterli olacaktır. Sisteminizde zaten MAMP veya WAMP veya XAMPP kuruluysa, Laravel'i çalıştırmak muhtemelen sorun olmayacaktır.

Laravel'i PHP'nin yerleşik web sunucusuyla da çalıştırabilirsiniz. Laravel sitenizin kök klasöründen php -S localhost:8000 -t public komutunu çalıştırın ve PHP'nin yerleşik web sunucusu sitenize http://localhost:8000/ adresinden hizmet verecektir.

Ancak, geliştirme ortamınızda biraz daha fazla güç istiyorsanız (her proje için farklı yerel etki alanları, MySQL gibi bağımlılıkların yönetimi vb.), PHP'nin yerleşik sunucusundan daha güçlü bir aracı kullanmak isteyeceksiniz.

Laravel, yerel geliştirme için dört araç sunar: Artisan Serve, Sail, Valet ve Homestead. Her birini kısaca ele alacağız. Hangisini kullanacağınızdan emin değilseniz, kişisel olarak Mac kullanıcıları için Valet ve diğer herkes için Sail hayranıyım.

**Artisan Serve**

Laravel uygulamanızı kurduktan sonra php artisan serve'i çalıştırırsanız, tıpkı daha önce PHP'nin yerleşik web sunucusunu kullanarak kurduğumuz gibi http://localhost:8000'de hizmet verecektir. Burada ücretsiz olarak başka bir şey almıyorsunuz, bu nedenle tek anlamlı faydası hatırlamanın daha kolay olmasıdır.

**Laravel Sail**

Sail, işletim sisteminizden bağımsız olarak aynı şekilde yerel Laravel geliştirmeye başlamanın en basit yoludur. Bir PHP web sunucusu, veritabanları ve projenin bağımlılıkları veya geliştiricilerinizin çalışma ortamları ne olursa olsun, projenizdeki her geliştirici için tutarlı olan tek bir Laravel kurulumunu çalıştırmayı çok kolaylaştıran bir dizi başka özellik ile birlikte gelir.

Neden Sail kullanmıyorum? Sail yukarıdakileri gerçekleştirmek için Docker'ı kullanıyor ve macOS'ta Docker, Valet'i tercih edeceğim kadar yavaş. Ancak Laravel'de yeniyseniz, özellikle de Mac kullanmıyorsanız, Laravel uygulamalarınızı oluşturmaya başlamanın en basit yoludur.

**Laravel Valet**

MacOS kullanıcısıysanız (Windows ve Linux için resmi olmayan dağıtımlar da vardır), Laravel Valet yerel Laravel uygulamalarınızın her birine (ve diğer birçok statik ve PHP tabanlı uygulamaya) farklı yerel etki alanlarında hizmet vermeyi kolaylaştırır.

Dökümantasyonun size yol göstereceği Homebrew’i kullanarak birkaç araç yüklemeniz gerekecek, ancak ilk kurulumdan uygulamalarınızı sunmaya kadar çok az adım vardır.

Valet'i kurun - en son yükleme yönergeleri için Valet belgelerine bakın - ve onu sitelerinizin yaşayacağı bir veya daha fazla dizine yönlendirin. Tüm geliştirdiğim uygulamalarımı koyduğum ~/Sites dizinimden valet park’ı çalıştırdım. Artık dizin adının sonuna .test ekleyebilir ve tarayıcınızda ziyaret edebilirsiniz

Valet, Laravel uygulamalarımıza hizmet vermeyi kolaylaştırır; belirli bir klasördeki tüm alt klasörleri {klasöradı}.test olarak sunmak için valet park, yalnızca tek bir klasöre hizmet vermek için valet link, bir klasör için Valetserved etki alanını göstermek üzere bir tarayıcı açmak için valet open, Valet sitesine HTTPS ile hizmet vermek için valet secure, ve sitenizi başkalarıyla paylaşabilmeniz için ngrok veya Expose tüneli açmak için valet share.

**Laravel Homestead**

Homestead, yerel geliştirme ortamınızı kurmak için kullanmak isteyebileceğiniz başka bir araçtır. Bu, Vagrant'ın (sanal makineleri yönetmek için bir araç) üzerinde yer alan ve Laravel geliştirme için mükemmel bir şekilde ayarlanmış ve birçok Laravel sitesinin üzerinde çalıştığı en yaygın production ortamını yansıtan önceden yapılandırılmış bir sanal makine görüntüsü sağlayan yapılandırma aracıdır.

[Homestead belgeleri](https://laravel.com/docs/10.x/homestead) detaylıdır ve sürekli güncel tutulur, bu nedenle nasıl çalıştığını ve nasıl kurulacağını öğrenmek istiyorsanız sizi onlara yönlendireceğim.

## Yeni Bir Lavel Projesi Oluşturmak

Yeni bir Laravel projesi oluşturmanın iki yolu vardır, ancak her ikisi de komut satırından çalıştırılır. İlk seçenek, Laravel kurulum aracını (Composer kullanarak) global olarak kurmaktır; ikincisi, Composer'ın create-project özelliğini kullanmaktır.

Kurulum belgeleri sayfasında her iki seçenek hakkında daha ayrıntılı bilgi edinebilirsiniz, ancak Laravel kurulum aracını tavsiye ederim.

**Laravel Kurulum Aracı ile Laravel Kurulumu**

Composer'ı global olarak yüklediyseniz, Laravel yükleyici aracını yüklemek için aşağıdaki komutu çalıştırın:

```
composer global require "laravel/installer"
```

Laravel yükleyici aracını yükledikten sonra, yeni bir Laravel projesi oluşturmak basittir. Sadece şu komutu komut satırınızdan çalıştırın:

```
laravel new projectName
```

Bu, geçerli dizininize projectName adlı yeni bir alt dizini oluşturacak ve içine iskelet Laravel projesi yükleyecektir.

**Laravel'i Composer'ın create-project Özelliği ile Kurma**

Composer ayrıca, belirli bir iskelet ile yeni projeler oluşturmak için create-project adlı bir özellik sunar. Yeni bir Laravel projesi oluşturmak üzere bu aracı kullanmak için aşağıdaki komutu çalıştırın:

```
composer create-project laravel/laravel projectName
```

Tıpkı kurulum aracında olduğu gibi, bu da geçerli dizininize geliştirmeniz için hazır bir iskelet Laravel kurulumu içeren projectName adlı bir alt dizin oluşturacaktır.

**Sail ile Laravel Kurulumu**

Laravel Sail ile çalışmayı planlıyorsanız, bir Laravel uygulaması yükleyebilir ve aynı zamanda Sail kurulum sürecini başlatabilirsiniz. Bilgisayarınızda Docker'ın kurulu olduğundan emin olun ve ardından "example-app" ifadesini uygulamanızın adıyla değiştirerek aşağıdaki komutu çalıştırın:

```
curl -s "https://laravel.build/example-app" | bash
```

Bu, Laravel'i mevcut klasörünüzün altındaki example-app klasörüne kuracak ve ardından Sail kurulum sürecini başlatacaktır.

Kurulum işlemi tamamlandıktan sonra, yeni dizininize geçin ve Sail'i çalıştırın:

```
cd example-app
./vendor/bin/sail up
```

**Not:** İlk kez sail up çalıştırdığınızda, Docker görüntüsünü oluşturması gerektiğinden, diğer yükleme işlemlerinden biraz daha uzun sürecektir.

## Laravel'in Dizin Yapısı

İskelet Laravel uygulaması içeren bir dizini açtığınızda, aşağıdaki dosya ve dizinleri göreceksiniz:

```
app/
bootstrap/
config/
database/
public/
resources/
routes/
storage/
tests/
vendor/
.editorconfig
.env
.env.example
.gitattributes
.gitignore
artisan
composer.json
composer.lock
package.json
phpunit.xml
readme.md
vite.config.js
```

Tanımak için bunları tek tek inceleyelim.

**Dizinler**

Kök dizin varsayılan olarak aşağıdaki klasörleri içerir:

- **app** - Uygulamanızın büyük kısmı burada bulunacak. Model, controller, command ve PHP etki alanı kodunuz burada yer alır.
- **bootstrap** - Laravel her çalıştırıldığında önyükleme yapmak için kullandığı dosyaları içerir.
- **config** - Tüm yapılandırma dosyalarının bulunduğu yer.
- **database** - Migration, seed ve factory’lerin bulunduğu yer.
- **public** - Sunucunun web sitesine hizmet verirken işaret ettiği dizin. Bu, önyükleme sürecini başlatan ve tüm istekleri uygun şekilde yönlendiren ön denetleyici olan index.php'yi içerir. Ayrıca resim, stylesheet, script gibi herkese açık dosyaların bulunduğu yerdir.
- **resources** - Diğer scriptler için gerekli olan dosyaların bulunduğu yer. View ve (isteğe bağlı olarak) kaynak CSS ve kaynak JavaScript dosyaları burada bulunur.
- **routes** - Hem HTTP rotaları hem de "konsol rotaları" veya Artisan komutları için tüm route tanımlarının yapıldığı yer.
- **storage** - Önbellek (cache), log ve derlenmiş sistem dosyalarının bulunduğu yer.
- **tests** - Unit ve entegrasyon testlerinin bulunduğu yer.
- **vendor** - Composer'ın bağımlılıklarını kurduğu yer. Composer'ın herhangi bir uzak sunucuda deploy sürecinizin bir parçası olarak çalışması beklendiğinden, Git tarafından göz ardı edildi (versiyon kontrol sisteminizden dışlanmak üzere işaretlendi).

**Dosyalar**

Kök dizin ayrıca aşağıdaki dosyaları içerir:

- **.editorconfig** - IDE/text editörünüze Laravel'in kodlama standartları hakkında talimatlar verir (ör. girintilerin boyutu, karakter kümesi ve sondaki boşlukların kırpılıp kırpılmayacağı).
- **.env** ve **.env.example** - Ortam değişkenlerini tanımlayın (her ortamda farklı olması beklenen ve bu nedenle versiyon kontrolüne bağlı olmayan değişkenler). .env.example, Git tarafından yok sayılan kendi .env dosyasını oluşturmak için her ortamın çoğaltması gereken bir şablondur.
- **.gitignore** ve **.gitattributes** - Git yapılandırma dosyaları.
- **artisan**: Artisan komutlarını (bkz. Bölüm 8) komut satırından çalıştırmanıza izin verir.
- **composer.json** ve **composer.lock** - Composer için yapılandırma dosyaları; composer.json kullanıcı tarafından düzenlenebilir, composer.lock düzenlenemez. Bu dosyalar, proje hakkında bazı temel bilgileri paylaşır ve ayrıca PHP bağımlılıklarını tanımlar.
- **package.json** - composer.json’a benzer ancak build sisteminin frontend assetleri ve bağımlılıkları için kullanılır; NPM'e hangi JavaScript tabanlı bağımlılıkların devreye gireceği konusunda talimat verir.
- **phpunit.xml** - Laravel'in test için kullandığı araç olan PHPUnit yapılandırma dosyası.
- **readme.md** - Laravel hakkında temel bilgiler veren Markdown dosyası. Laravel yükleyicisini kullanırsanız bu dosyayı görmezsiniz.
- **vite.config.js** - Vite için (isteğe bağlı) yapılandırma dosyası. Bu dosya, frontend assetlerinin nasıl derleyeceğini ve işleneceğini bildirir.

## Yapılandırma

Laravel uygulamanızın temel ayarları - veritabanı bağlantı ayarları, kuyruk ve mail ayarları vb. - config klasöründeki dosyalarda bulunur. Bu dosyaların her biri bir PHP dizisi döndürür ve dizideki her değere, dosya adından ve noktalarla (.) ayrılmış alt anahtarlardan oluşan bir yapılandırma anahtarıyla erişilebilir.

Dolayısıyla, *config/services.php*'de şuna benzeyen bir dosya oluşturursanız:

```
// config/services.php
return [
    'sparkpost' => [
        'secret' => 'abcdefg',
    ],
];
```

Bu yapılandırma değişkenine şunu kullanarak erişebilirsiniz:

```
config('services.sparkpost.secret')
```

Her ortam için farklı olması gereken tüm yapılandırma değişkenleri bunun yerine .env dosyalarınızda oluşturulur. Diyelim ki her ortam için farklı bir Bugsnag API anahtarı kullanmak istiyorsunuz. Yapılandırma dosyasını .env'den alacak şekilde ayarladınız:

```
// config/services.php
return [
    'bugsnag' => [
        'api_key' => env('BUGSNAG_API_KEY'),
    ],
];
```

env() helper fonksiyonu, aynı anahtarla .env dosyasından bir değer çeker. Şimdi, bu anahtarı .env (bu ortam için ayarlar) ve .env.example (tüm ortamlar için şablon) dosyalarınıza ekleyin:

```
# In .env
BUGSNAG_API_KEY=oinfp9813410942

# In .env.example
BUGSNAG_API_KEY=
```

.env dosyanız, framework’ün ihtiyaç duyduğu, hangi mail sürücüsünü kullanacağınız ve temel veritabanı ayarlarınızın ne olduğu gibi, ortama özgü epeyce değişken içerecektir.

**Yapılandırma Dosyalarının Dışında env Kullanımı**

Bazı önbelleğe alma ve optimizasyon özellikleri dahil olmak üzere Laravel'deki bazı özellikler, env() çağrıları ile yapılandırma dosyalarının dışında herhangi bir yerde kullanılamaz.

Ortam değişkenlerini çekmenin en iyi yolu, ortama özgü olmasını istediğiniz her şey için yapılandırma öğeleri ayarlamaktır. Bu yapılandırma öğelerinin ortam değişkenlerini okumasını ve ardından uygulamanızın herhangi bir yerinde yapılandırma değişkenlerine başvurmasını sağlayın:

```
// config/services.php
return [
    'bugsnag' => [
        'key' => env('BUGSNAG_API_KEY'),
    ],
];
```

```
// In controller, or whatever
$bugsnag = new Bugsnag(config('services.bugsnag.key'));
```

**.env Dosyası**

.env dosyasının varsayılan içeriğine hızlıca bir göz atalım. Anahtarlar, kullandığınız Laravel sürümüne bağlı olarak değişir, ancak nasıl göründüklerini görmek için Örnek 2-1'e bakın.

**Örnek 2-1**. Laravel'deki varsayılan ortam değişkenleri

```
APP_NAME=Laravel
APP_ENV=local
APP_KEY=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=laravel
DB_USERNAME=root
DB_PASSWORD=

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```

Hepsine girmeyeceğim, çünkü çok azı çeşitli servisler (Pusher, Redis, DB, Mail) için kimlik doğrulama bilgileri gruplarıdır. İşte bilmeniz gereken iki önemli ortam değişkeni:

- **APP_KEY** - Verileri şifrelemek için kullanılan rastgele oluşturulmuş bir dize. Bu boşsa, "No application encryption key has been specified." hatasıyla karşılaşabilirsiniz. Bu durumda, php artisan key:generate komutunu çalıştırın ve Laravel sizin için bir tane üretecektir.
- **APP_DEBUG** - Uygulamanızın bu örneğinde kullanıcıların hata ayıklama hatalarını görüp görmeyeceğini belirleyen boolean değeri; local ve staging ortamları için harika, production için korkunç.

Kimlik doğrulama dışı ayarların geri kalanına (BROADCAST_DRIVER, QUEUE_CONNECTION, vb.), harici servislere mümkün olduğunca az güvenerek çalışan varsayılan değerler verilir ve bu, yeni başladığınız zamanlar için idealdir.

Laravel uygulamanızı başlattığınızda, çoğu proje için muhtemelen yapmak isteyeceğiniz tek değişiklik, veritabanı yapılandırma ayarlarında olacaktır. Laravel Vale kullanıyorum, bu yüzden DB_DATABASE'i projemin adına, DB_USERNAME'i root'a ve DB_PASSWORD'u boş bir dizeye ayarlıyorum:

```
DB_DATABASE=myProject
DB_USERNAME=root
DB_PASSWORD=
```

Ardından, en sevdiğim MySQL istemcimde projemle aynı ada sahip bir veritabanı oluşturuyorum ve kullanıma hazır.

## Çalıştırma

Artık bir Laravel kurulumuyla çalışıyorsunuz. git init'i çalıştırın, dosyaları git add . ile işleyin ve git commit ile kodlamaya başlamaya hazırsınız. Bu kadar! Valet kullanıyorsanız, aşağıdaki komutları çalıştırabilir ve sitenizi anında tarayıcınızda canlı olarak görebilirsiniz:

```
laravel new myProject && cd myProject && valet open
```

Ne zaman yeni bir projeye başlasam, attığım adımlar şunlardır:

```
laravel new myProject
cd myProject
git init
git add .
git commit -m "Initial commit"
```

Tüm sitelerimi, birincil Valet dizinim olarak kurduğum ~/Sites klasöründe tutuyorum, bu durumda, hiçbir ek iş olmadan tarayıcımda myProject.test'e anında erişebiliyorum. .env dosyasını düzenleyebilir ve onu belirli bir veritabanına yönlendirebilirim, bu veritabanını uygulamama ekleyebilirim ve kodlamaya başlamaya hazırım. Ve unutmayın, Lambo kullanıyorsanız, bu adımların tümü zaten sizin yerinize atılmıştır.

## Test yapmak

Bundan sonraki her bölümde, bölümün sonundaki "Test Etme" bölümü, ele alınan özellik veya özellikler için testlerin nasıl yazılacağını size gösterecektir. Bu bölüm test edilebilir bir özelliği kapsamadığından, testlerden hızlıca bahsedelim. (Laravel'de test yazma ve çalıştırma hakkında daha fazla bilgi edinmek için Bölüm 12'ye gidin.)

Laravel, PHPUnit'i bir bağımlılık olarak getirir ve testleri, adı Test.php ile biten (örneğin, tests/UserTest.php) tests dizinindeki herhangi bir dosyada çalıştırmak üzere yapılandırılmıştır.

Bu nedenle, test yazmanın en basit yolu, testler dizininde Test.php ile biten bir adla dosya oluşturmaktır. Ve bunları çalıştırmanın en kolay yolu, komut satırından (proje kökünde) ./vendor/bin/phpunit komutunu çalıştırmaktır.

Herhangi bir test, veritabanı erişimi gerektiriyorsa, testlerinizi veritabanınızın barındırıldığı makineden yaptığınızdan emin olun; bu nedenle, veritabanınızı Vagrant'ta barındırıyorsanız, testlerinizi oradan çalıştırmak için Vagrant box’ı ssh yaptığınızdan emin olun. Yine, Bölüm 12'de bunu ve çok daha fazlasını öğrenebilirsiniz.

Ayrıca, test bölümlerinden bazıları, kitabı ilk kez okuyorsanız henüz aşina olmayacağınız test sözdizimini ve özelliklerini kullanacaktır. Test bölümlerinden herhangi birindeki kod kafa karıştırıcıysa, onu atlayın ve test bölümünü okuma fırsatı bulduktan sonra geri dönün.

## Özet

Laravel bir PHP framework olduğu için onu yerel olarak sunmak çok basittir. Laravel ayrıca yerel gelişiminizi yönetmeniz için üç araç sunar: Docker kurulumu olan Sail; daha basit bir macOS tabanlı araç olan Valet; ve önceden yapılandırılmış bir Vagrant kurulumu olan Homestead. Laravel, Composer'a dayanır ve bu araç tarafından kurulabilir ve kutudan hem kurallarını hem de diğer açık kaynak araçlarıyla ilişkisini yansıtan bir dizi klasör ve dosyayla çıkar.

**Kaynak:** “Laravel: Up & Running”, Matt Stauffer