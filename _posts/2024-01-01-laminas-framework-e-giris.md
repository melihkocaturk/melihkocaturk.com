---
layout: post
title: Laminas Framework'e Giriş
---

Nisan 2019'da Linux Vakfı yeni bir proje başlattığını duyurdu: Laminas. Bu aslında 2006'dan beri var olan Zend Framework projesinin yeni adıydı. O andan itibaren, halihazırda 400 milyon yüklemeye sahip olan Zend Framework, bağımsız bir açık kaynak yazılım haline gelecekti. Ancak Zend Technologies, adını taşıyan bu üründen birkaç yıldır vazgeçmiyor ve kurumsal destek vermeye devam ediyor.

Bu bölüm, Zend Framework mimarisinin Laminas’a geçişine kadarki evrimini sunar. Framework ekibinin neden diğerleri yerine bazı kararları aldığını anlayacaksınız.

Bu bölümde, aşağıdaki konuları ele alacağız:

- Neden framework’lere ihtiyacımız var?
- Zend Framework’ten Laminas’a
- Laminas topluluğunun teknik ve sosyal altyapısı

## Neden framework’lere ihtiyacımız var?

Bazı insanlar neden framework’e ihtiyacım var diye sorabilir. Bu tür insanlar neden sınıfları kullanmam gerekiyor diye de sorabilir. Sonuçta PHP basittir ve sınıflara ihtiyacı yoktur. Birkaç satır kodla, veritabanından işlenmiş veriler içeren bir sayfa oluşturabilirsiniz. PHP’nin  yaratıcısı Rasmus Lerdorf, konuşmalarında her zaman PHP'nin hızlı olduğunu ve çalışması için ne sınıflara ne de framework’e ihtiyacı olmadığını iddia etti. 2014 yılında Mariano Iglesias, PHP Brezilya Konferansı’nda framework’süz geleceği savunduğu bir ders verdi. Bahsettiğimiz yazılımın karmaşıklığına bağlı olarak hem Rasmus hem de Mariano haklı. Basit bir kişisel web sayfasını kurumsal bir sistemle kıyaslayamayız. Nesne yönelimli programlama (OOP) ve frameworklerden bahsettiğimizde, esas olarak karmaşıklığın kontrolünden bahsediyoruz.

Martin Fowler, çoğu insanın mimarlık terimi için hemfikir olduğu anlamlardan birinin, değiştirilmesi zor olan bir dizi karar olduğunu belirtir. Yazılım projesi için bu kararlar, istenen özelliklerle ilgilidir. Bir yazılım için neyin arzu edildiğini düşündüğünüzde, yazılımın ilk kararlı sürümünün teslim edilmesinin, yalnızca bir kilometre taşı olduğunu unutmamak önemlidir.

### Sürekli entegrasyon ve sürekli teslimat

Yazılım büyüyen bir organizmadır. Frederick Brooks, The Mythical Man-Month, Addison-Wesley adlı kitabında yazılım için dört karmaşıklık düzeyi olduğunu belirtir:

- Algoritması yalnızca bir insan beyninde saklanabilen **program**; başka bir deyişle, onu yalnızca bir kişi anlayabilir. Çok sınırlı bir görevi olan çok özel bir programdır. Bu tür yazılımlar spesifik ihtiyaçlara hizmet eder ve kısa bir sohbette açıklanabilecek şeylerdir, ancak yalnızca yaratıcısı onu çalıştırabilir ve bakımını yapabilir.
- **Programlama ürünü:** Minimum dökümantasyon ile herkes tarafından çalıştırılabilen ve bakımı yapılabilen ve insanlar tarafından okunabilen bir program.
- **Programlama sistemi:** Daha karmaşık görevleri gerçekleştirmek için bir programın diğer programlarla birleştirildiği bir dizi program.
- **Programlama sistemleri ürünü:** programdan dokuz kat daha karmaşık ve pahalı olan yazılım. Bu yazılımın bakımı için büyük bir ekip gerekir. Tek bir kişinin kafasına sığmaz. Nasıl çalıştığını veya nasıl organize edildiğini açıklamak kolay değildir. Ancak Brooks'a göre, çoğu yazılım geliştirme çabasının amacı budur.

