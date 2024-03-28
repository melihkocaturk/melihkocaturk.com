---
layout: post
title: Gatsby'nin Temelleri
---

Gatsby, React'ı temel alan, statik siteler oluşturmaya yönelik açık kaynaklı bir framework’tür. Genellikle static site generator (SSG) olarak anılan Gatsby, Gridsome gibi diğer SSG'lerin ve statik site oluşturmayı kolaylaştıran Next.js gibi JavaScript framework’lerinin yanı sıra Jamstack teknoloji kategorisinin bir parçasıdır. Gatsby performansa, ölçeklenebilirliğe ve güvenliğe özellikle önem veriyor. Pluginler, temalar, starter’lar ve katkıda bulunulan diğer projelerden oluşan zengin ve büyüyen bir ekosisteme sahip.

Bu ilk bölümde Gatsby'yi tanıyacağız. Gatsby'nin yapı taşları sayfalar ve bileşenler (component) olmasına rağmen, GraphQL ve source pluginlerinden oluşan dahili veri katmanı, veri kaynakları ile statik site oluşturma sırasında derlenen sayfalar ve bileşenler arasında köprü görevi görür. Gatsby, React üzerine inşa edilmiştir, bu nedenle Gatsby geliştiricilerinin anlaması için gerekli olan bazı JavaScript ve React kavramlarına da göz atacağız. Son olarak, Gatsby'nin geliştirici araçlarına aşina olmanızı sağlayacak ilk Gatsby "Hello World" sitemizi oluşturacağız.

## Gatsby Nedir?

Kısacası Gatsby, web siteleri ve web uygulamaları oluşturmak için React'ı temel alan ücretsiz ve açık kaynaklı bir framework’tür. Ancak yaratıcısı Kyle Mathews, Gatsby'yi karmaşık ve sağlam bir "web compiler" olarak tanımlıyor; veri alma ve işleme, bileşenleri kullanarak sayfa oluşturma, sayfalar arasında bağlantı kurma ve son olarak bileşenlerin çalışan bir statik site halinde derlenmesiyle ilgilenen yapı taşlarından oluşan bir koleksiyon.

Bu bölümde, ana bileşenlerini anlamamıza yardımcı olması için Gatsby'nin mimarisine kuşbakışı bakacağız. Ayrıca Gatsby'nin en önemli üç bileşenini daha yakından inceleyeceğiz: temel yapı taşları (sayfalar ve bileşenler), Gatsby veri katmanı (GraphQL ve source pluginleri) ve Gatsby ekosistemi (plugin ve temalar).

### Gatsby Sayfaları ve Bileşenleri

Gatsby, web siteleri oluşturmak için React bileşenlerinden yararlanıyor. Başka bir deyişle Gatsby'deki her şey bileşenler kullanılarak oluşturulmuştur. Bunu daha derinlemesine anlamak için tipik Gatsby proje yapısına bir göz atalım:

```
/
|-- /.cache
|-- /plugins
|-- /public
|-- /src
    |-- /pages
    | |-- index.js
    |-- /templates
    | |-- article.js
    |-- html.js
|-- /static
|-- gatsby-config.js
|-- gatsby-node.js
|-- gatsby-ssr.js
|-- gatsby-browser.js
```

Gatsby'nin bileşen tabanlı mimarisini göstermek amacıyla, bu Gatsby projesinin, article template’ine (article.js) bağlı farklı sayfalara bağlantı veren bir ana sayfadan (index.js) oluştuğunu hayal edelim. Article template, makalelerin tam metnini tutarlı bir şablona dönüştürür. Bu örneği daha da basitleştirmek için, makalelerin yerel dosya sisteminden ziyade harici bir veritabanından geldiğini varsayalım.

Gatsby'nin dört ana bileşeni vardır; bunlardan ilk üçünü bu örnek proje yapısında kolayca tanımlayabiliriz:

**Sayfa Bileşenleri**

Sayfa bileşenleri tam teşekküllü Gatsby sayfalarına dönüşür. src/pages altına yerleştirilen herhangi bir bileşen, adının önerdiği yola otomatik olarak atanan bir sayfa haline gelir. Örneğin, React bileşeni içeren bir contact.js veya contact.jsx dosyasını src/pages'e yerleştirirseniz, bu dosya en sonunda <example.com>/contact/ adresinde mevcut olacaktır. src/pages içindeki bir dosyanın sayfa haline gelmesi için bir dizeye veya React bileşenine çözümlenmesi gerekir.

**Sayfa Template Bileşenleri**

Statik sayfalar için sayfa bileşenleri nispeten iyi çalışır. Ancak article template gibi template olan sayfalar için, her makaleyi buna göre oluşturacağımız yeniden kullanılabilir ortak bir metine ihtiyacımız var. Sayfa template’leri src/templates dizini altında bulunur.

**HTML bileşeni**

Yalnızca tek bir HTML bileşeni vardır: src/html.js. Bu dosya, Gatsby'nin kontrol ettiği sayfa alanı dışındaki her şeyden sorumludur. Örneğin, <head> öğesinde bulunan meta verileri değiştirebilir ve başka markup’lar ekleyebilirsiniz. Gatsby'nin ayrıca yedek olarak kullandığı varsayılan bir html.js dosyası olduğundan, bu dosyayı özelleştirmek isteğe bağlıdır.

**Sayfa Dışı Bileşenler**

Bunlar tipik sayfa içi React bileşenleridir. Bunlar, bir sayfanın header gibi daha büyük bir sayfa bileşenine gömülü olan ve hiyerarşi oluşturan bölümleridir. Bölümün ilerleyen kısımlarında göreceğimiz gibi, layout bileşeni gibi daha büyük öneme sahip bazı sayfa dışı bileşenler vardır. Örnek Gatsby kodumuza src/components dizinini dahil etmedim ancak sayfa dışı bileşenlerimiz burada görünecek.

Gatsby geliştiricilerinin en çok zaman harcayacağı üç bileşen türü, sayfa bileşenleri, sayfa template bileşenleri ve sayfa dışı bileşenlerdir. Peki Gatsby bu bileşenlerdeki property’leri veya prop’ları doldurmak için hangi verileri kullanacağını nasıl biliyor? GraphQL ve Gatsby'nin - sonraki bölümlerde çok daha derinlemesine inceleyeceğimiz - veri katmanı tam da bu noktada devreye giriyor.

### Gatsby Veri Katmanı: GraphQL ve Source Pluginleri

GraphQL, yüksek düzeyde özelleştirilmiş sorgulara olanak tanıyan bir sorgu dilidir. REST modelinde, istemciler sunucudan hangi verileri alacaklarını tanımlayamazlar; bu sunucu tarafının sorumluluğundadır. GraphQL farklı bir yaklaşım benimseyerek istemcinin veri gereksinimlerini keyfi bir yapıya göre tanımlamasına olanak tanır. Sunucu daha sonra yalnızca gerekli verileri döndürmekle kalmaz, aynı zamanda istemcinin istekte gönderdiği yapıya göre de onu döndürür. Bu, GraphQL'in birincil avantajıdır.

GraphQL sıklıkla REST paradigmasını kullanan, genellikle RESTful API'ler veya REST API'ler olarak bilinen ve bunun yerine verileri istemci tarafından değil sunucu tarafından tanımlandığı şekilde döndüren API'lerin yerine geçmek veya geliştirmek için kullanılır. Örneğin birçok içerik ve ticaret mimarisinde, bir uygulamada kullanılmak üzere third-party servislerden ve veritabanları ve içerik yönetim sistemleri (CMS'ler) gibi veri sağlayıcılardan veri almak için kullanılır. Bu kullanım şeklinde sürekli çalışan ve gelen herhangi bir GraphQL sorgusuna yanıt vermeye hazır bir GraphQL sunucusu bulunur.

