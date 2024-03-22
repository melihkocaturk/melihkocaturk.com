---
layout: post
title: PHP – Gelişmiş Özellikler
---

Sınıf tipi ipuçlarının ve erişim kontrolünün size bir sınıfın arayüzü üzerinde nasıl daha fazla kontrol sağladığını zaten gördünüz. Bu bölümde, PHP'nin nesne yönelimli özelliklerini daha derinlemesine inceleyeceğim.

Bu bölüm birkaç konuyu kapsayacaktır:

- *Static metotlar ve property’ler*: Verilere ve işlevselliğe nesneler yerine sınıflar aracılığıyla erişme
- *Soyut (Abstract) sınıflar ve interface*: Tasarımı uygulamadan ayırma
- *Trait*: Uygulamayı sınıf hiyerarşileri arasında paylaşma
- *Hata işleme*: İstisnaların (Exception) tanıtılması
- *Final sınıf ve metotlar*: Kalıtımın sınırlandırılması
- *Interceptor metotlar*: Delegasyonu otomatikleştirme
- *Yıkıcı (Destructor) metotlar*: Nesnelerinizin arkasını temizlemek
- *Nesneleri klonlamak*: Nesne kopyaları oluşturma
- *Nesneleri dizelere dönüştürmek*: Özet metodu oluşturma
- *Callbacks*: Anonim işlev ve sınıflara sahip bileşenlere işlevsellik ekleme

## Static Metot ve Property’ler

Önceki bölümdeki örneklerin tümü nesnelerle çalıştı. Sınıfları, nesnelerin üretildiği şablonlar olarak ve nesneleri, metotlarını çağırdığınız ve property’lerine eriştiğiniz sınıfların aktif örnekleri olarak tanımladım. Nesne yönelimli programlamada gerçek işin sınıf örnekleri tarafından yapıldığını ima ettim. Ne de olsa sınıflar, yalnızca nesneler için şablonlardır.

Aslında, o kadar basit değil. Hem metotlara hem de property’lere nesne yerine sınıf bağlamında erişebilirsiniz. Bu tür metot ve property’ler "statik" tir ve static anahtar kelimesi kullanılarak bildirilmelidir:

```
class StaticExample
{
    public static int $aNum = 0;

    public static function sayHello(): void
    {
        print "hello";
    }
}
```

Static metotlar, sınıf kapsamına sahip fonksiyonlardır. Sınıftaki herhangi bir normal property’e kendileri erişemezler çünkü bunlar bir nesneye ait olacaktır; ancak static property’lere erişebilirler. Static bir property’i değiştirirseniz, o sınıfın tüm örnekleri yeni değere erişebilir.

Static öğeye örnek aracılığıyla değil, sınıf aracılığıyla eriştiğiniz için, nesneye başvuran bir değişkene ihtiyacınız yoktur. Bunun yerine, bu örnekte olduğu gibi sınıf adını :: ile birlikte kullanırsınız:

```
print StaticExample::$aNum;
StaticExample::sayHello();
```

Bu sözdizimi önceki bölümden tanıdık olmalıdır. Geçersiz kılınan bir metoda erişmek için parent ile birlikte :: kullandım. Şimdi, o zaman olduğu gibi, nesne yerine sınıfa erişiyorum. Sınıf kodu, sınıf adını kullanmadan bir üst sınıfa erişmek için parent anahtar sözcüğünü kullanabilir. Static bir metod veya property’e aynı sınıf içinden erişmek için self anahtar sözcüğünü kullanıyorum. Nesneler için $this sözde değişkeni ne ise sınıflar için "self" odur. StaticExample sınıfının dışından $aNum property’sine sınıf adını kullanarak erişiyorum:

```
StaticExample::$aNum;
```

```
class StaticExample2
{
    public static int $aNum = 0;

    public static function sayHello(): void
    {
        self::$aNum++;
        print "hello (" . self::$aNum . ")\n";
    }
}
```

**Not**: parent kullanarak metot çağrısı yapmak, static olmayan bir metoda static başvuru kullanmanız gereken tek durumdur.

Geçersiz kılınan bir metoda erişemiyorsanız, açıkça static olarak bildirilen bir metoda veya  property’e erişmek için yalnızca :: kullanmalısınız.

Bununla birlikte, belgelerde, genellikle bir metot veya property’e atıfta bulunmak için static sözdizimi kullanıldığını görürsünüz. Bu, söz konusu öğenin mutlaka static olduğu anlamına gelmez, yalnızca belirli bir sınıfa aittir. Örneğin ShopProductWriter sınıfının write() metodu static olmasa bile ShopProductWriter::write() olarak anılabilir. Uygun durumlarda bu sözdizimini burada göreceksiniz.

Tanım gereği, static metot ve property’ler nesnelerde değil, sınıflarda çağrılır. Bu nedenle, genellikle sınıf değişkenleri ve property’leri olarak adlandırılırlar. Bu sınıf yöneliminin sonucu olarak, static bir metot içinde $this sözde değişkenini kullanamazsınız.

Öyleyse neden static bir metot veya property kullanasınız? Static elemanlar, yararlı olabilecek bir dizi özelliğe sahiptir. İlk olarak, kodunuzun herhangi bir yerinden - sınıfa erişiminiz olduğu varsayılarak - erişilebilirler. Bu, sınıfın örneğini nesneden nesneye aktarmaya veya daha da kötüsü örneği global değişkende depolamaya gerek kalmadan işlevselliğe erişebileceğiniz anlamına gelir. İkinci olarak, sınıfın her örneği için static property mevcuttur, böylece bir türün tüm üyeleri tarafından kullanılabilir olmasını istediğiniz değerleri ayarlayabilirsiniz. Son olarak, static property veya metoda erişmek için bir örneğe ihtiyacınızın olmaması, sizi yalnızca basit bir işleve ulaşmak için nesneyi başlatmaktan kurtarabilir.

Bunu göstermek için, ShopProduct nesnelerinin örneklenmesini otomatikleştirmek için static bir metot oluşturacağım. SQLite kullanarak şöyle bir products tablosu tanımlayabilirim:

```
CREATE TABLE products (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    type TEXT,
    firstname TEXT,
    mainname TEXT,
    title TEXT,
    price float,
    numpages int,
    playlength int,
    discount int )
```

Şimdi row ID ve PDO nesnesi kabul eden, bunları bir veritabanı satırı elde etmek için kullanan ve ardından ShopProduct nesnesi döndüren getInstance() metodu oluşturmak istiyorum. Bir önceki bölümde oluşturduğum ShopProduct sınıfına bu metotları ekleyebilirim. Muhtemelen bildiğiniz gibi, PDO, PHP Data Object anlamına gelir. PDO sınıfı, farklı veritabanı uygulamaları için ortak bir arabirim sağlar:

```
// ShopProduct class...
private int $id = 0;
// ...
public function setID(int $id): void
{
    $this->id = $id;
}
// ...
public static function getInstance(int $id, \PDO $pdo): ShopProduct
{
    $stmt = $pdo->prepare("select * from products where id=?");
    $result = $stmt->execute([$id]);
    $row = $stmt->fetch();
    if (empty($row)) {
        return null;
    }
    if ($row['type'] == "book") {
        $product = new BookProduct(
            $row['title'],
            $row['firstname'],
            $row['mainname'],
            (float) $row['price'],
            (int) $row['numpages']
        );
    } elseif ($row['type'] == "cd") {
        $product = new CdProduct(
            $row['title'],
            $row['firstname'],
            $row['mainname'],
            (float) $row['price'],
            (int) $row['playlength']
        );
    } else {
        $firstname = (is_null($row['firstname'])) ? "" :
            $row['firstname'];
        $product = new ShopProduct(
            $row['title'],
            $firstname,
            $row['mainname'],
            (float) $row['price']
        );
    }
    $product->setId((int) $row['id']);
    $product->setDiscount((int) $row['discount']);
    return $product;
}
```

Gördüğünüz gibi, getInstance() metodu ShopProduct nesnesi döndürür ve türe bağlı  olarak, başlatması gereken nesneyi belirleyecek kadar akıllıdır. Örneği kompakt tutmak için herhangi bir hata işlemeyi atladım. Örneğin, bunun gerçek dünyadaki versiyonunda, sağlanan PDO nesnesinin doğru veritabanıyla konuşmak için başlatıldığını varsayacak kadar güvenmezdim. Aslında, muhtemelen PDO'yu bu davranışı garanti edecek bir sınıfla sarardım. Bölüm 13'te nesne yönelimli programlama ve veritabanları hakkında daha fazla bilgi edinebilirsiniz.

Bu metot, sınıf bağlamında nesne bağlamından daha kullanışlıdır. Başlamak için ShopProduct nesnesine sahip olmanızı gerektirmeden, veritabanındaki ham verileri kolayca bir nesneye dönüştürmenize olanak tanır. Metot, herhangi bir örnek property veya metodu kullanmaz, bu nedenle static olarak bildirilmemesi için hiçbir neden yoktur. Geçerli bir PDO nesnesi verildiğinde, metodu uygulamanın herhangi bir yerinden çağırabilirim:

```
$dsn = "sqlite:/tmp/products.sqlite3";
$pdo = new \PDO($dsn, null, null);
$pdo->setAttribute(\PDO::ATTR_ERRMODE, \PDO::ERRMODE_EXCEPTION);
$obj = ShopProduct::getInstance(1, $pdo);
```

Bunun gibi metotlar, hammaddeleri (satır verileri veya yapılandırma bilgileri gibi) alıp bunlarla nesneler ürettikleri için "fabrika" (factory) görevi görür. Factory terimi, nesne örnekleri oluşturmak için tasarlanmış koda uygulanır. İlerleyen bölümlerde yine factory örnekleri ile karşılaşacaksınız.

Elbette bazı açılardan bu örnek çözdüğü kadar çok sorunu da beraberinde getiriyor. ShopProduct::getInstance() metodu ShopProduct örneğine ihtiyaç duymadan sistemin herhangi bir yerinden erişilebilir kılsam da, yazılımcının PDO nesnesi sağlamasını da talep ediyorum. Bu nerede bulunur? Ve bir üst sınıfın alt sınıflar hakkında bu kadar bilgiye sahip olması gerçekten iyi bir uygulama mı? (İpucu: Hayır, değil.) Bu tür problemler - anahtar nesnelerin ve değerlerin nereden elde edileceği ve sınıfların birbiri hakkında ne kadar bilgi sahibi olması gerektiği - nesne yönelimli programlamada çok yaygındır. Bölüm 9'da nesne üretimine yönelik çeşitli yaklaşımları inceliyorum.

## Sabit (Constant) Property’ler

Bazı property’ler değiştirilmemelidir. Hata ve status flag’leri genellikle sınıflarınıza sabit olarak kodlanır. Herkese açık ve static olarak erişilebilir olmaları gerekmesine rağmen, yazılımcı bunları değiştirememelidir.

PHP, sınıf içinde sabit property’ler tanımlamanıza izin verir. Global sabitler gibi, sınıf sabitleri de ayarlandıktan sonra değiştirilemezler. Sabit property, const anahtar sözcüğüyle bildirilir. Sabitler, normal property’ler gibi dolar işareti ile ön eklenmez. Geleneksel olarak, genellikle yalnızca büyük harfler kullanılarak adlandırılırlar:

```
class ShopProduct
{
    public const AVAILABLE = 0;
    public const OUT_OF_STOCK = 1;
```

Sabit property’ler yalnızca primitive değerler içerebilir. Bir sabite nesne atayamazsınız. Static property’ler gibi, sabit property’lere de örnek üzerinden değil, sınıf aracılığıyla erişilir. Dolar işareti olmadan sabit tanımlarken olduğu gibi, sabite atıfta bulunduğunuzda baştaki simgeye gerek yoktur:

```
print ShopProduct::AVAILABLE;
```

**Not**: Sabit görünürlük bildirimi (visibility modifiers) için destek PHP 7.1'de sunulmuştur. Property’ler de olduğu gibi çalışırlar.

Bildirildikten sonra sabite değer atamaya çalışmak ayrıştırma hatasına neden olur.

Property’nizin sınıfın tüm örneklerinde kullanılabilir olması ve property değerinin sabitlenmesi ve değişmemesi gerektiğinde sabitleri kullanmalısınız.

## Soyut (Abstract) Sınıflar

Soyut bir sınıf somutlaştırılamaz. Bunun yerine, onu genişletebilecek herhangi bir sınıf için arayüz tanımlar (ve isteğe bağlı olarak kısmen uygular).

Soyut sınıf abstract anahtar sözcüğü ile tanımlanır. Bir önceki bölümde oluşturduğum ShopProductWriter sınıfını bu kez soyut sınıf olarak yeniden tanımlıyorum:

```
abstract class ShopProductWriter
{
    protected array $products = [];
    
    public function addProduct(ShopProduct $shopProduct): void
    {
        $this->products[] = $shopProduct;
    }
}
```

