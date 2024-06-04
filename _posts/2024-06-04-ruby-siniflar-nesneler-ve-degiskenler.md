---
layout: post
title: Ruby - Sınıflar, Nesneler ve Değişkenler
---

Ruby'nin nesne yönelimli bir dil olduğuna dair daha önceki iddiamızı merak ediyor olabilirsiniz. İşte bu iddiayı haklı çıkardığımız yer burası. Ruby'de sınıfların ve nesnelerin nasıl oluşturulduğunu ve Ruby'nin diğer nesne yönelimli dillere göre nasıl daha esnek olabildiğini inceleyeceğiz.

Daha önce söylediğimiz gibi Ruby'de manipüle ettiğimiz her şey bir nesnedir. Ve Ruby'deki her nesne doğrudan ya da dolaylı olarak bir sınıftan başlatılır. Bu bölümde bu sınıfları oluşturmayı ve değiştirmeyi derinlemesine inceleyeceğiz.

## Sınıfları Tanımlamak

Kendimize çözmemiz gereken basit bir problem verelim. Diyelim ki ikinci el kitap mağazası işletiyoruz. Her hafta stok kontrolü yapıyoruz. Bir grup tezgahtar, raflarımızdaki kitapları kaydetmek için taşınabilir barkod tarayıcılar kullanıyor. Tarayıcılar, taranan her kitap için bir satır içeren basit virgülle ayrılmış değer (CSV) dosyası oluşturur. Satırda (diğer şeylerin yanı sıra) kitabın ISBN'si ve fiyatı yer alır. Bu dosyaların birinden alınan alıntı şuna benzer:

```
"Date","ISBN","Price"
"2013-04-12","978-1-9343561-0-4",39.45
"2013-04-13","978-1-9343561-6-6",45.67
"2013-04-14","978-1-9343560-7-4",36.95
```

Bizim işimiz tüm CSV dosyalarını alıp hangi kitaptan kaç taneye sahip olduğumuzu ve stoktaki kitapların toplam liste fiyatını hesaplamak.

Nesneye Dayalı bir sistem tasarlarken, ilk adım, uğraştığınız domain (etki alanı) kavramlarını tanımlamaktır. Tipik olarak, fiziksel bir nesneyi, bir süreci veya başka bir tür varlığı temsil edebilen domain kavramı, son programınızda sınıflara dönüşür ve daha sonra bu kavramların bireysel örnekleri, bu sınıfların örnekleri olur.

Tarayıcılar tarafından yakalanan veri okumasını temsil edecek bir şeye ihtiyacımız olacağı oldukça açık görünüyor. Bu sınıfın her örneği belirli bir veri satırını temsil edecek ve tüm bu nesnelerin toplamı, yakaladığımız tüm verileri temsil edecektir.

Bu sınıfa `BookInStock` adını verelim. (Sınıf adlarının büyük harfle başladığını ve metot adlarının normalde küçük harfle başladığını unutmayın.) Ruby'de bu sınıfı şu şekilde tanımlıyoruz:

```
class BookInStock
end
```

Önceki bölümde gördüğümüz gibi, `new` kullanarak bu sınıfın yeni örneklerini oluşturabiliriz:

```
a_book = BookInStock.new
another_book = BookInStock.new
```

Bu kod çalıştırıldıktan sonra, her ikisi de BookInStock sınıfından olan iki farklı nesnemiz olur. Ancak farklı nesne kimliklerine sahip olmaları dışında, bir örneği diğerinden ayırt edecek hiçbir şey yoktur. Daha da kötüsü, bu nesneler ihtiyacımız olan bilgilerin hiçbirini henüz taşımıyor.

Bunu düzeltmenin en iyi yolu sınıfa `initialize` metodu sağlamaktır. Bu metot, her nesnenin durumunu oluşturulduğu sırada ayarlamanızı sağlar. Bu durumu nesnenin içindeki örnek değişkenlerde saklıyoruz. (Örnek değişkenleri hatırlıyor musunuz? Bunlar @ işaretiyle başlayanlardır.) Ruby'deki her nesnenin kendine özgü örnek değişkenleri olduğundan, her nesnenin kendine özgü bir durumu olabilir.

İşte güncellenmiş sınıf tanımımız:

```
class BookInStock
  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end
end
```

`initialize` metodu Ruby programlarına özeldir. Yeni bir nesne oluşturmak için `BookInStock.new`'i çağırdığınızda, Ruby, başlatılmamış bir nesneyi tutmak için bir miktar bellek ayırır ve ardından, new'e iletilen tüm argümanları alarak o nesnenin `initialize` metodunu çağırır. Bu size nesnenin durumunu ayarlayan kod yazma şansı verir.

`BookInStock` sınıfı için, `initialize` metodu iki parametre alır. Bu parametreler metot içindeki local değişkenler gibi davranır, dolayısıyla küçük harfle başlama şeklindeki local değişken adlandırma kuralını izlerler. Ancak local değişkenler olarak, `initialize` metodu dönüş yaptığında buharlaşırlar, bu nedenle onları örnek değişkenlere aktarmamız gerekir. Bu, `initialize` metodunda çok yaygın bir davranıştır; amaç, nesnenin `initialize` geri dönüşü sırasında kurulmasını ve kullanılabilir olmasını sağlamaktır.

Bu yöntem aynı zamanda Ruby'ye yeni gelenleri sıklıkla şaşırtan bir şeyi de göstermektedir. `@isbn = isbn`'in nasıl yazıldığına dikkat edin. Buradaki iki değişkenin, bir şekilde ilişkili olduğunu hayal etmek kolaydır. Görünüşe göre aynı isme sahipler ama aynı değiller. İlki bir örnek değişkendir ve @ işareti aslında adının bir parçasıdır.

