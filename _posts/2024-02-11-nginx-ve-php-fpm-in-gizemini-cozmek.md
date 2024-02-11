---
layout: post
title: Nginx ve PHP-FPM'in Gizemini Çözmek
---

Kodlarının sunucu tarafında yürütülmesi söz konusu olduğunda, geliştiriciler genellikle farklı düzeylerde deneyim ve bilgiye sahiptir. Bunun nedeni, geliştiricilerin genellikle zamanlarını yeni özellikleri kodlamaya odaklarken, uygulamaları dağıtma, sunucuları kurma ve sunucuların yapılandırmasını ayarlama ile ilgili görevlerin genellikle SRE (Site Güvenilirliği Mühendisliği)'ler tarafından yapılmasıdır. Geçmişiniz ne olursa olsun, Nginx ve PHP-FPM'i içeren standart PHP Web Mimarisi hakkında daha derin bir anlayış kazanmak faydalı olabilir. Bu blog yazısında, uygulamalarınızı hayata geçiren bu sunucu tarafı bileşenleri inceleyeceğiz ve maksimum performans için sisteminizde ince ayar yapmanızı sağlayabilecek bazı önemli yapılandırma parametrelerini gözden geçireceğiz.

**Not:** Hem Nginx hem de PHP-FPM için, uygulamanızın performansını artırmanıza yardımcı olabilecek, bu yazıda ele alınmayan daha birçok yapılandırma seçeneği vardır.

## PHP Web Mimarisi

PHP çekirdeği, local environment’ta çalışırken geliştirme amacıyla kullanabileceğiniz yerleşik bir sunucu içermesine rağmen, tek seferde tek bir istek yürütmesini sınırlayan tek iş parçacıklı yapısı nedeniyle production ortamları için uygun değildir. Diğerlerinin yanı sıra güvenlik, önbelleğe alma, ölçekleme vb. ile ilgili yeteneklerden yoksundur. Sonuç olarak PHP, birden çok işlemi yönetmek için ek bir bileşen olan PHP-FPM'e ve nihayetinde web uygulamalarını verimli bir şekilde sunmak için Nginx'e güvenir.

![PHP web mimarisi](/assets/images/2024/02/php-web-architecture.jpg "PHP web mimarisi"){: class="img-fluid"}

## PHP-FPM: FastCGI Process Manager

PHP-FPM veya "PHP FastCGI Process Manager", PHP için gelişmiş, yüksek performanslı bir FastCGI proses yöneticisidir. Aynı anda birden çok isteği işleyebilen birden çok worker oluşturarak PHP'nin yerleşik sunucusunun eşzamanlılık sorununu çözer. Ancak PHP-FPM, gelen HTTP trafiğini doğrudan yönetemez ve bu da bir web sunucusunun ters proxy gibi davranmasını gerektirir.

## Nginx: Yüksek Performanslı Web Sunucusu ve Ters Proxy

Nginx, gelen HTTP trafiğini yönetmede mükemmel olan güçlü, açık kaynaklı bir web sunucusudur. client isteklerini PHP-FPM'ye ileten ve işlenen yanıtları client’lara geri döndüren bir ters proxy işlevi görür. Ek olarak Nginx, web uygulamalarınızın genel performansını ve güvenilirliğini artıran yük dengeleme, SSL termination ve statik dosyalar sunma gibi çeşitli avantajlar sağlar.

Sonuç olarak, Nginx ve PHP-FPM arasındaki ilişkiyi anlamak, PHP uygulamanızın mimarisi ve dağıtımı hakkında bilinçli kararlar vermenizi sağlayacaktır. Bu güçlü bileşenlerden yararlanarak, uygulamalarınızın zorlu production ortamlarında bile verimli ve güvenilir bir şekilde çalışmasını sağlayabilirsiniz.

## PHP-FPM'i Ayarlamak

### Process Manager Stratejileri: On-Demand, Statik ve Dinamik

Her bir stratejiye dalmadan önce, process manager, worker ve bir isteğin (request) yaşam döngüsü ile ilgili bazı temel kavramları anlamak önemlidir.

Bir isteğin yaşam döngüsü, web sunucusuna (Nginx) ulaştığında başlar. Web sunucusu, isteği işlemek için uygun bir worker atayan PHP-FPM'e iletir. Worker, PHP scriptini yürüterek isteği işler ve web sunucusu aracılığıyla client’a döndürülen bir yanıt oluşturur. Worker farklı durumlarda olabilir:

- **Idle:** Bu durumda, worker herhangi bir isteği işlemez ve yeni isteklerin gelmesini bekler. Worker yeni görevler almaya hazırdır.
- **Active:** Bu durumda worker, istekle ilişkili PHP scriptini yürütür.
- **Terminated:** Worker, process manager stratejisine ve yapılandırma ayarlarına göre sonlandırılabilir. Sunucunuzun kaynak kullanımını optimize etmek için PHP-FPM yapılandırma dosyasında ayarlayabileceğiniz bazı temel direktiflere göz atın:
    - **process_idle_timeout:** on-demand ve dinamik stratejilerde, boşta kalan worker belirli bir boşta kalma zaman aşımından sonra sonlandırılır.
    - **max_requests:** maksimum istek sayısına ulaşıldığında worker değiştirilir; bu, potansiyel bellek sızıntılarını önlemenin anahtarıdır.
    - **request_terminate_timeout:** aktif bir worker, talebi işlemek için maksimum süreye ulaşılırsa process manager tarafından sonlandırılabilir; bu, her zaman zamanında yanıt veren bir sistem sağlamak için özellikle önemlidir.
    - **php_value[memory_limit]:** worker’ın ayırabileceği maksimum bellek miktarını belirler. Bellek kullanımını sınırlandırarak, tek bir worker veya havuzun tamamının mevcut tüm sistem kaynaklarını tüketmesini ve sunucunuzun genel performansını etkilemesini önleyebilirsiniz.

Process manager, worker havuzunu kontrol etmekten sorumludur. Seçilen strateji ve yapılandırma ayarlarına göre bu worker’ların nasıl oluşturulacağını, sürdürüleceğini ve sonlandırılacağını belirler. Aynı anda birden çok istek geldiğinde, process manager bu istekleri uygun worker’lara dağıtarak sunucunun gelen trafiği işleme kapasitesini artırır.

Process manager stratejisi, CPU, bellek ve bant genişliği gibi sunucu kaynaklarının potansiyel kullanımını etkiler. Yanıt verebilirlik ve verimlilik arasında dengeyi korurken mevcut kaynakları en iyi şekilde kullanan bir strateji seçmek çok önemlidir.

Şimdi mevcut farklı stratejilerin her birini inceleyelim:

### On-Demand (İstek Üzerine) Strateji

On-demand strateji, boş bir worker havuzuyla başlar ve yalnızca ihtiyaç duyulduğunda worker prosesleri oluşturur. İstek işlendikten sonra boştaki worker sonlandırılır.

![On-Demand Strateji](/assets/images/2024/02/on-demand-strategy.jpg "On-Demand Strateji"){: class="img-fluid"}

**Yapılandırma**

- **pm:** On-demand stratejisini etkinleştirmek için bu parametreyi ondemand olarak ayarlayın.
- **pm.max_children:** Oluşturulabilecek maksimum worker proses sayısını tanımlar. PHP-FPM, bu sınırdan daha fazla worker üretmeyecek.
- **pm.process_idle_timeout:** Boşta kalan worker’ların sonlandırılacağı saniye cinsinden süreyi belirtir.

On-demand strateji kaynak açısından verimli olsa da worker proseslerini oluşturmak ve sonlandırmak için gereken süre nedeniyle ek gecikmeye neden olabilir.

### Statik Strateji

Statik strateji, sabit sayıda worker ile başlar ve sürdürerek, gelen istekleri işlemeye her zaman hazır olan sabit bir worker havuzu sağlar.

![Statik Strateji](/assets/images/2024/02/static-strategy.jpg "Statik Strateji"){: class="img-fluid"}

**Yapılandırma**

- **pm:** Statik stratejiyi etkinleştirmek için bu parametreyi static olarak ayarlayın.
- **pm.max_children:** Sürdürülecek sabit worker sayısını belirler.

Statik strateji, tutarlı performans ve öngörülebilir kaynak tahsisi sağlar. Boştaki prosesler bellek ve CPU açısından çok düşük kaynak tükettiği için kaynak israfı marjinaldir. Sunucunun performansı, her zaman hazır olan sabit sayıda worker nedeniyle hem yüksek hem de düşük trafik senaryolarında daha kararlı ve öngörülebilirdir.

max_requests yönergesi, statik stratejide worker proseslerin düzenli olarak yeniden başlatılmasını sağlamak ve dolayısıyla olası bellek sızıntılarını önlemek için gereklidir.