Metot ve property’leri normal şekilde oluşturabilirsiniz, ancak soyut bir nesneyi bu şekilde başlatma girişimleri hataya neden olur:

```
$writer = new ShopProductWriter();
```

Bu çıktıda hatayı görebilirsiniz:

```
Error: Cannot instantiate abstract class
popp\ch04\batch03\ShopProductWriter
```

Çoğu durumda, soyut sınıf en az bir soyut metot içerecektir. Bunlar yine abstract anahtar sözcüğü ile bildirilir. Soyut metodun uygulaması olamaz. Bunu normal şekilde bildirirsiniz, ancak bildirimi metot gövdesi yerine noktalı virgülle biter. Burada, ShopProductWriter sınıfına soyut bir write() metodu ekliyorum:

```
abstract class ShopProductWriter
{
    protected array $products = [];

    public function addProduct(ShopProduct $shopProduct): void
    {
        $this->products[] = $shopProduct;
    }
    
    abstract public function write(): void;
}
```

Soyut bir metot oluştururken, uygulamanın tüm somut alt sınıflarda mevcut olmasını sağlarsınız, ancak bu uygulamanın ayrıntılarını tanımsız bırakırsınız.

Bu örnekte olduğu gibi, write() metodunu uygulamayan ShopProductWriter'dan türetilen bir sınıf oluşturacağımı varsayalım:

```
class ErroredWriter extends ShopProductWriter
{
}
```

Aşağıdaki hatayla karşılaşıyorum:

```
Fatal error: Class popp\ch04\batch03\ErroredWriter contains 1 abstract
method and must therefore be declared abstract or implement the remaining methods (popp\ch04\batch03\ShopProductWriter::write) in...
```

Bu nedenle, soyut sınıfı genişleten herhangi bir sınıf, tüm soyut metotları uygulamalı veya kendisi soyut olarak bildirilmelidir. Genişleyen sınıf, soyut metodu uygulamaktan daha fazlasından sorumludur. Bunu yaparken, metot imzasını yeniden üretmelidir. Bu, uygulama metodunun erişim kontrolünün soyut metottan daha katı olamayacağı anlamına gelir. Uygulama metodu ayrıca, soyut metotla aynı sayıda parametre gerektirmelidir.

İşte ShopProductWriter'ın iki uygulaması: Birincisi, XmlProductWriter:

```
class XmlProductWriter extends ShopProductWriter
{
    public function write(): void
    {
        $writer = new \XMLWriter();
        $writer->openMemory();
        $writer->startDocument('1.0', 'UTF-8');
        $writer->startElement("products");

        foreach ($this->products as $shopProduct) {
            $writer->startElement("product");
            $writer->writeAttribute("title", $shopProduct->getTitle());
            $writer->startElement("summary");
            $writer->text($shopProduct->getSummaryLine());
            $writer->endElement(); // summary
            $writer->endElement(); // product
        }
        
        $writer->endElement(); // products
        $writer->endDocument();
        print $writer->flush();
    }
}
```

Bu, daha basit TextProductWriter'dır:

```
class TextProductWriter extends ShopProductWriter
{
    public function write(): void
    {
        $str = "PRODUCTS:\n";
        foreach ($this->products as $shopProduct) {
            $str .= $shopProduct->getSummaryLine() . "\n";
        }
        print $str;
    }
}
```

Böylece, her biri kendi write() metot uygulamasına sahip iki sınıf oluşturdum. Birincisi XML çıktısı, ikincisi ise metin çıktısı verir. ShopProductWriter nesnesi gerektiren bir metot, bu iki sınıftan hangisini aldığını bilemez, ancak write() metodunun uygulandığından kesinlikle emin olabilir. Dizi olarak ele almadan önce $products’ın türünü test etmediğimi unutmayın. Bunun nedeni, bu property’nin hem bir dizi olarak bildirilmesi hem de ShopProductWriter sınıfında başlatılmasıdır.

## Arayüzler (Interfaces)

Soyut sınıflar, bir miktar uygulama sağlamanıza izin verse de, arayüzler saf şablonlardır. Arayüz yalnızca işlevselliği tanımlayabilir; asla uygulayamaz. interface anahtar kelimesiyle arayüz tanımlanır. Property ve metot bildirimlerini içerebilir, ancak metot gövdelerini içeremez.

İşte bir arayüz:

```
interface Chargeable
{
    public function getPrice(): float;
}
```

Gördüğünüz gibi, arayüz bir sınıfa çok benziyor. Bu arayüzü içeren herhangi bir sınıf, tanımladığı tüm metotları uygulamayı taahhüt eder veya soyut olarak bildirilmelidir. 

Bir sınıf, bildiriminde implements anahtar sözcüğünü kullanarak arayüzü uygulayabilir. Bunu yaptıktan sonra, arayüzü uygulama süreci, soyut bir sınıfı genişletmekle aynıdır. Şimdi ShopProduct sınıfının Chargeable uygulamasını yapacağım:

```
class ShopProduct implements Chargeable
{
    // ...
    protected float $price;
    // ...
    public function getPrice(): float
    {
        return $this->price;
    }
    // ...
}
```

ShopProduct zaten getPrice() metoduna sahipti, öyleyse Chargeable arayüzünü uygulamak neden yararlı olabilir? Bir kez daha, cevabın türlerle ilgisi var. Uygulama sınıfı, genişlettiği sınıfın türünü ve uyguladığı arayüzü alır.

Bu, CdProduct sınıfının aşağıdakilere ait olduğu anlamına gelir:

```
CdProduct
ShopProduct
Chargeable
```

Bir nesnenin türünü bilmek, onun yeteneklerini bilmektir. Bu metodu göz önünde bulundurun:

```
public function cdInfo(CdProduct $prod): int
{
    // we know we can call getPlayLength()
    $length = $prod->getPlayLength();
    // ...
}
```

Metot, $prod nesnesinin, ShopProduct sınıfında ve Chargeable arabiriminde tanımlanan tüm metotlara ek olarak getPlayLength() metoduna sahip olduğunu bilir.

Daha genel tür gereksinimi olan bir metot - CdProduct yerine ShopProduct - sağlanan nesnenin yalnızca ShopProduct metotlarını içerdiğini bilebilir.

```
public function addProduct(ShopProduct $prod)
{
    // even if $prod is a CdProduct object
    // we don't *know* this -- so we can't
    // presume to use getPlayLength()
    // ...
}
```

Daha fazla test yapılmadan metot, getPlayLength() metodu hakkında hiçbir şey bilmeyecektir.

Chargeable nesnesi gerektiren bir metot ShopProduct veya CdProduct türleriyle ilgili  hiçbir şey bilmiyor:

```
public function addChargeableItem(Chargeable $item)
{
    // all we know about $item is that it
    // is a Chargeable object -- the fact that it
    // is also a CdProduct object is irrelevant.
    // We can only be sure of getPrice()
    //
    //...
}
```

Bu metot yalnızca $item parametresinin getPrice() metodu içerip içermediğiyle ilgilenir.

Herhangi bir sınıf bir arayüz uygulayabildiğinden (aslında, bir sınıf herhangi bir sayıda arayüz uygulayabilir), arayüzler alakasız olan türleri etkili bir şekilde birleştirir. Chargeable'ı uygulayan tamamen yeni bir sınıf tanımlayabilirim:

```
class Shipping implements Chargeable
{
    public function __construct(private float $price)
    {
    }
    
    public function getPrice(): float
    {
        return $this->price;
    }
}
```

ShopProduct nesnesinde olduğu gibi, Shipping nesnesini de addChargeableItem() metoduna iletebilirim.

Chargeable nesnesiyle çalışan bir istemci için önemli olan, getPrice() metodunu çağırabilmesidir. Kullanılabilir diğer tüm metotlar, nesnenin kendi sınıfı, bir üst sınıf veya başka bir arayüz aracılığıyla diğer türlerle ilişkilendirilir.

Bir sınıf, hem üst sınıfı genişletebilir hem de herhangi bir sayıda arayüz uygulayabilir. extends ifadesi, implements ifadesinden önce gelmelidir:

```
class Consultancy extends TimedService implements Bookable, Chargeable
{
    // ...
}
```

Consultancy sınıfının birden fazla arayüz uyguladığına dikkat edin. Birden çok arayüz, virgülle ayrılmış bir listede implements anahtar sözcüğünü takip eder. PHP yalnızca tek bir ebeveynden kalıtımı destekler, bu nedenle extends anahtar sözcüğü yalnızca tek bir sınıf adından önce gelebilir.

## Trait’ler

Gördüğümüz gibi, arayüzler Java gibi PHP'nin çoklu kalıtımı desteklemediği gerçeğini yönetmenize yardımcı olur. Başka bir deyişle, PHP'deki bir sınıf yalnızca tek bir ebeveyni genişletebilir. Ancak, bir sınıfa istediğiniz kadar arayüz uygulayabilirsiniz; sınıf, uyguladığı her arayüz için karşılık gelen türü alır.

Böylece arayüzler, uygulama olmadan türler sağlar. Peki ya bir uygulamayı kalıtım hiyerarşileri arasında paylaşmak isterseniz? PHP 5.4 trait’leri tanıttı ve bunlar tam olarak bunu yapmanıza izin veriyor.

Trait, kendisi somutlaştırılamayan ancak sınıflara dahil edilebilen sınıf benzeri bir yapıdır. Trait’te tanımlanan tüm metotlar, onu kullanan herhangi bir sınıfın parçası olarak kullanılabilir hale gelir. Trait, bir sınıfın yapısını değiştirir, ancak türünü değiştirmez.

Trait’in neden yararlı olabileceğine bakalım.

### Trait’lerin Çözmesi Gereken Bir Problem

İşte ShopProduct sınıfının calculateTax() metotlu versiyonu:

```
class ShopProduct
{
    private int $taxrate = 20;
    // ...
    public function calculateTax(float $price): float
    {
        return (($this->taxrate / 100) * $price);
    }
}
```

calculateTax() metodu, $price parametresini alır ve private $taxrate property’sine göre satış vergisi tutarını hesaplar.

Tabii ki, bir alt sınıf calculateTax()'a erişebilir. Peki ya tamamen farklı sınıf hiyerarşileri? Service sınıfından miras alan UtilityService adlı bir sınıf düşünün. UtilityService'in aynı yordamı kullanması gerekiyorsa, kendimi calculateTax()'ı kopyalarken bulabilirim.

```
abstract class Service
{
    // service oriented stuff
}

// And here is UtilityService:
class UtilityService extends Service
{
    private int $taxrate = 20;

    public function calculateTax(float $price): float
    {
        return (($this->taxrate / 100) * $price);
    }
}
```

UtilityService ve ShopProduct herhangi bir ortak temel sınıfa sahip olmadığından, calculateTax() uygulamasını kolayca paylaşamazlar. Bu nedenle, uygulamamızı bir sınıftan diğerine kopyalayıp yapıştırmak zorunda kalıyoruz.

### Trait Tanımlamak ve Kullanmak

Bu kitapta ele alacağım nesne yönelimli tasarım hedeflerinden biri, kod tekrarından kaçınmaktır. Bölüm 11'de göreceğiniz gibi, bu tür bir yinelemenin çözümü, onu yeniden kullanılabilir bir strateji sınıfına ayırmaktır. Trait’ler başka bir yaklaşım sağlar - çok şık olmasa da kesinlikle etkilidir.

Burada, calculateTax() metodunu tanımlayan tek bir trait bildiriyorum ve ardından bunu hem ShopProduct hem de UtilityService'e dahil ediyorum:

```
trait PriceUtilities
{
    private $taxrate = 20;
    
    public function calculateTax(float $price): float
    {
        return (($this->taxrate / 100) * $price);
    }
    // other utilities
}
```

trait anahtar sözcüğü ile PriceUtilities’i deklare ediyorum. Trait gövdesi, sınıfın gövdesine çok benziyor. Basitçe parantez içinde toplanan bir dizi metot ve property’dir. Bunu tanımladıktan sonra, sınıflarımdan PriceUtilities trait’ine erişebilirim. Bunu, use anahtar kelimesini ve ardından dahil etmek istediğim trait adını kullanarak yapıyorum. calculateTax() metodunu tek bir yerde tanımlayıp uyguladıktan sonra, devam edip ShopProduct sınıfına dahil ediyorum.

```
use popp\ch04\batch06_1\PriceUtilities;

class ShopProduct
{
    use PriceUtilities;
}
```

Ayrıca, tabii ki onu UtilityService sınıfına ekliyorum:

```
class UtilityService extends Service
{
    use PriceUtilities;
}
```

Şimdi, bu sınıfları çağırdığımda, kod tekrarı olmadan PriceUtilities uygulamasını paylaştıklarını biliyorum. PriceUtilities'de bir hata bulsaydım, onu tek bir yerde düzeltebilirdim.

