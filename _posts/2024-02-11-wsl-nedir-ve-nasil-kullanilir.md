---
layout: post
title: WSL Nedir ve Nasıl Kullanılır?
---

Windows Subsystem For Linux (WSL), Linux'u Windows'ta native olarak çalıştırmak için Microsoft tarafından sağlanan bir araçtır. Kusursuz bir deneyim olacak şekilde tasarlanmıştır ve esasen Windows dosya sisteminizle etkileşime girebilecek tam bir Linux shell sağlar.

## Sadece Bir Sanal Makine Değil

WSL'nin ne olmadığıyla başlayalım; örneğin, Ubuntu'yu VirtualBox'ta çalıştırarak elde edebileceğiniz tam bir Linux masaüstü deneyimi değildir. Performans nedenleriyle teknik olarak sanallaştırmayı kullanır, ancak tam bir VM çalıştırmaktan çok farklıdır. Her şey sizin için yönetilir ve işinizi yapmanız ve git, ssh gibi tanıdık bash komutlarını kullanmanız için basit bir shell ve environment sağlar.

Temel olarak WSL, app store’dan bir uygulama olarak tam bir Linux dağıtımı kurmanıza olanak tanır. Ubuntu'yu (veya tercih ettiğiniz dağıtımı) indirebilir, kurabilir ve başlatabileceğiniz bir uygulama veya terminalinizde CMD veya Powershell ile birlikte çalışacak bir profil olarak kullanıma sunabilirsiniz.

En basit açıklaması "Windows'ta bash çalıştırmanıza izin vermesi" olsa da, bir kabuktan daha fazlasıdır; apt kullanarak programları yükleyebilir ve normal bir Linux örneği gibi özelleştirebilirsiniz.

WSL, geliştiricilere ve bash kullanıcılarına, Windows'u birincil işletim sistemi olarak kullanmak zorunda kalmalarına rağmen Linux shell deneyimi sunmayı amaçlamaktadır. Daha kolay komut satırı erişimi için bir Linux shell’inin yanı sıra Visual Studio gibi Windows uygulamalarını çalıştırmanıza izin vererek her iki dünyanın da en iyisini sunar.

Microsoft, WSL'nin iki sürümünü sunmaktadır: sürüm 1 ve sürüm 2. WSL 2, sanal makine kullanır ve Windows ile oluşturulup gönderilen tam bir Linux çekirdeğine sahiptir. WSL 1 daha eskidir ve genellikle çok daha yavaştır ancak dosya sistemleri arasında çalışırken daha iyi performansa sahiptir. Daha hızlı olduğundan ve Docker gibi araçlarla çok daha iyi çalıştığından çoğu işlem için WSL 2'yi öneriyoruz.

## WSL Kurulumu

WSL isteğe bağlı bir Windows özelliğidir, dolayısıyla onu açmanız gerekir. Powershell'i başlat menüsünden Administrator olarak açın:

![Powershell'i yönetici olarak çalıştır](/assets/images/2024/02/powershell-run-as-administrator.png "Powershell'i yönetici olarak çalıştır"){: class="img-fluid"}

Ardından açın:

<pre>
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
</pre>

Değişiklikleri uygulamak için makinenizi yeniden başlatmanız gerekecektir. Bundan sonra Windows Store’u açabilir ve mevcut dağıtımları görüntülemek için “WSL”yi arayabilirsiniz:

![WSL Windows Store](/assets/images/2024/02/wsl-windows-store.png "WSL Windows Store"){: class="img-fluid"}

İlk kez çalıştırdığınızda, örnek için kullanıcı adınızı ve şifrenizi ayarlamanız istenecektir. Bunlar yalnızca Linux içindir ve Windows parolanızla eşleşmesi gerekmez.

İlk yüklediğiniz varsayılan olarak ayarlanmalıdır, ancak birden fazla yükleme yaparsanız varsayılanı manuel olarak ayarlamanız gerekecektir. WSL'yi Powershell'deki wsl komutu aracılığıyla yönetebilirsiniz. Örneğin, şu anda yüklü olan tüm sürümlerin listesi:

<pre>
PS C:\> wsl --list
Windows Subsystem for Linux Distributions:
Ubuntu-20.04 (Default)
docker-desktop
docker-desktop-data
</pre>

Ardından, WSL 1 ve 2 arasında geçiş yapmak için de kullanılabilen aşağıdaki komutla varsayılan WSL dağıtımınızı ayarlayabilirsiniz.

<pre>
wsl --set-version Ubuntu-20.04 2
</pre>

Gelecekteki tüm kurulumlar için tercih edilen bir WSL sürümünü de ayarlayabilirsiniz:

<pre>
wsl --set-default-version 2
</pre>

## WSL’i Kullanmak

WSL'yi birkaç farklı şekilde açabilirsiniz. En kolayı, Windows'a basıp "wsl" yazmaktır; bu, otomatik olarak wsl.exe'yi çalıştıracak ve varsayılan dağıtımınızı açacaktır.

![WSL Start Menu](/assets/images/2024/02/wsl-start-menu.png "WSL Start Menu"){: class="img-fluid"}

![WSL Windows Terminal](/assets/images/2024/02/wsl-windows-terminal.png "WSL Windows Terminal"){: class="img-fluid"}

Bu sadece temel bir terminal penceresidir; Microsoft'un yeni Terminal Uygulamasını kullanıyorsanız - ki bunu kesinlikle öneriyoruz - WSL sürümlerinize açılır menü aracılığıyla erişilebilir (ayarlamış olabileceğiniz SSH profillerinin yanı sıra):

![WSL Windows Terminal Menu](/assets/images/2024/02/wsl-windows-terminal-menu.png "WSL Windows Terminal Menu"){: class="img-fluid"}

## Dosya Sistemleri Üzerine Bir Not

Linux dosya sisteminiz her zamanki gibi / konumuna monte edilmiştir. Windows, /mnt/c/ konumunda veya varsayılan sürücü harfiniz hangisiyse orada bulunur.

Bunların hepsi sanal olduğundan, Linux dosyalarınız elbette sürücünün kendisinde depolanır. WSL 1 için bunlar basit bir klasörde saklanır. WSL 2 bazı şeyleri değiştirir ve dosyaları sanal sabit disk imajı veya VHDX olarak saklar.

Bu imajları aşağıdaki yolda bulabilirsiniz ve dağıtım kayıtlı değilken de monte edilebilseler de, Windows tarafında onları karıştırmanızı önermiyoruz.

<pre>
%USERPROFILE%AppDataLocalPackagesCanonicalGroupLimited.Ubuntu18.04onWindows_79rhkp1fndgscLocalState
</pre>