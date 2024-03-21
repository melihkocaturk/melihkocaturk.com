---
layout: post
title: PHP – Nesnelerin Temelleri
---

Nesneler ve sınıflar bu kitabın merkezindedir ve on yıl önce PHP 5'in piyasaya sürülmesinden bu yana PHP'nin de kalbinde yer alıyor. Bu bölümde, PHP'nin nesne yönelimli özelliklerini inceleyerek nesneleri ve tasarımı daha derinlemesine ele almak için temel oluşturuyorum. Nesne yönelimli programlamada yeniyseniz, bu bölümü dikkatlice okumalısınız.

Bu bölüm aşağıdaki konuları kapsayacaktır:

- Sınıflar ve nesneler: Sınıfları tanımlamak ve nesneleri başlatmak
- Kurucu metotlar: Nesnelerin kurulumunu otomatikleştirmek
- Primitif ve sınıf tipleri: Tipler neden önemlidir?
- Kalıtım: Neden kalıtıma ihtiyacımız var ve onu nasıl kullanacağız?
- Görünürlük: Nesne arayüzünü düzene sokmak

## Sınıflar ve Nesneler

Nesne yönelimli programlamayı anlamanın önündeki ilk engel, sınıf ve nesne arasındaki garip ve harika ilişkidir. Pek çok insan için, aydınlanmayı temsil eden bu ilişkidir. O halde temel bilgileri gözden kaçırmayalım.

**İlk Sınıf**

Sınıflar genellikle nesneler açısından tanımlanır. Bu ilginç çünkü nesneler genellikle sınıflar olarak tanımlanır. Bu döngüsellik, nesne yönelimli programlamanın ilk adımlarını zorlaştırabilir. Nesneleri şekillendiren sınıflar olduğu için, bir sınıf tanımlayarak başlamalıyız.

Kısacası, bir sınıf, bir veya daha fazla nesne oluşturmak için kullanılan bir kod şablonudur. class anahtar sözcüğü ve sınıf adıyla bir sınıf bildirirsiniz. Sınıf adları bir sayı ile başlayamaz, fakat sayı ve harflerin herhangi bir bileşimi olabilir. Alt çizgi karakteri de içerebilirler. Sınıfla ilişkilendirilen kod parantez içine alınmalıdır. Bir sınıf oluşturmak için bu öğeleri birleştiriyorum:

```
class ShopProduct
{
    // class body
}
```

Örnekteki ShopProduct sınıfı, henüz çok kullanışlı olmasa da kurala uygun bir sınıftır. Yine de oldukça önemli bir şey yaptım. Bir tür tanımladım; yani kullanabileceğim bir veri kategorisi oluşturdum. Bölüm boyunca bunun gücü daha net hale gelecektir.

**İlk Nesne (veya İki)**

Sınıf, nesneleri oluşturmak için bir şablonsa, nesnenin, sınıfta tanımlanan şablona göre yapılandırılmış veriler olduğu sonucu çıkar. Nesne, sınıfının örneğidir. Sınıf tarafından tanımlanan tiptedir.

ShopProduct nesnelerini oluşturmak için ShopProduct sınıfını kalıp olarak kullanıyorum. Bunu yapmak için new operatörüne ihtiyacım var. new operatörü, sınıfın adıyla birlikte kullanılır:

```
$product1 = new ShopProduct();
$product2 = new ShopProduct();
```

new operatörü, tek operant olarak sınıf adıyla çağrılır ve o sınıfın örneğini döndürür; yukarıda ShopProduct nesnesini oluşturur.

ShopProduct sınıfını şablon olarak kullanarak, iki ShopProduct nesnesi oluşturdum. $product1 ve $product2 işlevsel olarak aynı olmalarına rağmen, tek bir sınıftan türetilmiş, aynı türden farklı nesnelerdir.

Hala kafanız karışıksa, şu benzetmeyi deneyin. Sınıfı, plastik ördek yapan bir makinede kalıp olarak düşünün. Nesnelerimiz, bu makinenin ürettiği ördekler. Üretilen şeyin türü, preslendiği kalıba göre belirlenir. Ördekler her yönden aynı görünürler, ancak farklı varlıklardır. Başka bir deyişle, aynı türün farklı örnekleridir. Ördeklerin kimliklerini kanıtlamak için kendi seri numaraları bile olabilir. PHP kodunda oluşturulan her nesneye ayrıca kendi benzersiz kimliği verilir. Bunu, $product1 ve $product2 nesnelerini yazdırarak gösterebilirim:

```
var_dump($product1);
var_dump($product2);
```

Bu fonksiyonları çalıştırmak aşağıdaki çıktıyı üretir:

```
object(popp\ch03\batch01\ShopProduct)#235 (0) {
}
object(popp\ch03\batch01\ShopProduct)#234 (0) {
}
```

**Not**: PHP'nin eski sürümlerinde (5.1 sürümüne kadar), bir nesneyi doğrudan yazdırabilirsiniz. Bu, nesneyi, nesnenin kimliğini içeren bir metne dönüştürür. PHP 5.2'den itibaren artık bu desteklenmiyor ve bir nesneyi metin olarak yazdırma girişimleri, nesnenin sınıfında __toString() adlı bir metot tanımlanmadıkça hataya neden oluyor. Metotlara daha sonra bakacağım ve Bölüm 4'te __toString()'i ele alacağım.

Nesneleri var_dump()'a ileterek, # işaretinden sonra nesnelerin kimliği de dahil olmak üzere yararlı bilgileri alıyorum.

Bu nesneleri daha ilginç hale getirmek için ShopProduct sınıfını, properties adı verilen özel veri alanlarını destekleyecek şekilde değiştirebilirim.

## Sınıf Özellikleri (Properties)

Sınıflar, property adı verilen özel değişkenleri tanımlayabilir. Üye değişken olarak da bilinen property, nesneden nesneye değişebilen verileri tutar. Dolayısıyla, ShopProduct nesneleri söz konusu olduğunda, örneğin başlık ve fiyat alanlarını değiştirmek isteyebilirsiniz.

Property, standart bir değişkene benzer, ancak bir property bildirirken, değişkenden önce görünürlük (visibility) anahtar sözcüğü kullanmalısınız. Bu, public, protected ve private olabilir ve erişilebilirliği belirler. Örneğin, public property’lere sınıf dışından erişilebilir ve private property’lere yalnızca sınıf içindeki koddan erişilebilir.

Anahtar kelimelere ve erişilebilirlik konusuna daha sonra döneceğim. Şimdilik, public anahtar sözcüğünü kullanarak bazı property’ler tanımlayacağım:

```
class ShopProduct
{
    public $title = "default product";
    public $producerMainName = "main name";
    public $producerFirstName = "first name";
    public $price = 0;
}
```

Gördüğünüz gibi, her birine varsayılan değer atayarak dört property oluşturdum. ShopProduct sınıfından örneklendirdiğim tüm nesneler artık varsayılan verilerle önceden doldurulacak. Her property bildirimindeki public anahtar sözcüğü, property’e nesne bağlamı dışından erişebilmemi sağlar.

“->” karakterini (nesne operatörü) kullanarak, property değişkenlerine nesne bazında erişebilirsiniz:

```
$product1 = new ShopProduct();
print $product1->title;
// default product
```

Property’ler public olarak tanımlandığından, sınıftaki herhangi bir varsayılan değeri değiştirerek - okuyabildiğiniz gibi - değer atayabilirsiniz:

```
$product1 = new ShopProduct();
$product2 = new ShopProduct();
$product1->title = "My Antonia";
$product2->title = "Catch 22";
```

ShopProduct sınıfında $title property’sini bildirerek ve değer atayarak, tüm ShopProduct nesnelerinin ilk oluşturulduğunda bu özelliğe sahip olmasını sağlayabilirsiniz. Bu sınıfı kullanan kodun, bu varsayıma dayalı olarak ShopProduct nesneleriyle çalışabileceği anlamına gelir. Yine de sıfırlayabildiğim için, $title'ın değeri nesneden nesneye değişebilir.

Aslında, PHP bizi sınıfın tüm property’lerini bildirmeye zorlamaz. Property’leri bir nesneye dinamik olarak ekleyebilirsiniz:

```
$product1->arbitraryAddition = "treehouse";
```

Ancak bu yöntem, nesne yönelimli programlamada iyi bir uygulama olarak kabul edilmez.

Property’leri dinamik olarak oluşturmak neden kötü bir uygulamadır? Bir sınıf oluşturduğunuzda, tür tanımlarsınız. Dünyaya, sınıfınızın (ve ondan örneklenen herhangi bir nesnenin) belirli bir alan ve metot kümesinden oluştuğunu bildirirsiniz. ShopProduct sınıfınızda $title property’si tanımlanmışsa ShopProduct nesneleriyle çalışan herhangi bir kod, $title property’sinin kullanılabilir olacağı varsayımıyla ilerleyebilir. Yine de, dinamik olarak ayarlanmış property’ler hakkında hiçbir garanti verilemez.

Nesnelerim bu aşamada hala hantal. Nesnenin özellikleriyle çalışmam gerektiğinde, bunu şu anda nesnenin dışında yapmalıyım. Birden çok nesnede birden çok property ile çalışmak, kısa sürede angarya haline gelecektir:

```
$product1 = new ShopProduct();
$product1->title = "My Antonia";
$product1->producerMainName = "Cather";
$product1->producerFirstName = "Willa";
$product1->price = 5.99;
```

Tüm ürün ayrıntılarını set edene kadar varsayılan property değerlerini birer birer geçersiz kılıyorum. Artık bazı verileri set ettiğime göre, bunlara da erişebilirim:

```
print "author: {$product1->producerFirstName} "
    . "{$product1->producerMainName}\n";
// author: Willa Cather
```

Bu yaklaşımla ilgili bir dizi sorun vardır. PHP, property’leri dinamik olarak set etmenize izin verdiği için, property adını yanlış yazarsanız veya unutursanız uyarı almazsınız. Örneğin, şu satırı yazmak istediğimi varsayalım:

```
product1->producerMainName = "Cather";
```

Maalesef yanlışlıkla şöyle yazıyorum:

```
$product1->producerSecondName = "Cather";
```

PHP motoru söz konusu olduğunda, bu kod tamamen kurala uygundur ve sizi bu konuda uyarmaz. Yazarın adını yazdırmak istediğimde ise beklenmedik sonuçlarla karşılaşacağım.

Başka bir sorun da, sınıfımın fazla rahat olması. Başlık, fiyat veya yazar isimleri belirlemek zorunda değilim. Yazılımcı, bu property’lerin var olduğundan emin olabilir, ancak çoğu zaman varsayılan değerlerle karşılaşma olasılığı yüksektir. İdeal olarak, ShopProduct nesnesini başlatan herkesi anlamlı property değerleri belirlemeye teşvik etmek istiyorum.

Son olarak, muhtemelen oldukça sık yapmak isteyeceğim bir şeyi yapmak için çemberlerin içinden atlamam gerekiyor. Gördüğünüz gibi, yazar adının basılması yorucu bir süreçtir.

Nesnenin bu tür angarya işleri benim adıma halletmesi güzel olurdu.

Bu sorunlar, ShopProduct nesnesine, property verilerini nesne bağlamı içinde işlemek için kullanılabilecek kendi fonksiyon kümesi verilerek çözülebilir.

## Metotlarla Çalışmak

Property’ler, nesnelerinizin veri depolamasına izin verdiği gibi, metotlar da nesnelerinizin görevleri gerçekleştirmesine izin verir. Metotlar, bir sınıf içinde bildirilen özel fonksiyonlardır. Tahmin edebileceğiniz gibi, metot bildirimi fonksiyon bildirimine benzer. function anahtar sözcüğü, metot adından önce gelir, ardından parantez içinde isteğe bağlı parametre listesi gelir. Metot gövdesi parantez içine alınır:

```
public function myMethod($argument, $another)
{
    // ... 
}
```

Fonksiyonlardan farklı olarak, metotlar bir sınıfın gövdesinde bildirilmelidir. Ayrıca, görünürlük anahtar kelimesi de dahil olmak üzere bir dizi niteleyiciyi kabul edebilirler. Property gibi, metotlar da public, protected veya private olarak bildirilebilir. Metodu public olarak tanımlayarak, geçerli nesnenin dışından çağrılabilmesini sağlarsınız. Metot bildiriminde görünürlük anahtar sözcüğünü atlarsanız, dolaylı olarak public olarak tanımlanır. Bununla birlikte, tüm metotlar için açıkça görünürlük tanımlamak iyi bir uygulama olarak kabul edilir.

**Not**: Kodlama stili standardı PSR-12, görünürlüğün tüm yöntemler için bildirilmesini gerektirir.

```
class ShopProduct
{
    public $title = "default product";
    public $producerMainName = "main name";
    public $producerFirstName = "first name";
    public $price = 0;

    public function getProducer()
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }
}
```

Çoğu durumda, nesne işleci -> ve metot adıyla bağlantılı bir nesne değişkeni kullanarak metotları çağırırsınız. Fonksiyon çağırıyormuşsunuz gibi (metoda herhangi bir parametre iletmiyor olsanız bile) metot çağrınızda parantez kullanmalısınız:

```
$product1 = new ShopProduct();
$product1->title = "My Antonia";
$product1->producerMainName = "Cather";
$product1->producerFirstName = "Willa";
$product1->price = 5.99;

print "author: {$product1->getProducer()}\n";
// author: Willa Cather
```

GetProducer() metodunu ShopProduct sınıfına ekliyorum. getProducer() metodunu public olarak tanımladığıma dikkat edin, bu onun sınıf dışından çağrılabileceği anlamına gelir.

Metot gövdesinde bir özellik tanıtıyorum. $this sözde değişkeni, sınıfın nesne örneğine atıfta bulunabileceği mekanizmadır. Bu kavramı anlamakta zorlanıyorsanız, $this ifadesini "geçerli örnek" ifadesiyle değiştirmeyi deneyin. Aşağıdaki ifadeyi göz önünde bulundurun:

```
$this->producerFirstName
```

Bu, şu anlama gelir:

*geçerli örneğin* $producerFirstName *property’si*

Böylece getProducer() metodu, $producerFirstName ve $producerMainName property’lerini birleştirir ve döndürür, bu da beni, tam yapımcı adını her alıntılamam gerektiğinde bu işi yapma zahmetinden kurtarır.

Sınıfı biraz geliştirdim. ShopProduct nesnesinin property’lerinin varsayılan değerlerinin değiştirilmesi için yazılımcıya güveniyorum. Bu iki yönden sorunludur. İlk olarak, ShopProduct nesnesini düzgün bir şekilde başlatmak beş satır alır ve hiçbir yazılımcı bunun için size teşekkür etmez. İkincisi, ShopProduct nesnesi başlatıldığında herhangi bir property’nin set edildiğinden emin olmanın hiçbir yolu yoktur.

İhtiyacım olan, nesne oluşturulduğunda otomatik olarak çağrılan bir metot.

## Kurucu (Constructor) Metot Oluşturmak

Bir nesne oluşturulduğunda kurucu metot çağrılır. Gerekli property’lere değer atamak, bir şeyleri ayarlamak ve gerekli ön işleri tamamlamak için kullanabilirsiniz.

**Not**: PHP 5'ten önceki sürümlerde, sınıfın adını alan bir kurucu metot vardı. Böylece ShopProduct sınıfı, kurucu olarak ShopProduct() metodunu kullanır. Bu, PHP 7'den itibaren kullanımdan kaldırılmıştır ve PHP 8'den itibaren artık çalışmamaktadır. Kurucu metodunuzu  __construct() olarak adlandırın.

Metot adının iki alt çizgi karakteriyle başladığını unutmayın. PHP sınıflarındaki diğer birçok özel metot için bu adlandırma kuralının kullanıldığını göreceksiniz. ShopProduct sınıfı için bir kurucu tanımlıyorum:

**Not**: Bu şekilde başlayan metotlar, belirli durumlarda otomatik olarak çağrıldıklarından sihirli metotlar olarak ta bilinirler. Bunlar hakkında daha fazla bilgiyi www.php.net/manual/en/language.oop5.magic.php adresinde bulabilirsiniz. Çift alt çizgi belirli bir çağrışıma sahip olduğu için, bunları kendi özel metotlarınızda kullanmamak iyi bir fikirdir.

```
class ShopProduct
{
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price = 0;

    public function __construct(
        $title,
        $firstName,
        $mainName,
        $price
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
    }

    public function getProducer()
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }
}
```

Bir kez daha sınıfımı geliştiriyorum, koddan ve tekrardan tasarruf ediyorum. __construct() metodu, new operatörü kullanılarak nesne oluşturulduğunda çağrılır:

```
$product1 = new ShopProduct(
    "My Antonia",
    "Willa",
    "Cather",
    5.99
);

print "author: {$product1->getProducer()}\n";
// author: Willa Cather
```

Verilen parametreler kurucuya iletilir. Kurucu metot, nesnenin property’lerine değer atamak için $this sözde değişkenini kullanır.

**Not**: ShopProduct nesnesinin örneğini oluşturmak artık daha kolay ve kullanımı daha güvenli. Örneklendirme ve kurulum tek bir ifadede tamamlanıyor. ShopProduct nesnesini kullanan herhangi bir kod, tüm property’lere değer atandığından makul ölçüde emin olabilir.

**Yeni Kurucu Metot Sözdizimi**

ShopProduct sınıfını daha güvenli ve daha pratik hale getirdik. Sınıfa bir göz atın. Dört property’e sahip bir nesneyi örneklendirmek için, property’leri tanımlıyoruz, verileri tutmak için kurucu metot parametrelerini tanımlıyoruz ve ardından, property’lere metot parametrelerini atayarak hepsini bir araya getiriyoruz. PHP 8, yeni bir kurucu metot kısayolu sağlar. Kurucu metot parametrelerinize görünürlük anahtar sözcüğü ekleyerek, bunları property bildirimleriyle birleştirebilir ve aynı anda atayabilirsiniz. İşte ShopProduct'ın yeni bir versiyonu:

```
class ShopProduct
{
    public function __construct(
        public $title,
        public $producerFirstName,
        public $producerMainName,
        public $price
    ) {
    }

    public function getProducer()
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }
}
```

Kurucu metottaki property’lerin hem bildirimi hem de ataması dolaylı olarak işlenir. Bu tekrarı ve hata yapma olasılığını da azaltır. Sınıfı daha kompakt hale getirerek, kaynak kodu okuyanların mantığa odaklanmasını kolaylaştırır.

Öngörülebilirlik, nesne yönelimli programlamanın önemli bir yönüdür. Sınıflarınızı, nesneleri  kullananların özelliklerinden emin olabileceği şekilde tasarlamalısınız. Nesneyi güvenli hale getirmenin bir yolu, property’lerinde tuttuğu veri türlerini tahmin edilebilir kılmaktır. Örneğin, $name property’sinin her zaman karakterlerden oluşması sağlanabilir. Ancak property verileri sınıfın dışından alınıyorsa bunu nasıl başarabilirsiniz? Bir sonraki bölümde, metot bildirimlerinde nesne türlerini zorlamak için kullanabileceğiniz bir mekanizmayı inceliyorum.

## Varsayılan Parametreler ve Adlandırılmış Parametreler

Zamanla, metot parametre listesi uzun ve hantal hale gelebilir. Bu, metotların gerektirdiği parametreleri takip etmeyi zorlaştırır. Metot tanımlarında varsayılan değerler kullanarak yazılımcı için işleri kolaylaştırabiliriz. Örneğin, ShopProduct nesnemiz için başlığa ihtiyacımız olduğunu, ancak üretici adları için boş string değerlerini ve fiyat için sıfır değerini kabul ettiğimizi varsayalım. Çağıran kodun tüm bu verileri sağlaması gerekir:

```
$product1 = new ShopProduct("Shop Catalogue", "", "", 0);
```

Parametrelerimiz için varsayılan değerler atayarak örneklemeyi kolaylaştırabiliriz:

```
class ShopProduct
{
    public function __construct(
        public $title,
        public $producerFirstName = "",
        public $producerMainName = "",
        public $price = 0
    ) {
    }

    // ...
}
```

Şimdi, kurucuya yapılan isteğin yalnızca bir değer belirtmesi gerekir: başlık.

```
$product1 = new ShopProduct("Shop Catalogue");
```

Varsayılan parametre değerleri, metotlarla çalışmayı daha kolay hale getirebilir, ancak çoğu zaman, istenmeyen komplikasyonlara da neden olabilir. Bir fiyat vermek istesem, ancak yine de isimlerin varsayılan değerlerinde kalmasını istersem, kurucu metot çağrıma ne olur? PHP 8'den önce takılıp kalırdım. Fiyatı belirtmek için boş isimler vermem gerekecek. Bu bizi başladığımız yere getiriyor. Ayrıca, kurucunun boş isimler için ne tür değerler beklediğini de bulmam gerekecek. Boş bir metin mi atamalıyım? Yoksa null mu olmalı? İşi kurtarmak şöyle dursun, varsayılan değerler kafa karışıklığına yol açmış olabilir.

Şanslıyız, PHP 8 adlandırılmış parametreleri sağlar. Metot çağrısında, artık iletmek istediğim değerin önünde parametre adını belirtebilirim. PHP değeri, - daha sonra çağıran koddaki sıra ne olursa olsun -  metot imzasındaki doğru parametre ile ilişkilendirir.

```
$product1 = new ShopProduct(
    price: 0.7,
    title: "Shop Catalogue"
);
```

Buradaki sözdizimine dikkat edin: PHP'ye $price parametresini 0.7'ye set etmek istediğimi, önce parametre adını, sonra iki nokta üst üste ve ardından değeri belirterek söylüyorum. Adlandırılmış parametreler kullandığım için artık sıraları önemli değil ve artık boş değerler vermem gerekmiyor.

## Parametreler ve Tipler

Tipler, verilerin yönetilme şeklini belirler. Örneğin, karakter verilerini görüntülemek için string türünü kullanırsınız ve bu tür verileri string fonksiyonlarıyla manipule edersiniz. Integer matematiksel ifadelerde, boolean test ifadelerinde vb. kullanılır. Bunlar ilkel (primitive) tipler olarak bilinir. Bu nedenle ShopProduct nesnesi, ilkel nesne tipine  aittir, ancak aynı zamanda sınıf tipine de aittir. Bu bölümde, sınıf metotlarıyla ilgili olarak her iki türe de bakacağım.

Metot ve fonksiyon tanımları, parametrenin belirli bir türde olmasını zorunlu olarak gerektirmez. Bu hem bir lanet hem de bir nimettir. Parametrenin herhangi bir türden olabileceği gerçeği size esneklik sunar. İşlevselliği değişen koşullara uyarlayarak farklı veri türlerine akıllıca yanıt veren metotlar oluşturabilirsiniz. Bu esneklik, belirsizliğe neden olabilir.

### İlkel Tipler

PHP gevşek (loosely typed) yazılmış bir dildir. Bu, bir değişkenin belirli bir veri tipini tutması için tanımlanmasına gerek olmadığı anlamına gelir. $number değişkeni 2 ve "two" değerini aynı kapsamda tutabilir. C veya Java gibi sıkı tip denetimi olan (strongly typed) dillerde, bir değer atamadan önce değişkenin türünü bildirmelisiniz ve tabii ki değer belirtilen türde olmalıdır.

Bu, PHP'nin tip kavramı olmadığı anlamına gelmez. Değişkene atanabilen her değerin bir tipi vardır. PHP'nin tip kontrol işlevlerinden birini kullanarak değişkenin değerinin tipini belirleyebilirsiniz. Aşağıdaki tablo, PHP'de tanınan ilkel türleri ve bunlara karşılık gelen test fonksiyonlarını listeler. Her fonksiyon bir değişkeni veya değeri kabul eder ve bu parametre  ilgili türdeyse true döndürür.

**Tablo 3-1**. PHP'de İlkel Tipler ve Kontrol Fonksiyonları

| Fonksiyon | Tip | Açıklama |
| --- | --- | --- |
| is_bool() | Boolean | true veya false |
| is_integer() | Integer | Tam sayı. is_int() ve is_long()’un takma adı |
| is_float() | Float | Kayan noktalı sayı. is_double()’ın takma adı |
| is_string() | String | Karakter verileri |
| is_object() | Object | Nesne |
| is_resource() | Resource | Veritabanları veya dosyalar gibi dış kaynakları tanımlamak ve bunlarla çalışmak için kullanılan veri tipi |
| is_array() | Array | Dizi |
| is_null() | Null | Atanmamış değer |

Özellikle metot ve fonksiyon parametreleriyle çalışırken değişkenin türünü kontrol etmek önemli olabilir.

**İlkel Tipler: Bir Örnek**

Kodunuzu yazarken dikkatli olmalısınız. İşte karşılaşabileceğiniz tiple ilgili birçok sorundan birine örnek.

Yapılandırma ayarlarının bir XML dosyasında tutulduğunu hayal edin. `<resolveddomains></resolveddomains>` XML öğesi, uygulamanıza IP adreslerini çözümlemeye çalışıp çalışmayacağını söyler; bu yararlı ancak maliyetli bir işlemdir.

```
<settings>
    <resolvedomains>false</resolvedomains>
</settings>
```

"false" dizesi, uygulamanız tarafından alınır ve IP adresi verilerini görüntüleyen outputAddresses() adlı bir metoda iletilir:

```
class AddressManager
{
    private $addresses = ["209.131.36.159", "216.58.213.174"];

    public function outputAddresses($resolve)
    {
        foreach ($this->addresses as $address) {
            print $address;
            if ($resolve) {
                print " (" . gethostbyaddr($address) . ")";
            }
            print "\n";
        }
    }
}
```

Elbette, AddressManager sınıfı biraz geliştirilebilir. Örneğin, IP adreslerini bir sınıfın içine kodlamak pek kullanışlı değildir. Yine de outputAddresses() metodu, döngü oluşturarak  her öğeyi yazdırır. $resolve parametresi true olarak çözümlenirse, metot alan adının yanı sıra IP adresini de verir.

İşte, AddressManager sınıfıyla birlikte XML yapılandırma öğesini kullanan bir yaklaşım. Kusurlu olduğunu tespit edip edemeyeceğinize bakın:

```
$settings = simplexml_load_file(__DIR__ . "/resolve.xml");
$manager = new AddressManager();
$manager->outputAddresses((string)$settings->resolvedomains);
```

Kod parçası, SimpleXML API'sini kullanır. Bu örnekte, değerin "false" olduğunu biliyorum ve SimpleXML belgelerinin yapmamı önerdiği gibi onu string’e dönüştürüyorum.

Bu kod beklediğiniz gibi davranmayacaktır. "false" dizesini outputAddresses() metoduna iletirken, metodun parametre hakkında yaptığı örtük varsayımı yanlış anlıyorum. Metot bir boolean değer (yani, true veya false) bekliyor. "false" dizesi aslında true olarak yorumlanır. Bunun nedeni, PHP'nin sizin için boş olmayan bir string değerini boolean tipine çevirecek olmasıdır. Bu kod üzerinde düşünün:

```
if ("false") {
    // ...
}
```

Aslında şuna eşdeğerdir:

```
if (true) {
    // ...
}
```

Bunu düzeltmek için uygulayabileceğiniz birkaç yaklaşım var.

outputAddresses() matodunu daha töleranslı hale getirebilirsiniz, böylece dizeyi tanır ve onu boolean eşdeğerine dönüştürmek için bazı temel kurallar uygular:

```
public function outputAddresses($resolve)
{
    if (is_string($resolve)) {
        $resolve = (preg_match("/^(false|no|off)$/i", $resolve)) ? false : true;
    }
    // ...
}
```

Bununla birlikte, böyle bir yaklaşımdan kaçınmak gerekir. Genel olarak konuşursak, metot veya fonksiyon için net ve katı bir arayüz sağlamak, belirsiz bir arayüz sunmaktan daha iyidir. Belirsiz metot ve fonksiyonlar kafa karışıklığına neden olabilir ve bu nedenle bugları besleyebilir.

Başka bir yaklaşım benimseyebilirsiniz: outputAddresses() metodunu olduğu gibi bırakın ve $resolve parametresinin boolean değer içermesi gerektiğine dair açık talimatlar içeren bir yorum ekleyin. Bu yaklaşım, temel olarak yazılımcıya küçük yazıları okumasını söyler:

```
/**
* Outputs the list of addresses.
* If $resolve is true then each address will be resolved
* @param $resolve boolean Resolve the address?
*/
public function outputAddresses($resolve)
{
    // ...
}
```

Bu, yazılımcının belgeleri özenle okuduğu (veya bu tür ek açıklamaları tanıyan akıllı editörler kullandıkları) varsayıldığında makul bir yaklaşımdır.

Son olarak, outputAddresses() metodunu veri tipi konusunda katı hale getirebilirsiniz. Boolean gibi ilkel türler için, PHP 7'nin yayınlanmasından önce bunu yapmanın gerçekten tek bir yolu vardı. Gelen verileri incelemek için kod yazmanız ve gerekli tiple eşleşmiyorsa bir şeyler yapmanız gerekir:

```
public function outputAddresses($resolve)
{
    if (! is_bool($resolve)) {
        // do something drastic
    }
}
```

Bu yaklaşım, doğru veri türünü sağlamaya zorlamak veya bir uyarı vermek için kullanılabilir.