```
$p = new ShopProduct();
print $p->calculateTax(100) . "\n";

$u = new UtilityService();
print $u->calculateTax(100) . "\n";
```

### Birden Fazla Trait Kullanmak

Her birini use anahtar sözcüğünden sonra virgülle ayırıp listeleyerek bir sınıfa birden fazla trait ekleyebilirsiniz. Bu örnekte, orijinal PriceUtilities trait’imi koruyarak yeni bir trait olan IdentityTrait‘i tanımlayıp uyguluyorum:

```
trait IdentityTrait
{
    public function generateId(): string
    {
        return uniqid();
    }
}
```

Hem PriceUtilities hem de IdentityTrait'i use anahtar sözcüğüyle uygulayarak, calculateTax() ve generateId() metodlarını ShopProduct sınıfı için kullanılabilir hale getiriyorum.

```
class ShopProduct
{
    use PriceUtilities;
    use IdentityTrait;
}
```

**Not**: IdentityTrait, generateId() metodunu sağlar. Aslında, veritabanı genellikle nesneler için tanımlayıcılar üretir, ancak yerel uygulamalarda test amacıyla kullanabilirsiniz. Identity Map pattern’ini kapsayan Bölüm 13'te nesneler, veritabanları ve benzersiz tanımlayıcılar (unique identifiers) hakkında daha fazla bilgi edinebilirsiniz. Bölüm 18'de test etme hakkında daha fazla bilgi edinebilirsiniz.

Artık ShopProduct sınıfında hem generateId() hem de calculateTax() metodlarını çağırabilirim.

```
$p = new ShopProduct();
print $p->calculateTax(100) . "\n";
print $p->generateId() . "\n";
```

### Trait ve Arayüzleri Birleştirmek

Trait’ler yararlı olsalar da uygulandıkları sınıfın türünü değiştirmezler. Dolayısıyla, IdentityTrait‘i birden çok sınıfa uyguladığınızda, metot imzasında ima edilebilecek bir türü paylaşmazlar.

Neyse ki, trait’ler arayüzlerle iyi çalışıyor. generateId() metodu gerektiren bir arayüz tanımlayabilir ve ardından ShopProduct'ın bunu uyguladığını bildirebilirim:

```
interface IdentityObject
{
    public function generateId(): string;
}
```

ShopProduct'ın IdentityObject trait’ini yerine getirmesini istiyorsam, şimdi onun IdentityObject arayüzünü uygulamasını sağlamalıyım.

```
class ShopProduct implements IdentityObject
{
    use PriceUtilities;
    use IdentityTrait;
}
```

Daha önce olduğu gibi ShopProduct, IdentityTrait‘i kullanır. Ancak, bu içe aktarılan metot, artık IdentityObject arayüzü taahhüdünü de yerine getiriyor. Bu, ShopProduct nesnelerini, IdentityObject örneklerini talep etmek için tip ipuçlarını (type hinting) kullanan metotlara ve fonksiyonlara aktarabileceğimiz anlamına gelir:

```
public static function storeIdentityObject(IdentityObject $idobj)
{
    // do something with the IdentityObject
}
```

### Metot Adı Çakışmalarını Yönetmek

Trait’leri birleştirme yeteneği güzel bir özelliktir, ancak er ya da geç çakışmalar kaçınılmazdır. Örneğin, calculateTax() metodu sağlayan iki trait kullanırsam ne olacağını düşünün:

```
trait TaxTools
{
    public function calculateTax(float $price): float
    {
        return 222;
    }
}
```

calculateTax() metotlarını içeren iki trait eklediğim için, PHP hangisinin diğerini geçersiz kılacağını çözemiyor. Sonuç hatadır:

```
Fatal error: Trait method popp\ch04\batch06_3\TaxTools::calculateTax has
not been applied as
popp\ch04\batch06_3\UtilityService::calculateTax, because of collision with
popp\ch04\batch06_3\PriceUtilities::calculateTax in...
```

Bu sorunu çözmek için, insteadof anahtar sözcüğünü kullanabilirim:

```
class UtilityService extends Service
{
    use PriceUtilities;
    use TaxTools {
        TaxTools::calculateTax insteadof PriceUtilities;
    }
}
```

use ifadesine başka yönergeler uygulamak için önce gövde eklemeliyim. Bunu parantezler ile yapıyorum. Bu blok içinde insteadof operatörünü kullanıyorum. Bu, sol tarafta tam metot referansı (yani, hem trait hem de metot adlarını kapsam çözümleme operatörüyle ayrılmış olarak tanımlayan) gerektirir.

Sağ tarafta insteadof, eşdeğer metodu geçersiz kılması gereken trait’in adını gerektirir:

```
TaxTools::calculateTax insteadof PriceUtilities;
```

Önceki kod parçasının anlamı "PriceUtilities'de aynı adlı metot yerine TaxTools'un calculateTax() metodunu kullanın."

```
$u = new UtilityService();
print $u->calculateTax(100) . "\n";
```

TaxTools::calculateTax() içine yerleştirdiğim dummy çıktıyı alıyorum:

```
222
```

### Geçersiz Kılınan Trait Metotlarına Takma Ad Vermek

Metotlar arasındaki belirsizliği gidermek için insteadof kullanabileceğinizi gördük. Yine de, geçersiz kılınan metoda erişmek isterseniz ne yaparsınız? as operatörü, trait metotlarına takma ad vermenizi sağlar. Bir kez daha, as operatörü, sol tarafında bir metoda tam referans gerektirir. Operatörün sağ tarafına takma adı yazmalısınız. Bu nedenle burada, örneğin, basicTax() takma adını kullanarak PriceUtilities trait’inin calculateTax() metodunu eski haline getiriyorum:

```
class UtilityService extends Service
{
    use PriceUtilities;
    
    use TaxTools {
        TaxTools::calculateTax insteadof PriceUtilities;
        PriceUtilities::calculateTax as basicTax;
    }
}
```

Artık UtilityService sınıfı iki metot edinmiştir: calculateTax()'ın TaxTools sürümü ve basicTax() takma adıyla PriceUtilities sürümü. Bu metotları çalıştıralım:

```
$u = new UtilityService();
print $u->calculateTax(100) . "\n";
print $u->basicTax(100) . "\n";
```

Bu, aşağıdaki çıktıyı verir:

```
222
20
```

Böylece PriceUtilities::calculateTax(), basicTax() adı altında UtilityService sınıfının bir parçası olarak yeniden canlandırıldı.

**Not**: Metot adının trait’ler arasında çakışması durumunda, use bloğundaki metot adlarından birini takma adla adlandırmak yeterli değildir. Öncelikle insteadof operatörünü kullanarak hangi metodun diğerinin yerine geçtiğini belirlemelisiniz. Ardından, atılan metoda as operatörüyle yeni bir ad atayabilirsiniz.

Bu arada, ad çakışmasının olmadığı durumlarda da takma ad kullanabilirsiniz. Örneğin, üst sınıfta veya arayüzde bildirilen soyut metot imzasını uygulamak için trait metodu kullanmak isteyebilirsiniz.

### Trait’lerde Static Metot Kullanmak

Şimdiye kadar gördüğünüz örneklerin çoğu, örnek verilerini depolamadıkları için static metotlar kullanabilir. Trait’e static metot yerleştirmenin karmaşık bir tarafı yoktur. Burada, PriceUtilities::$taxrate property’sini ve PriceUtilities::calculateTax() metodunu static olacak şekilde değiştiriyorum:

```
trait PriceUtilities
{
    private static int $taxrate = 20;

    public static function calculateTax(float $price): float
    {
        return ((self::$taxrate / 100) * $price);
    }
    // other utilities
}
```

İşte UtilityService minimal biçimine geri döndü:

```
class UtilityService extends Service
{
    use PriceUtilities;
}
```

Tek yaptığı, PriceUtilities trait’ini kullanmak. Bununla birlikte, calculateTax() metodunun çağrılması söz konusu olduğunda önemli bir fark vardır:

```
print UtilityService::calculateTax(100) . "\n";
```

Şimdi metodu nesne yerine sınıfta çağırmalıyım. Tahmin edebileceğiniz gibi, bu kod satırı aşağıdakini verir:

```
20
```

Böylece, static metotlar trait’lerde bildirilir ve üst sınıf aracılığıyla normal şekilde erişilir.

### Trait’lerde Soyut Metot Tanımlamak

Trait’te soyut metot ları tıpkı sınıfta yaptığınız gibi tanımlayabilirsiniz. Trait bir sınıf tarafından kullanıldığında, bildirdiği herhangi bir soyut metodu uygulama taahhüdünü üstlenir.

**Not**: PHP 8'den önce, trait’lerde tanımlanan soyut metotlar için metot imzaları her zaman tam olarak uygulanmıyordu. Bu, bazı durumlarda parametre ve dönüş tiplerinin, uygulama sınıfında belirtilenlerden farklı olabileceği anlamına geliyordu. Bu boşluk artık kapatılmıştır.

Bu bilgiyle donanmış olarak, önceki örneğimi yeniden uygulayabilirim, böylece trait onu kullanan herhangi bir sınıfı tax rate bilgisi vermeye zorlar:

```
trait PriceUtilities
{
    public function calculateTax(float $price): float
    {
        // better design.. we know getTaxRate() is implemented
        return (($this->getTaxRate() / 100) * $price);
    }
    
    abstract public function getTaxRate(): float;
    // other utilities
}
```

PriceUtilities trait’inde soyut getTaxRate() metodu bildirerek, UtilityService sınıfını uygulama sağlamaya zorluyorum.

```
class UtilityService extends Service
{
    use PriceUtilities;
    
    public function getTaxRate(): float
    {
        return 20;
    }
}
```

Trait’teki soyut bildirim sayesinde, getTaxRate() metodu sağlamamış olsaydım, fatal error ile ödüllendirilirdim.

### Trait Metotlarına Erişim Haklarını Değiştirmek

Elbette trait metodunu public, private veya protected olarak tanımlayabilirsiniz. Ancak, bu erişimi trait’i kullanan sınıfın içinden de değiştirebilirsiniz. as operatörünün metot adına takma ad vermek için kullanılabileceğini zaten gördünüz. Bu operatörün sağ tarafında erişim değiştirici (access modifier) kullanırsanız, metodun adı yerine erişim düzeyini değiştirir.

Örneğin, UtilityService içinden calculateTax()'ı kullanmak istediğinizi, ancak kod uygulama için uygun hale getirmediğinizi hayal edin. use deyimini şu şekilde değiştirebilirsiniz:

```
class UtilityService extends Service
{
    use PriceUtilities {
        PriceUtilities::calculateTax as private;
    }

    public function __construct(private float $price)
    {
    }

    public function getTaxRate(): float
    {
        return 20;
    }
    
    public function getFinalPrice(): float
    {
        return ($this->price + $this->calculateTax($this->price));
    }
}
```

calculateTax() metoduna private erişim ayarlamak için as operatörünü private anahtar sözcüğüyle birlikte kullanıyorum. Bu, getFinalPrice() metoduna erişebileceğim anlamına gelir. calculateTax() metoduna dışarıdan bir erişim girişimi aşağıda verilmiştir:

```
$u = new UtilityService(100);
print $u->calculateTax() . "\n";
```

Ne yazık ki, bu kod hata üretecek:

```
Error: Call to private method popp\ch04\batch06_9\
UtilityService::calculateTax() from context ...
```

## Late Static Binding: Static Anahtar Kelimesi

Artık soyut sınıfları, trait’leri ve arayüzleri gördüğünüze göre, kısaca static metotlara dönme zamanı. Sınıf örneklerini oluşturmak için factory kullanılabileceğini gördünüz. Benim kadar tembel bir kodlayıcıysanız, bunun gibi bir örnek sizi rahatsız edebilir:

```
abstract class DomainObject
{
}

class User extends DomainObject
{
    public static function create(): User
    {
        return new User();
    }
}

class Document extends DomainObject
{
    public static function create(): Document
    {
        return new Document();
    }
}
```

DomainObject adında bir üst sınıf oluşturuyorum. Gerçek dünyadaki bir projede, elbette, bu, genişleyen sınıfların ortak işlevlerini içerecektir. Sonra iki alt sınıf oluşturuyorum, User and Document. Somut sınıflarımın static create() metotlarına sahip olmasını istiyorum.

Bu kod iyi çalışıyor, ancak can sıkıcı yinelemeler var. Her DomainObject alt sınıfı için bunun gibi bir kod oluşturmak istemiyorum. Bunun yerine, create() metodunu üst sınıfa taşımayı deneyeceğim:

```
abstract class DomainObject
{
    public static function create(): DomainObject
    {
        return new self();
    }
}
```

Güzel görünüyor. Artık tek bir yerde ortak kodum var ve sınıfa referans olarak self'i kullandım. Ancak self anahtar kelimesi hakkında bir varsayımda bulundum. Aslında, $this gibi davranmaz. self, arama bağlamına atıfta bulunmaz; çözüm bağlamını ifade eder. Önceki örneği çalıştırırsam şunu elde ederim:

```
Error: Cannot instantiate abstract class
popp\ch04\batch06\DomainObject
```

Böylece self, çağrıldığı sınıf olan Document'a değil, create()'in tanımlandığı yer olan DomainObject'i çözümler. PHP 5.3'e kadar bu, pek çok beceriksiz geçici çözümün ortaya çıkmasına neden olan ciddi bir sınırlamaydı. PHP 5.3, late static binding adı verilen bir kavram getirmiştir. Bu özelliğin en belirgin tezahürü static anahtar kelimesidir. static, kapsayıcı sınıftan ziyade çağrılan sınıfa atıfta bulunması dışında self'e benzer. Bu durum, Document::create() çağrısının, yeni bir Document nesnesiyle sonuçlandığı anlamına gelir.

```
abstract class DomainObject
{
    public static function create(): DomainObject
    {
        return new static();
    }
}

class User extends DomainObject
{
}

class Document extends DomainObject
{
}
```

Şimdi, alt sınıflardan birinde create()'i çağırırsak, artık bir hataya neden olmaz ve create()'i barındıran sınıfla değil, çağırdığımız sınıfla ilgili nesneyi geri alırız.

```
print_r(Document::create());
```

İşte çıktı.

```
popp\ch04\batch07\Document Object
(
)
```

Static anahtar kelimesi, örneklemeden daha fazlası için kullanılabilir. self ve parent gibi static olmayan bir bağlamdan bile static metot çağrıları için bir tanımlayıcı olarak kullanılabilir. Diyelim ki DomainObject sınıflarıma grup kavramını dahil etmek istiyorum.

Varsayılan olarak, yeni sınıflandırmamda, tüm sınıflar "default" kategorisine giriyor, ancak bunu miras hiyerarşimin bazı dalları için geçersiz kılmak istiyorum:

```
abstract class DomainObject
{
    private string $group;

    public function __construct()
    {
        $this->group = static::getGroup();
    }

    public static function create(): DomainObject
    {
        return new static();
    }

    public static function getGroup(): string
    {
        return "default";
    }
}

class User extends DomainObject
{
}

class Document extends DomainObject
{
    public static function getGroup(): string
    {
        return "document";
    }
}

class SpreadSheet extends Document
{
}

print_r(User::create());
print_r(SpreadSheet::create());
```

DomainObject sınıfına bir kurucu tanıttım. Static bir metodu çağırmak için static anahtar sözcüğünü kullanır. DomainObject varsayılan uygulamayı sağlar, ancak Document bunu geçersiz kılar. Ayrıca Document'ı genişleten yeni bir SpreadSheet sınıfı oluşturdum. İşte çıktı:

```
popp\ch04\batch07\User Object (
    [group:popp\ch04\batch07\DomainObject:private] => default
)

popp\ch04\batch07\SpreadSheet Object (
    [group:popp\ch04\batch07\DomainObject:private] => document
)
```

User sınıfı için çok zekice bir şey olması gerekmez. DomainObject kurucusu getGroup()'u çağırır ve onu yerel olarak bulur. SpreadSheet söz konusu olduğunda, arama, SpreadSheet'in kendisinde çağrılan sınıfta başlar. Uygulama sağlamaz, bu nedenle Document sınıfındaki getGroup() metodu çağrılır. PHP 5.3'ten önce, burada sadece DomainObject sınıfında getGroup()'u arayan self anahtar sözcüğüyle takılıp kalırdım.

## Hataları İşlemek

İşler ters gidiyor. Dosyalar yanlış yerleştirilmiş, veritabanı sunucuları başlatılmamış durumda, URL'ler değiştirilmiş, XML dosyaları karıştırılmış, izinler doğru şekilde ayarlanmamış ve disk kotaları aşılmış. Liste uzayıp gidiyor. Sorunu tahmin etme mücadelesinde, basit bir yöntem bazen kendi hata işleme kodunun ağırlığı altında kalabilir.

Bir XML yapılandırma dosyasında verileri depolayan, alan ve ayarlayan basit bir Conf sınıfı:

```
class Conf
{
    private \SimpleXMLElement $xml;
    private \SimpleXMLElement $lastmatch;

    public function __construct(private string $file)
    {
        $this->xml = simplexml_load_file($file);
    }

    public function write(): void
    {
        file_put_contents($this->file, $this->xml->asXML());
    }

    public function get(string $str): ?string
    {
        $matches = $this->xml->xpath("/conf/item[@name=\"$str\"]");
        if (count($matches)) {
            $this->lastmatch = $matches[0];
            return (string)$matches[0];
        }
        return null;
    }
    
    public function set(string $key, string $value): void
    {
        if (!is_null($this->get($key))) {
            $this->lastmatch[0] = $value;
            return;
        }
        $conf = $this->xml->conf;
        $this->xml->addChild('item', $value)->addAttribute('name', $key);
    }
}
```

Conf sınıfı, ad değer çiftlerine erişmek için SimpleXml uzantısını kullanır. İşte çalışmak üzere tasarlandığı format:

```
<?xml version="1.0" ?>
<conf>
    <item name="user">bob</item>
    <item name="pass">newpass</item>
    <item name="host">localhost</item>
</conf>
```

Conf sınıfının kurucusu, simplexml_load_file()‘a ilettiği bir dosya yolunu kabul eder. Ortaya çıkan SimpleXmlElement nesnesini $xml adlı property’de saklar. get() metodu, verilen ada sahip öğeyi bulmak için XPath'i kullanır ve değerini döndürür. set() mevcut bir öğenin değerini değiştirir veya yeni bir öğe oluşturur. Son olarak, write() metodu yeni yapılandırma verilerini dosyaya kaydeder.

Çoğu örnek kod gibi, Conf sınıfı da oldukça basitleştirilmiştir. Özellikle var olmayan veya yazılamayan dosyaları işlemek için bir stratejisi yoktur. Görünüm açısından da iyimser. XML belgesinin iyi biçimlendirileceğini ve beklenen öğeleri içereceğini varsayar.

Bu hata koşullarını test etmek nispeten önemsizdir, ancak yine de ortaya çıkmaları durumunda bunlara nasıl yanıt vereceğime karar vermeliyim. Genelde iki seçenek vardır.

Önce uygulamayı durdurabilirim. Bu basit ama sert. Mütevazi sınıfım daha sonra çöken uygulamanın sorumluluğunu üstlenirdi. __construct() ve write() gibi metotlar, hataları saptamak için iyi yerleştirilmiş olsalar da, bunları nasıl ele alacaklarına karar verecek bilgiye sahip değiller.

Sınıfımdaki hatayı işlemek yerine, bir tür hata bayrağı döndürebilirim. Bu değer boole veya 0, -1 gibi bir tamsayılar olabilir. Bazı sınıflar, yazılımcının bir hatadan sonra daha fazla bilgi alabilmesi için hata dizesi veya bayrağı da ayarlar.

Pek çok PEAR paketi, hata oluştuğuna dair bildirim işlevi gören ve içinde hata mesajını içeren bir hata nesnesi (PEAR_Error örneği) döndürerek bu iki yaklaşımı birleştirir. Bu yaklaşım artık kullanımdan kaldırılmıştır, ancak pek çok sınıf güncellenmemiştir, çünkü kodlar genellikle eski davranışa bağlıdır.

Buradaki sorun, dönüş değerinin bozulmasıdır. Hataya açık metot her çağrıldığında dönüş türünü test etmek için yazılımcıya güvenmeniz gerekir. Bu riskli olabilir. Kimseye güvenmeyin!

Çağıran koda hata değeri döndürdüğünüzde, hatanın doğru şekilde ele alacağının garantisi yoktur. Eğer durum buysa, sorun baştan başlar. Yazılımcı, hata durumuna nasıl yanıt verileceğini belirlemeli, hatta belki farklı bir hata raporlama stratejisi uygulamalıdır.

### İstisnalar (Exceptions)

PHP 5, hataları ele almanın kökten farklı bir yolu olan istisnaları getirdi. PHP için farklı fakat Java veya C++ deneyiminiz varsa, onları akıl almaz derecede tanıdık bulacaksınız. İstisnalar, bu bölümde şimdiye kadar dile getirdiğim tüm sorunları ele alır.

İstisna, yerleşik Exception sınıfından (veya türetilmiş bir sınıftan) örneklenen özel bir nesnedir.

Exception türündeki nesneler, hata bilgilerini tutmak ve raporlamak için tasarlanmıştır.

Exception sınıfı kurucusu, ileti dizesi ve hata kodu olmak üzere isteğe bağlı iki parametre kabul eder. Sınıf, hata koşullarını analiz etmek için bazı yararlı metotlar sağlar. Bunlar Tablo 4-1'de açıklanmıştır.

Exception sınıfı, hata bildirimi ve hata ayıklama bilgileri sağlamak için oldukça yararlıdır (getTrace() ve getTraceAsString() metotları bu açıdan özellikle yararlıdır). Aslında, daha önce tartışılan PEAR_Error sınıfıyla neredeyse aynıdır. Yine de, bir istisna için sahip olduğu bilgilerden çok daha fazlası vardır.

**Tablo 4-1**. İstisna Sınıfının Genel Yöntemleri

| Metot | Açıklama |
| --- | --- |
| getMessage() | Kurucuya iletilen mesaj dizesini alın |
| getCode() | Kurucuya iletilen kod tamsayısını alın |
| getFile() | İstisnanın oluşturulduğu dosyayı alın |
| getLine() | İstisnanın oluşturulduğu satır numarasını alın |
| getPrevious() | İç içe geçmiş istisna nesnesi alın |
| getTrace() | Metot, sınıf, dosya ve parametre verileri dahil olmak üzere istisnaya yol açan metot çağrılarını izleyen çok boyutlu bir dizi elde edin |
| getTraceAsString() | getTrace() tarafından döndürülen verilerin dize sürümünü alın |
| __toString() | İstisna nesnesi dize bağlamında kullanıldığında otomatik olarak çağrılır. İstisna ayrıntılarını açıklayan bir dize döndürür. |

**Exception Fırlatmak**

throw anahtar sözcüğü, bir Exception nesnesiyle bağlantılı olarak kullanılır. Geçerli metodun yürütülmesini durdurur ve hatayı işleme sorumluluğunu çağıran koda geri verir. Burada, throw deyimini kullanmak için __construct() metodunu değiştiriyorum:

```
public function __construct(private string $file)
{
    if (! file_exists($file)) {
        throw new \Exception("file '{$file}' does not exist");
    }
    $this->xml = simplexml_load_file($file);
}
```

write() metodu benzer bir yapı kullanabilir:

```
public function write(): void
{
    if (! is_writeable($this->file)) {
        throw new \Exception("file '{$this->file}' is not writeable");
    }
    print "{$this->file} is apparently writeable\n";
    file_put_contents($this->file, $this->xml->asXML());
}
```

```
try {
    $conf = new Conf("/tmp/conf01.xml");
    //$conf = new Conf( "/root/unwriteable.xml" );
    //$conf = new Conf( "nonexistent/not_there.xml" );
    print "user: " . $conf->get('user') . "\n";
    print "host: " . $conf->get('host') . "\n";
    $conf->set("pass", "newpass");
    $conf->write();
} catch (\Exception $e) {
    // handle error in some way
}
```

Gördüğünüz gibi, catch bloğu yüzeysel olarak bir metot bildirimine benziyor. Exception fırlatıldığında, catch bloğu çağrılır. Exception nesnesi, parametre olarak otomatik iletilir.

Exception fırlatıldığında kontrol doğrudan catch bloğuna geçer. Orada, herhangi bir hata kurtarma görevini gerçekleştirebilirsiniz. Bir hatadan kurtulamazsanız, her zaman yeni bir exception fırlatabilirsiniz:

```
} catch (\Exception $e) {
    // handle error in some way
    // or
    throw new \Exception("Conf error: " . $e->getMessage());
}
```

Alternatif olarak, size verilen istisnayı yeniden yazabilirsiniz:

```
try {
    $conf = new Conf("nonexistent/not_there.xml");
} catch (\Exception $e) {
    // handle error...
    // or rethrow
    throw $e;
}
```

Hata işlerken Exception nesnesinin kendisine ihtiyacınız yoksa, PHP 8'den itibaren exception argümanını tamamen atlayabilir ve sadece türü belirtebilirsiniz:

```
try {
    $conf = new Conf("nonexistent/not_there.xml");
} catch (\Exception) {
    // handle error without using the Exception object
}
```

**İstisnaları Genişletmek**

Herhangi bir sınıfta yaptığınız gibi, Exception sınıfını genişleten sınıflar oluşturabilirsiniz. Bunu yapmak istemenizin iki nedeni olabilir. İlk olarak, sınıfın işlevselliğini genişletebilirsiniz. İkinci olarak, türetilmiş sınıfın yeni bir sınıf türü tanımlaması, kendi başına hata işlemeye yardımcı olabilir.

