---
title: Konfiguracja usługi Active GRUB w usłudze Azure serial Console | Microsoft Docs
description: Skonfiguruj GRUB dla różnych dystrybucji umożliwiających dostęp do jednego użytkownika i trybu odzyskiwania na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/10/2019
ms.author: mimckitt
ms.openlocfilehash: aba47500400004c1d6a7044a266bad6f20d5d9c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91360552"
---
# <a name="proactively-ensuring-you-have-access-to-grub-and-sysrq-could-save-you-lots-of-down-time"></a>Proaktywne zagwarantowanie, że masz dostęp do GRUB i sysrq mogą zaoszczędzić znaczną część czasu

Posiadanie dostępu do konsoli szeregowej i GRUB zwiększy czas odzyskiwania maszyny wirtualnej z systemem IaaS Linux w większości przypadków. GRUB oferuje opcje odzyskiwania, które w przeciwnym razie zajmie więcej czasu na odzyskanie maszyny wirtualnej. 


Przyczyny przeprowadzenia odzyskiwania maszyny wirtualnej są różne i można je przypisać do scenariuszy takich jak:

   - Uszkodzone systemy plików/jądra/MBR (główny rekord rozruchowy)
   - Nieudane uaktualnienia jądra
   - Nieprawidłowe parametry jądra GRUB
   - Nieprawidłowe konfiguracje fstab
   - Konfiguracje zapory
   - Utracone hasło
   - Pliki konfiguracji zniekształcona SSHD
   - Konfiguracje sieci

 Wiele innych scenariuszy, jak [opisano tutaj](./serial-console-linux.md#common-scenarios-for-accessing-the-serial-console)

Sprawdź, czy masz dostęp do GRUB i Konsola szeregowa na maszynach wirtualnych wdrożonych na platformie Azure. 

Jeśli jesteś nowym w konsoli szeregowej, Skorzystaj z [tego linku](./serial-console-linux.md).

> [!TIP]
> Przed wprowadzeniem zmian upewnij się, że są wykonywane kopie zapasowe plików

Obejrzyj poniższe wideo, aby dowiedzieć się, jak szybko odzyskać maszynę wirtualną z systemem Linux po uzyskaniu dostępu do GRUB

[Odzyskaj wideo z maszyną wirtualną z systemem Linux](https://youtu.be/KevOc3d_SG4)

Istnieje kilka metod odzyskiwania maszyn wirtualnych z systemem Linux. W środowisku chmury ten proces był trudne.
Postęp jest ciągle wykonywany w celu uzyskania narzędzi i funkcji, aby zapewnić szybkie odzyskiwanie usług.

Za pomocą konsoli szeregowej platformy Azure można korzystać z maszyny wirtualnej z systemem Linux, tak jakby była ona konsolą systemu.

Można manipulować wieloma plikami konfiguracji, w tym w przypadku uruchamiania jądra. 

Im bardziej doświadczeni administratorzy systemu Linux/UNIX, będą wdzięczni z **jednego użytkownika** i  **trybu awaryjnego** , który jest dostępny za pośrednictwem konsoli szeregowej platformy Azure, dzięki czemu wymiana dysków i usunięcie maszyny wirtualnej są nieznacznie nadmiarowe.

Metoda odzyskiwania zależy od napotkanego problemu, na przykład utraconych lub nieprawidłowych haseł można resetować za pomocą opcji Azure Portal — >  **resetowania hasła**. Funkcja **resetowania hasła** jest znana jako rozszerzenie i komunikuje się z agentem gościa systemu Linux.

Dostępne są inne rozszerzenia, takie jak skrypt niestandardowy, ale te opcje wymagają, aby **waagent** systemu Linux był w dobrej kondycji, co nie zawsze jest w przypadku.

![stan agenta](./media/virtual-machines-serial-console/agent-status.png)


Upewnienie się, że masz dostęp do konsoli szeregowej platformy Azure i GRUB oznacza, że zmiana hasła lub niepoprawna konfiguracja może zostać poprawiona w ciągu minut, a nie godzin. Można nawet wymusić rozruch maszyny wirtualnej z alternatywnego jądra, jeśli masz wiele jądra na dysku w scenariuszu, w którym jądro podstawowe przestało być uszkodzone.

![wiele jądra](./media/virtual-machines-serial-console/more-kernel.png)

## <a name="suggested-order-of-recovery-methods"></a>Sugerowana kolejność metod odzyskiwania:

- Konsola szeregowa platformy Azure

- Wymiana dysków — można zautomatyzować przy użyciu dowolnego z tych funkcji:

   - [Skrypty odzyskiwania programu PowerShell](https://github.com/Azure/azure-support-scripts/tree/master/VMRecovery/ResourceManager)
   - [Skrypty odzyskiwania bash](https://github.com/sribs/azure-support-scripts)

- Starsza Metoda

## <a name="disk-swap-video"></a>Wideo z wymianą dysku:

Jeśli nie masz dostępu do GRUB Obejrzyj [ten](https://youtu.be/m5t0GZ5oGAc) film wideo i zobacz, jak można łatwo zautomatyzować procedurę wymiany dysku w celu odzyskania maszyny wirtualnej

## <a name="challenges"></a>Wyzwanie

Nie wszystkie maszyny wirtualne platformy Azure z systemem Linux są domyślnie skonfigurowane dla dostępu GRUB i nie są skonfigurowane do przerywania działania za pomocą poleceń sysrq. Niektóre starsze dystrybucje, takie jak SLES 11, nie są skonfigurowane do wyświetlania monitu logowania w konsoli szeregowej platformy Azure

W tym artykule omówiono różne dystrybucje systemu Linux i konfiguracje dokumentów na temat sposobu udostępniania GRUB.




## <a name="how-to-configure-linux-vm-to-accept-sysrq-keys"></a>Jak skonfigurować maszynę wirtualną z systemem Linux do akceptowania kluczy SysRq
Klucz sysrq jest domyślnie włączony w przypadku niektórych nowszych dystrybucje systemu Linux, ale na innych, można go skonfigurować do akceptowania wartości tylko dla niektórych funkcji SysRq.
W starszej wersji dystrybucje może być całkowicie wyłączony.

Funkcja SysRq jest przydatna do ponownego uruchomienia maszyny wirtualnej, która uległa awarii lub nie odpowiada, bezpośrednio z poziomu konsoli szeregowej platformy Azure, przydatnej także w uzyskaniu dostępu do menu GRUB. Alternatywnie ponowne uruchomienie maszyny wirtualnej z innego okna portalu lub sesji SSH może porzucić bieżące połączenie z konsolą, co spowodowało wygaśnięcie limitów czasu GRUB, do których są używane do wyświetlania menu GRUB.
Maszyna wirtualna musi być skonfigurowana do akceptowania wartości 1 dla parametru jądra, który włącza wszystkie funkcje sysrq lub 128, co umożliwia ponowne uruchomienie/wyłączenie


[Włącz wideo sysrq](https://youtu.be/0doqFRrHz_Mc)


Aby skonfigurować maszynę wirtualną do akceptowania ponownego uruchomienia za pomocą poleceń SysRq na Azure Portal, należy ustawić wartość 1 dla parametru jądra kernel.sysRQ

W przypadku tej konfiguracji w celu utrwalenia ponownego uruchomienia należy dodać wpis do pliku **sysctl. conf**

`echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Aby dynamicznie skonfigurować parametr jądra

`sysctl -w kernel.sysrq=1`

Jeśli nie masz dostępu do **katalogu głównego** lub sudo jest uszkodzony, nie będzie możliwe skonfigurowanie sysrq z poziomu wiersza powłoki.

Sysrq w tym scenariuszu można włączyć przy użyciu Azure Portal. Ta metoda może być korzystna, jeśli plik  **sudo. d/waagent** uległ uszkodzeniu lub został usunięty.

Za pomocą funkcji Azure Portal Operations-> Run > funkcja RunShellScript wymaga, aby proces waagent działał prawidłowo, a następnie wstrzyknąć to polecenie, aby włączyć sysrq

`sysctl -w kernel.sysrq=1 ; echo kernel.sysrq = 1 >> /etc/sysctl.conf`

Jak pokazano poniżej: ![ enable sysrq2](./media/virtual-machines-serial-console/enabling-sysrq-2.png)

Po zakończeniu możesz spróbować uzyskać dostęp do **sysrq** i sprawdzić, czy jest możliwy ponowny rozruch.

![Włącz sysrq3](./media/virtual-machines-serial-console/enabling-sysrq-3.png)

Wybierz pozycję **Uruchom ponownie** i **Wyślij sysrq** polecenie

![Włącz sysrq4](./media/virtual-machines-serial-console/enabling-sysrq-4.png)

System powinien rejestrować komunikat resetowania, taki jak

![Włącz sysrq5](./media/virtual-machines-serial-console/enabling-sysrq-5.png)


## <a name="ubuntu-grub-configuration"></a>Konfiguracja Ubuntu GRUB

Domyślnie powinno być możliwe uzyskanie dostępu do GRUB przez przytrzymanie klawisza **ESC** podczas rozruchu maszyny wirtualnej. Jeśli menu GRUB nie jest widoczne, możesz wymusić i pozostawić menu GRUB na ekranie w konsoli szeregowej platformy Azure, korzystając z jednej z tych opcji.

**Opcja 1** — wymusza wyświetlanie grub na ekranie 

Zaktualizuj plik/etc/default/grub.d/50-cloudimg-Settings.cfg, aby zachować menu GRUB na określonym LIMICIE czasu.
Nie jest wymagane naciśnięcie **klawisza ESC** , ponieważ grub będzie wyświetlany natychmiast

```
GRUB_TIMEOUT=0

change to

GRUB_TIMEOUT=5
```

**Opcja 2** — umożliwia naciśnięcie **klawisza ESC** przed rozruchem

Podobne zachowanie może być spowodowane wprowadzeniem zmian w pliku/etc/default/grub i obserwuj 3-sekundowy limit czasu na naciśnięcie **klawisza ESC**


Skomentuj te dwa wiersze:

```
#GRUB_HIDDEN_TIMEOUT=0
#GRUB_HIDDEN_TIMEOUT_QUIET=true
```
i Dodaj następujący wiersz:

```
GRUB_TIMEOUT_STYLE=countdown
```


## <a name="ubuntu-1204"></a>Ubuntu 12 \. 04

Ubuntu 12,04 zezwoli na dostęp do konsoli szeregowej, ale nie oferuje możliwości współdziałania. **Logowanie:** monit nie jest wyświetlany


Aby uzyskać monit dotyczący 12,04 **:**
1. Utwórz plik o nazwie/etc/init/ttyS0.conf zawierający następujący tekst:

    ```
    # ttyS0 - getty
    #
    # This service maintains a getty on ttyS0 from the point the system is
    # started until it is shut down again.
    start on stopped rc RUNLEVEL=[12345]
    stop on runlevel [!12345]
    
    respawn
    exec /sbin/getty -L 115200 ttyS0 vt102
    ```    

2. Poproszenie o uruchomienie Getty     
    ```
    sudo start ttyS0
    ```
 
Ustawienia wymagane do skonfigurowania konsoli szeregowej dla wersji Ubuntu można znaleźć [tutaj](https://help.ubuntu.com/community/SerialConsoleHowto)

## <a name="ubuntu-recovery-mode"></a>Tryb odzyskiwania Ubuntu

Dodatkowe opcje odzyskiwania i czyszczenia są dostępne dla Ubuntu za pośrednictwem GRUB, ale te ustawienia są dostępne tylko wtedy, gdy odpowiednio skonfigurujesz parametry jądra.
Niepowodzenie konfigurowania tego parametru rozruchowego jądra spowoduje, że menu odzyskiwanie zostanie wysłane do Diagnostyka Azure a nie do konsoli szeregowej platformy Azure.
Dostęp do menu odzyskiwania Ubuntu można uzyskać, wykonując następujące czynności:

Przerywanie procesu rozruchu i menu GRUB dostępu

Wybierz opcje zaawansowane dla Ubuntu i naciśnij klawisz ENTER

![Zrzut ekranu przedstawia Konsola szeregowa wybrane opcje zaawansowane dla Ubuntu.](./media/virtual-machines-serial-console/ubunturec1.png)

Wybierz wyświetlanie linii *(tryb odzyskiwania)* nie naciskaj klawisza ENTER, ale naciśnij klawisz "e"

![Zrzut ekranu przedstawia Konsola szeregowa z wybraną wersją trybu odzyskiwania.](./media/virtual-machines-serial-console/ubunturec2.png)

Znajdź wiersz, który załaduje jądro i Zastąp ostatni parametr **nomodeset** z miejscem docelowym jako **Console = ttyS0**

```
linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery nomodeset

change to

linux /boot/vmlinuz-4.15.0-1023-azure root=UUID=21b294f1-25bd-4265-9c4e-d6e4aeb57e97 ro recovery console=ttyS0
```

![Zrzut ekranu przedstawia Konsola szeregowa o zmienionej wartości.](./media/virtual-machines-serial-console/ubunturec3.png)

Naciśnij **kombinację klawiszy Ctrl-x** , aby uruchomić i załadować jądro.
Jeśli wszystko przebiegnie prawidłowo, zobaczysz te dodatkowe opcje, które mogą ułatwić przeprowadzenie innych opcji odzyskiwania

![Zrzut ekranu przedstawia Konsola szeregowa w menu odzyskiwanie, które oferuje dodatkowe opcje odzyskiwania.](./media/virtual-machines-serial-console/ubunturec4.png)


## <a name="red-hat-grub-configuration"></a>Konfiguracja GRUB Red Hat

## <a name="red-hat-74-grub-configuration"></a>\.Konfiguracja grub firmy Red Hat 7 \+
Domyślna konfiguracja/etc/default/Grub w tych wersjach jest odpowiednio skonfigurowana

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_SERIAL_COMMAND="serial"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Włącz klucz SysRq

```
sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq
```

## <a name="red-hat-72-and-73-grub-configuration"></a>GRUB konfiguracja Red Hat 7 \. 2 i 7 \. 3
Plik do zmodyfikowania to/etc/default/grub — konfiguracja domyślna wygląda podobnie do tego przykładu:

```
GRUB_TIMEOUT=1
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```

Zmień następujące wiersze w/etc/default/grub

```
GRUB_TIMEOUT=1 

to

GRUB_TIMEOUT=5
```


```
GRUB_TERMINAL_OUTPUT="console"

to

GRUB_TERMINAL="serial console"
```

Dodaj również ten wiersz:

```
GRUB_SERIAL_COMMAND=”serial –speed=115200 –unit=0 –word=8 –parity=no –stop=1″
```

/etc/default/grub powinien teraz wyglądać podobnie do tego przykładu:

```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=saved
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
GRUB_DISABLE_RECOVERY="true"
```
 
Kończenie i aktualizowanie konfiguracji grub za pomocą

`grub2-mkconfig -o /boot/grub2/grub.cfg`

Ustaw parametr jądra SysRq:

`sysctl -w kernel.sysrq = 1;echo kernel.sysrq = 1 >> /etc/sysctl.conf;sysctl -a | grep -i sysrq`

Możesz Alternatywnie skonfigurować GRUB i SysRq przy użyciu jednego wiersza w powłoce lub za pomocą polecenia Uruchom. Wykonaj kopię zapasową plików przed uruchomieniem tego polecenia:


`cp /etc/default/grub /etc/default/grub.bak; sed -i 's/GRUB_TIMEOUT=1/GRUB_TIMEOUT=5/g' /etc/default/grub; sed -i 's/GRUB_TERMINAL_OUTPUT="console"/GRUB_TERMINAL="serial console"/g' /etc/default/grub; echo "GRUB_SERIAL_COMMAND=\"serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1\"" >> /etc/default/grub;grub2-mkconfig -o /boot/grub2/grub.cfg;sysctl -w kernel.sysrq=1;echo kernel.sysrq = 1 /etc/sysctl.conf;sysctl -a | grep -i sysrq`


## <a name="red-hat-6x-grub-configuration"></a>Konfiguracja GRUB w Red Hat 6 \. x
Plik do zmodyfikowania to/boot/grub/grub.conf. Wartość określi, `timeout` jak długo GRUB jest pokazywany.

```
#boot=/dev/vda
default=0
timeout=15
splashimage=(hd0,0)/grub/splash.xpm.gz
#hiddenmenu
serial --unit=0 --speed=9600
terminal serial
terminal --timeout=5 serial console
```


Ostatni wiersz  *terminala — timeout = 5 Konsola szeregowa* zwiększy limit czasu **grub** przez dodanie monitu o 5 sekund w **celu kontynuowania.**

![Zrzut ekranu przedstawia konsolę z danymi wyjściowymi.](./media/virtual-machines-serial-console/rh6-1.png)

Menu GRUB powinno pojawić się na ekranie dla skonfigurowanego limitu czasu = 15 bez konieczności naciskania klawisza ESC. Upewnij się, że kliknij w konsoli programu w przeglądarce, aby uaktywnić menu i wybrać wymagane jądro

![Zrzut ekranu przedstawia konsolę z dwiema opcjami systemu Linux.](./media/virtual-machines-serial-console/rh6-2.png)

## <a name="suse"></a>Szło

## <a name="sles-12-sp1"></a>SLES 12 z dodatkiem SP1
Użyj programu inicjującego YaST zgodnie z oficjalnymi [dokumentami](./serial-console-grub-single-user-mode.md#grub-access-in-suse-sles)

Lub Dodaj/Zmień, aby/etc/default/grub następujące parametry:

```
GRUB_TERMINAL=serial
GRUB_TIMEOUT=5
GRUB_SERIAL_COMMAND="serial --unit=0 --speed=9600 --parity=no"

```
Sprawdź, czy ttyS0 jest używany w GRUB_CMDLINE_LINUX lub GRUB_CMDLINE_LINUX_DEFAULT

```
GRUB_CMDLINE_LINUX_DEFAULT="console=ttyS0,9600n"
```

Utwórz ponownie grub. cfg

`grub2-mkconfig -o /boot/grub2/grub.cfg`


## <a name="sles-11-sp4"></a>SLES 11 Z DODATKIEM SP4 
Zostanie wyświetlona konsola szeregowa, która wyświetla komunikaty rozruchowe, ale nie wyświetla monitu o podanie **nazwy logowania:**

Otwórz sesję SSH na maszynie wirtualnej i zaktualizuj plik **/etc/inittab** , usuwając komentarz z tego wiersza:

```
#S0:12345:respawn:/sbin/agetty -L 9600 ttyS0 vt102
```

Następnie uruchom polecenie 

`telinit q`

Aby włączyć GRUB, należy wprowadzić następujące zmiany w/boot/grub/menu.lst 

```
timeout 5
serial --unit=0 --speed=9600 --parity=no
terminal --timeout=5 serial console

root (hd0,0)
kernel /boot/vmlinuz-3.0.101-108.74-default root=/dev/disk/by-uuid/ab6b62bb--
1a8c-45eb-96b1-1fbc535b9265 disk=/dev/sda  USE_BY_UUID_DEVICE_NAMES=1 earlyprinttk=ttyS0 console=ttyS0 rootdelay=300  showopts vga=0x314
```

 Ta konfiguracja spowoduje, że komunikat **naciśnij dowolny klawisz, aby nadal** pojawiał się w konsoli programu przez 5 sekund 

Następnie zostanie wyświetlone menu GRUB przez dodatkowe 5 sekund — naciskając strzałkę w dół, spowoduje to przerwanie działania licznika i wybranie jądra, które ma zostać rozruchowe, dołączenie słowa kluczowego **pojedynczego** dla trybu jednego użytkownika, który wymaga ustawienia hasła głównego.

Dołączenie polecenia **init =/bin/bash** spowoduje załadowanie jądra, ale zapewnia, że program init zostanie zastąpiony przez powłokę bash.

Będziesz uzyskiwać dostęp do powłoki bez konieczności wprowadzania hasła. Następnie można wykonać aktualizację hasła dla kont systemu Linux lub wprowadzić inne zmiany w konfiguracji.


## <a name="force-the-kernel-to-a-bash-prompt"></a>Wymuś monitowanie jądra z bash
Posiadanie dostępu do usługi GRUB umożliwia przerwanie procesu inicjowania tej interakcji jest przydatne w przypadku wielu procedur odzyskiwania.
Jeśli nie masz hasła głównego, a pojedynczy użytkownik wymaga posiadania hasła głównego, możesz uruchomić jądro zastępujące program init z monitem bash — to przerwanie można osiągnąć przez dołączenie init =/bin/bash do wiersza rozruchu jądra

![Zrzut ekranu przedstawia konsolę z zaktualizowanym wierszem rozruchowym.](./media/virtual-machines-serial-console/bash1.png)

Zainstaluj ponownie system plików/(root) RW przy użyciu polecenia

`mount -o remount,rw /`

![Zrzut ekranu przedstawia konsolę z akcją ponownego zainstalowania.](./media/virtual-machines-serial-console/bash2.png)


Teraz można przeprowadzić zmianę hasła głównego lub wiele innych zmian konfiguracji systemu Linux

![Zrzut ekranu przedstawia konsolę, w której można zmienić hasło główne i inną konfigurację.](./media/virtual-machines-serial-console/bash3.png)

Uruchom ponownie maszynę wirtualną za pomocą 

`/sbin/reboot -f`




## <a name="single-user-mode"></a>Tryb pojedynczego użytkownika

Alternatywnie może być konieczne uzyskanie dostępu do maszyny wirtualnej w trybie jednego użytkownika lub trybu awaryjnego. Wybierz jądro, które chcesz uruchomić lub przerwać przy użyciu klawiszy strzałek.
Wprowadź żądany tryb, dołączając słowo kluczowe **Single** lub **1** do wiersza rozruchowego jądra. W systemach RHEL można również dołączyć **Rd. Break**.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do trybu pojedynczego użytkownika, zobacz [ten dokument](./serial-console-grub-single-user-mode.md#general-single-user-mode-access) . 


![single_user_ubuntu](./media/virtual-machines-serial-console/single-user-ubuntu.png)


## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [usłudze Azure serial Console]( ./serial-console-linux.md)