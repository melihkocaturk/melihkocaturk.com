---
layout: post
title: RabbitMQ – Tavşan Canlanıyor
---

Mesajlaşma veya mesaj kuyruğu, uygulamalar veya bileşenler arasında bir iletişim yöntemidir. Mesaj kuyrukları sayesinde bu uygulamalar, kendi görevlerini yerine getirirken tamamen ayrı kalabilir. Mesajlar genellikle küçük istekler, yanıtlar, durum güncellemeleri ve hatta yalnızca bilgilerdir. Mesaj kuyruğu, bu mesajların kalması için geçici bir yer sağlar ve uygulamaların gerektiğinde bunları göndermesine ve almasına olanak tanır.

RabbitMQ, bağımsız uygulamalar için aracı veya aracı olarak hareket eden ve onlara iletişim kurmaları için ortak bir platform sağlayan açık kaynaklı bir mesaj aracısıdır. RabbitMQ, uygulamaların iletişim kurmaları için ortak bir platform sağlayan açık kaynaklı bir araçtır.

Bu bölüm, RabbitMQ'ya nasıl başlanacağı ve bunun bir mimariye neden fayda sağlayacağı hakkında bilgi içerir. Bu kitap, RabbitMQ'nun mimariye nasıl uygulandığını göstermek için hayali bir taksi acentesi olan Complete Car'ı (CC) takip ediyor. Bu bölümde RabbitMQ'nun nasıl kurulacağı ve yapılandırılacağı gösterilmektedir.

Bu bölüm aşağıdaki konuları kapsayacaktır:

- Mesaj kuyruklarını anlamak
- AMQP ve RabbitMQ'yu keşfetmek
- RabbitMQ'yu gerçek hayatta kullanmak
- Mesaj kuyruklarının faydalarını keşfetmek
- Bir RabbitMQ senaryosu
- RabbitMQ’yu hazırlamak

Hadi başlayalım!

## Mesaj kuyruklarını anlamak

Duman sinyalleri, kuryeler, postacı güvercinler ve semaforlar: Bu bir bilmece olsaydı, akla hemen mesaj kelimesi gelirdi. İnsanlığın her zaman bağlantı kurma, iletişim kurmaya ihtiyaç duyan farklı insan grupları arasındaki mesafenin getirdiği zorluklara meydan okumanın yeni yollarını bulma ihtiyacı olmuştur. İnsanlık, modern teknolojilerle uzun bir yol kat etti, ancak esasen temeller değişmedi. Göndericiler, alıcılar ve mesajlar, tüm iletişim altyapılarımızın merkezinde yer alır.

Yazılım uygulamaları aynı ihtiyaçlara sahiptir; sistemlerin birbirleri arasında iletişim kurması ve mesaj göndermesi gerekir. Bazen gönderilen mesajın hedefine ulaştığından emin olmaları, bazen de anında yanıt almaları gerekir. Hatta bazı durumlarda birden fazla yanıt almaları gerekebilir. Bu farklı ihtiyaçlara dayalı olarak, sistemler arasında farklı iletişim biçimleri ortaya çıkmıştır.

RabbitMQ'nun varsayılan protokolü olan AMQP sonraki bölümde açıklanmaktadır.

## AMQP ve RabbitMQ'yu Keşfetmek

Mesaj kuyruğu, sistemler arasında eşzamansız etkileşim sağlayan tek yönlü bir iletişim tarzıdır. Bu bölüm, mesaj kuyruklarının nasıl çalıştığını açıklamaya devam ettikçe, faydaları netleşecektir. İstek-yanıt (request-response) mesaj alışveriş modeliyle ilgili bazı bilgiler, RabbitMQ'nun nasıl çalıştığına ışık tutacaktır.

### İstek-yanıt mesaj alışveriş modeli

Pek çok mesaj alışveriş modeli türü vardır, ancak istek-yanıt stili en yaygın olanıdır. Client olarak hareket eden bir sistem, server olarak hareket eden başka bir uzak sistemle etkileşime girer. Client, veri için bir istek gönderir ve server, aşağıdaki şemada gösterildiği gibi isteğe yanıt verir:

![Request Response Model](/assets/images/2024/01/request-response-model.png "Client ve server arasındaki istek-yanıt etkileşimi"){: class="img-fluid"}

İstek-yanıt stili, müşterinin anında bir yanıt alması gerektiğinde veya servisin bir görevi gecikmeden tamamlamasını istediğinde, örneğin bir restorana masa ayırtmak için çağrıldığında beklemeye alınması gerektiğinde kullanılır:

![Request Response Style](/assets/images/2024/01/request-response-style.png "Müşteri ile restoran arasındaki istek-yanıt"){: class="img-fluid"}

İster uzaktan prosedür çağrısı, ister web hizmeti çağrısı veya bir kaynağın kullanımı şeklinde olsun, model aynıdır: bir sistem diğerine bir mesaj gönderir ve uzak tarafın yanıt vermesini bekler. Olayların ve süreçlerin aynı anda meydana geldiği veya zamana bağlı bağımlılıkların veya olayların olduğu sistemler, birbirleriyle noktadan noktaya iletişim kurar; istemci ve sunucu arasındaki etkileşim senkronizedir.