Ancak Gatsby, GraphQL'den ek bir amaç için yararlanıyor: dahili veri gereksinimlerinin bildirimi. Gatsby, GraphQL'i bir istemci olarak harici kaynaklardan tüketmek yerine, veri alımını ve işlemeyi çalışma zamanında değil derleme zamanında gerçekleştirmek için GraphQL'i ön plana yerleştirir. Başka bir deyişle, birçok framework GraphQL'i yalnızca verileri harici olarak tüketmek için kullanırken, Gatsby bunun yerine GraphQL'i dahili olarak kullanır, böylece birçok farklı veri kaynağı tek bir GraphQL API'sinde birleştirilebilir.

Tipik bir Gatsby sitesi birçok farklı veri kaynağından faydalanabilir. Farklı veri yapılarının daha sonra geliştiricilerin Gatsby'de kolayca kullanabileceği bir formatta uyumlu hale getirilmesi gerekiyor. Gatsby, doğrudan Gatsby sitesine yerleştirilmiş verileri dahili bir dosya sisteminden alabilir veya derleme süreci sırasında ayrı bir CMS veya ticaret platformuna API çağrıları gerçekleştirebilir. Gatsby, kendi dahili GraphQL API'sini doldurmak için bir GraphQL API'sinden bile faydalanabilir. Ve bu kaynakların tümünden verileri aynı anda tek bir birleşik GraphQL API'sine çekebilir, bu da verilere kolayca erişilmesini ve tüketilmesini kolaylaştırır.

Her Gatsby bileşeni, o bileşenin proplarını dolduran verileri belirleyen bir GraphQL sorgusu içerir. Verileri doğrudan bileşen içinde bildirebilseniz de, muhtemelen başka kaynaklardan çekmek istediğiniz verileriniz vardır. Gatsby bunu nasıl başarıyor?

Gatsby'de pluginler, mevcut bir Gatsby sitesinin üzerine yerleştirilebilecek ek işlevlerdir. Bazı pluginler Gatsby'nin temel işlevselliğini genişletebilir ancak harici kaynaklardan veri alınmasındaki anahtar rolü nedeniyle özellikle önemli olan bir eklenti türü vardır: source pluginleri.

Gatsby'de source pluginleri, kaynak ne olursa olsun, kaynağından veri getiren Node.js paketleridir. Gatsby, API'ler, veritabanları, CMS'ler, ticaret sistemleri ve hatta yerel dosya sistemleri dahil olmak üzere veri yayan çeşitli sistemler için birçok source plugin sunar. Örneğin, gatsby-source-filesystem, ister Markdown ister başka bir şey olsun, verileri doğrudan Gatsby'nin dahili dosya sisteminden çeker. Ayrıca, gatsby-source-drupal ve gatsby-source-contentful, sırasıyla Drupal ve Contentful CMS'lerden içerik çeker.

Source pluginlere kısa bir süre içinde geri döneceğiz, ancak önce Gatsby ekosistemine bir göz atalım.

### Gatsby Ekosistemi

Gatsby ekosistemi, topluluğun katkıda bulunduğu tüm pluginleri ve Gatsby'ye yeni işlevler kazandıran diğer modülleri kapsar. Gatsby ekosisteminde dört tür proje vardır:

**Pluginler**

Önceki bölümde değindiğimiz gibi Gatsby'deki pluginler, Gatsby çekirdeğine önemli işlevler sağlayan Node.js paketleridir. Örneğin, gatsby-image eklentisi, güçlü görüntü işlemeye ihtiyaç duyan geliştiriciler tarafından Gatsby sitelerine eklenebilir. Gatsby pluginleri, site haritası ve RSS beslemesi işlevselliğinden arama motoru optimizasyonu (SEO) desteğine kadar geniş bir yelpazeyi çalıştırır.

**Temalar**

Gatsby teması, tıpkı normal bir Gatsby sitesi gibi, gatsby-config.js dosyasını içeren bir tür plugindir. Bu dosya, Gatsby sitelerine isteğe bağlı tanımlanmış yapılandırma sağlar. Bu, bir Gatsby sitesinin, başka bir Gatsby sitesine paket olarak yüklenen ve onu yeniden kullanılabilir işlevlerle genişleten bir Gatsby teması haline gelebileceği anlamına gelir.

**Starter’lar**

Gatsby ekosisteminde starter’lar, geliştiricilerin Gatsby geliştirmesi için bir başlangıç noktası olarak kendi makinelerine kolayca kopyalayabilecekleri standart şablonlardır. Bununla birlikte, bir geliştirici tarafından değiştirildikten sonra, starter’lar, temalardan farklı olarak orijinal durumlarıyla devam eden bir ilişkiye sahip olmazlar. Gatsby, tipik kullanım durumları için uzun bir official starter listesi sunar ve aynı zamanda kendi kullanım senaryonuzu oluşturmanız için de zengin bir sürece sahiptir.

**Tarifler (Recipes)**

Gatsby'de yeni olan ve hala deneysel olan tarifler, sayfa oluşturma, plugin yükleme, TypeScript'i destekleme ve daha fazlası gibi belirli ortak sorumlulukları otomatikleştirmek için Gatsby CLI'de çağrılan araçlardır. Bu yazının yazıldığı an itibariyle Gatsby, yaklaşık bir düzine örnek tarifin yanı sıra kendi tariflerinizi nasıl yazacağınıza dair bilgiler de yayınladı.

Bu tür Gatsby ekosistemi projelerinin her birini inceleyeceğiz, ancak artık Gatsby'nin ne olduğuna dair temel bir anlayışa sahip olduğunuza göre şunu merak ediyor olabilirsiniz: Neden Gatsby kullanmalıyım?

## Neden Gatsby?

Bu bölümde, geliştiricileri bir sonraki web sitesi projeleri için Gatsby'yi seçmeye motive eden benzersiz avantajlardan ve gerekçelerden bahsedeceğiz. Gatsby, dört temel konuya odaklanmasıyla JavaScript ve Jamstack topluluklarında itibar kazandı: performans, erişilebilirlik, geliştirici deneyimi ve güvenlik.

### Performans

Günümüzde pek çok web sitesi esas olarak sunucu odaklı olmaya devam ediyor; bu, sunucuların tarayıcı tarafından gönderilen her bir isteğe yanıt verdiği anlamına geliyor. Ancak Gatsby, yük optimizasyonu (tarayıcıya gönderilen dosyanın boyutunun en aza indirilmesi) ve dağıtım optimizasyonu (oraya ulaşmak için gereken sürenin en aza indirilmesi) dahil olmak üzere her düzeyde performans iyileştirmelerini vurguluyor.

Gatsby ile performansı arttırmanın dört temel yolu şunlardır:

**Statik Site Teslimi**

Gatsby derlemesi gerçekleştirdiğinizde sonuç, Gatsby proje kökündeki public klasöründe bulunan herhangi bir içerik dağıtım ağına (CDN) yüklenebilecek statik varlıkların bir koleksiyonudur. Bu, Gatsby sitelerinin öncelikle tarayıcıda hızla yüklenen statik HTML'den oluştuğu anlamına gelir. Ancak o zaman React tarafından sağlananlar gibi istemci tarafı JavaScript başlatılır.

**Build-Time Derlemesi**

