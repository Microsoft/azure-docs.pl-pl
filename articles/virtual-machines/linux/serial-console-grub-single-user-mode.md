---
title: Konsola szeregowa platformy Azure dla trybu GRUB i tryb jednego użytkownika
description: Korzystanie z konsoli szeregi dla grub na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 2aa7110ab4e52fdc5c3804bd27be5f41081fb435
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758511"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Uzyskiwanie dostępu do programu GRUB i trybu pojedynczego użytkownika za pomocą konsoli szeregowej
GRUB jest GRand Unified Bootloader. Z GRUB jesteś w stanie zmodyfikować konfigurację rozruchu, aby uruchomić w trybie pojedynczego użytkownika, między innymi.

Tryb pojedynczego użytkownika to minimalne środowisko z minimalną funkcjonalnością. Może to być przydatne do badania problemów z rozruchem, problemami z systemem plików lub problemami z siecią. W tle może działać mniej usług, a w zależności od poziomu uruchomienia system plików może nawet nie być montowany automatycznie.

Tryb pojedynczego użytkownika jest również przydatne w sytuacjach, gdy maszyna wirtualna może być skonfigurowana tylko do akceptowania kluczy SSH do logowania. W takim przypadku można użyć trybu pojedynczego użytkownika, aby utworzyć konto z uwierzytelnianiem hasłem.

Aby przejść do trybu pojedynczego użytkownika, należy wprowadzić GRUB podczas uruchamiania maszyny Wirtualnej i zmodyfikować konfigurację rozruchu w GRUB. Można to zrobić za pomocą konsoli szeregowej maszyny Wirtualnej.

## <a name="general-grub-access"></a>Ogólny dostęp do GRUB
Aby uzyskać dostęp do GRUB, należy ponownie uruchomić maszynę wirtualną przy zachowaniu bloku konsoli szeregowej otwarte. Niektóre dystrybucje będą wymagać wprowadzania danych za pomocą klawiatury, aby pokazać GRUB, podczas gdy inne automatycznie pokażą GRUB przez kilka sekund i pozwolą na wprowadzenie klawiatury użytkownika, aby anulować limit czasu. 

Należy upewnić się, że GRUB jest włączona na maszynie Wirtualnej, aby móc uzyskać dostęp do trybu pojedynczego użytkownika. W zależności od dystrybucji może być kilka prac konfiguracyjnych, aby upewnić się, że GRUB jest włączona. Informacje dotyczące dystrybucji są dostępne poniżej.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Uruchom ponownie maszynę wirtualną, aby uzyskać dostęp do GRUB w konsoli szeregi
Ponowne uruchomienie maszyny Wirtualnej z otwartą łażką konsoli `'b'` szeregowej można wykonać za pomocą polecenia SysRq, jeśli jest włączona funkcja [SysRq,](./serial-console-nmi-sysrq.md) lub klikając przycisk Uruchom ponownie w bloku Przegląd (otwórz maszynę wirtualną w nowej karcie przeglądarki, aby ponownie uruchomić komputer bez zamykania bloku konsoli szeregowej). Postępuj zgodnie z poniższymi instrukcjami dotyczącymi dystrybucji, aby dowiedzieć się, czego można oczekiwać od GRUB po ponownym uruchomieniu komputera.

## <a name="general-single-user-mode-access"></a>Ogólny dostęp do trybu pojedynczego użytkownika
Ręczny dostęp do trybu pojedynczego użytkownika może być potrzebny w sytuacjach, gdy konto nie zostało skonfigurowane z uwierzytelnianiem hasłem. Należy zmodyfikować konfigurację GRUB, aby ręcznie przejść do trybu pojedynczego użytkownika. Po wykonaniu tej tej opcji zobacz Resetowanie lub dodawanie hasła w celu uzyskania dalszych instrukcji— użyj trybu pojedynczego użytkownika.

W przypadkach, gdy maszyna wirtualna nie może uruchomić, dystrybucje często automatycznie upuszczają Cię w trybie pojedynczego użytkownika lub w trybie awaryjnym. Inne wymagają jednak dodatkowej konfiguracji, zanim będą mogły automatycznie wpaść do trybu jednego użytkownika lub awaryjnego (na przykład skonfigurowania hasła głównego).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Resetowanie lub dodawanie hasła za pomocą trybu pojedynczego użytkownika
Gdy jesteś w trybie pojedynczego użytkownika, wykonaj następujące czynności, aby dodać nowego użytkownika z uprawnieniami sudo:
1. Uruchom, `useradd <username>` aby dodać użytkownika
1. Uruchom, `sudo usermod -a -G sudo <username>` aby przyznać nowe uprawnienia główne użytkownika
1. Służy `passwd <username>` do ustawiania hasła dla nowego użytkownika. Następnie będziesz mógł zalogować się jako nowy użytkownik

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Dostęp dla red hat enterprise linux (RHEL)
RHEL automatycznie przejdzie do trybu pojedynczego użytkownika, jeśli nie będzie mógł normalnie uruchomić. Jeśli jednak nie skonfigurowałeś dostępu administratora dla trybu pojedynczego użytkownika, nie będziesz mieć hasła głównego i nie będziesz mógł się zalogować. Istnieje obejście (patrz "Ręczne wprowadzanie trybu pojedynczego użytkownika" poniżej), ale sugestia jest, aby skonfigurować dostęp roota początkowo.