Son olarak, bu kodda basit bir validation görülmektedir. `Float` metodu argümanı alır ve onu kayan noktalı sayıya dönüştürür, bu dönüştürme başarısız olursa programı hata ile sonlandırır. Kitabın ilerleyen kısımlarında bu istisnai durumlarla başa çıkmanın daha başka yollarını göreceğiz.

Burada yaptığımız şey, fiyat parametresi için, o parametre bir kayan noktaya dönüştürülebildiği sürece herhangi bir nesneyi kabul etmek istediğimizi söylemektir. Kayan noktayı, tamsayıyı veya hatta kayan noktanın temsilini içeren bir dizeyi iletebiliriz ve işe yarayacaktır. Şimdi şunu deneyelim. Her biri farklı başlangıç durumuna sahip üç nesne oluşturacağız. `p` metodu nesnenin temsilini yazdırır. Bunu kullanarak, her durumda parametrelerimizin nesnenin durumuna aktarıldığını ve örnek değişkenler haline geldiğini görebiliriz:

```
class BookInStock
  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end
end

b1 = BookInStock.new("isbn1", 3)
p b1
b2 = BookInStock.new("isbn2", 3.14)
p b2
b3 = BookInStock.new("isbn3", "5.67")
p b3

#<BookInStock:0x0000000100a188c0 @isbn="isbn1", @price=3.0>
#<BookInStock:0x0000000100a18410 @isbn="isbn2", @price=3.14>
#<BookInStock:0x0000000100a182a8 @isbn="isbn3", @price=5.67>
```

Nesnelerimizi yazdırmak için neden `puts` yerine `p` metodunu kullandık? Peki, `puts` kullanarak kodu tekrarlayalım:

```
b1 = BookInStock.new("isbn1", 3)
puts b1
b2 = BookInStock.new("isbn2", 3.14)
puts b2
b3 = BookInStock.new("isbn3", "5.67")
puts b3

#<BookInStock:0x0000000102c587c8>
#<BookInStock:0x0000000102c583e0>
#<BookInStock:0x0000000102c58318>
```

Unutmayın, `puts` dizeleri programınızın standart output una yazar. Sınıfa dayalı bir nesneyi ona ilettiğinizde, nesneyle ne yapacağını henüz bilmiyor, bu yüzden çok basit bir yöntem kullanıyor: nesne sınıfının adını, ardından iki nokta üst üste ve nesnenin onaltılık sayı olan benzersiz nesne tanımlayıcısını yazıyor. Her şeyi `#<...>` içine yerleştirir.

Deneyimlerimiz bize, geliştirme sırasında `BookInStock` nesnesinin içeriğini birçok kez yazdıracağımızı ve varsayılan biçimlendirmenin arzu edilen bir şeyi bıraktığını söylüyor.

Neyse ki Ruby'nin, dize olarak oluşturmak istediği herhangi bir nesneye gönderdiği `to_s` adında standart bir mesajı vardır. `Object` sınıfında tanımlanan `to_s`'in varsayılan davranışı, ClassName, ardından iki nokta üst üste ve az önce tanımladığımız nesne kimliği davranışıdır. Dolayısıyla, `BookInStock` nesnelerimizden birini puts'a ilettiğimizde, puts metodu dize temsilini almak için o nesnedeki `to_s`'i çağırır.

Farklı bir davranış istiyorsak, nesnelerimizin daha iyi işlenmesini sağlamak için `to_s`'in varsayılan uygulamasını geçersiz kılabiliriz; bunun nasıl çalıştığı hakkında daha fazla bilgiyi ilerleyen bölümlerde - kalıtım, modüller ve mixin’ler - göreceksiniz.

```
class BookInStock
  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  def to_s
    "ISBN: #{@isbn}, price: #{@price}"
  end
end

b1 = BookInStock.new("isbn1", 3)
puts b1
b2 = BookInStock.new("isbn2", 3.14)
puts b2
b3 = BookInStock.new("isbn3", "5.67")
puts b3

#ISBN: isbn1, price: 3.0
#ISBN: isbn2, price: 3.14
#ISBN: isbn3, price: 5.67
```

`p` metodunun nesneler üzerinde çağırdığı farklı bir mesajı vardır; bu metoda `inspect` adı verilir. Aradaki fark, `inspect`'in geliştirici için hata ayıklama sırasında yararlı olacak bir temsil üretmek üzere tasarlanmış olması ve `to_s`'in daha genel çıktı için insan tarafından okunabilir bir temsil üretmesinin beklenmesidir.

Burada hem önemsiz hem de derin bir şeyler oluyor. `initialize` metodunda `@isbn` ve `@price` örnek değişkenlerine atadığımız değerlerin daha sonra `to_s` metodunda nasıl mevcut olduğunu gördünüz mü? Bu, örnek değişkenlerin nasıl çalıştığını gösterir; bunlar her nesneyle birlikte depolanır ve bu nesnelerin tüm örnek metotları tarafından kullanılabilir.

## Nesneler ve Nitelikler

Şu ana kadar oluşturduğumuz `BookInStock` nesnelerinin dahili durumları vardır (ISBN ve fiyat). Bu durum bu nesnelere özeldir; başka hiçbir nesne bir nesnenin örnek değişkenlerine erişemez. Genel olarak bu iyi bir şeydir. Bu, nesnenin yalnızca kendi tutarlılığını korumaktan sorumlu olduğu anlamına gelir. (Ruby'de mükemmel gizlilik diye bir şeyin olmadığını ve güvenlik amacıyla Ruby'nin dil gizliliğine güvenmemeniz gerektiğini burada belirtmeyi zorunlu görüyoruz.)