**Not**: Sonraki bölüm olan "Tip Bildirimleri: Nesne Tipleri"nde, metotlara ve fonksiyonlara iletilen parametre tiplerini sınırlamanın çok daha iyi bir yolunu açıklayacağım.

String parametreyi dönüştürmek kolay olabilir, ancak muhtemelen başka sorunlara da yol açabilir. Bir dönüştürme mekanizması sağlarken, yazılımcının bağlamını ve amacını ikinci kez tahmin edersiniz. Öte yandan, boolean veri türünü zorlayarak, dizelerin boolean değerler ile eşleşip eşlemeyeceğine ve hangi sözcüğün true veya false olarak yorumlanacağına karar verme işini yazılımcıya bırakmış olursunuz. outputAddresses() metodu ise gerçekleştirmek üzere tasarlandığı göreve odaklanır. Belirli bir görevi daha geniş bağlamı göz ardı ederek gerçekleştirmeye yapılan bu vurgu, nesne yönelimli programlamada önemli bir ilkedir ve kitap boyunca buna sık sık döneceğim.

Aslında, parametre tipleri ile başa çıkma stratejileri bir yandan olası hataların ciddiyetine, diğer yandan esnekliğin faydalarına bağlı olacaktır. PHP, sizin için - içeriğe bağlı olarak - ilkel değerleri atar. Dizelerdeki sayılar, örneğin bir matematiksel ifadede kullanıldığında integer veya float eşdeğerlerine dönüştürülür. Dolayısıyla kodunuz doğal olarak tip hatalarına karşı töleranslı olabilir.

Bununla birlikte, genel olarak, hem nesne hem de ilkel tipler söz konusu olduğunda katı olmak en iyisidir. Şanslıyız ki, PHP 8, tip güvenliğini sağlamak için her zamankinden daha fazla araç sağlar.

### Diğer Bazı Tip Kontrol Fonksiyonları

İlkel tipleri kontrol eden fonksiyonları gördük. Değişkenlerimizin içeriğini kontrol ederken -  değişkende tutulan verilerin nasıl kullanılabileceği hakkında daha genel bilgi sağlamak için -  ilkel türleri kontrol etmenin ötesine geçen birkaç fonksiyondan bahsetmekte fayda var. Bunları Tablo 3-2'de listeliyorum.

**Tablo 3-2**. Sözde Tip Denetimi İşlevleri

| Fonksiyon | Açıklama |
| --- | --- |
| is_countable() | count() fonksiyonuna aktarılabilen dizi veya nesne |
| is_iterable() | foreach kullanılarak döngüye alınabilen bir veri yapısı |
| is_callable() | Çağrılabilen kod - genellikle anonim bir fonksiyon veya fonksiyon adı |
| is_numeric() | integer, long veya sayı olarak çözümlenebilen string |

Tablo 3-2'de açıklanan fonksiyonlar, belirli tipleri kontrol etmekten çok, test ettiğiniz değerleri nasıl ele alacağınızı kontrol eder. Örneğin, is_callable() değişken için true değerini döndürürse, ona bir fonksiyon veya metot gibi davranabileceğinizi ve onu çağırabileceğinizi bilirsiniz. Benzer şekilde, is_iterable() testinden geçen bir değeri - dizi yerine özel bir nesne türü olsa bile - döngüye sokabilirsiniz.

### Tip Bildirimleri: Nesne Tipleri

Tıpkı bir parametre değişkeninin herhangi bir ilkel türü içerebileceği gibi, varsayılan olarak herhangi bir türden nesneyi de içerebilir. Bu esnekliğin kullanım alanları vardır, ancak bir metot tanımı bağlamında sorunlar ortaya çıkarabilir.

ShopProduct nesnesiyle çalışmak üzere tasarlanmış bir metot düşünün:

```
class ShopProductWriter
{
    public function write($shopProduct)
    {
        $str = $shopProduct->title . ": "
            . $shopProduct->getProducer()
            . " (" . $shopProduct->price . ")\n";
        print $str;
    }
}
```

Bu sınıfı şu şekilde test edebilirsiniz:

```
$product1 = new ShopProduct("My Antonia", "Willa", "Cather", 5.99);
$writer = new ShopProductWriter();
$writer->write($product1);
// My Antonia: Willa Cather (5.99)
```

ShopProductWriter sınıfı tek bir metot içerir, write(). Metot, ShopProduct nesnesini kabul eder ve nesnenin özetini oluşturmak ve yazdırmak için property ve metotlarını kullanır. $shopProduct parametresinin adını - metodun beklediği parametreye uygun olarak - ShopProduct olarak kullandım, ancak bunu zorlamadım. Bu, beklenmeyen bir nesne veya ilkel bir tür iletilebileceğim anlamına gelir. O zamana kadar kod, kendisine gerçek bir ShopProduct nesnesinin iletildiği varsayımına göre hareket etmiş olabilir.

**Not**: write() metodunu neden doğrudan ShopProduct'a eklemediğimi merak edebilirsiniz. Nedeni sorumluluk alanlarında yatmaktadır. ShopProduct sınıfı, ürün verilerinin yönetiminden sorumludur; ShopProductWriter bunu yazdırmaktan sorumludur. Bu bölümü okurken, işbölümünün yararlarını görmeye başlayacaksınız.

Bu sorunu çözmek için PHP 5, sınıf tipi bildirimlerini tanıttı. Metot parametresine sınıf tipi bildirimi eklemek için, sınırlandırmanız gereken parametrenin önüne sınıf adını koymanız yeterlidir. write() metodunu şu şekilde değiştirebilirim:

```
public function write(ShopProduct $shopProduct)
{
    // ...
}
```

Artık write() metodu, $shopProduct parametresini yalnızca ShopProduct türünde bir nesne içeriyorsa kabul edecektir.

İşte basit bir sınıf:

```
class Wrong
{
}
```

Ve burada write() metodunu Wrong nesnesiyle çağırmaya çalışıyoruz:

```
$writer = new ShopProductWriter();
$writer->write(new Wrong());
```

write() metoduna Wrong nesnesini iletmek önemli bir hataya neden olur.

```
TypeError: popp\ch03\batch08\ShopProductWriter::write(): Argument #1
($shopProduct) must be of type
popp\ch03\batch04\ShopProduct, popp\ch03\batch08\Wrong given, called in
/var/popp/src/ch03/batch08/Runner.php on ...
```

**Not**: TypeError çıktısının çok fazla ek bilgi içerdiğini fark etmiş olabilirsiniz. Örneğin, Wrong sınıfı popp\ch03\batch08\Wrong şeklinde belirtilir. Bunlar namespace (isim alanı) örnekleridir ve bunları Bölüm 4'te ayrıntılı olarak göreceksiniz.

Bu, beni parametrenin türünü test etme zorunluluğundan kurtarıyor. Ayrıca yazılımcı için metot imzasını çok daha net hale getirir. Bir bakışta write() metodunun gereksinimlerini görebilir. Deklarasyon katı bir şekilde uygulandığından, tip hatasından kaynaklanan bazı belirsiz bug’lar hakkında endişelenmeye gerek yoktur.

Otomatikleştirilmiş tip denetimi, hataları önlemenin harika bir yolu olsa da, tip bildirimlerinin çalışma zamanında denetlendiğini anlamak önemlidir. Bu, yalnızca metoda istenmeyen bir nesne gönderildiği anda hata oluştuğu anlamına gelir. write() çağrısı, yalnızca Noel sabahı çalışan koşullu bir ifadeye gömülürse ve kodunuzu dikkatli bir şekilde kontrol etmediyseniz, kendinizi tatilde çalışırken bulabilirsiniz.

### Tip Bildirimleri: İlkel Tipler

PHP 7'nin piyasaya sürülmesine kadar, yalnızca nesneleri ve diğer birkaç türü (callable ve array) kısıtlamak mümkündü. PHP 7 sonunda skaler tip bildirimlerini tanıttı. Bu, parametre listenizde boolean, string, integer ve float türlerini kullanmanıza olanak tanır.

Skaler tip bildirimleriyle donanmış olarak, ShopProduct sınıfına bazı kısıtlamalar ekleyebilirim:

```
class ShopProduct
{
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price = 0;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
    }
    // ...
}
```

Kurucu metot bu şekilde desteklendiğinde, $title, $firstName ve $mainName parametrelerinin her zaman string ve $price öğesinin float veri içerdiğinden emin olabilirim. Bunu, ShopProduct'ı yanlış bilgilerle başlatarak gösterebilirim:

```
$product = new ShopProduct("title", "first", "main", []);
```

ShopProduct nesnesini başlatmaya çalışıyorum. Kurucuya üç dize iletiyorum, ancak sonuncu da gerekli değişken yerine boş bir dizi ileterek başarısız oluyorum. Tip bildirimleri sayesinde, PHP bundan kurtulmama izin vermiyor:

```
TypeError: popp\ch03\batch09\ShopProduct:: construct(): Argument #4
($price) must be of type float, array given, called in...
```

Varsayılan olarak PHP, mümkün olduğunda, parametreleri gerekli tipe dolaylı olarak dönüştürür. Bu, daha önce karşılaştığımız güvenlik ve esneklik arasındaki ikileme bir örnektir. Örneğin, ShopProduct sınıfının yeni uygulaması, bizim için sessizce string’i float’a çevirecek. Dolayısıyla, bu örnekleme başarısız olmaz:

```
$product = new ShopProduct("title", "first", "main", "4.22");
```

Perde arkasında, string "4.22" float 4.22 olur. Şimdiye kadar, çok faydalı görünüyor. Ancak, AddressManager sınıfında karşılaştığımız sorunu tekrar düşünün. "false" dizesi sessizce true boolean'a çözümleniyordu. Varsayılan olarak, AddressManager::outputAddresses() metodunda şuna benzer bir bool tip bildirimi kullanırsam aynı şey olur:

```
public function outputAddresses(bool $resolve)
{
    // ...
}
```

Şimdi şöyle bir metot çağrısını düşünün:

```
$manager->outputAddresses("false");
```

Örtülü atama nedeniyle, işlevsel olarak aynıdır. Yalnızca dosya bazında olmasına rağmen, skaler tip bildirimlerini katı hale geirebilirsiniz. Burada katı tip bildirimlerini açıyorum ve outputAddresses()'i bir kez daha string ile çağırıyorum:

```
declare(strict_types=1);

$manager->outputAddresses("false");
```

Bu çağrı TypeError hatasına neden olur:

```
TypeError: popp\ch03\batch09\AddressManager::outputAddresses(): Argument #1
($resolve) must be of type bool, string given, called in...
```

**Not**: strict_types bildirimi, fonksiyonun veya metodun uygulandığı dosyaya değil, çağrının yapıldığı dosyaya uygulanır. Bu nedenle, katılığı uygulamak yazılımcıya bağlıdır.

İsteğe bağlı bir parametre yapmanız gerekebilir, ancak yine de onun türünü kısıtlayın. Bunu varsayılan değer atayarak yapabilirsiniz:

```
class ConfReader
{
    public function getValues(array $default = [])
    {
        $values = [];
        // do something to get values
        // merge the provided defaults (it will always be an array)
        $values = array_merge($default, $values);
        return $values;
    }
}
```

### Mixed Tipler

PHP 8.0 ile tanıtılan mixed tip bildirimi, sentetik şeker olarak görülebilir, yani kendi başına pek bir şey yapmaz. Şunlar arasında işlevsel bir fark yoktur:

```
class Storage
{
    public function add(string $key, $value)
    {
        // do something with $key and $value
    }
}
```

```
class Storage
{
    public function add(string $key, mixed $value)
    {
        // do something with $key and $value
    }
}
```

İkinci versiyonda, add() için $value parametresinin mixed, başka bir deyişle array, bool, callable, int, float, null, object, resource veya string herhangi bir türü kabul edeceğini bildirdim. Bu nedenle, mixed $value bildirmek, parametre listesinde tip bildirimi olmadan $value kullanmakla aynıdır. Öyleyse neden mixed bildirimle uğraşıyorsunuz? Özünde, parametrenin kasıtlı olarak herhangi bir değeri kabul ettiğini beyan ediyorsunuz. Çıplak bir parametre, herhangi bir değeri kabul etmeye yönelik olabilir - veya yazılımcı tembel olduğu için tip bildirimi olmadan bırakılmış olabilir. Mixed şüphe ve belirsizliği ortadan kaldırır ve bu nedenle yararlıdır.

Özetlemek gerekirse, Tablo 3-3'te PHP tarafından desteklenen tip bildirimlerini listeliyorum.

**Tablo 3-3**. Tip Bildirimleri

| Tip Bildirimi | PHP Versiyon | Tanım |
| --- | --- | --- |
| array | 5.1 | Varsayılan olarak null veya bir dizi olabilir |
| int | 7.0 | Varsayılan olarak null veya bir tamsayı olabilir |
| float | 7.0 | Kayan noktalı sayı. Katı mod etkinleştirilmiş olsa bile tamsayı kabul edilecektir. Varsayılan olarak null, float veya integer olabilir |
| callable | 5.4 | Çağrılabilir kod (anonim bir fonksiyon gibi). Varsayılan olarak null olabilir |
| bool | 7.0 | Varsayılan olarak null veya boolean olabilir |
| string | 5.0 | Varsayılan olarak null veya dize olabilir |
| self | 5.0 | İçinde bulunduğu sınıfa başvuru |
| [a class type] | 5.0 | Sınıf veya arayüz tipi. Varsayılan olarak null olabilir |
| iterable | 7.1 | foreach ile gezilebilir (dizi olması gerekmez - Traversable uygulanabilir) |
| object | 7.2 | Nesne |
| mixed | 8.0 | Değerin herhangi bir türde olabileceğine dair açık bildirim |

### Birleşik (Union) Tipler

Mixed ile tip bildirimlerinin göreli katılığı arasında oldukça büyük bir uçurum vardır. Bir parametreyi iki, üç veya daha fazla tiple sınırlamanız gerekirse ne yaparsınız? PHP 8'e kadar, bunu başarabilmenin tek yolu, metot gövdesi içinde türü test etmekti. Storage sınıfına yeni bir gereksinimle dönelim. add(), yalnızca string veya boolean değer kabul etmelidir. İşte metot gövdesi içinde tipi kontrol eden bir uygulama:

```
class Storage
{
    public function add(string $key, $value)
    {
        if (! is_bool($value) && ! is_string($value)) {
            error_log("value must be string or Boolean - given: " .
            gettype($value));
            return false;
        }
        // do something with $key and $value
    }
}
```

**Not**: Aslında, false döndürmek yerine büyük olasılıkla exception fırlatırdık. Bölüm 4'te istisnalar hakkında daha fazla bilgi edinebilirsiniz.

Bu manuel kontrol işimizi görsede, hantal ve okunması zor. Şanslıyız ki, PHP 8 yeni bir özellik getirdi: birleşik tip bildirimi yapmak için pipe sembolüyle ayrılmış iki veya daha fazla tipi birleştirmenize izin veren union type. İşte benim Storage'i yeniden uygulamam:

```
class Storage
{
    public function add(string $key, string|bool $value)
    {
        // do something with $key and $value
    }
}
```

Şimdi $value parametresine float veya boolean dışında herhangi bir şey atamaya çalışırsam, artık tanıdık bir TypeError alacağım.

add() metodunu biraz daha toleranslı yapmak istersem, null değerine izin vermek için birleşik tip kullanabilirim.

```
class Storage
{
    public function add(string $key, string|bool|null $value)
    {
        // do something with $key and $value
    }
}
```

Birleşik tip bildirimleri, nesne tipi bildirimleriyle aynı şekilde çalışacaktır. Bu örnek, ShopProduct tipinde bir nesneyi veya null değeri kabul eder:

```
public function setShopProduct(ShopProduct|null $product)
{
    // do something with $product
}
```

Birçok metot false'ı alternatif bir değer olarak kabul ettiğinden veya döndürdüğünden, PHP 8 birleşik tipler bağlamında false sözde türünü destekler. Dolayısıyla, bu örnekte, ShopProduct nesnesini veya false'ı kabul edeceğim:

```
public function setShopProduct2(ShopProduct|false $product)
{
    // do something with $product
}
```

Bu, `ShopProduct|bool` birleşiminden daha kullanışlı çünkü hiçbir senaryoda true kabul etmek istemiyorum.

**Not**: Birleşik tipler PHP 8'de eklenmiştir.

### Null Yapılabilir Tipler

Birleşik tipin null değerini iki seçenekten biri olarak kabul ettiği durumlarda, kullanabileceğiniz eşdeğer bir parametre vardır. Null yapılabilir tip, soru işaretiyle başlayan bir tip bildiriminden oluşur. Storage'in bu sürümü string veya null kabul eder:

```
class Storage
{
    public function add(string $key, ?string $value)
    {
        // do something with $key and $value
    }
}
```

Sınıf tipi bildirimlerini açıkladığımda, türlerin ve sınıfların eşanlamlı olduğunu ima ettim. Ancak ikisi arasında önemli bir fark vardır. Bir sınıf tanımladığınızda, tür de tanımlarsınız, ancak bir tür, tüm sınıf ailesini tanımlayabilir. Farklı sınıfların bir tür altında gruplandırılabileceği mekanizmaya kalıtım denir. Kalıtımı bir sonraki bölümde ele alacağım.

## Dönüş Tipi Bildirimi

Bir parametrenin tipini bildirebileceğimiz gibi, metotlarımızın döndürdüğü tipleri sınırlamak için dönüş tipi bildirimlerini kullanabiliriz. Doğrudan bir metodun veya fonksiyonun kapanış parantezinden sonra iki nokta üst üste eklenir ve onu dönüş tipi bildirimi takip eder. Dönüş tipi bildirilirken, parametre tiplerinde olduğu gibi aynı türler desteklenir. Burada getPlayLength() dönüş tipini kısıtlıyorum:

```
public function getPlayLength(): int
{
    return $this->playLength;
}
```

Bu metot çağrıldığında tamsayı değeri döndüremezse, PHP bir hata üretir:

```
TypeError: popp\ch03\batch15\CdProduct::getPlayLength(): Return value must be of type int, none returned
```

Dönüş değeri bu şekilde zorlandığından, metodu çağıran herhangi bir kod, dönüş değerinin tamsayı olmasına güvenebilir.

Dönüş tipi bildirimleri, null yapılabilir ve birleşik türleri destekler:

```
public function getPrice(): int|float
{
    return ($this->price - $this->discount);
}
```

PHP 8'den itibaren desteklenen void dönüş tipi bildirimi ile bir metodun asla değer döndürmeyeceğini beyan edebilirsiniz. Örneğin, setDiscount() metodu bir değer sağlamak yerine set etmek için tasarlandığından, burada void dönüş tipi bildirimini kullanıyorum:

```
public function setDiscount(int|float $num): void
{
    $this->discount = $num;
}
```

## Kalıtım

Kalıtım, temel bir sınıftan bir veya daha fazla sınıfın türetilebileceği anlamına gelir.

Bir başka sınıftan miras alan sınıfın onun alt sınıfı (subclass) olduğu söylenir. Bu ilişki genellikle ebeveynler ve çocuklar açısından tanımlanır. Bir alt sınıf (child), ebeveynden (parent) türetilir ve özelliklerini miras alır. Bu özellikler property ve metotlardan oluşur. Alt sınıf, tipik olarak ebeveyni (superclass olarak ta bilinir) tarafından sağlananlara yeni işlevler ekler; bu nedenle alt sınıfın ebeveynini genişlettiği söylenir.

Kalıtımın söz dizimine girmeden önce, onun çözmenize yardımcı olabileceği sorunları inceleyeceğim.

### Kalıtım Problemi