Gatsby, çok daha ayrıntılı olan ve yalnızca tek bir sayfayı oluşturabilen runtime derlemesi yerine, statik dosyalar üretmek için build-time derlemesini kullanır. Bunun yerine, build-time tam bir statik sayfa kümesi oluşturur. Bu, harici veri kaynaklarına yapılan isteklerin hiçbir zaman toplam teslim süresine ek gecikme getirmediği anlamına gelir; çünkü sonuç, istek üzerine verileri alması ve işlemesi gereken bir sayfa değil, verileri önceden doldurulmuş bir dizi statik dosyadır. Bu şekilde, genellikle sunucuların web uygulamaları için gerçekleştirmesi gereken iş miktarını sınırlar. Gatsby, performans açısından hangisinin daha uygun olduğuna bağlı olarak hem build-time hem de runtime derlemesinden yararlanabilir.

**Yük Optimizasyonu**

Gatsby, görsellerin ve JavaScript dosyalarının boyutlarını azaltarak, görsellerin ekranın alt kısmına yüklenmesini geciktirerek ve gerektiğinde CSS'i satır içi ekleyerek kullanıcılara iletilen nihai verinin boyutunu sınırlamak için çok çalışıyor. Gatsby ayrıca otomatik rota tabanlı kod bölme (böylece yalnızca gerekli rotaya ait kod yüklenir), arka planda önceden yükleme (böylece varlıklar arka planda yüklenebilir) ve hemen ihtiyaç duyulmayan varlıkların yavaş yüklenmesini de sağlar.

**Dağıtım Optimizasyonu**

Gatsby siteleri, dağıtılan kodun daha hızlı teslim edilmesini sağlayan bir CDN'den hizmet verecek şekilde optimize edilmiştir. Gereksinimlerinize bağlı olarak bu model, tarayıcıdan gelen her isteğin kaynak sunucuya ve veritabanına dönmesinin gerektiği tarihsel paradigmaya göre önemli bir gelişme olabilir.

Statik site teslimi ve build-time derlemesi sayesinde, Gatsby siteleri çok sayıda JavaScript dosyası yerine çoğunlukla HTML'den oluşur. Yük optimizasyonu ve dağıtım optimizasyonu sayesinde, Gatsby siteleri uçta hızla yüklenir ve küçük kalır.

### Erişilebilirlik

Bir zamanlar önemsiz bir konu olan erişilebilirlik, pazardaki işletmeler ve kuruluşlar için önemli bir husus haline geliyor. Tek sayfalı JavaScript uygulamaları yakın zamana kadar yardımcı teknolojilere olan zayıf destekleri ve farklı erişim ihtiyaçlarının dikkate alınmaması nedeniyle kötü bir şöhrete sahipti. Gatsby, yüksek düzeyde dinamik JavaScript yerine statik HTML sunmanın yanı sıra, erişilebilir markup ve erişilebilir yönlendirme sağlamaya odaklanarak erişilebilirliğe birinci sınıf bir vatandaş gibi davranır:

**Erişilebilir Markup**

Gatsby, varsayılan olarak kod için erişilebilirlik linting aracı olan eslint-plugin-jsx-a11y paketini sağlar. Bu paket, diğer olası sorunların yanı sıra resimler için eksik alternatif metin ve Accessible Rich Internet Applications (ARIA) property’lerinin doğru kullanımını arar.

**Erişilebilir Yönlendirme**

Çoğu JavaScript uygulaması, tarayıcının ek bir sayfa yüklemesine neden olmak yerine, kullanıcının bir bağlantıya tıklayıp sayfanın hemen yeniden oluşturulduğunu görmesine olanak sağlamak için dinamik sayfa navigasyonu kullanır. Ancak bu dinamik yönlendirmenin engelli kullanıcılar için normal linklerle aynı rahatlıkla kullanılması imkansız olabilir. Gatsby,  erişilebilir dinamik gezinmeyi destekleyen @reach/router kütüphanesi ile birlikte gelir ve ayrıca Gatsby linklerinin erişilebilir bir şekilde çalışmasını sağlar.

### Geliştirici Deneyimi

Geliştirmeyi olabildiğince sorunsuz hale getirmek ve geliştiricilerin web siteleri oluştururken zamandan tasarruf etmelerini sağlamak için Gatsby, aşağıdakiler de dahil olmak üzere çeşitli geliştirici deneyimi özellikleri (özellikle tekrarlanan görevler için) sunar:

**Starter ve Minimum Konfigürasyon**

Gatsby starter’ları neredeyse sıfır yapılandırmaya izin verir ve tamamen hazır bir dizin yapısı sağlar. Tüm starter’lar tamamen işlevsel Gatsby siteleridir ve hemen devreye alınabilir. Sıfırdan bir Gatsby sitesi oluşturmak mümkün olsa da, mevcut official starter’lardan birinin kullanılması şiddetle tavsiye edilir ve bizimde bu kitapta benimsediğimiz yaklaşım budur.

**Hot Reloading ile Local Development Ortamı**

Gatsby, kod değişiklikleri kaydedildiğinde hot reloading özelliğine sahip local development ortamı sağlar. Bu, üzerinde çalıştığınız siteye değişikliklerin anında yansıdığını görebildiğiniz  hızlı bir geri bildirim döngüsü oluşturur.

**GraphiQL ve GraphQL Explorer**

Gatsby, GraphQL’in yanı sıra, GraphQL aracılığıyla veri almak isteyenlere yardımcı olacak geliştirici araçlarını da sağlar. Hata ayıklamayı sağlayan bir GUI olan GraphiQL ve GraphQL için bir schema explorer olan GraphQL Explorer, varsayılan olarak Gatsby ile birlikte gelir.

**Built-in Linting ve Test Etme**

Gatsby aynı zamanda temel paketinde, kodlama standartlarına ve erişilebilirlik kurallarına bağlılık, geliştirme süreci sırasında ortaya çıkan diğer kalite sorunları gibi endişeleri kontrol etmenize olanak tanıyan çeşitli yerleşik testler ve linting süreçlerini de içerir.

**Sürekli Entegrasyon ve Sürekli Dağıtım (CI/CD)**

Son olarak Gatsby, sürekli entegrasyon (Continuous Integration) ve sürekli dağıtım (Continuous Deployment) için gelişmiş yetenekler sunan çeşitli altyapı sağlayıcılarıyla zarif bir şekilde entegre olur. Gatsby için kullanılan en yaygın sunucu hizmetlerinden bazıları Netlify, Vercel ve Gatsby Cloud'dur.

### Güvenlik

Günümüzde güvenlik, geleneksel web sitelerini rahatsız eden en önemli şeylerden biridir. Pek çok web sitesi, özellikle veritabanı ve kimlik doğrulama gibi katmanlar arasında koruma olmadığında, sistemleri saldırganlara karşı savunmasız olabilecek monolitik mimarilerden hâlâ yararlanıyor. Diğerleri aktif olarak çalışan veritabanı ve sunuculara ihtiyaç duyar,
dikkatli bir şekilde tasarlanmadığı takdirde güvenlik açıkları oluşabilir.

Gatsby, sitelerini API'ler veya tükettiği veri kaynakları hakkında bilgi içeren dinamik kod olarak değil, statik HTML olarak sunduğundan, harici veri kaynaklarını otomatik olarak gizleyerek onları istenmeyen gözlerden korur. İçerik ve ticaret sistemi yöneticileri için bu özellikle yararlı olabilir, çünkü bu veri kaynakları, devam eden bir Gatsby derlemesi veya verileri güncellemek isteyen bir editör tarafından ihtiyaç duyulmadığında kapatılabilir. Güvenlik, bu kitap boyunca geri döneceğimiz çok yönlü bir konudur, ancak Gatsby'nin statik site sunumu, markup ve veri kaynaklarınız arasında çok sağlam bir güvenlik duvarı sağlar.