**Not:** Mythical Man-Month, yazılım mühendisliği için klasik bir metindir. Çevik yazılım geliştirmenin yükselişinden önce yazılmış olmasına rağmen, yazılım üretmenin karmaşıklığına ve görevleri paralelleştirmenin zorluğuna ilişkin analizi güncelliğini koruyor.

Sürekli değişmesi gereken karmaşık yazılımlar, bazı özelliklerin uzun süre korunmasını gerektirir. Dayanması ve bir hafta sonra çöpe atılmaması için yapılmış yazılımlardan bahsediyoruz. Bazı yazılımlar geçicidir, bu nedenle yapısı hakkında çok fazla endişelenmenize gerek yoktur. Otomatik kod oluşturma araçları, yakında atılacak olan kodu oluşturmak için harikadır. Ancak evrilen yazılımlar, yazılımın nasıl çalıştığına dair derin bir anlayış gerektirir. Aslında, uzun ömürlü olması için yapılan yazılımlar bazı temel özellikler gerektirir: minimum karmaşıklık, gevşek bağlantı, genişletilebilirlik ve yeniden kullanılabilirlik. Bu arzu edilen özelliklerin her biri, kritik bir özellikle ilişkilidir: bakım kolaylığı.

Karmaşık bir sistemin minimum karmaşıklığa sahip olması gerektiğini söylemek çelişkili görünüyor. Aslında bir programlama sistemleri ürününün karmaşık olduğunu söylediğimizde, birçok parçası olan ve büyük olan bir sistemden bahsediyoruz. Minimum karmaşıklık, bu parçaların iç organizasyonuna ve nasıl bağlandıklarına atıfta bulunur. Geliştirme ekibinin her üyesinin tüm sistem hakkında her zaman her şeyi bilmesi beklenmez, ancak ekibin herhangi bir üyesinin gerektiğinde değiştirebilmesi için sistemin bir bölümünü kolayca anlayabilmesi arzu edilir.

Erich Gamma, Bill Venners ile röportaj yaptığında, bugünkü yazılım ortamlarının, mevcut kodu yeniden kullanmadan uygulamalar oluşturmak için çok karmaşık olduğunu belirtti. Gamma, yeniden kullanım için gerekli olan genişletilebilirliği sunduğu için OOP'nin standart geliştirme paradigması olduğunu varsayar. Gamma, OOP'de üç yeniden kullanım seviyesi olduğunu belirtir: sınıfların yeniden kullanımı, pattern’lerin yeniden kullanımı ve frameworkler. Framework’lerin, sorunları çözmek için temel soyutlamaları tanımladıkları ve çeşitli geliştiricilerin bilgi ve deneyimlerini içeren uygulamalar sundukları için en yüksek yeniden kullanım düzeyi olduğunu vurguluyor.

### Framework’ün kullanışlılığı

Yazılım geliştirmek için framework kullandığınızda, kütüphane kullanırken yaptığınız gibi yalnızca sınıfları yeniden kullanmıyorsunuz. Sınıf kütüphaneleri, yazılım geliştirmede taşeron kullanmak gibidir. Sınıf kütüphanelerini kullanırsınız çünkü zaten çözülmüş genel sorunlara değil, asıl işinize odaklanmak istersiniz. Ama siz bir kütüphanenin sınıflarını çağırıyorsunuz. Framework Hollywood Yasası’na bağlı çalışır: Siz bizi aramayın, biz sizi ararız. Framework sınıfları, sınıflarınızı çağırır. Yazılımın genel davranışıyla ilgilenir ve özel davranışlar tanımlamanıza izin verir. Framework kullandığınızda, yazılımınız franchise sahibi gibidir. Yazılımınız, diğer yazılımlarla aynı altyapıyı paylaşır. Framework bir soruna çözüm sunduğunda, onu kullanan tüm uygulamalar için ortak bir sorunu çözmüş olur. Zindan Efendisi’nin dediği gibi, birinin kaderi herkes tarafından paylaşılır.