### Dinamik Strateji

Dinamik strateji, on-demand ve statik stratejiler arasında bir denge sağlar. Minimum boş worker sayısını belirler ve talebe göre havuz boyutunu ayarlar. Kaynak kullanımı ve yanıt verebilirlik arasında bir uzlaşma sunar.

![Dinamik Strateji](/assets/images/2024/02/dynamic-strategy.jpg "Dinamik Strateji"){: class="img-fluid"}

**Yapılandırma**

- **pm:** Dinamik stratejiyi etkinleştirmek için bu parametreyi dynamic olarak ayarlayın.
- **pm.start_servers:** Başlangıçta oluşturulacak worker proses ilk sayısını belirler.
- **pm.max_children:** Oluşturulabilecek maksimum worker proses sayısını tanımlar.
- **pm.min_spare_servers:** Her zaman sürdürülmesi gereken minimum boş worker sayısını ayarlar. Boştaki worker sayısı bu değerin altına düşerse, PHP-FPM ek prosesler oluşturacaktır.
- **pm.max_spare_servers:** İzin verilen maksimum boş worker proses sayısını belirtir. Boştaki worker sayısı bu değeri aşarsa, PHP-FPM fazla işlemleri sonlandırır.
- **pm.process_idle_timeout:** Boştaki worker proseslerin ne kadar süre sonra sonlandırılacağını belirtir. Bu, kaynak kullanımını ve yanıt verebilirliği dengeleyerek optimum sayıda boş proses’in sürdürülmesine yardımcı olur.

Dinamik strateji duruma göre uyarlanabilir ve çok çeşitli senaryolar için uygun olabilir. Ancak, yapılandırma parametrelerinde ince ayar yapılması optimum performans için çok önemlidir ve hataya açık olabilir. Boştaki worker sayısını sistem kaynaklarına ve trafik seviyelerine göre dengelemek önemlidir, ancak trafiğin yoğun olduğu zamanlarda yanlış veya optimal olmayan bir yapılandırma nedeniyle olumsuz durumlarla karşılaşabilirsiniz. Bu gibi durumlarda, statik strateji daha istikrarlı ve öngörülebilir bir performans sağladığı için daha uygun bir seçim olabilir.

## Nginx'i Ayarlamak

Nginx'in HTTP isteklerini nasıl ele aldığını, worker ve bağlantıları (connection) nasıl yönettiğini anlamak, web sunucunuzun performansını optimize etmek için çok önemlidir.

Nginx, client’lardan gelen HTTP isteklerini işler, bu istekleri PHP-FPM'ye iletir ve uygun yanıtları client’a geri döndürür. Bu istekleri verimli bir şekilde yönetmek için Nginx, worker ve bağlantıları kullanır.

Nginx'teki bir worker, gelen bağlantıları ve istekleri işlemekten sorumlu ayrı bir prosestir. Her worker proses, birden çok bağlantıyı yöneterek, Nginx'in nispeten az sayıda worker proses ile aynı anda birçok isteği işlemesine olanak tanır.

Nginx'teki bir bağlantı, sunucuya bireysel bir client bağlantısını ifade eder. Her bağlantı, web uygulamanıza istekte bulunan tek bir client’ı temsil eder. Nginx, olay odaklı mimarisi sayesinde çok sayıda bağlantıyı verimli bir şekilde işlemek için tasarlanmıştır.

### Worker Process

worker_processes yönergesi bağlantı havuzunu işlemek için oluşturulacak Nginx worker proseslerinin sayısını belirtir. Genel olarak, bu değeri sunucunuzda bulunan CPU çekirdeği sayısına eşit olarak ayarlamanız önerilir. Bu ayar, mevcut CPU kaynaklarının kullanımını en üst düzeye çıkararak en iyi performansı sağlar. Değerin otomatik olarak ayarlanması, Nginx'in mevcut CPU çekirdeği sayısına göre en uygun sayıda worker proses otomatik olarak algılamasına ve kullanmasına olanak tanır.

### Worker Connection

worker_connections yönergesi, her bir worker proses tarafından yönetilebilecek maksimum eşzamanlı connection sayısını belirler. Bu değer, sunucunuzun aynı anda hizmet verebileceği toplam client sayısını doğrudan etkiler. Değeri ayarlamak için sunucunuzun kapasitesini ve beklenen eşzamanlı bağlantı sayısını göz önünde bulundurun.