Artık bu kadar çok geliştiricinin Gatsby'yi seçmesinin ardındaki bazı motivasyonları anladığınıza göre, bunu daha geniş mimari paradigmalar ve React ile JavaScript geliştirme çerçevesi içinde bağlamsallaştıralım.

Gatsby, yüksek performanslı web siteleri ve web uygulamaları oluşturmaya yönelik bağımsız bir framework olsa da, çalışma zamanı derlemesi yerine derleme süresini ödüllendiren, sunucuya bağımlı mimariler yerine sunucusuz olan web framework’lerine yönelik daha büyük bir eğilimin parçasıdır. Bir sonraki bölümde Gatsby'nin Jamstack mimarileri, JavaScript ve React'ın daha geniş bağlamlarına nasıl uyduğuna bakacağız.

## Gatsby ve Jamstack

Organizasyonların neden eskisinden daha az monolitik ve daha ayrık mimarilere geçiş yaptığını açıklayan mimari temelleri ele aldık. Ancak geliştiricilerin neden benzer hamleler yaptığını anlamak için, yüksek performanslı web siteleri geliştirmeyi kolaylaştıran JavaScript, API'ler ve statik markup ile denge kuran bir mimari paradigma olan Jamstack'ı (aynı zamanda JAMstack olarak da yazılır) keşfetmemiz gerekiyor.

Bu bölümde ilk olarak, çalışma zamanı yerine derleme zamanı deploy sağlayan SSG'leri ele alacağız. Ardından, içerik ve ticaret mimarilerinde meydana gelen eş zamanlı bir paradigma değişimi olan dağıtılmış içerik ve ticareti ele alacağız. Son olarak, Jamstack'in ne olduğunu ve Gatsby'nin Jamstack ekosisteminin neresinde olduğunu tanımlamak ve özetlemek için SSG'leri, içerik ve ticaret mimarilerini birleştireceğiz.

### Statik Site Oluşturucular

En temel düzeyde Gatsby bir SSG'dir. SSG'ler, bir template ve harici bir veritabanı veya başka bir kaynak tarafından sağlanan verilere dayalı olarak statik HTML dosyaları oluşturan araçlardır. Bu veri girişi göz önüne alındığında, geliştirici tarafından yazılan mantığa dayalı olarak verilere Markdown, Liquid vb. template dillerinde yazılabilen stil ve template’ler uygulanır.

Genellikle aktif olarak çalışan bir sunucu gerektiren ve çalışma zamanında yürütülen tipik dinamik web uygulamalarından farklı olarak SSG'ler, web sunucusundaki dosya sisteminden daha fazlasını gerektirmeyen bir dizi statik dosya ve asset sağlar. Genellikle terminal komutu aracılığıyla manuel olarak çalıştırıldıkları için derleme zamanında yürütülürler; bu da yalnızca statik varlıkların durumunun güncellenmesi gerektiğinde çalıştıkları anlamına gelir.

SSG'lerin ilk dalgası, statik site oluşturmak ve verilerin template’ler halinde işlenmesini gerçekleştirmek için öncelikle sunucu tarafı teknolojilerine dayanıyordu. Örneğin, hala popüler olan Jekyll ve Hugo, Ruby ve Go gibi yalnızca sunucuya yönelik dillerde yazılsa da, Metalsmith gibi diğer eski SSG'ler Node.js ve JavaScript ile yazılmıştır. İlk nesil SSG'ler sınırlı içeriğe ve dinamizme sahip küçük web siteleri için ideal olsa da, JavaScript uygulamalarında bulunan etkileşimden tamamen yoksundular.

Son yıllarda, özellikle Javascript ekosisteminde ikinci nesil SSG'ler ortaya çıktı. Bunlar, Jekyll ve Hugo gibi eski araçlardan önemli ölçüde farklıdır. Sunucu tarafı diller ile ilişkilendirilmek yerine JavaScript framework’lerinin istemci tarafı yeteneklerine güvendikleri için bu SSG'ler kendilerini JavaScript framework’lerine bağlar. Örneğin Gatsby ve Next.js, React destekli SSG'ler kategorisine girerken Gridsome ve Nuxt.js, Vue ekosisteminin bir parçasıdır.

Bu bizi kitapta ele aldığımız framework’ün çeşitli tanımlarından ilkine getiriyor: Gatsby her şeyden önce React'ı temel alan bir SSG'dir. Ancak Gatsby'nin neden ortaya çıktığını ve başarısının ardındaki nedenleri incelemek için, Gatsby'nin içerik ve ticari web sitesi uygulamalarıyla ilgili birçok kalıcı sorunu nasıl çözdüğüne dair çok daha geniş bir perspektifi incelememiz gerekiyor.

### Dağıtılmış İçerik ve Ticaret

Son yıllarda Gatsby gibi framework’lerin ortaya çıkması sayesinde, dağıtılmış içerik ve ticaret biçiminde yeni bir mimari paradigma ortaya çıkmaya başladı (bunun içerik varyantına bazen “content mesh” te deniyor).

Herhangi bir içerik ve ticaret uygulamasının karşılaştığı en önemli sorunlardan biri, third-party entegrasyonlarını bir web uygulamasında kullanılabilir hale getiren plugin veya uzantıların mevcudiyetidir. Bu plugin’lerin çoğu, arama tool’u veya ödeme sistemi gibi içerik ve ticaret açısından önemli olan küçük sorunları ele alır. Monolitik içerik ve ticaret çağında çoğu plugin ekosistemi, aşağıda gösterildiği gibi sıkı bir şekilde kontrol altına alınmıştı. Örneğin WordPress plugin’leri hiçbir zaman Drupal modülleriyle değiştirilemezdi.

![Monolitik içerik ve ticaret mimarileri](/assets/images/2024/03/monolithic-content-and-commerce-systems.png "Monolitik içerik ve ticaret mimarileri"){: class="img-fluid"}

Dağıtılmış içerik ve ticaret mimarilerinin ortaya çıkmasının bir diğer nedeni de bu plugin’lerin uzun vadeli bakımına ilişkin artan endişeydi. Bir ödeme sistemi geliştiricisi, açık kaynak projesini geliştirmeyi bırakmaya veya plugin’i kaldırmaya karar verirse, ortaya çıkan kesinti, söz konusu plugin kullanıcısının ödeme sistemini üzerinde büyük bir etkiye sahiptir. Çoğu zaman tek çözüm, plugin’i sıfırdan yeniden oluşturmak veya özel bir uygulama tasarlamaktır.

Son birkaç yılda, aşağıda görüldüğü gibi, geliştiriciler arasında, yeni servisler ortaya çıktıkça değiştirilebilir sayfa içi servislere olan ilgi arttı. Yeni bir startup sınıfı, içerik ve ticaret ekosistemlerinden bağımsız sayfa içi widget'lar ve third-party servisler sunuyor; bu da bunların herhangi bir web uygulamasında kullanılabileceği anlamına geliyor (yine de uzun vadeli bakım sorunları olabilir ve varlıkları sona erebilir). Bu şirketler arasında arama özellikleri sağlayan Algolia; alışveriş sepeti ve ödeme sürecini sağlayan Snipcart; ve kolayca yönetilen web formları sunan Typeform vardır.

![Dağıtılmış içerik ve ticaret mimarileri](/assets/images/2024/03/distributed-cms-architecture.png "Dağıtılmış içerik ve ticaret mimarileri"){: class="img-fluid"}