Ancak tamamen gizli olan bir nesne işe yaramaz; onu yaratabilirsiniz ama sonra onunla hiçbir şey yapamazsınız. Normalde bir nesnenin durumuna erişmenize ve onu değiştirmenize olanak tanıyan ve dış dünyanın nesneyle etkileşimini sağlayan metotlar tanımlayacaksınız. Nesnenin dışarıdan görülebilen bu yönlerine onun nitelikleri denir.

`BookInStock` nesnelerimiz için ihtiyacımız olabilecek ilk şey ISBN ve fiyatı bulma yeteneğidir (böylece her bir kitabı sayabilir ve fiyat hesaplamaları yapabiliriz). Bunu yapmanın bir yolu erişim metotları (accessor) yazmaktır:

```
class BookInStock
  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  def isbn
    @isbn
  end

  def price
    @price
  end

  # ..
end

book = BookInStock.new("isbn1", 12.34)
puts "ISBN = #{book.isbn}"
puts "Price = #{book.price}"

#ISBN   = isbn1
#Price  = 12.34
```

Burada iki örnek değişkenin değerlerini döndürmek için iki accessor metot tanımladık. Örneğin `isbn` metodu, `@isbn` örnek değişkeninin değerini döndürür, çünkü metotta yürütülen son (ve tek) şey, yalnızca `@isbn` değişkenini değerlendiren ifadedir. Daha sonra, tek satırlık metotları bildirmek için daha kısa bir sözdizimine bakacağız.

Diğer nesneler söz konusu olduğunda, accessor metotların çağrılması ile diğer metotların çağrılması arasında hiçbir fark yoktur. Bu harika çünkü nesnenin dahili uygulamasının, diğer nesnelerin değişiklikten haberdar olmasına gerek kalmadan değişebileceği anlamına geliyor.

Accessor metotlar çok yaygın kullanıldığı için Ruby kullanışlı kısayollar sağlar. `attr_reader` metodu sizin için şu nitelik okuyan metotları oluşturur:

```
class BookInStock
  attr_reader :isbn, :price

  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  # ..
end

book = BookInStock.new("isbn1", 12.34)
puts "ISBN = #{book.isbn}"
puts "Price = #{book.price}"

#ISBN   = isbn1
#Price  = 12.34
```

Bu bölümde sembolleri ilk kez kullanıyoruz. Semboller bir isme gönderme yapmanın uygun bir yoludur. Bu kodda `:isbn`'i isbn adı anlamında, düz `isbn`'i ise değişkenin değeri anlamında düşünebilirsiniz. Bu örnekte accessor metotlarına `isbn` ve `price` adını verdik. Karşılık gelen örnek değişkenler `@isbn` ve `@price`'dır. Bu accessor metotlar daha önce elle yazdıklarımızın aynısıdır; adı accessor metodun adıyla eşleşen örnek değişkenin değerini döndüreceklerdir. Bu metotlar yalnızca niteliği okumanıza izin verir, onu değiştirmenize izin vermez.

`attr_reader` bildiriminin aslında örnek değişkenleri bildirdiğine dair yaygın bir yanılgı vardır. Accessor metotlarını oluşturur ancak değişkenlerin bildirilmesine gerek yoktur. Bir örnek değişken, ona değer atadığınızda ortaya çıkar ve değer atanmamış herhangi bir örnek değeri, erişildiğinde `nil` değerini döndürür. Ruby, örnek değişkenleri ve accessor metotları tamamen ayırır.

### Niteliklere Yazmak

Bazen nesnenin dışından bir nitelik ayarlayabilmeniz gerekir. Örneğin tarama verilerini okuduktan sonra bazı kitapların fiyatlarında indirim yapmamız gerektiğini varsayalım.

C# ve Java gibi örnek değişkenlere erişimi kısıtlayan diğer dillerde bunu *setter* metotları ile yaparsınız:

```
// Java code
class JavaBookInStock {
  private double _price;

  public double getPrice() {
    return _price;
  }

  public void setPrice(double newPrice) {
    _price = newPrice;
  }
}

b = new JavaBookInStock(....);
b.setPrice(calculate_discount(b.getPrice()));
```

Ruby'de bir nesnenin niteliklerine getter metot aracılığıyla erişilebilir ve bu erişim diğer metotlar ile aynı görünür. Bunu daha önce `book.isbn` gibi ifadelerle görmüştük. Bu nedenle, bir niteliğin değerinin, normal değişken ataması gibi (`book.isbn = "new isbn"`) görünecek şekilde ayarlanması doğaldır. Adı eşittir işaretiyle biten bir Ruby metodu oluşturarak bu atamayı etkinleştirirsiniz. Bu şekilde adlandırılan bir metot, atamaların hedefi olarak kullanılabilir:

```
class BookInStock
  attr_reader :isbn, :price

  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  def price=(new_price)
    @price = new_price
  end

  # ...
end

book = BookInStock.new("isbn1", 33.80)
puts "ISBN = #{book.isbn}"
puts "Price = #{book.price}"
book.price = book.price * 0.75
puts "New price = #{book.price}"

#ISBN      = isbn1
#Price     = 33.8
#New price = 25.349999999999998
```

`book.price = book.price * 0.75` ataması, kitap nesnesindeki `price=` metodunu çağırır ve ona argüman olarak indirimli fiyatı iletir. Adı eşittir işaretiyle biten bir metot oluşturursanız, bu ad atamanın sol tarafında görülebilir (ve Ruby ayrıştırıcısı adın sonu ile eşittir işareti arasındaki boşlukları yok sayar).

