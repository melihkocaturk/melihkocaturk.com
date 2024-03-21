---
layout: post
title: PHP ve Nesneler
---

Nesneler her zaman PHP projesinin önemli bir parçası değildi. Aslında, bir zamanlar PHP tasarımcıları tarafından “sonradan düşünülmüş” olarak tanımlanmışlardı. Bu bölümde, PHP'nin nesne yönelimli özelliklerinin gelişimini özetleyerek tanıtıyorum.

Aşağıdakilere bakacağız:

- PHP/FI 2.0: PHP, ama bildiğimiz gibi değil.
- PHP 3: Nesneler ilk kez ortaya çıkıyor.
- PHP 4: Nesne yönelimli programlama gelişir.
- PHP 5: Dilin merkezindeki nesneler.
- PHP 7: Boşluğu kapatmak.
- PHP 8: Konsolidasyon devam ediyor.

## PHP Nesnelerinin Kazara Başarısı

PHP'nin kapsamlı nesne desteği ve dolaşımdaki çok sayıda nesne yönelimli PHP kütüphanesi ve uygulamasıyla, PHP'de nesnenin yükselişi doğal ve kaçınılmaz bir süreç gibi görünebilir. Doğrusu, hiçbir şey gerçeklerden daha uzak olamaz.

**Başlangıç: PHP/FI**

Bugün bildiğimiz PHP'nin doğuşu, Rasmus Lerdorf tarafından Perl kullanılarak geliştirilen iki araca dayanmaktadır. PHP, Personal Home Page Tools anlamına geliyordu. FI, Form Interpreter anlamına gelir. Birlikte, veritabanlarına, formlara ve akış kontrolüne SQL ifadeleri gönderen makrolardan oluşuyorlardı.

Bu araçlar C'de yeniden yazıldı ve PHP/FI 2.0 adı altında birleştirildi. Bu aşamadaki dil, bugün tanıdığımız sözdiziminden farklı görünüyordu ama o kadar da farklı değildi. Değişkenler, ilişkisel diziler ve fonksiyonlar için destek vardı. Ancak nesneler ufukta görünmüyordu.

**Sentetik Şeker: PHP 3**

Hatta PHP 3 daha planlama aşamasındayken bile konu gündem dışıydı. PHP 3'ün başlıca mimarları Zeev Suraski ve Andi Gutmans idi. PHP 3 tamamen yeniden yazıldı, ancak nesneler yeni sözdiziminin gerekli bir parçası olarak görülmedi.

Zeev Suraski'ye göre, sınıf desteği neredeyse sonradan düşünülerek eklendi (kesin olarak 27 Ağustos 1997'de). Sınıflar ve nesneler aslında ilişkisel dizileri tanımlamanın ve bunlara erişmenin başka bir yoluydu…

Elbette, metotların ve kalıtımın eklenmesi, sınıfları ilişkisel dizilerden çok daha fazlası haline getirdi, ancak yine de sınıflarla neler yapabileceğiniz konusunda ciddi sınırlamalar vardı. Özellikle, bir üst sınıfın geçersiz kılınan metotlarına erişemezsiniz (bunun ne anlama geldiğini henüz bilmiyorsanız merak etmeyin; daha sonra açıklayacağım). Bir sonraki bölümde inceleyeceğim bir diğer dezavantaj, PHP scriptlerinde nesnelerin aktarılmasının optimal olmayan yoluydu.

Nesnelerin o zamanlar marjinal bir konu olduğu, resmi dökümantasyonda önem taşımamalarıyla vurgulanmaktadır. Dökümantasyon, nesnelere bir cümle ve bir kod örneği ayırdı. Örnek, kalıtımı veya özellikleri (property) göstermiyordu.

**PHP 4 ve Sessiz Devrim**