ShopProduct sınıfına tekrar bakalım. Şu anda, oldukça genel. Her türlü ürün için kullanılabilir:

```
$product1 = new ShopProduct("My Antonia", "Willa", "Cather", 5.99);
$product2 = new ShopProduct(
    "Exile on Coldharbour Lane", "The",
    "Alabama 3",
    10.99
);
print "author: " . $product1->getProducer() . "\n";
print "artist: " . $product2->getProducer() . "\n";

// author: Willa Cather
// artist: The Alabama 3
```

Yapımcı adını ikiye ayırmak hem kitaplar hem de CD'ler için uygundur. "The" ve "Willa"ya göre değil, "Alabama 3" ve "Cather"e göre sıralama yapabilmek istiyorum. Tembellik mükemmel bir tasarım stratejisidir, dolayısıyla bu aşamada ShopProduct'ın birden fazla ürün türü için kullanma konusunda endişelenmenize gerek yoktur.

Bununla birlikte, örneğime bazı yeni gereksinimler eklersem, işler hızla daha karmaşık hale gelebilir. Örneğin, kitaplara ve CD'lere özgü verileri temsil etmeniz gerektiğini düşünün. CD'ler için toplam çalma süresini kaydetmelisiniz; kitaplar için toplam sayfa sayısı. Daha başka farklar da olabilir, ancak bu, konuyu açıklamaya hizmet edecektir.

Bu değişiklikleri karşılamak için örneğimi nasıl genişletebilirim? İki seçenek hemen kendini gösterir. İlk olarak, tüm verileri ShopProduct sınıfına atabilirim. İkincisi, ShopProduct'ı iki ayrı sınıfa ayırabilirim.

İlk yaklaşımı inceleyelim. Burada, CD ve kitapla ilgili verileri tek bir sınıfta birleştiriyorum:

```
class ShopProduct
{
    public $numPages;
    public $playLength;
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price,
        int $numPages = 0,
        int $playLength = 0
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
        $this->numPages = $numPages;
        $this->playLength = $playLength;
    }

    public function getNumberOfPages(): int
    {
        return $this->numPages;
    }

    public function getPlayLength(): int
    {
        return $this->playLength;
    }

    public function getProducer(): string
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }
}
```

$numPages ve $playLength property’lerine metot erişimi sağladım. Bu sınıftan örneklenen nesne, fazladan bir metot içerecektir ve bir CD için, gereksiz bir kurucu metot parametresi kullanılarak örneklendirilmelidir: CD, kitap sayfalarıyla ilgili bilgileri depolayacak ve kitap oynatma uzunluğu verisini destekleyecektir. Bu muhtemelen şu anda birlikte yaşayabileceğiniz bir şey. Ancak, her biri kendine ait metotları olan ürün türleri eklemeye devam edersem ne olur? Sınıfımız giderek daha karmaşık ve yönetilmesi zor hale gelir.

Bu nedenle, tek bir sınıfa ait olmayan alanları zorlamak, gereksiz property ve metotlarla şişirilmiş nesnelere yol açar.

Sorun verilerle de bitmiyor. İşlevsellik konusunda da zorluklarla karşılaşıyorum. Ürünü özetleyen bir metot düşünün. Satış departmanı, faturalarda kullanılmak üzere net bir özet satırı talep etti. CD'ler için çalma süresi ve kitaplar için sayfa sayısı eklememi istiyorlar, bu yüzden her tür için farklı uygulamalar sağlamak zorunda kalacağım. Nesnenin formatını takip etmek için flag kullanmayı deneyebilirim.

```
public function getSummaryLine(): string
{
    $base = "{$this->title} ( {$this->producerMainName}, ";
    $base .= "{$this->producerFirstName} )";

    if ($this->type == 'book') {
        $base .= ": page count - {$this->numPages}";
    } elseif ($this->type == 'cd') {
        $base .= ": playing time - {$this->playLength}";
    }

    return $base;
}
```

$type’ı ayarlamak için, $numPages parametresini kurucu metodta test edebilirim. Yine de ShopProduct sınıfı bir kez daha gereğinden fazla karmaşık hale geldi. Daha fazla farklılık  veya yeni formatlar eklendikçe, bu işlevsel farklılıkları yönetmek daha da zorlaşacak. Belki de bu sorun için başka bir yaklaşım denemeliyim.

ShopProduct bir arada iki sınıf gibi hissettirmeye başladığından, iki farklı tür oluşturabilirim:

```
class CdProduct
{
    public $playLength;
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price,
        int $playLength
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
        $this->playLength = $playLength;
    }

    public function getPlayLength(): int
    {
        return $this->playLength;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        $base .= ": playing time - {$this->playLength}";
        return $base;
    }

    public function getProducer(): string
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }
}
```

```
class BookProduct
{
    public $numPages;
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price,
        int $numPages
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
        $this->numPages = $numPages;
    }

    public function getNumberOfPages(): int
    {
        return $this->numPages;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        $base .= ": page count - {$this->numPages}";
        return $base;
    }

    public function getProducer(): string
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }
}
```

Karmaşıklık sorununu ele aldım, ancak bunun bir bedeli var. Artık flag kullanmadan her format için bir getSummaryLine() metodu oluşturabilirim. Hiçbir sınıf, kendisiyle ilgili olmayan alanları veya metotları tutmaz.

Maliyet çoğaltmada yatmaktadır. getProducerName() metodu her sınıfta tamamen aynıdır. Her kurucu metot, aynı şekilde bir dizi özdeş property ayarlar.

GetProducer() metodunun her sınıf için aynı şekilde davranması gerekiyorsa, bir uygulamada yaptığım herhangi bir değişikliği diğeri içinde yapmam gerekir. Umursamazsanız, sınıflar kısa sürede senkronizasyondan çıkar.

Yinelemeyi sürdürebileceğimden emin olsam bile endişelerim bitmedi. Artık bir yerine iki türüm var.

ShopProductWriter sınıfını hatırlıyor musunuz? write() metodu, tek bir türle çalışacak şekilde tasarlanmıştır: ShopProduct. Bunu eskisi gibi çalışacak şekilde değiştirebilirim? Sınıf türü bildirimini metot imzasından kaldırabilirim, ancak o zaman doğru türde nesne iletildiği konusunda şansa güvenmeliyim. Metodun gövdesine kendi tip kontrol kodumu ekleyebilirim:

```
class ShopProductWriter
{
    public function write($shopProduct): void
    {
        if (
            ! ($shopProduct instanceof CdProduct) &&
            ! ($shopProduct instanceof BookProduct)
        ){
            die("wrong type supplied"); 
        }
        $str  = "{$shopProduct->title}: "
            . $shopProduct->getProducer()
            . " ({$shopProduct->price})\n";
        print $str;
    }
}
```

Örnekteki instanceof operatörüne dikkat edin; sol işlenendeki nesne sağ işlenen tarafından temsil edilen türdeyse, instanceof true olarak çözümlenir.

Bir kez daha, karmaşıklığı arttırmak zorunda kaldım. $shopProduct parametresini iki tür için test etmem ve her türün diğeriyle aynı alanları ve metotları desteklemeye devam edeceğine güvenmem gerekiyor. Sınıf tipi bildirimi kullandığımda ve tek bir tür talep ettiğimde - ShopProduct sınıfının belirli bir arabirimi desteklediğinden emin olabileceğim için - her şey çok daha düzenliydi.

ShopProduct sınıfı CD ve kitap yönleriyle birlikte iyi çalışmıyor ama ayrı da yaşayamıyor gibi görünüyor. Her format için ayrı bir uygulama sağlarken, kitap ve CD'leri tek bir tür olarak tanımlamak istiyorum. Tekrarı önlemek için ortak bir işlevsellik sağlamalıyım, ancak her formatın bazı metot çağrılarını farklı şekilde işlemesine izin vermek istiyorum. Kalıtım kullanmam gerekiyor.

### Kalıtımla Çalışmak

Bir kalıtım ağacı oluşturmanın ilk adımı, temel sınıfın birbirine uymayan veya farklı şekilde ele alınması gereken öğelerini bulmaktır. getPlayLength() ve getNumberOfPages() metotlarının birbirine ait olmadığını biliyorum. Ayrıca getSummaryLine() metodu için farklı uygulamalar oluşturmam gerektiğini de biliyorum. Bu farklılıkları türetilmiş iki sınıf için temel olarak kullanalım:

```
class ShopProduct
{
    public $numPages;
    public $playLength;
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price,
        int $numPages = 0,
        int $playLength = 0
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
        $this->numPages = $numPages;
        $this->playLength = $playLength;
    }

    public function getProducer(): string
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        return $base;
    }
}
```

```
class CdProduct extends ShopProduct
{
    public function getPlayLength(): int
    {
        return $this->playLength;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        $base .= ": playing time - {$this->playLength}";
        return $base;
    }
}
```

```
class BookProduct extends ShopProduct
{
    public function getNumberOfPages(): int
    {
        return $this->numPages;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        $base .= ": page count - {$this->numPages}";
        return $base;
    }
}
```

Alt sınıf oluşturmak için, sınıf bildiriminde extends anahtar sözcüğünü kullanmalısınız. Örnekte, BookProduct ve CdProduct olmak üzere iki yeni sınıf oluşturdum. Her ikisi de ShopProduct sınıfını genişletir.

Türetilmiş sınıflarda kurucu metot tanımlamadığından, üst sınıfın kurucusu otomatik olarak çağrılır. Alt sınıflar, ebeveynin tüm public ve protected metotlarına erişebilir (ancak private metot veya property’lere erişemezler). Bu, ShopProduct sınıfında getProducer() tanımlanmış olsa bile, CdProduct sınıfından oluşturulan bir nesnede getProducer() yöntemini çağırabileceğiniz anlamına gelir:

```
$product2 = new CdProduct(
    "Exile on Coldharbour Lane",
    "The",
    "Alabama 3",
    10.99,
    0,
    60.33
);

print "artist: {$product2->getProducer()}\n";
```