Aslında try deyimi için ihtiyaç duyduğunuz kadar çok catch bloğu tanımlayabilirsiniz. Çağrılan catch bloğu, fırlatılan exception’ın türüne ve parametre listesindeki sınıf türüne bağlı olacaktır. İstisna'yı genişleten bazı basit sınıflar:

```
class XmlException extends \Exception
{
    public function construct(private \LibXmlError $error)
    {
        $shortfile = basename($error->file);
        $msg = "[{$shortfile}, line {$error->line}, col {$error->column}]
        {$error->message}";
        $this->error = $error;
        parent:: __construct($msg, $error->code);
    }
    
    public function getLibXmlError(): \LibXmlError
    {
        return $this->error;
    }
}
```

```
class FileException extends \Exception
{
}
```

```
class ConfException extends \Exception
{
}
```

LibXmlError sınıfı, SimpleXml bozuk bir XML dosyasıyla karşılaştığında arka planda oluşturulur. $message ve $code property’lerine sahiptir ve Exception sınıfına benzer. Bu benzerlikten yararlanarak XmlException sınıfında LibXmlError nesnesini kullanıyorum. FileException ve ConfException sınıfları, Exception alt sınıfından farklı bir şey yapmaz. Artık bu sınıfları kodumda kullanabilir ve hem construct() hem de write() öğelerini değiştirebilirim:

```
public function __construct(private string $file)
{
    if (! file_exists($file)) {
        throw new FileException("file '$file' does not exist");
    }
    $this->xml = simplexml_load_file($file, null, LIBXML_NOERROR);
    if (! is_object($this->xml)) {
        throw new XmlException(libxml_get_last_error());
    }
    $matches = $this->xml->xpath("/conf");
    if (! count($matches)) {
        throw new ConfException("could not find root element: conf");
    }
}

public function write(): void
{
    if (! is_writeable($this->file)) {
        throw new FileException("file '{$this->file}' is not writeable");
    }
    file_put_contents($this->file, $this->xml->asXML());
}
```

__construct(), karşılaştığı hatanın türüne bağlı olarak XmlException, FileException veya ConfException fırlatır. LIBXML_NOERROR flag’ini simplexml_load_file() fonksiyonuna parametre olarak verdiğime dikkat edin. Bu, uyarıları bastırır ve XmlException sınıfımla bunları işlemek için beni özgür bırakır. Hatalı biçimlendirilmiş bir XML dosyasıyla karşılaşırsam, simplexml_load_file() nesne döndürmeyeceğinden hata oluştuğunu biliyorum. Daha sonra libxml_get_last_error() kullanarak hataya erişebilirim.

$file property’si yazılamaz bir varlığa işaret ediyorsa write() metdu FileException fırlatır.

Böylece, __construct()'ın olası üç istisnadan birini oluşturabileceğini belirledim. Bundan nasıl yararlanabilirim? İşte bir Conf nesnesini başlatan bazı kodlar:

```
class Runner
{
    public static function init()
    {
        try {
            $conf = new Conf(__DIR__ . "/conf.broken.xml");
            print "user: " . $conf->get('user') . "\n";
            print "host: " . $conf->get('host') . "\n";
            $conf->set("pass", "newpass");
            $conf->write();
        } catch (FileException $e) {
            // permissions issue or non-existent file throw $e;
        } catch (XmlException $e) {
            // broken xml
        } catch (ConfException $e) {
            // wrong kind of XML file
        } catch (\Exception $e) {
            // backstop: should not be called
        }
    }
}
```

Her sınıf türü için bir catch bloğu sağlıyorum. Çağrılan blok, fırlatılan exception tipine bağlıdır. İlk eşleşen çalıştırılacaktır, bu nedenle en genel türü sona ve en özel türü başlangıca yerleştirmeyi unutmayın. Örneğin, Exception için catch bloğunu XmlException ve ConfException bloğunun önüne yerleştirecek olsaydınız, bunların hiçbiri çağrılmazdı. Bunun nedeni, bu sınıfların her ikisinin de Exception türüne ait olması ve dolayısıyla ilk testle eşleşecek olmasıdır.

Yapılandırma dosyasında bir sorun varsa (dosya mevcut değilse veya yazılamıyorsa) ilk catch bloğu (FileException) çağrılır. İkinci blok (XmlException), XML dosyasını ayrıştırırken bir hata oluşursa (örneğin, bir öğe kapalı değilse) çağrılır. Üçüncü blok (ConfException), geçerli bir XML dosyası beklenen kök conf öğesini içermiyorsa çağrılır. Son bloğa (Exception) ulaşılmamalı çünkü benim metotlarım yalnızca açıkça ele alınan üç exception oluşturuyor. Geliştirme sırasında koda yeni istisnalar eklemeniz durumunda, bunun gibi bir "backstop" bloğuna sahip olmak genellikle iyi bir fikirdir.

**Not**: Bir "backstop” yakalama bloğu kullanırsanız, çoğu durumda istisna hakkında gerçekten bir şeyler yaptığınızdan emin olmalısınız; sessizce fail etmek, teşhis edilmesi zor hatalara neden olabilir.

Bu ayrıntılı catch bloklarının yararı, farklı hatalara farklı kurtarma veya fail mekanizmaları uygulamanıza izin vermesidir. Örneğin, yürütmeyi sonlandırmaya, hatayı loglara kaydedip devam etmeye veya açıkça bir exception fırlatmaya karar verebilirsiniz.

Burada oynayabileceğiniz başka bir numara, mevcut istisnayı saran yeni bir istisna oluşturmaktır. Bu, yakaladığınız istisna tarafından özetlenen verileri korurken kendi bağlamsal bilgilerinizi eklemenize olanak tanır. Bölüm 15'te bu teknik hakkında daha fazla bilgi edinebilirsiniz.

Peki yazılımcı tarafından istisna yakalanmazsa ne olur? Örtük olarak yeniden fırlatılır ve yazılımcının kendi kodunda onu yakalama fırsatı verilir. Bu süreç ya istisna yakalanana kadar ya da artık fırlatılamayana kadar devam eder. Bu noktada fatal error oluşur. Örneğimdeki istisnalardan birini yakalamazsam ne olur:

```
PHP Fatal error: Uncaught exception 'FileException' with message
'file 'nonexistent/not_there.xml' does not exist' in ...
```

Bu nedenle, bir istisna oluşturduğunuzda, yazılımcıyı bununla ilgilenme sorumluluğunu almaya zorlarsınız. Bu sorumluluktan kaçmak değildir. Metod bir hata algıladığında, ancak bunu akıllıca işleyebilmek için bağlamsal bilgiye sahip olmadığında exception fırlatılmalıdır. Örneğimdeki write() metodu, yazma girişiminin ne zaman başarısız olacağını ve nedenini biliyor ama bu konuda ne yapacağını bilmiyor. Bu olması gerektiği gibi. Conf sınıfını şu anda olduğundan daha bilgili hale getirirsem, odak noktasını kaybeder ve daha az tekrar kullanılabilir hale gelir.

**try/catch Bloklarını finally ile Temizlemek**

Kod akışının istisnalardan etkilenme şekli beklenmeyen sorunlara neden olabilir. Örneğin, try bloğu içinde istisna oluşturulduktan sonra temizlik işlemleri gerçekleştirilemeyebilir. Gördüğünüz gibi try bloğu içinde bir istisna üretilirse akış doğrudan ilgili catch bloğuna gider. Veritabanı bağlantılarını veya dosya tanıtıcılarını kapatan kod çağrılamayabilir ve durum bilgileri güncellenmeyebilir.

Örneğin, Runner::init()'in eylemlerinin log’unu tuttuğunu hayal edin. Başlatma işlemini, karşılaşılan hataları log’a kaydeder ve ardından sonucu günlüğe kaydeder. Burada, bu tür log kaydının tipik olarak basitleştirilmiş bir örneğini sunuyorum:

```
public static function init(): void
{
    try {
        $fh = fopen("/tmp/log.txt", "a"); fputs($fh, "start\n");
        $conf = new Conf(dirname( FILE ) . "/conf.broken.xml");
        print "user: " . $conf->get('user') . "\n";
        print "host: " . $conf->get('host') . "\n";
        $conf->set("pass", "newpass");
        $conf->write();
        fputs($fh, "end\n");
        fclose($fh);
    } catch (FileException $e) {
        // permissions issue or non-existent file
        fputs($fh, "file exception\n");
        throw $e;
    } catch (XmlException $e) {
        fputs($fh, "xml exception\n");
        // broken xml
    } catch (ConfException $e) {
        fputs($fh, "conf exception\n");
        // wrong kind of XML file
    } catch (\Exception $e) {
        fputs($fh, "general exception\n");
        // backstop: should not be called
    }
}
```

Bir dosya açıyorum, log.txt; ona yazıyorum; ve sonra yapılandırma kodumu çağırıyorum. Bu süreçte bir istisna ile karşılaşılırsa, bunu ilgili catch bloğuna kaydederim. Log’a yazıp dosyayı kapatarak try bloğunu sonlandırıyorum.

Elbette, bir istisna ile karşılaşılırsa bu son adıma asla ulaşılamaz. Akış doğrudan ilgili catch bloğuna geçer ve try bloğunun geri kalanı asla çalıştırılmaz. İşte bir XML istisnası oluşturulduğunda log çıktısı:

```
start
xml exception
```

Gördüğünüz gibi, log’a kaydetme başladı ve file exception not edildi, ancak log’un sonunu kaydeden kod bölümüne hiçbir zaman ulaşılamadı ve bu nedenle log bununla güncellenmedi. 

Çözümün, son kayıt adımını try/catch bloğunun tamamen dışına yerleştirmek olduğunu düşünebilirsiniz. Bu güvenilir bir şekilde çalışmaz. Oluşturulan bir istisna yakalanırsa ve try bloğu yürütmenin devam etmesine izin verirse, akış try/catch yapısının ötesine geçer. Ancak, bir catch bloğu exception’ı yeniden fırlatabilir veya komut dosyasının yürütülmesini tamamen sonlandırabilir.

Programcıların bu gibi sorunlarla başa çıkmalarına yardımcı olmak için PHP 5.5 yeni bir anahtar sözcük ekledi: finally.  Java'ya aşina iseniz, muhtemelen bunu daha önce görmüşsünüzdür. catch blokları yalnızca eşleşen exception’lar fırlatıldığında koşullu olarak çalıştırılsa da, try bloğu içinde bir istisna oluşturulsun veya oluşturulmasın, her zaman finally bloğu çalıştırılır.

Log kodumu finally bloğuna yaklaştırarak bu sorunu çözebilirim:

```
public static function init2(): void
{
    $fh = fopen("/tmp/log.txt", "a");
    try {
        fputs($fh, "start\n");
        $conf = new Conf(dirname( FILE ) . "/conf.not-there.xml");
        print "user: " . $conf->get('user') . "\n";
        print "host: " . $conf->get('host') . "\n";
        $conf->set("pass", "newpass");
        $conf->write();
    } catch (FileException $e) {
        // permissions issue or non-existent file
        fputs($fh, "file exception\n");
        //throw $e;
    } catch (XmlException $e) {
        fputs($fh, "xml exception\n");
        // broken xml
    } catch (ConfException $e) {
        fputs($fh, "conf exception\n");
        // wrong kind of XML file
    } catch (Exception $e) {
        fputs($fh, "general exception\n");
        // backstop: should not be called
    } finally {
        fputs($fh, "end\n");
        fclose($fh);
    }
}
```

Log yazma ve fclose() çağrısı finally bloğu ile sarıldığı için, bu ifadeler, FileException yakalandığında olduğu gibi, exception yeniden fırlatılsa bile çalıştırılacaktır.

Şimdi, FileException oluşturulduğunda log metni şöyledir:

```
start
file exception
end
```

**Not**: Çağrılan catch bloğu exception’ı yeniden fırlatırsa veya bir değer döndürürse, finally bloğu çalıştırılır. Ancak, try veya catch bloğunda die() veya exit() öğesinin çağrılması komut dosyasının yürütülmesini sonlandırır ve finally bloğu çalıştırılmaz.

## Final Sınıf ve Metotlar

Kalıtım, sınıf hiyerarşisi içinde muazzam bir esneklik sağlar. Bir sınıfı veya metodu geçersiz kılabilirsiniz, böylece yazılımcının yapacağı çağrı, sınıf örneğine göre kökten farklı etkiler elde eder. Ancak bazen sınıf veya metot sabit ve değişmeden kalmalıdır. Sınıfınız veya metodunuz için kesin işlevselliğe ulaştıysanız ve bunu geçersiz kılmanın yalnızca çalışmanızın nihai mükemmelliğine zarar vereceğini düşünüyorsanız, final anahtar kelimesine ihtiyacınız olabilir.

