---
layout: post
title: PHP - Tasarım ve Yönetim
---

Temmuz 2004'te PHP 5.0 yayınlandı. Bu sürüm, bir dizi radikal geliştirmeyle geldi. Belki de bunlardan ilki, nesne yönelimli programlama için kökten geliştirilmiş destekti. Bu, PHP topluluğu içinde nesnelere ve tasarıma büyük ilgi uyandırdı. Aslında bu, PHP 4.0 ile  başlayan bir sürecin devamıydı.

Bu bölümde, nesnelerin çözebileceği bazı sorunları inceliyorum. Örüntülerin ve ilgili uygulamaların evriminin bazı yönlerini kısaca özetliyorum. 

Ayrıca bu kitapta ele alınan konuları da özetledim.

- *Felaketin evrimi*: Bir proje kötüye gidiyor
- *Tasarım ve PHP*: Nesne yönelimli tasarım teknikleri PHP topluluğunda nasıl kök saldı?
- *Bu kitap*: Nesneler, Pattern’ler, Uygulama

## Sorun

Sorun şu ki PHP çok kolay. Fikirlerinizi denemeniz için sizi cezbeder ve iyi sonuçlarla gururunuzu okşar. PHP bunu desteklemek için tasarlandığından, kodunuzun çoğunu doğrudan web sayfalarınıza yazarsınız. Sayfadan sayfaya dahil edilebilecek dosyalara yardımcı işlevler (veritabanı erişim kodu gibi) eklersiniz ve siz farkına bile varmadan çalışan bir web uygulamanız olur.

Felakete doğru ilerliyorsun. Bunu elbette anlamıyorsunuz çünkü siteniz harika görünüyor. İyi performans gösteriyor, müşterileriniz mutlu ve kullanıcılarınız para harcıyor.

Yeni bir aşamaya başlamak için koda geri döndüğünüzde sorun çıkar. Artık daha büyük bir ekibiniz, daha fazla kullanıcınız ve daha büyük bir bütçeniz var. Ancak, hiçbir uyarı olmadan  işler ters gitmeye başlar. Sanki projeniz zehirlenmiş gibi.

Yeni programcınız kodu anlamakta zorlanıyor. Bir ekip üyesi olarak tam güce ulaşması beklediğinizden daha uzun sürüyor.

Bir gün olarak tahmin edilen basit bir değişiklik, sonuç olarak 20 veya daha fazla sayfayı  güncellemeniz gerektiğini keşfettiğinizde üç gün sürer.

Yazılımcılarınızdan biri, bir süre önce aynı kodda yaptığınız büyük değişikliklerin üzerine kendi sürümünü kaydeder. Kayıp üç gün boyunca fark edilmez, bu zamana kadar kendi yerel kopyanızı değiştirmiş olursunuz. Aynı zamanda dosya üzerinde çalışan üçüncü bir geliştiriciyi bekleterek karmaşayı çözmek bir gün sürer.

Uygulamanın popülaritesi nedeniyle, kodu yeni bir sunucuya taşımanız gerekir. Proje elle kurulmalıdır ve dosya yollarının, veritabanı adlarının ve parolaların birçok kaynak dosyaya sabit kodlanmış olduğunu keşfedersiniz. Taşıma sırasında çalışmayı durdurursunuz çünkü yapılandırma değişikliklerinin üzerine yazmak istemezsiniz. Birisinin Apache modülü ModRewrite ile ilgili akıllıca bir şey yaptığı ve uygulamanın artık düzgün çalışması için buna ihtiyaç duyduğu ortaya çıktığında, tahmini iki saat sekiz saat oluyor.

Sonunda 2. faza geçtiniz. Bir buçuk gün boyunca her şey yolunda. İlk hata raporu, siz ofisten çıkmak üzereyken gelir. Müşteri şikayet etmek için dakikalar sonra telefon eder. Rapor ilkine benziyor, ancak biraz daha incelendiğinde, benzer davranışlara neden olan farklı bir hata olduğu ortaya çıkıyor. Projenin geri kalanında kapsamlı değişiklikler gerektiren, fazın başlangıcındaki basit değişikliği hatırlıyorsunuz.