Yazılımın bakımını kolaylaştırmak için framework kullanıyoruz. Bu nedenle, framework, minimum karmaşıklıkla kod oluşturmamıza ve gevşek bağlı bileşenlerle mevcut kodu yeniden kullanmamıza yardımcı olmalıdır. Framework, design pattern’leri (birbirine benzer sorunları çözmek için geliştirilmiş ve işlerliği kanıtlanmış genel çözüm önerileri) uyguladığında, genel sorunlara çözüm sağlar ve evrilirken genel çözümlerin iyileştirilmesine odaklanır.

Mariano Iglesias gibi harika bir yazılım geliştiricisinin framework’lere karşı konuştuğunu gördüğümüzde, aslında başka bir şeye karşı konuşuyor: frameworkitis (framework hastalığı). Bill Venners'ın Erich Gamma ile yaptığı konuşmada söylediği gibi, bu terim "framework’lerin her şeyi yapmasını isteme hastalığı" anlamına gelir. Erich Gamma, framework olarak adlandırılan bazı yazılımların aslında geliştiriciler için çok fazla şey yapmak istediğini söylüyor. Sorun şu ki, bunu yapmaya çalıştıklarında, bunu tüm geliştiricilerin istemeyeceği bir şekilde ve - daha da kötüsü - geliştiricilerin değiştiremeyeceği bir şekilde yapıyorlar.

Gerçek bir framework, "Endişelenme, uygulamanızı ben kontrol ediyorum ve genel sorunları sizin için halledeceğim" demelidir. Frameworkitis'li bir çerçeve ise, "Merak etmeyin, ben sizin için kod üretiyorum ve her şeyi en iyi şekilde yapıyorum ve benim uygulamamı değiştirmenize gerek yok - aslında değiştiremezsiniz" diyor. Bu davranış, sürekli değişen bir senaryonun tersi yönde ilerliyor. Frameworkitis, geliştiricileri sevgiden nefrete götürür. Önce kendileri için her şeyi yapıyormuş gibi görünen bir aracı severler ve sonra bağlandıkları ve değiştiremedikleri araçtan nefret ederler. Çok şükür ki Laminas öyle değil.

## Zend Framework’ten Laminas’a

X-Men (2000) filminin açılışında Profesör Xavier, mutasyonun evrimimizin anahtarı olduğunu söylüyor. Mutasyonla tek hücreli organizmalardan gezegendeki baskın türlere nasıl evrimleştiğimizi de ekliyor. Yazılım yaşayan bir organizma gibidir. Hayatta kalabilmesi için gelişmesi gerekir. Bazen çok değişmesi gerekiyor ve bu bir mutasyon gibi görünüyor. Değişim ihtiyacı, yazılımın değişime izin vermesini gerektirir. Değiştirilmesi zor bir yazılım parçası yok olmaya adaydır.

Yazılımın evrimi, bir bileşeni diğeriyle değiştirmek gibi görünebilir. Ölçeklenebilirliğin, performansın veya güvenliğin sürdürülmesi veya iyileştirilmesi için bir değişim gerekli olabilir.

Ancak, bir yazılım parçasının bir bileşenini değiştirmek zor veya neredeyse imkansızsa, iyileştirmelerin zor bir şekilde, genellikle mevcut kodu yeniden yazarak yapılması gerekecektir.

Örüntüler (nesnelerin düzenli bir biçimde birbirini takip ederek gelişmesi) - bileşenler de aynı örüntüleri paylaştığı için - bileşenlerin değiştirilmesine yardımcı olur. Bir şirketin yazılım geliştirme ekipleri, bileşenler için farklı örüntüler kullanarak yazılım geliştirirse, bileşenlerin paylaşılması zor olacaktır ve bu, uzun ömürlü yazılımlar için arzu edilen bir özellik olan kodun yeniden kullanımı engeller.

### Zend Framework ile tanışmam

