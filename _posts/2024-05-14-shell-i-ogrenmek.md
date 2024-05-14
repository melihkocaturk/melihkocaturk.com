---
layout: post
title: Shell’i Öğrenmek
---

Neden komut satırını öğrenmeniz gerekiyor? Peki, size bir hikaye anlatayım. Yıllar önce çalıştığım yerde bir sorun yaşadık. Dosya sunucularımızdan birinde sürekli dolmaya başlayan bir ortak sürücü vardı. Bu eski işletim sisteminin kullanıcı kotalarını desteklemediğinden bahsetmeyeceğim; bu başka bir hikaye. Ancak sunucu dolmaya devam etti ve insanların çalışmasını engelledi. Yazılım mühendislerimizden biri, tüm kullanıcı dizinlerini tarayacak, kullandıkları alanı toplayacak ve sonuçların listesini yapacak bir C++ programı yazmak için birkaç saat harcadı. İşteyken eski işletim sistemini kullanmak zorunda kaldığım için Linux benzeri bir komut satırı ortamı kurdum. Sorunu duyduğumda bu görevi şu tek satırla yapabileceğimi fark ettim:

```
du -s * | sort -nr > $HOME/user_space_report.txt
```

Grafik kullanıcı arayüzleri (GUI'ler) birçok görev için faydalıdır ancak tüm görevler için iyi değildir. Uzun zamandır günümüz bilgisayarlarının çoğunun elektrikle çalışmadığını hissettim. Bunun yerine farenin "pompalama" hareketiyle güç alıyor gibi görünüyorlar. Bilgisayarların bizi manuel işlerden kurtarması gerekiyordu, ancak kaç kez bilgisayarın yapabileceğinden emin olduğunuz bir görevi yerine getirdiniz, fareyi sıkıcı bir şekilde kullanarak işi kendiniz yapmak zorunda kaldınız?

Bir keresinde bir yazarın, çocukken bilgisayarı resimlere bakarak kullandığını söylediğini duymuştum. Büyüyünce okumayı ve yazmayı öğrenirsin. Bilgisayar Okuryazarlığı 101'e hoş geldiniz. Şimdi işe koyulalım.

## "Shell" nedir?

Basitçe söylemek gerekirse shell, klavyeden komutları alan ve bunları gerçekleştirmesi için işletim sistemine veren bir programdır. Eski günlerde Linux gibi Unix benzeri sistemlerde kullanılabilen tek kullanıcı arayüzüydü. Günümüzde shell gibi komut satırı arayüzlerinin (CLI'ler) yanı sıra grafik kullanıcı arayüzlerine (GUI'ler) de sahibiz.

Çoğu Linux sisteminde, **bash** adı verilen bir program (Bourne Again SHell anlamına gelir; orijinal Unix shell programının geliştirilmiş versiyonu olan **sh**, Steve Bourne tarafından yazılmıştır) shell görevi görür. Linux sistemleri için bash'ın yanı sıra başka shell programları da mevcuttur: **ksh**, **tcsh** ve **zsh**.

### "Terminal" nedir?

Terminal emülatörü adı verilen bir programdır. Bu, bir pencere açan ve shell ile etkileşime girmenizi sağlayan bir programdır. Kullanabileceğimiz bir sürü farklı terminal emülatörü var. Bazı Linux dağıtımları birkaç tane yükler. Bunlar **gnome-terminal**, **konsole**, **xterm**, **rxvt**, **kvt**, **nxterm** ve **eterm**'i içerebilir.

### Terminali Başlatmak

Genellikle menüden terminal başlatmanın bir yolu vardır. Terminal emülatörüne benzeyen bir şey olup olmadığını görmek için program listesine bakın. Çok sayıda farklı terminal emülatörü olmasına rağmen hepsi aynı şeyi yapar. Bizim için shell oturumuna erişim sağlıyorlar. Sağladığı farklı özelliklere göre muhtemelen bir tanesini tercih edeceksiniz.

### Klavyeyi Test Etmek

Tamam, biraz yazmayı deneyelim. Bir terminal penceresi açın. Görmemiz gereken ilk şey, kullanıcı adınız, makinenin adı ve ardından dolar işaretini içeren bir shell prompt udur. Bunun gibi bir şey:

```
[me@linuxbox me]$
```

Harika! Şimdi bazı rastgele karakterler yazın ve enter tuşuna basın.

```
[me@linuxbox me]$ kdkjflajfks
```

Her şey yolunda giderse, komutun anlaşılamadığından şikayet eden bir hata mesajı almış olmalıyız:

```
[me@linuxbox me]$ kdkjflajfks
bash: kdkjflajfks: command not found
```

Müthiş! Şimdi yukarı ok tuşuna basın. Önceki komutumuz "kdkjflajfks"in nasıl döndüğünü izleyin. Evet, komut geçmişimiz var. Aşağı oka bastığımızda tekrar boş satıra ulaşıyoruz.

Gerekirse yukarı ok tuşunu kullanarak "kdkjflajfks" komutunu geri çağırın. Şimdi sol ve sağ ok tuşlarını deneyin. İmleci komut satırında herhangi bir yere konumlandırabiliriz. Bu, hataları kolayca düzeltmemize olanak tanır.

**Root olarak çalışmadığınıza emin olun!**

Shell prompt unuzun son karakteri $ yerine # ise, superuser olarak çalışıyorsunuz demektir. Bu, yönetici ayrıcalıklarına sahip olduğunuz anlamına gelir. Sistemdeki herhangi bir dosyayı silebileceğiniz veya üzerine yazabileceğiniz için bu tehlikeli olabilir. Kesinlikle yönetici ayrıcalıklarına ihtiyacınız olmadığı sürece superuser olarak çalışmayın.

### Fare’yi Kullanmak

Shell bir komut satırı arayüzü olmasına rağmen fare hala kullanışlıdır.

Terminal penceresinin içeriğini kaydırmak için fareyi kullanmanın yanı sıra, metni kopyalamak için de kullanabiliriz. Sol düğmeyi basılı tutarken fareyi bir metnin üzerine sürükleyin. Metin vurgulanmalıdır. Sol düğmeyi bırakın, fare işaretçisini terminal penceresine getirin ve orta fare düğmesine basın (alternatif olarak, dokunmatik yüzey üzerinde çalışırken hem sol hemde sağ düğmelere aynı anda basın). Pencerede vurguladığımız metin, komut satırına kopyalanacaktır.

## Navigasyon

Bu derste ilk üç komutumuzu tanıtacağız: **pwd** (print working directory), **cd** (change directory) ve **ls** (list files and directories).

Kavramlara alışmak biraz zaman alacağından, komut satırına yeni başlayanlar bu derse çok dikkat etmelidir.

### Dosya Sistemi Organizasyonu

Windows gibi, Linux sistemindeki dosyalar da hiyerarşik dizin yapısı adı verilen şekilde düzenlenir. Bu, dosyaları ve alt dizinleri içerebilen, ağaç benzeri bir dizin düzeninde (diğer sistemlerde klasörler olarak adlandırılır) organize edildikleri anlamına gelir. Dosya sistemindeki ilk dizine kök (root) dizin denir. Kök dizin, daha fazla dosya ve alt dizin vb. içeren dosyalar ve alt dizinler içerir.

Çoğu grafik ortam, dosya sisteminin içeriğini görüntülemek ve değiştirmek için kullanılan bir dosya yöneticisi programı içerir. Çoğu zaman dosya sisteminin şu şekilde temsil edildiğini göreceğiz:

![File Manager](/assets/images/2024/05/file-manager.jpg "File Manager"){: class="img-fluid"}

Windows ile Linux gibi Unix benzeri işletim sistemleri arasındaki önemli bir fark, Linux'un sürücü harfleri kavramını kullanmamasıdır. Windows sürücü harfleri dosya sistemini bir dizi farklı ağaca (her aygıt için bir tane) bölerken, Linux'te her zaman tek bir ağaç bulunur. Farklı depolama aygıtları ağacın farklı dalları olabilir ancak her zaman tek bir ağaç vardır.

### pwd

Komut satırı arayüzü dosya sistemi yapısının grafik resimlerini sağlayamadığı için onu farklı bir şekilde temsil etmemiz gerekir. Bunu yapmak için dosya sistemi ağacını bir labirent olarak ve bizim de onun içinde durduğumuzu düşünün. Herhangi bir anda tek bir dizinde bulunuruz. Bu dizinin içinde, dosyalarını ve ana dizinine giden yolu ve içinde bulunduğumuz dizinin alt dizinlerine giden yolları görebiliriz.

İçinde bulunduğumuz dizine çalışma dizini denir. Çalışma dizininin adını görmek için `pwd` komutunu kullanıyoruz.

```
[me@linuxbox me]$ pwd
/home/me
```

Linux sistemimize ilk giriş yaptığımızda çalışma dizini home olarak ayarlanmıştır. Dosyalarımızı buraya koyacağız. Çoğu sistemde, giriş dizini */home/kullanıcı_adı* olarak adlandırılacaktır, ancak sistem yöneticisinin isteğine göre herhangi bir şey olabilir.

Çalışma dizinindeki dosyaları listelemek için `ls` komutunu kullanırız.

```
[me@linuxbox me]$ ls
Desktop    Downloads         foo.txt  Pictures  Templates
Documents  examples.desktop  Music    Public    Videos
```

Bir sonraki derste `ls` konusuna geri döneceğiz. Bununla yapabileceğiniz pek çok eğlenceli şey var, ancak önce biraz yol adları ve dizinler hakkında konuşmamız gerekiyor.

### cd

Çalışma dizinini (labirentte durduğumuz yer) değiştirmek için `cd` komutunu kullanırız. Bunu yapmak için `cd` yazıp ardından istenen çalışma dizininin yol adını yazıyoruz. Yol adı, istediğimiz dizine ulaşmak için ağacın dalları boyunca izlediğimiz yoldur. Yol adları iki farklı şekilde belirtilebilir; mutlak yol adları veya göreli yol adları. Önce mutlak yol adlarına bakalım.

Mutlak bir yol adı kök dizinle başlar ve istenen dizine veya dosyaya giden yol tamamlanana kadar ağaç dalını takip eder. Örneğin sisteminizde çoğu programın kurulu olduğu bir dizin var. Dizinin yol adı */usr/bin*'dir. Bu, kök dizinde (yol adının başındaki eğik çizgiyle temsil edilir) "bin" adlı bir dizini içeren "usr" adlı bir dizin olduğu anlamına gelir.

Hadi şunu deneyelim:

```
[me@linuxbox me]$ cd /usr/bin
[me@linuxbox bin]$ pwd
/usr/bin
[me@linuxbox bin]$ ls
'['                                   mshortname
 2to3-2.7                             mshowfat
 411toppm                             mtools
 a2ps                                 mtoolstest
 a2ps-lpr-wrapper                     mtr
 aa-enabled                           mtrace
 aa-exec                              mtr-packet
 aclocal                              mtvtoppm
 aclocal-1.15                         mtype
 aconnect                             mutter
 acpi_listen                          mxtar
 add-apt-repository                   mzip
 addpart                              namei

and many more...
```

Artık mevcut çalışma dizinini */usr/bin* olarak değiştirdiğimizi ve dosyalarla dolu olduğunu görebiliriz. Shell prompt unun nasıl değiştiğine dikkat ettiniz mi? Kolaylık olması açısından genellikle çalışma dizininin adını görüntüleyecek şekilde ayarlanır.

Mutlak yol adı kök dizinden başlayıp hedefine gittiğinde, göreceli yol adı çalışma dizininden başlar. Bunu yapmak için dosya sistemi ağacındaki göreli konumları temsil eden birkaç özel gösterim kullanır. Bu özel gösterimler "." (nokta) ve ".." (nokta nokta).

"." gösterimi çalışma dizininin kendisini, ".." gösterimi ise çalışma dizininin üst dizinini belirtir. Nasıl çalıştığını görelim. Çalışma dizinini tekrar */usr/bin* olarak değiştirelim:

```
[me@linuxbox me]$ cd /usr/bin
[me@linuxbox bin]$ pwd
/usr/bin
```

Tamam, şimdi diyelim ki çalışma dizinini */usr/bin*'in ebeveyni olan */usr* ile değiştirmek istedik. Bunu iki farklı şekilde yapabiliriz. İlk olarak, mutlak yol adıyla:

```
[me@linuxbox bin]$ cd /usr
[me@linuxbox usr]$ pwd
/usr
```

Veya göreceli yol adıyla:

```
[me@linuxbox bin]$ cd ..
[me@linuxbox usr]$ pwd
/usr
```

Aynı sonuçlara sahip iki farklı metot. Hangisini kullanmalıyız? En az yazmayı gerektireni!

Aynı şekilde çalışma dizinini */usr*'dan */usr/bin*'e iki farklı şekilde değiştirebiliriz. İlk önce mutlak yol adı kullanarak:

```
[me@linuxbox usr]$ cd /usr/bin
[me@linuxbox bin]$ pwd
/usr/bin
```

Veya göreceli yol adıyla:

```
[me@linuxbox usr]$ cd ./bin
[me@linuxbox bin]$ pwd
/usr/bin
```

Burada belirtmemiz gereken önemli bir şey var. Çoğu durumda "./" işaretini atlayabiliriz:

```
[me@linuxbox usr]$ cd bin
```

Genel olarak, eğer bir şeye yol adı belirtmezsek, çalışma dizini varsayılacaktır. Bunun önemli bir istisnası var ama buna bir süre değinmeyeceğiz.

### Birkaç Kısayol

Eğer `cd` ve ardından hiçbir şey yazmazsak, çalışma dizinini ana dizinimize değiştirecektir.

İlgili bir kısayol `cd ~kullanıcı_adı` yazmaktır. Bu durumda, çalışma dizinini belirtilen kullanıcının ana dizinine değiştirecektir.

`cd -` yazmak çalışma dizinini bir öncekine değiştirir.

### Dosya Adlarıyla İlgili Önemli Bilgiler

1. Nokta karakteriyle başlayan dosya adları gizlenir. Bu, `ls -a` kullanmadığınız sürece `ls`'nin onları listelemeyeceği anlamına gelir. Hesabınız oluşturulduğunda, hesabınızla ilgili ayarları yapılandırmak için ana dizininize birkaç gizli dosya yerleştirildi. Daha sonra ortamımızı nasıl özelleştirebileceğinizi görmek için bu dosyalardan bazılarına daha yakından bakacağız. Ayrıca bazı uygulamalar, yapılandırma ve ayar dosyalarını gizli dosyalar olarak ana dizininize yerleştirir.
2. Unix gibi Linux'taki dosya adları büyük/küçük harfe duyarlıdır. "Dosya1" ve "dosya1" dosya adları farklı dosyaları ifade eder.
3. Linux'un Windows sistemleri gibi bir "dosya uzantısı" kavramı yoktur. Dosyaları istediğiniz şekilde adlandırabilirsiniz. Ancak Linux'un kendisi dosya uzantılarını umursamasa da birçok uygulama bunu önemser.
4. Linux, boşluklar ve noktalama işaretleri içerebilen uzun dosya adlarını desteklese de, noktalama işaretlerini nokta, kısa çizgi ve alt çizgi ile sınırlandırın. En önemlisi dosya adlarında boşluk kullanmayın. Bir dosya adındaki sözcükler arasındaki boşlukları temsil etmek istiyorsanız alt çizgi karakterlerini kullanın. Daha sonra kendinize teşekkür edeceksiniz.

## Etrafa Gözatın

Artık çalışma dizininden çalışma dizinine nasıl geçeceğimizi bildiğimize göre, Linux sistemimizde bir tur atacağız ve bu arada onu neyin harekete geçirdiği hakkında bazı şeyler öğreneceğiz. Ancak başlamadan önce yolculuğumuz sırasında işimize yarayacak bazı araçları öğrenmemiz gerekiyor. Bunlar:

- **ls** (dosyaları ve dizinleri listele)
- **less** (metin dosyalarını görüntüle)
- **file** (bir dosyanın içeriğini sınıflandırın)

### ls

`ls` komutu bir dizinin içeriğini listelemek için kullanılır. Muhtemelen en yaygın kullanılan Linux komutudur. Birçok farklı şekilde kullanılabilir. İşte bazı örnekler:

| Komut | Sonuç |
| --- | --- |
| `ls` | Çalışma dizinindeki dosyaları listele |
| `ls /bin` | */bin* dizinindeki (veya başka bir dizindeki) dosyaları listeleyin |
| `ls -l` | Çalışma dizinindeki dosyaları uzun formatta listeleyin |
| `ls -l /etc /bin` | */bin* dizinindeki ve */etc* dizinindeki dosyaları uzun formatta listeleyin |
| `ls -la ..` | Çalışma dizininin üst öğesindeki tüm dosyaları (nokta karakteriyle başlayan, normalde gizli olanlar dahil) uzun formatta listeleyin |

Bu örnekler aynı zamanda komutlarla ilgili önemli bir kavrama da işaret etmektedir. Çoğu komut şu şekilde çalışır:

```
command -options arguments
```

burada `command` komutun adıdır, `-options` komutun davranışında yapılan bir veya daha fazla ayarlamadır ve `arguments` ise komutun üzerinde çalıştığı bir veya daha fazla "şey"dir.

`ls` söz konusu olduğunda `ls`‘nin komutun adı olduğunu, `-a` ve `-l` gibi bir veya birden fazla seçeneğe sahip olabileceğini, bir veya birden fazla dosya veya dizin üzerinde çalışabildiğini görüyoruz.

**Uzun Formata Yakından Bir Bakış**

`ls` ile birlikte `-l` seçeneğini kullanırsak, listelenen dosyalar hakkında zengin bilgiler içeren bir dosya listesi elde edersiniz. İşte bir örnek:

```
-rw-------   1 me       me            576 Apr 17  2019 weather.txt
drwxr-xr-x   6 me       me           1024 Oct  9  2019 web_page
-rw-rw-r--   1 me       me         276480 Feb 11 20:41 web_site.tar
-rw-------   1 me       me           5743 Dec 16  2018 xmas_file.txt

----------     -------  -------  -------- ------------ -------------
    |             |        |         |         |             |
    |             |        |         |         |         File Name
    |             |        |         |         |
    |             |        |         |         +---  Modification Time
    |             |        |         |
    |             |        |         +-------------   Size (in bytes)
    |             |        |
    |             |        +-----------------------        Group
    |             |
    |             +--------------------------------        Owner
    |
    +----------------------------------------------   File Permissions
```

**File Name**
Dosyanın veya dizinin adı.

**Modification Time**
Dosyanın en son değiştirildiği zaman. Son değişiklik altı aydan daha uzun bir süre önce yapılmışsa tarih ve yıl görüntülenir. Aksi takdirde günün saati gösterilir.

**Size**
Dosyanın bayt cinsinden boyutu.

**Group**
Dosyanın sahibinin yanı sıra dosya izinlerine de sahip olan grubun adı.

**Owner**
Dosyanın sahibi olan kullanıcının adı.

**File Permissions**
Dosyanın erişim izinlerinin temsili. İlk karakter dosyanın türüdür. "-" normal (sıradan) bir dosyayı belirtir. "d" bir dizini belirtir. Üç karakterden oluşan ikinci grup, dosya sahibinin okuma, yazma ve yürütme haklarını temsil eder. Sonraki üçü dosya grubunun haklarını, son üçü ise diğer herkese verilen hakları temsil eder. Bunu daha sonraki bir derste daha ayrıntılı olarak tartışacağız.

### less

**less** metin dosyalarını görüntülememizi sağlayan bir programdır. Linux'u kontrol etmek ve yapılandırmak için kullanılan dosyaların çoğu insan tarafından okunabildiğinden bu çok kullanışlıdır.

**“Text” nedir?**

Bilgisayarda bilgiyi temsil etmenin birçok yolu vardır. Tüm metotlar, bilgi ile onu temsil etmek için kullanılacak bazı sayılar arasındaki ilişkinin tanımlanmasını içerir. Sonuçta bilgisayarlar yalnızca sayıları anlar ve tüm veriler sayısal gösterime dönüştürülür.

Bu gösterim sistemlerinden bazıları oldukça karmaşıktır (sıkıştırılmış multimedya dosyaları gibi), diğerleri ise oldukça basittir. En eski ve en basitlerinden biri ASCII metnidir. ASCII ("As-Key" olarak telaffuz edilir) “American Standard Code for Information Interchange” in kısaltmasıdır. Bu, ilk olarak Teletype makinelerinde klavye karakterlerini sayılarla eşleştirmek için kullanılan basit bir kodlama şemasıdır.

Metin, karakterlerin sayılara basitçe bire bir eşleştirilmesidir. Çok kompakttır. Elli karakterlik metin, elli baytlık veriye karşılık gelir. Bir Linux sisteminde birçok dosya metin biçiminde saklanır ve metin dosyalarıyla çalışan birçok Linux aracı vardır. Windows sistemleri bile bu formatın öneminin farkındadır. Tanınmış Notepad.exe programı, düz ASCII metin dosyaları için bir düzenleyicidir.

`less` programı basitçe şunu yazarak çağrılır:

```
less text_file
```

Bu dosyayı gösterecektir.

**less’i kontrol etmek**

Bir kez başlatıldığında, `less` metin dosyasını her seferinde bir sayfa olarak görüntüler. Metin dosyasında ilerlemek için “Page Up” ve “Page Down” tuşlarını kullanabiliriz. Çıkmak için "q" yazıyoruz. `less`’in kabul edeceği bazı komutlar şunlardır:

| Komut | Sonuç |
| --- | --- |
| Page Up veya b | Bir sayfa geriye git |
| Page Down veya space | Bir sayfa ileri kaydır |
| G | Metin dosyasının sonuna git |
| 1G | Metin dosyasının başına git |
| /characters | Belirtilen karakterleri metin dosyasında ileriye doğru arayın |
| n | Önceki aramayı tekrarla |
| h | Komut ve seçeneklerin tam listesini görüntüleyin |
| q | Çıkış |

### file

Linux sistemimizde dolaşırken, bir dosyayı görüntülemeye çalışmadan önce ne tür veriler içerdiğini belirlemek yararlı olacaktır. İşte bu noktada `file` komutu devreye giriyor. `file` bir dosyayı inceleyecek ve bize onun ne tür bir dosya olduğunu söyleyecektir.

`file` programını kullanmak için şunu yazmamız yeterli:

```
file name_of_file
```

`file` programı aşağıdakiler gibi çoğu dosya türünü tanıyabilir:

| Dosya Tipi | Açıklama | Metin olarak görüntülenebilir mi? |
| --- | --- | --- |
| ASCII text | Adı her şeyi söylüyor | Evet |
| Bourne-Again shell script text | Bir bash scripti | Evet |
| ELF 64-bit LSB executable | Yürütülebilir binary program | Hayır |
| ELF 64-bit LSB shared object | Paylaşılan bir kütüphane | Hayır |
| GNU tar archive | Arşiv dosyası. Dosya gruplarını depolamanın yaygın bir yolu | Hayır, listeyi görüntülemek için **tar tvf**'yi kullanın. |
| gzip compressed data | gzip ile sıkıştırılmış arşiv | Hayır |
| HTML document text | Web sayfası | Evet |
| JPEG image data | Sıkıştırılmış bir JPEG görüntüsü | Hayır |
| PostScript document text | PostScript dosyası | Evet |
| Zip archive data | Zip ile sıkıştırılmış arşiv | Hayır |

Çoğu dosya metin olarak görüntülenemiyor gibi görünse de, şaşırtıcı bir sayı olabilir. Bu özellikle önemli yapılandırma dosyaları için geçerlidir. Maceramız sırasında işletim sisteminin birçok özelliğinin metin yapılandırma dosyaları ve shell script dosyaları tarafından kontrol edildiğini göreceğiz. Linux'ta sır yoktur!

## Rehberli Tur

Turumuza çıkmanın vakti geldi. Aşağıda keşfedilecek bazı ilginç yerler listelenmektedir. Bu kesinlikle tam bir liste değil, ancak ilginç bir macera olduğu kanıtlanmalıdır. Aşağıda listelenen dizinlerin her biri için aşağıdakileri yapın:

- Her dizine `cd` ile ulaşın.
- Dizinin içeriğini listelemek için `ls` kullanın.
- İlginç bir dosya varsa içeriğini belirlemek için `file` komutunu kullanın.
- Metin dosyalarını görüntülemek için `less` kullanın.

| Dizin | Açıklama |
| --- | --- |
| / | Dosya sisteminin başladığı kök dizin. Kök dizin muhtemelen yalnızca alt dizinleri içerecektir. |
| /boot | Linux çekirdeği ve önyükleyici dosyalarının saklandığı yer burasıdır. Çekirdek vmlinuz adlı bir dosyadır. |
| /etc | /etc dizini sistemin yapılandırma dosyalarını içerir. /etc içindeki tüm dosyalar metin dosyaları olmalıdır. |
| /bin, /usr/bin | Bu iki dizin sistemdeki programların çoğunu içerir. /bin dizini sistemin çalışması için gereken temel programları içerirken, /usr/bin sistem kullanıcılarına yönelik uygulamaları içerir. |
| /sbin, /usr/sbin | sbin dizinleri, çoğunlukla superuser tarafından kullanılmak üzere sistem yönetimine yönelik programlar içerir. |
| /usr | /usr dizini kullanıcı uygulamalarını destekleyen çeşitli şeyleri içerir. |
| /usr/local | /usr/local ve alt dizinleri, local makinede kullanılacak yazılımın ve diğer dosyaların kurulumu için kullanılır. Bunun gerçekte anlamı, resmi dağıtımın parçası olmayan (genellikle /usr/bin dizinine giren) yazılımın buraya gelmesidir. Sisteminize kurabileceğiniz ilgi çekici programlar bulduğunuzda, bunların /usr/local dizinlerinden birine kurulması gerekir. Çoğunlukla tercih edilen dizin /usr/local/bin'dir. |
| /var | /var dizini sistem çalışırken değişen dosyaları içerir. /var/log log dosyaları içeren dizin. Bunlar sistem çalıştıkça güncellenir. Sisteminizin sağlığını izlemek için bu dizindeki dosyaları zaman zaman görüntülemek iyi bir fikirdir. /var/spool dizini, e-mail mesajları ve yazdırma işleri gibi bazı işlemler için kuyruğa alınan dosyaları tutmak için kullanılır. |
| /lib | Paylaşılan kütüphaneler (diğer işletim sistemindeki DLL'lere benzer) burada tutulur. |
| /home | /home kullanıcıların kişisel çalışmalarını sakladıkları yerdir. Genel olarak kullanıcıların dosya yazmasına izin verilen tek yer burasıdır. Bu, her şeyin güzel ve temiz kalmasını sağlar. |
| /root | Bu, superuser’ın ana dizinidir. |
| /tmp | /tmp, programların geçici dosyalarını yazabileceği bir dizindir. |
| /dev | /dev dizini özel bir dizindir, çünkü alışılagelmiş anlamda dosya içermez. Bunun yerine, sistem tarafından kullanılabilen cihazları içerir. Linux'ta (Unix gibi), cihazlara dosyalar gibi davranılır. Cihazları sanki dosyalarmış gibi okuyabilir ve yazabilirsiniz. Örneğin /dev/fd0 ilk disket sürücüsüdür, /dev/sda ise ilk sabit sürücüdür. Kernel’in anladığı tüm cihazlar burada temsil edilir. |
| /proc | /proc dizini de özeldir. Bu dizin dosya içermiyor. Aslında dizin mevcut değil, tamamen sanaldır. /proc dizini kernel’e küçük gözetleme delikleri içerir. Bu dizinde sistemde çalışan tüm proseslere karşılık gelen bir grup numaralı giriş (entry) vardır. Ek olarak, sistemin mevcut konfigürasyonuna erişime izin veren çok sayıda adlandırılmış giriş vardır. Bu girişlerin çoğu görüntülenebilir. /proc/cpuinfo dosyasını görüntülemeyi deneyin. Bu giriş size kernel’in sistem CPU'su hakkında ne düşündüğünü söyleyecektir. |
| /media | Son olarak, özel bir şekilde kullanılan normal bir dizin olan /media'ya geliyoruz. /media dizini bağlantı noktaları için kullanılır. İkinci derste öğrendiğimiz gibi, farklı fiziksel depolama aygıtları (sabit disk sürücüleri gibi) dosya sistemi ağacının çeşitli yerlerine eklenir. Ağaca bir cihaz bağlama işlemine mounting denir. Bir cihazın kullanılabilmesi için öncelikle mount edilmesi gerekir. Hangi aygıtların ve bağlantı noktalarının kullanıldığını görmek için mount yazın. |

**Tuhaf bir dosya türü...**

Turunuz sırasında muhtemelen özellikle */lib* dizininde garip bir dizin girişi fark etmişsinizdir. `ls -l` ile listelendiğinde şunun gibi bir şey görmüş olabilirsiniz:

```
lrwxrwxrwx     25 Jul  3 16:42 System.map -> /boot/System.map-4.0.36-3
-rw-r--r-- 105911 Oct 13  2018 System.map-4.0.36-0.7
-rw-r--r-- 105935 Dec 29  2018 System.map-4.0.36-3
-rw-r--r-- 181986 Dec 11  2019 initrd-4.0.36-0.7.img
-rw-r--r-- 182001 Dec 11  2019 initrd-4.0.36.img
lrwxrwxrwx     26 Jul  3 16:42 module-info -> /boot/module-info-4.0.36-3
-rw-r--r--  11773 Oct 13  2018 module-info-4.0.36-0.7
-rw-r--r--  11773 Dec 29  2018 module-info-4.0.36-3
lrwxrwxrwx     16 Dec 11  2019 vmlinuz -> vmlinuz-4.0.36-3
-rw-r--r-- 454325 Oct 13  2018 vmlinuz-4.0.36-0.7
-rw-r--r-- 454434 Dec 29  2018 vmlinuz-4.0.36-3
```

*System.map*, *module-info* ve *vmlinuz* dosyalarına dikkat edin. Dosya adlarından sonraki garip gösterimi görüyor musunuz?

Bunun gibi dosyalara sembolik link denir. Sembolik linkler, başka bir dosyaya işaret eden özel bir dosya türüdür. Sembolik linkler ile tek bir dosyanın birden fazla isme sahip olması mümkündür. Şu şekilde çalışır: Sisteme sembolik bir link olan dosya adı verildiğinde, sistem bunu şeffaf bir şekilde işaret ettiği dosyayla eşleştirir.

Peki bu ne işe yarar? Bu çok kullanışlı bir özelliktir. Yukarıdaki dizin listesine (eski bir sistemin */boot* dizini) bakalım. Bu sistemde Linux çekirdeğinin birden fazla sürümü kuruludur. Bunu vmlinuz-4.0.36-0.7 ve vmlinuz-4.0.36-3 dosyalarından görebiliriz. Bu dosya adları hem 4.0.36-0.7 hem de 4.0.36-3 sürümünün yüklü olduğunu göstermektedir. Dosya adları sürümü içerdiğinden, dizin listesindeki farklılıkları görmek kolaydır. Ancak bu, kernel dosyasının adını kullanan programlar için kafa karıştırıcı olabilir. Bu programlar kernel’in basitçe "vmlinuz" olarak adlandırılmasını bekleyebilir. Sembolik link’in güzelliği burada devreye giriyor. vmlinuz-4.0.36-3'e işaret eden vmlinuz adında sembolik bir link oluşturarak sorunu çözdük.

Sembolik link oluşturmak için `ln` komutunu kullanın.

## Dosyaları Düzenlemek

Bu derste aşağıdaki komutlar tanıtılacaktır:

- **cp** - dosyaları ve dizinleri kopyala
- **mv** - dosyaları ve dizinleri taşıyın veya yeniden adlandır
- **rm** - dosyaları ve dizinleri kaldır
- **mkdir** - dizin oluştur

Bu dört komut en sık kullanılan Linux komutları arasındadır. Bunlar hem dosyaları hem de dizinleri değiştirmek için kullanılan temel komutlardır. 

Açıkçası bu komutların gerçekleştirdiği bazı görevleri grafiksel dosya yöneticisi ile daha kolay bir şekilde gerçekleştirebilirsiniz. Dosya yöneticisiyle, bir dosyayı bir dizinden diğerine sürükleyip bırakabilir, dosyaları kesip yapıştırabilir, dosyaları silebilirsiniz vb. Peki neden bu eski komut satırı programlarını kullanasınız ki?

Cevap güç ve esnekliktir. Grafiksel dosya yöneticisiyle basit dosya işlemlerini gerçekleştirmek kolay olsa da, komut satırı programlarıyla karmaşık görevler daha kolay olabilir. Örneğin, tüm HTML dosyalarını bir dizinden diğerine nasıl kopyalarsınız, ancak yalnızca hedef dizinde bulunmayan veya hedef dizindeki sürümlerden daha yeni olan dosyaları kopyalarsınız? Dosya yöneticisiyle oldukça zor. Komut satırıyla oldukça kolay:

```
[me@linuxbox me]$ cp -u *.html destination
```
### Joker Karakterler

Komutlarımıza başlamadan önce, bu komutları bu kadar güçlü kılan shell özelliğine bakacağız. Shell dosya adlarını çok fazla kullandığından, dosya adı gruplarını hızla belirlemenize yardımcı olacak özel karakterler sağlar. Bu özel karakterlere joker karakterler denir. Joker karakterler, karakter paternlerine göre dosya adlarını seçmenize olanak tanır. Aşağıdaki tabloda joker karakterler ve bunların seçtikleri listelenmektedir:

| Joker Karakter | Anlamı |
| --- | --- |
| `*` | Herhangi bir karakterle eşleşir |
| `?` | Herhangi bir tek karakterle eşleşir |
| `[characters]` | Ayarlanan karakterlerin üyesi olan herhangi bir karakterle eşleşir. Karakter kümesi ayrıca aşağıdakilerden gibi bir POSIX karakter sınıfı olarak da ifade edilebilir: `[:alnum:]` Alfanümerik karakterler `[:alpha:]` Alfabetik karakterler `[:digit:]` Rakamlar `[:upper:]` Büyük harfli alfabetik karakterler `[:lower:]` Küçük harfli alfabetik karakterler |
| `[!characters]` | Ayarlanan karakterlerin üyesi olmayan herhangi bir karakterle eşleşir |

Joker karakterleri kullanarak dosya adları için çok karmaşık seçim kriterleri oluşturmak mümkündür. İşte bazı patern örnekleri ve bunların eşleştiği şeyler:

| Patern | Eşleşme |
| --- | --- |
| `*` | Tüm dosya adları |
| `g*` | "g" karakteriyle başlayan tüm dosya adları |
| `b*.txt` | "b" karakteriyle başlayan ve ".txt" karakteriyle biten tüm dosya adları |
| `Data???` | "Data" karakterleriyle başlayan ve ardından tam olarak 3 karakter daha gelen herhangi bir dosya adı |
| `[abc]*` | "a", "b" veya "c" ile başlayan ve ardından başka karakterler gelen herhangi bir dosya adı |
| `[[:upper:]]*` | Büyük harfle başlayan herhangi bir dosya adı. Bu bir karakter sınıfı örneğidir. |
| `BACKUP.[[:digit:]][[:digit:]]` | Karakter sınıflarına başka bir örnek. Bu patern, "BACKUP." karakterleriyle başlayan ve ardından tam olarak iki rakam gelen herhangi bir dosya adıyla eşleşir. |
| `*[![:lower:]]` | Küçük harfle bitmeyen herhangi bir dosya adı. |

Dosya adı argümanı kabul eden herhangi bir komutta joker karakterler kullanabiliriz.

### cp

`cp` programı dosyaları ve dizinleri kopyalar. En basit haliyle tek bir dosyayı kopyalar:

```
[me@linuxbox me]$ cp file1 file2
```

Birden fazla dosyayı (ve/veya dizini) farklı bir dizine kopyalamak için de kullanılabilir:

```
[me@linuxbox me]$ cp file... directory
```

**Gösterimle ilgili not:** ... bir öğenin bir veya daha fazla kez tekrarlanabileceğini belirtir.

`cp` ve seçeneklerinin diğer kullanışlı örnekleri şunları içerir:

| Komut | Sonuç |
| --- | --- |
| `cp file1 file2` | file1'in içeriğini file2'ye kopyalar. file2 mevcut değilse oluşturulur; aksi takdirde, file1'in içeriği file2'nin üzerine sessizce yazılır. |
| `cp -i file1 file2` | "-i" (etkileşimli) seçeneği belirtildiğinden, eğer file2 mevcutsa, file1'in içeriğinin üzerine yazılmadan önce kullanıcıya bilgi verilir. |
| `cp file1 dir1` | file1'in içeriğini (file1 adlı bir dosyaya) dir1 dizininin içine kopyalayın. |
| `cp -R dir1 dir2` | dir1 dizininin içeriğini kopyalayın. dir2 mevcut değilse oluşturulur. Aksi takdirde, dir2 dizini içerisinde dir1 adında bir dizin oluşturur. |

### mv

`mv` komutu, nasıl kullanıldığına bağlı olarak dosyaları ve dizinleri taşır veya yeniden adlandırır. Bir veya daha fazla dosyayı farklı bir dizine taşıyacak veya bir dosya veya dizini yeniden adlandıracaktır. Bir dosyayı yeniden adlandırmak için şu şekilde kullanılır:

```
[me@linuxbox me]$ mv filename1 filename2
```

Dosyaları (ve/veya dizinleri) farklı bir dizine taşımak için:

```
[me@linuxbox me]$ mv file... directory
```

`mv` ve seçeneklerinin örnekleri şunları içerir:

| Komut | Sonuç |
| --- | --- |
| `mv file1 file2` | file2 mevcut değilse, file1, file2 olarak yeniden adlandırılır. file2 mevcutsa, içeriği sessizce file1'in içeriğiyle değiştirilir. |
| `mv -i file1 file2` | "-i" (etkileşimli) seçeneği belirtildiğinden, eğer file2 mevcutsa, file1'in içeriği üzerine yazılmadan önce kullanıcıya bilgi verilir. |
| `mv file1 file2 dir1` | file1 ve file2 dosyaları dir1 dizinine taşınır. dir1 mevcut değilse, mv bir hatayla çıkacaktır. |
| `mv dir1 dir2` | dir1 mevcut değilse, dir1, dir2 olarak yeniden adlandırılır. dir2 mevcutsa, dir1 dizini dir2'nin içine taşınır. |

### rm

`rm` komutu dosyaları ve dizinleri kaldırır (siler).

```
[me@linuxbox me]$ rm file...
```

Özyinelemeli seçeneği (-r) kullanarak, dizinleri silmek için de kullanılabilir:

```
[me@linuxbox me]$ rm -r directory...
```

`rm` ve seçeneklerinin örnekleri şunları içerir:

| Komut | Sonuç |
| --- | --- |
| `rm file1 file2` | file1 ve file2'yi siler. |
| `rm -i file1 file2` | "-i" (etkileşimli) seçeneği belirtildiğinden, her dosya silinmeden önce kullanıcıya bilgi verilir. |
| `rm -r dir1 dir2` | dir1 ve dir2 dizinleri tüm içerikleriyle birlikte silinir. |

**rm’yi dikkati kullanın**

Linux'un silmeyi geri alma komutu yoktur. `rm` ile bir şeyi sildiğinizde, o şey gider. Özellikle joker karakterleri kullanırken dikkat etmezseniz `rm` ile sisteminize büyük zararlar verebilirsiniz.

`rm`'yi joker karakterlerle kullanmadan önce şu yararlı yöntemi deneyebilirsiniz: komutunuzu `ls` kullanarak oluşturun. Bunu yaparak, dosyaları silmeden önce joker karakterlerinizin etkisini görebilirsiniz. Komutunuzu `ls` ile test ettikten sonra yukarı ok tuşuyla komutu çağırın ve `ls` yerine `rm` yazın.

### mkdir

`mkdir` komutu dizin oluşturmak için kullanılır. Kullanmak için şunu yazmanız yeterlidir:

```
[me@linuxbox me]$ mkdir directory...
```

### Komutları Joker Karakterlerle Kullanmak

Burada ele aldığımız komutlar birden fazla dosya ve dizin adını argüman olarak kabul ettiğinden, bunları belirtmek için joker karakterler kullanabilirsiniz. İşte birkaç örnek:

| Komut | Sonuç |
| --- | --- |
| `cp *.txt text_files` | Geçerli çalışma dizinindeki adları ".txt" karakterleriyle biten tüm dosyaları, text_files adlı mevcut bir dizine kopyalar. |
| `mv dir1 ../*.bak dir2` | dir1 alt dizinini ve geçerli çalışma dizininin üst dizinindeki ".bak" ile biten tüm dosyaları dir2 adlı mevcut bir dizine taşır. |
| `rm *~` | Geçerli çalışma dizinindeki "~" karakteriyle biten tüm dosyaları siler. Bazı uygulamalar bu adlandırma şemasını kullanarak yedekleme dosyaları oluşturur. Bu komutu kullanmak onları dizinden temizleyecektir. |

## Komutlarla Çalışmak

Şu ana kadar çok sayıda komutu ve bunların gizemli seçeneklerini ve argümanlarını gördük. Bu derste bu gizemin bir kısmını ortadan kaldırmaya çalışacağız. Aşağıdaki komutları tanıtacağız.

- **type** - Komut türüyle ilgili bilgileri görüntüler
- **which** - Bir komut bulun
- **help** - Shell yerleşik referans sayfasını görüntüler
- **man** - Çevrimiçi komut referansını görüntüler

### "Komut" Nedir?

Komutlar 4 farklı türden biri olabilir:

- `/usr/bin`'de gördüğümüz tüm dosyalar gibi **çalıştırılabilir bir program**. Bu kategoride programlar, C ve C++ ile yazılmış programlar gibi derlenmiş binary dosyalar veya shell, Perl, Python, Ruby vb. script dillerinde yazılmış programlar olabilir.
- **Shell’in kendisinde yerleşik bir komut**. bash, dahili olarak shell yerleşikleri (builtin) adı verilen bir dizi komut sağlar. Örneğin `cd` komutu shell yerleşiğidir.
- **Shell fonksiyonu**. Bunlar ortama dahil edilen minyatür shell script dosyalarıdır. Daha sonraki derslerde ortamı (environment) yapılandırmayı ve shell fonksiyonları yazmayı ele alacağız, ancak şimdilik bunların var olduğunu unutmayın.
- **Takma ad (alias)**. Kendimiz tanımlayabileceğimiz, diğer komutlardan oluşan komutlar. Bu daha sonra ele alınacaktır.

### Komutları Tanımlamak

Dört tür komuttan hangisinin kullanıldığını bilmek genellikle faydalıdır ve Linux bunu öğrenmenin birkaç yolunu sunar.

**type**

`type` komutu, belirli bir komut adı verildiğinde shell’in yürüteceği komut türünü görüntüleyen shell yerleşiğidir. Şu şekilde çalışır:

```
type command
```

burada “command” incelemek istediğimiz komutun adıdır. İşte bazı örnekler:

```
[me@linuxbox me]$ type type
type is a shell builtin
[me@linuxbox me]$ type ls
ls is aliased to 'ls --color=auto'
[me@linuxbox me]$ type cp
cp is /bin/cp
```

Burada üç farklı komut için sonuçları görüyoruz. ls komutunun aslında “-- color=auto” seçeneğinin eklendiği ls komutunun takma adı olduğuna dikkat edin. Artık ls çıktısının neden renkli görüntülendiğini biliyoruz!

**which**

Bazen bir sistemde programın birden fazla sürümü kurulu olabilir. Bu, masaüstü sistemlerde çok yaygın olmasa da, büyük sunucularda alışılmadık bir durum değildir. Belirli bir dosyanın tam konumunu belirlemek için `which` komutu kullanılır:

```
[me@linuxbox me]$ which ls
/bin/ls
```

`which` yalnızca çalıştırılabilir programlar için işe yarar, gerçek çalıştırılabilir programların yerine geçen yerleşikler veya takma adlar için geçerli değildir.

### Komut Dokümantasyonunun Alınması

Komutun ne olduğuna dair bu bilgiyle, artık her komut türü için mevcut belgeleri arayabiliriz.

**help**

bash, shell yerleşiklerinin her biri için yardım olanağına sahiptir. Kullanmak için “help” ve ardından shell yerleşiğinin adını yazın. İsteğe bağlı olarak çıktının formatını değiştirmek için `-m` seçeneğini ekleyebiliriz. Örneğin:

```
[me@linuxbox me]$ help -m cd
NAME
    cd - Change the shell working directory.

SYNOPSIS
    cd [-L|-P] [dir]

DESCRIPTION
    Change the shell working directory.
    
    Change the current directory to DIR.  The default DIR is the value of the
    HOME shell variable.
    
    The variable CDPATH defines the search path for the directory containing
    DIR.  Alternative directory names in CDPATH are separated by a colon (:).
    A null directory name is the same as the current directory.  If DIR begins
    with a slash (/), then CDPATH is not used.
    
    If the directory is not found, and the shell option `cdable_vars' is set,
    the word is assumed to be  a variable name.  If that variable has a value,
    its value is used for DIR.
    
    Options:
        -L  force symbolic links to be followed
        -P  use the physical directory structure without following symbolic
      links
    
    The default is to follow symbolic links, as if `-L' were specified.
    
    Exit Status:
    Returns 0 if the directory is changed; non-zero otherwise.

SEE ALSO
    bash(1)

IMPLEMENTATION
    GNU bash, version 4.1.5(1)-release (i486-pc-linux-gnu)
    Copyright (C) 2009 Free Software Foundation, Inc.
```

**Gösterimle ilgili not**: Bir komutun sözdiziminin açıklamasında köşeli parantezler göründüğünde, isteğe bağlı öğeleri belirtir. Dikey çubuk karakteri birbirini dışlayan öğeleri belirtir. Yukarıdaki `cd` komutu durumunda:

```
cd [-L|-P] [dir]
```

Bu gösterim, `cd` komutunun ardından isteğe bağlı olarak "-L" veya "-P" ve ayrıca isteğe bağlı olarak "dir" argümanının gelebileceğini belirtir.

**--help**

Yürütülebilir programların çoğu, komutun desteklenen söz dizimi ve seçeneklerinin açıklamasını görüntüleyen "--help" seçeneğini destekler. Örneğin:

```
[me@linuxbox me]$ mkdir --help
Usage: mkdir [OPTION] DIRECTORY...
Create the DIRECTORY(ies), if they do not already exist.
Mandatory arguments to long options are mandatory for short options
too.

   -Z, --context=CONTEXT (SELinux) set security context to CONTEXT
   -m, --mode=MODE   set file mode (as in chmod), not a=rwx - umask
   -p, --parents     no error if existing, make parent directories as
                     needed
   -v, --verbose     print a message for each created directory
   --help            display this help and exit
   --version         output version information and exit
```

Bazı programlar “--help” seçeneğini desteklemez ama yine de deneyin.

**man**

Komut satırı kullanımına yönelik çoğu yürütülebilir program, manual veya man sayfası adı verilen resmi bir dökümantasyon sağlar. Bunları görüntülemek için `man` programı kullanılır:

```
man program
```

burada “program” görüntülenecek komutun adıdır. Man sayfaları format olarak biraz farklılık gösterir ancak genellikle başlık, komut sözdiziminin özetini, komut amacının açıklamasını ve komut seçeneklerinin her birinin listesini ve açıklamasını içerir. Ancak Man sayfaları genellikle örnekler içermez ve bir tutorial olarak değil, referans olarak tasarlanmıştır. `ls` komutu için man sayfasını görüntülemeyi deneyelim:

```
[me@linuxbox me]$ man ls
```

Çoğu Linux sisteminde `man`, kılavuz sayfasını görüntülemek için `less` kullanır, böylece bilinen `less` komutlarının tümü sayfayı görüntülerken çalışır.

## I/O Yönlendirmesi

Bu derste, input/output yönlendirmesi adı verilen, komut satırı programlarının kullandığı güçlü bir özelliği inceleyeceğiz. Gördüğümüz gibi `ls` benzeri birçok komut çıktılarını ekrana yazdırır. Ancak böyle olmak zorunda değildir. Bazı özel gösterimler kullanarak birçok komutun çıktısını dosyalara, aygıtlara ve hatta diğer komutların girişine yönlendirebiliriz.

### Standart Output

Sonuçlarını görüntüleyen çoğu komut satırı programı, sonuçlarını standart output adı verilen bir araca göndererek bunu yapar. Varsayılan olarak standart output, içeriği ekrana yönlendirir. Standart output’u bir dosyaya yönlendirmek için ">" karakteri şu şekilde kullanılır:

```
[me@linuxbox me]$ ls > file_list.txt
```

Bu örnekte `ls` komutu çalıştırılıyor ve sonuçlar *file_list.txt* adlı bir dosyaya yazılıyor. `ls` çıktısı dosyaya yönlendirildiği için ekranda hiçbir sonuç görünmüyor.

Yukarıdaki komut her tekrarlandığında, `ls` komutunun çıktısı baştan itibaren *file_list.txt* dosyasının üzerine yazılır. Bunun yerine yeni sonuçların dosyaya eklenmesini sağlamak için ">>" ifadesini şu şekilde kullanırız:

```
[me@linuxbox me]$ ls >> file_list.txt
```

Yeni sonuçlar dosyanın sonuna eklenir, böylece komut her tekrarlandığında dosya daha uzun hale gelir. Dosya mevcut değilse, dosya oluşturulacaktır.

### Standard Input

Birçok komut, standart input adı verilen bir özellikten giriş kabul edebilir. Varsayılan olarak standart input, içeriğini klavyeden alır ancak standart output gibi yeniden yönlendirilebilir. Standart input’u klavye yerine bir dosyadan yönlendirmek için "<" karakteri şu şekilde kullanılır:

```
[me@linuxbox me]$ sort < file_list.txt
```

Yukarıdaki örnekte *file_list.txt* içeriğini işlemek için `sort` komutunu kullandık. Standart output yönlendirilmediği için sonuçlar ekranda görüntülenir. Standart output’u şu şekilde başka bir dosyaya yönlendirebiliriz:

```
[me@linuxbox me]$ sort < file_list.txt > sorted_file_list.txt
```

Gördüğümüz gibi, bir komutun hem girişi hem de çıkışı yeniden yönlendirilebilir. Yönlendirme sırasının önemli olmadığını unutmayın. Tek gereksinim, yeniden yönlendirme işleçlerinin ("<" ve ">") komuttaki diğer seçeneklerden ve parametrelerden sonra görünmesi gerektiğidir.

### Pipeline

I/O yönlendirmeyle yapabileceğimiz en kullanışlı ve güçlü şey, birden çok komutu birbirine bağlayarak pipeline adı verilen şeyi oluşturmaktır. Pipeline ile bir komutun standart output’u diğerinin standart input’unu besler. İşte çok yararlı bir örnek:

```
[me@linuxbox me]$ ls -l | less
```

Bu örnekte `ls` komutunun çıktısı `less`'e aktarılmıştır. Bu `| less` hilesini kullanarak herhangi bir komutun kayan çıktıya sahip olmasını sağlayabiliriz.

Komutları birbirine bağlayarak inanılmaz başarılar elde edebiliriz. İşte deneyebileceğiniz bazı örnekler:

| Komut | Ne Yapar |
| --- | --- |
| `ls -lt | head` | Geçerli dizindeki en yeni 10 dosyayı görüntüler. |
| `du | sort -nr` | Dizinlerin listesini ve ne kadar alan tükettiklerini büyükten küçüğe doğru sıralanmış olarak görüntüler. |
| `find . -type f -print | wc -l` | Geçerli çalışma dizinindeki ve onun tüm alt dizinlerindeki toplam dosya sayısını görüntüler. |

### Filtreler

Pipeline’da sıklıkla kullanılan program türlerinden birine filtre adı verilir. Filtreler standart input’u alıp üzerinde işlem yapar ve sonuçları standart output’a gönderir. Bu şekilde, bilgiyi işlemek için birleştirilebilirler. Filtre olarak kullanılan yaygın programlardan bazıları şunlardır:

| Program | Ne Yapar |
| --- | --- |
| `sort` | Standart input’u sıralar ve sıralanan sonucu standart output’a verir. |
| `uniq` | Standart input’tan sıralanmış bir veri akışı verildiğinde, yinelenen veri satırlarını kaldırır (yani her satırın benzersiz olmasını sağlar). |
| `grep` | Standart input’tan aldığı her veri satırını inceler ve belirli bir karakter patern’ini içeren her satırın çıktısını alır. |
| `fmt` | Standart input’taki metni okur, ardından biçimlendirilmiş metni standart output’a verir. |
| `pr` | Metin girişini standart input’tan alır ve yazdırmaya hazırlık amacıyla verileri sayfa sonları, üstbilgiler ve altbilgilerle sayfalara böler. |
| `head` | İlk birkaç satırının çıktısını verir. Bir dosyanın başlığını almak için kullanışlıdır. |
| `tail` | Son birkaç satırının çıktısını verir. Log dosyasından en son girişleri almak gibi şeyler için kullanışlıdır. |
| `tr` | Karakterleri çevirir. Büyük/küçük harf dönüştürme veya satır sonlandırma karakterlerini bir türden diğerine değiştirme (örneğin, DOS metin dosyalarını Unix tarzı metin dosyalarına dönüştürme) gibi görevleri gerçekleştirmek için kullanılabilir. |
| `sed` | Akış düzenleyicisi. `tr`'ye göre daha karmaşık metin çevirileri gerçekleştirebilir. |
| `awk` | Filtreler oluşturmak için tasarlanmış eksiksiz bir programlama dili. Son derece güçlüdür. |

## İzinler

Linux gibi Unix benzeri işletim sistemleri, yalnızca çoklu görev değil aynı zamanda çok kullanıcılı olmaları açısından diğer sistemlerden farklıdır.

Bu tam olarak ne anlama geliyor? Bu, birden fazla kullanıcının aynı anda bilgisayarı çalıştırabileceği anlamına gelir. Masaüstü veya dizüstü bilgisayarda yalnızca bir klavye ve monitör bulunurken yine de birden fazla kullanıcı tarafından kullanılabilir. Örneğin bilgisayar bir ağa veya internete bağlıysa uzaktaki kullanıcılar ssh (secure shell) aracılığıyla giriş yapıp bilgisayarı çalıştırabilirler. Aslında uzaktaki kullanıcılar grafik uygulamaları çalıştırabilir ve çıktının uzaktaki bir bilgisayarda görüntülenmesini sağlayabilir. X Window sistemi bunu desteklemektedir.

Unix benzeri sistemlerin çok kullanıcılı özelliği, işletim sisteminin tasarımına derinlemesine yerleşmiş bir özelliktir. Unix'in yaratıldığı ortamı hatırlarsak, bu çok mantıklıdır. Yıllar önce bilgisayarlar "kişisel" olmadan önce büyük, pahalı ve merkeziydi. Tipik bir üniversite bilgisayar sistemi, kampüsteki bazı binalarda bulunan büyük bir ana bilgisayardan ve kampüs boyunca her biri büyük merkezi bilgisayara bağlanan terminallerden oluşuyordu. Bilgisayar aynı anda birçok kullanıcıyı destekleyecektir.

Bunu pratik hale getirebilmek için kullanıcıları birbirinden koruyacak bir yöntemin geliştirilmesi gerekiyordu. Sonuçta ne bir kullanıcının eylemlerinin bilgisayarın çökmesine neden olmasını, ne de bir kullanıcının başka bir kullanıcıya ait dosyalara müdahale etmesine izin veremeyiz.

Bu ders aşağıdaki komutları kapsayacaktır:

- **chmod** - dosya erişim haklarını değiştirin
- **su** - geçici olarak superuser olun
- **sudo** - geçici olarak superuser olun
- **chown** - dosya sahipliğini değiştirin
- **chgrp** - dosyanın grup sahipliğini değiştirin

### Dosya İzinleri

Bir Linux sisteminde, her dosya ve dizine, dosyanın sahibine, ilgili kullanıcı grubunun üyelerine ve diğer herkese erişim hakları atanır. Bir dosyayı okumak, bir dosyayı yazmak ve bir dosyayı yürütmek (yani dosyayı bir program olarak çalıştırmak) için haklar atanabilir.

Bir dosyanın izin ayarlarını görmek için `ls` komutunu kullanabiliriz. Örnek olarak */bin* dizininde bulunan bash programına bakacağız:

```
[me@linuxbox me]$ ls -l /bin/bash
-rwxr-xr-x 1 root root 1113504 Jun  6  2019 /bin/bash
```

Burada şunları görebiliriz:

- "/bin/bash" dosyası "root" kullanıcısına aittir
- superuser’ın bu dosyayı okuma, yazma ve yürütme hakkı vardır
- Dosyanın sahibi "root" grubudur
- "root" grubunun üyeleri de bu dosyayı okuyabilir ve çalıştırabilir
- Herkes bu dosyayı okuyabilir ve çalıştırabilir

Aşağıdaki şemada listenin ilk bölümünün nasıl yorumlandığını görüyoruz. Dosya türünü belirten bir karakterden ve ardından dosya sahibi, grup ve diğer herkes için okuma, yazma ve yürütme iznini gösteren üç karakterli üç setten oluşur.

![File Permissions](/assets/images/2024/05/file-permissions.png "File Permissions"){: class="img-fluid"}

### chmod

`chmod` komutu bir dosyanın veya dizinin izinlerini değiştirmek için kullanılır. Kullanmak için istenen izin ayarlarını ve değiştirmek istediğimiz dosya veya dosyaları belirtiyoruz. İzinleri belirtmenin iki yolu vardır. Bu derste bunlardan biri olan sekizli notasyon yöntemine odaklanacağız.

İzin ayarlarını bir dizi bit olarak düşünmek kolaydır (bilgisayarda onlar hakkında böyle düşünür). Şöyle çalışıyor:

```
rwx rwx rwx = 111 111 111
rw- rw- rw- = 110 110 110
rwx --- --- = 111 000 000

ve benzeri...

rwx = 111 in binary = 7
rw- = 110 in binary = 6
r-x = 101 in binary = 5
r-- = 100 in binary = 4
```

Şimdi, eğer üç izin kümesinin (sahip, grup ve diğer) her birini tek basamaklı olarak temsil edersek, izin ayarlarını ifade etmenin oldukça uygun bir yoluna sahip oluruz. Örneğin, *some_file*‘ın sahibi için okuma ve yazma iznine sahip olacak şekilde ayarlamak istiyor ancak dosyayı diğerlerinden gizli tutmak istiyorsak, şunu yaparız:

```
[me@linuxbox me]$ chmod 600 some_file
```

Burada tüm ortak ayarları kapsayan bir sayı tablosu verilmiştir. "7" ile başlayan dosyalar programlarla (yürütmeyi mümkün kıldıkları için) kullanılır, geri kalanlar ise diğer dosya türleri içindir.

| Değer | Anlamı |
| --- | --- |
| 777 | (rwxrwxrwx) İzinlerde herhangi bir kısıtlama yoktur. Herkes her şeyi yapabilir. Genellikle arzu edilen bir ayar değildir. |
| 755 | (rwxr-xr-x) Dosyanın sahibi dosyayı okuyabilir, yazabilir ve çalıştırabilir. Diğerleri dosyayı okuyabilir ve çalıştırabilir. Bu ayar, tüm kullanıcılar tarafından kullanılan programlar için ortaktır. |
| 700 | (rwx------) Dosyanın sahibi dosyayı okuyabilir, yazabilir ve çalıştırabilir. Başka hiç kimsenin hiçbir hakkı yoktur. Bu ayar yalnızca sahibinin kullanabileceği ve diğerlerinden gizli tutulması gereken programlar için kullanışlıdır. |
| 666 | (rw-rw-rw-) Tüm kullanıcılar dosyayı okuyabilir ve yazabilir. |
| 644 | (rw-r--r--) Dosyanın sahibi bir dosyayı okuyup yazabilir, diğer herkes ise yalnızca dosyayı okuyabilir. Veri dosyaları için herkesin okuyabileceği, ancak yalnızca sahibinin değiştirebileceği ortak bir ayar. |
| 600 | (rw-------) Sahibi bir dosyayı okuyabilir ve yazabilir. Diğerlerinin hiçbir hakkı yoktur. Sahibinin gizli tutmak istediği veri dosyaları için ortak bir ayar. |

### Dizin İzinleri

chmod komutu, dizinlere erişim izinlerini kontrol etmek için de kullanılabilir. İzinleri ayarlamak için yine sekizli gösterimi kullanabiliriz ancak r, w ve x niteliklerinin anlamı farklıdır:

- **r** - x niteliği de ayarlanmışsa dizin içeriğinin listelenmesine izin verir.
- **w** - x niteliği de ayarlanmışsa, dizin içindeki dosyaların oluşturulmasına, silinmesine veya yeniden adlandırılmasına izin verir.
- **x** - Bir dizinin girilmesine izin verir (örn. **cd dir**).

Dizinler için bazı kullanışlı ayarlar şunlardır:

| Değer | Anlamı |
| --- | --- |
| 777 | (rwxrwxrwx) İzinlerde herhangi bir kısıtlama yoktur. Herkes dosyaları listeleyebilir, dizinde yeni dosyalar oluşturabilir ve dizindeki dosyaları silebilir. Genellikle iyi bir ayar değildir. |
| 755 | (rwxr-xr-x) Dizin sahibinin tam erişimi vardır. Diğerleri dizini listeleyebilir ancak dosya oluşturamaz veya silemez. Bu ayar, diğer kullanıcılarla paylaşmak istediğiniz dizinler için ortaktır. |
| 700 | (rwx------) Dizin sahibinin tam erişimi vardır. Başka hiç kimsenin hiçbir hakkı yoktur. Bu ayar, yalnızca sahibinin kullanabileceği ve diğerlerinden gizli tutulması gereken dizinler için kullanışlıdır |

### Kısa Süreliğine Superuser Olmak

Önemli sistem yönetimi görevlerini gerçekleştirmek için genellikle superuser olmak gerekir, ancak bildiğimiz gibi superuser olarak oturum açmamalıyız. Çoğu dağıtımda, superuser ayrıcalıklarına geçici olarak erişmenizi sağlayacak bir program vardır. Bu programa `su` (substitute user kelimesinin kısaltması) adı verilir ve az sayıda görev için superuser olmanızın gerektiği durumlarda kullanılabilir. Superuser olmak için `su` komutunu yazmanız yeterlidir. Superuser şifresi istenecektir:

```
[me@linuxbox me]$ su
Password:
[root@linuxbox me]#
```

`su` komutunu çalıştırdıktan sonra superuser olarak yeni bir shell oturumu açılır. Superuser oturumundan çıkmak için, `exit` yazın ve önceki oturumunuza dönün.

Çoğu modern dağıtımda alternatif bir yöntem kullanılır. Bu sistemler `su` kullanmak yerine `sudo` komutunu kullanır. sudo ile bir veya daha fazla kullanıcıya gerektiğinde superuser ayrıcalıkları verilir. Bir komutu superuser olarak yürütmek için istenen komutun önüne `sudo` komutu gelir. Komut girildikten sonra kullanıcıdan superuser şifresi yerine kendi şifresi istenir:

```
[me@linuxbox me]$ sudo some_command
Password for me:
[me@linuxbox me]$
```

Aslında, modern dağıtımlar root hesap şifresini bile ayarlamadığından, root kullanıcı olarak oturum açmayı imkansız hale getirir. "-i" seçeneği kullanılarak sudo ile root shell hala mümkündür:

```
[me@linuxbox me]$ sudo -i
Password for me:
root@linuxbox:~#
```

### Dosya Sahipliğini Değiştirmek

`chown` komutunu kullanarak bir dosyanın sahibini değiştirebiliriz. İşte bir örnek: Diyelim ki *some_file*’ın sahibini "me" yerine "you" olarak değiştirmek istiyoruz.

```
[me@linuxbox me]$ sudo chown you some_file
```

Bir dosyanın sahibini değiştirmek için superuser ayrıcalıklarına sahip olmamız gerektiğine dikkat edin. Bunu yapmak için örneğimizde `sudo` komutunu kullandık.

`chown`, dosyalarda olduğu gibi dizinlerde de aynı şekilde çalışır.

### Grup Sahipliğini Değiştirmek

Bir dosyanın veya dizinin grup sahipliği `chgrp` ile değiştirilebilir. Bu komut şu şekilde kullanılır:

```
[me@linuxbox me]$ chgrp new_group some_file
```

Yukarıdaki örnekte, *some_file*'ın grup sahipliğini "new_group" olarak değiştirdik. `chgrp` işlemini gerçekleştirmek için dosyanın veya dizinin sahibi olmamız gerekir.

## İş Kontrolü

Önceki derste Linux'un çok kullanıcılı bir işletim sistemi olmasının bazı sonuçlarına baktık. Bu dersimizde Linux'un çoklu görev yapısını ve komut satırı arayüzü ile nasıl kontrol edildiğini inceleyeceğiz.

Herhangi bir çoklu görev işletim sisteminde olduğu gibi, Linux’te birden fazla eşzamanlı proses yürütür. Aslında, tek bir işlemci çekirdeği aynı anda yalnızca bir proses yürütebilir, ancak Linux kernel’i işleme sırasını işlemciye vermeyi başarır ve her biri aynı anda çalışıyor gibi görünür.

Süreçleri kontrol etmek için kullanılan çeşitli komutlar vardır. Bunlar:

- **ps** - sistemde çalışan prosesleri listeleyin
- **kill** - bir veya daha fazla prosese sinyal gönderin (genellikle prosesi "sonlandırmak" için)
- **jobs** - kendi proseslerinizi listelemenin alternatif bir yolu
- **bg** - prosesi arka plana koyar
- **fg** - prosesi ön plana koyar

### Pratik Bir Örnek

Bu konu oldukça belirsiz gibi görünse de çoğunlukla grafik kullanıcı arayüzü ile çalışan ortalama bir kullanıcı için oldukça pratik olabilir. Görünür olmasa da, grafik programların çoğu komut satırından başlatılabilir. İşte bir örnek: X Window sistemiyle birlikte sağlanan **xload** adında, sistem yükünü temsil eden bir grafik görüntüleyen küçük bir program var. Bu programı aşağıdakileri yazarak çalıştırabiliriz:

```
[me@linuxbox me]$ xload
```

Küçük **xload** penceresinin göründüğüne ve sistem yük grafiğini görüntülemeye başladığına dikkat edin. Xload'ın kullanılamadığı sistemlerde bunun yerine **gedit**'i deneyin. Program başlatıldıktan sonra prompt un yeniden görünmediğine de dikkat edin. Shell, geri dönmeden önce programın bitmesini bekliyor. Eğer **xload** penceresini kapatırsak **xload** programı sonlandırılır ve prompt geri döner.

### Programı Arka Plana Yerleştirmek

Şimdi hayatı biraz daha kolaylaştırmak için xload programını tekrar başlatacağız ancak bu sefer prompt un geri dönmesi için onu arka plana koyacağız. Bunu yapmak için xload'ı şu şekilde çalıştırıyoruz:

```
[me@linuxbox me]$ xload &
[1] 1223
[me@linuxbox me]$
```

Bu durumda proses arka planda tutulduğu için prompt geri döndü.

Şimdi programı arka plana yerleştirmek için "&" sembolünü kullanmayı unuttuğumuzu hayal edin. Hala umut var. Ctrl-z ile proses askıya alınacaktır. Bunu programın penceresinin donmuş olduğunu görerek doğrulayabiliriz. Proses hala mevcut ancak boşta. Proses i arka planda sürdürmek için `bg` komutunu yazın (background’un kısaltması). İşte bir örnek:

```
[me@linuxbox me]$ xload
[2]+ Stopped xload

[me@linuxbox me]$ bg
[2]+ xload &
```

### Çalışan Proseslerin Listelenmesi

Artık arka planda bir prosesimiz olduğuna göre, başlattığımız proseslerin listesini görüntülemek faydalı olacaktır. Bunu yapmak için `jobs` komutunu veya daha güçlü olan `ps` komutunu kullanabiliriz.

```
[me@linuxbox me]$ jobs
[1]+ Running xload&

[me@linuxbox me]$ ps
PID  TTY   TIME     CMD
1211 pts/4 00:00:00 bash
1246 pts/4 00:00:00 xload
1247 pts/4 00:00:00 ps

[me@linuxbox me]$
```

### Proses’i Sonlandırmak

Yanıt vermeyen bir programımız olduğunu varsayalım; ondan nasıl kurtuluruz? Elbette `kill` komutunu kullanıyoruz. Bunu xload'da deneyelim. Öncelikle sonlandırmak istediğimiz prosesi tanımlamamız gerekiyor. Bunu yapmak için `jobs` veya `ps` komutunu kullanabiliriz. Eğer `jobs` kullanırsak bir job numarası alacağız. `ps` ile bize bir proses id (PID) verilir. Bunu her iki şekilde de yapalım:

```
[me@linuxbox me]$ xload &
[1] 1292

[me@linuxbox me]$ jobs
[1]+ Running xload&

[me@linuxbox me]$ kill %1

[me@linuxbox me]$ xload &
[2] 1293
[1] Terminated xload

[me@linuxbox me]$ ps
PID  TTY   TIME     CMD
1280 pts/5 00:00:00 bash
1293 pts/5 00:00:00 xload
1294 pts/5 00:00:00 ps

[me@linuxbox me]$ kill 1293
[2]+ Terminated xload
[me@linuxbox me]$
```

### kill Hakkında Biraz Daha Fazla Bilgi

`kill` komutu prosesleri "sonlandırmak" için kullanılsa da asıl amacı proseslere sinyal göndermektir. Çoğu zaman sinyalin amacı proses’in sona erdiğini söylemektir ancak bundan daha fazlası vardır. Programlar (eğer doğru şekilde yazılmışlarsa) işletim sisteminden gelen sinyalleri dinler ve bunlara yanıt verirler; çoğu zaman zarif bir sonlandırma yöntemine izin verirler. Örneğin bir metin düzenleyici, kullanıcının oturumu kapattığını veya bilgisayarın kapandığını gösteren herhangi bir sinyali dinleyebilir. Bu sinyali aldığında, devam eden işi çıkmadan önce kaydedebilir. `kill` komutu proseslere çeşitli sinyaller gönderebilir. Şu komutla:

```
kill -l
```

desteklediği sinyallerin bir listesini yazdıracaktır. Birçoğu oldukça belirsizdir, ancak bazılarını bilmek faydalıdır:

| Sinyal # | Ad | Açıklama |
| --- | --- | --- |
| 1 | SIGHUP | Kapatma sinyali. Programlar bu sinyali dinleyebilir ve ona göre hareket edebilir. Bu sinyal, terminali kapattığınızda terminalde çalışan proseslere gönderilir. |
| 2 | SIGINT | Kesinti (interrupt) sinyali. Bu sinyal proseslere onları kesmeleri için verilir. Programlar bu sinyali işleyebilir ve ona göre hareket edebilir. Bu sinyali programın çalıştığı terminal penceresine Ctrl-c yazarak doğrudan verebiliriz. |
| 15 | SIGTERM | Sonlandırma sinyali. Bu sinyal proseslere onları sonlandırmak için verilir. Yine programlar bu sinyali işleyebilir ve ona göre hareket edebilir. Bu, herhangi bir sinyal belirtilmemişse kill komutunun gönderdiği varsayılan sinyaldir. |
| 9 | SIGKILL | Kill sinyali. Bu sinyal, proses’in Linux kerneli tarafından anında sonlandırılmasına neden olur. Programlar bu sinyali dinleyemez. |

Şimdi askıda kalan bir programımız olduğunu ve ondan kurtulmak istediğimizi varsayalım. İşte yaptığımız şey:

- Sonlandırmak istediğimiz proses’in id’sini (PID) almak için `ps` komutunu kullanın.
- Bu PID için `kill` komutu verin.
- Proses sonlandırılmayı reddediyorsa (yani sinyali görmezden geliyorsa), sonlandırılana kadar giderek daha sert sinyaller gönderin.

```
[me@linuxbox me]$ ps x | grep bad_program
PID  TTY   STAT TIME COMMAND
2931 pts/5 SN   0:00 bad_program

[me@linuxbox me]$ kill -SIGTERM 2931

[me@linuxbox me]$ kill -SIGKILL 2931
```

Yukarıdaki örnekte, tüm proseslerimizi (mevcut terminalden başlatılmayanlar dahil) listelemek için `ps` komutunu `x` seçeneğiyle birlikte kullandık. Ek olarak, yalnızca ilgilendiğimiz programı listelemek için ps komutunun çıktısını grep'e aktardık. Daha sonra, sorunlu programa SIGTERM sinyali vermek için `kill`'i kullandık. Gerçek uygulamada kill tarafından gönderilen varsayılan sinyal SIGTERM olduğundan ve `kill` sinyal adı yerine sinyal numarasını da kullanabileceğinden, bunu aşağıdaki şekilde yapmak daha yaygındır:

```
[me@linuxbox me]$ kill 2931
```

Ardından, proses sonlandırılmazsa SIGKILL sinyaliyle zorlayın:

```
[me@linuxbox me]$ kill -9 2931
```

**Kaynak:** [LinuxCommand.org](https://linuxcommand.org/)