Böylece her iki çocuk sınıf, ebeveynin davranışlarını miras alır. BookProduct nesnesine ShopProduct nesnesiymiş gibi davranabilirsiniz. BookProduct veya CdProduct nesnesini ShopProductWriter sınıfının write() metoduna iletebilirsiniz ve her şey beklendiği gibi çalışacaktır.

CdProduct ve BookProduct sınıflarının getSummaryLine() metodunu geçersiz kılarak kendi uygulamalarını sağladığına dikkat edin. Türetilmiş sınıflar, ebeveynlerinin işlevlerini genişletebilir ama aynı zamanda değiştirebilir.

Üst sınıfın bu metodu uygulaması, alt sınıflar tarafından geçersiz kılındığı için gereksiz görünebilir. Bununla birlikte, yeni alt sınıfların kullanabileceği temel işlevleri sağlar. Metodun varlığı aynı zamanda, tüm ShopProduct nesnelerinin getSummaryLine() metodunu sağlayacağına dair bir garanti verir. Daha sonra, herhangi bir uygulama olmadan bunu temel sınıfta yapmanın nasıl mümkün olduğunu göreceksiniz. Her alt ShopProduct sınıfı, ebeveyninin özelliklerini devralır. Hem BookProduct hem de CdProduct, getSummaryLine() versiyonlarında $title property’sine erişir.

Kalıtım, ilk başta anlaşılması zor bir kavram olabilir. Diğerini genişleten bir sınıf tanımlayarak, ondan örneklenen nesnenin önce alt sınıfın, sonra üst sınıfın özellikleri tarafından tanımlanmasını sağlarsınız. Bunu düşünmenin başka bir yolu da aramadır. $product2->getProducer()'ı çağırdığımda, CdProduct sınıfında böyle bir metot olmadığı için metot çağrısı, ShopProduct'taki varsayılan uygulamaya düşüyor. $product2->getSummaryLine()'ı çağırdığımda ise, getSummaryLine() metodu CdProduct'ta bulunuyor ve çağrılıyor.

Aynı şey property erişimleri için de geçerlidir. BookProduct sınıfının getSummaryLine() metodunda $title'a eriştiğimde, property BookProduct sınıfında bulunamadı. Bunun yerine üst sınıftan, ShopProduct'tan alınır. $title her iki alt sınıfa da uygulanır ve bu nedenle üst sınıfa aittir.

Bununla birlikte, ShopProduct kurucu metoduna baktığımızda, alt sınıflar tarafından işlenmesi gereken verileri yönetmeye devam ettiğini gösteriyor. BookProduct sınıfı, $numPages parametre ve property’sini, CdProduct sınıfı ise $playLength parametre ve property’sini işlemelidir. Bunun işe yaraması için, alt sınıfların her birinde kurucu metotlar tanımlayacağım.

**Kurucular ve Kalıtım**

Bir alt sınıfta kurucu metot tanımladığınızda, üst sınıfa herhangi bir argümanı iletmekten sorumlu olursunuz. Bunu başaramazsanız, kısmen inşa edilmiş bir nesne elde edebilirsiniz.

Üst sınıftan bir metodu çağırmak için, önce sınıfın kendisine atıfta bulunmanın bir yolunu bulmalısınız. PHP bu amaç için bize parent anahtar sözcüğünü sağlar.

Nesne yerine sınıf bağlamında bir metoda başvurmak için -> yerine :: kullanırsınız:

```
parent::__construct()
```

Önceki kod parçası, "Ana sınıfın __construct() metodunu çağır" anlamına gelir. Burada, örneğimi her sınıfın yalnızca kendisine uygun olan verileri işlemesi için değiştiriyorum:

```
class ShopProduct
{
    public $title;
    public $producerMainName;
    public $producerFirstName;
    public $price;

    public function __construct(
        $title,
        $firstName,
        $mainName,
        $price
    ) {
        $this->title = $title;
        $this->producerFirstName = $firstName;
        $this->producerMainName = $mainName;
        $this->price = $price;
    }

    public function getProducer(): string
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )"; return $base;
    }
}
```

```
class BookProduct extends ShopProduct
{
    public $numPages;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price,
        int $numPages
    ) {
        parent:: __construct(
            $title,
            $firstName,
            $mainName,
            $price
        );
        $this->numPages = $numPages;
    }

    public function getNumberOfPages(): int
    {
        return $this->numPages;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( $this->producerMainName, ";
        $base .= "$this->producerFirstName )";
        $base .= ": page count - {$this->numPages}";
        return $base;
    }
}
```

```
class CdProduct extends ShopProduct
{
    public $playLength;

    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        float $price,
        int $playLength
    ) {
        parent:: __construct(
            $title,
            $firstName,
            $mainName,
            $price
        );
        $this->playLength = $playLength;
    }

    public function getPlayLength(): int
    {
        return $this->playLength;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        $base .= ": playing time - {$this->playLength}";
        return $base;
    }
}
```

Her alt sınıf, kendi property’lerini ayarlamadan önce üst sınıfın kurucusunu çağırır. Temel sınıf artık yalnızca kendi verilerini tutuyor. Genel bir kural olarak, üst sınıflara alt sınıfları hakkında herhangi bir özel bilgi vermekten kaçınmalısınız.

**Not**: PHP 5'ten önce, kurucular çevreleyen sınıfın adını alırdı. Yeni kurucular __construct() adını kullanır. Eski sözdizimini kullanarak, üst sınıfın kurucusuna yapılan çağrı sizi söz konusu sınıfa bağlar: parent::ShopProduct();. Eski kurucu sözdizimi PHP 7.0'da deprecated oldu ve PHP 8'de tamamen kaldırıldı.

**Geçersiz Kılınan Metodu Çalıştırmak**

parent anahtar sözcüğü, bir üst sınıftaki eşdeğerini geçersiz kılan herhangi bir metotla kullanılabilir. Bir metodu geçersiz kıldığınızda, üst sınıfın işlevselliğini ortadan kaldırmak yerine onu genişletmek isteyebilirsiniz. Bunu, geçerli nesne bağlamında üst sınıfın metodunu çağırarak başarabilirsiniz. getSummaryLine() metoduna tekrar bakarsanız, birçok kodun kopyalandığını göreceksiniz. ShopProduct sınıfında halihazırda geliştirilen işlevselliği yeniden oluşturmak yerine kullanmak daha iyi olacaktır:

```
public function getSummaryLine(): string
{
    $base = "{$this->title} ( {$this->producerMainName}, ";
    $base .= "{$this->producerFirstName} )";
    return $base;
}
```

```
public function getSummaryLine(): string
{
    $base = parent::getSummaryLine();
    $base .= ": page count - $this->numPages";
    return $base;
}
```

ShopProduct üst sınıfında getSummaryLine() metodu için temel işlevselliği oluşturdum.

Bunu CdProduct ve BookProduct alt sınıflarında yeniden oluşturmak yerine, özet dizesine daha fazla veri eklemeye devam etmeden önce ana metodu çağırıyorum.

Artık kalıtımın temellerini gördüğünüze göre, property ve metot görünürlüğünü resmin tamamı ışığında yeniden inceleyeceğim.

### Public, Private, and Protected: Sınıflarınıza Erişimi Yönetmek

Şimdiye kadar, tüm property’leri public olarak tanımladım. Property bildiriminizde eski var anahtar sözcüğünü kullandıysanız, metodlar ve property’ler için genel erişim varsayılan ayardı.

**Not**: var, PHP 5'te kullanımdan kaldırılmıştır ve muhtemelen gelecekte dilden tamamen kaldırılacaktır.

Gördüğümüz gibi, sınıflarınızdaki öğeler public, private veya protected olarak bildirilebilir:

- Public property ve metotlara herhangi bir bağlamdan erişilebilir.
- Private property veya metoda yalnızca çevreleyen sınıf içinden erişilebilir. Alt sınıfların bile erişimi yoktur.
- Protected property veya metoda yalnızca çevreleyen sınıf içinden veya bir alt sınıftan erişilebilir. Hiçbir harici koda erişim izni verilmez.

Peki bu bizim için ne kadar faydalı? Görünürlük anahtar sözcükleri, bir sınıfın yalnızca yazılımcı tarafından gerekli görülen yönlerini açığa çıkarmanıza olanak tanır. Bu, nesneniz için net bir arayüz sağlar.

Erişim denetimi, yazılımcının belirli özelliklere erişmesini engelleyerek kodunuzdaki hataların önlenmesine de yardımcı olabilir. Örneğin, ShopProduct nesnelerinin indirimi desteklemesine izin vermek istediğinizi düşünün. $discount property’si ve setDiscount() metodu ekleyebilirsiniz:

```
// ShopProduct class
public $discount = 0;
//...
public function setDiscount(int $num): void
{
    $this->discount = $num;
}
```

Uygulanan indirimi dikkate alan bir getPrice() metodu oluşturabilirsiniz:

```
public function getPrice(): int|float
{
    return ($this->price - $this->discount);
}
```

Bu noktada bir sorunumuz var. Yalnızca ayarlanmış fiyatı göstermek istiyorsunuz, ancak bir yazılımcı getPrice() metodunu kolayca atlayabilir ve $price özelliğine erişebilir:

```
print "The price is {$product1->price}\n";
```

Bu, sunmak istediğiniz iskontoya göre düzeltilmiş fiyatı değil, ham fiyatı yazdıracaktır. $price property’sini private yaparak buna hemen bir son verebilirsiniz. Bu, yazılımcıları getPrice() metodunu kullanmaya zorlayarak doğrudan erişimi engelleyecektir. ShopProduct sınıfının dışından $price property’sine erişme girişimleri başarısız olur. Daha geniş dünya söz konusu olduğunda, bu property’nin varlığı sona ermiştir.