2005 yılında, birkaç yazılım geliştirme birimine sahip büyük bir IT şirketine katıldım. Bu şirket devlet için yazılımlar geliştiriyordu ve çeşitli programlama dillerinde binlerce proje geliştirdi. Sorun, her ekibin projeleri için farklı standartlar kullanmasıydı, bu nedenle bileşenler yeniden kullanılamıyordu. Birkaç ekip framework kullandı, ancak her biri aynı dil için farklı frameworkler kullandı. Dolayısıyla, bileşenleri paylaşmanın zorluğu devam etti.

2007'de yeni teknolojiler arayan bir bölümde çalışıyordum ve ekibim şirkette PHP uygulama geliştirmeyi standart hale getirmek için bir framework seçmek zorunda kaldı. Tesadüfen, o yıl São Paulo şehrinde PHP frameworkleri üzerine bir seminer düzenlendi. Bu etkinliğe gittik ve frameworkler ile ilgili üç konuşmaya katıldık: CakePHP, Symfony ve Zend. CakePHP'yi Brezilya'daki ilk PHP Konferansından zaten biliyordum ve şirketimden birkaç meslektaşım zaten Symfony kullanıyordu. Ancak Zend Framework benim için tam bir yenilikti. Bu sürpriz olmamalı çünkü Zend Framework'ün ilk kararlı sürümü o yıl yayınlandı. Ancak 2005'ten beri geliştirme aşamasındaydı.

CakePHP ve Symfony konferanslarına izleyicilerin tepkisi benzerdi. Konuşmacılar, terminalde kod üreten komutları çalıştırdığında, izleyiciler Vay canına! dediler. Ancak Zend'in konuşmasında kod üreteci olmadığını gördüklerinde cesaretleri kırıldı. Konuşmacı şikayetleri dinledi ve Zend'in kod oluşturma araçları olmamasına rağmen oldukça esnek olduğunu söyledi. İzleyicilerden biri, framework mimarisinin, yalnızca uygulamayı oluşturmakla kalmayıp uygulamayı sürdürmekle de ilgilendiğini fark etti.

Etkinlikten çıkarken, birkaç kişinin CakePHP veya Symfony kullanacağını söylediğini, kod oluşturma araçlarına vurgu yaptığını duydum. Bu insanlar muhtemelen yazılım geliştirmede veya daha doğrusu mevcut yazılımların bakımında derinlemesine deneyime sahip değillerdi. IDE gibi kod oluşturmaya yardımcı olabilecek başka araçlar da vardır. Frameworkler, kod oluşturma araçları değil, kodu yeniden kullanma araçlarıdır.

Konuşmacılardan biri, o etkinlikte sunulan frameworkler de dahil olmak üzere 5 framework’ün analizini içeren bir kitap satıyordu. Satın aldım, okudum ve bu frameworkler  için testler yaptım. Analiz edilen frameworkler arasında minimum karmaşıklık, gevşek bağlantı, genişletilebilirlik ve yeniden kullanılabilirlik gibi en iyi özelliklere sahip olan Zend Framework idi. Büyük ölçüde yapılandırılabilirdi ve diğer framework bileşenlerinin kullanılmasını engellemiyordu. O zamanlar PHP Framework Interoperability Group (PHP-FIG) henüz mevcut değildi, ancak birlikte çalışabilirlik sunan araçlara ihtiyacımız vardı.

**PHP Framework Interoperability Group**

2009'da Brezilya'daki PHP Konferansında bazı PHP konuşmacıları PHP Framewarks adlı bir konferans verdi. Bu "frameworkler savaşına" şaka yollu bir göndermeydi. Konuşmacıların her biri en sevdikleri framework’ü savundu ve diğerlerine saldırdı. Belki tam olarak bir savaş  yoktu, ancak gerçek şu ki, bir framework’ü bir kez benimsediğinizde, diğerlerinin bileşenlerini kullanmak zordu çünkü her biri kendi örüntülerini takip ediyordu. Bu senaryo, aynı yıl, 2009'da, bir grup PHP uzmanının PHP topluluğu için bileşenlerin spesifikasyonlarını (teknik standartlarını) oluşturmak üzere Chicago, ABD'de bir araya gelmesiyle değişmeye başladı. Bu grup ilk olarak PHP Standard Recommendations (PSRs) olarak adlandırıldı. İlk spesifikasyon, Composer'ın ortaya çıkmasına yardımcı olan bir otomatik yükleme standardı hakkındaydı. 2011 yılında grup, adını PHP Framework Interoperability Group (PHP-FIG) olarak değiştirdi. O zamandan beri son derece etkileyici PHP projelerinin yaratıcıları, PHP Standard Recommendations (PSRs) adı verilen özellikleri tartışmak ve yazmak için bir araya geldi. Çeşitli frameworkler ve diğer PHP uygulamaları, PHP-FIG standartlarını benimsemiştir.