PHP 4, dil için bir başka çığır açan adım olsa da, temel değişikliklerin çoğu yüzeyin altında gerçekleşti. Zend Motoru (adı Zeev ve Andi'den türetilmiştir) dili güçlendirmek için sıfırdan yazılmıştır. Zend Engine, PHP'yi çalıştıran ana bileşenlerden biridir. Aramak isteyebileceğiniz herhangi bir PHP fonksiyonu, aslında üst düzey uzantı katmanının bir parçasıdır. Bunlar, veritabanı API'leriyle konuşmak veya dizelerde hokkabazlık yapmak gibi işleri yaparlar. Bunun altında, Zend Engine belleği yönetir, kontrolü diğer bileşenlere devreder ve her gün çalıştığınız tanıdık PHP sözdizimini çalıştırılabilir bayt koduna çevirir. Sınıflar gibi temel dil özellikleri için teşekkür etmemiz gereken Zend Engine'dir.

Nesnel bakış açımızdan, PHP 4'ün ana metotları geçersiz kılmayı ve bunlara alt sınıflardan erişmeyi mümkün kılması büyük bir avantajdı.

Ancak büyük bir dezavantaj devam etti. Bir değişkene nesne atamak, onu fonksiyona göndermek veya metottan döndürmek, bir kopyanın oluşmasıyla sonuçlandı. Bunun gibi bir atama düşünün:

```
$my_obj = new User('bob');
$other = $my_obj;
```

Bu kod çalıştırıldığında, aynı User nesnesine iki referans yerine, iki User nesnesinin varlığıyla sonuçlandı. Çoğu nesne yönelimli dilde, değere göre değil referansa göre atama beklersiniz. Bu, nesnelerin kendisini kopyalamak yerine nesne işaretçilerini atayacağınız anlamına gelir. Varsayılan değere göre atama davranışı, programcıların farkında olmadan komut dosyasının bir bölümündeki nesneleri değiştirerek, değişikliklerin başka bir yerdeki referanslar aracılığıyla görülmesini bekledikleri için pek çok hatayla sonuçlandı. Bu kitap boyunca, aynı nesnenin birden fazla referansını kullandığım birçok örnek göreceksiniz.

İyi ki, referansa dayalı atamayı zorlamanın bir yolu vardı, ancak bu, hantal bir yapı kullanmayı hatırlamak anlamına geliyordu.

Referans olarak nasıl atayacağınız aşağıda açıklanmıştır:

```
$other =& $my_obj;
// $other and $my_obj point to same object
```

Bu, referansa göre geçişi zorunlu kılar:

```
function setSchool(& $school)
{
    // $school is now a reference to not a copy of passed object
}
```

Ve işte referansla geri dönüş:

```
function & getSchool()
{
    // returning a reference not a copy
    return $this->school;
}
```

İyi çalışsa da, & işaretini eklemeyi unutmak kolaydı ve bu, hataların koda sızmasının çok kolay olduğu anlamına geliyordu. Hataların izini sürmek özellikle zordu, çünkü nadiren raporlanan hatalara neden oluyorlardı.

PHP kılavuzunda genel olarak sözdiziminin, özel olarak nesnelerin kapsamı genişletildi ve nesne yönelimli kodlama akımına doğru yönelim başladı. PHP'deki nesneler tartışmasız değildi (şimdi olduğu gibi) ve "Nesnelere ihtiyacım var mı?" sorusu, posta listelerinde yaygın olarak görülüyordu. Gerçekten de, Zend sitesi, nesne yönelimli programlamayı teşvik eden makalelerin yanı sıra bu konuda uyaran makalelere ev sahipliği yaptı. Referansla geçiş sorunları ve tartışmalara rağmen, birçok programcı kodlarını & karakteriyle doldurdu. Nesne yönelimli PHP popülaritesini artırdı. Zeev Suraski bu konuyu DevX.com için bir makalede yazdı.

“PHP tarihindeki en büyük dönüm noktalarından biri - sınırlı işlevselliğe ve bir dizi sorun ve sınırlamaya rağmen - nesne yönelimli programlamanın gelişmesi ve artan sayıda kullanıma hazır PHP uygulamaları için en popüler paradigma haline gelmesiydi. Çoğunlukla beklenmedik olan bu eğilim, PHP'yi hazırlıksız yakaladı. Nesnelerin diğer dillerdeki nesneler gibi davranmadığı ve bunun yerine *ilişkisel* diziler gibi davrandığı ortaya çıktı.”

Önceki bölümde belirtildiği gibi, nesne yönelimli tasarıma olan ilgi çevrimiçi sitelerde ve makalelerde belirgin hale geldi. PHP'nin resmi yazılım reposu PEAR nesne yönelimli programlamayı benimsemiştir. Geriye dönüp bakıldığında, PHP'nin nesne yönelimli desteği benimsemesinin kaçınılmaz bir güce isteksiz bir teslimiyet olduğunu düşünmek kolaydır. Nesneye yönelik programlamanın 1960'lardan beri var olmasına rağmen, 1990'ların ortalarında gerçekten zemin kazandığını hatırlamak önemlidir. Nesne yönelimli programlamayı popülerleştiren Java, 1995'e kadar piyasaya sürülmedi. Prosedürel bir dil olan C'nin bir üst kümesi C++, 1979'dan beri ortalıkta dolaşıyor. Uzun bir evrimden sonra, 1990'larda tartışmasız büyük bir sıçrama yaptı. Perl 5, 1994 yılında piyasaya sürüldü, bu, kullanıcılarının nesneler üzerinde düşünmesini mümkün kılan, prosedürel bir dilde başka bir devrimdi. Küçük bir prosedürel dil için PHP, nesne desteğini oldukça hızlı geliştirerek, kullanıcılarının gereksinimlerine gerçek bir yanıt verdi.

**Benimsenen Değişim: PHP 5**

PHP 5, nesnelerin ve nesne yönelimli programlamanın açık onayını temsil ediyordu. Bu, nesnelerin PHP ile çalışmanın tek yolu olduğu anlamına gelmez (bu arada, bu kitap bunu da söylemiyor). Bununla birlikte, nesneler, enterprise sistemler geliştirmek için güçlü ve önemli bir araç olarak kabul edildi ve PHP, core tasarımında bunları tam olarak destekledi.

Muhtemelen, PHP 5'teki geliştirmelerin önemli etkilerinden biri, dilin daha büyük İnternet şirketleri tarafından benimsenmesiydi. Örneğin Yahoo ve Facebook, PHP'yi yoğun bir şekilde kullanmaya başladı. Sürüm 5 ile PHP, web geliştirme için standart dillerden biri haline geldi.

Nesneler dil sürücüsüne taşınmıştı. Belki de en önemli değişiklik, pass-by-reference davranışıydı. Ancak bu sadece başlangıçtı. Bu kitap boyunca ve özellikle de bu bölümünde, private ve protected metotlar ve properties (özellikler), static anahtar sözcüğü, namespace’ler, type hints (artık type declaration olarak adlandırılmaktadır) ve exceptions (istisnalar) dahil olmak üzere çok daha fazla geliştirmeyle karşılaşacağız. PHP 5 uzun bir süredir (yaklaşık 12 yıl) ortalıkta dolaşıyordu ve yeni özellikler aşamalı olarak piyasaya sürüldü.

**Not**: PHP'nin, PHP 5'in tanıtılmasıyla referans olarak atama için tam anlamıyla hareket etmediğini ve bu durumun değişmediğini belirtmekte fayda var. Bunun yerine, varsayılan olarak, bir nesne atandığında, metoda iletildiğinde veya metottan döndürüldüğünde, o nesneye tanımlayıcı kopyalanır. Bu nedenle, & işareti ile referansa göre atamayı zorunlu kılmadığınız sürece, yine de bir kopyalama işlemi gerçekleştiriyorsunuz. Bununla birlikte, pratik açıdan, bu tür kopyalama ile referans yoluyla atama arasında genellikle çok az fark vardır, çünkü aynı hedef nesneye başvuruda bulunursunuz.

```
class megaquiz_util_Conf
{
}
```

Bunun gibi sınıf adları, paketler arasındaki çakışmaları önlemenin bir yoludur, ancak dolambaçlı kodlar oluşturabilirler. Ayrıca closure, generator, trait, late static binding desteği de verildi.

**PHP 7: Boşluğu Kapatmak**

Programcılar zorlu bir gruptur. Design pattern’leri seven birçok kişi için, PHP'de hala eksik olan iki temel özellik vardı. Bunlar, skaler tip bildirimleri ve zorunlu dönüş tipleriydi. PHP 5 ile, yalnızca bir nesneye, diziye veya daha sonra çağrılabilir bir koda ihtiyacınız olduğu sürece, metoda veya fonksiyona iletilen değişkenin tipini zorlamak mümkündü. Skaler değerler (integer, string ve float gibi) hiç uygulanamadı. Ayrıca, bir metodun veya fonksiyonun dönüş tipini bildirme şansınız yoktu.

Göreceğiniz gibi, nesne yönelimli tasarım genellikle bir tür sözleşme olarak metot bildirimi kullanır. Metot, belirli girdiler talep eder ve karşılıklı olarak, size belirli bir veri tipini geri vermeyi taahhüt eder. PHP 5 programcıları, birçok durumda bu tür sözleşmeleri sürdürmek için yorumlara, kurallara ve manuel tip denetimine güvenmek zorunda kaldı. Geliştiriciler ve yorumcular genellikle bundan şikayet ediyordu.

PHP 7, skaler tip bildirimlerini (önceden type hint olarak biliniyordu) ve dönüş tipi bildirimlerini tanıttı. Dahası, PHP 7.4, typed property sunarak tip güvenliğini daha da ileri götürdü. Doğal olarak, tüm bunlar bu baskıda ele alınmıştır.

PHP 7 ayrıca, anonim sınıflar ve bazı namespace geliştirmeleri dahil olmak üzere başka güzel şeyler de sağladı.

**PHP 8: Konsolidasyon devam ediyor**

PHP, diğer dillerin en iyi özelliklerini ödünç almıştır. PHP 8, diğer dillerde annotation olarak bilinen nitelikler de dahil olmak üzere birçok yeni özellik sunar. Bu kullanışlı etiketler, bir sistemdeki sınıflar, metotlar, properties ve sabitler hakkında ek bağlamsal bilgiler sağlamak için kullanılabilir. Ayrıca PHP 8, tip bildirimleri için desteğini genişletmeye devam etmiştir. Bu alanda özellikle ilginç olanı, union tipi bildirimidir. Bu, bir özelliğin veya parametrenin tipinin belirtilen birkaç tipten biriyle sınırlandırabilmenize olanak tanır. PHP'nin tip esnekliğinden yararlanırken aynı zamanda tipleri kilitleyebilirsiniz.

**Nesne Tartışması**

Nesneler ve nesne yönelimli tasarım, tutku uyandırıyor gibi görünüyor. Pek çok mükemmel programcı, nesneleri kullanmadan yıllarca mükemmel kodlar üretti ve PHP, prosedürel web programlama için etkileyici bir platform olmaya devam ediyor.

Bu kitap doğal olarak baştan sona nesne yönelimli önyargı sergiliyor, bu önyargı benim nesneye bulaşmış bakış açımı yansıtıyor. Konumuz nesneler olduğundan, vurgunun kesinlikle nesne yönelimli olması kaçınılmazdır. Bununla birlikte, bu kitaptaki hiçbir şey, nesnelerin başarıya giden tek gerçek yol olduğunu öne sürmeyi amaçlamamaktadır.

Geliştiricilerin nesne yönelimli bir dil olarak PHP ile çalışmayı seçip seçmemesi bir zamanlar bir tercih meselesiydi. Bu, tamamen kabul edilebilir sistemler yaratılabileceği ölçüde hala geçerlidir. Bazı harika araçlar (örneğin, WordPress) temel mimarilerinde hala prosedüreldir. Bununla birlikte, PHP'nin nesne desteğini kullanmadan ve anlamadan bir PHP programcısı olarak çalışmak giderek daha zor hale geliyor, özellikle de projelerinizde kullanacağınız kütüphanelerin kendileri muhtemelen nesne yönelimli olacakları için.

Yine de, Perl'in şu ünlü mottosunu akılda tutmaya değer: "Bir şeyi yapmanın birden fazla yolu var." Bu özellikle daha küçük scriptler için geçerlidir, hızlı bir şekilde çalışan bir örnek oluşturmak, daha büyük bir sisteme ölçeklendirilecek bir yapı oluşturmaktan daha önemlidir.

Kod esnek bir ortamdır. İşin püf noktası, konseptinizin ne zaman daha büyük bir geliştirmenin temeli haline geldiğini bilmektir. Artık büyüyen projenize tasarım odaklı bir yaklaşım benimsemeye karar verdiğinize göre, bu kitabın nesne yönelimli mimariler oluşturmaya başlamak için ihtiyaç duyduğunuz yardımı sağlayacağını umuyorum.

**Özet**

Bu kısa bölüm, nesneleri PHP dilindeki bağlamlarına yerleştirdi. PHP'nin geleceği, nesne yönelimli tasarıma bağlıdır. Sonraki birkaç bölümde, PHP'nin mevcut nesne desteğinin anlık görüntüsünü alacağım ve bazı tasarım konularını tanıtacağım.

**Kaynak:** “PHP 8 Objects, Patterns, and Practice”, Matt Zandstra