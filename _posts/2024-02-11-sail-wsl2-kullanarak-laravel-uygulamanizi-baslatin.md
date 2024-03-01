---
layout: post
title: Sail (WSL2) Kullanarak Laravel Uygulamanızı Başlatın
---

Laravel geliştirme ortamında Homestead VirtualBox'a aşina olabilirsiniz. Ancak, bu geleneksel sanal makine, gerçek donanım kaynaklarınıza erişmeden önce ekstra bir ek yük katmanıyla birlikte gelir.

Son birkaç yılda Konteynerizasyon (veya Docker) konusunu İnternet'in her yerinde duyabilirsiniz. VM'nin tamamını sanallaştırmak yerine kodu ve tüm bağımlılıklarını bir "Konteyner" olarak paketler. Konteynerler hakkında daha fazla bilgiyi buradan edinebilirsiniz… Hala Laradock kullanıyor olabilirsiniz… Son olarak, Aralık 2020'de Taylor Otwell, Laravel Sail'i piyasaya sürdü!

*Laravel Sail, Laravel'in varsayılan Docker geliştirme ortamıyla etkileşime geçmek için kullanılan hafif bir komut satırı arayüzüdür. Sail, önceden Docker deneyimi gerektirmeden PHP, MySQL ve Redis kullanarak bir Laravel uygulaması oluşturmak için harika bir başlangıç noktası sağlar.*

*Kaynak: Laravel Belgeleri*

Bu eğitimde WSL 2 kullanarak Windows üzerinde yepyeni bir Laravel projesi başlatacaksınız. WSL, Windows Subsystem for Linux anlamına gelir ve geliştiricilerin herhangi bir "gerçek" VM host olmadan Linux ortamlarını doğrudan Windows üzerinde çalıştırmasına olanak tanır. WSL 2 büyük bir revizyondur ve daha iyi dosya IO performansıyla (20 kat daha hızlı) WSL 1'in avantajlarını sağlar. Önyüklemesi yavaş, kaynak yükü büyük ve tamamen yalıtılmış olan geleneksel VM'e benzemez.

Windows makinenizde WSL 2'yi açalım!

## WSL 2'yi Yükleyin

### Basitleştirilmiş Kurulum (Windows 11/Windows 10 sürüm 2004 veya üzeri)

Yönetici ayrıcalıklarına sahip bir komut penceresi açın ve wsl --install komutunu çalıştırın, yeniden başlattıktan sonra WSL'yi kullanmaya hazır olacaksınız.

Komut Windows 10’da mevcut değilse aşağıdaki manuel kurulumu deneyin. Alternatif olarak daha fazla ayrıntı için KB5004296'yı arayabilirsiniz.

### Manuel Kurulum (Windows 10 Build 18362 veya üzeri, Home Edition dahil)

**1. WSL'yi etkinleştirin**

Yönetici ayrıcalıklarına sahip bir komut penceresi açın ve ardından aşağıdaki komutu çalıştırın.

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

DISM hakkında daha fazla bilgi için [buraya](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism?view=windows-11) tıklayın.

**2. Sanal Makine özelliğini etkinleştirin**

WSL 2 için sanallaştırma yetenekleri hala gereklidir. Yönetici ayrıcalıklarına sahip bir komut penceresi açın ve ardından aşağıdaki çalıştırın.