Yine Ruby, bu basit setter metotlarını oluşturmak için bir kısayol sağlar. Salt yazılır bir accessor istiyorsanız `attr_writer` formunu kullanabilirsiniz, ancak bu oldukça nadirdir. Belirli bir niteliği hem okumak hem de yazmak isteme olasılığınız çok daha yüksektir, bu nedenle daha kullanışlı olan `attr_accessor` metodunu tercih edeceksiniz:

```
class BookInStock
  attr_reader :isbn
  attr_accessor :price

  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  # ...
end

book = BookInStock.new("isbn1", 33.80)
puts "ISBN = #{book.isbn}"
puts "Price = #{book.price}"
book.price = book.price * 0.75 # discount price
puts "New price = #{book.price}"

#ISBN      = isbn1
#Price     = 33.8
#New price = 25.349999999999998
```

Bu örnekte `attr_accessor :price` satırı, hem `puts book.price` yazmanıza izin veren getter metodunu hem de `book.price = book.price * 0,75` yazmanıza olanak tanıyan setter metodunu oluşturur.

### Nitelikler Sadece Argümansız Metotlardır

Accessor metotların yalnızca nesnenin örnek değişkenlerini çevreleyen basit sarmalayıcılar olması gerekmez. Örneğin, fiyata kayan noktalı dolar yerine tam sayı cent olarak erişmek isteyebilirsiniz.

```
class BookInStock
  attr_reader :isbn
  attr_accessor :price

  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  def price_in_cents
    (price * 100).round
  end

  # ...
end

book = BookInStock.new("isbn1", 33.80)
puts "Price = #{book.price}"
puts "Price in cents = #{book.price_in_cents}"

#Price          = 33.8
#Price in cents = 3380
```

Fiyatı cent cinsinden bulmak için kayan nokta fiyatını 100 ile çarpıyoruz ve ardından bunu tam sayıya dönüştürmek için `round` metodunu kullanıyoruz. Neden? Çünkü kayan noktalı sayıların her zaman tam bir temsili yoktur. 33,8'i 100 ile çarptığımızda 3379,99999999999954525265 elde ederiz. `Integer` metodu bunu 3379'a kısaltır. `round` kullanmak en iyi tamsayı temsilini elde etmemizi sağlar. Bu, finansal hesaplamalarda neden `Float` yerine `BigDecimal`'ı kullanmak istediğinize dair iyi bir örnektir.

Bunu daha da ileri götürebilir ve reader metoduna paralel bir writer metodu oluşturabilir, değeri dahili olarak örnek değişkenle eşleyebiliriz:

```
class BookInStock
  attr_reader :isbn
  attr_accessor :price

  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  def price_in_cents
    (price * 100).round
  end
  
  def price_in_cents=(cents)
    @price = cents / 100.0
  end

  # ...
end

book = BookInStock.new("isbn1", 33.80)
puts "Price = #{book.price}"
puts "Price in cents = #{book.price_in_cents}"
book.price_in_cents = 1234
puts "Price = #{book.price}"
puts "Price in cents = #{book.price_in_cents}"

#Price          = 33.8
#Price in cents = 3380
#Price          = 12.34
#Price in cents = 1234
```

Burada sanal bir örnek değişken oluşturmak için nitelik metotlarını kullandık. Dış dünyaya göre, `price_in_cents` diğer nitelikler gibi görünüyor. Ancak olarak buna karşılık gelen bir örnek değişken yoktur.

Bertrand Meyer çığır açan kitabı *Object-Oriented Software Construction* da, bunu *Uniform Access Principle* olarak adlandırıyor. Örnek değişkenler ile hesaplanan değerler arasındaki farkı gizleyerek dünyanın geri kalanını sınıfınızın uygulanmasından koruyorsunuz. Gelecekte, sınıfınızı kullanan milyonlarca satırlık kodu etkilemeden işlerin çalışma şeklini değiştirmekte özgürsünüz; örneğin, kayan noktalı değişkenden `BigDecimal`'e geçiş yapabilirsiniz ve bu sınıfın kullanıcılarının hiçbir zaman bunu bilmesine gerek kalmaz. Bu büyük bir kazanç.

### Nitelikler, Örnek Değişkenleri ve Metotlar

Niteliklerin bu tanımı, bunların metotlardan başka bir şey olmadığını düşünmenize neden olabilir; neden onlara süslü bir isim bulmamız gerekti? Bir bakıma bu kesinlikle doğru. Bir nitelik yalnızca bir metottur. Bazen bir nitelik basitçe bir örnek değişkenin değerini döndürür. Bazen bir nitelik bir hesaplamanın sonucunu döndürür. Ve bazen adlarının sonunda eşittir işareti bulunan bu ilginç metotlar nesnenin durumunu güncellemek için kullanılır. Öyleyse soru şu: Nitelikler nerede biter ve metotlar nerede başlar? Bir şeyi sadece bir metot değil de nitelik haline getiren şey nedir? İşte kişisel bir bakış.

Bir sınıf tasarladığınızda, onun hangi iç duruma sahip olduğuna karar verirsiniz ve aynı zamanda bu durumun kullanıcılara dışarıda nasıl görüneceğine de karar verirsiniz. Dahili durum örnek değişkenlerde tutulur. Dış durum, nitelik dediğimiz metotlarla açığa çıkar. Sınıfınızın gerçekleştirebileceği diğer eylemler ise yalnızca normal metotlardır. Bu gerçekten çok önemli bir ayrım değil, ancak bir nesnenin dış durumunu onun nitelikleri olarak adlandırarak, insanlara yazdığınız sınıfı nasıl görmeleri gerektiği konusunda ipucu vermeye yardımcı oluyorsunuz.