### Zend Framework - PHP uygulamaları için referans mimarisi

Zend Framework, uygulamaların gelişmesine yardımcı olan referans mimarisi oluşturmamıza imkan verdi. Uygulama, kendi bünyesinde bulunan ve yalnızca kendisine hizmet eden dahili bileşenleri veya diğer uygulamalar tarafından paylaşılan bileşenleri kullanabilir.

Belirli bir sorunu çözmek için, yazılım mimarı önce mevcut bir bileşeni aramalıdır. Varsa, ancak tüm gereksinimleri karşılamıyorsa, genişletilme olasılığı analiz edilmelidir. Yalnızca son çare olarak yeni bir bileşen oluşturulmalıdır. Bu durumda bileşen, belirli bir ihtiyacı karşılamak için dahili bir bileşen olarak doğar. Ancak bileşen mimarisi, daha sonra dağıtılabilmesi, yeniden kullanılabilir bir bileşen haline gelmesi ve iyileştirilebilmesi için genel olarak ele alınmalıdır. Zend Framework bu konuyla ilgili bir yapıya sahiptir.

Zend Framework'ün ilk kararlı sürümü PHP 5.3'ten önce yayınlandı, dolayısıyla ZF1 namespace kullanmıyordu. Henüz Composer yoktu, bu nedenle ZF1'in kendi otomatik yükleyicisi vardı. Ancak Zend Framework'ün yapısında sunduğu tek özellik, bahsettiğim gibi değişme olasılığıydı. Kararsız sürümler şu şekilde yapılandırılabilir kodlar getirdi:

```
Zend_Controller_Front::run('/path/to/controllers');
```

Zend Framework, Controller sınıfları için dizin belirlemek yerine, geliştiricinin seçim yapmasına izin verir. Önerilen bir proje yapısı vardır ve bugün bir uygulama oluşturmaya başlamak için Model-View-Controller (MVC) iskeleti kullanılıyor.

Zend Framework'ün bir başka ilginç özelliği de, PHP uygulamalarının zaten var olduğunu ve var olanı geliştirmeniz gerektiğini fark etmesidir. Yeni projeler için her zaman talep olmayacak, ancak kullanılan projelerin sürdürülmesi gerekiyor. Böylece, Zend Framework en başından itibaren MVC’den ayrılmış bileşenlerin kullanımına izin verir. Zend Framework bileşenlerini kullanarak bir uygulamayı yeniden düzenleyebilmek, her şeyi değiştirmek zorunda kalmadan uygulamanın odak noktası olmayan görevleri dışarıdan alabilmek anlamına geliyordu. Değişim gereklidir, ancak yavaş, kademeli ve güvenli bir şekilde yapılabiliyorsa daha iyidir.

Zend Framework, design pattern’lerini ve programlama paradigmalarını dahil ederken, PHP programlama dilinin ve diğer yazılım geliştirme teknolojilerinin gelişimine ayak uydurarak kademeli olarak gelişti. 2008'de Zend Framework'ün 1.5 sürümü, Two-Step View pattern’i Zend_Layout bileşeniyle birleştirdi ve dinamik formlar oluşturmak için Zend_Form adlı bir bileşen geliştirdi. Aynı yıl, 1.6 sürümü PHPUnit'i Zend_Test bileşeni aracılığıyla genişletti. 2009'da Zend Framework, kod oluşturma aracı olan Zend_Tool'u ve IOC bileşeni olan Zend_Application'ı kazandı. Eclipse ve Netbeans gibi IDE'ler çok geçmeden Zend_Tool ile entegrasyon sağladı.

