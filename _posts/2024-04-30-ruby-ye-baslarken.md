---
layout: post
title: Ruby'ye Başlarken
---

Bu kitapta Ruby dili hakkında konuşarak çok zaman harcayacağız. Bunu yapmadan önce Ruby'yi bilgisayarınıza kurup çalıştırabildiğinizden emin olmak istiyoruz. Bu şekilde örnek kodları deneyebilir ve okurken kendi başınıza denemeler yapabilirsiniz. Ruby'yi öğrenmek istiyorsanız, okurken kod yazma alışkanlığını da edinmelisiniz.

## Ruby'yi Yüklemek

İşletim sisteminizde Ruby'nin zaten kurulu olması ihtimali yüksektir. Komut istemine `ruby -- version` yazmayı deneyin; hoş bir sürpriz yaşayabilirsiniz. Ancak büyük olasılıkla Ruby sürümünün güncel olmadığını göreceksiniz. Örneğin, bu yazının yazıldığı sırada macOS Ruby 2.6.10 ile birlikte gelir.

Bu kitaptaki örnekler Ruby 3.2'de yazılmıştır. Kodun çoğu Ruby'nin eski sürümlerinde çalışsa da, performans ve güvenlik nedeniyle en güncel sürümü kullanmaya çalışmalısınız.

Ruby'yi çeşitli farklı şekillerde kurabilirsiniz, böylece genel kurulum talimatlarını vermek, biraz kendi maceranı seç hikayesine dönüşür. Bu kitaptaki örneklerin çoğu, Linux tarzı komutlara yanıt veren Linux veya Unix tarzı sistem kullandığınızı varsaymaktadır. Buna tüm Linux dağıtımları, macOS, Windows Subsystem for Linux (WSL) çalıştıran Windows sistemleri ve çoğu Docker container’ının yanı sıra Replit gibi bulut tabanlı geliştirme ortamları dahildir.

Bununla birlikte, Ruby Windows'ta çalışır. Windows'ta Ruby kurulumunu yönetme süreci farklıdır ve bunu bu bölümün ilerleyen kısımlarında tüm ayrıntılarıyla ele alacağız.

Lütfen Ruby kurulumuyla ilgili araçların sık sık değiştiğini ve bazı özel talimatların güncelliğini yitirmiş olabileceğini veya daha yeni araçlarla değiştirilebileceğini unutmayın.

Herhangi bir sebepten dolayı bilgisayarınıza herhangi bir şey yüklemek istemiyorsanız Replit veya GitHub Codespaces gibi bulut tabanlı geliştirme ortamlarından yararlanabilirsiniz. Bu ortamlar, kodunuzu bir tarayıcıda yazmanıza ve bulut tabanlı bir sanal makinede çalıştırmanıza olanak tanır.

## Ruby'yi rbenv Version Manager ile Yüklemek