Bir yandan, bu istek-yanıt stili, geliştiricilere her şey prosedürel olarak gerçekleştiği için basit bir programlama modeli sağlar. Öte yandan, her iki taraf arasındaki sıkı bağlantı, tüm sistemin mimarisi üzerinde derin bir etkiye sahiptir, çünkü bağımsız sürümlerde geliştirilmesi, ölçeklenmesi ve piyasaya sürülmesi zordur.

İster uzaktan prosedür çağrısı, ister web hizmeti çağrısı veya bir kaynağın kullanımı şeklinde olsun, model aynıdır: bir sistem diğerine bir mesaj gönderir ve uzak tarafın yanıt vermesini bekler. Olayların ve süreçlerin aynı anda meydana geldiği veya zamana bağlı bağımlılıkların veya olayların olduğu sistemler, birbirleriyle noktadan noktaya iletişim kurar; istemci ve sunucu arasındaki etkileşim senkronizedir.

Bir yandan, bu istek-yanıt stili, geliştiricilere her şey prosedürel olarak gerçekleştiği için basit bir programlama modeli sağlar. Öte yandan, her iki taraf arasındaki sıkı bağlantı, tüm sistemin mimarisi üzerinde derin bir etkiye sahiptir, çünkü bağımsız sürümlerde geliştirilmesi, ölçeklenmesi ve piyasaya sürülmesi zordur.

### Mesaj kuyruğu modeli

Mesaj kuyruğu, bir sistemin mesajlar aracılığıyla, genellikle bir message broker aracılığıyla başka bir sistemle eşzamansız olarak etkileşime girdiği tek yönlü bir etkileşim tarzıdır. Eşzamansız iletişim modunda talepte bulunan bir sistem, bir cevap beklemez veya bilgi dönüşü gerektirmez; ne olursa olsun çalışmaya devam eder. Böyle bir etkileşimin en yaygın örneği e-postadır. Önemli olan, eşzamansız iletişimin çalışmaya devam etmek için bir yanıt beklemeyi içermemesidir. Aslında, yanıt gelmeyebilir veya yanıtın gönderilmesi biraz zaman alabilir. Durum ne olursa olsun, sistem çalışmaya devam etmek için bir cevaba güvenmez.

Mesajlar, publisher’dan broker’a ve son olarak consumer’a tek yönde akar:

![One Way Interaction with Message Queuing](/assets/images/2024/01/one-way-interaction-with-message-queuing.png "Mesaj kuyruğu ile tek yönlü etkileşimin temel bileşenleri"){: class="img-fluid"}

Sistemler ve uygulamalar, hem publisher (üretici) hem de consumer (abone) rolünü oynar. Publisher, verileri hedeflenen consumer’a ulaştırmak için güvendiği bir broker’a mesaj gönderir. Bir yanıt gerekirse, aynı mekanizma aracılığıyla zamanın bir noktasında ulaşacaktır, ancak akış tersine dönecektir (consumer ve publisher rolleri yer değiştirecektir).

### Gevşek bağlı mimari

Mesajları kuyruğa alma yaklaşımının büyük bir avantajı, sistemlerin birbiriyle gevşek bir şekilde bağlı hale gelmesidir. Ağdaki diğer düğümlerin yerini bilmeleri gerekmez; onlara ulaşmak için sadece bir isim yeterlidir. Bu nedenle, mesaj iletiminin güvenilirliği broker’a  emanet edildiğinden, sistemler birbirini etkilemeden bağımsız bir şekilde geliştirilebilir.

Aşağıdaki şema, publisher ile consumer arasındaki gevşek bağlantıyı göstermektedir:

![Loosely Coupled Architecture](/assets/images/2024/01/loosely-coupled-architecture.png "Gevşek bağlı bir mimariye olanak sağlayan mesaj kuyruğu"){: class="img-fluid"}

Sistemlerden biri herhangi bir nedenle çökerse, sistemin diğer kısmı çalışmaya devam eder ve aralarında gönderilmesi gereken mesajlar kuyrukta bekler.

Mesaj kuyruğu ile temsil edilen mimari aşağıdakilere izin verir:

- Publisher veya consumer’lar, birbirini etkilemeden tek tek güncellenebilir.
- Her iki tarafın performansı diğer tarafı etkilemez.
- Publisher veya consumer’ların birbirlerini etkilemeden başarısız olmalarına izin verilir.
- Publisher veya consumer’ların iş yüklerini ölçeklendirecekleri ve barındıracakları örneklerin sayısı tamamen bağımsızdır.
- Publisher veya consumer’lar arasında teknoloji karışımı.

Bu yaklaşımın dezavantajı, programcıların olayların birbiri ardına meydana geldiği prosedürel programlamanın zihinsel modeline güvenememeleridir. Mesajlaşmada, işler zamanla olur. Sistemler bununla başa çıkmak için programlanmalıdır.

Bütün bunlar biraz bulanıksa, iyi bilinen bir protokol olan Simple Mail Transfer Protocol (SMTP) örneğini kullanın. Bu protokolde, e-postalar bir SMTP sunucusuna yayınlanır (gönderilir). Bu ilk sunucu daha sonra e-postayı depolar ve bir sonraki SMTP sunucusuna iletir ve alıcı e-posta sunucusuna ulaşılana kadar bu böyle devam eder. Bu noktada, mesaj gelen kutusunda kuyruğa alınır ve consumer tarafından alınmayı bekler (genellikle POP3 veya IMAP aracılığıyla). SMTP ile, publisher’ın e-postanın ne zaman teslim edileceği veya sonunda teslim edilip edilmeyeceği hakkında hiçbir fikri yoktur. Teslimatın başarısız olması durumunda, publisher sorunlar hakkında daha sonra bilgilendirilir.