İlk ana sürümünde bile, Zend Framework isteğe bağlı olarak modül oluşturma imkanı sunmaya başladı. Zend Framework terminolojisinde modüller, yeniden kullanılabilen bağımsız birimlerdir. Modül mimarisi, birkaç alt sisteme bölünmesi gereken karmaşık sistemler için çok uygundur. Mikroservis dünyasında modüller, kademeli olarak dağıtılmış bir sisteme dönüştürülebilen, başlangıçta monolitik bir uygulama oluşturmanıza olanak tanır.

Neden mikroservisleri kullanarak bir uygulama oluşturmuyoruz diye sorabilirsiniz. Başka bir soru ile cevaplayalım: Bu gerçekten gerekli mi? Bugün, bulut ortamları ile esnek kaynaklara sahibiz, ancak bu, henüz ihtiyaç duymadığımız kaynakları tahsis etmek için bir sebep değil. Bunu önceden yapmamıza gerek yok, sadece değişime hazırlıklı olmalıyız.

Zend Framework 2.0 sürümünde, modül mimarisini geliştirdi ve bileşenlerin tutarlılığını ve performansını iyileştirdi. Kod, namespace kullanacak şekilde yeniden düzenlendi ve iki yeni bileşen dahil edildi: MVC uygulaması tarafından kullanılan event-driven (olaya dayalı) programlama için çeşitli stratejilerin bir kombinasyonu olan EventManager ve dependency injection (bağımlılık yönetimi) için bir container olan ServiceManager. Bileşenler arasındaki ayrıştırma, sürüm 2.0'dan bu yana önemli ölçüde arttı.

Sürüm 2.5, Zend Framework'e köklü değişiklikler getirdi. Her bileşene kendi reposu verildi ve bağımsız bir yaşam döngüsüne sahip oldu. Ayrıca, sınıfların otomatik yüklenmesi ve bileşenlerin kurulumu artık Composer tarafından yapılıyor. 2.4 sürümüne kadar, Zend Framework her şeyi indiriyordu. 2.5 sürümünden itibaren, yalnızca istediğiniz bileşenleri indirebilir ve kullanabilirsiniz.

Zend Framework'ün 3.0 sürümü, PHP'ye olan minimum bağımlılığı versiyon 7'ye yükseltti. Ek olarak, middleware oluşturmak için özel bir microframework olan Expressive'i getirdi. Bu microframework, kullanılacak bileşenlerden tamamen bağımsız olarak, PSR'ler ile güçlü bir uyum içinde uygulanmıştır.

Ve bu, Zend Framework'ün sonuydu. Evet bu kadardı çünkü bölümün başında da söylediğimiz gibi artık Lasminas olarak adlandırılıyor. Lamina bıçak anlamına gelir. Bu ismin nedeni, framework’ün uygulamaların katmanlar halinde - ancak ince katmanlar halinde, yani gerekli minimum katman boyutuyla - oluşturulmasına izin vermesidir. Profesör X'in dediği gibi, evrim yavaştır. Ancak bazen evrim ileri sıçrar. Zend Framework'ten Laminas'a geçiş, tüm sınıf adlarının yeniden düzenlenmesini gerektirdi. Ayrıca topluluğun teknik ve sosyal altyapısı da değişmektedir. Bu sıçrayış iyi oldu çünkü bunca yıllık deneyime sahip bir framework artık daha açık.

### Zend Framework ve Laminas arasındaki farklar

Ekosistem açısından Zend Framework ve Laminas arasında bazı farklılıklar vardır. Zend Framework, açık kaynaklı bir proje olarak kod katkılarını kabul etti. Laminas, kar amacı gütmeyen bir kuruluş olduğu için bağış alabiliyor. Zend Framework, topluluğun tartışabilmesi ve katkıda bulunabilmesi için forumlara ve bir Slack kanalına sahipti. Ayrıca Laminas, aylık olarak herkese açık toplantılar düzenler ve Technical Steering Committee (TSC, projeyi yöneten teknik bilgiye sahip üyelerden oluşan bir gruptur) oylarının kaydını tutar. Diğer bir deyişle Laminas, Zend Framework'e göre daha açık ve şeffaftır. Dilerseniz, web uygulamaları oluşturmanıza yardımcı olan bir ürünün geliştirilmesine aktif olarak katılabilirsiniz.