```
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

**3. Linux çekirdeği güncelleme paketini indirin**

[wsl_update_x64.msi](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi) paketini indirin ve yükleyin. Daha sonra bilgisayarınızı yeniden başlatın.

**4. WSL 2'yi varsayılan sürümünüz olarak ayarlayın**

```
wsl --set-default-version 2
```

**5. Ubuntu'yu (veya seçtiğiniz Linux dağıtımını) yükleyin**

- Microsoft Mağazasını açın ve ardından Ubuntu'yu arayın ve seçin.
- Dağıtım sayfasından “Al”ı ve ardından “Yükle”yi seçin.
- Hazır olduğunda “Başlat”a tıklayın.
- Ubuntu için kullanıcı adınızı ve şifrenizi oluşturun.

**6. Kurulumunuzu doğrulamak için wsl --list --verbose komutunu kullanın.**

![wsl --list --verbose](/assets/images/2024/02/wsl-list-verbose.png "wsl --list --verbose"){: class="img-fluid"}

Herhangi bir sorunla karşılaşırsanız [buradaki](https://docs.microsoft.com/en-my/windows/wsl/install-win10#troubleshooting-installation) sorun giderme ipuçlarına başvurabilirsiniz.

## Docker'ı Yükleyin

**1. İndirin**

Docker [Desktop Stable](https://docs.docker.com/desktop/install/windows-install/) sürümünü indirin.

**2. Kurulum**

Docker Masaüstünü kurmak için kurulum sihirbazını takip edin. WSL 2'niz düzgün yapılandırılmışsa kurulum sırasında WSL 2'yi etkinleştirmeniz istenecektir.

![Docker WSL2 engine kullanıyor](/assets/images/2024/02/docker-use-wsl2-based-engine.png "Docker WSL2 engine kullanıyor"){: class="img-fluid"}

**3. WSL Entegrasyonunu Yapılandırın**

Doğru Linux dağıtımlarını açtığınızdan emin olun.

![Docker WSL entegrasyon ayarları](/assets/images/2024/02/docker-wsl-integration-settings.png "Docker WSL entegrasyon ayarları"){: class="img-fluid"}

## Haydi Yelken Açalım

Windows Terminalini başlatın ve wsl'yi girin.

![Terminal wsl komutu](/assets/images/2024/02/terminal-wsl-command.png "Terminal wsl komutu"){: class="img-fluid"}

İşte püf noktası… Yolun, Windows kullanıcı dizininiz olduğunu fark edebilirsiniz.

Resmi Microsoft WSL belgelerinde [WSL özellikleri](https://learn.microsoft.com/en-us/windows/wsl/compare-versions#comparing-features) karşılaştırıldı. WSL 2 mimarisi, işletim sistemi dosya sistemlerindeki performans dışında, WSL 1'den birkaç açıdan daha iyi performans gösterir.

![WSL özelliklerinin karşılaştırılması](/assets/images/2024/02/wsl-comparing-features.png "WSL özelliklerinin karşılaştırılması"){: class="img-fluid"}

cd ~ komutunu kullanarak Linux ana dizininize gidebiliriz.

Aşağıdaki komutu kullanarak test-project adında yeni bir Laravel projesi oluşturalım:

```
curl -s https://laravel.build/test-project | bash
```

Daha sonra kurulumu bekleyin.

![WSL Laravel kurulumu](/assets/images/2024/02/wsl-laravel-installation.png "WSL Laravel kurulumu"){: class="img-fluid"}

WSL storage’in gerçek konumu (vhdx formatında) şu adreste bulunur:

```
C:\Users\user_name\AppData\Local\Packages\
CanonicalGroupLimited.UbuntuonWindows_79rhkp1fndgsc\LocalState
```

Artık cd test-project ile dizine girebilir ve Sail'i başlatabiliriz!

```
./vendor/bin/sail up
```

Sail’i ilk kez çalıştırdığınızda, docker imajlarını indirmek ve konteynerinizi oluşturmak biraz zaman alır ancak bir sonraki çalıştırma için çok hızlı olacaktır.

Herhangi bir sorunla karşılaşırsanız suçlu Antivirüs yazılımınız olabilir. Lütfen “C:\ProgramData\DockerDesktop\vm-data\” yolunu hariç tutun.

![Docker container çalışıyor](/assets/images/2024/02/container-up-and-running.png "Docker container çalışıyor"){: class="img-fluid"}

Container başlatıldıktan sonra yepyeni Laravel projenize erişmek için http://localhost adresine gidebilirsiniz.

### Sail Stack’inizi Seçin

Varsayılan stack mysql, redis, meilisearch, mailhog ve selenium'u içeriyor. Mevcut servisler arasında mysql, pgsql, mariadb, redis, memcached, meilisearch, selenium ve mailhog bulunmaktadır.

Query kullanarak favori stack’inizi seçebilirsiniz:

```
curl -s "https://laravel.build/example-app?with=mysql,redis" | bash
```

docker-compose.yml dosyanız buna göre yapılandıracaktır.

### Günlük Sail Rutini

**Sail’i detached modda başlatın**

```
./vendor/bin/sail up -d
```

**Sail’i durdurun**

“Detached” modu kullanmıyorsanız Control+C, aksi halde,

```
./vendor/bin/sail down
```

**Takma ad**

Uzun bir yol adı yazmak yerine, sail’e doğrudan ulaşmanıza olanak tanıyan bir Bash takma adı yapılandırın.

```
alias sail='bash vendor/bin/sail'
```

Bir takma ad oluşturabilir ve takma adı kalıcı olarak .bashrc dosyanıza kaydedebilirsiniz. nano ~/.bashrc komutunu kullanıp daha sonra alt tarafa alias sail='bash vendor/bin/sail' ekleyip çalıştırabilirsiniz.

## Kodlamaya Başlayın

**Visual Studio Code**

[Remote-WSL extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) yükleyin ve proje klasöründe olduğunuzdan emin olun.

```
cd ~/test-project/
```

Sonra aşağıdaki kodu çalıştırın.

```
code .
```

VS Code'un başlatıldığını ve proje klasörünüzdeki dosyaları görebilmeniz gerekir.

![VS Code Explorer](/assets/images/2024/02/vs-code-explorer.png "VS Code Explorer"){: class="img-fluid"}

Artık http://localhost adresinde kodlamaya başlayabilir ve değişikliklerinizi izleyebilirsiniz.

**PhpStorm**

Projeyi \\wsl$\Ubuntu\home\<user>\test-project kullanarak açabilirsiniz.

**İpucu:** sail artisan <command> komutunu kullanarak artisan komutunu çağırabilirsiniz.

Okuduğunuz için teşekkürler. İyi kodlamalar!

**Kaynak:** https://blog.devgenius.io/kickstart-your-laravel-web-app-using-laravel-sail-30276265e588