final kalıtıma son verir. final sınıf alt sınıflara ayrılamaz ve final metot geçersiz kılınamaz.

İşte final sınıf:

```
final class Checkout
{
    // ...
}
```

Checkout sınıfından alt sınıf oluşturma girişimi:

```
class IllegalCheckout extends Checkout
{
    // ...
}
```

Bu bir hata üretir:

```
Fatal error: Class popp\ch04\batch13\IllegalCheckout may not inherit
from final class (popp\ch04\batch13\Checkout) in ...
```

Tüm sınıf yerine final metot tanımlayarak işleri biraz rahatlatabilirim. final anahtar sözcüğü, aşağıdaki gibi, protected ve static gibi diğer erişim belirteçlerinin önüne yerleştirilmelidir:

```
class Checkout
{
    final public function totalize(): void
    {
        // calculate bill
    }
}
```

Artık Checkout'un alt sınıflarını oluşturabilirim, ancak totalize() metodunu geçersiz kılmaya yönelik herhangi bir girişim, hataya neden olur:

```
class IllegalCheckout extends Checkout
{
    final public function totalize(): void
    {
        // change bill calculation
    }
}

Fatal error: Cannot override final method popp\ch04\batch14\
Checkout::totalize() in /var/popp/src/ch04/batch14/IllegalCheckout.php on line 9
```

İyi nesne yönelimli kod, iyi tanımlanmış arayüzü vurgulama eğilimindedir. Bununla birlikte, arayüzün arkasında, uygulamalar genellikle değişiklik gösterecektir. Farklı sınıflar veya sınıf kombinasyonları, ortak arayüzlere uygundur ancak farklı koşullarda farklı davranır. Bir sınıf veya metodu final olarak tanımlarsanız bu esnekliği sınırlandırırsınız. Bunun istendiği zamanlar olacaktır ve bunlardan bazılarını kitabın ilerleyen kısımlarında göreceksiniz.

Ancak, bir şeyi final olarak tanımlamadan önce dikkatlice düşünmelisiniz. Geçersiz kılmanın yararlı olacağı hiçbir koşul yok mu? Elbette daha sonra her zaman fikrinizi değiştirebilirsiniz, ancak başkalarının kullanması için bir kütüphane dağıtıyorsanız bu o kadar kolay olmayabilir. final’ı dikkatli kullanın.

## Dahili Hata Sınıfı

İstisnalar ilk ortaya çıktığında, try ve catch dünyası, çekirdek motora değil, öncelikle PHP'de yazılmış koda uygulanıyordu. Dahili olarak üretilen hatalar kendi mantığını korudu. Temel hataları, kod tarafından oluşturulan istisnalarla aynı şekilde yönetmek istiyorsanız, bu durum karmaşık bir hal alabilir. PHP 7, Error sınıfıyla bu sorunu çözmeye başladı. Bu, Exception sınıfının uyguladığı yerleşik arayüzün aynısı olan Throwable'ı uygular ve bu nedenle aynı şekilde ele alınabilir. Bu aynı zamanda Tablo 4-2’de açıklanan metotların da dikkate alındığı anlamına gelir. Error, özel hata türleri için alt sınıflara ayrılır. eval ifadesi tarafından oluşturulan ayrıştırma hatasını şu şekilde yakalayabilirsiniz:

```
try {
    eval("illegal code");
} catch (\Error $e) {
    print get_class($e) . "\n";
    print $e->getMessage();
} catch (\Exception $e) {
    // do something with an Exception
}
```

İşte çıktı:

```
ParseError
syntax error, unexpected identifier "code"
```

Böylece, bazı dahili hata türlerini catch bloklarında, Error üst sınıfını belirterek veya daha spesifik bir alt sınıf belirleyerek eşleştirebilirsiniz. Tablo 4-2 mevcut Error alt sınıflarını gösterir.

**Tablo 4-2**. PHP 7 Tarafından Sunulan Yerleşik Hata Sınıfları

| Fonksiyon | Tip |
| --- | --- |
| ArgumentCountError | Kullanıcı tanımlı bir metot veya fonksiyona çok az argüman iletildiğinde fırlatılır |
| ArithmeticError | Matematikle ilgili hatalar için fırlatılır - özellikle bitsel aritmetik ile ilgili olanlar |
| AssertionError | assert() dil yapısı başarısız olduğunda (hata ayıklama) fırlatılır |
| CompileError | PHP kodu hatalı olduğunda ve çalıştırmak için derlenemediğinde fırlatılır |
| DivisionByZeroError | Bir sayı sıfıra bölünmek istendiğinde fırlatılır |
| ParseError | PHP'yi ayrıştırmak için bir çalışma zamanı girişimi (örneğin, eval() kullanmak) başarısız olduğunda fırlatılır |
| TypeError | Metoda yanlış türde bir parametre iletildiğinde, metot yanlış türde bir değer döndürdüğünde veya metoda yanlış sayıda parametre iletildiğinde fırlatılır. |

## Interceptor’lar ile Çalışmak

PHP, tanımsız metot ve property’lere gönderilen mesajları yakalayabilen yerleşik interceptor metodlarını sağlar. Bu aynı zamanda aşırı yükleme (overloading) olarak da bilinir, ancak bu terim Java ve C++'da oldukça farklı bir anlama geldiği için, interception açısından konuşmanın daha iyi olacağını düşünüyorum.

PHP, çeşitli yerleşik interceptor veya “sihirli” metodları destekler. __construct() gibi, bunlar da doğru koşullar oluştuğunda sizin için çağrılır. Tablo 4-3’te bu metotlardan bazıları açıklanmaktadır.

**Tablo 4-3**. Interceptor Metotları

| Metot | Açıklama |
| --- | --- |
| __get($property) | Tanımlanmamış property’e erişildiğinde çağrılır |
| __set($property, $value) | Tanımlanmamış property’e değer atandığında çağrılır |
| __isset($property) | Tanımlanmamış property üzerinde isset() çağrıldığında çağrılır |
| __unset($property) | Tanımlanmamış property üzerinde unset() çağrıldığında çağrılır |
| __call($method, $arg_array) | Tanımsız, static olmayan bir metot çağrıldığında çağrılır |
| __callStatic($method, $arg_array) | Tanımsız, static bir metot çağrıldığında çağrılır |

**Not**: Interceptor veya sihirli metotlar hakkında daha fazla bilgiyi PHP kılavuzunda bulabilirsiniz: www.php.net/manual/en/language.oop5.magic.php.

__get() ve __set() metotları, sınıfta (veya onun ebeveynlerinde) bildirilmemiş property’lerle çalışmak için tasarlanmıştır.

Yazılımcı bildirilmemiş bir property’i okumaya çalıştığında __get() çağrılır. Yazılımcının erişmeye çalıştığı property’nin adını içeren tek bir string parametre ile otomatik olarak çağrılır. __get() metodundan döndürdüğünüz her şey, hedef property bu değere sahipmiş gibi geri gönderilir. İşte bir örnek:

```
class Person
{
    public function __get(string $property): mixed
    {
        $method = "get{$property}";
        if (method_exists($this, $method)) {
            return $this->$method();
        }
    }

    public function getName(): string
    {
        return "Bob";
    }
    
    public function getAge(): int
    {
        return 44;
    }
}
```

Tanımsız bir property’e erişilmeye çalışıldığında, __get() metodu çağrılır. Property adını almak ve başına “get” sözcüğünü koyarak yeni bir dize oluşturmak için __get()'i uyguladım. Bu dizeyi, nesne ve metot adını kabul eden ve metodun varlığını test eden method_exists() adlı fonksiyona iletiyorum. Metot varsa, onu çağırırım ve dönüş değerini iletirim. $name property’sinin istendiğini varsayalım:

```
$p = new Person();
print $p->name;
```

Bu durumda, getName() metodu arka planda çağrılır:

```
Bob
```

Metod yoksa, kullanıcının erişmeye çalıştığı property null olarak çözümlenecektir.

__isset() metodu, __get() metoduna benzer şekilde çalışır. Yazılımcı, tanımsız bir property üzerinde isset() öğesini çağırdıktan sonra çağrılır. Person’ı şu şekilde genişletebilirim:

```
public function __isset(string $property): bool
{
    $method = "get{$property}";
    return (method_exists($this, $method));
}
```

Artık dikkatli bir kullanıcı, property’i üzerinde çalışmadan önce test edebilir: 

```
$p = new Person();
if (isset($p->name)) {
    print $p->name;
}
```

Tanımsız bir property’e atama yapmaya çalıştığınızda __set() metodu çağrılır. İki parametre iletilir: property adı ve ayarlamaya çalışılan değer. Daha sonra bu değişkenlerle nasıl çalışacağınıza karar verebilirsiniz. Burada, Person sınıfını değiştirmeye devam ediyorum:

```
class Person
{
    private ?string $myname;
    private ?int $myage;

    public function __set(string $property, mixed $value): void
    {
        $method = "set{$property}";
        if (method_exists($this, $method)) {
            $this->$method($value);
        }
    }

    public function setName(?string $name): void
    {
        $this->myname = $name;
        if (!is_null($name)) {
            $this->myname = strtoupper($this->myname);
        }
    }
    
    public function setAge(?int $age): void
    {
        $this->myage = $age;
    }
}
```

Bu örnekte, “getters” yerine “setters” metodlarla çalışıyorum. Yazılımcı tanımsız bir property’e değer atamaya çalışırsa, property adı ve atanan değerle __set() metodu çağrılır. Uygun metodun varlığını test ederim ve varsa onu çağırırım. Bu şekilde atanan değeri filtreleyebilirim.

**Not**: PHP belgelerindeki metot ve özelliklerden, sınıflarıyla özdeşleştirilmeleri için sıklıkla static terimlerle bahsedildiğini unutmayın. Dolayısıyla, property static olarak bildirilmese ve aslında bir nesne aracılığıyla erişilecek olsa bile, Person::$name property’si hakkında konuşabilirsiniz.

Bu nedenle, bir Person nesnesi oluşturur ve ardından Person::$name adlı bir property’e değer atamaya çalışırsam, bu sınıf $name property’sini tanımlamadığından __set() metodu çağrılır. Metot, “name” dizesini ve yazılımcının atadığı değeri iletir. Değerin daha sonra nasıl kullanılacağı __set()'in uygulanmasına bağlıdır. Bu örnekte, property adını “set” dizesiyle birleştirip bir metot adı oluşturuyorum. setName() metodu bulunur ve gereken şekilde çağrılır. Bu, gelen değeri dönüştürür ve bir property’de saklar:

```
$p = new Person();
$p->name = "bob";
// the $myname property becomes 'BOB'
```

Tahmin edebileceğiniz gibi, __unset() __set()'in tersidir. Tanımlanmamış bir property üzerinde unset() çağrıldığında, __unset() property’nin adıyla birlikte çağrılır. Daha sonra bu bilgilerle istediğinizi yapabilirsiniz. Aşağıdaki örnek, __set() tarafından kullanıldığını gördüğünüz teknik ile çözümlenen bir metoda null değerini iletir:

```
public function __unset(string $property): void
{
    $method = "set{$property}";
    if (method_exists($this, $method)) {
        $this->$method(null);
    }
}
```

__call() metodu, muhtemelen tüm interceptor metotları arasında en yararlı olanıdır. Yazılımcı tarafından tanımsız bir metot çağrıldığında çağrılır. __call(), metot adı ve iletilen tüm parametreleri tutan bir dizi ile çağrılır. __call() metodundan döndürdüğünüz herhangi bir değer, çağrılan metot tarafından döndürülmüş gibi istemciye döndürülür.

**Not**: __get(), __set() ve __call() erişilemeyen bir metot veya property’e (yani private veya protected olarak ayarlanan metot veya property’ler) erişilmeye çalışıldığında da otomatik olarak çağrılır.

## Yıkıcı (Destructor) Metot Tanımlamak

Bir nesne başlatıldığında __construct() metodunun otomatik olarak çağrıldığını gördünüz. PHP 5 ayrıca __destruct() metodunu da tanıttı. Bu, bir nesne çöp olarak toplanmadan hemen önce - yani bellekten silinmeden önce - çağrılır. Gerekli olabilecek herhangi bir son temizliği gerçekleştirmek için bu yöntemi kullanabilirsiniz.

Örneğin, kendisini veritabanına kaydeden bir sınıf düşünün. Bir örneğin silindiğinde verilerini kaydetmesini sağlamak için __destruct() metodunu kullanabilirim:

```
class Person
{
    private int $id;
    
    public function __construct(protected string $name, private int $age)
    {
        $this->name = $name;
        $this->age = $age;
    }

    public function setId(int $id): void
    {
        $this->id = $id;
    }

    public function __destruct()
    {
        if (!empty($this->id)) {
            // save Person data
            print "saving person\n";
        }
    }
}
```