Kesin olan tek şey, broker’ın başlangıçta gönderilen mesajı başarıyla kabul etmiş olmasıdır. Tüm bu süreç aşağıdaki şemada görülebilir:

![SMTP Server](/assets/images/2024/01/smtp-server.png "Mesaj kuyruğu için örnek olarak e-posta altyapısı"){: class="img-fluid"}

Ayrıca, yanıta ihtiyaç duyulursa, aynı mekanizma kullanarak ancak publisher ve consumer rolleri tersine çevrilerek eşzamansız olarak ulaşacaktır.

Temel kavramlar oluşturulduktan sonra, bu kitapta kullanılacak mesajlaşma protokolünü, yani AMQP'yi incelemenin tam zamanı.

### AMQP ile tanışın

AMQP, bir sistemin nasıl mesaj alışverişi yapabileceğini tanımlayan açık standart bir protokoldür. Protokol, birbirleriyle haberleşecek sistemlerin uyması gereken bir dizi kural tanımlar. Consumer/producer ile broker arasında meydana gelen etkileşimi tanımlamanın yanı sıra, değiş tokuş edilen mesajların ve komutların temsilini de tanımlar. AMQP, mesajların formatını belirlediği ve belirli bir hosting platformu tarafından yorumlanmaya açık hiçbir şey bırakmadığı için gerçekten birlikte çalışabilir. Açık kaynak olduğu için AMQP topluluğu üretkendir ve çok çeşitli dillerde broker ve client uygulamalarına sahiptir.

RabbitMQ, AMQP 0-9-1 spesifikasyonu üzerine kurulmuştur, ancak AMQP 1.0'ı destekleyen eklentiler mevcuttur.

Aşağıda, gelecek bölümlerde ayrıntılı olarak açıklanacak olan AMQP'nin temel kavramlarının bir listesi bulunmaktadır:

- **Broker veya mesaj broker:** Broker, bir uygulama veya servisten mesajları alan ve bunları başka bir uygulamaya, servise veya broker’a teslim eden bir yazılım parçasıdır.
- **Virtual host, vhost:** Broker içinde bir vhost bulunur. Broker içindeki mantıksal container’a benzer şekilde, aynı RabbitMQ örneğini kullanan uygulamaları ayırmanın bir yoludur; örneğin, çalışma ortamlarını vhost üzerinde development ve diğerinde staging olarak ayırmak, birden fazla broker kurmak yerine bunları aynı broker içinde tutmak. Kullanıcılar, exchange’ler, kuyruklar vb. belirli bir vhost’ta izole edilir. Belirli bir vhost’a bağlı bir kullanıcı, başka bir vhost’tan herhangi bir kaynağa (queue, exchange vb.) erişemez.
- **Connection:** Uygulama (publisher/consumer) ile broker arasındaki fiziksel ağ (TCP) bağlantısı. Client bağlantısı kesildiğinde veya bir sistem hatası oluştuğunda bağlantı kapatılır.
- **Channel:** Channel, connection içindeki sanal bir bağlantıdır. Yeniden yetkilendirme ve yeni bir TCP akışı açma gereksinimini ortadan kaldırarak connection’ı yeniden kullanır. Mesajlar channel üzerinden yayınlanır veya alınır. Tek bir connection içinde birçok channel kurulabilir.
- **Exchange:** Exchange, mesajların nihai hedeflerine ulaştığından emin olarak, mesajlar için yönlendirme kurallarını uygulamaktan sorumludur. Başka bir deyişle, exchange, alınan mesajın doğru kuyruklarda son bulmasını sağlar. Mesajın hangi kuyruğa gireceği, exchange type tarafından tanımlanan kurallara bağlıdır. Mesaj alabilmek için kuyruğun en az bir exchange’e bağlı olması gerekir. Routing (yönlendirme) kuralları, direct (noktadan noktaya), topic (publish-subscribe), fanout (multicast) ve header değişimlerini içerir.
- **Queue (Kuyruk):** Kuyruk, bir dizi öğedir; bu durumda, mesajlardan oluşur. Kuyruk, broker içinde bulunur.
- **Binding:** Binding, exchange ile broker içindeki kuyruk arasındaki sanal bir bağlantıdır.

Aşağıdaki diyagram, AMQP'deki bazı kavramlara genel bir bakışı göstermektedir:

![AMQP Specification](/assets/images/2024/01/amqp-specification.png "AMQP spesifikasyonu tarafından tanımlanan bazı kavramlara genel bakış"){: class="img-fluid"}

Bu kitapta ayrıntılı olarak gösterilen açık kaynak broker, sıfırdan AMQP'yi desteklemek için oluşturulmuştur, ancak MQTT, HTTP ve STOMP gibi diğer birçok protokol de RabbitMQ tarafından desteklenmektedir.

Şimdi, odağı RabbitMQ'ya çevirme zamanı.

### RabbitMQ broker