Gerekli değişikliklerin tümünün yerinde olmadığını anlıyorsunuz. Bunun nedeni, başlangıçta atlanmış olmaları veya söz konusu dosyaların üzerine yazılmasıdır. Hataları düzeltmek için gereken değişiklikleri aceleyle yaparsınız. Değişiklikleri test etmek çok acele ediyorsunuz, ancak bunlar basit bir kopyala ve yapıştır meselesi, öyleyse ne ters gidebilir?

Ertesi sabah, ofise geldiğinizde bir alışveriş sepeti modülünün bütün gece kapalı olduğunu görürsünüz. Yaptığınız son dakika değişiklikleri, baştaki tırnak işaretini atlayarak kodu kullanılamaz hale getirdi. Elbette, siz uyurken, diğer zaman dilimlerindeki potansiyel müşteriler tamamen uyanıktı ve mağazanızda para harcamaya hazırdı. Sorunu çözersiniz, müşterileri sakinleştirirsiniz ve ekibi başka bir günkü yangınla mücadele için bir araya getirirsiniz.

Kod yazan insanların bu günlük hikayesi biraz abartılı görünebilir, ancak tüm bunların tekrar tekrar olduğunu gördüm. Pek çok PHP projesi hayata küçük başlar ve canavarlara dönüşür.

Sunum katmanı aynı zamanda uygulama mantığını da içerdiğinden, veritabanı sorguları, kimlik doğrulama kontrolleri, form işlemleri ve daha fazlası sayfadan sayfaya kopyalanır. Bu kod bloklarından birinde değişiklik gerektiğinde, kodun bulunduğu her yerde yapılması gerekir.

Dokümantasyon eksikliği kodun okunmasını zorlaştırır ve test eksikliği belirsiz hataların dağıtıma kadar farkedilmesini engeller. Bir işin değişen doğası, genellikle kodun, temel olarak uygun olmadığı görevleri yerine getirene kadar asıl amacından uzaklaşması anlamına gelir. Bu tür bir kod genellikle arap saçına döndüğünden, yeni amaca uyacak şekilde kısımları değiştirmek ve yeniden yazmak imkansız değilse bile zordur.

## PHP ve Diğer Diller

PHP'nin olağanüstü popülaritesi, sınırlarının erken ve zorlu bir şekilde test edilmesi anlamına geliyordu. Bir sonraki bölümde göreceğiniz gibi, PHP hayata kişisel web sayfalarını yönetmek için bir dizi makro olarak başladı. PHP 3'ün ve daha büyük ölçüde PHP 4'ün gelişiyle, dil hızla büyük kurumsal web sitelerinin arkasındaki başarılı güç haline geldi. Bununla birlikte, birçok yönden, PHP'nin başlangıç mirası, script tasarımı ve proje yönetimine taşındı. Bazı çevrelerde PHP, sunum (presentation) görevleri için en uygun olan hobi dili olarak haksız bir üne sahipti.

Bu sıralarda (milenyum başında), diğer topluluklarda yeni fikirler geçerlilik kazanıyordu. Nesne yönelimli tasarıma olan ilgi, Java topluluğunu harekete geçirdi. Java nesne yönelimli bir dil olduğu için bunun gereksiz olduğunu düşünebilirsiniz. Ancak sınıfları ve nesneleri kullanmak kendi başına belirli bir tasarım yaklaşımı sağlamaz.

