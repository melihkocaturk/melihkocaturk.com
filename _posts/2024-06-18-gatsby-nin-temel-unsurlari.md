---
layout: post
title: Gatsby'nin Temel Unsurları
---

Önceki bölümde, Gatsby ile çalışmaya başladığınızda aşina olmanız gereken en önemli temel bilgilerden bazılarını ele aldık ve local geliştirme ortamınızda "Hello World" sitesi oluşturma adımlarını anlattık. Bu bölümde, 1. Bölüm'ün sonunda ele aldığımız konuları daha ayrıntılı olarak ele alacağız. Gatsby CLI, starter’lar çalışma, Gatsby sayfalarının ve bileşenlerinin nasıl çalıştığı, layout bileşeni, Gatsby'de CSS ve sitenize plugin’lerin nasıl ekleneceği hakkında daha fazla bilgi vereceğim.

Gatsby CLI'i Bölüm 1'in sonunda uzun uzun inceledik çünkü bu, bir Gatsby geliştiricisi olarak Gatsby sitelerinizle etkileşimde bulunacağınız birincil kanaldır. Gatsby geliştirmeye yönelik pek çok ortak görev, Gatsby CLI ile gerçekleştirilebilir ve bu kitap boyunca Gatsby CLI komutlarına sık sık döneceğiz. Bu nedenle CLI'e ve onun Gatsby geliştirme iş akışlarınızı hızlandırmak için neler yapabileceğine daha yakından bakarak başlamamız mantıklı olacaktır.

## Gatsby CLI

Gatsby CLI, geliştiricilerin geliştirme sırasında Gatsby siteleriyle etkileşimde bulunmalarının birincil yoludur. Scaffolding yoluyla (ortak bir şablona bağlı kalarak dosya ve dizinlerin oluşturulması) yeni bir Gatsby uygulaması kurmanın ve hata ayıklama için development sunucusu çalıştırmanın ana yolu budur. Gatsby CLI'e yeniden göz atacağız ve en önemli komutlardan bazılarını tartışacağız.

### Gatsby CLI'i Yükleme ve Yapılandırma

Gatsby CLI, `gatsby-cli` adında yürütülebilir bir JavaScript paketidir ve global olarak yüklerseniz dosya sisteminizin herhangi bir yerinde kullanabilirsiniz; ancak bazı komutlar yalnızca Gatsby kod tabanının mevcut olduğu dizinlerde çalışır. Bölüm 1'de kısaca ele aldığımız gibi, Gatsby CLI, NPM registry’de mevcuttur ve tüm kullanıcıların onu global olarak aşağıdaki komutu çalıştırarak yüklemeleri (i install’ın kısaltmasıdır) önerilir:

```
$ npm i -g gatsby-cli
```

Gatsby CLI'nin dahili öğelerinde gezinmenize yardımcı olacak komutların listesini ve yardım modunu görmek için, gatsby kelimesiyle başlayan aşağıdaki help komutunu çalıştırabilirsiniz:

```
$ gatsby --help
```

Bazı starter’lar, Gatsby CLI komutlarını, package.json dosyasında tanımlanabilen yürütülebilir komutlar olan NPM scriptlerine yerleştirir. NPM projelerinde package.json dosyası, projeye özel komutlar gibi diğer önemli bilgilerin yanı sıra söz konusu proje ve tüm bağımlılıkları hakkında bilgiler içerir. Artık Gatsby CLI kurulum sürecini ve yardım modunu ele aldığımıza göre, Gatsby CLI'de en sık çağrılan komutlardan bazılarına ve bunların neden kendi çalışmanızla alakalı olduğuna geçeceğiz.

Belirli senaryolarda Gatsby CLI komutlarını yürütmek için NPM scriptlerini kullanmak isteyebilirsiniz. Geliştiricilerin NPM scriptlerini kullanmak üzere takma adlar oluşturmasına izin vermek için package.json dosyanıza aşağıdakini ekleyin.

```
{
    "scripts": {
        "develop": "gatsby develop"
    }
}
```

Artık, `gatsby develop` komutuna erişmek için `gatsby develop` komutunu çalıştırmak yerine, tanımlanan NPM scriptine göre aşağıdaki komutu da çalıştırabilirsiniz:

```
$ npm run develop
```

### gatsby new

`gatsby new` komutu, belirli parametrelere dayalı olarak yeni Gatsby sitelerinin oluşturulmasından sorumludur. Aşağıdaki formatı alır:

```
$ gatsby new [site-name [starter-url]]
```

`gatsby new` komutu iki parametre kabul eder:

*site-name*
Gatsby sitenizin adı. Bu aynı zamanda yeni proje dizininizin adı olarak da kullanılır.

*starter-url*
Gatsby starter veya local dosya yoluna işaret eden bir URL. Bir starter URL'si vermezseniz, Gatsby CLI varsayılan olarak `gatsby-starter-default`'u kullanacaktır.

Varsayılan starter ile my-great-gatsby-site adında yeni bir Gatsby sitesi oluşturmak için, starter-url parametresi vermeden `gatsby new` komutunu çalıştırabilirsiniz:

```
$ gatsby new my-great-gatsby-site
```

Belirli bir starter ile yeni bir Gatsby sitesi oluşturmak için, starter URL'sini veya local dosya yolunu, starter-url parametresi olarak verebilirsiniz. Bu örnekte, starter seçeneği olarak `gatsby-starter-blog`'u kullanıyoruz:

```
$ gatsby new my-great-gatsby-site \ https://github.com/gatsbyjs/gatsby-starter-blog
```

Ayrıca yalnızca GitHub kullanıcı adı ve repo kullanarak starter’ı kullanabilirsiniz.

```
$ gatsby new my-great-gatsby-site gatsbyjs/gatsby-starter-blog
```

Makinenizin dosya sisteminde bulunan kendi özel starter’ınız için şunu kullanın:

```
$ gatsby new my-great-gatsby-site ~/path/to/my/custom/starter
```

İki parametreden herhangi birini vermezseniz, Gatsby CLI sizden bunları aşağıdakine benzeyen etkileşimli bir shell arayüzü aracılığıyla sağlamanızı isteyecektir:

```
$ gatsby new
create-gatsby version 3.13.1

Welcome to Gatsby!

This command will generate a new Gatsby site for you in /Users/fox with the setup you select. Let's answer some
questions:

What would you like to call
? > My Gatsby Site
```

`gatsby new` komutu, minimum yük ile kullanıma hazır bir Gatsby sitesi geliştirmeye başlamak için en hızlı giriş noktasıdır. Kendi Gatsby sitenizi kesinlikle sıfırdan yazabilseniz de, yeni bir kod tabanını sıfırdan başlatmak yerine, mevcut varsayılan starter’lardan birini kullanmayı ve onu ihtiyaçlarınıza göre uyarlamayı çok daha kolay bulacaksınız.

Artık Gatsby CLI'ın site oluşturmayı nasıl desteklediğini ele aldığımıza göre, Bölüm 1'in sonunda sitenizi tarayıcıda görmek için yürüttüğünüz komut olan `gatsby develop` hakkında daha fazla ayrıntıya geçeceğiz.

### gatsby develop

gatsby develop komutu herhangi bir Gatsby projesinin kök dizininde çalıştırılabilir:

```
$ gatsby develop
```

Yeni Gatsby sitenizi oluşturduktan sonra, dizini (cd) projenin kök dizinine değiştirebilir ve gatsby develop komutunu hemen çalıştırabilirsiniz:

```
$ gatsby new my-great-gatsby-site
$ cd my-great-gatsby-site
$ gatsby develop
```

`gatsby develop` komutu, kodunuz güncellendiğinde, hot reloading (otomatik tarayıcı yenileme) içeren bir local develop sunucusunu başlatır. Bu, kodunuzu değiştirirken ve değişiklikleri tarayıcınızda önizlerken `gatsby develop`‘u süresiz olarak çalışmaya devam ettirebileceğiniz anlamına gelir. Local develop sunucusundan çıkmak için Ctrl-C tuşlarına basmanız yeterlidir.

`gatsby develop` komutu birkaç seçeneği kabul eder:

*-host veya -H*
Bu, gatsby develop komutunuz için host’u ayarlar ve local development için varsayılan olarak localhost'u ayarlar.

*--port veya -p*
Bu, local geliştirme sunucunuzun portunu ayarlar ve varsayılan olarak env.PORT (tanımlanmış environment değişkenleriniz varsa) veya 8000 (hiçbiri ayarlanmadıysa) olarak ayarlanır.