Property’leri private olarak ayarlamak aşırı hevesli bir strateji olabilir. Private bir property’ye alt sınıf tarafından erişilemez. İş kurallarımızın yalnızca kitapların indirim için uygun olmaması gerektiğini belirttiğini hayal edin. GetPrice() metodunu, indirim uygulamadan $price property’sini döndürmesi için geçersiz kılabilirsiniz:

```
// BookProduct
public function getPrice(): int|float
{
    return $this->price;
}
```

Private $price property’si BookProduct'ta değil ShopProduct sınıfında bildirildiğinden, ona buradan erişme girişimi başarısız olur. Bu sorunun çözümü, $price değişkenini protected olarak bildirmek ve böylece alt sınıflara erişim sağlamaktır. Protected property veya metot, bildirildiği sınıf hiyerarşisinin dışından erişilemez. Yalnızca kaynak sınıfından veya kaynak sınıfın alt öğelerinden erişilebilir.

İlk başta property’leri private veya protected hale getirin ve kısıtlamanızı yalnızca gerektiğinde gevşetin. Sınıflarınızdaki çoğu (çoğu değilse de büyük kısmı) metot public olacak, ancak şüpheniz varsa bir kez daha kilitleyin. Sınıfınızdaki diğer metotlar için yerel işlevsellik sağlayan bir metodun, sınıfınızın kullanıcıları ile hiçbir ilgisi yoktur. Private veya protected yapın.

**Erişim (Accessor) Metotları**

Yazılımcıların sınıfınız tarafından tutulan değerlerle çalışması gerektiğinde, property’lere doğrudan erişimi reddetmek, bunun yerine gerekli değerleri aktaran metotlar sağlamak genellikle iyi bir fikirdir. Bu tür metotlar accessor veya getter ve setter olarak bilinir.

Erişim metotlarının sağladığı bir avantajı zaten gördünüz. getPrice() metodunda gösterildiği gibi, property değerini koşullara göre filtrelemek için accessor kullanabilirsiniz.

Property türünü zorlamak için setter kullanabilirsiniz. Tip bildirimleri, metot parametrelerini kısıtlamak için kullanılabilir, ancak property herhangi bir türde veri içerebilir. Liste verilerini çıkarmak için ShopProduct nesnesini kullanan ShopProductWriter sınıfını hatırlıyor musunuz? Bunu, bir defada herhangi bir sayıda ShopProduct nesnesini yazabilecek şekilde geliştirebilirim:

```
class ShopProductWriter
{
    public $products = [];

    public function addProduct(ShopProduct $shopProduct): void
    {
        $this->products[] = $shopProduct;
    }

    public function write(): void
    {
        $str = "";
        foreach ($this->products as $shopProduct) {
            $str .= "{$shopProduct->title}: ";
            $str .= $shopProduct->getProducer();
            $str .= " ({$shopProduct->getPrice()})\n";
        }
        print $str;
    }
}
```

ShopProductWriter sınıfı artık çok daha kullanışlı. Birçok ShopProduct nesnesini tutabilir ve hepsi için tek seferde veri yazabilir. Yine de, yazılımcılara sınıfı amacına uygun olarak kullanmaları konusunda güvenmeliyim. addProduct() metodunu sağlamış olmama rağmen, yazılımcıların $products property’sini doğrudan manipüle etmesini engellemedim. Birisi $products property’sine yanlış türde nesne eklemekle kalmaz, aynı zamanda tüm dizinin üzerine yazıp onu ilkel bir veri ile değiştirebilir. $products property’sini private yaparak bunu önleyebilirim:

```
class ShopProductWriter
{
    private $products = [];
    //...
```

Harici kodun $products property’sine zarar vermesi artık imkansız. Tüm erişim addProduct() metodu aracılığıyla olmalıdır ve metot bildiriminde kullandığım sınıf tipi bildirimi, array property’sine yalnızca ShopProduct nesnelerinin eklenebilmesini sağlar.

### Property Tipleri

Böylece, metot imzalarındaki tip bildirimlerini property görünürlük bildirimleriyle birleştirerek, sınıflarınızdaki property tiplerini kontrol edebilirsiniz. İşte başka bir örnek: property tiplerini yönetmek için tip bildirimlerini ve property görünürlüğünü kullandığım Point sınıfı:

```
class Point
{
    private $x = 0;
    private $y = 0;

    public function setVals(int $x, int $y)
    {
        $this->x = $x;
        $this->y = $y;
    }

    public function getX(): int
    {
        return $this->x;
    }

    public function getY(): int
    {
        return $this->y;
    }
}
```

$x ve $y property’leri private olduğundan, yalnızca setVals() metoduyla ayarlanabilirler ve setVals() yalnızca integer değerleri kabul edeceğinden, $x ve $y'nin her zaman tamsayı içerdiğinden emin olabilirsiniz.

Tabii ki, bu property’ler private olarak ayarlandığından, bunlara erişmenin tek yolu getter veya accessor metotlardır.

Property tiplerini tanıtan PHP sürüm 7.4'e kadar, property tiplerini sabitlemek için bu yönteme takılıp kalmıştık. Bu, property’leri için tip bildirmemizi sağlar. İşte bundan yararlanan Point sürümü:

```
class Point
{
    public int $x = 0;
    public int $y = 0;
}
```

$x ve $y property’lerini public yaptım ve türlerini kısıtlamak için tip bildirimi kullandım. Bu nedenle, istersem kontrolden ödün vermeden setVals() metodundan kurtulmayı seçebilirim. Ayrıca getX() ve getY() metotlarına da ihtiyacım yok. Point artık son derece basit bir sınıftır, ancak her iki property public olsa bile, tuttuğu veriler hakkında dünyaya garantiler sunar.

Bu property’lerden birine dize ayarlamaya çalışalım:

```
$point = new Point();
$point->x = "a";
```

PHP bundan kaçmamıza izin vermeyecek:

```
TypeError: Cannot assign string to property popp\ch03\batch11\Point::$x of type int
```

**Not**: Birleşik tipler, property tip bildirimlerinde de kullanılabilir.

**ShopProduct Sınıfları**

Bu bölümü, ShopProduct sınıfı ve alt öğelerin erişim denetimini kilitleyerek ve ele aldığımız diğer özelliklerden bazılarını dahil edecek şekilde değiştirerek kapatalım:

```
class ShopProduct
{
    private int|float $discount = 0;

    public function __construct(
        private string $title,
        private string $producerFirstName,
        private string $producerMainName,
        protected int|float $price
    ) {
    }

    public function getProducerFirstName(): string
    {
        return $this->producerFirstName;
    }

    public function getProducerMainName(): string
    {
        return $this->producerMainName;
    }

    public function setDiscount(int|float $num): void
    {
        $this->discount = $num;
    }

        public function getDiscount(): int
    {
        return $this->discount;
    }

    public function getTitle(): string
    {
        return $this->title;
    }

    public function getPrice(): int|float
    {
        return ($this->price - $this->discount);
    }

    public function getProducer(): string
    {
        return $this->producerFirstName . " "
            . $this->producerMainName;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        return $base;
    }
}
```

Görünürlüklerini private (veya $discount durumunda protected) olarak ayarlayarak property’lerin çoğuna erişimi kapatmanın yanı sıra, property bildirimlerimi kurucu metot imzasıyla birleştirdim. Aynı zamanda PHP 8'in yeni tip birleşim özelliğini gösteren $discount için property tip bildirimi kullandım. Int veya float değer atanabilmesi için $discount'u kısıtladım. $discount private olarak bildirildiğinden ve setDiscount() metodundaki tip bildirimi aynı koşulu uygulayacağından, bu kısıtlama gereksiz görünebilir. Bununla birlikte, bir tür zorunlu satır içi belgeleme işlevi gördüğü için ve kısmen de ShopProduct'ın geliştirilmesi sırasında yanlışlıkla $discount ile istenmeyen şekilde oynamamızı önleyeceği için property tipi bildirmek iyi bir uygulamadır.

```
class CdProduct extends ShopProduct
{
    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        int|float $price,
        private int $playLength
    ) {
        parent:: __construct(
            $title,
            $firstName,
            $mainName,
            $price
        );
    }

    public function getPlayLength(): int
    {
        return $this->playLength;
    }

    public function getSummaryLine(): string
    {
        $base = "{$this->title} ( {$this->producerMainName}, ";
        $base .= "{$this->producerFirstName} )";
        $base .= ": playing time - {$this->playLength}";
        return $base;
    }
}
```

Yine property tip bildirimini kurucu metot imzası ile birleştiriyorum. Bu sefer, yalnızca bir parametre için: $playLength. Kurucu parametrelerinin geri kalanını üst sınıfa aktarıyorum ve onlar için görünürlük ayarlamıyorum. Bunun yerine kurucunun gövdesinde kullanıyorum.

```
class BookProduct extends ShopProduct
{
    public function __construct(
        string $title,
        string $firstName,
        string $mainName,
        int|float $price,
        private int $numPages
    ) {
        parent:: construct(
            $title,
            $firstName,
            $mainName,
            $price
        );
    }

    public function getNumberOfPages(): int
    {
        return $this->numPages;
    }

    public function getSummaryLine(): string
    {
        $base = parent::getSummaryLine();
        $base .= ": page count - $this->numPages";
        return $base;
    }

    public function getPrice(): int|float
    {
        return $this->price;
    }
}
```

ShopProduct ailesinin bu sürümünde tüm property’ler private veya protected olarak tanımlandı. İşleri tamamlamak için bir dizi erişim metodu ekledim.

**Özet**

Bu bölüm, bir sınıfı boş bir uygulamadan tüm özeliklere sahip kalıtım hiyerarşisine götüren birçok konuyu kapsıyordu. Özellikle tür ve kalıtımla ilgili bazı tasarım konularını ele aldınız. PHP'nin görünürlük desteğini gördünüz ve bazı kullanımlarını incelediniz. Bir sonraki bölümde, size PHP'nin nesne yönelimli özelliklerinden daha fazlasını göstereceğim.

**Kaynak:** “PHP 8 Objects, Patterns, and Practice”, Matt Zandstra