Bir sorunu, çözümünün özüyle birlikte tanımlamanın yolu olarak design pattern (tasarım deseni) kavramı ilk kez 1970'lerde tartışıldı. Fikir - belkide en uygun şekilde - bilgisayar bilimi alanından değil, mimarlık alanından, Christopher Alexander'ın ufuk açıcı bir çalışmasıyla ortaya çıkmıştır: A Pattern Language (Oxford University Press, 1977). 1990'ların başlarında, nesne yönelimli programcılar, yazılım tasarımı problemlerini adlandırmak ve tanımlamak için aynı tekniği kullanıyorlardı. Erich Gamma, Richard Helm, Ralph Johnson ve John Vlissides'ın (bundan böyle bu kitapta “Gang of Four” takma adlarıyla anılacaklardır) yazdığı tasarım desenleri üzerine ufuk açıcı kitap Design Patterns: Elements of Reusable Object-Oriented Software bugün hala vazgeçilmezdir. İçerdiği pattern’ler, bu alana yeni başlayan herkes için gerekli bir ilk adımdır, bu nedenle bu kitaptaki pattern’lerin çoğu ondan alınmıştır.

Java dilinin kendisi, API'sinde birçok core pattern’i konuşlandırdı, ancak design pattern’lerin topluluğun genelinin bilincine sızması 1990'ların sonlarını buldu. Pattern’ler hızla Main Street kitapçılarının bilgisayar bölümlerine ulaştı ve ilk ateşli tartışmalar mail listelerinde ve forumlarda başladı.

İlgili konular da önem kazandı. Bunların arasında Kent Beck tarafından desteklenen Extreme Programming (XP) de vardı. XP, esnek, tasarım yönlü, yüksek odaklı planlama ve yürütmeyi teşvik eden bir proje yaklaşımıdır.

XP'nin ilkeleri arasında öne çıkan, testin bir projenin başarısı için çok önemli olduğu konusundaki ısrardır. Testler otomatikleştirilmeli, sık çalıştırılmalı ve tercihen kodlar yazılmadan önce tasarlanmalıdır.

XP ayrıca projelerin küçük (çok küçük) iterasyonlara bölünmesi gerektiğini de belirtir. Hem kod hem de gereksinimler her zaman incelenmelidir. Mimari ve tasarım, kodun sık sık gözden geçirilmesine yol açan, paylaşılan ve sürekli bir sorun olmalıdır.

XP, tasarım hareketinin militan kanadıysa, o zaman ılımlı eğilim, programlama hakkında şimdiye kadar okuduğum en iyi kitaplardan biri tarafından iyi bir şekilde temsil ediliyor: The Pragmatic Programmer: From Journeyman to Master by Andrew Hunt and David Thomas.

XP, bazıları tarafından biraz kült olarak kabul edildi, ama en üst düzeyde yirmi yıllık nesne yönelimli pratikle ortaya çıktı. Özellikle, yeniden düzenleme olarak bilinen code revision, pattern’lere güçlü bir yardımcı-tamamlayıcı olarak ele alındı.

Yeniden düzenleme 1980'lerden beri gelişti, ancak Martin Fowler'ın 1999'da yayınlanan ve alanı tanımlayan "Refactoring: Improving the Design of Existing Code" adlı kitabında  sistematikleştirildi.

XP'nin ve pattern’lerin öne çıkmasıyla birlikte test etme sıcak bir konu haline geldi. Otomatik testlerin önemi, Java programcılarının cephaneliğinde önemli bir silah haline gelen güçlü JUnit test platformunun piyasaya sürülmesiyle daha da vurgulandı. Konuyla ilgili dönüm noktası niteliğinde bir makale, Kent Beck ve Erich Gamma tarafından yazılan "Test Infected: Programmers Love Writing Tests", konuya mükemmel bir giriş yapıyor ve büyük ölçüde etkili olmaya devam ediyor.

PHP 4, yaklaşık olarak bu sıralarda piyasaya sürüldü ve beraberinde verimlilikte iyileştirmeler ve en önemlisi, nesneler için gelişmiş destek getirdi. Bu geliştirmeler, tamamen nesne yönelimli projeleri bir olasılık haline getirdi. Programcılar, biraz şaşırtacak şekilde bu özelliği benimsedi. Bir sonraki bölümde göreceğiniz gibi, PHP'nin nesne desteği kesinlikle mükemmel değildi. Ancak disiplin ve sözdiziminin dikkatli kullanımı ile kişi aynı anda nesneler ve PHP hakkında düşünmeye başlayabilir.