*--open veya -o*
Herhangi bir manuel işlem yapmanıza gerek kalmadan siteyi sizin adınıza varsayılan tarayıcınızda açar.

*--https veya -S*
Sunucuya bir HTTPS development sunucusu kurmak için HTTPS kullanma talimatını verir.

`gatsby develop` komutu aynı ağdaki diğer cihazlardan geliştirme ortamınıza erişmek için host seçeneğiyle (--host veya -H) birlikte de kullanılabilir. Bunu yapmak için aşağıdaki komutu çalıştırın:

```
$ gatsby develop -H 0.0.0.0
```

Terminal çıktısı daha sonra bilgileri normal şekilde log’a kaydedecek ve ayrıca size diğer cihazları kullananlarla paylaşabileceğiniz bir URL sağlayacaktır:

```
You can now view gatsby-starter-hello-world in the browser.
⠀
Local: http://localhost:8000/
On Your Network: http://192.168.1.102:8000/
```

Sitenin local makineniz dışında nasıl görüntülendiğini görmek için aynı ağdaki bir cihazda bu URL'ye gidin.

### gatsby build

`gatsby build` komutu, Gatsby sitesini derlemek ve onu production’a (örneğin CDN'e) deploy etmeye hazırlamak için kullanılır. Genel olarak konuşursak, sitenizi web'de erişime açmadan önce atacağınız son adımdır:

```
$ gatsby build
```

`gatsby build` komutu projenizin kök dizininde çalıştırılmalıdır ve çeşitli seçenekleri kabul eder. Burada seçeneklerden birinin `--prefix-paths`'in *gatsby-config.js*'deki bilgilere dayandığını unutmayın; bunu Gatsby CLI'ın bu incelemesinden sonra tartışacağız:

*--prefix-paths*
Siteyi tüm bağlantıların ön eki ile oluşturun (Gatsby yapılandırmanızda pathPrefix'i ayarlayarak).

*--no-uglify*
Siteyi, JavaScript paketlerini uglify etmeden oluşturun (bu, oluşturulan paketlerde hata ayıklamayı çok daha kolaylaştırır).

*–profile*
Site performansını incelemek için siteyi React'ın profil oluşturma API'si etkinleştirilmiş şekilde oluşturun.

*--open-tracing-config-file*
Siteyi oluşturun ve OpenTracing uyumlu bir tracer yapılandırma dosyası açın. Bu, Bölüm 14'te geri döneceğimiz bir konu olan performans takibi içindir.

*--graphql-tracing*
Siteyi oluşturun ve her GraphQL çözümleyicisi için bir tracer açın. Bu seçenek site performansını düşürebilir.

*--no-color veya --no-colors*
Siteyi oluşturun ancak renk kodlu terminal çıktısını devre dışı bırakın.

### gatsby serve

`gatsby serve` komutu, sitenizin production build’ini local geliştirme sunucusundan sunmak için kullanılır, böylece sitenizin production sürümünü tarayıcıda görebilirsiniz:

```
$ gatsby serve
```

`gatsby serve` komutu projenizin kök dizininde çalıştırılmalıdır ve çeşitli seçenekleri kabul eder:

*--host veya -H*
Host’u seçtiğiniz host’a ayarlar. Varsayılan olarak localhost'tur.

*--port veya -p*
Portu seçtiğiniz porta ayarlar. Varsayılan olarak 9000'dir.

*--open veya -o*
Herhangi bir manuel işlem yapmanıza gerek kalmadan siteyi sizin adınıza varsayılan tarayıcınızda açacaktır.

*--prefix-paths*
Gatsby sitesine, Gatsby yapılandırmanızdaki pathPrefix'e göre ön eklenmiş bağlantı yollarıyla hizmet verir.

Bunlar, Gatsby sitenizle etkileşimde bulunurken düzenli olarak kullanacağınız başlıca komutlardır.

### Diğer Faydalı Gatsby CLI Komutları

Gatsby'nin geliştirilmesi için faydalı olan ancak diğerleri kadar sık kullanılmayan başka Gatsby komutları da vardır. Bu komutlar genellikle site hakkındaki bilgilerin görüntülenmesinden, sitenin cache klasörünün temizlenmesinden ve sizi pluginler ilgili belgelere yönlendirmekten sorumludur.

**gatsby info**

`gatsby info` komutu, Gatsby sitesinin görüntülendiği ortam hakkında yararlı bilgiler sağlamak için kullanılır; böylece bir hata bildirildiğinde, soruna yol açan durumu yeniden oluşturabilir:

```
$ gatsby info
```

`gatsby info` komutu projenizin kök dizininde çalıştırılmalıdır ve bir seçeneği kabul eder: --clipboard veya -C, terminal çıktısında görüntülenen ortam bilgilerini otomatik olarak panonuza kopyalar, böylece başkalarına gönderebilirsiniz.

**gatsby clean**

`gatsby clean` komutu, Gatsby'nin *.cache* klasöründeki, dahili önbelleğindeki ve derlenmiş dosyaların teslim edildiği public dizinlerdeki tüm öğeleri silmek için kullanılır:

```
$ gatsby clean
```

`gatsby clean` komutunun projenizin kök dizininde çalıştırılması gerekir ve kod tabanınızda aşağıdaki gibi sorunlar yaşandığında son çare olarak çok kullanışlıdır:

*Eski veriler*
Bazen Gatsby'nin dahili önbelleği, verileri doldurmak için kullanılan belirli dosyalar veya kaynaklarla senkronizasyonunu bozabilir. Belirli bir dosya veya kaynak görünmüyorsa veya yüklenmesi gereken verileri göremiyorsanız dahili Gatsby önbelleğini temizlemek sorunu çözebilir.

*GraphQL hataları*
Bir GraphQL kaynağı eklediyseniz ancak görünmüyorsa, `gatsby clean`'in yürütülmesi GraphQL'in tüm yeni kaynakları hesaba katmasını sağlayabilir.

*Bağımlılık sorunları*
Amaçlanandan farklı bir sürüme sahip bir bağımlılık veya terminalde var olmayan bir bağımlılıkla ilgili anlaşılmaz hatalar görüyorsanız, önbelleğinizin temizlenmesi gerekebilir.

*Plugin sorunları*
Local bir plugin geliştiriyorsanız, değişiklikleriniz ve eklediğiniz özellikler etkili olmuyorsa önbelleği temizleyin.

`gatsby clean` komutu, Gatsby'nin geliştirilmesi sırasında ortaya çıkabilecek çeşitli durumlarda faydalı olabilir ve bunlardan bazılarını daha sonraki bölümlerde inceleyeceğiz.

**gatsby plugin**

`gatsby plugin` komutu, Gatsby ekosistemindeki plugin’ler ile ilgili komutları yürütmek için kullanılır. `gatsby plugin docs` komutu sizi plugin’lerin nasıl kullanılacağı, kurulacağı ve yazılacağıyla ilgili belgelere yönlendirecektir:

```
$ gatsby plugin docs
```

**gatsby repl**

`gatsby repl` komutu, Gatsby ortamınız bağlamında bir Node.js read–eval–print döngüsü (REPL) açmak için kullanılır:

```
$ gatsby repl
```

`gatsby repl` ile etkileşimde bulunduğunuzda, Gatsby sizden belirli komutları yazmanızı isteyecektir.

```
$ gatsby >
```

Daha sonra site verilerinizin Gatsby'de nasıl kullanıldığı hakkında bilgi edinmek için bir REPL komutu yazabilirsiniz.

## Starter’lar

Bölüm 1'de kısaca gördüğümüz gibi, starter’lar, Gatsby topluluğu tarafından yönetilen ve Gatsby geliştirmeye hızlı bir şekilde başlamanızı amaçlayan standart Gatsby site template’leridir. `gatsby new` komutunu çalıştırdığınızda, starter reposunu klonlar, gerekli tüm bağımlılıkları yükler ve Git commit geçmişini temizler, böylece temiz bir sayfayla başlayabilirsiniz.

### Resmi ve Topluluk Starter’ları

Gatsby ekosistemi, Gatsby proje ekibi tarafından desteklenen bir dizi resmi starter sunar. Birçok yeni Gatsby geliştiricisi, `gatsby-starter-default` starter’ına geçmeden önce, Bölüm 1'de yaptığımız gibi, `gatsby-starter-hello-world` starter’ı ile başlar.

Aşağıdaki tablo resmi starter’ları ve onların en göze çarpan özelliklerini listelemektedir. Sonraki bölümlerde Gatsby tema starter’larını ele alacağız.

| Starter | GitHub URL | Kullanım Örneği | Özellikler |
| --- | --- | --- | --- |
| gatsby-starter-default | https://github.com/gatsbyjs/gatsby-starter-default | Çoğu kullanım durumu için yeterli | Temel Gatsby sitesi |
| gatsby-starter-blog | https://github.com/gatsbyjs/gatsby-starter-blog | Basit bir blog oluşturun | Blog yazısı sayfaları ve blog yazısı listeleri |
| gatsby-starter-hello-world | https://github.com/gatsbyjs/gatsby-starter-hello-world | Gatsby'yi kullanmaya başlayın | Gatsby sitesinin temel özellikleri |
| gatsby-starter-blog-theme | https://github.com/gatsbyjs/gatsby-starter-blog-theme | Blog gönderileri ve sayfaları içeren bir blog oluşturun | Gatsby temaları |
| gatsby-starter-theme-workspace | https://github.com/gatsbyjs/gatsby-starter-theme-workspace | Gatsby temalarını kullanmaya başlayın | Gatsby temaları |

`gatsby-starter-default starter`’ı, Gatsby geliştiricilerinin siteye sıfırdan başlaması için en yaygın kullanılan başlangıç noktasıdır. Gatsby'nin sayfa stratejisine ilişkin incelememiz sırasında sayfa, template ve bileşenler gibi temel kavramlar hakkında daha fazla konuşacağız.

Gatsby ayrıca topluluğa katkıda bulunanların, Gatsby topluluğu üyeleri tarafından oluşturulan başlangıçların bir özeti olan topluluk başlangıç kitaplığına dahil edilmek üzere başlangıçlar göndermelerine de olanak tanır. Kendiniz veya topluluk için kendi özel başlatıcınızı nasıl oluşturacağınız konusuna Bölüm 9'da döneceğiz.

### Starter’ları Değiştirme

Tüm Gatsby starter’ları kullanıma hazır tamamen işlevsel web siteleridir, ancak Gatsby geliştiricilerinin büyük çoğunluğu starter’ı değiştirmek ve ihtiyaçlarına göre özelleştirmek isteyecektir. Bu nedenle, ister bir blog, ister bir içerik veya ticaret sitesi veya başka bir şey oluşturuyor olun, gereksinimlerinizi karşılayan bir starter seçmek önemlidir. Belirli bir starter’da çalışmanız gereken dört ana alan vardır:

*gatsby-config.js dosyası*
Gatsby yapılandırma dosyası, kod tabanını bir Gatsby sitesi olarak tanımlar. Gatsby sitesi ve diğer kritik bilgiler için benzersiz bir ad sağlamak amacıyla, geliştiricilerin bir starter’ı klonladıktan sonra değiştirdiği ilk dosyalardan biri *gatsby-config.js* dosyasıdır.

*JSX'teki sayfa, template ve bileşenler*
Gatsby'nin sayfa ve bileşenlerinin önceden tanımlanmış yapısının, bunları nasıl düzenlemeyi tercih ettiğinizle veya ihtiyaçlarınızla tam olarak uyumlu olmaması muhtemeldir. Gatsby, bileşenler için birincil sözdizimi olarak JSX'i kullandığından, JSX konusunda biraz uzmanlığa ihtiyacınız olacak. Ayrıca benzer yapıya sahip sayfalar için, Bölüm 6'da ele alacağımız template’lere ihtiyacınız olacak.

*Dış veya iç kaynaklardan gelen veriler*
Verileriniz ister Gatsby sitenizin bulunduğu dosya sisteminde ister farklı bir sunucuda bulunan ayrı bir CMS veya ticaret sisteminde barındırılıyor olsun, başlangıçtaki dummy verileri kendi orijinal verilerinizle değiştirmeniz gerekecektir. Bunu 4. ve 5. Bölümlerde ele alacağız.

*CSS dosyaları*
CSS, web'de stil ve sunum için kullanılan standart dildir ve tüm Gatsby siteleri, belirli bir Gatsby sayfasına renk, düzen ve tipografi gibi önemli estetik unsurları eklemek için CSS'den yararlanır. Gatsby, CSS'nize nasıl yaklaşacağınız konusunda fikir sahibi değildir, bunun için Emotion ve Styled Components gibi çeşitli yaklaşımları kullanıma sunuyor.

Çok daha derin bir incelemeyi hak eden dış ve iç kaynaklardan alınan veriler dışında, bu tipik alanların her birine sırasıyla bakalım. *gatsby-config.js* dosyasını, Gatsby sayfalarını ve bileşenlerini, Gatsby'nin layout bileşenini ve Gatsby ile CSS'yi kullanmayı keşfederken Bölüm 2'nin geri kalanında bize yol gösterecek yeni bir proje oluşturacağız.

### Starter’dan Yeni Bir Proje Oluşturma

Terminalinizde aşağıdaki komutu yürütün:

```
$ gatsby new gtdg-ch2-pages-components gatsbyjs/gatsby-starter-hello-world
```

Burada, yeni Gatsby sitemizi içerecek yeni bir dizin (gtdg-ch-2-pages-components) oluşturduğumuzu ve Gatsby starter reposunu almak için GitHub kullanıcı adını ve repo stratejisini kullandığımızı unutmayın. Daha sonra bu dizine geçin:

```
$ cd gtdg-ch2-pages-components
```

ve *gatsby-config.js* dosyasını istediğiniz kod düzenleyicide açın. Örneğin, dosyayı Visual Studio Code'da açmak için şunu kullanın:

```
$ code gatsby-config.js
```

ve Atom için şunu kullanın:

```
$ atom gatsby-config.js
```

Gatsby'nin, bir Gatsby sitesi geliştirirken hangi kod düzenleyiciyi kullandığınız konusunda hiçbir fikri yoktur. Birçok geliştirici, dile ve framework’e özgü uzantılar ve hata ayıklama araçlarına yönelik zengin yetenekleri nedeniyle Visual Studio Code veya Atom'u tercih ediyor. Bu kod editörleri aynı zamanda tek komutla açılabilir olma avantajına da sahiptir.

### gatsby-config.js Dosyası

*gatsby-config.js* dosyasını seçtiğiniz kod düzenleyicide açma adımlarını izlediyseniz aşağıdaki içeriği göreceksiniz:

```
/**
 * Configure your Gatsby site with this file.
 *
 * See: https://www.gatsbyjs.com/docs/reference/config-files/gatsby-config/
 */

/**
 * @type {import('gatsby').GatsbyConfig}
 */
module.exports = {
  plugins: [],
}
```

Bu boş *gatsby-config.js* dosyasında hiçbir yapılandırma seçeneği ayarlanmamış ve hiçbir plugin yüklenmemiş. Gatsby sitenizi kendinize ait hale getirmenin ilk adımı, sayfa başlığını ve diğer sayfa meta verilerini belirterek site hakkında bazı bilgiler eklemektir. *gatsby-config.js* dosyanızın üst kısmına yeni bir `siteMetadata` nesnesi ve siteniz hakkında bazı bilgiler ekleyin. Fikir edinmek için bu örneği kullanabilirsiniz:

```
// gatsby-config.js
module.exports = {
  siteMetadata: {
    title: `My New Gatsby Site`,
    description: `Check out Gatsby with this easy-to-use site.`,
    author: `@melihkocaturk`,
  },
  plugins: [],
}
```

Tebrikler! Artık ilk Gatsby sitenizi benzersiz bir site olarak tanımlayarak kendinize ait hale getirdiniz. Şimdi dikkatimizi Gatsby sitesinde olup bitenlere çevirelim; bu da starter sayfalarımızdan birini açmak anlamına geliyor.

## Gatsby Sayfaları ve Bileşenleri

Sayfalar ve bileşenler, geliştiriciler için bir Gatsby sitesi oluştururken tipik giriş noktalarıdır. Sayfalar, Gatsby'nin bir site oluşturmak için kullandığı üst düzey bileşenlerdir; bileşenler ise bu sayfaların içine ve birbirlerine yerleştirilebilir. Yeni bir sayfa eklediğinizde, Gatsby bunu otomatik olarak tanıyacak ve site yapınıza dahil edecektir.

Peki bu tam olarak nasıl çalışıyor? Bu bölümde Gatsby sayfalarına, Gatsby bileşenlerine ve Gatsby sayfaları arasında nasıl bağlantı kurulacağına yakından bakacağız.

### Sayfalar

Gatsby sitelerinin en önemli atomik birimi olan sayfa (page) ile başlayacağız. Gatsby'de, *src/pages/.js* biçiminde bir adla tanımlanan herhangi bir React bileşeni, Gatsby tarafından otomatik olarak sayfa olarak tanınacak ve bu şekilde işlenecektir. “Hello World” starter’ımız da, *src/pages*'de zaten *index.js* adında bir sayfamız var.

Herhangi bir web sitesinin tipik ihtiyacı bir hakkında sayfasıdır, bu yüzden site hakkında biraz bilgi içeren about sayfası ekleyelim. Gatsby, *src/pages/.js* adlı her yeni dosyayı yeni bir bileşen olarak tanıdığı için, about.js adında bir dosya oluşturup bu dizine yerleştirebiliriz.

Öncelikle *src/pages/about.js* konumunda yeni bir dosya oluşturun ve aşağıdakini içine yapıştırın:

```
import React from "react"

export default function About() {
  return (
    <div>
      <h1>About us</h1>
      <p>Welcome to our about page! Here is some info about us.</p>
    </div>
  )
}
```

Ardından, eğer halihazırda `gatsby develop`’u çalıştırmıyorsanız, devam edin ve geliştirme sunucunuzu başlatın: 

```
$ gatsby develop
```

Artık *https://localhost:8000/about* adresine giderseniz yeni hakkında sayfanızı göreceksiniz. Ayrıca pages dizinine dosya eklemek dışında herhangi bir şey yapmanıza da gerek yoktu!

Gatsby'nin sayfaları nasıl oluşturduğu, Gatsby'nin bileşenlerden sayfaları nasıl oluşturduğu ve sayfa oluşturma sürecine nasıl dahil edileceği gibi sayfalar hakkında bilinmesi gereken çok daha fazla şey var. Ayrıca bazı sayfalarda, belirli bir kategorideki sayfaları (blog yazıları veya ürün sayfaları gibi) programlı olarak oluşturmak için kullanılan, sayfalar arasında paylaşılan bir format olan template’ler bulunur. Ancak şimdilik bu, Gatsby'deki sayfalara yeterli bir giriş niteliğindedir ve Gatsby bileşenlerine geçmemize olanak sağlar.

### Bileşenler

Gatsby'de bileşenler, geleneksel anlamda React bileşenleri gibidir: bunlar, birden fazla yerde yeniden kullanılmak üzere ayrı bir dosyaya yerleştirilmesi gereken bir sayfanın veya tekrarlanabilir kodun bölümleridir. Gatsby sayfası bağlamında bir bileşen header, footer veya sidebar olabilir. Bu aynı zamanda lead generation formu veya iletişim formu da olabilir veya site için önemli olan bir kod parçasını - Google Analytics kodu gibi - ekleyebilir. Hem ana sayfamızda hem de hakkımızda sayfamızda kullanılacak bir footer bileşeni oluşturalım. Öncelikle `src/components` konumunda yeni bir dizin oluşturun. Bu dizinde `footer.js` adında bir dosya oluşturun ve aşağıdaki kodu ekleyin:

```
// src/components/footer.js
import React from "react"

export default function Footer() {
  return <p>&copy; 2024 My Gatsby Site. All rights reserved.</p>
}
```

Şimdi yeni footer bileşenimizi kullanacak şekilde sayfaların her birini değiştirelim. Öncelikle, ek bir import ifadesi ekleyerek ve JSX'teki bileşeni çağırarak footer’ı hakkımızda sayfamıza ekleyelim:

```
import React from "react"
import Footer from "../components/footer"

export default function About() {
  return (
    <div>
      <h1>About us</h1>
      <p>Welcome to our about page! Here is some info about us.</p>
      <Footer />
    </div>
  )
}
```

Şimdi aynısını *src/pages/index.js*'de yapacağız:

```
import * as React from "react"
import Footer from "../components/footer"

export default function Home() {
  return (
    <div>
      Hello world!
      <Footer />
    </div>
  )
}
```

React, JSX öğelerine nitelikler ekleyerek bir bileşenin hangi property’lere (prop) sahip olması gerektiğini tanımlamamıza olanak tanır. Her iki sayfada da aynı copyright metnini kullanmak yerine, sayfanın copyright bildiriminin hangi yıla ait olması gerektiğini tanımlamasını sağlayalım. *src/components/footer.js* dosyasını aşağıdaki şekilde değiştirin:

```
// src/components/footer.js
import React from "react"

export default function Footer(props) {
  return <p>&copy; {props.copyrightYear} My Gatsby Site. All rights reserved.</p>
}
```

Şimdi hakkında sayfamızda eksik copyright yılını doldurmak için footer’ın ihtiyaç duyduğu prop’u verelim:

```
// src/pages/about.js
import React from "react"
import Footer from "../components/footer"

export default function About() {
  return (
    <div>
      <h1>About us</h1>
      <p>Welcome to our about page! Here is some info about us.</p>
      <Footer copyrightYear="2024" />
    </div>
  )
}
```

*https://localhost:8000/about* adresindeki hakkında sayfasına gittiğinizde aynı copyright mesajını göreceksiniz, ancak bu sefer prop’u footer bileşeninin kendisi yerine footer bileşenini kullanırken tanımlıyorsunuz. Aynı şeyi *index.js*'de de yapın.

Gatsby'de sayfalar arasında paylaşılan davranışı tanımlamak için yaygın olarak kullanılan, layout bileşeni olarak bilinen özel bir bileşen türü vardır. Bunu daha sonra tartışacağız.

### Sayfalar Arasında Bağlantı Oluşturmak

Artık sayfalar ve bileşenler ve bunların birbirleriyle nasıl ilişki kurduğu hakkında temel düzeyde anlayışa sahip olduğunuza göre, dikkatimizi Gatsby'de bulunan ve aslında framework’e özgü olan başka bir bileşen türüne çevirebiliriz: Gatsby sayfaları arasında bağlantı kurmayı kolaylaştıran `<Link />` bileşeni. Gatsby'nin sayfalar arasında yönlendirmeyi nasıl gerçekleştirdiğine bir göz atalım.

Öncelikle ana sayfamızı açalım ve hakkında sayfamıza `<Link />` bileşenini ekleyelim. `<Link />` bileşenini import etmemiz gerektiğini unutmayın:

```
// src/pages/index.js
import * as React from "react"
import { Link } from "gatsby"
import Footer from "../components/footer"

export default function Home() {
  return (
    <div>
      <p>Hello world!</p>
      <p><Link to="/about/">About</Link></p>
      <Footer copyrightYear="2024" />
    </div>
  )
}
```

Şimdi hakkında sayfamıza ana sayfaya dönüş bağlantısını ekleyelim:

```
// src/pages/about.js
import React from "react"
import { Link } from "gatsby"
import Footer from "../components/footer"

export default function About() {
  return (
    <div>
      <h1>About us</h1>
      <p>Welcome to our about page! Here is some info about us.</p>
      <p><Link to="/">Home</Link></p>
      <Footer copyrightYear="2024" />
    </div>
  )
}
```

Artık `<Link />` bileşenini kullanarak iki farklı sayfa arasında bağlantı kurabiliyoruz.

`<Link />` bileşeni, Gatsby sayfaları arasındaki herhangi bir bağlantı için kullanılmalıdır. Harici bağlantılar veya anchor bağlantıları için `<Link />` yerine HTML `<a>` etiketini kullanmalısınız. Harici URL'ler Gatsby'nin bir parçası olmadığından `<Link />` çalışmaz.

### Layout Bileşeni

Dikkatimizi CSS'e çevirmeden önce, Gatsby'deki en önemli bileşenlerden birini ele almamız gerekiyor; bu bileşen, CSS'den nasıl yararlanacağımız konusunda önemli bir etkiye sahiptir. Layout bileşenleri, sitenizin birden fazla sayfasında paylaşılan ve bu sayfalarda stil sağlamak için en sık kullanılan bölümlerdir (önceki bölümde oluşturduğumuz footer bileşeninin aksine).

Gatsby varsayılan olarak sayfalara hiçbir zaman belirli bir layout uygulamaz çünkü bu, React'ın iç içe geçebilen bileşenleri import etme ve kullanma konusundaki kompozisyon yaklaşımına bağlıdır. Bileşenlerin iç içe yerleştirilebilirliği, Gatsby sitelerindeki template’lerde zengin hiyerarşilere olanak tanır ve her sayfada görünen header ve footer’ın yanı sıra yalnızca belirli sayfalarda görünmesi gereken belirli bileşenler oluşturabilirsiniz. Tüm Gatsby bileşenleri React bileşenleri olduğundan, template ve sayfa bileşenleri arasında veri aktarımı çözülmesi gereken önemsiz bir sorundur.

Layout bileşenleri Gatsby'de önemlidir çünkü Gatsby geliştiricileri olarak biz, Gatsby'ye farklı layout’lara sahip sayfaların farklı bileşenleri görüntülemesi gerektiğini bu şekilde gösterebiliriz. Örneğin bir Gatsby sitesinin ana sayfasında genel bir footer ve header mevcut olabilir, ancak geliştiriciler dahili sayfalara menü içeren ek bir sidebar eklemek isteyebilir.

Gatsby, layout bileşenlerinizi, *src/components/* gibi aynı dizindeki diğer bileşenlerinizin yanına yerleştirmenizi şiddetle tavsiye eder. Gatsby dökümantasyonun dan uyarlanan ve *src/components/layout.js* adresine yerleştireceğimiz basit bir layout bileşeni örneği:

```
// src/components/layout.js
import React from "react"

export default function Layout({ children }) {
  return (
    <div style={{ margin: `0 auto`, maxWidth: 960, padding: `0 1.5rem` }}>
      {children}
    </div>
  )
}
```

Burada, `<div>` öğesinde satır içi stil kullandığımızı ancak yakında bu kodu yeniden düzenleyeceğimizi unutmayın.

JSX'te tire içeren CSS property adlarını kebab-case'den camelCase'e yeniden düzenlemelisiniz, çünkü kebab-case daha sonra JavaScript'te sorunlara yol açabilir.

Artık ana sayfamıza dönebilir ve bu layout’u, sayfadaki tüm markup’ın layout bileşenimiz tarafından çevreleneceği şekilde sayfanın içeriğine uygulayabiliriz. Bu layout’a sahip olmasını istediğimiz herhangi bir sayfa veya template, tanımladığımız `<Layout>` bileşenini kullanmalıdır. Az önce oluşturduğumuz layout bileşenini ana sayfamıza import ettiğimizi unutmayın:

```
// src/pages/index.js
import React from "react"
import { Link } from "gatsby"
import Footer from "../components/footer"
import Layout from "../components/layout"

export default function Home() {
  return (
    <Layout>
      <p>Hello world!</p>
      <p><Link to="/about/">About</Link></p>
      <Footer copyrightYear="2024" />
    </Layout>
  )
}
```

Artık ilk layout bileşenimize sahip olduğumuza göre, ilk global stilimizi tanımlayabiliriz.

## Gatsby'de CSS Kullanmak

Gatsby'de, web sitenizin tipografisini, renklerini ve düzenini ayarlamanıza olanak tanıyan birkaç farklı stil yaklaşımı vardır. Bu bölümde Gatsby'nin kullanıma sunduğu üç CSS yaklaşımının her birini ele alacağız:

*Global Stil*
CSS'in birçok kullanımında, tek bir stylesheet (CSS dosyası) indirilir ve web sitesine stil vermek için kullanılır. Gatsby ayrıca, CSS'in genel kurallarına göre global kapsama (scope) sahip tek bir CSS dosyası sağlamanıza veya bir bileşenle ilişkili ancak stilleri hâlâ global olarak geçerli olan çeşitli CSS dosyaları sağlamanıza olanak tanır. Bununla birlikte, kullanılmayan stilleri çıkarmak için CSS'i sonradan düzenlemezseniz, global stylesheet’te bazı ölü CSS'lerin bulunabileceğini unutmayın. Bu, çoğu web geliştiricisinin zaten aşina olduğu geleneksel yaklaşımdır.

*Modüler Stil*
Gatsby ayrıca CSS bildirimlerinin kapsamını tek tek Gatsby bileşenlerine göre ayarlamanıza olanak tanıyan modüler stylesheet de sağlar. Bu modelde CSS geleneksel olarak Gatsby bileşenleri içerisinde yazılır ancak JavaScript tarafından işlenir. Bu, local kapsam belirlemeye olanak tanır ve başka yerlerdeki istenmeyen sonuçların önlenmesini sağlar. Gatsby bu CSS paradigmasını kullanıma hazır olarak sunuyor.

*JavaScript'te CSS*
Son yıllarda, JavaScript uygulamalarında CSS için yeni bir paradigma olan JS'de CSS ortaya çıktı. Bu yaklaşımla, CSS bildirimleri hala local olarak kapsam dahilindedir ve JavaScript'te tüketilmektedir, ancak JavaScript, CSS'i tamamen yönettiğinden, asenkron olaylara dayalı dinamik stillendirme mümkündür. Bu yaklaşım third-party bağımlılıklarının kullanılmasını gerektirir.

Gatsby'nin hangi CSS paradigmasını kullanmanız gerektiği konusunda hiçbir fikri veya reçetesi olmadığını ve üçünün de kullanımının Gatsby topluluğunda yaygın olduğunu belirtelim. Ek olarak, bu seçeneklerin üçünde de bağımlılık yönetimiyle ilgili baş ağrılarından kaçınmanıza yardımcı olabilecek hem resmi hem de topluluk pluginleri bulunur. Her birine daha yakından bakalım.

### Global Stil

Geleneksel CSS yaklaşımlarında, global kapsamlı CSS, *.css* uzantısına sahip harici stylesheet’te bulunur. Gatsby'de global stylesheet kullanmanın iki yolu vardır: layout bileşeni olmadan veya layout bileşeniyle.

**Layout bileşeniyle global stil**

Global stilleri layout bileşeniyle uygulamak, halihazırda var olan bir CSS’i Gatsby sitesine uygulamanın veya geleneksel CSS yaklaşımlarına bağlı kalan ve her tarayıcıda çalışacak CSS yazmanın en hızlı yoludur.

Global CSS’i layout bileşeniyle kullanmak için, header, footer, sidebar ve menüler gibi diğer ortak bileşenler de dahil olmak üzere sitedeki tüm paylaşılan bileşenleri çevreleyen bir layout bileşeni ekleyebiliriz. Gatsby'nin varsayılan stater’ı da aynı yaklaşımı kullanıyor ancak layout bileşeninin global stillerle nasıl çalıştığını öğrenmek için bunun yerine "Hello World" starter’ı nı kullanacağız.

Local makinenizde yeni bir Gatsby sitesi oluşturun. Burada doğru starter’ı hedeflemek için GitHub kullanıcı adı/repo stratejisini kullandığımızı unutmayın:

```
$ gatsby new gtdg-ch2-global-styling-with-layout \ gatsbyjs/gatsby-starter-hello-world
$ cd gtdg-ch2-global-styling-with-layout
```

Daha sonra, *src/components* konumunda yeni bir dizin oluşturun ve bunun içinde *layout.js* ve *layout.css* adında iki yeni dosya oluşturun. *layout.js* dosyası layout bileşenimizi temsil ederken, *layout.css* dosyası da o layout bileşeniyle ilişkili stilleri temsil eder.

Artık önceki bölümde satır içi tanımladığımız stillerin aynılarını alıp *src/components/layout.css* dosyasına ekleyebiliriz:

```
/* src/components/layout.css */
div {
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
}
```

Sayfayı yüklendiğinde hemen farkedilecek, kolayca tanımlanabilen bir CSS kuralı da ekleyelim:

```
/* src/components/layout.css */
div {
  /* Previous lines of CSS */
  background-color: blue;
}
```

Şimdi, yeni oluşturduğumuz stylesheet’i içe aktaralım ve diğer bileşenlerin de kullanabilmesi için layout bileşenini dışa aktaralım:

```
// src/components/layout.js
import React from "react"
import "./layout.css"
export default function Layout({ children }) {
  return <div>{children}</div>
}
```

Global stillerimizi layout bileşenine uyguladığımız için `<div>` öğemizin artık herhangi bir satır içi stile ihtiyacı olmadığını unutmayın.

Son adım, ana sayfamızı layout bileşeni ile güncellemek, böylece ana sayfanın layout bileşenini ve bununla ilişkili stilleri kullanması sağlanır:

```
// src/pages/index.js
import React from "react"
import Layout from "../components/layout"
export default function Home() {
  return <Layout>Hello world!</Layout>
}
```

Şimdi projemizin kök dizininde `gatsby develop `komutunu çalıştırırsak, sayfamızın mavi arka plan rengine sahip bir `<div>` içerdiğini göreceğiz:

```
$ gatsby develop
```

Ancak layout bileşeni kullanımının tercih edilmediği durumlar da vardır. Daha sonra, layout bileşenini kullanmadan global stillerin nasıl yazılacağını tartışacağız.

**Layout bileşeni olmadan global stil**

Sitenizdeki bileşenleri tasarlama şekliniz nedeniyle veya sitenizdeki başka bir öğenin layout bileşeninin kullanımını engellemesi nedeniyle, global stilleri yönlendirmek için layout bileşenini kullanmak istemeyeceğiniz durumlar vardır. Neyse ki, React paradigmasından ayrı olarak global stylesheet eklemek için Gatsby brower API'sini kullanabilirsiniz.

Global stili desteklemek için layout bileşeni yerine *gatsby-browser.js*'den nasıl yararlanabileceğimize göz atmak için yeni bir "Hello World" sitesi oluşturalım. Öncelikle “Hello World” starter’ın yeni bir kopyasıyla başlıyoruz:

```
$ gatsby new gtdg-ch2-global-styling-without-layout \ gatsbyjs/gatsby-starter-hello-world
$ cd gtdg-ch2-global-styling-without-layout
```

Şimdi bir stylesheet oluşturalım ve onu *src/styles/global.css* dosyasına kaydedelim:

```
/* src/styles/global.css */
div {
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
  background-color: blue;
}
```

Daha sonra projemizin kök klasöründe *gatsby-browser.js* adında yeni bir dosya oluşturacağız ve global stilimizi aktaracağız:

```
// gatsby-browser.js
import "./src/styles/global.css"
```

Global stiler, geliştiricilerin yıllardır CSS yazarken kullandıkları birincil araç olsa da, isim çakışmaları ve istenmeyen miras örnekleri de dahil olmak üzere global stillerin önemli dezavantajları vardır. Bileşen stillerinin yardımcı olabileceği yer burasıdır.

**Bileşen stylesheet’te global stil**

Birçok Gatsby geliştiricisi tüm stillerini tek bir yerde tutmak için global stylesheet kullanırken,
diğerleri CSS lerini bileşen düzeyindeki CSS dosyalarına veya hatta tek bir Gatsby bileşeni için geçerli olan ayrı sınıf bildirim bloklarına bölmeyi tercih eder. Ekip üyelerinin ayrı ayrı çalışmasına ve yalnızca belirli bir sayfa, template veya bileşen için ihtiyaç duyduğunuz CSS dosyalarını içe aktarmasına olanak tanımak için global stylesheet’i birden çok bileşen stylesheet’ine bölebilirsiniz.

Örneğin, *components* dizininde (daha iyi organizasyon için) farklı bileşenler için ayrı CSS dosyaları oluşturabilir ve bunları bileşenlerinize aktarabilirsiniz. Bileşen stylesheet leri ile global stile geçmek için önceki örnek olan gtdg-ch2-pages-components'a geri dönün, çünkü orada tanımlanmış bir footer bileşenimiz var. Daha önce uyguladığımız stillere sahip "footer" bileşenini içe aktarmanız gereken bir senaryo düşünün:

```
/* src/components/footer.css */
p {
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
  background-color: blue;
}
```

Şimdi footer bileşenimize döndüğümüzde tek yapmamız gereken CSS dosyasını içe aktarmak:

```
// src/components/footer.js
import React from "react"
import "./footer.css"

export default function Footer(props) {
  return <p>&copy; {props.copyrightYear} My Gatsby Site. All rights reserved.</p>
}
```

Bu stylesheet’ler hala global namespace e ihtiyaç duyduğundan, global selector kullanan bir *footer.css* dosyasının stilleri tüm siteye uygulanacağından, aynı sorunların çoğunu sunabilirler. Sonraki bölümlerde, CSS Modülleri ve JS'de CSS ile JavaScript'in CSS imizin kapsamını global yerine local olarak belirlememize nasıl yardımcı olabileceğini tartışacağız.

### CSS Modülleri ile Modüler Stil

Gatsby, CSS yazma konusunda bileşenlere yönelik iki yaklaşım sunar: CSS Modülleri ve JS'de CSS (sonraki bölümde ele alınmıştır). CSS'in kapsamını tek tek bileşenlere ayırmak, Gatsby geliştiricilerinin taşınabilir ve bakımı daha kolay olan geleneksel CSS yazmalarına olanak tanır. Yalnızca tek bir bileşene uygulanan stiller hiçbir zaman farklı bir bileşene sızmaz veya diğer bileşenlerin stilini değiştirmez.

CSS Modülleri, bileşen kapsamlı CSS yazmanın iyi bilinen bir yoludur. Kısacası, bir CSS Modülü, tüm sınıf adlarının kutudan çıktığı haliyle local olarak kapsandığı CSS stylesheet’tir. CSS Modüllerinde CSS stilleri, bileşenler arasında aktarılan JavaScript nesneleri haline gelir. CSS Modülleri sınıflara ve animasyonlara otomatik olarak benzersiz adlar atadığından, farklı bileşenlerdeki aynı adlı iki selector arasında çakışma riski yoktur.

Bunu açıklamak için önceki bölümdeki örneğe dönelim (geleneksel CSS'in bileşene aktarılması durumu) ve onu aynı bileşene aktarılan bir CSS Modülüne dönüştürelim. CSS dosyamıza yapmamız gereken tek şey onu yeniden adlandırmak ve *src/components/footer.module.css* dizinine taşımak.

Ayrıca, CSS Modüllerine bu bakışın amaçları doğrultusunda, `<div>` öğesi yerine bir sınıf adı seçelim:

```
/* src/components/footer.module.css */
.footer {
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
  background-color: blue;
}
```

Şimdi, CSS stil sayfasını doğrudan içe aktarmak yerine CSS Modül paradigmasını kullanmak için *src/components/footer.js* dosyasındaki kodu değiştirin:

```
// src/components/footer.js
import React from "react"
import * as footerStyles from "./footer.module.css"

export default function Footer(props) {
  return <p className={footerStyles.footer}>&copy; {props.copyrightYear} My Gatsby Site. All rights reserved.</p>
}
```

`ClassName` değerimizdeki, aradığımız belirli sınıf adını bulmamıza olanak tanıyan sözdizimine dikkat edin: `.footer. import` kullandığımız için, o stylesheet te bulunan stillerden herhangi birini bileşen içinde kullanabiliriz ve hiçbir stil, bileşenin kapsamından kaçamaz. CSS Modülleri, sınıf adını footer-module--container--2KckL gibi dinamik bir CSS sınıfı adına dönüştürecektir.

Daha sonra, JS'de CSS ile bileşenlere local olarak nasıl stil uygulanacağına göz atacağız.

### JS'de CSS

JS'de CSS, CSS Modüllerinden çok farklı bir yaklaşımdır; CSS'i harici CSS dosyaları yerine JavaScript'te yazma fikrine odaklanır. Bu yaklaşımı kullanırken, CSS'nin bazı geleneksel söz dizimlerinin JavaScript'te aynı şekilde yazılamayacağını unutmamak önemlidir (örneğin, Java-Script'te backgroundColor camelCase iken, CSS'de background-color kebab-case'dir).

JS'de CSS, kapsamlı stillendirmeyi, ölü kodları ortadan kaldırmayı ve dinamik stil söz konusu olduğunda daha iyi performansı vurgulayan bir paradigmadır. Aşağıdaki özelliklere sahiptir:

*Bileşen odaklı*
JS'de CSS, React ve Gatsby ile aynı bileşen metodolojisini kullandığından (tüm CSS'ler bileşenler biçiminde gelir), JS'de CSS'nin genellikle diğer yöntemlerden daha zarif olduğunu göreceksiniz.

*Local kapsam*
CSS Modülleri gibi, JS'deki CSS'in kapsamı da varsayılan olarak ayrı bileşenlere ayrılmıştır.

*Dinamik stil*
Bileşen durumundaki istemci tarafı değişiklikleri nedeniyle stillerde eşzamansız değişikliklere ihtiyacınız varsa, bu duruma bağlı olan JavaScript değişkenlerini JS'nizdeki CSS'nize entegre edebilirsiniz, böylece değer çevreye göre değişebilir.

*Performansa göre ayarlanmış*
JS'de CSS kütüphaneleri, çakışmayan benzersiz sınıf adları oluşturur; böylece daha kolay önbelleğe alma, otomatik vendor prefix, en önemli CSS stillerinin hızlı yüklenmesi ve diğer özellikleri destekler.

JS'de CSS, CSS'i işlemek için JavaScript topluluğunda çok popüler olsa da, geleneksel CSS'den gelenler için bu o kadar kolay değildir ve geleneksel CSS'i JS'de CSS'e (veya tam tersi) refactor etmek zaman alabilir. Ve Gatsby'nin hangi CSS stratejisini kullandığınız konusunda hiçbir fikri olmadığı için, JS'de CSS her zaman CSS-in-JS kütüphanesi biçiminde ek bir bağımlılığın yüklenmesini gerektirir.

Gatsby'de yaygın olarak kullanılan iki kütüphane vardır ve burada her ikisini de ele alacağız: Emotion ve Styled Components.

**Emotion**

Emotion, önceden var olan diğer kütüphaneleri temel alan, dize veya nesne stili bildirimlerini kullanarak uygulamalara stiller eklemenizi sağlayan, JS'de CSS kütüphanesidir. Emotion, geleneksel CSS kullanımına eşlik eden bazı kaygılarından kaçınmak için öngörülebilir stil kompozisyonundan yararlanır. Ayrıca Emotion, daha iyi hata ayıklama deneyimi ve performans için daha iyi önbellekleme sunmak amacıyla kaynak haritaları (source map) ve etiketleri kullanır.

Emotion'u kullanabilmek için ilk plugin’imizi kurmamız gerekiyor, bunu başka bir "Hello World" starter kurduktan sonra yapacağız:

```
$ gatsby new gtdg-ch2-css-in-js-emotion gatsbyjs/gatsby-starter-hello-world
```

Artık sitemizi oluşturduğumuza göre, sitedeki dizini değiştirelim ve NPM kullanarak Emotion için gereken bağımlılıkları ekleyelim:

```
$ cd gtdg-ch2-css-in-js-emotion
$ npm install gatsby-plugin-emotion @emotion/react @emotion/styled
```

Son adımımız *gatsby-config.js* dosyasını açmak ve plugin’i oradaki plugin listesine eklemek. Yüklü başka plugin’iniz yoksa aşağıdaki gibi görünmesi gerekir:

```
// gatsby-config.js
module.exports = {
  plugins: [`gatsby-plugin-emotion`],
}
```

Şimdi, bir header bileşenine ilişkin stilleri Emotion sayfamıza tanıtalım. Emotion'ın, bu stillerin JSX öğeleri üzerinde nasıl etkili olacağını tanımlayan mantıkla aynı dosyada olmayı tercih ettiğini unutmayın:

```
// src/pages/index.js
import React from "react"
import styled from "@emotion/styled"
import { css } from "@emotion/react"

const Header = styled.div`
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
  background-color: blue;
  color: white;
`
export default function Home({ children }) {
  return (
    <Header>
      <div>Welcome to my Gatsby site!</div>
    </Header>
  )
}
```

Gördüğünüz gibi burada header’ın CSS kurallarını tanımlamak için çok satırlı bir dize kullanıyoruz.

Emotion ayrıca JSX öğelerini şekillendirmek için kullanılabilecek isteğe bağlı bildirimler eklememize de olanak tanır. Aşağıdaki örnekte, başlığın büyük harfle yazılmasını sağlamak için bir `text-transform` özelliği uyguluyoruz:

```
// src/pages/index.js
import React from "react"
import styled from "@emotion/styled"
import { css } from "@emotion/react"

const Header = styled.div`
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
  background-color: blue;
  color: white;
`

const uppercase = css`
  text-transform: uppercase;
`
export default function Home({ children }) {
  return (
    <Header>
      <div css={uppercase}>Welcome to my Gatsby site!</div>
    </Header>
  )
}
```

Emotion ve diğer JS'de CSS tekniklerinin en önemli faydalarından biri, CSS'nin doğrudan JavaScript'teki bileşeninizin tanımına yerleştirilmesidir. Ancak Emotion'ı bağımlılık olarak ekleyerek ve layout bileşeninde kullanarak sitenize global stil uygulamak için de kullanabilirsiniz. Emotion'ı global stil için layout bileşeniyle birlikte kullanmak, ayrı bir layout.css dosyasına olan ihtiyacı ortadan kaldıracaktır.

**Styled Components**

Styled Components, Gatsby geliştiricilerinin tıpkı Emotion gibi bileşenlerin içine gerçek CSS yazmasına olanak tanıyan başka bir JS’de CSS kütüphanesidir. Emotion gibi Styled Components de production CSS'iniz de ad çakışmaları ve ölü kodların ortaya çıkma olasılığını ortadan kaldırır.

Styled Components'ın nasıl benzer bir yaklaşım sunduğunu görmek için Emotion ile uyguladığımız örneğe bir göz atalım. Styled Components'ı kullanmak için plugin yüklememiz gerekiyor; bunu başka bir "Hello World" starter ayarladıktan sonra yapacağız.

```
$ gatsby new gtdg-ch2-css-in-js-styled-components \
gatsbyjs/gatsby-starter-hello-world
```

Dizini değiştirelim ve NPM kullanarak Styled Components için gereken bağımlılıkları ekleyelim:

```
$ cd gtdg-ch2-css-in-js-styled-components
$ npm install gatsby-plugin-styled-components styled-components \
babel-plugin-styled-components
```

Daha sonra *gatsby-config.js* dosyasını açıp plugin’i buradaki plugin listesine ekleyeceğiz. Yüklü başka plugin’iniz yoksa aşağıdaki gibi görünmesi gerekir:

```
// gatsby-config.js
module.exports = {
  plugins: [`gatsby-plugin-styled-components`],
}
```

`index.js` dosyamız Emotion örneğine benzeyecek ve aynı sonucu üretecektir:

```
// src/pages/index.js
import React from "react"
import styled from "styled-components"

const Header = styled.div`
  margin: 0 auto;
  max-width: 960px;
  padding: 0 1.5rem;
  background-color: blue;
  color: white;
`
export default function Home({ children }) {
  return (
    <Header>
      <div>Welcome to my Gatsby site!</div>
    </Header>
  )
}
```

Tıpkı Emotion'da olduğu gibi, layout bileşenine global stil uygulamak mümkündür ancak Styled Components biraz farklı bir yaklaşım gerektirir. Styled Components, global kapsamlı stilleri tanımlayan `createGlobalStyle` metodunu sunar. Bu metot, Gatsby'nin layout bileşeni içinde, söz konusu layout bileşenini kullanan tüm sayfalarda paylaşıldığından emin olmak için çağrılmalıdır.

Gatsby dökümantasyonun dan uyarlanan, `GlobalStyle` nesnesini kullanan bir layout bileşeni örneğini burada bulabilirsiniz. Burada layout bileşeninin durumuna göre iki farklı arka plan rengi arasında geçiş yaptığımızı unutmayın:

```
// src/components/layout.js
import React from "react"
import { createGlobalStyle } from "styled-components"

const GlobalStyle = createGlobalStyle`
  body {
    background-color: ${props => (props.theme === "blue" ? "blue" : "white")};
  }
`
export default function Layout({ children }) {
  return (
    <React.Fragment>
      <GlobalStyle theme="blue" />
    </React.Fragment>)
}
```

Gatsby uygulamalarında hem Emotion hem de Styled Components yaygın olarak kullanılır. Ancak global stil ve geleneksel CSS'i kullanmanın da hiçbir sakıncası yoktur ve Gatsby, sitenizi oluştururken hangi CSS paradigmasını kullanmayı tercih edeceğiniz konusunda hiçbir varsayımda bulunmaz!

İlk iki Gatsby plugin’imizi yükledik. Bir sonraki adımımız, Gatsby'nin plugin ekosistemini ve Gatsby framework’ünün temel işlevselliğinin topluluk plugin’leri ile nasıl genişletileceğini keşfetmektir.

## Gatsby'yi Pluginler ile Genişletmek

Plugin’ler, Gatsby API'lerini (geliştiricilerin kendi özel kodlarını uygulamak için bağlayabilecekleri Gatsby işlevselliği) uygulayan ek işlevler sunan Node.js paketleridir. Plugin’ler özellikle kullanışlıdır çünkü işlevselliği modülerleştirmenize yardımcı olabilirler. Gatsby plugin’leri, SEO, responsive image desteği, CSS için Sass ve LESS desteği, çevrimdışı destek, site haritaları ve RSS beslemeleri, TypeScript ve CoffeeScript, Google Analytics ve daha fazlasını içeren çeşitli kullanım örneklerini çalıştırır.

Emotion ve Styled Components plugin’lerini yüklediğimizde gördüğünüz gibi, yeni bir plugin yüklemenin yolu, bağımlılığı package.json dosyanıza eklemek için NPM kullanmaktır. Ancak, Gatsby'nin de bundan haberdar olması için plugin’i açıkça *gatsby-config.js*'ye eklemeniz gerekir.

Gatsby ekosisteminde birkaç geniş plugin kategorisi bulunmaktadır:

**Source Pluginleri**

Bu plugin’ler, ister CMS, ister e-ticaret sistemi, local dosya sistemindeki statik dosyalar, REST API veya GraphQL API gibi bir web servisi olsun, harici veri veya içeriğin isteğe bağlı bir kaynaktan getirilmesinden sorumludur. Source plugin’leri, Gatsby'nin dahili GraphQL API'si aracılığıyla harici verileri tüketilebilir verilere dönüştürür.

**Dönüştürücü Pluginler**

Dönüştürücü plugin’ler, verileri bir formattan diğerine, özellikle de JSON'a dönüştürmekten sorumludur. Örneğin, bazı harici veriler Markdown, YAML, CSV veya XML biçiminde gelir; bunların tümü JavaScript'te kullanılmaya uygun olmayan serileştirme biçimleridir. Dönüştürücü plugin’ler her bir formatı Gatsby için JSON'a dönüştürür.

**Harici Servis Pluginleri**

Genel anlamda, third-party işlevselliği yerleştiren veya ekleyen herhangi bir plugin, bilgileri veya kodu doğrudan Google Analytics, Segment veya Algolia gibi bir üçüncü taraftan alan harici bir servis pluginidir. Bu plugin’ler çok yaygın olmasına rağmen, source plugin’lerinden veya dönüştürücü plugin’lerden boyut olarak çok daha küçük olma eğilimindedirler.

Ayrıca net bir kategoriye uymayan birçok eklenti de var. Nelerin mevcut olduğu hakkında fikir edinmek için Gatsby Plugin Kütüphanesi’ne göz atın.

Plugin’lerin gerekli olmadığı yerlere dikkat etmek de önemlidir. Plugin’ler, Gatsby sitelerine ek işlevler kazandırsa da, tüm ek işlevlerin pluginler aracılığıyla eklenmesi gerekmez. Örneğin, lodash veya axios gibi yardımcı programları sağlayan veya görselleştirme için d3 gibi bir bileşenin işleyişi için gerekli olan kütüphaneleri entegre eden JavaScript paketleri kullanmak istiyorsanız, genellikle paketi doğrudan bir bağımlılık olarak içe aktarmak en iyisidir. Plugin olarak değil package.json'da. Bu, kütüphaneyi normal şekilde bir NPM paketi olarak kuracağınız ancak *gatsby-config.js*'deki herhangi bir şeyi yeni kütüphanenin varlığını yansıtacak şekilde düzenlemekten kaçınacağınız anlamına gelir.

Genel bir kural olarak, bazı işlevlerin plugin mi yoksa ayrı bir kütüphane mi olması gerektiğine karar vermenize yardımcı olmak için, işlevsellik hiçbir şekilde Gatsby'nin API'lerine bağlanmıyor veya bunları uygulamıyorsa, yalnızca bağımlılık olarak içe aktarmak genellikle güvenlidir.

### Gatsby Pluginlerini Yükleme

Emotion ve Styled Components incelememizden plugin kurulum sürecinin nasıl çalıştığını hatırlayın. Burada tipik bir plugin’i tekrar ele alalım. İşleri basitleştirmek için, Google Analytics kodunu sitemize yerleştirmemize olanak tanıyan gatsby-plugin-google-analytics'i kullanacağız.

Gatsby sitenizin kök dizininden ve istediğiniz zaman bir plugin yükleyebilirsiniz. Plugin yüklemek için, plugin adını seçtiğiniz bir adla değiştirerek bu komutu çalıştırın:

```
$ npm install gatsby-plugin-google-analytics
```

*gatsby-config.js* dosyanızda plugin’inizin, *plugins* dizisinde temsil edildiğinden emin olun. İlk kez plugin ekliyorsanız *plugins* dizisini kendiniz eklemeniz gerekebilir. Yapılandırmanızda zaten plugin’ler varsa, onu mevcut diziye ekleyebilirsiniz:

```
// gatsby-config.js
module.exports = {
  plugins: [`gatsby-plugin-google-analytics`],
}
```

Sitenize başka bir plugin eklemeniz gerektiğinde her seferinde bu işlemi uygulayacaksınız.

Bazı plugin’ler, *gatsby-config.js*'deki seçenekleri de kabul eder. Bu genellikle plugin’in nasıl davranması gerektiğini veya harici bir servis için gerekli kimlik bilgilerini belirten temel parametreleri yapılandırmak içindir. Örneğin, gatsby-plugin-google-analytics için tipik bir `options` nesnesi şuna benzer:

```
// gatsby-config.js
module.exports = {
  plugins: [
    {
      resolve: `gatsby-plugin-google-analytics`,
      options: {
        // The property ID; the tracking code won't be generated without it
        trackingId: "YOUR_GOOGLE_ANALYTICS_TRACKING_ID",
        // Defines where to place the tracking script - `true` in the head
        // and `false` in the body
        head: false,
        // Setting this parameter is optional
        anonymize: true,
        // Setting this parameter is also optional
        respectDNT: true,
        // Avoids sending pageview hits from custom paths
        exclude: ["/preview/**", "/do-not-track/me/too/"],
        // Delays sending pageview hits on route update (in milliseconds)
        pageTransitionDelay: 0,
        // Enables Google Optimize using your container Id
        optimizeId: "YOUR_GOOGLE_OPTIMIZE_TRACKING_ID",
        // Enables Google Optimize Experiment ID
        experimentId: "YOUR_GOOGLE_EXPERIMENT_ID",
                // Set Variation ID. 0 for original 1,2,3....
        variationId: "YOUR_GOOGLE_OPTIMIZE_VARIATION_ID",
        // Defers execution of google analytics script after page load
        defer: false,
        // Any additional optional fields
        sampleRate: 5,
        siteSpeedSampleRate: 10,
        cookieDomain: "example.com",
      },
    },
  ],
}
```

Plugin yapısına bağlı olarak seçenekler gerektirebilir veya gerektirmeyebilir.

### Local Plugin Yükleme

Dosya sisteminden veri elde ettiğiniz gibi plugin’leri de local dosya sisteminizden yüklemek mümkündür. Gatsby, plugin’leri, *src* dizininin yanında *plugins* dizinine yerleştirdiğiniz sürece, kod tabanınızdan yükleyebilir. Local plugin’ler, Gatsby kod tabanına özel kod eklemeniz gerekebileceği veya kendi özel plugin’lerinizi yazmak istediğiniz durumlar için değerlidir. Gatsby dökümantasyonu, gatsby-local-plugin adı verilen local plugin’den yararlanacak bir proje yapısına örnek sağlar:

```
/my-gatsby-site
└── /src
  └── /pages
  └── /components
└── /plugins
  └── /gatsby-local-plugin
    └── /package.json
    └── /gatsby-node.js
└── gatsby-config.js
└── gatsby-node.js
└── package.json
```

Local plugin’ler ile NPM kurulum komutuna ihtiyacınız yoktur, ancak plugin’i *gatsby-config.js* dosyasında yapılandırmanız gerekir, böylece Gatsby bundan haberdar olur:

```
module.exports = {
  plugins: [`gatsby-local-plugin`]
}
```

Plugin’in sitenize doğru şekilde yüklendiğini doğrulamak için *plugins* dizinine bir *gatsbynode.js* dosyası ekleyebilir (bu dosyanın ne işe yaradığını ilerleyen bölümlerde ele alacağız) ve `gatsby development` veya `gatsby build`'i çalıştırdığınızda yazdırılan terminal çıktısına bir satır ekleyin.

Bu satır, Gatsby'nin `onPreInit` gibi build hook’larından birini kullanmalıdır.

```
// plugins/gatsby-local-plugin/gatsby-node.js
exports.onPreInit = () => {
  console.log("Hey, I'm a local plugin that just got loaded! Look at me!")
}
```

Terminal çıktınızda, `gatsby development` veya `gatsby build`'i bir sonraki çalıştırışınızda aşağıdakileri göreceksiniz:

```
success open and validate gatsby-configs - 0.053s
success load plugins - 1.036s
Hey, I'm a local plugin that just got loaded! Look at me!
success onPreInit - 0.025s
...
```

**Kaynak:** “Gatsby: The Definitive Guide”, Preston So