### <a name="grub-access-in-rhel"></a>Dostęp do GRUB w RHEL
RHEL jest wyposażony w grub włączone po wyjęciu z pudełka. Aby wprowadzić GRUB, uruchom `sudo reboot` ponownie maszynę wirtualną i naciśnij dowolny klawisz. Pojawi się ekran GRUB.

> Uwaga: Red Hat zawiera również dokumentację do uruchamiania w trybie ratunkowym, trybie awaryjnym, trybie debugowania i resetowaniu hasła głównego. [Kliknij tutaj, aby uzyskać do niego dostęp](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurowanie dostępu głównego dla trybu pojedynczego użytkownika w RHEL
Tryb pojedynczego użytkownika w RHEL wymaga włączenia użytkownika głównego, który jest domyślnie wyłączony. Jeśli musisz włączyć tryb pojedynczego użytkownika, należy wykonać następujące instrukcje:

1. Zaloguj się do systemu Red Hat za pośrednictwem SSH
1. Przełączanie do katalogu głównego
1. Włącz hasło dla użytkownika root 
    * `passwd root`(ustaw silne hasło główne)
1. Upewnij się, że użytkownik root może zalogować się tylko za pośrednictwem ttyS0
    * `edit /etc/ssh/sshd_config`i upewnij się, że PermitRootLogIn jest ustawiona na brak
    * `edit /etc/securetty file`aby zezwolić tylko na logowanie za pośrednictwem ttyS0 

Teraz, jeśli system uruchamia się w trybie pojedynczego użytkownika, możesz zalogować się za pomocą hasła głównego.

Alternatywnie dla RHEL 7.4+ lub 6.9+ można włączyć tryb pojedynczego użytkownika w monitach GRUB, patrz instrukcje [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ręczne wejście w tryb pojedynczego użytkownika w RHEL
Jeśli masz skonfigurowany GRUB i dostęp do roota z powyższymi instrukcjami, możesz wejść w tryb pojedynczego użytkownika z następującymi instrukcjami:

1. Naciśnij przycisk "Esc" podczas ponownego uruchamiania maszyny Wirtualnej, aby wprowadzić GRUB
1. W GRUB naciśnij "e", aby edytować wybrany system operacyjny, do którego chcesz uruchomić (zwykle pierwszy wiersz)
1. Znajdź linię jądra - na platformie Azure rozpocznie się od`linux16`
1. Naciśnij klawisze Ctrl+E, aby przejść do końca wiersza
1. Dodaj na końcu wiersza następujące elementy:`systemd.unit=rescue.target`
    * Spowoduje to uruchomienie w trybie pojedynczego użytkownika. Jeśli chcesz korzystać z `systemd.unit=emergency.target` trybu awaryjnego, dodaj na końcu wiersza zamiast`systemd.unit=rescue.target`
1. Naciśnij klawisze Ctrl + X, aby zakończyć i ponownie uruchomić komputer z zastosowanymi ustawieniami
1. Zostaniesz poproszony o podanie hasła administratora, zanim będzie można przejść do trybu pojedynczego użytkownika - jest to to samo hasło, które utworzono w powyższych instrukcjach    

    ![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Wejdź w tryb pojedynczego użytkownika bez włączonego konta głównego w RHEL
Jeśli nie przeszedłeś powyższych kroków, aby włączyć użytkownika root, nadal możesz zresetować hasło główne. Skorzystaj z następujących instrukcji:

> Uwaga: Jeśli używasz SELinux, upewnij się, że podjąłeś dodatkowe kroki opisane w dokumentacji Red Hat [tutaj](https://aka.ms/rhel7grubterminal) podczas resetowania hasła głównego.

1. Naciśnij przycisk "Esc" podczas ponownego uruchamiania maszyny Wirtualnej, aby wprowadzić GRUB
1. W GRUB naciśnij "e", aby edytować wybrany system operacyjny, do którego chcesz uruchomić (zwykle pierwszy wiersz)
1. Znajdź linię jądra - na platformie Azure rozpocznie się od`linux16`
1. Dodaj `rd.break` na końcu wiersza, upewniając się, `rd.break` że jest miejsce przed (patrz przykład poniżej)
    - Spowoduje to przerwanie procesu rozruchu, zanim kontrola zostanie przekazana z `initramfs` do `systemd`, jak opisano w dokumentacji Red Hat [tutaj](https://aka.ms/rhel7rootpassword).
1. Naciśnij klawisze Ctrl + X, aby zakończyć i ponownie uruchomić komputer z zastosowanymi ustawieniami
1. Po uruchomieniu zostaniesz przesuń w tryb awaryjny z systemem plików tylko do odczytu. Wprowadź `mount -o remount,rw /sysroot` do powłoki, aby ponownie zamontować główny system plików z uprawnieniami do odczytu/zapisu
1. Po uruchomieniu w trybie pojedynczego użytkownika wpisz, `chroot /sysroot` aby przełączyć się do `sysroot` więzienia
1. Jesteś teraz root. Możesz zresetować hasło `passwd` główne, a następnie skorzystać z powyższych instrukcji, aby przejść do trybu pojedynczego użytkownika. Wpisz, `reboot -f` aby ponownie uruchomić po zakończeniu.

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Uwaga: Przejście przez powyższe instrukcje spowoduje upuszczenie cię do powłoki awaryjnej, dzięki czemu można również wykonywać zadania, takie jak edycja `fstab`. Jednak ogólnie akceptowana sugestia polega na zresetowaniu hasła głównego i użyciu go do wejścia w tryb pojedynczego użytkownika. 


## <a name="access-for-centos"></a>Dostęp dla CentOS
Podobnie jak Red Hat Enterprise Linux, tryb pojedynczego użytkownika w CentOS wymaga grub i użytkownika root, aby być włączone. 

### <a name="grub-access-in-centos"></a>Dostęp do GRUB w CentOS
CentOS jest wyposażony w GRUB włączone po wyjęciu z pudełka. Aby wprowadzić GRUB, uruchom `sudo reboot` ponownie maszynę wirtualną i naciśnij dowolny klawisz. Pojawi się ekran GRUB.

### <a name="single-user-mode-in-centos"></a>Tryb pojedynczego użytkownika w CentOS
Postępuj zgodnie z instrukcjami dla RHEL powyżej, aby włączyć tryb pojedynczego użytkownika w CentOS.

## <a name="access-for-ubuntu"></a>Dostęp dla Ubuntu 
Obrazy Ubuntu nie wymagają hasła głównego. Jeśli system uruchamia się w trybie pojedynczego użytkownika, można go używać bez dodatkowych poświadczeń. 

### <a name="grub-access-in-ubuntu"></a>Dostęp DO GRUB w Ubuntu
Aby uzyskać dostęp do GRUB, naciśnij i przytrzymaj "Esc", gdy maszyna wirtualna jest uruchamiana. 

Domyślnie obrazy Ubuntu nie będą automatycznie wyświetlane na ekranie GRUB. Można to zmienić za pomocą następujących instrukcji:
1. Otwieranie `/etc/default/grub.d/50-cloudimg-settings.cfg` w wybranym edytorze tekstu
1. Zmienianie `GRUB_TIMEOUT` wartości na wartość niezerową
1. Otwieranie `/etc/default/grub` w wybranym edytorze tekstu
1. Skomentuj `GRUB_HIDDEN_TIMEOUT=1` wiersz
1. Uruchom polecenie `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Tryb pojedynczego użytkownika w Ubuntu
Ubuntu automatycznie przejdzie do trybu pojedynczego użytkownika, jeśli nie będzie mógł normalnie uruchomić. Aby ręcznie przejść do trybu pojedynczego użytkownika, należy użyć następujących instrukcji:

1. Z GRUB, naciśnij "e", aby edytować wpis startowy (wpis Ubuntu)
1. Poszukaj linii, `linux`która zaczyna się od , a następnie poszukaj`ro`
1. Dodaj `single` `ro`po , upewniając się, że jest miejsce przed i po`single`
1. Naciśnij klawisze Ctrl + X, aby ponownie uruchomić komputer z tymi ustawieniami i przejść do trybu pojedynczego użytkownika

## <a name="access-for-coreos"></a>Dostęp dla CoreOS
Tryb pojedynczego użytkownika w CoreOS wymaga włączenia GRUB. 

### <a name="grub-access-in-coreos"></a>Dostęp do GRUB w CoreOS
Aby uzyskać dostęp do GRUB, naciśnij dowolny klawisz podczas uruchamiania maszyny Wirtualnej.

### <a name="single-user-mode-in-coreos"></a>Tryb pojedynczego użytkownika w CoreOS
CoreOS automatycznie przejdzie do trybu pojedynczego użytkownika, jeśli nie będzie mógł normalnie uruchomić. Aby ręcznie przejść do trybu pojedynczego użytkownika, należy użyć następujących instrukcji:
1. Z GRUB naciśnij "e", aby edytować wpis startowy
1. Poszukaj linii, `linux$`która zaczyna się od . Powinny być 2, hermetyzowane w różnych klauzulach if/else
1. Dołącz `coreos.autologin=ttyS0` do końca obu `linux$` wierszy
1. Naciśnij klawisze Ctrl + X, aby ponownie uruchomić komputer z tymi ustawieniami i przejść do trybu pojedynczego użytkownika

## <a name="access-for-suse-sles"></a>Dostęp dla SUSE SLES
Nowsze obrazy SLES 12 SP3+ umożliwiają dostęp za pośrednictwem konsoli szeregowej w przypadku, gdy system uruchamia się w tryb awaryjny. 

### <a name="grub-access-in-suse-sles"></a>Dostęp do GRUB w SUSE SLES
Dostęp GRUB w SLES wymaga konfiguracji bootloadera przez YaST. Aby to zrobić, postępuj zgodnie z poniższymi instrukcjami:

1. ssh do maszyny Wirtualnej `sudo yast bootloader`SLES i uruchomić . Użyj `tab` klawiszy, `enter` klawiszy i klawiszy strzałek, aby poruszać się po menu. 
1. Przejdź `Kernel Parameters`do , `Use serial console`i sprawdź . 
1. Dodawanie `serial --unit=0 --speed=9600 --parity=no` do argumentów konsoli

1. Naciśnij klawisz F10, aby zapisać ustawienia i wyjść
1. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną i naciśnij dowolny klawisz podczas sekwencji rozruchowej, aby GRUB pozostał na ekranie
    - Domyślny limit czasu dla GRUB to 1s. Można to zmodyfikować, `GRUB_TIMEOUT` zmieniając zmienną w`/etc/default/grub`

![](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Tryb pojedynczego użytkownika w SUSE SLES
Zostaniesz automatycznie upuszczony do powłoki awaryjnej, jeśli SLES nie może normalnie uruchomić. Aby ręcznie wprowadzić powłokę awaryjną, należy użyć następujących instrukcji:

1. Z GRUB, naciśnij "e", aby edytować wpis startowy (wpis SLES)
1. Poszukaj linii jądra, z która rozpocznie się od`linux`
1. Dołącz `systemd.unit=emergency.target` do końca wiersza
1. Naciśnij klawisze Ctrl + X, aby ponownie uruchomić komputer z tymi ustawieniami i wprowadzić powłokę awaryjną
   > Należy pamiętać, że zostaniesz upuszczony do powłoki awaryjnej z systemem plików tylko do _odczytu._ Jeśli chcesz wprowadzić zmiany w dowolnych plikach, musisz ponownie zamontować system plików z uprawnieniami do odczytu i zapisu. Aby to zrobić, wprowadź `mount -o remount,rw /` do powłoki

## <a name="access-for-oracle-linux"></a>Dostęp dla Oracle Linux
Podobnie jak Red Hat Enterprise Linux, tryb pojedynczego użytkownika w systemie Oracle Linux wymaga włączenia GRUB i głównego użytkownika. 

### <a name="grub-access-in-oracle-linux"></a>Dostęp do GRUB w systemie Oracle Linux
Oracle Linux jest wyposażony w GRUB włączone po wyjęciu z pudełka. Aby wprowadzić GRUB, uruchom `sudo reboot` ponownie maszynę wirtualną i naciśnij przycisk "Esc". Pojawi się ekran GRUB.

### <a name="single-user-mode-in-oracle-linux"></a>Tryb pojedynczego użytkownika w systemie Oracle Linux
Postępuj zgodnie z instrukcjami dotyczącymi RHEL powyżej, aby włączyć tryb pojedynczego użytkownika w systemie Oracle Linux.

## <a name="next-steps"></a>Następne kroki
* Główna konsola szeregowa Linux strona dokumentacji znajduje się [tutaj](serial-console.md).
* Używanie konsoli szeregowej dla [połączeń NMI i SysRq](serial-console-nmi-sysrq.md)
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Windows](../windows/serial-console.md)
* Dowiedz się więcej o [diagnostyce rozruchu](boot-diagnostics.md)