## Diğer Sınıflarla Çalışan Sınıflar

Başlangıçta karşılaştığımız zorluk, birden fazla CSV dosyasındaki verileri okumak ve çeşitli basit raporlar oluşturmaktı. Şu ana kadar elimizde olan tek şey, kitabın verilerini temsil eden bir sınıf olan `BookInStock`'tur. Yazdığımız programın CSV veri akışlarını birleştirmesi ve özetlemesi gerekeceğini biliyoruz. Ama bu çok pasif bir ifade. Özetlemenin ne işe yaradığını kendimize sorarak bunu bir tasarıma dönüştürelim. Ve cevap (bizim durumumuzda) bir CSV okuyucusudur:

```
class CsvReader
  def initialize
    # ...
  end

  def read_in_csv_data(csv_file_name)
    # ...
  end

  def total_value_in_stock
    # ...
  end

  def number_of_each_isbn
    # ...
  end
end
```

Buna benzer bir şey kullanarak onu çağırabiliriz:

```
reader = CsvReader.new
reader.read_in_csv_data("file1.csv")
reader.read_in_csv_data("file2.csv")

puts "Total value in stock = #{reader.total_value_in_stock}"
```

Birden fazla CSV dosyasını işleyebilmemiz gerekiyor, dolayısıyla reader nesnemizin, beslendiği her CSV dosyasındaki değerleri toplaması gerekiyor. Bunu, bir örnek değişkeninde bir dizi değer tutarak yapacağız. Ve her bir kitabın verilerini nasıl temsil edeceğiz? `BookInStock` sınıfını yazmayı yeni bitirdik, böylece sorun çözüldü. Diğer tek soru, CSV dosyasındaki verileri nasıl ayrıştırdığımızdır. Neyse ki Ruby, daha sonra ayrıntılı olarak ele alacağımız iyi bir CSV kütüphanesiyle birlikte gelir. Başlık satırına sahip bir CSV dosyası verildiğinde, kalan satırlar üzerinde yinelenebilir ve değerleri ada göre çıkartabiliriz:

```
class CsvReader
  def initialize
    @books_in_stock = []
  end

  def read_in_csv_data(csv_file_name)
    CSV.foreach(csv_file_name, headers: true) do |row|
      @books_in_stock << BookInStock.new(row["ISBN"], row["Price"])
    end
  end
end
```

Muhtemelen neler olduğunu merak ettiğiniz için hadi şu `read_in_csv_data` metodunu inceleyelim. İlk satırda CSV kütüphanesine verilen isimdeki dosyayı açmasını söylüyoruz. `headers: true` seçeneği, kütüphaneye dosyanın ilk satırını sütun adları olarak ayrıştırmasını ve her satırı, sütun adlarının anahtar ve satır değerlerinin de değerler olduğu bir hash halinde ayrıştırmasını söyler.