Ruby kurulumumuzu kolaylaştırmak için, aynı makineye birden fazla Ruby sürümü kurmanıza ve bunlar arasında geçiş yapmanıza olanak tanıyan bir araç olan sürüm yöneticisi kullanacağız. Ruby kurulumunuzu gerçekleştirmek için sürüm yöneticisi kullanmanın birçok nedeni vardır. Ruby'nin birden çok sürümü arasında kolayca geçiş yapabilmek, size farklı zamanlarda yazılmış olabilecek birden çok projeyle çalışma esnekliği sağlar. Ayrıca sürüm yöneticileri kolay kurulum için oluşturulmuştur, bu nedenle birden fazla Ruby sürümünü bir sürüm yöneticisiyle kurmak, tek bir sürümü tek başına kurmaktan daha kolaydır. Ruby'nin yalnızca bir sürümünü indirmekle ilgileniyorsanız, sistem bazında talimatları [https://www.ruby-lang.org/en/documentation/installation](https://www.ruby-lang.org/en/documentation/installation) adresinde bulabilirsiniz.

Bu kitapta kullanacağımız aracın adı rbenv'dir. Rbenv tek Ruby sürüm yöneticisi olmasa da muhtemelen bugünlerde en sık kullanılanıdır. Yaygın olarak kullanılan diğer sürüm yöneticileri RVM ve chruby'dir. Birden fazla dil için sürüm yönetimi kullanıyorsanız, farklı dillerin sürüm yöneticilerini birleştiren asdf adlı bir projeye bakmak isteyebilirsiniz ve Ruby'de hızla daha popüler hale geliyor.

Rbenv'i, rbenv-installer programı aracılığıyla kuracağız. Başka birinin shell script’ini çalıştırmak sizi tedirgin ediyorsa, script’i çalıştırmadan önce [https://github.com/rbenv/rbenv-installer/blob/main/bin/rbenv-installer](https://github.com/rbenv/rbenv-installer/blob/main/bin/rbenv-installer) adresinden inceleyebilirsiniz.

Bir terminalden bu komutun tamamını tek bir satıra girin.

```
$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash
```

Curl, URL'lere erişmek ve dönüş değeriyle faydalı bir şeyler yapmak için kullanılan komut satırı aracıdır - bu durumda, rbenv GitHub reposundan shell script’ini alır ve bunu yürütecek olan bash kabuğuna iletir.

Bu script dosyası, sisteminiz için uygun paket yöneticisini kullanarak rbenv'i kuracak ve ayrıca farklı Ruby sürümlerinin indirilmesini ve kurulumunu yönetecek ruby-build adlı bir helper programı da kuracaktır.

Kurulum komutu çok fazla çıktı üretebilir; özellikle de Homebrew paket yöneticisini kullanan bir macOS sistemindeyseniz. Mac'te aşağıdaki şekilde bitmelidir; WSL altındaki bir Windows kullanıcısı farklı bir şey görebilir:

```
All done! Note that this installer does NOT edit your shell configuration files: 1. Run `rbenv init' to view instructions on how to configure rbenv for your shell. 2. Launch a new terminal window after editing shell configuration files.
```

Talimatları takip ederek `rbenv init`'i çalıştırın. Bu, zshell çalıştıran bir Mac'teki çıktıdır (talimatlarınız farklı olabilir):

```
$ rbenv init
# Load rbenv automatically by appending 
# the following to ~/.zshrc:
eval "$(rbenv init - zsh)"
```

Kurulumunuz ne olursa olsun, bu talimatta almanız gerekenler:

- Güncellemeniz gereken kabuk yapılandırmasını içeren dosya
- Dosyanın sonuna koymanız gereken metin

Önerilen metin satırını yapılandırma dosyanızın sonuna koymanız ve yeni bir terminal penceresi açmanız gerekir; değişiklik yalnızca bir pencere yüklendiğinde etkili olur, dolayısıyla rbenv'i başlatmanın en kolay yolu yeni bir terminal penceresi açmaktır.

Şimdi belirli bir Ruby sürümünü yükleyelim.

**Ruby'leri rbenv ile kurmak**

Rbenv, `rbenv install -l` komutuyla yüklemek isteyeceğiniz Ruby sürümlerinin bir listesini görmenizi sağlar. İşte bugün itibariyle liste:

```
$ rbenv install -l 
2.7.7
3.0.5
3.1.3
3.2.0
jruby-9.4.0.0
mruby-3.1.0
picoruby-3.0.0
rbx-5.0
truffleruby-22.3.0
truffleruby+graalvm-22.3.0
Only latest stable releases for each Ruby implementation are shown.
Use 'rbenv install --list-all / -L' to show all local versions.
```

Bu liste, çeşitli Ruby uygulamalarının en güncel yama sürümüdür. Ana Ruby uygulama sürümü 2.7, 3.0, 3.1 ve 3.2 için Ruby sürümlerini görebilirsiniz. (Farklı Ruby uygulamalarından bahsederken, ana versiyona bazen CRuby, bazen de “Matz'ın Ruby Yorumlayıcısı” için MRI denir.) Burada fazla bahsetmeyeceğimiz başka versiyonlar da var. JRuby, Java Sanal Makinesi üzerinde çalışan bir Ruby sürümüdür; mruby ise gömülü donanım üzerinde çalışmaya yönelik özel sınırlı bir Ruby yapısıdır. Rbx, Ruby'yi Ruby'de uygulamaya yönelik artık kullanılmayan bir proje olan Rubinius'tur ve TruffleRuby, yüksek performansa odaklanan dilin bir uygulamasıdır.

Şu an ilgi alanımız rbenv install 3.2.0 komutuyla kurabileceğimiz Ruby 3.2.0. Listede Ruby'nin en güncel sürümünü göremiyorsanız ve rbenv'i daha önce yüklediyseniz, listenizde daha yeni Ruby sürümleri almak için ruby-build'i nasıl güncelleyeceğinize ilişkin talimatlar alabilirsiniz. Rbenv komutlarının hiçbirinin superuser erişimine sahip olmamızı veya sudo kullanmamızı gerektirmediğini unutmayın. Rbenv de dahil olmak üzere Ruby sürüm yöneticilerinin keyifleri yanlarından biri, her şeyi ana dizininizin içinde yapmalarıdır, yeni Ruby sürümlerini yüklemek veya kullanmak için herhangi bir özel sistem ayrıcalığına ihtiyacınız yoktur.

```
$ rbenv install 3.2.0
To follow progress, use
'tail -f <REDACTED>'
or pass --verbose
Installing openssl-3.0.7...
Installed openssl-3.0.7 to /Users/noel/.rbenv/versions/3.2.0
Installing ruby-3.2.0...
ruby-build: using readline from homebrew
ruby-build: using gmp from homebrew
Installed ruby-3.2.0 to /Users/noel/.rbenv/versions/3.2.0
```

Çıktınız, tam sürüm numarasına ve Ruby sürümünü yeniden kurup kurmadığınıza bağlı olarak biraz farklı olabilir.

Örneğin Ruby sürümünün rbenv sürümleriyle birlikte kurulduğunu doğrulayabiliriz.

```
$ rbenv versions
* system
3.2.0
```

Buradaki system, eğer böyle bir şey varsa işletim sistemi için önceden tanımlanmış Ruby'dir ve yıldız işareti o anda hangi sürümün aktif olduğunu gösterir.

Önemli bir adım: rbenv aracılığıyla yeni bir Ruby kurduktan sonra `rbenv rehash` komutunu çalıştırmalısınız. Bu komut hiçbir çıktı üretmez ancak rbenv'in yeni yüklenen Ruby'yi kullanmasını sağlar.

Şu anda Ruby system hala aktif. Bunu değiştirelim.

**Ruby'leri rbenv ile değiştirmek**

İşte bu noktada getirisini görmeye başlıyoruz. Farklı Ruby sürümleri yüklendikten ve `rbenv rehash` çağrıldıktan sonra, Rbenv bize, kullandığımız Ruby sürümünü değiştirmemiz için birden fazla yol sağlar.

`rbenv local <version>` komutu, bulunduğunuz dizinin Ruby sürümünü değiştirir:

```
$ ruby --version
ruby 2.6.10p210 (2022-04-12 revision 67958) [universal.arm64e-darwin22]
$ rbenv local 3.2.0
$ ruby --version
ruby 3.2.0 (2022-12-25 revision a528908271) [arm64-darwin22]
```

Dizinin bu ayarı, dizinden çıkıp geri dönseniz bile korunur. (Değişikliğin geçerli oturumun ötesinde devam etmesini istemiyorsanız, `rbenv local` yerine `rbenv shell <version>` kullanabilirsiniz).

```
$ cd ..
$ cd test
$ ruby --version
ruby 3.2.0 (2022-12-25 revision a528908271) [arm64-darwin22]
```

Rbenv bunu dizine .ruby-version adlı bir dosya koyarak başarır, bu sadece o dizin için ayarladığınız Ruby'nin sürüm numarasını içerir.

```
% cat .ruby-version
3.2.0
```

Bu dosya aynı zamanda tersten de çalışır; eğer rbenv'i yüklediyseniz ve `.ruby-version` dosyasını içeren bir dizine geçerseniz, rbenv kuruluysa otomatik olarak o Ruby sürümüne geçecektir veya dizinin kaldırılmış bir Ruby beklediği konusunda sizi uyarabilir. Pek çok Ruby projesi, Ruby sürümünü belirtmek için `.ruby-version` dosyası kullanır ve bu dosya, tüm Ruby sürüm yöneticileri tarafından dikkate alınır.

Kendileri belirtmeyen dizinler için varsayılan bir Ruby sürümü ayarlamak istiyorsanız bunu `rbenv global <version>` ile yapabilirsiniz.

Bu, Ruby'yi kurmayı beklediğinizden daha fazla iş gerektirebilir. Eğer yapmak istediğiniz Ruby'nin tek bir versiyonunu kullanmak olsaydı, buna katılırdık. Ancak burada gerçekten yaptığınız şey, kendinize inanılmaz miktarda esneklik kazandırmaktır. Belki gelecekte `.ruby-version` dosyasına göre Ruby 2.7.5 kullanan bir proje ortaya çıkabilir. Bu sorun değil; yalnızca `rbenv install 2.7.5`'i kullanın; `rbenv rehash` ve rbenv, sürümü `.ruby-version` dosyasından otomatik olarak alacaktır.

**rbenv aslında ne yapıyor?**

Rbenv, dinamik davranışını normal terminal ortamınızda mümkün olduğunca az değişiklik yaparak sağlamaya çalışır.

Unix terminali, bir komut yazdığınızda yürütülebilir programlar için hangi dizinlere bakacağını belirlemek amacıyla PATH adı verilen genel bir ortam değişkenini kullanır. Terminalinizin yapılandırma dosyasına bakarsanız, muhtemelen PATH değişkeninin değiştirildiğini göreceksiniz.

Terminal kurulumunuzun bir parçası olarak rbenv init komutu yürütüldüğünde, PATH'inizin önüne bir dizin ekler, böylece işletim sisteminiz başka bir yere bakmadan önce rbenv dizinine bakar. Bu dizinde rbenv'in shim olarak adlandırdığı bir dizi şey vardır; Tüm Ruby sürümlerinizdeki tüm yürütülebilir komutlarla eşleşen küçük programlar (Yeni bir Ruby kurduktan sonra `rbenv rehash`'ı çalıştırmanızın nedeni bu dizini yenilemektir). Ruby veya (birazdan göreceğiniz gibi) irb gibi bir Ruby komutunu çağırdığınızda, ilk önce rbenv shim ile karşılaşılır ve genellikle bir .ruby-version dosyasının varlığına bağlı olarak hangi Ruby'nin aktif olduğunu dinamik olarak seçer. Daha sonra komut, o geçerli sürümdeki gerçek yürütülebilir programa aktarılır. Bu gerçek sürümleri görebilirsiniz, ~/.rbenv/versions adresindeki ana dizininizde bulunurlar.

## Ruby'yi Çalıştırmak

Artık Ruby kurulduğuna göre muhtemelen bazı programları çalıştırmak isteyeceksiniz. Derlenmiş dillerden farklı olarak Ruby'yi çalıştırmanın iki yolu vardır; kodu etkileşimli olarak yazabilir veya program dosyaları oluşturup çalıştırabilirsiniz. Kodu etkileşimli olarak yazmak, dili denemenin harika bir yoludur, ancak daha karmaşık kodlar veya birden fazla çalıştırmak isteyeceğiniz kodlar için program dosyaları oluşturmanız ve bunları çalıştırmanız gerekir. Ancak daha ileri gitmeden önce Ruby'nin kurulu olup olmadığını test edelim. Yeni bir komut istemi açın ve şunu yazın:

```
$ ruby --version
ruby 3.2.0 (2022-12-25 revision a528908271) [arm64-darwin22]
```

Teknik olarak, yalnızca kabuk komut istemine ruby yazarak Ruby'yi etkileşimli olarak çalıştırabilirsiniz. Yanıt olarak boş bir satır alacaksınız ve oraya Ruby kodunu yazabilirsiniz.

```
$ ruby
puts "Hello, world!" 
^D
Hello, world!
```

Bu örnekte tek bir Ruby satırı yazdık. Bu satır iki bölümden oluşuyor. İlk bölüm, bir metodun adıdır. Metot önceden tanımlanmış bir kod yığınıdır. Bu durumda `puts` metodu Ruby tarafından bizim için tanımlanan çeşitli metotlardan biridir. İkinci bölüm olan "Hello, world!", string (dize) adı verilen çift tırnakla çevrelenmiş metindir. İkisini birleştiren Ruby kodu, "Hello, world!" parametresiyle `puts` metodunu çağırır. `puts` metodu daha sonra bu parametreyi terminale geri gönderir; puts, “outPUT String”in kısaltmasıdır.

Bir sonraki satıra dosya sonu karakterini (sistemimizde Ctrl+D) yazarak programdan çıktık ve yazdıklarımızın değerlendirilmesine neden olduk. Ruby'yi bu şekilde kullanmak işe yarar, ancak yanıtları yalnızca açıkça yazdırırsanız gösterir. Ayrıca, bir yazım hatası yaparsanız can sıkıcı olabilir ve yazarken neler olduğunu göremezsiniz.

Neyse ki Ruby ile etkileşim kurmanın daha iyi bir yolu var.

Etkileşimli Ruby veya irb, Ruby'yi etkileşimli olarak çalıştırmak için tercih edilen araçtır. Irb, komut satırı geçmişi, satır düzenleme yetenekleri ve iş kontrolü ile eksiksiz bir Ruby kabuğudur. irb'yi komut satırından çalıştırabilirsiniz. Başladıktan sonra Ruby kodunu yazın. Her ifadeyi değerlendirirken size değerini gösterecektir. Exit yazarak veya Ctrl+D ile irb oturumundan çıkın.

İşte örnek bir oturum:

```
$ irb
irb(main):001:1* def sum(n1, n2)
irb(main):002:1* n1 + n2
irb(main):003:0> end
=> :sum
irb(main):004:0> sum(3, 4)
=> 7
irb(main):005:0> sum("cat", "dog") => "catdog"
irb(main):006:0> exit
```

Bu oturumun ilk üç satırında sum adında bir metot tanımlıyoruz. Bu metodu tanımlama eylemi, metodun adıyla eşleşen bir Ruby sembolü olan :sum değerini döndürür. Semboller ve metot adları hakkında daha sonra daha fazla konuşacağız. 4. satırda, önce 3 ve 4 parametreleriyle, ardından 5. satırda "cat" ve "dog" parametreleriyle metodu çağırıyoruz. Ruby'de dizeleri eklemek onları birleştirir, böylece "catdog" dizesi döndürülür. Daha sonra 6. satırda çıkıyoruz.

Bunu Ruby 3.1 veya üzeri sürümlerde denerseniz, irb'nin değişken adlarını, komutları ve renk kodlarını otomatik olarak tamamlamaya çalıştığını fark edeceksiniz; bunların hiçbirinin bir kitapta gösterilmesi kolay değildir.

irb'ye aşina olmanızı öneririz; Ruby kavramlarını keşfetmenin ve kodunuzda hata ayıklamanın harika bir yoludur ve Ruby deneyiminizi daha eğlenceli hale getirecektir.

**Docker'a ne dersiniz?**

Ruby'yi daha büyük bir projede veya daha büyük bir ekiple kullanıyorsanız Docker'ın geliştirme ortamınızın bir parçası olma ihtimali yüksektir. Docker, container tanımlamanıza ve çalıştırmanıza olanak tanıyan bir araçtır. Container, kod çalıştırmak için gereken tüm bağımlılıkları paketlemenin bir yoludur; etkili bir şekilde bilgisayarınızın içindeki sanal bir işletim sistemidir. Docker'ı kullanarak, hangi işletim sistemini çalıştırıyor olursanız olun bir Linux ortamını simüle edebilirsiniz.

Docker'ın tam bir açıklaması bu kitabın kapsamı dışındadır, ancak genel olarak Docker'a zaten aşina iseniz, Docker'ın önceden yüklenmiş farklı Ruby sürümleriyle image’leri tuttuğunu ve Ruby ile her zaman en son yayımlanan sürüme ulaşabileceğinizi belirtmekte fayda var. `ruby:latest` ile en son yayımlanan sürüme ulaşabilirsiniz ve `docker run -it ruby irb` ile doğrudan dockerize edilmiş irb komut istemine gidebilirsiniz. Harici Ruby dosyalarını Docker container’ında çalıştırmak da mümkündür ancak biraz daha Docker bilgisi gerektirir.

## Ruby Programları Oluşturmak

Ruby programları yazmanın en yaygın yolu Ruby kodunu bir veya daha fazla metin dosyasına yerleştirmektir. Bu dosyaları oluşturmak için bir metin düzenleyici veya Entegre Geliştirme Ortamı (IDE) kullanacaksınız; Ruby desteğine sahip Visual Studio Code, Vim, Sublime Text ve RubyMine gibi birçok popüler editör vardır. Daha sonra dosyaları editörün içinden veya komut satırından çalıştıracaksınız. Her iki teknik de faydalıdır; tek dosyalı programları editörün içinden, daha karmaşık programları ise komut satırından çalıştırabilirsiniz.

Basit bir Ruby programı oluşturup çalıştıralım. Terminal penceresini açın ve bir yerde boş bir dizin oluşturun, belki buna pickaxe diyebilirsiniz.

Ardından, seçtiğiniz editörü kullanarak aşağıdaki metni içeren *myprog.rb* dosyasını oluşturun.

```
puts "Hello, Ruby Programmer"
puts "It is now #{Time.now}"
```

İkinci dizenin parantezlerin değil süslü parantezlerin arasında Time.now metnini içerdiğini unutmayın.

Bir Ruby programını, Python gibi başka bir script dilindeki herhangi bir shell script’i gibi dosyadan çalıştırabilirsiniz. Basitçe Ruby yorumlayıcısını çalıştırın ve ona parametre olarak script dosyası adını verin:

```
$ ruby myprog.rb
Hello, Ruby Programmer
It is now 2022-12-25 12:29:56 -0600
```

Unix sistemlerinde program dosyasının ilk satırı olarak “shebang” gösterimini kullanabilirsiniz. Sisteminiz destekliyorsa, `#!/usr/bin/env ruby` komutunu kullanarak Ruby yolunu sabit kodlamaktan kaçınabilirsiniz; bu, yolunuzu Ruby için arayacak ve daha sonra çalıştıracaktır.

```
#!/usr/bin/env ruby
puts "Hello, Ruby Programmer"
puts "It is now #{Time.now}"
```

Bu kaynak dosyayı yürütülebilir hale getirirseniz (örneğin, `chmod +x myprog.rb` kullanarak), Unix dosyayı bir program olarak çalıştırmanıza olanak tanır:

```
$ ./myprog.rb
Hello, Ruby Programmer
It is now 2022-12-25 12:29:56 -0600
```

Dosya ilişkilendirmelerini kullanarak Microsoft Windows altında benzer bir şey yapabilirsiniz ve Ruby GUI uygulamalarını Windows Gezgini'nde adlarına çift tıklayarak çalıştırabilirsiniz.

## Ruby Hakkında Daha Fazla Bilgi Almak

Ruby kütüphanelerinin hacmi büyüdükçe hepsini tek bir kitapta belgelemek imkansız hale geldi; Ruby ile birlikte gelen standart kütüphane artık 9.000'den fazla metot içeriyor.

Resmi Ruby belgeleri [https://docs.ruby-lang.org](https://docs.ruby-lang.org) adresindedir. Ruby 3.2'de, siz yazdıkça irb size standart metot adlarının dokümantasyonunu verecektir.

Bu belgelerin çoğu, RubyDoc adlı bir araç kullanılarak kaynak kodundaki yorumlardan oluşturulmuştur. Buna “Ruby'yi Belgelemek” bölümünde bakacağız. [https://www.rubydoc.info](https://www.rubydoc.info) adresindeki RubyDoc sitesi, RubyDoc kullanan Ruby projelerine yönelik dökümantasyonları içerir.

Ruby dünyasındaki third-party kütüphanelere gem denir ve Ruby Gems resmi listesi [https://rubygems.org](https://rubygems.org) adresindedir. “Ruby Gems” bölümünde gemler hakkında daha fazla konuşacağız.

Ayrıca Ruby core dokümantasyonu için ri adı verilen bir komut satırı aracı da vardır. Bir sınıfın dokümantasyonunu bulmak için `ri <sınıf adı>` yazın. Örneğin String sınıfına ait özet bilgilerin başlangıcı aşağıdadır. Hiçbir parametre olmadan `ri` yazarsanız, sizden sınıf isteyen bir prompt alırsınız.

```
= String < Object
------------------------------------------------------------------------
= Includes:
Comparable (from ruby core)
(from ruby core)
------------------------------------------------------------------------
A String object has an arbitrary sequence of bytes, typically
representing text or binary data. A String object may be created using
String::new or as literals.
String objects differ from Symbol objects in that Symbol objects are
designed to be used as identifiers, instead of text or data.
```

String'in tüm metotlarını listelemeye devam ediyor.

Ayrıca bir metot adını da deneyebilirsiniz:

```
$ ri strip
= .strip

(from ruby core)
=== Implementation from String
------------------------------------------------------------------------
  strip -> new_string

------------------------------------------------------------------------

Returns a copy of the receiver with leading and trailing whitespace
removed; see {Whitespace in
Strings}[rdoc-ref:String@Whitespace+in+Strings]:

  whitespace = "\x00\t\n\v\f\r "
  s = whitespace + 'abc' + whitespace
  s       # => "\u0000\t\n\v\f\r abc\u0000\t\n\v\f\r "
  s.strip # => "abc"

Related: String#lstrip, String#rstrip.
```

Daha sonra `q` yazarak listeden çıkabilirsiniz.

## Sıradaki

Artık Ruby'yi kullanmaya başladığınıza göre Ruby'nin nasıl çalıştığını ve dilin temel özelliklerini öğrenmenin zamanı geldi. Ancak öncelikle dilin öne çıkan noktalarına hızlı bir genel bakış yapacağız.

**Kaynak:** “Programming Ruby 3.2: The Pragmatic Programmers’ Guide”, Noel Rappin with Dave Thomas