Günümüzde bir içerik ve ticaret uygulamasının, web sitesini doldurmak için birden fazla kaynaktan veri çekmesi gerekebilir; backend veri sağlayıcıları ise arka planda eklenip kaldırılır. Dahası, bir içerik ve ticaret uygulaması, geliştiricilerin uygun gördüğü şekilde değiştirilebilen sayfa içi servislerin çok yönlü bir kombinasyonunu gerektirebilir. Tüm bunlar, içerik ve ticaret mimarileri için, geliştiricilerin çok çeşitli veri kaynaklarından ve bir o kadar çeşitli third-party plugin ve servislerinden yararlanmasına olanak tanıyan daha dağıtılmış bir geleceğe işaret ediyor.

### Jamstack

Jamstack mimarilerini diğer (yani monolitik ve ayrıştırılmış) mimarilerden bu kadar farklı kılan şeyin ne olduğuna geçmeden önce Jamstack'i tartışalım.

Jamstack framework’lerive metodolojileri, derleme zamanı render kavramını ortaya koymaktadır. Jamstack mimarilerinde, build başladıktan sonra render işlemini gerçekleştirmekten SSG sorumludur. Derleme sırasında SSG'nin JavaScript'i, API'lerden veri almaktan ve bu verileri statik markup’a dönüştürmekten sorumludur. Derleme sonucu, sürekli çalışan bir veritabanı, web sunucusu veya sunucu tarafı uygulaması yerine herhangi bir dosya sistemine yüklenebilen statik asset’lerin bir koleksiyonudur.

**Jamstack ve diğer mimariler arasındaki farklar**

Öte yandan, geleneksel mimariler (özellikle monolitik ve birçok ayrılmış içerik ve ticaret uygulaması), yalnızca bir kullanıcını web sitesi sayfasına istek yaptığında gerçekleşen çalışma zamanı derleme işlemini gerçekleştirir. Veritabanı bir istek alır ve gerekli verileri aktif olarak çalışan başka bir backend uygulamasına sağlar ve bu daha sonra sürekli çalışan bir web sunucusuna deploy edilir. Bu istenmeyen davranış, çoğu uygulamada agresif önbelleğe alma ve CDN yoluyla hafifletilir.

Jamstack mimarilerinin ortaya çıkmasından önce, sunucu tarafındaki tüm JavaScript uygulamaları, sürekli çalışan bir Node.js web sunucusundan yararlanıyordu. Ve tüm monolitik içerik ve ticaret mimarileri bu modeli takip ediyor. Ayrılmış içerik ve ticaret mimarilerine gelince, bu, derleme zamanı mı yoksa çalışma zamanı render’ın gerçekleştiğine bağlıdır. Dağıtılmış içerik ve ticaret mimarileri, derleme zamanı render’a en uygun şekilde katkıda bulunsa da, çalışma zamanı render uygulamak da mümkündür.

Çalışma zamanı oluşturmanın en önemli dezavantajı, yalnızca ayrı sayfaları üretmesidir. Bu sayfalar kesinlikle bir CDN'e yerleştirilebilir ve agresif bir şekilde önbelleğe alınabilir, ancak CDN'e veya önbelleğe alınmamış  yeni bir sayfa istendiğinde, çalışma zamanı render işleminin yeniden gerçekleşmesi gerekir.

**Sunucusuz altyapı**

Günümüzde Jamstack uygulamaları için derleme zamanı render ve CDN desteği sağlayan, özellikle de Gatsby'dekiler olmak üzere çok çeşitli altyapı sağlayıcıları bulunmaktadır. Toplu olarak bu altyapı sağlayıcıları serverless (sunucusuz) olarak bilinir çünkü Jamstack uygulamalarında meydana gelmeyen çalışma zamanı render gibi işlevselliklere güç sağlamak için sürekli çalışan sunuculara güvenmezler. Sunucusuz host’lar, Jamstack deploy’unun başarılı olması için genellikle üç öğeye ihtiyaç duyar:

- Statik site kod tabanı, genellikle GitHub gibi bir repository.
- Sunucusuz host tarafından yürütülen derleme ve render sürecini tetikleyen bir build komutu.
- Statik olarak oluşturulan asset’lerin yerleştirileceği URL'i temsil eden bir deploy hedefi.

En yaygın kullanılan sunucusuz altyapı sağlayıcılarından biri Netlify'dır. Diğerleri arasında Next.js framework’ün yaratıcısı Vercel (eski adıyla Zeit); Amazon'un AWS Amplify'ı; ve Gatsby uygulamalarında uzmanlaşmış Gatsby Cloud.

Artık Jamstack'ın temellerini anladığınıza göre, Gatsby'nin JavaScript dünyasına nasıl uyduğuna ve kullanıcı arayüzleri oluşturmak için JavaScript ve React'tan nasıl yararlandığına göz atalım.

## Gatsby'de JavaScript

JavaScript'in temel kavramlarına ve geçmişine kapsamlı bir genel bakış bu kitabın kapsamı dışındadır, ancak bir geliştirici olarak Gatsby ile çalışmak için gerekli olan en dikkat çekici kavramlardan birkaçını vurgulayalım.