__destruct() metodu, nesne üzerinde unset() işlevini her çağırdığınızda veya proseste nesneye başka referans olmadığında çağrılır. Dolayısıyla, Person nesnesi oluşturup yok edersem, __destruct() metodunun devreye girdiğini görebilirsiniz:

```
$person = new Person("bob", 44);
$person->setId(343);
unset($person);
```

İşte çıktı:

```
saving person
```

Bunun gibi numaralar eğlenceli olsa da, bir uyarıda bulunmaya değer. __call(), __destruct() ve arkadaşlarına bazen sihirli metotlar denir. Daha önce fantastik roman okuduysanız bileceğiniz gibi, sihir her zaman iyi bir şey değildir. Sihir keyfi ve beklenmediktir. Sihir kuralları esnetir. Sihir gizli maliyetler doğurur.

Örneğin, __destruct() durumunda, kullanıcıları istenmeyen sürprizlerle karşı karşıya bırakabilirsiniz. Person sınıfını düşünün, kendi __destruct() metodunda veritabanına yazma işlemi gerçekleştirir. Şimdi, acemi bir geliştiricinin, Person sınıfını adım adım ilerlettiğini hayal edin. __destruct() matodunu fark etmez ve bir dizi Person nesnesinin örneğini oluşturmaya başlar. Değerleri kurucuya ileterek CEO'nun gizli ve biraz müstehcen takma adını $name property’sine atar ve ardından $age'i 150 olarak ayarlar. Test script’ini birkaç kez çalıştırarak renkli ad ve yaş kombinasyonlarını dener.

Ertesi sabah, yöneticisi onu toplantı odasına çağırarak veritabanının neden aşağılayıcı Person verileri içerdiğini açıklamasını ister. Sihire güvenmeyin.

## __clone() ile Nesneleri Kopyalamak

PHP 4'te, nesne kopyalamak, bir değişkenden diğerine atama yapmak kadar basitti:

```
class CopyMe
{
}

$first = new CopyMe();
$second = $first;
// PHP 4: $second and $first are 2 distinct objects
// PHP 5 plus: $second and $first refer to one object
```

Değişkenler atandığında, metotlar çağrıldığında ve nesneler döndürüldüğünde yanlışlıkla nesne kopyaları oluşturulduğundan, bu "basit mesele" birçok hatanın kaynağıydı. Bu, aynı nesneye atıfta bulunup bulunmadığını görmek için iki değişkeni test etmenin bir yolu olmadığı gerçeğiyle daha da kötüleşti. Eşdeğerlik testleri size tüm alanların aynı olup olmadığını (==) veya her iki değişkenin de nesne olup olmadığını (===) söyler, ancak aynı nesneyi işaret edip etmediklerini söylemez.

PHP'de, nesne içeriyormuş gibi görünen bir değişken aslında temeldeki veri yapısına başvuran bir tanımlayıcı içerir. Böyle bir değişken metoda atandığında veya iletildiğinde, içerdiği tanımlayıcı kopyalanır. Ancak, her kopya aynı nesneyi işaret etmeye devam eder. Bu, önceki örneğimde, $first ve $second nesnenin iki kopyası yerine aynı nesneyi işaret eden tanımlayıcılar içerdiği anlamına gelir. Genellikle nesnelerle çalışırken istediğiniz şey bu olsa da, nesnenin kopyasını almanız gereken durumlar olacaktır.

```
$first = new CopyMe();
$second = clone $first;
// PHP 5 plus: $second and $first are 2 distinct objects
```

Nesne kopyalamayı çevreleyen sorunlar burada başlıyor. Önceki bölümde uyguladığım Person sınıfını ele alalım. Person nesnesinin kopyası, veritabanında doğru satırı bulmak için kullanacağım tanımlayıcıyı ($id property’si) içerir. Bu property’nin kopyalanmasına izin verirsem, aynı veri varlığını (veritabanı satırı) temsil eden iki ayrı nesne oluşabilir.

Neyse ki, nesne üzerinde clone çağrıldığında neyin kopyalanacağını kontrol edebilirsiniz. Bunu, __clone() adlı özel bir metodu uygulayarak yababilirsiniz (sihirli mettoların özelliği olan baştaki iki alt çizgiye dikkat edin). __clone(), nesne üzerinde clone anahtar sözcüğü çağrıldığında otomatik olarak çağrılır.

__clone() uyguladığınızda, metodun çalıştığı bağlamı anlamak önemlidir. __clone() kopyalanan nesne üzerinde çalıştırılır, orijinal üzerinde çalıştırılmaz. Burada, Person sınıfının başka bir versiyonuna __clone() ekliyorum:

```
class Person
{
    private int $id = 0;

    public function __construct(private string $name, private $age)
    {
    }

    public function setId(int $id): void
    {
        $this->id = $id;
    }
    
    public function __clone(): void
    {
        $this->id = 0;
    }
}
```

Person nesnesinde clone çağrıldığında, yeni bir yüzeysel kopya yapılır ve onun __clone() metodu çağrılır. Bu, __clone() içinde yaptığım herhangi bir şeyin, kopyanın üzerine yazacağı anlamına gelir. Bu durumda, kopyalanan nesnenin $id property’sinin sıfır olmasını sağlarım:

```
$person = new Person("bob", 44);
$person->setId(343);
$person2 = clone $person;
```

Yüzeysel kopya, basit özelliklerin eski nesneden yeni nesneye kopyalanmasını sağlar. Nesne property’lerinin tanımlayıcıları kopyalanır ancak temel verileri kopyalanmaz; bu, bir nesneyi klonlarken istediğiniz veya beklediğiniz şey olmayabilir. Person nesnesine bir Account nesnesi property’si verdiğimi varsayalım. Bu nesne, klonlanan nesneye kopyalanmasını istediğim bir bakiyeye sahiptir. Yine de istemediğim şey, her iki Person nesnesinin de aynı hesaba referanslar içermesidir:

```
class Account
{
    public function __construct(public float $balance)
    {
    }
}
```

```
class Person
{
    private int $id;

    public function __construct(
        private string $name,
        private int $age,
        public Account $account
    ) {
    }

    public function setId(int $id): void
    {
        $this->id = $id;
    }
    
    public function __clone(): void
    {
        $this->id = 0;
    }
}
```

```
$person = new Person("bob", 44, new Account(200));
$person->setId(343);
$person2 = clone $person;
// give $person some money
$person->account->balance += 10;
// $person2 sees the credit too
print $person2->account->balance;
```

Bu, aşağıdaki çıktıyı verir:

```
210
```

$person, herkesin erişimine açık tuttuğum bir Account nesnesine referans tutuyor (bildiğiniz gibi, genellikle property’e erişimi kısıtlar, gerekirse accessor metot sağlardım). Klon oluşturulduğunda, aynı Account nesnesine ait bir referansı da içerir. Bunu, $person nesnesinin Account’una ekleyerek ve artan bakiyeyi $person2 aracılığıyla onaylayarak gösteriyorum.

Klonlama işleminden sonra nesne property’sinin paylaşılmasını istemiyorsam, bunu __clone() metodunda açıkça klonlamak bana kalmıştır:

```
public function __clone(): void
{
    $this->id = 0;
    $this->account = clone $this->account;
}
```

## Nesneleriniz İçin String Değerleri Tanımlamak

PHP 5 tarafından tanıtılan Java'dan ilham alan başka bir özellik de __toString() metoduydu. PHP 5.2'den önce, bir nesneyi yazdırdığınızda, şuna benzer bir dizeye çözümlenirdi:

```
class StringThing
{
}

$st = new StringThing();
print $st;
```

PHP 5.2'den bu yana, bu kod şöyle bir hata üretecektir:

```
Object of class popp\ch04\batch22\StringThing could not be converted to
string ...
```

__toString() metodunu uygulayarak, dize bağlamında erişildiğinde (veya açıkça bir dizeye dönüştürüldüğünde) nesnelerinizin kendilerini nasıl temsil ettiğini kontrol edebilirsiniz. Bir dizi değeri döndürmek için __toString() yazılmalıdır. Metod, nesneniz print veya echo’ya iletildiğinde otomatik olarak çağrılır ve dönüş değeri değiştirilir. Burada, Person sınıfına __toString() ekliyorum:

```
class Person
{
    public function getName(): string
    {
        return "Bob";
    }

    public function getAge(): int
    {
        return 44;
    }
    
    public function __toString(): string
    {
        $desc = $this->getName() . " (age ";
        $desc .= $this->getAge() . ")";
        return $desc;
    }
}
```

Şimdi Person nesnesini yazdırdığımda, nesne şu şekilde çözümlenecek:

```
$person = new Person();
print $person;
Bob (age 44)
```

__toString() metodu, ana görevi bilgi iletmek olan sınıfların yanı sıra log kaydı ve hata raporlama için özellikle yararlıdır. Örneğin, Exception sınıfı, istisna verilerini __toString() metodunda özetler.

PHP 8'den itibaren, __toString() metodunu uygulayan herhangi bir sınıfın, yerleşik Stringable arayüzünü uyguladığı dolaylı olarak bildirilir. Bu, parametre ve property’leri kısıtlamak için bileşik tür bildirimi kullanabileceğiniz anlamına gelir. İşte bir örnek:

```
public static function printThing(string|\Stringable $str): void
{
    print $str;
}
```

Bir dizeyi veya Person nesnesini printThing() metoduna iletebiliriz ve seçtiğimiz herhangi bir dize benzeri şeyle çalışabileceğinden emin olabiliriz.

## Callback’ler, Anonim Fonksiyonlar ve Closure’lar

Kesin olarak nesne yönelimli bir özellik olmasa da, anonim fonksiyonlar, callback kullanan nesne yönelimli uygulamalarda karşılaşabileceğiniz için burada bahsedeceğiz. 

**Not**: Callback, bir değişkende saklanabilen veya daha sonra çağırmak üzere metot ve fonksiyonlara aktarılabilen yürütülebilir kod bloğudur.

İşte birkaç sınıf:

```
class Product
{
    public function __construct(public string $name, public float $price)
    {
    }
}
```

```
class ProcessSale
{
    private array $callbacks;

    public function registerCallback(callable $callback): void
    {
        $this->callbacks[] = $callback;
    }
    
    public function sale(Product $product): void
    {
        print "{$product->name}: processing \n";
        foreach ($this->callbacks as $callback) {
            call_user_func($callback, $product);
        }
    }
}
```

Bu kod, çeşitli callback’leri çalıştırmak için tasarlanmıştır. Product ve ProcessSale olmak üzere iki sınıftan oluşur. Product, yalnızca $name ve $price property’lerini saklar. 

ProcessSale iki metottan oluşur. İlki olan registerCallback(), callable türü kabul eder ve onu $callbacks dizi property’sine ekler. İkinci metot olan sale(), Product nesnesini kabul eder, bununla ilgili bir mesaj verir ve ardından $callbacks dizi property’sini döngüye sokar.

Her öğeyi, kodu çağıran call_user_func()'a $product parametresiyle beraber olarak iletir. Aşağıdaki örneklerin tümü framework ile çalışacaktır.

Callback’ler neden yararlıdır? Çalışma zamanında o bileşenin asıl görevi ile doğrudan ilgili olmayan bir işlevsellik eklemenize izin verirler. Bir bileşeni callback’ten haberdar hale getirerek, başkalarına kodunuzu henüz bilmediğiniz bağlamlarda genişletme gücü verirsiniz.

Örneğin, gelecekteki bir ProcessSale kullanıcısının satış log’u oluşturmak istediğini hayal edin. Kullanıcının sınıfa erişimi varsa, log kodunu doğrudan sale() metoduna ekleyebilir. Yine de bu her zaman iyi bir fikir değildir. ProcessSale paketinin bir sonraki güncellemesinde değişikliklerinin üzerine yazılacaktır. Paketi yazan siz olsanız bile, sale() metoduna pek çok arızi görev eklemek, temel sorumluluğunu aşmaya başlayacak ve potansiyel olarak onu projeler arasında daha az kullanılabilir hale getirecektir. Buna bir sonraki bölümde döneceğim.

ProcessSale'i callback’in farkındadır. Burada, log’a kaydetmeyi simüle eden bir callback oluşturuyorum:

```
$logger = function ($product) {
    print " logging ({$product->name})\n";
};

$processor = new ProcessSale();
$processor->registerCallback($logger);
$processor->sale(new Product("shoes", 6));
print "\n";
$processor->sale(new Product("coffee", 6));
```

Burada anonim fonksiyon oluşturuyorum. Yani, function anahtar kelimesini fonksiyon adı olmadan kullanıyorum. Bu bir satır içi ifade olduğundan, kod bloğunun sonunda noktalı virgül gerektiğini unutmayın. Anonim fonksiyon değişkende saklanabilir, metot ve fonksiyonlara parametre olarak iletilebilir. $logger değişkenine fonksiyon atayarak ve bunu ProcessSale::registerCallback() metoduna ileterek yaptığım tam da bu. Son olarak, birkaç ürün oluşturup bunları sale() metoduna aktarıyorum. Satış daha sonra işlenir (gerçekte ürün hakkında basit bir mesaj yazdırılır) ve tüm callback’ler yürütülür.