Nginx'te worker ve connection’ların rollerini anlayarak ve worker_processes ile worker_connections yönergelerinde ince ayar yaparak web sunucunuzun performansını optimize edebilirsiniz. Nginx'in performansı ve sistem davranışını geliştirmek için bu yazıda ele alınmayan ancak web uygulamaları için çok önemli olan birçok başka yeteneğe sahip olduğunu unutmayın — bu konuşmada Nginx optimizasyon teknikleri hakkında daha fazla bilgi edinebilirsiniz.

## İzleme ve Sorun Giderme

PHP uygulamalarınızın performansını ve kararlılığını korumak için, sunucuda ortaya çıkabilecek sorunları izlemek ve gidermek çok önemlidir.

**PHP-FPM'i İzlemek**

PHP-FPM, havuzlarınızın performansını ve sağlığını izlemenize yardımcı olabilecek yerleşik bir status sayfası sağlar. PHP-FPM yapılandırmanızda status sayfasını etkinleştirerek, PHP-FPM havuzlarınız hakkında aktif proses sayısı, boştaki proses ve kuyruktaki istekler gibi gerçek zamanlı bilgilere erişebilirsiniz.

**Nginx'i İzlemek**

Nginx ayrıca, Nginx sunucunuzun performansını ve sağlığını izlemenize yardımcı olabilecek yerleşik bir status modülü içerir. Nginx yapılandırmanızda status modülünü etkinleştirerek, Nginx sunucunuz hakkında aktif bağlantı sayısı, kabul edilen bağlantılar ve işlenen istekler gibi gerçek zamanlı bilgilere erişebilirsiniz.

### Sorun giderme

Sorunlar ortaya çıktığında, ilgili bilgileri nerede arayacağınızı bilmek önemlidir. Hem PHP-FPM hem de Nginx, sorunları belirlemenize ve çözmenize yardımcı olabilecek log dosyaları oluşturur.

**PHP-FPM Log Dosyaları**

PHP-FPM log dosyaları, PHP uygulamalarınızla ilgili hatalar ve uyarılar hakkında değerli bilgiler içerir. PHP-FPM logları varsayılan olarak /var/log/php-fpm dizininde bulunabilir veya PHP-FPM yapılandırma dosyanızda belirtilebilir. Bu logları incelemek, PHP kodunuz veya yapılandırma ayarlarınızla ilgili sorunları belirlemenize yardımcı olabilir.

**Nginx Log Dosyaları**

Erişim ve hata logları dahil olmak üzere Nginx log dosyaları, /var/log/nginx dizininde bulunabilir veya Nginx yapılandırma dosyanızda belirtilebilir. Erişim logları, gelen tüm istekleri kaydederken hata logları, Nginx sunucusuyla ilgili sorunlar hakkında bilgiler içerir. Bu logları analiz etmek, sunucu yapılandırmanız, kaynak kullanımınız veya performans darboğazlarınız ile ilgili sorunları belirlemenize yardımcı olabilir.

PHP-FPM ve Nginx yapılandırmalarınızı izleyerek ve sorun gidererek, PHP uygulamalarınızın sorunsuz çalışmasını sağlayabilir ve yüksek performanslı bir sunucu ortamı sağlayabilirsiniz.

## Sonuç

Bir geliştirici olarak, kodunuzun nasıl yürütüldüğü ve production ortamında ortaya çıkan zorluklar hakkında belirli bir düzeyde farkındalığa sahip olmak önemlidir. Yapılandırmaları ve yönetim stratejileriyle birlikte Nginx ve PHP-FPM'nin rollerini anlamak, web uygulamalarınızı optimize etmenize, kararlılık, yanıt verebilirlik ve verimlilik sağlamanıza yardımcı olabilir.

Sunucu tarafı bileşenlerinin derinliklerine inenerek ve nasıl ince ayar yapacağınızı öğrenerek, kullanıcılarınız için daha iyi bir deneyim oluşturabilir ve yüksek performanslı bir sunucu ortamı sağlayabilirsiniz. Bu bilgi, yalnızca olası sorunları daha etkin bir şekilde ele almanıza olanak sağlamakla kalmaz, aynı zamanda Ürün ve SRE ekipleri arasında işbirliğini teşvik eder ve sonuç olarak daha sağlam ve ölçeklenebilir uygulamalara yol açar.

**Kaynak:** https://medium.com/@mgonzalezbaile/demystifying-nginx-and-php-fpm-for-php-developers-bba548dd38f9