Yine de, bu bölümün başında tasvir edilene benzer tasarım felaketleri yaygın olmaya devam etti. Tasarım kültürü olgunlaşmamıştı ve PHP ile ilgili kitaplarda neredeyse yoktu. Ancak çevrimiçi ortamda ilgi olduğu açıktı. Leon Atkinson, 2001'de Zend için PHP ve pattern’ler hakkında bir makale yazdı ve Harry Fuecks, 2002'de www.phppatterns.com'da (şu anda  kullanılmayan) kendi blog’unu başlattı. BinaryCloud gibi model tabanlı framework projelerinin yanı sıra otomatikleştirilmiş test ve belgeleme araçları ortaya çıkmaya başladı.

İlk PHP 5 beta sürümünün 2003 yılında piyasaya sürülmesi, nesne yönelimli programlama dili olarak PHP'nin geleceğini garantiledi. Zend Engine 2, büyük ölçüde geliştirilmiş nesne desteği sağladı. Aynı derecede önemli olan, nesnelerin ve nesne yönelimli tasarımın artık PHP projelerinin merkezinde yer aldığına dair bir sinyal gönderdi.

Yıllar geçtikçe PHP 5, namespace ve closure gibi önemli yeni özellikleri bünyesine katarak gelişmeye ve evrilmeye devam etti. Bu süre zarfında, sunucu taraflı web programlama için en iyi seçenek olarak ününü sağlamlaştırdı.

Aralık 2015'te yayınlanan PHP 7, bu eğilimin devamını temsil ediyordu. Özellikle, birçok geliştiricinin (bu kitabın önceki sürümleriyle birlikte) yıllardır yaygara kopardığı iki özellik olan hem parametre hem de dönüş tipi bildirimleri için destek sağladı. Anonim sınıflar, geliştirilmiş bellek kullanımı ve artırılmış hız dahil olmak üzere birçok başka özellik ve iyileştirme vardı. Yıllar geçtikçe dil, nesne yönelimli yazılımcının bakış açısından istikrarlı bir şekilde daha sağlam, daha temiz ve daha eğlenceli hale geldi.

Aralık 2020'de, PHP 7'nin yayınlanmasından neredeyse tam beş yıl sonra, PHP 8'in piyasaya sürülmesi gerekiyor. Bazı uygulama ayrıntıları değişebilir (ve bu kitabın yazılması sırasında biraz değişmiş olsa da), özellikler bu yazının yazıldığı tarihte (Ağustos 2020) zaten mevcuttur. Birçoğunu burada ayrıntılı olarak ele alıyorum. Tip bildirimlerinde iyileştirmeler, modern özellik ataması ve diğer birçok yeni özellik içerir.

## Kitap Hakkında