RabbitMQ, AMQP broker Erlang uygulamasıdır. Protokol tarafından izin verildiği şekilde, AMQP'nin 0-9-1 sürümünü özel uzantılarla uygular. Erlang, son derece güvenilir olduğu ve dağıtılmış uygulamalar oluşturmaya yönelik içsel desteği nedeniyle seçilmiştir. Gerçekten de Erlang, birkaç büyük telekomünikasyon sisteminde telekomünikasyon anahtarlarını çalıştırmak için kullanılır ve sistemin dokuz dokuzluk kullanılabilirliği rapor edilmiştir (bu, yılda yalnızca 32 milisaniye kesinti süresidir). Erlang herhangi bir işletim sisteminde çalışabilir.

Veri kalıcılığı için RabbitMQ, Erlang'ın bellek içi/dosya kalıcı yerleşik veritabanı olan Mnesia'ya güvenir. Mnesia, kullanıcı, exchange, kuyruk, binding vb. hakkındaki bilgileri depolar. Kuyruk dizini, mesaj konumlarını ve bir mesajın teslim edilip edilmediğiyle ilgili bilgileri saklar. Mesajlar, kuyruk dizininde veya tüm kuyruklar arasında paylaşılan bir anahtar/değer deposu olan mesaj deposunda depolanır.

Clustering (Kümeleme) için, esas olarak Erlang'ın kökleşmiş cluster yeteneklerine dayanır. RabbitMQ, eklentilerin eklenmesiyle kolayca genişletilebilir. Örneğin bu mekanizma sayesinde RabbitMQ üzerinde web tabanlı bir yönetim paneli kurulabilir.

RabbitMQ, tek, standalone örnekte veya birden çok sunucuda cluster olarak kurulabilir:

![Standalone Instance or Cluster](/assets/images/2024/01/standalone-instance-or-cluster.png "Standalone veya birden çok sunucuda cluster olarak"){: class="img-fluid"}

RabbitMQ broker’ları, broker’lar arasında akıllı mesaj yönlendirme ve birden çok data center’a yayılma kapasitesi ile mesajlaşma topolojileri oluşturmak için federation gibi farklı teknikler kullanılarak birbirine bağlanabilir.

Aşağıdaki ekran görüntüsü, dünyanın farklı yerlerinde bulunan RabbitMQ brokerleri arasındaki federasyonu göstermektedir:

![RabbitMQ Federation Topology](/assets/images/2024/01/rabbitmq-federation-topology.png "Çeşitli topolojilerle bağlanan RabbitMQ broker"){: class="img-fluid"}

Peki, RabbitMQ ne zaman ve nerede kullanılır? Bir sonraki bölümde, RabbitMQ için bazı yaygın kullanım durumları açıklanmaktadır.

## RabbitMQ'yu gerçek hayatta kullanmak

RabbitMQ için en yaygın kullanım durumu, tek bir producer, tek bir consumer kuyruğudur. Bunu, bir uygulamanın borunun bir ucuna mesajlar koyduğu ve diğer uygulamanın diğer uçtan çıkan mesajları okuduğu düşünün. Mesajlar ilk giren ilk çıkar metoduna göre teslim edilir. Bu mesajlar komutlar olabilir veya önemli veriler içerebilir. Kulağa kolay geliyor, ancak bu tür bir mimari nerede uygulanabilir? Mesaj kuyruğunun ne zaman ve neden öne çıktığını anlamanın zamanı geldi!

### Mikro servisler arasındaki mesaj kuyrukları

Mesaj kuyrukları genellikle mikro servisler arasında kullanılır, ancak bu ne anlama geliyor?

Mikro servis mimari stili, uygulamayı mikro servislerin toplamı olan bitmiş uygulama ile küçük servislere böler. Servisler kesinlikle birbirine bağlı değildir. Bunun yerine, iletişim halinde olmak için örneğin mesaj kuyruklarını kullanırlar. Servis, mesajları eşzamansız olarak bir kuyruğa gönderir ve bu mesajlar, consumer hazır olduğunda doğru hedefe teslim edilir.

Mikro servis mimarisi, genellikle tüm sistemin tek bir yazılım parçası halinde toplandığı monolit mimari ile karşılaştırılır. Bir uygulama yalnızca belirli bir görevden sorumlu değildir; aslında belirli bir işlevi tamamlamak için gereken her adımı gerçekleştirir. Tüm parçalar aynı süreçte çalıştığı için monolit sistem içinde iletişim kurar. Her işlev diğerlerine bağlı olduğundan, bu sistem oldukça bağlantılıdır.

Monolit mimari stili üzerine inşa edilmiş bir web mağazası örneğinde, aşağıdaki şemada gösterildiği gibi envanter, ödemeler, yorum ve derecelendirmeler vb. dahil olmak üzere tüm işlevleri tek bir sistem yönetir:

![Monolith Architecture Style](/assets/images/2024/01/monolith-architecture-style.png "Monolit mimari tarzında inşa edilmiş bir web mağazası"){: class="img-fluid"}

Mikro servis mimarisi üzerine inşa edilmiş bir web mağazası ise, sistemin her bir parçasının ayrı bir faaliyet olduğu anlamına gelir. İncelemeleri ve derecelendirmeleri tek bir mikro servis yönetir. Ardından, aşağıdaki şemada gösterildiği gibi, envanter ve ardından ödemeler için başka bir servis vardır ve bu böyle devam eder:

![Micro Services Single Business Capability](/assets/images/2024/01/micro-services-single-business-capability.png "Her parçanın tek bir işe odaklandığı bir mikro servis mimarisi stili."){: class="img-fluid"}

Her istek ve yanıt çifti bağımsız olarak iletişim kurar. Bu, stateless communication olarak bilinir.

Birçok mikro servis söz konusu olsa da, doğrudan birbirlerine bağımlı değildirler.

RabbitMQ için başka bir tipik kullanım durumu, bir sonraki bölümde ele alacağımız görev sırasıdır.

### Olay ve görevler

Olaylar (Events), bir şey olduğunda uygulamalara haber veren bildirimlerdir. Bir uygulama, başka bir uygulamadaki olaylara abone olabilir ve kendileri için görevler oluşturup yöneterek yanıt verebilir. Tipik bir kullanım, RabbitMQ'nun yavaş işlemleri işleyen bir görev kuyruğu gibi davranmasıdır.

Bunun iki örneğini inceleyelim:

- Instagram gibi bir sosyal medya uygulaması düşünün. Birisi her yeni gönderi yayınladığında, ağ (takipçiler) yeni gönderi hakkında bilgilendirilmelidir. Bu çok zaman alan bir işlem olabilir. Milyonlarca insan aynı anda aynı görevi yapmaya çalışıyor olabilir. Uygulama, mesaj kuyruğunu kullanarak, gelen her gönderi için kuyruğa bir görev ekleyebilir. worker isteği aldığında, gönderen için bir takipçi listesi alır ve her birini günceller.
- Başka bir örnek olarak, binlerce kullanıcıya binlerce e-posta gönderen bir e-posta haber bülteni kampanya aracı düşünün. Birçok kullanıcının aynı anda toplu mesajları tetiklediği olası bir senaryo ile. E-posta haber bülteni kampanya aracının bu hacimdeki mesajları işleyebilmesi gerekir. Tüm bu e-postalar, worker’a neyin kime gönderileceğine ilişkin talimatlar içeren bir kuyruğa eklenebilir. Tüm e-postalar gönderilene kadar her bir e-posta tek tek işlenir.

Aşağıdaki diyagram, mesajların önce kuyruğa girdiği ve ardından işlendiği bir görev kuyruğunu göstermektedir. Yeni görevler daha sonra başka bir kuyruğa eklenir:

![Event and Task Queue](/assets/images/2024/01/event-and-task-queue.png "Olay ve görev kuyruğu"){: class="img-fluid"}

## Mesaj kuyruklarının faydalarını keşfetmek

Çeşitli uygulamalar arasındaki iletişim, dağıtık sistemlerde önemli bir rol oynar. Mesaj kuyruğunun ne zaman kullanılabileceğine dair pek çok örnek vardır, bu nedenle mikro servis mimarilerinde mesaj kuyruğu oluşturmanın bazı özelliklerini ve faydalarını vurgulayalım:

- **Geliştirme ve bakımı kolaylaştırır:** Bir uygulamayı birden çok servise bölmek, sorumlulukları ayırabilmenizi sağlar ve geliştiricilere belirli bir servis için seçilen herhangi bir dilde kod yazma özgürlüğü verir. Yazılı kodu korumak ve sistemde değişiklik yapmak daha kolay olacaktır; tek bir kimlik doğrulama şemasını güncellerken, diğer işlevleri kesintiye uğratmadan yalnızca kimlik doğrulama modülünde test için kod eklenmiş olmalıdır.
- **Hata izolasyonu:** Hata tek bir modülde izole edilebilir ve bu nedenle diğer servisleri etkilemeyecektir. Başka bir örnek olarak, raporlama servisinde değişiklik yapmak, müşterilerin raporları görüntüleyemediklerinde bile temel işlemleri gerçekleştirmelerine olanak tanır.
- **Gelişmiş hız ve üretkenlik düzeyi:** Farklı geliştiriciler aynı anda farklı modüller üzerinde çalışabilirler. Geliştirme döngüsünü hızlandırmanın yanı sıra, test aşaması mikro servislerin ve mesaj kuyruklarının kullanımından da etkilenir. Bunun nedeni, genel sistemin hazır olup olmadığını belirlemek için her servisin kendi başına test edilebilmesidir.
- **Geliştirilmiş ölçeklenebilirlik:** Mikro servisler aynı zamanda istendiğinde zahmetsiz ölçeklendirmeye de olanak tanır. Mesaj kuyruğu büyüyorsa daha fazla consumer eklemek mümkündür. Yalnızca bir servise yeni bileşenler eklemek, başka herhangi bir servisi değiştirmeden kolayca yapılabilir.
- **Anlaşılması kolay:** Mikro servis mimarisindeki her modül tek bir işlevi temsil ettiğinden, bir görevle ilgili ayrıntıları öğrenmek kolaydır. Örneğin, tek bir servis için danışman kiralamak, tüm sistemi anlamalarını gerektirmez.

Şimdi bu bilgi oldukça yeterli, bu yüzden kitabın geri kalanı için sahneyi hazırlayan RabbitMQ senaryosuna dalmak için iyi bir zaman.

## Bir RabbitMQ senaryosu