```
shoes: processing
    logging (shoes)
coffee: processing
    logging (coffee)
```

PHP 7.4, anonim fonksiyonları bildirmenin yeni bir yolunu tanıttı. Arrow fonksiyonları, daha önce karşılaştığınız anonim fonksiyonlara işlevsel olarak çok benzer. Bununla birlikte, sözdizimi çok daha derli topludur. function anahtar sözcüğü yerine, fn ile tanımlanırlar. Ardından parametreler için parantez ve son olarak süslü parantez yerine arrow işleci (=>) ve ardından tek bir ifade gelir. Bu derli toplu form, arrow fonksiyonlarını özel sıralamalar ve benzerleri için - küçük callback’ler oluşturarak - çok kullanışlı hale getirir. Burada, $logger anonim fonksiyonunu arrow kullanarak eşdeğeriyle değiştiriyorum:

```
$logger = fn($product) => print " logging ({$product->name})\n";
```

Arrow fonksiyon çok daha derli topludur, ancak yalnızca tek bir ifade tanımladığınız için nispeten basit görevlerde kullanılır. 

Elbette, callback’lerin isimsiz olması gerekmez. Bir fonksiyonun adını, hatta bir nesne referansını ve bir metodu callback olarak kullanabilirsiniz. İşte tam da bunu yapıyorum:

```
class Mailer
{
    public function doMail(Product $product): void
    {
        print " mailing ({$product->name})\n";
    }
}

$processor = new ProcessSale();
$processor->registerCallback([new Mailer(), "doMail"]);
$processor->sale(new Product("shoes", 6));
print "\n";
$processor->sale(new Product("coffee", 6));
```

Bir sınıf oluşturuyorum: Mailer. Tek metodu olan doMail(), Product nesnesini kabul eder ve bununla ilgili bir mesaj verir. registerCallback()'i çağırdığımda, ona bir dizi iletiyorum. İlk öğe Mailer nesnesi ve ikincisi, çağrılmasını istediğim metodun adıyla eşleşen bir dizedir.

registerCallback()’in callable parametreyi zorlamak için bir tür bildirimi kullandığını unutmayın. PHP, bu tür bir diziyi callable olarak tanıyacak kadar akıllıdır. Dizi biçimindeki geçerli bir callback, ilk öğesi olarak bir nesneye ve ikinci öğesi olarak bir metot adına sahip olmalıdır. Testi burada geçiyorum ve işte çıktı:

```
shoes: processing
    mailing (shoes)
coffee: processing
    mailing (coffee)
```

Bir metodun anonim fonksiyon döndürmesini sağlayabilirsiniz;

```
class Totalizer
{
    public static function warnAmount(): callable
    {
        return function (Product $product) {
            if ($product->price > 5) {
                print " reached high price: {$product->price}\n";
            }
        };
    }
}

$processor = new ProcessSale();
$processor->registerCallback(Totalizer::warnAmount());
$processor->sale(new Product("shoes", 6));
print "\n";
```

Anonim fonksiyon için factory olarak warnAmount() metodunu kullanmanın dışında, pek ilgi çekici bir şey eklemedim. Ancak bu yapı, anonim fonksiyon oluşturmaktan çok daha fazlasını yapmama izin veriyor. Closure’lardan yararlanmamı sağlıyor. Anonim fonksiyonlar, anonim fonksiyonların üst kapsamında bildirilen değişkenlere başvurabilir. Bu bazen anlaşılması zor bir kavramdır. Anonim fonksiyon, oluşturulduğu bağlamı hatırlamaya devam ediyor gibidir. Totalizer::warnAmount() fonksiyonunun iki şey yapmasını istediğimi hayal edin. Her şeyden önce, isteğe bağlı bir hedef tutarı kabul etmesini istiyorum. İkincisi, ürünler satıldıkça fiyatların çetelesini tutmasını istiyorum. Toplam, hedeflenen tutarı aştığında, fonksiyon bir eylem gerçekleştirecektir (bu durumda, tahmin edebileceğiniz gibi, sadece bir mesaj yazacaktır).

use kullanarak anonim fonksiyonumun değişkenleri daha geniş bir kapsamdan izlemesini sağlayabilirim:

```
class Totalizer2
{
    public static function warnAmount($amt): callable
    {
        $count = 0;
        return function ($product) use ($amt, &$count) {
            $count += $product->price;
            print " count: $count\n";
            if ($count > $amt) {
                print " high price reached: {$count}\n";
            }
        };
    }
}

$processor = new ProcessSale();
$processor->registerCallback(Totalizer2::warnAmount(8));
$processor->sale(new Product("shoes", 6));
print "\n";
$processor->sale(new Product("coffee", 6));
```

Totalizer2::warnAmount() tarafından döndürülen anonim fonksiyon, use ifadesinde iki değişken belirtir. İlki $amt. Bu, warnAmount() tarafından kabul edilen parametredir. İkinci closure değişkeni $count'tur. $count, warnAmount() gövdesinde bildirilir ve başlangıçta sıfıra ayarlanır. Use ifadesindeki $count değişkeninin başına & işareti koyduğuma dikkat edin. Bu, değişkene anonim fonksiyondaki değer yerine referans yoluyla erişileceği anlamına gelir. Anonim fonksiyonun gövdesinde, ürünün değerine göre $count'u artırıyorum ve ardından yeni toplamı $amt'a karşı test ediyorum. Hedef değere ulaşıldıysa bildirim çıktısı alıyorum.

İşte çalışan kod:

```
shoes: processing
    count: 6
coffee: processing
    count: 12
    high price reached: 12
```

Bu, callback’in çağrılar arasında $count'u takip ettiğini gösterir. Hem $count hem de $amt fonksiyonla ilişkili kalır, çünkü fonksiyon bildiriminin bağlamında bulunurlar ve use ifadesinde belirtilirler.

Arrow fonksiyonları da closure oluşturur (anonim fonksiyonlar gibi, yerleşik Closure sınıfının bir örneğine çözümlenirler). Closure değişkenleriyle açık bir ilişki gerektiren anonim fonksiyonların aksine, kapsamdaki tüm değişkenlerin yan-değer kopyasını otomatik olarak alırlar. İşte bir örnek:

```
$markup = 3;
$counter = fn (Product $product) => print "($product->name) marked up
price: " .
    ($product->price + $markup) . "\n";
$processor = new ProcessSale();
$processor->registerCallback($counter);
$processor->sale(new Product("shoes", 6));
print "\n";
$processor->sale(new Product("coffee", 6));
```

ProcessSale::sale()’e ilettiğim anonim fonksiyon içinde $markup’a erişebiliyorum. Ancak, fonksiyon yalnızca değere göre erişime sahip olduğundan, fonksiyon içinde gerçekleştireceğim herhangi bir değişiklik kaynak değişkeni etkilemeyecektir.

PHP 7.1, nesne bağlamındaki closure’ları yönetmenin yeni bir yolunu tanıttı. Closure::fromCallable() metodu, bir nesnenin sınıflarına ve property’lerine çağıran kod erişimi sağlayan bir closure oluşturmanıza olanak tanır. Son örnekle aynı sonucu elde etmek için nesne özelliklerini kullanan Totalizer serisinin bir versiyonu:

```
class Totalizer3
{
    private float $count = 0;
    private float $amt = 0;
    
    public function warnAmount(int $amt): callable
    {
        $this->amt = $amt;
        return \Closure::fromCallable([$this, "processPrice"]);
    }
    
    private function processPrice(Product $product): void
    {
        $this->count += $product->price;
        print " count: {$this->count}\n";
        if ($this->count > $this->amt) {
            print " high price reached: {$this->count}\n";
        }
    }
}
```

warnAmount() metodu bu örnekte static değildir. Bunun nedeni, Closure::fromCallable() sayesinde, daha geniş nesneye erişimi olan processPrice() metoduna bir callback döndürmemdir. $amt property’sini ayarlıyorum ve callable metot referansı döndürüyorum. processPrice() çağrıldığında, $count property’sini artırır ve $amt değerine ulaşıldığında uyarı verir. processPrice() public bir metot olsaydı, basitçe [$this, "processPrice"] döndürebilirdim. Gördüğümüz gibi, PHP böyle iki elemanlı bir dizinin callable olarak çözülmesi gerektiğini anlayacak kadar zekidir. Bununla birlikte, Closure::fromCallable()'ı kullanmak istememin iki iyi nedeni var. İlk olarak, private veya protected metotlara, onları tüm dünyaya açmak zorunda kalmadan kontrollü erişim verebilirim; bu, erişimi kontrol ederken gelişmiş işlevsellik sunar. İkincisi, performans artışı elde ediyorum çünkü dönüş değerinin gerçekten callable olup olmadığını anlamaya yönelik bir ek yükten kurtuluyorum.

Burada Totalizer3'ü değişmemiş ProcessSale sınıfıyla kullanıyorum:

```
$totalizer3 = new Totalizer3();
$processor = new ProcessSale();
$processor->registerCallback($totalizer3->warnAmount(8));
$processor->sale(new Product("shoes", 6));
print "\n";
$processor->sale(new Product("coffee", 6));
```

## Anonim Sınıflar

PHP 7 anonim sınıfları tanıttı. Bunlar, küçük bir sınıftan örnek oluşturmanız ve türetmeniz gerektiğinde, söz konusu üst sınıf basit ve yerel bağlama özgü olduğunda kullanışlıdır.

PersonWriter örneğimize dönelim. Bu sefer bir arayüz oluşturarak başlayacağım:

```
interface PersonWriter
{
    public function write(Person $person): void;
}
```

PersonWriter nesnesini kullanabilen bir Person sınıfı sürümü:

```
class Person
{
    public function output(PersonWriter $writer): void
    {
        $writer->write($this);
    }

    public function getName(): string
    {
        return "Bob";
    }
    
    public function getAge(): int
    {
        return 44;
    }
}
```

output() metodu, PersonWriter örneğini kabul eder ve ardından geçerli sınıfın örneğini write() metoduna iletir. Bu şekilde, Person sınıfı, write uygulamasından güzel bir şekilde yalıtılmıştır.

Person nesnesi için name ve age değerlerini yazdıracak bir writer’a ihtiyacımız olursa, devam edip her zamanki gibi bir sınıf oluşturabiliriz. Ancak bu o kadar önemsiz bir uygulama ki, aynı anda bir sınıf oluşturup bunu Person'a iletebiliriz:

```
$person = new Person();

$person->output(
    new class implements PersonWriter
    {
        public function write(Person $person): void
        {
            print $person->getName() . " " . $person->getAge() . "\n";
        }
    }
);
```

Gördüğünüz gibi, new class anahtar kelimeleri ile anonim bir sınıf tanımlayabilirsiniz. Daha sonra, sınıf bloğunu oluşturmadan önce gerekli olan herhangi bir extensions ve implements ifadesini ekleyebilirsiniz.

Anonim sınıflar closure’ları desteklemez. Yani daha geniş kapsamda bildirilen değişkenlere sınıf içerisinden erişilemez. Ancak, değerleri anonim sınıfın kurucusuna iletebilirsiniz. Biraz daha karmaşık bir PersonWriter oluşturalım:

```
$person = new Person();

$person->output(
    new class("/tmp/persondump") implements PersonWriter
    {
        private $path;

        public function __construct(string $path)
        {
            $this->path = $path;
        }
        
        public function write(Person $person): void
        {
            file_put_contents($this->path, $person->getName() . " " .
                $person->getAge() . "\n");
        }
    }
);
```

Kurucuya bir path parametresi ilettim. Bu değer, $path property’sinde saklandı ve sonunda write() metodu tarafından kullanıldı.

Elbette, anonim sınıfınızın boyutu ve karmaşıklığı büyümeye başlarsa, bir sınıf dosyasında adlandırılmış bir sınıf oluşturmak daha mantıklı hale gelir. Bu, özellikle anonim sınıfınızı birden fazla yere kopyalamanız gerekirse geçerlidir.

**Özet**

Bu bölümde, PHP'nin gelişmiş nesne yönelimli özelliklerini ele aldık. Kitapta çalıştıkça bunlardan bazıları size tanıdık gelecek. Özellikle soyut sınıflara, istisnalara ve static metotlara sık sık döneceğim.

Bir sonraki bölümde, yerleşik nesne özelliklerinden bir adım geri çekiliyorum ve nesnelerle çalışmanıza yardımcı olmak için tasarlanmış sınıflara ve fonksiyonlara bakıyorum.

**Kaynak:** “PHP 8 Objects, Patterns, and Practice”, Matt Zandstra