Bu kitap, nesne yönelimli tasarım alanında çığır açmaya çalışmıyor. Bunun yerine, PHP bağlamında, bazı yerleşik tasarım ilkelerini ve bazı temel pattern’leri (özellikle klasik Gang of Four kitabı olan Design Patterns'te yazılanları) inceliyorum. Son olarak, bir projenin başarısını sağlamaya yardımcı olabilecek araçlara ve tekniklere bakmak için katı kod sınırlarının ötesine geçiyorum. Giriş ve kısa bir sonuç dışında, kitap üç ana bölüme ayrılmıştır: nesneler, pattern’ler ve uygulama.

**Nesneler**

Bölüm 1'e PHP'nin ve nesnelerin tarihine hızlı bir bakışla başlıyorum, PHP 3'teki sonradan düşünülenden PHP 5'teki temel özelliğe geçişi çiziyorum.

Nesneler hakkında çok az bilginiz olsa da, hiç bilginiz olmadan da deneyimli ve başarılı bir PHP programcısı olabilirsiniz. Bu nedenle nesneleri, sınıfları ve kalıtımı açıklamaya temel ilkelerden başlıyorum. Bu erken aşamada bile, PHP 5, PHP 7 ve PHP 8'in getirdiği bazı nesne geliştirmelerine bakıyorum.

Temel bilgileri aldığınızda, PHP'nin daha gelişmiş nesne yönelimli özelliklerini inceleyerek konumuzun derinliklerine iniyorum. PHP'nin nesneler ve sınıflarla çalışmanıza yardımcı olmak için sağladığı araçlara da bir bölüm ayırdım.

Bununla birlikte, bir sınıfın nasıl tanımlanacağını bilmek ve onu bir nesneyi başlatmak için kullanmak yeterli değildir. Öncelikle sisteminiz için doğru katılımcıları seçmeli ve onlar için en iyi etkileşim yollarına karar vermelisiniz. Bu seçimleri açıklamak ve öğrenmek, nesne araçları ve sözdizimi hakkındaki çıplak gerçekleri öğrenmekten çok daha zordur. Bölüm 1'i PHP ile nesne yönelimli tasarıma girişle bitiriyorum.

**Pattern’ler**

Pattern, yazılım tasarımındaki bir sorunu tanımlar ve çözümün özünü sağlar. Buradaki "Çözüm", bir cookbook’ta bulabileceğiniz türden kes-yapıştır kodu anlamına gelmez (yine de cookbook’lar programcı için birer kaynaktır). Bunun yerine, design pattern, bir sorunu çözmek için kullanılabilecek yaklaşımı tanımlar.

Bölüm 2, design pattern’leri tanımlayarak ve yapılarını açıklayarak başlar. Popülerliklerinin ardındaki bazı nedenlere de bakıyorum.

Pattern’ler, belirli temel tasarım ilkelerini destekleme ve takip etme eğilimindedir. Bunların anlaşılması, pattern’in motivasyonunun analiz edilmesine yardımcı olabilir ve tüm programlamaya faydalı bir şekilde uygulanabilir. Bu ilkelerin bazılarını tartışıyorum. Sınıfları ve etkileşimlerini tanımlamanın platformdan bağımsız bir yolu olan Unified Modeling Language (UML) de inceliyorum.
Bu kitap bir pattern kataloğu olmasa da, en ünlü ve faydalı pattern’lerden bazılarını inceliyorum. Her pattern’in ele aldığı sorunu anlatıyor, çözümü analiz ediyor ve PHP'de bir uygulama örneği sunuyorum.

**Uygulama**

Doğru yönetilmezse, doğru dengelenmiş bir mimari bile başarısız olur. 3. Bölümde, projenizin başarısını garanti altına alan bir framework oluşturmanıza yardımcı olacak mevcut araçlara bakıyorum. Kitabın geri kalanı tasarım ve programlama pratiği hakkındaysa, 3. Kısım kodunuzu yönetme pratiği hakkındadır. İncelediğim araçlar, bir proje için destek yapısı oluşturarak, ortaya çıkan hataları izlemeye yardımcı olabilir, programcılar arasında işbirliğini teşvik edebilir ve kurulum kolaylığı ve kod netliği sağlayabilir.

Otomatik testin gücünden daha önce bahsetmiştim. Bölüm 3'e, bu alandaki sorunlara ve çözümlere genel bir bakış sunan giriş bölümüyle başlıyorum.

Birçok programcı, her şeyi kendi başına yapma dürtüsüne yenik düşmekten suçludur. Composer, ana reposu olan Packagist ile birlikte, kolaylıkla projelere eklenebilecek binlerce  pakete erişim sunar.

Composer konusu açılmışken, bir paketin dağıtımını tek bir komut kadar basit hale getiren kurulum mekanizmasını inceliyorum.

Kod işbirliği ile ilgilidir. Bu gerçek ödüllendirici olabilir. Aynı zamanda tam bir kabus olabilir. Git, birçok programcının birbirlerinin çalışmalarının üzerine yazmadan aynı kod tabanı üzerinde birlikte çalışmasını sağlayan bir sürüm kontrol sistemidir. Geliştirmenin herhangi bir aşamasında projenizin anlık görüntülerini almanıza, kimin hangi değişiklikleri yaptığını görmenize ve projeyi birleştirilebilir dallara ayırmanıza olanak tanır. Git projenizi bir gün kurtaracak.

İnsanlar ve kütüphaneler işbirliği yaptıklarında, partiye genellikle farklı gelenekler ve stiller getirirler. Bu sağlıklı olmakla birlikte birlikte çalışabilirliği de baltalayabilir. Uymak ve kabul etmek gibi sözcükler tüylerimi ürpertiyor ama internetin yaratıcılığının standartlarla desteklendiği inkar edilemez. Belirli geleneklere uyarak, hayal edilemeyecek kadar geniş bir sanal alanda oynamak için özgürleşiriz. Bu yüzden PHP standartlarını, bize nasıl yardımcı olabileceklerini, nasıl ve neden uymamız gerektiğini keşfediyorum.

İki gerçek kaçınılmaz görünüyor. İlk olarak, hatalar genellikle aynı kod bölgesinde tekrarlanır ve bazı iş günleri déjà vu'da bir egzersiz haline getirir. İkincisi, genellikle iyileştirmeler düzelttikleri kadar veya daha fazla bozarlar. Otomatik test, kodunuzdaki sorunlar için bir erken uyarı sistemi sağlayarak bu sorunların her ikisini de ele alabilir. İlk önce Smalltalk için tasarlanmış, ancak şimdi başta Java olmak üzere birçok dile taşınan xUnit test platformunun güçlü bir uygulaması olan PHPUnit'i tanıtıyorum. Özellikle PHPUnit'in özelliklerine ve daha genel olarak test etmenin faydalarına ve maliyetine bakıyorum.

Uygulamanız dağınıksa dosyaların standart olmayan konumlara yüklenmesi gerekebilir, veritabanı kurmak veya sunucu yapılandırmasına yama uygulamak isteyebilir. Kısacası, uygulamaların kurulum sırasında yapılması gerekenler vardır. Phing, Ant adlı bir Java aracının bağlantı noktasıdır. Phing ve Ant, bir yapılandırma dosyasını yorumlar ve kaynak dosyalarınızı onlara söylediğiniz şekilde işler. Bu genellikle dosyalarınızı bir dizinden sisteminizdeki çeşitli hedef konumlara kopyalamak anlamına gelir, ancak ihtiyaçlarınız daha karmaşık hale geldikçe, Phing bunları karşılamak için kolayca ölçeklenir.

Bazı şirketler geliştirme platformlarını zorunlu kılar, ancak çoğu durumda ekipler bir dizi farklı işletim sistemi çalıştırır. Bunların tümü, kolaylıkla bir LAMP yığınını çalıştıracaktır. Ancak ideal olarak, geliştiricilerin kodlarını production’a çok benzeyen ortamlarda çalıştırmaları gerekir. Ekip üyelerinin kendilerine özgü geliştirme platformlarını koruyabilmeleri ancak production benzeri bir sistemde kodlarını çalıştırabilmeleri için sanallaştırma uygulaması olan Vagrant'ı inceliyorum.

Testler oldukça faydalıdır, ancak testlerinizi yüklemeniz, çalıştırmanız ve buna devam etmeniz gerekiyor. Build ve testlerinizi otomatikleştirmezseniz, rehavete kapılmak ve her şeyin kaymasına izin vermek kolaydır. Tam da bu konuda size yardımcı olacak "sürekli entegrasyon" kategorisinde bir araya toplanmış bazı araçlara ve tekniklere bakıyorum.

**Kaynak:** “PHP 8 Objects, Patterns, and Practice”, Matt Zandstra