JavaScript, bir web tarayıcısında çalıştırılabilen iki programlama dilinden biridir (diğeri bu yazının yazıldığı an itibariyle yaygın bir desteği bulunmayan WebAssembly'dir). Imperative orientation, weak typing, prototip tabanlı kalıtım ve fonksiyonel doğası ile karakterize edilir.

JavaScript tarihinin ilk yıllarında, öncelikle sınırlı etkileşim ve asenkron render ile statik web sayfalarını süslemek için kullanıldı. Ancak günümüzde JavaScript, web geliştirmede en önemli diller arasındadır ve geçtiğimiz yıllarda JavaScript ile çalışmış olanlar - CLI'ler ve modern JavaScript'in modülerliği nasıl kullandığı da dahil olmak üzere - JavaScript geliştirici deneyiminin birçok yönünün önemli ölçüde değiştiğini göreceklerdir.

### Komut Satırı Arayüzleri (CLI)

CLI'ler, geliştiricilerin local makinelerindeki dosya sistemleriyle etkileşimde bulunmak ve uygulama geliştirme, bağımlılık yönetimi ve deploy için araçlar sağlamak için kullandıkları metin tabanlı arayüzlerdir. En eski CLI'ler Shell veya Bash gibi düşük seviyeli dillerde yazılmış olsa da, bugün bu arayüzlerin çoğu Node.js (veya Rust ve Go gibi diğer diller) aracılığıyla JavaScript'te yazılmıştır.

Server side rendering (SSR) ve evrensel JavaScript yaklaşımları için kullanılan Node.js, yalnızca tarayıcı ortamı dışında yürütülmesi gereken JavaScript için kullanışlı değildir. Dosyaları işlemek ve uygulama geliştirme açısından önemli olan diğer eylemleri gerçekleştirmek için bilgisayarlarda yürütülmesi gereken JavaScript için de faydalıdır. Örneğin, birçok JavaScript uygulaması, çoğu zaman JavaScript alanındaki diğer geliştiriciler tarafından sağlanan kütüphaneler olmak üzere birçok bağımlılığın indirilmesini gerektirir.

Bugün, JavaScript bağımlılıkları, kütüphanelerin alınmasını ve bunlara ihtiyaç duyan uygulamaya enterpolasyonunu kolaylaştıran paket yöneticileri tarafından indirilmekte ve yönetilmektedir. En popüler paket yöneticileri, her biri geniş bir paket koleksiyonu sunan Node Package Manager (NPM) ve Yarn'dır. Bu paketlerin birçoğu kodun küçük bölümleri haline gelen bağımlılıklar olabilse de, diğer paketler genel olarak kurulabilen ve komutları yerel dosya sisteminin herhangi bir yerinde çağrılabilen CLI'ler içerir.

"Gatsby ile Çalışmaya Başlamak" bölümünde ele aldığımız Gatsby CLI, genellikle şu komut çağrılarak global olarak yüklenen JavaScript odaklı CLI'ın bir örneğidir:

```
npm install -g gatsby-cli
```

NPM ve Yarn, JavaScript dünyasında hem bağımlılık yönetimi hem de CLI kurulumuna yönelik en popüler yaklaşımlardır.

### Modüler JavaScript

Bugün JavaScript kayda değer bir modülerlik sergiliyor; dilin ilk kez 1990'larda tanıtıldığı zaman durum böyle değildi. O zamanlar, çoğu JavaScript kodu ya devasa boyutlarda tek bir gömülü dosyada ya da yürütme sırasını belirten art arda gömülü birden fazla JavaScript dosyasında tutuluyordu. 2000'lerin sonlarında ve 2010'ların başlarında JavaScript uygulamaları daha karmaşık hale geldikçe, birçok geliştirici modülerliğe daha iyi bir yaklaşımın gerekli olduğunu fark etti.

ES6 (ECMAScript 2015), modül kavramını tanıtan ilk JavaScript sürümüdür. Kısacası modüller, uygulamadaki diğer JavaScript dosyalarıyla dışa ve içe aktarma yoluyla ilişkileri olan izole edilmiş JavaScript dosyalarıdır. Biri modül sağlayan ve diğeri bu modülü kullanan iki JavaScript dosyasını düşünün (bu örnek, Axel Rauschmayer'in Exploring ES6 kitabından uyarlanmıştır). İşte ilki:

```
// lib.js
export const sqrt = Math.sqrt
export function sq(x) {
    return x * x
}
export function diag(x, y) {
    return sqrt(sq(x) + sq(y))
}
```

Bu, modül olarak başka bir uygulamaya aktarmak istediğimiz bir kütüphane ise, yalnızca ihtiyaç duyduğumuz dışa aktarılan fonksiyon adlarıyla başvurarak içe aktarabiliriz:

```
// app.js
import { sq, diag } from 'lib'
console.log(sq(5))
// 25
console.log(diag(4, 3))
// 5
```

Veya modülün tamamını içe aktarabilir ve ona atadığımız namespace altında başvurabiliriz:

```
// app.js
import * as lib from 'lib'
console.log(lib.sq(5))
// 25
console.log(lib.diag(4, 3))
// 5
```

Birçok JavaScript framework’ü ve JavaScript uygulaması, değerleri, fonksiyonları ve sınıfları birbirleri arasında iletmek ve böylece bunların uygulamanın diğer alanlarında çağrılabilmesi ve kullanılabilmesi için modüller kullanır. Gatsby bir istisna değildir ve JavaScript'te bileşen odaklı kullanıcı arayüzleri yazmaya yönelik bir kütüphane olan React da bir istisna değildir. Bir sonraki bölümde, Gatsby'yi daha geniş React ekosistemi içinde bağlamsallaştırmak için bilmeniz gereken temel React kavramlarına hızlı bir giriş yapacağız.

## Gatsby'de React

React kendisini kullanıcı arayüzleri oluşturmaya yönelik bir JavaScript kütüphanesi olarak tanımlıyor ancak etkileşimli kullanıcı arayüzleri yazmaya yönelik bildirimsel, bileşen tabanlı bir yaklaşım olarak tanımlanması belki daha iyi olur. Başlangıçta Facebook tarafından oluşturulan React, artık JavaScript uygulamaları oluşturmak için kullanılan en popüler kütüphanelerden biridir. Gatsby, React'ın üzerine inşa edilmiştir ve onun birçok konseptini miras almıştır.

Burada React'a kapsamlı bir genel bakış sunmak imkansız olabilir, ancak kitap boyunca birçok React konseptini ele alacağız. React söz konusu olduğunda keşfedilecek çok şey olsa da, şimdilik anlaşılması gereken en önemli fikirler bildirimsel render, render ve logic’in React bileşenlerinde yerleşimi ve React prop'larını kullanarak React bileşenleri arasında değerlerin aktarılmasıdır.

### JSX ile Bildirime Dayalı Render

JSX, çoğu React geliştiricisinin React'te rendering gerçekleştirmek için kullandığı sözdizimi uzantısına verilen addır. Örneğin şu “Hello World” örneğini düşünün:

```
const el = <h1>Hello, world!</h1>
```

JSX görünüm olarak HTML'e veya templating diline benzese de aslında React tarafından JavaScript sözdizimi olarak yorumlanır. Bu kod yürütüldüğünde bir HTML öğesi oluşturacak ve onu Document Object Model’a (DOM) eklemeye hazırlayacaktır.

```
const el = <h1>Hello, world!</h1>

ReactDOM.render(
    el,
    document.getElementById('root')
)
```

Çoğu React uygulaması, herhangi bir render gerçekleşmeden önce, yalnızca <body> öğesi içindeki bir <div id="root"></div>'den oluşur. React, JSX'te belirtilen tüm öğeleri o HTML öğesi içinde oluşturacaktır.

JSX ayrıca HTML'den farklı olarak öğe bildirimleri içinde ifadelere de izin verir:

```
const el = <h1>Hello, world! The current time is {Date().toLocaleString()}.</h1>

ReactDOM.render(
    el,
    document.getElementById('root')
)
```

JavaScript'teki mevcut anahtar kelimeler ile oluşabilecek çakışmalar veya React'ın camelCase'e yönelik stilistik tercihi nedeniyle HTML niteliklerinin JSX'te farklı şekilde adlandırılması gerektiğini unutmayın:

```
const el = <div tabIndex="0"></div>
const el = <div className="hero-image"></div>
```

JSX'te çift tırnak kullanamazsınız (JavaScript tarafından farklı şekilde yorumlandıkları için), bu nedenle JSX niteliklerine yapılan enterpolasyon küme parantezleri içinde gerçekleşmelidir:

```
const el = <img src={article.heroImageUrl} />
```

JSX ayrıca iç içe geçmiş öğelere de izin verebilir. Çok satırlı öğe bildirimleri için, bunları parantez içine almak en iyisidir, böylece JavaScript bunları ifade olarak yorumlayabilir:

```
const el = (
    <div className="hero-image">
        <img src={article.heroImageUrl} />
    </div>
)
```

JSX'i ham JavaScript'e derleyebilen bir JavaScript derleyicisi olan Babel'in, JSX'i JavaScript'e nasıl derlediğine dair bir örnek görelim. Aşağıdaki kod önceki örnekteki koda eşdeğerdir:

```
const el = React.createElement(
    "div",
    {
        className: "hero-image"
    },
    React.createElement(
        "img",
        {
            src: article.heroImageUrl
        }
    )
);
```

Artık React'ın bildirimsel render gerçekleştirmek için JSX'i nasıl kullandığını anladığınıza göre, dikkatimizi React'ın kullanıcı arayüzü geliştirmeye yönelik bileşen odaklı yaklaşımına çevirebiliriz.

### React Bileşenleri

React, uygulama geliştirmede önemli olan iki temel şeyin birlikte konumlandırılması konusunda açık bir tercih sergiliyor: markup ve mantık (logic). Markup ve mantığı isteğe bağlı olarak farklı dosyalara ayıran birçok tarihsel yaklaşımın aksine, React, her iki sorumluluğun tek bir dosyada birlikte konumlandırılmasını teşvik etmek için hem render kodunu hem de veri yönetimi mantığını içeren bileşenler üzerinde çalışır. Bu nedenle, önceki bölümde gördüğümüz bildirimsel render, genellikle her dosyada, işlenecek verileri yöneten mantıkla birlikte sunulur.

Bir bileşeni fonksiyon veya sınıf olarak tanımlayabilirsiniz:

```
// Function component
function HeroImage(props) {
    return (
        <div className="hero-image">
            <img src={props.heroImageUrl} />
        </div>
    )
}

// Class component
class HeroImage extends React.Component {
    render() {
        return (
            <div className="hero-image">
                <img src={this.props.heroImageUrl} />
            </div>
        )
    }
}
```

Şimdiye kadar, bileşen içinde meydana gelen dahili bildirime dayalı render tanımlamak için yalnızca standart HTML öğelerini kullandık. Ancak React, bileşenlerin JSX öğeleri olarak tanımlandıktan sonra yeniden kullanılmasına izin verir. React, mevcut bir HTML öğesi yerine bileşene eşdeğer bir JSX öğesiyle karşılaştığında, bu öğe bildiriminin içerdiği nitelikleri, React dilinde props olarak bilinen bir nesne olarak bileşene iletir ("properties"in kısaltması).

### React Prop’ları

Öğeyi bildirirken, makalenin "hero" görselinin URL'sini, props içinde yer alan yeni bir değerle değiştirdiğimiz aşağıdaki örneği düşünün. Burada ortaya çıkan HTML şu şekildedir: `<img src="https://img.example.com/hero.jpg" />`:

```
class HeroImage extends React.Component {
    render() {
        return <img src={this.props.src} />
    }
}

const el = <HeroImage src="https://img.example.com/hero.jpg" />

ReactDOM.render(
    el,
    document.getElementById('root')
)
```

Yeni React uygulamalarının çoğu, uygulamanın tamamını ve içerdiği bileşenleri temsil eden tek bir root-level App bileşeni içerir. Bu örnekte, az önce oluşturduğumuz diğer bileşeni içeren yeni bir App bileşeni oluşturuyoruz:

```
class HeroImage extends React.Component {
    render() {
        return <img src={this.props.src} />
    }
}

class App extends React.Component {
    render() {
        return <HeroImage src="https://img.example.com/hero.jpg" />
    }
)

ReactDOM.render(
    <App />,
    document.getElementById('root')
)
```

React'ta prop'ların, fonksiyon veya sınıf olarak tanımlanmalarına bakılmaksızın bileşenler içinde salt okunur olduğunu unutmayın. React'ın tüm prop’lar için geçerli olan katı bir kuralı vardır: "Tüm React bileşenleri, kendi prop’larına göre saf fonksiyonlar gibi davranmalıdır." Başka bir deyişle, bileşen kendi prop’larını değiştirmemelidir; bunları yalnızca öngörülebilir bir sonuç döndürmek için kullanmalıdır. Saf fonksiyonlar asla girişlerini değiştirmez.

Gatsby konseptlerini derinlemesine incelerken yeni React konseptleri ortaya çıktıkça bunları tek başına incelemek yerine Gatsby bağlamında inceleyeceğiz. Gatsby, bileşen ve prop kullanımı açısından temellerini güçlendirmek için React'ı yoğun bir şekilde kullanıyor. Şimdi ilk Gatsby sitemizi oluşturalım!

## Gatsby'yle Başlarken

Artık Gatsby'nin Java-Script ve React dünyalarına nasıl uyduğunu anladığınıza göre, ilk Gatsby sitemize başlamanın zamanı geldi. Bu bölümde Gatsby için local development ortamı oluşturmaya odaklanacağız.

### Komut Satırı

Diğer birçok web geliştirme aracı gibi, Gatsby de geliştiricilerin grafiksel araçlar yerine bilgisayardaki dosya ve klasörleri değiştirmesine olanak tanıyan metin tabanlı arayüz olan komut satırını kapsamlı bir şekilde kullanır. Terminal olarak da bilinen komut satırı, Gatsby'nin uygulama için temel olarak sağladığı local development ortamıyla etkileşim kurmanın birincil yoludur.

Local makinenizde komut satırını bulmak için:

- macOS'ta Launchpad’i açın ve “Terminal” yazarak uygulamayı başlatın.
- Windows 10'da Başlat düğmesine tıklayın ve cmd yazın. Ardından, döndürülen arama sonuçlarından Komut İstemi'ni seçin.
- Linux'te masaüstündeki application menüsünü açın, uygulamaları aramak için “Terminal” yazın ve Terminal'i seçin.

Her üç komut satırında da, komutu yazıp Enter tuşuna basarak bir komutu çalıştırabilirsiniz (örneğin, mevcut çalışma dizininin yolunu terminale yazdıran pwd veya bir dizine girmek için dizini değiştiren cd).

### Gatsby CLI'yi Yükleme

Gatsby CLI, geliştiricilerin Gatsby siteleriyle etkileşimde bulunmak için kullanabileceği birincil araçtır. Gatsby sitelerini çok hızlı bir şekilde oluşturabilen ve çalıştırabilen bir araçtır ve Gatsby sitelerini geliştirmek için yararlı olan çeşitli komutlar içerir. Bir NPM paketi olarak yayınlandığından, Gatsby CLI, Node.js'nin yüklü olduğu herhangi bir bilgisayara kurulabilir.

Gatsby CLI'yi kurmak için aşağıdaki NPM komutunu çalıştırın. Local makinenizin dosya sisteminin herhangi bir yerinde yeni Gatsby sitelerini oluşturabilmeniz için Gatsby CLI'yi global olarak -g flag’ini kullanarak kurmanız önemle tavsiye edilir:

```
npm install -g gatsby-cli
```

**Not**: Gatsby CLI'yi ilk yüklediğinizde, framework’ün gelecekteki gelişimine rehberlik etmek üzere Gatsby komutlarının anonim kullanım verilerini toplayan Gatsby telemetrisi hakkında sizi bilgilendiren bir mesaj görünecektir. Telemetri verilerinin toplanmasını önlemek için aşağıdaki komutu kullanabilirsiniz:

```
gatsby telemetry --disable
```

Gatsby CLI'de kullanabileceğiniz tüm komutları görmek için help komutunu çalıştırın:

```
gatsby --help
```

Artık ilk Gatsby komutunuzu başarıyla çalıştırdığınıza göre, Gatsby CLI'yi kullanarak ilk Gatsby sitenizi oluşturmaya hazırsınız. Siteyi sıfırdan oluşturmak yerine Gatsby'nin "Merhaba Dünya" starter’ı ile başlayacağız.

## İlk Gatsby Sitenizi Oluşturun

Gatsby starter’ları, bazı başlangıç yapılandırmalarına sahip, kısmen tamamlanmış Gatsby sitelerini içeren standart template’lerdir. Starter’lar, belirli bir kullanım durumu için yeni bir site oluşturmayı mümkün olduğunca sorunsuz ve kolay hale getirecek şekilde tasarlanmıştır. "Hello World" starter, tipik bir Gatsby sitesi için temel düzeydeki gereksinimleri içerir.

"Hello World" starter’ını temel alan yeni bir site oluşturmak için, terminalinizi açın ve ardından belirttiğiniz dizinde yeni bir proje oluşturan gatsby new komutunu çalıştırın (komutu herhangi bir argüman olmadan çalıştırırsanız, Gatsby CLI belirli parametreleri sağlayabileceğiniz etkileşimli bir shell açacaktır):

```
gatsby new hello-world \ https://github.com/gatsbyjs/gatsby-starter-hello-world
```

İsteğe bağlı bir dizin adı girebilirsiniz; eğer dizin mevcut değilse, Gatsby CLI devam edip dizini sizin için oluşturacaktır; örneğin:

```
gatsby new my-new-gatsby-site \ https://github.com/gatsbyjs/gatsby-starter-hello-world
```

Komutun https:// ile başlayan son kısmı, starter kodunun bulunduğu URL'dir. Gatsby, starter kod repolarını yönetmek için GitHub'u kullanıyor.

Artık dizine geçebilirsiniz:

```
cd hello-world
```

Dizine farklı bir ad verdiyseniz bunun yerine şunu kullanın:

```
cd my-new-gatsby-site
```

“Hello World” starter’ını kapsayan dosya ve dizinleri görmek için list komutunu (ls) verebilirsiniz. Yeni değiştirdiğiniz dizinde aşağıdaki komutu çalıştırarak, tipik bir Gatsby sitesinin kök klasörünün nasıl göründüğünü görebileceksiniz. -la flag’i, terminale, adları nokta (.) ile başlayan dosyalar da dahil olmak üzere tüm dosyaları ve bunlarla ilgili belirli ayrıntıları göstermesi talimatını verir:

```
ls -la
```

Bu komutun çıktısı projedeki dosya ve dizinlerin bir listesidir:

```
total 2240
drwxr-xr-x   13 fox  staff      416 27 Mar 16:47 .
drwxr-xr-x@   4 fox  staff      128 27 Mar 16:39 ..
-rw-r--r--@   1 fox  staff     6148 27 Mar 16:47 .DS_Store
drwxr-xr-x   12 fox  staff      384 27 Mar 16:39 .git
-rw-r--r--    1 fox  staff      974 27 Mar 16:38 .gitignore
-rw-r--r--    1 fox  staff      675 27 Mar 16:38 LICENSE
-rw-r--r--    1 fox  staff     5657 27 Mar 16:38 README.md
-rw-r--r--    1 fox  staff      219 27 Mar 16:38 gatsby-config.js
drwxr-xr-x  863 fox  staff    27616 27 Mar 16:39 node_modules
-rw-r--r--    1 fox  staff  1074190 27 Mar 16:39 package-lock.json
-rw-r--r--    1 fox  staff      703 27 Mar 16:38 package.json
drwxr-xr-x    4 fox  staff      128 27 Mar 16:47 src
drwxr-xr-x    3 fox  staff       96 27 Mar 16:38 static
```

Ama işimiz daha bitmedi! Bir Gatsby sitesinin dosya ve dizinlerinin listesini görmek yeterli değildir; onu bir tarayıcıda görmek isteriz. Ancak Gatsby sitemizi hâlâ çevrimdışıyken tarayıcıda görüntülemek için local development sunucusunu çalıştırmamız gerekecek. Bunu daha sonra yapacağız.

### Development Sunucusunu Başlatma

Henüz çevrimiçi olmayan bir Gatsby sitesini web tarayıcısında görüntülemek için, tarayıcının bu Gatsby sitesine erişebilmesini sağlayacak bir yol bulmamız gerekir. Bu, çevrimiçi web sitesini taklit edebilecek ve Gatsby sitemizi tarayıcıda ziyaret etme deneyimine mümkün olduğunca yakın olabilecek bir local development sunucusu kurmamız gerektiği anlamına gelir.

Gatsby CLI, sizin için local development sunucusu başlatan bir development modu içerir. Development moduna girmek ve local development sunucusunu başlatmak için Gatsby sitenizin kök dizininde aşağıdaki komutu çalıştırın:

```
gatsby develop
```

Gatsby CLI'den görüntülenecek mesajların bir parçası olarak http://localhost:8000 gibi bir URL göreceksiniz. Burada localhost, Gatsby'nin "Hello World" sitesine, herhangi bir yerde çevrimiçi olarak harici bir URL'den getirmek yerine, local makinemizde oluşturduğunuz "Hello World" sitesine eriştiğimiz anlamına geliyor. Bu, localhost URL'lerine erişmek için internet bağlantısına ihtiyacımız olmadığı anlamına gelir.

İstediğiniz tarayıcıyı açın ve http://localhost:8000 adresine veya terminalinizde belirtilen URL'ye gidin. Orada "Hello world!" mesajını göreceksiniz. İyi iş çıkardınız; Aşağıda görebileceğiniz gibi, ilk Gatsby sitenizi oluşturdunuz!

![Gatsby develop command](/assets/images/2024/03/gatsby-develop.png "Gatsby develop command"){: class="img-fluid"}

Terminale geri döndüğünüzde, gatsby develop’un hala çalışmakta olduğuna dikkat edin; Aşağıda gösterildiği gibi komut sonlandırılmadı. Bu, local development sunucusunu ihtiyaç duyduğunuz süre boyunca kullanmaya devam edebilmenizi sağlamak içindir. Gatsby development çalıştığı sürece, local development sunucusu localhost URL'sinde mevcut olacak ve kodda yaptığınız değişikliklerin büyük çoğunluğu tarayıcıya anında yansıtılacaktır.

![Gatsby localhost](/assets/images/2024/03/gatsby-localhost.png "Gatsby localhost"){: class="img-fluid"}

Hot reloading’i çalışırken görmek için src/pages/index.js dosyasını açın ve "Hello world!" yazısını değiştirin. Dosyanızı gatsby development hala çalışırken kaydederseniz, tarayıcının yeniden yüklenmesine gerek kalmadan değişikliğin anında etkili olduğunu göreceksiniz!

Development sunucusunu çalıştırmayı durdurmak için terminal penceresinde Ctrl-C tuşlayın. Bu, komutu sonlandıracak, local development sunucusunu kapatacak ve siteye tarayıcı aracılığıyla erişilemez hale gelecektir. Local development sunucusunu yeniden başlatmak için `gatsby develop` komutunu yeniden çalıştırın.

### Production Build Oluşturma

İlk Gatsby sitemizi kurmuş olsak da işimiz henüz bitmedi. Şu ana kadar bir web sitesinin development sunucusunda local olarak çalışmasını sağladık ancak bu, web sitemizin çevrimiçi olduğu anlamına gelmiyor. Bunun için Gatsby sitemizi, herkesin siteye erişebildiği, bir web sunucusuna sahip olan production ortamına deploy etmemiz gerekiyor. Ek olarak, bir Gatsby sitesini production’a hazır hale getirecek ve mümkün olduğunca küçültecek bir Gatsby build’i oluşturmamız gerekiyor, böylece herhangi bir performans sorunu yaşanmaz.

Production build oluşturmak, geliştirmeye hazır bir Gatsby sitesini, HTML, CSS, JavaScript ve resimler gibi statik varlıklardan oluşan, production’a hazır statik bir Gatsby sitesine dönüştürmek anlamına gelir. Production build oluşturmak için, Gatsby site dizininizin kökünde aşağıdaki komutu çalıştırın:

```
gatsby build
```

Bu komut, local ve işlenmiş statik dosyaları, production ortamına deploy etmeye hazır şekilde public dizinine depolayacaktır.

### Production Build’i Local Olarak Sunmak

Artık bu statik siteyi kendi seçeceğimiz bir altyapı sağlayıcısına deploy edebiliriz veya yerel olarak deploy edip tarayıcıda görüntüleyebiliriz. Production sitenizi local olarak görüntülemek için local development sunucusunu başlatan aşağıdaki komutu çalıştırın:

```
gatsby serve
```

Bu portu başka bir şey için kullanmıyorsanız, site https://localhost:9000 adresinde görüntülenebilir (local development sunucusundan farkına dikkat edin). İşte karşınızda: ilk Gatsby siteniz, local development veya production dağıtımına hazır!

**Kaynak:** “Gatsby: The Definitive Guide”, Preston So