CC, büyük potansiyele sahip yeni bir taksi acentesidir. Bugün şirketin sadece iki taksi şoförü ve iki geliştiricisi var, ancak önümüzdeki yıl çok fazla büyümek istiyorlar. CC zaten Ruby'de bir web sitesi oluşturdu ve CC yolculuklarını veritabanında depolayan yine Ruby'de yazılmış bir backend ile başladı. CC'de ayrıca rota raporları oluşturan Python'da yazılmış bazı komut dosyaları vardır.

Şimdiye kadar CC'nin sistemi şu şekilde çalışıyor:

- Şirketin web sitesi ve blogu Ruby'de çalışıyor.
- Yolculuğun başlangıç noktası ve bitiş noktası gibi rota verilerini depolayan Zengin İnternet Uygulaması Ruby'de yazılmıştır.
- Sürücülere rota güncellemelerini gönderen ve Ruby'de yazılmış bir back ofis var.
- Rota raporları oluşturmak üzere verileri ayıklamak ve iletmek için birden çok Python script’i kullanılır.
- Taksi uygulamaları Python ile yazılmıştır.

Eski mimari şu şekilde gösterilmektedir:

![CC Software Landscape](/assets/images/2024/01/cc-software-landscape.png "CC yazılım mimarisi"){: class="img-fluid"}

CC neden zaten yoğun olan bir ortama RabbitMQ eklemeyi düşünüyor? Bunun ana nedeni, CC'nin müşterilerine sunmak istediği yeni bir özelliktir - hareket halindeyken rezervasyonları işleyen bir taksi uygulaması oluşturmak istemeleridir. CC ayrıca acısız bir şekilde ölçeklendirme yapabilmek istiyor. Plan, kullanıcıların bir akıllı telefon aracılığıyla araba rezervasyonu yapabileceği, rezervasyon onayı alabileceği ve yolculuğun başlangıç noktasına yaklaşan arabayı görebileceği bir uygulama oluşturmaktır.

CC'nin halihazırda farklı dillerde bazı servisleri olduğundan ve kolayca ölçeklenebilmek istediğinden, uygulama, client ve backend arasında eşzamansız iletişim için RabbitMQ gibi hazır mesaj odaklı bir ara yazılım kullanmaya karar verdiler.

CC'nin RabbitMQ bilgisi ve kullanımı arttıkça, onu ortamda kullanmak için yeni fırsatlar keşfedecekler. Şimdilik, RabbitMQ ile çalışmaya ilk adımını atan CC'yi takip edelim.

## RabbitMQ’yu hazırlamak

Başlamak için aşağıdaki üç kurulum ve yapılandırma adımının tamamlanması gerekir:

- RabbitMQ broker yükleme
- Yönetim eklentisini yükleme (Web UI)
- Vhost  ve kullanıcıyı yapılandırma

Broker’ı yükleyerek başlayalım!

### Broker’ı yüklemek