Steve McConnell, programlama dili seçiminin yazılım projeleri için önemli bir karar olduğunu belirtir. Buna karşılık PHP topluluğu, PHP'nin özellikle web geliştirme için uygun olduğunu belirtir. Ancak programlama dili, endişelenmeniz gereken şeylerden yalnızca biridir. Bu yüzden Laminas’tan bahsediyoruz.

## Laminas topluluğunun teknik ve sosyal altyapısı

Laminas’ın birçok özelliğinden bahsedeceğiz ama elbette her şeye değinmeyeceğiz. Laminas için ana referans kaynak, https://docs.laminas.dev adresinde bulunan resmi dökümantasyondur.

Dokümantasyon ana sayfasındaki Components ve MVC seçenekleri bu kitabın içeriği ile doğrudan ilişkilidir. Bununla birlikte Laminas, - daha önce Expressive olarak bilinen - microframework Mezzio'yu ve API Tools’u da içerir.

Tüm bileşenlerin kaynak kodunu https://github.com/laminas adresinden indirilebilir ve katkıda bulunabilirsiniz. Olgunlaşmış bileşenlere sahip 100'den fazla repo bulacaksınız. Bu sayfada, hataları düzeltmek veya yeni özellikler istemek için konular açabilirsiniz - özellikler için alacağınız yanıtın topluluğun ilgisine bağlı olacağını unutmayın. Ücretsiz ve açık kaynak bir projeye katkıda bulunmak, yazılım geliştirme hakkında daha fazla bilgi edinmenin harika bir yoludur ve daha sonra meslektaşlarınız tarafından tanınmanıza yardımcı olabilir.

Roger Pressman, yazılımı bilgisayar programları ve bu programlarla ilgili belgelerin bir kombinasyonu olarak tanımlar. İnsanlar aynı zamanda bir yazılım dokümantasyonunun parçasıdır, çünkü genellikle henüz açık bilgiye dönüştürülmemiş örtülü bilgiye sahiptirler. Dokümantasyon yeterli olmadığında, insanlardan yardım almamız gerekebilir ve kaydedilmiş bir görüşme dokümantasyona dönüşebilir. https://discourse.laminas.dev adresinde bulunan resmi tartışma forumlarında Laminas'a dahil olan veya ilgilenen kişilerle sohbet edebilirsiniz. security@getlaminas.org adresine mesaj göndererek olası güvenlik açıklarını bildirebilirsiniz. Laminas'ın güvenlik politikası hakkında bilgi almak için https://getlaminas.org/security adresini ziyaret edin.

Konu açarak ve sorularınızı forumlara göndererek topluluktan destek alabilirsiniz; hatta bileşenleri genişleterek sorunları kendiniz çözebilirsiniz. Ancak isteğe bağlı danışmanlık desteği veya uzun vadeli destek istiyorsanız, Zend Technologies'in kurumsal desteğini düşünebilirsiniz.

## Özet

Yazılım yaşayan bir organizma gibidir: doğar, büyür ve bir gün ölür. Yazılımımızın erken ölmesini elbette istemiyoruz. Bunun için yazılımlarımıza uzun süre hayatta kalmasını sağlayacak özellikler kazandırmamız gerekiyor. Bu bölümde, yeniden kullanılabilir ve bakımı kolay yazılımlar oluşturmamıza yardımcı olan framework’lerden bahsettik. PHP web uygulamaları oluşturmak için ücretsiz ve açık kaynak bir framework olan Laminas'ın tarihine değindik. Bir sonraki bölümde Laminas ile PHP web uygulamaları geliştirmek için ortamımızı hazırlayacağız.

**Kaynak:** "PHP Web Development with Laminas", Flávio Gomes da Silva Lisboa