Kütüphane daha sonra dosyanın geri kalanını okur ve her satırı sırasıyla bloğa (`do` ve `end` arasındaki kod) iletir. Bloğun içinde ISBN ve Price sütunlarından verileri çıkarıyoruz ve bu verileri yeni bir `BookInStock` nesnesi oluşturmak için kullanıyoruz. Daha sonra bu nesneyi `@books_in_stock` adı verilen örnek değişkene ekliyoruz (`<<` operatörü Ruby'de farklı şeyler yapar, bu durumda "bir diziye ekleme" anlamına gelir). Peki bu değişken nereden geliyor? `initialize` metodunda oluşturduğumuz bir dizi.

Yine, hedeflemek istediğiniz patern budur. `initialize` metodumuz, nesneniz için bir ortam oluşturarak onu kullanılabilir durumda bırakır. Daha sonra diğer metotlar bu durumu kullanır.

Bu kodu çalıştırdığınızda “‘Float’: can’t convert nil into Float (TypeError)” şeklinde bir hatayla karşılaşırsanız, muhtemelen CSV veri dosyanızdaki başlık satırının sonunda fazladan boşluk vardır. CSV kütüphanesi kabul ettiği formatlar konusunda oldukça katıdır.

Öyleyse bunu bir kod parçasından çalışan bir programa dönüştürelim. Kaynağımızı üç dosya halinde düzenleyeceğiz. İlki, *book_in_stock.rb*, `BookInStock` sınıfının tanımını içerecektir. İkincisi, *csv_reader.rb*, CsvReader sınıfının kaynağıdır. Son olarak üçüncü dosya olan *stock_stats.rb*, ana sürücü programıdır. *book_in_stock.rb* ile başlayacağız:

```
class BookInStock
  attr_reader :isbn, :price

  def initialize(isbn, price)
    @isbn = isbn
    @price = Float(price)
  end

  def price_in_cents
    (@price * 100).round
  end
end
```

Kayan nokta hataları biriktirmeden para aritmetiği yapabilmek için, `price_in_cents` metodunu koruyoruz.

İşte *csv_reader.rb* dosyası. `CsvReader` sınıfının iki harici bağımlılığı vardır: standart CSV kütüphanesine ve *book_in_stock.rb* dosyasındaki `BookInStock` sınıfına ihtiyaç duyar. Ruby'nin harici dosyaları yüklememize izin veren birkaç helper metodu vardır.

```
require "csv"
require_relative "book_in_stock"

class CsvReader
  def initialize
    @books_in_stock = []
  end

  def read_in_csv_data(csv_file_name)
    CSV.foreach(csv_file_name, headers: true) do |row|
      @books_in_stock << BookInStock.new(row["ISBN"], row["Price"])
    end
  end

  def total_value_in_stock
    # later we'll see easier ways to sum a collection
    sum = 0.0
    @books_in_stock.each { |book| sum += book.price_in_cents }
    sum / 100.0
  end

  def number_of_each_isbn
    # ...
  end
end
```

Bu dosyada Ruby standart kütüphanesinden Ruby CSV kütüphanesini yüklemek için `require` metodunu, yazdığımız `book_in_stock` dosyasına yüklemek için ise `require_relative` i kullanıyoruz. Bunun için require_relative kullanıyoruz çünkü yüklediğimiz dosyanın konumunu, onu yüklediğimiz dosyaya göre tanımlamak en kolay yoldur; ikisi de aynı dizindedir.

Toplam değeri hesaplamak için `price_in_cents` kullanıyoruz.

Ve son olarak, *stock_stats.rb* dosyasındaki ana programımız:

```
require_relative "csv_reader"

reader = CsvReader.new

ARGV.each do |csv_file_name|
  $stderr.puts "Processing #{csv_file_name}"
  reader.read_in_csv_data(csv_file_name)
end

puts "Total value = #{reader.total_value_in_stock}"
```

Yine, bu dosya ihtiyaç duyduğu kütüphaneyi (bu durumda *csv_reader.rb* dosyası) getirmek için `require_relative`'i kullanır. Programın komut satırı argümanlarına erişmek ve komut satırında belirtilen her CSV dosyası için `ARGV` değişkenini kullanır.

```
$ ruby stock_stats.rb data.csv
Processing data.csv
Total value = 122.07
```

Bunun için üç kaynak dosyaya ihtiyacımız var mı? Şart değil. Ancak programlarınız büyüdükçe (ve neredeyse tüm programlar zamanla büyüdükçe), büyük dosyaların hantallaşmaya başladığını göreceksiniz. Ayrıca, kod monolitik bir yığın halindeyse, koda test yazmakta da zorlanacaksınız. Son olarak, eğer hepsi birleştirilmişse, sınıfları yeniden kullanamayacaksınız. Sonuç olarak, her dosya için yalnızca bir Ruby sınıfının olması oldukça yaygındır.

Sınıf tartışmamıza geri dönelim.

## Erişim Kontrolü

Sınıf arayüzü tasarlarken sınıfınızın ne kadarını dış dünyaya sunacağınızı düşünmek önemlidir. Çok fazla erişime izin verirseniz, farklı sınıfların birbirlerinin dahili uygulamalarına bağımlı olma riskini artırırsınız; buna birleştirme adı verilir. Sınıfınızın kullanıcıları, mantıksal arayüz yerine sınıf uygulamanızın ayrıntılarına güvenme eğiliminde olacaktır. İyi haber şu ki Ruby'de bir nesnenin durumunu değiştirmenin tek kolay yolu onun metotlarından birini çağırmaktır. Metotlara erişimi kontrol ettiğinizde nesneye erişimi de kontrol etmiş olursunuz. İyi bir temel kural, nesne durumunu geçersiz bırakabilecek metotları asla açığa çıkarmamaktır.

Ruby size üç düzeyde erişim kontrolü sunar:

- *Public* metotlar herkes tarafından çağrılabilir; erişim kontrolü zorunlu değildir. Metotlar varsayılan olarak herkese açıktır (her zaman private olan `initialize` dışında).
- *Protected* metotlar yalnızca tanımlayan sınıfın ve onun alt sınıflarının nesneleri tarafından çağrılabilir. Erişim aile içinde tutulur. Alt sınıflar hakkında daha fazla bilgiyi ilerleyen bölümlerde konuşacağız.
- *Private* metotlar açık bir alıcıyla çağrılamaz; alıcı her zaman `self` olarak da bilinen geçerli nesnedir. Bu, private metotların yalnızca geçerli nesne bağlamında çağrılabileceği anlamına gelir. Başka bir nesnenin private metotlarını normal nokta sözdizimiyle çağıramazsınız.

"protected" ve "private" arasındaki fark oldukça incedir ve Ruby'de çoğu yaygın OO dilinden farklıdır. Bir metot protected ise, tanımlayan sınıfın veya onun alt sınıflarının herhangi bir örneği tarafından çağrılabilir. Bir metot private ise, yalnızca çağıran nesnenin bağlamı içinde çağrılabilir; başka bir nesnenin private metoduna doğrudan erişmek asla mümkün değildir. Uygulamada "protected" ifadesini görmek oldukça nadirdir.

Ruby'de erişim kontrolü, program derlenirken veya yorumlanırken statik olarak değil, program çalışırken dinamik olarak belirlenir. Yalnızca kod kısıtlı metodu yürütmeye çalıştığında erişim ihlaliyle karşılaşırsınız.

`public`, `protected` ve `private` üç erişim yönteminden birini veya daha fazlasını kullanarak sınıf veya modül tanımları içindeki metotların erişim düzeylerini belirlersiniz. Her işlevi üç farklı şekilde kullanabilirsiniz.

Üç işlev kendinden sonra tanımlanan metotların varsayılan erişim kontrolünü ayarlar. Aynı etkiyi elde etmek için `public` gibi anahtar sözcükler kullandığınız bir C# veya Java programcısıysanız bu muhtemelen tanıdık bir davranıştır. Bu kullanım bir anahtar kelime gibi görünse de Ruby'de erişim kontrolü aslında bir metottur.

```
class MyClass
  # default is "public"
  def method1
    # This method is public
  end

  protected

  # subsequent methods will be "protected"
  def method2
    # This method is protected
  end

  private

    # subsequent methods will be private"
  def method3
    # This method is private
  end

  public

  # subsequent methods will be "public"
  def method4
    # this method is public
  end
end
```

Metotlar için varsayılan erişim `public` olduğundan, erişim kontrolünü belirtmek için açıkça `public` kullanılması nadirdir.

Stil olarak, `public` gibi bir erişim metoduna yapılan çağrıdan sonraki metotlar genellikle girintili değildir, bir blok tanımlamıyorsunuz, yalnızca sonraki metotların erişim durumunu tanımlıyorsunuz.

Alternatif olarak, adlandırılmış metotların erişim düzeylerini, bunları erişim kontrolü işlevlerine argüman olarak vererek ayarlayabilirsiniz:

```
class MyClass
  def method1
  end

  def method2
  end

  # ... and so on
  public :method1, :method4
  protected :method2
  private :method3
end
```

Bu mekanizma pratikte oldukça nadirdir, ancak Ruby'de erişimi bildirmenin üçüncü yolunu mümkün kılar.

Ruby'deki çoğu ifadenin bir değer döndürdüğünden bahsetmiştik. Özellikle, bir metodu `def` ile tanımlamak değer döndürür; yani metodun sembol olarak adı. Sonuç olarak, erişimi doğrudan metot tanımından önce bildirebilirsiniz.

```
class MyClass
  def method1
    # This method is public
  end

  protected def method2
    # This method is protected
  end

  private def method3
    # This method is private
  end

  public def method4
    # This method is public
  end
end
```

Burada olan şey, `def method2` ifadesinin `:method2` değerini döndürmesidir; bu değer hemen `protected` öğesine argüman olarak iletilir, bu da `protected :method2` ile sonuçlanır ve bu metodu, yalnızca o metodu `protected` hale getirir. Bu şekilde bildirilen erişim, dosyaya yayılmaz; yalnızca erişim değiştiricinin (access modifier) önce geldiği metot için geçerlidir.

Biz bu son biçimi tercih ediyoruz çünkü bu, her metodun erişim düzeyi hakkında çok daha açıklayıcıdır. Bununla birlikte, ilk biçim daha eskidir ve şu anda kodda muhtemelen daha yaygındır.

Bazı örneklerin zamanı geldi. Belki bir muhasebe sistemi modelliyoruz. Kimsenin kuralları ihlal edemeyeceğinden emin olmak istediğimiz için, borç ve alacak işlemlerini yapan metotları `private` hale getireceğiz ve harici arayüzümüzü işlemler açısından tanımlayacağız.

```
class Account
  attr_accessor :balance

  def initialize(balance)
    @balance = balance
  end
end

class Transaction
  def initialize(account_a, account_b)
    @account_a = account_a
    @account_b = account_b
  end

  def transfer(amount)
    debit(@account_a, amount)
    credit(@account_b, amount)
  end

  private def debit(account, amount)
    account.balance -= amount
  end

  private def credit(account, amount)
    account.balance += amount
  end
end

savings = Account.new(100)
checking = Account.new(200)
transaction = Transaction.new(checking, savings)
transaction.transfer(50)
```

`protected erişim`, nesnelerin aynı sınıftaki diğer nesnelerin dahili durumuna erişmesi gerektiğinde kullanılır. Örneğin, iki ayrı `Account` nesnesinin bakiyelerini doğrudan karşılaştırmasına izin vermek, ancak bu bakiyeleri dünyanın geri kalanından gizlemek isteyebiliriz (belki de bunları farklı bir biçimde sunduğumuz için):

```
class Account
  protected attr_reader :balance # accessor method 'balance' but make it protected

  def greater_balance_than?(other)
    @balance > other.balance
  end
end
```

`balance` protected olduğu için yalnızca `Account` nesnelerinde kullanılabilir.

## Değişkenler

Artık tüm bu nesneleri yaratma zahmetine girdiğimize göre, onları kaybetmediğimizden emin olalım. Değişkenler nesneleri takip etmek için kullanılır; her değişken bir nesneye referans tutar. Bunu bir kodla doğrulayalım:

```
person = "Tim"
puts "The object in 'person' is a #{person.class}"
puts "The object has an id of #{person.object_id}"
puts "and a value of '#{person}'"

# The object in 'person' is a String
# The object has an id of 60
# and a value of 'Tim'
```

İlk satırda Ruby, `Tim` değerine sahip yeni bir dize nesnesi oluşturur. Bu nesnenin referansı `person` değişkenine yerleştirilir. Hızlıca kontrol ettiğimizde, değişkenin gerçekten de bir sınıf, bir nesne kimliği ve bir değerle birlikte dizenin kişiliğini üstlendiğini gösterir.

Peki değişken bir nesne midir? Ruby'de cevap "hayır"dır. Değişken basitçe bir nesneye yapılan referanstır. Nesneler büyük bir havuzda (çoğu zaman heap memory) yüzer ve değişkenler tarafından işaret edilir. Örneği biraz daha karmaşık hale getirelim:

```
person1 = "Tim"
person2 = person1
person1[0] = 'J'

puts "person1 is #{person1}"
puts "person2 is #{person2}"

# person1 is Jim
# person2 is Jim
```

Burada ne oldu? `person1`’in ilk karakterini değiştirdik (Java'dan farklı olarak Ruby dizeleri değiştirilebilir), ancak hem `person1` hem de `person2 Tim`'den `Jim`'e değiştirildi.

Her şey, değişkenlerin nesnelerin kendilerine değil, nesnelere referanslar tuttuğu gerçeğine geri dönüyor. `person1`'i `person2`'ye atamak herhangi bir yeni nesne yaratmaz; aşağıdaki çizimde gösterildiği gibi hem `person1` hem de `person2` aynı nesneye referansta bulunacak şekilde `person1`'in nesne referansını `person2`'ye kopyalar.

![Ruby Variables](/assets/images/2024/06/ruby-variables.png "Ruby Variables"){: class="img-fluid"}

Atama nesnelere takma ad oluşturur ve potansiyel olarak size aynı nesneye başvuran birden fazla değişken verir.

Ancak bu, kodunuzda sorunlara neden olamaz mı? Olabilir, ancak düşündüğünüz kadar sık ​​değil (Java'daki nesneler tamamen aynı şekilde çalışır). Örneğin önceki örnekte, aynı içeriğe sahip yeni bir dize nesnesi oluşturan `String`'in `dup` metodunu kullanarak takma adlardan kaçınabilirsiniz:

```
person1 = "Tim"
person2 = person1.dup
person1[0] = "J"
puts "person1 is #{person1}"
puts "person2 is #{person2}"

# person1 is Jim
# person2 is Tim
```

Ayrıca herhangi birinin belirli bir nesneyi değiştirmesini de önleyebilirsiniz. Dondurulmuş bir nesneyi değiştirmeye çalıştığınızda Ruby `RuntimeError` exception’ı fırlatır:

```
person1 = "Tim"
person2 = person1
person1.freeze # prevent modifications to the object
person2[0] = "J"

from prog.rb:4:in `<main>'
prog.rb:4:in `[]=': can't modify frozen String: "Tim" (FrozenError)
```

Sayılar ve semboller Ruby'de her zaman dondurulur, dolayısıyla bu değerler her zaman sabittir.

## Sınıfları Yeniden Açmak

Sınıflardan bahsederken en azından Ruby'nin sınıf yapısının benzersiz özelliklerinden birinden bahsetmemiz gerektiğini düşünüyoruz: bir sınıf tanımını yeniden açma ve ona herhangi bir zamanda yeni metot veya değişkenler ekleme yeteneği...

Başka bir deyişle, Ruby'de buna benzer bir şey varsa:

```
class Book
  attr_accessor :title
  # and a bunch of other stuff
end

# Later, you can do this:
class Book
  def uppercase_title
    title.upcase
  end
end
```

`Book` sınıfını tamamladığınızda, önceden tanımlanmış bir `Book` sınıfı varsa Ruby hata vermez ve ikinci bildirimdeki yeni tanımlar mevcut sınıfa eklenir. Metot eklemek veya değiştirmek için sınıfları yeniden açmaya yönelik bu süreç monkey-patching olarak bilinir.

Tipik olarak, böyle bir sınıfı yalnızca orijinal sınıf kodunuzun bir parçası değilse genişletirsiniz, ancak Ruby'de bu yöntemi çekirdek sınıflara veya standart kütüphaneye yardımcı (utility) fonksiyonlar eklemek için kullanmak oldukça yaygındır. Örneğin Ruby on Rails bunu çok sık yapıyor.

Örnek vermek gerekirse Ruby on Rails, dizedeki fazladan boşlukları temizleyen, `squish` adı verilen bir metot tanımlar.

```
"This   string   has   whitespace"
```

Rails, monkey-patching yoluyla aşağıdaki gibi bir metot tanımlayabilir:

```
class String
  def squish
    # implementation
  end
end
```

Daha sonra bunu diğer metotlar gibi `str#squish` kullanarak çağırın.

Diğer birçok dilin kullandığı alternatif, bir yardımcı sınıf tanımlamak ve bunun üzerinde sınıf metodu tanımlamaktır; bu şöyle görünür:

```
class StringUtilities
  def self.squish(str)
    # implementation
  end
end
```

Bunu daha sonra `StringUtilities.squish(str)` ile çağırırsınız.

Umarız bu örnek, sınıfların yeniden açılmasına izin vermenin avantajını gösterir; yardımcı metotlar ekleme yeteneği çok kullanışlıdır. Rails tarafından hangi metotların tanımlandığını ve metodun birçok olası yardımcı sınıftan hangisinin içinde olabileceğini bilmek zorunda olmamak güzel bir şey.

Bununla birlikte, bu dikkatli yapılması gereken bir şeydir; birçok takım, çok açık bir neden olmaksızın kendi kodlarında buna izin vermez. Ve burada yaptığımız gibi yeni metotlar eklemek yerine, mevcut metotların davranışını değiştirmek için monkey-patching kullanma konusunda oldukça dikkatli olmalısınız. Monkey-patching, kodun davranışını öngörülemez hale getirebilir, davranışın nerede tanımlandığını söylemek zor olabilir ve bu değişiklikler globaldir; yani, iki dosya aynı metodu tanımlarsa, son tanımlananın kazanacağı anlamına gelir, potansiyel olarak bulunması zor hatalara yol açar.

Daha sonra metaprogramming bölümünde, size sınıfları yeniden açma avantajı sağlayan ancak aynı zamanda değişikliklerinizin kapsamını da sınırlayan bir Ruby özelliği hakkında konuşacağız.

## Sıradaki

Ruby'deki sınıflar ve nesneler hakkında söylenecek daha çok şey var. Hala sınıf metotlarına, mixin ve miras gibi kavramlara bakmamız gerekiyor. Bunu ilerleyen bölümlerde yapacağız. Ancak şimdilik Ruby'de değiştirdiğiniz her şeyin bir nesne olduğunu ve nesnelerin hayata sınıf örnekleri olarak başladığını bilin. Nesnelerle yaptığımız en yaygın şeylerden biri de onlardan koleksiyonlar oluşturmaktır. Ama bu bir sonraki bölümümüzün konusu.

**Kaynak:** “Programming Ruby 3.2: The Pragmatic Programmers’ Guide”, Noel Rappin with Dave Thomas