CC, production sunucularını Ubuntu Linux üzerinde çalıştırır. Bir geliştirici macOS ve Linux'a sahipken diğeri tamamen Windows'a sahip. Bu heterojenlik, tüm bu işletim sistemlerinde local olarak çalışabilen RabbitMQ için bir endişe kaynağı değildir. RabbitMQ, desteklenen tüm işletim sistemleri için eksiksiz çevrimiçi kurulum kılavuzları sağlar ve burada bulunabilirler: [http://www.rabbitmq.com/download.html](http://www.rabbitmq.com/download.html). Bu kitap, RabbitMQ'nun apt deposundan yüklendiği Debian/Ubuntu için talimatlar içerir.

**Ubuntu'da RabbitMQ kurulumu**

RabbitMQ'yu yüklemek için gereken nispeten az adım vardır. Bunlar aşağıdaki gibidir:

- Ubuntu'yu güncelleyin.
- Repository anahtarını indirin ve kurun.
- Anahtarın repository’de olduğundan emin olun.
- RabbitMQ'yu paket repository’sinden kurun.

İndirmeye başlamadan önce Ubuntu'nun güncel olduğundan emin olun. Eski bağımlılıklar güvenlik açıkları oluşturduğundan, işletim sisteminin tüm yazılımların en son sürümlerini kullandığından emin olun.

Yüklü yazılımın en son sürümlerini indirmek için apt update komutunu çalıştırın:

```
apt upgrade
```

RabbitMQ birkaç yazılım paketi gerektirir. Aşağıdaki komutu çalıştırarak curl, apt-transport-https ve GnuPG'nin sistemde olduğunu doğrulayın:

```
sudo apt install curl gnupg -y
sudo apt install apt-transport-https
```

-y seçeneği, bu bağımlılıklar için tüm lisansları kabul eder. Ubuntu gerekli tüm alt paketleri kurar.

Aşağıdaki komutlardan herhangi birini çalıştırarak işletim sisteminin adını bulun:

```
cat /etc/os-release
lsb_release -a
hostnamectl
```

Ubuntu varsayılan olarak RabbitMQ'yu içermez, bu nedenle devam etmeden önce repository anahtarına eklenmesi gerekir. Aşağıdaki komut dizisini terminalde yürütün:

```
curl -fsSL https://github.com/rabbitmq/signingkeys/
releases/download/2.0/rabbitmq-release-signing-key.asc
sudo apt-key add -
sudo tee /etc/apt/sources.list.d/bintray.rabbitmq.list <<EOF
deb https://dl.bintray.com/rabbitmq-erlang/debian [os release name] erlang
deb https://dl.bintray.com/rabbitmq/debian [os release name] main
EOF
```

Bu komutlar, broker ve Erlang için uygun işletim sistemi paketlerini eklemeden önce anahtarı indirir ve repo listesine ekler.

RabbitMQ, dağıtılmış ağlar oluşturmak için sağlam yerleşik desteğe sahip fonksiyonel bir dil olan Erlang'da yazılmıştır. Geliştiriciler, broker’ın desteklenen en son sürümleri için dilin minimum sürümlerinin (https://www.rabbitmq.com/what-erlang.html) bir listesini tutar. Bu kitap yazılırken RabbitMQ 3.8, Erlang 21.3 ila 23'ü destekler.

RabbitMQ artık doğru bir şekilde kurulabilir.

RabbitMQ'yu yüklemek için aşağıdaki komutları çalıştırın:

```
sudo apt install -y rabbitmq-server
sudo apt install librabbitmq-dev
```

librabbitmq-dev kitaplığı, broker ile etkileşim için bir client içerir. Ancak, server tek gereksinim olabilir.

**RabbitMQ'yu başlatmak**

RabbitMQ sunucusunu repodan yüklemek, sunucuyu ilk kez başlatmak için kullanılan bir dizi komut satırı aracını da yükler. Bu, aşağıdaki komutu yürüterek yapılır:

```
rabbitmq-server start
```

Sunucu ön planda başlar. Broker’ı servis olarak çalıştırmak için aşağıdaki komutları kullanın:

```
sudo systemctl enable rabbitmq-server
sudo systemctl start rabbitmq-server
sudo systemctl status rabbitmq-server
```

systemctl komutu, Ubuntu'daki servisleri yönetmek için de kullanılabilir. Son komutun çıktısı, broker’ın çalıştığını göstermelidir. Değilse RabbitMQ belgelerine (https://www.rabbitmq.com/troubleshooting.html) bakın.

Varsayılan olarak broker servisinin, Linux server başladığında otomatik olarak başlayacak şekilde yapılandırıldığını unutmayın.

### Yönetim eklentisini yüklemek (Web UI)

RabbitMQ, varsayılan olarak bir yönetim paneli kurmaz, ancak bu örnekte kullanılan isteğe bağlı web tabanlı eklenti, çalışan bir RabbitMQ örneğine göz atmayı kolaylaştırır.

Debian paketi birkaç script yükler. Bunlardan biri rabbitmq-plugins’dir. Amacı, eklentileri kurmamıza ve kaldırmamıza izin vermektir. Yönetim eklentisini yüklemek için aşağıdaki gibi kullanın:

```
$ sudo rabbitmq-plugins enable rabbitmq_management
Enabling plugins on node rabbit@host:
rabbitmq_management
The following plugins have been configured:
rabbitmq_consistent_hash_exchange
rabbitmq_event_exchange
rabbitmq_federation
rabbitmq_management
rabbitmq_management_agent
rabbitmq_shovel
rabbitmq_web_dispatch
Applying plugin configuration to rabbit@host...
The following plugins have been enabled:
rabbitmq_management
rabbitmq_management_agent
rabbitmq_web_dispatch
```

Evet, bu kadar kolay!

Aşağıdaki ekran görüntüsünde gösterildiği gibi http://<hostname>:15672 adresine giderek yönetim panelinin ana sayfasına erişmek için web tarayıcısı kullanın:

![Login Screen of the Management Console](/assets/images/2024/01/login-screen-of-the-management-console.png "Yönetim panelinin oturum açma ekranı"){: class="img-fluid"}

Bir sonraki bölüm için bizi izlemeye devam edin - kullanıcı oluşturma ve yapılandırma!

### Kullanıcıları yapılandırmak

Debian paketi tarafından yüklenen scriptlerden biri, RabbitMQ node’larını yönetmek için bir araç olan ve broker’ın tüm yönlerini yapılandırmak için kullanılan rabbitmqctl'dir. Broker’daki bir admin kullanıcısını aşağıdaki gibi yapılandırın:

```
$ sudo rabbitmqctl add_user cc-admin taxi123
Adding user "cc-admin" ...
$ sudo rabbitmqctl set_user_tags cc-admin administrator
Setting tags for user "cc-admin" to [administrator] ...
```

RabbitMQ, varsayılan olarak, guest kullanıcısıyla (parola: guest) birlikte gelir. Bu parolayı aşağıdaki gibi başka bir şeyle değiştirin:

```
$ sudo rabbitmqctl change_password guest guest123
```

Yönetim panelinin login ekranına geri gitmek, cc-admin kullanıcı adı ve taxi123 şifresiyle giriş yapmamızı sağlar.

Karşılama ekranı, aşağıdaki ekran görüntüsünde gösterildiği gibi, broker’ın dahili özelliklerine genel bir bakış sunar:

![Main Dashboard](/assets/images/2024/01/main-dashboard-of-the-management-console.png "Yönetim panelinin ana ekranı"){: class="img-fluid"}

Bu noktada, cc-admin kullanıcısının herhangi bir vhost’taki herhangi bir exchange veya kuyruğu inceleyemeyeceğini unutmayın. Uygulamaların RabbitMQ'ya bağlanabilmesi için şimdilik geliştirme amaçlı başka bir kullanıcı oluşturulması gerekiyor.

cc-dev kullanıcısını şu şekilde oluşturun:

```
$ sudo rabbitmqctl add_user cc-dev taxi123
Adding user "cc-dev" ...
```

Bu bölümde daha önce tartışıldığı gibi RabbitMQ, farklı kullanıcıların farklı erişim ayrıcalıklarına sahip olabildiği vhost kavramını destekler. CC geliştirme ortamı, vhost olarak bilinen bir vhost'a sahip olacaktır. Vhost’ta olan her şey, gelecekte yaratılacak herhangi bir ortamdan (QA ortamı gibi) ayrı olarak gerçekleşir. RabbitMQ'nun sonraki sürümlerinde (3.7+) birkaç kuyrukta ve eşzamanlı client bağlantılarında vhost başına sınırlar ayarlamak mümkündür.

Aşağıdaki gibi cc-dev-vhost adlı bir vhost oluşturun:

```
$ sudo rabbitmqctl add_vhost cc-dev-vhost
Adding vhost "cc-dev-vhost" ...
```

Bu, development için bir kullanıcı ve bir vhost oluşturur.

**Ayrılmış vhost’ları yapılandırmak**

RabbitMQ, guest kullanıcısının tam izinlere sahip olduğu / adlı varsayılan bir vhost ile birlikte gelir. Bu, hızlıca birşeyleri test etmek için uygun olsa da, sorumlulukları ayrı tutmak için özel bir vhost oluşturulması önerilir; böylece bir vhost'u tamamen bırakmak ve beklenmedik etkiler olmadan sıfırdan yeniden başlamak mümkündür.

Görüldüğü gibi, ne cc-admin ne de cc-dev kullanıcılarının cc-dev-vhost üzerinde herhangi bir şey yapma izni yoktur. Vhost'a aşağıdaki gibi tüm hakları vererek bunu düzeltebilirsiniz:

```
$ sudo rabbitmqctl set_permissions -p cc-dev-vhost cc-admin ".*" ".*" ".*"
Setting permissions for user "cc-admin" in vhost "cc-dev-vhost" ... $ $ $ sudo rabbitmqctl set_permissions -p cc-dev-vhost cc-dev ".*" ".*" ".*"
Setting permissions for user "cc-dev" in vhost "cc-dev-vhost" ...
```

Az önce yapılanları özetlemek gerekirse, komutun çoğu basit ama ".*" ".*" ".*" kısmı biraz gizemli görünüyor, o yüzden onu analiz edelim.

Bu, değerlendirilen kullanıcı ve vhost için belirlenen kaynaklar üzerinde yapılandırma, yazma ve okuma izinleri veren, söz konusu vhost için bir izinler üçlüsüdür. Exchange ve kuyruklardan oluşan kaynaklar, adlarıyla eşleşen normal ifadelerle belirtilir. Bu durumda, .* ifadesi aracılığıyla talep edilen herhangi bir kaynağa izin verilir.

Verilen komutlar, kaynak türüne ve verilen izinlere bağlıdır. RabbitMQ tarafından desteklenen erişim denetimi ilkelerinin tam listesi için bkz. http://www.rabbitmq.com/access-control.html.

Komut satırına alternatif olarak, yönetim panelinin kullanıcı yönetimi özelliklerini kullanabilirsiniz. Aşağıdaki ekran görüntüsünde gösterilene benzer bilgileri görüntülemek için konsolun Admin sekmesine ve ardından Users sekmesinde listelenen cc-dev kullanıcısına tıklayın. Komut satırından ayarlanan tüm kullanıcı yapılandırması görünür ve yönetim panelinde düzenlenebilir:

![RabbitMQ User Management](/assets/images/2024/01/rabbitmq-user-management.png "RabbitMQ yönetim panelinden kullanıcı yönetimi"){: class="img-fluid"}

Bir kullanıcının ayrıntıları, yönetim panelinde belirli bir kullanıcının adına tıklanarak bulunabilir:

![RabbitMQ User Details](/assets/images/2024/01/rabbitmq-user-details.png "Yönetim panelindeki bir kullanıcının ayrıntıları"){: class="img-fluid"}

RabbitMQ broker ve yönetim eklentisi (Web UI) kuruldu ve vhost ile kullanıcılar yapılandırıldı.

## Özet

Bu bölüm, AMQP ve RabbitMQ'nun nasıl çalıştığı da dahil olmak üzere mesajlaşma mimarisini ve tasarımını inceledi. Ayrıca, taksi acentesi Complete Car'ın neden RabbitMQ'yu yazılım ortamına dahil etmeye karar verdiği keşfedildi. Son olarak, bir RabbitMQ broker kuruldu ve bunun için kullanıcı ve çeşitli vhost’lar yapılandırıldı. Mesaj kuyrukları ve RabbitMQ hakkında temel bir bilgiyle donanmış olan bir sonraki bölüm, bu kavramlar üzerine inşa ediliyor ve Complete Car taksi uygulamasının arkasındaki mimariyi araştırıyor.

İşe koyulup biraz kod yazmanın zamanı geldi. RabbitMQ destekli bir uygulama oluşturmaya başlamak için bir sonraki bölüme geçin!

**Kaynak:** “RabbitMQ Essentials”, Lovisa Johansson, David Dossot