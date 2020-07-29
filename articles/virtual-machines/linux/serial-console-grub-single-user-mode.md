---
title: Konsola szeregowa platformy Azure dla GRUB i tryb jednego użytkownika
description: Korzystanie z konsoli szeregowej dla Grub w usłudze Azure Virtual Machines.
services: virtual-machines-linux
author: asinn826
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: da7e558464c82db9d71132c4b1f53f9892d2d653
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87292240"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Uzyskiwanie dostępu do programu GRUB i trybu pojedynczego użytkownika za pomocą konsoli szeregowej
GRUB to wielkie ujednolicone program inicjujący. Z usługi GRUB można zmodyfikować konfigurację rozruchową, aby przeprowadzić rozruch w trybie jednego użytkownika między innymi.

Tryb jednego użytkownika jest minimalnym środowiskiem o minimalnej funkcjonalności. Może być przydatny do badania problemów z rozruchem, rozwiązywania problemów z systemem plików lub problemów z siecią. Mniej usług może działać w tle i, w zależności od runlevel, system plików może nie być nawet automatycznie instalowany.

Tryb pojedynczego użytkownika jest również przydatny w sytuacjach, w których maszyna wirtualna może być skonfigurowana tylko do akceptowania kluczy SSH do zalogowania. W takim przypadku może być możliwe użycie trybu jednego użytkownika w celu utworzenia konta z uwierzytelnianiem przy użyciu hasła.

Aby wprowadzić tryb pojedynczego użytkownika, należy wprowadzić GRUB podczas uruchamiania maszyny wirtualnej i zmodyfikować konfigurację rozruchu w GRUB. Można to zrobić za pomocą konsoli szeregowej maszyny wirtualnej.

## <a name="general-grub-access"></a>Ogólny dostęp do GRUB
Aby uzyskać dostęp do programu GRUB, należy ponownie uruchomić maszynę wirtualną, pozostawiając otwarty blok konsoli szeregowej. Niektóre dystrybucje będą wymagały wejścia z klawiatury do wyświetlania GRUB, podczas gdy inni będą automatycznie wyświetlać GRUB przez kilka sekund i zezwalać na wprowadzanie danych przez klawiaturę użytkownika w celu anulowania limitu czasu. 

Upewnij się, że GRUB jest włączona na maszynie wirtualnej, aby można było uzyskać dostęp do trybu jednego użytkownika. W zależności od dystrybucji może istnieć kilka czynności konfiguracyjnych, aby upewnić się, że GRUB jest włączona. Informacje specyficzne dla dystrybucji są dostępne poniżej.

### <a name="reboot-your-vm-to-access-grub-in-serial-console"></a>Ponowne uruchamianie maszyny wirtualnej w celu uzyskania dostępu do GRUB w konsoli szeregowej
Ponowny rozruch maszyny wirtualnej z otwartym blokiem konsoli szeregowej można wykonać przy użyciu `'b'` polecenia SysRq, jeśli [sysrq](./serial-console-nmi-sysrq.md) jest włączona, lub klikając przycisk Uruchom ponownie w bloku przegląd (Otwórz maszynę wirtualną na nowej karcie przeglądarki, aby ponownie uruchomić komputer bez zamykania bloku konsoli szeregowej). Postępuj zgodnie z poniższymi instrukcjami dystrybucji, aby dowiedzieć się, czego można oczekiwać od GRUB po ponownym uruchomieniu.

## <a name="general-single-user-mode-access"></a>Ogólny dostęp do trybu pojedynczego użytkownika
W sytuacjach, w których nie skonfigurowano konta z uwierzytelnianiem przy użyciu hasła, może być wymagany ręczny dostęp do trybu jednego użytkownika. Należy zmodyfikować konfigurację GRUB, aby ręcznie wprowadzić tryb pojedynczego użytkownika. Po wykonaniu tej czynności Zobacz Użycie trybu jednego użytkownika, aby zresetować lub dodać hasło w celu uzyskania dalszych instrukcji.

W przypadkach, gdy maszyna wirtualna nie jest w stanie rozruchowym, dystrybucje często automatycznie powróci do trybu jednego użytkownika lub trybu awaryjnego. Inne, jednak wymagają dodatkowych ustawień, zanim będą mogli automatycznie porzucić użytkownika w trybie awaryjnym lub w tryb awaryjny (na przykład konfigurując hasło główne).

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Aby zresetować lub dodać hasło, użyj trybu jednego użytkownika
Gdy jesteś w trybie jednego użytkownika, wykonaj następujące czynności, aby dodać nowego użytkownika z uprawnieniami sudo:
1. Uruchom `useradd <username>` , aby dodać użytkownika
1. Uruchom, `sudo usermod -a -G sudo <username>` Aby udzielić nowemu użytkownikowi uprawnień
1. Służy `passwd <username>` do ustawiania hasła dla nowego użytkownika. Następnie będzie można zalogować się jako nowy użytkownik

## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Dostęp do Red Hat Enterprise Linux (RHEL)
RHEL powróci do trybu jednego użytkownika automatycznie, jeśli nie będzie można normalnie przeprowadzić rozruch. Niemniej jednak, jeśli nie skonfigurowano dostępu głównego do trybu jednego użytkownika, nie będziesz mieć hasła głównego i nie będzie można się zalogować. Istnieje obejście problemu (zobacz sekcję "ręczne wprowadzanie trybu pojedynczego użytkownika"), ale sugestia polega na wstępnym skonfigurowaniu dostępu do katalogu głównego.

### <a name="grub-access-in-rhel"></a>GRUB dostęp w RHEL
RHEL jest GRUB z włączoną obsługą pola. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną za pomocą `sudo reboot` i naciśnij dowolny klawisz. Zostanie wyświetlony ekran GRUB.

> Uwaga: w Red Hat dostępna jest również dokumentacja dotycząca rozruchu w trybie ratowniczym, tryb awaryjny, tryb debugowania i resetowanie hasła głównego. [Kliknij tutaj, aby uzyskać do niego dostęp](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurowanie dostępu głównego do trybu jednego użytkownika w RHEL
Tryb pojedynczego użytkownika w RHEL wymaga włączenia użytkownika root, który jest domyślnie wyłączony. Jeśli musisz włączyć tryb pojedynczego użytkownika, Skorzystaj z następujących instrukcji:

1. Logowanie do systemu Red Hat za pośrednictwem protokołu SSH
1. Przełącz do katalogu głównego
1. Włącz hasło dla użytkownika root 
    * `passwd root`(Ustaw silne hasło główne)
1. Upewnij się, że użytkownik główny może zalogować się tylko za pośrednictwem ttyS0
    * `edit /etc/ssh/sshd_config`i upewnij się, że PermitRootLogIn jest ustawiony na wartość nie
    * `edit /etc/securetty file`Zezwalaj tylko na logowanie za pośrednictwem ttyS0 

Teraz, gdy system jest uruchamiany w trybie jednego użytkownika, możesz zalogować się za pomocą hasła głównego.

Alternatywnie w przypadku RHEL 7.4 + lub 6,9 + można włączyć tryb jednego użytkownika w wierszach GRUB, zobacz instrukcje [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single)

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ręczne wprowadzanie trybu pojedynczego użytkownika w RHEL
Jeśli skonfigurowano GRUB i dostęp do katalogu głównego z powyższymi instrukcjami, można wprowadzić tryb jednego użytkownika z następującymi instrukcjami:

1. Naciśnij klawisz "Esc" podczas ponownego uruchamiania maszyny wirtualnej, aby wprowadzić GRUB
1. W GRUB naciśnij klawisz "e", aby edytować wybrany system operacyjny, do którego chcesz przeprowadzić rozruch (zazwyczaj pierwszy wiersz)
1. Znajdź linię jądra na platformie Azure, która zaczyna się od`linux16`
1. Naciśnij klawisze Ctrl + E, aby przejść do końca wiersza
1. Dodaj następujący znak na końcu wiersza:`systemd.unit=rescue.target`
    * Spowoduje to przerozruch użytkownika w trybie jednego użytkownika. Jeśli chcesz użyć trybu awaryjnego, Dodaj `systemd.unit=emergency.target` do końca wiersza zamiast`systemd.unit=rescue.target`
1. Naciśnij klawisze Ctrl + X, aby wyjść i ponownie uruchomić przy użyciu zastosowanych ustawień
1. Przed wprowadzeniem trybu pojedynczego użytkownika zostanie wyświetlony monit o podanie hasła administratora. jest to samo hasło, które zostało utworzone w powyższych instrukcjach.    

    ![Animowany obraz przedstawiający interfejs wiersza polecenia. Użytkownik wybiera serwer, lokalizuje koniec wiersza jądra, a następnie wprowadza określony tekst.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Wprowadzanie trybu pojedynczego użytkownika bez włączonego konta głównego w RHEL
Jeśli nie wykonano powyższych kroków w celu włączenia użytkownika root, można nadal zresetować hasło główne. Wykonaj następujące instrukcje:

> Uwaga: Jeśli korzystasz z SELinux, upewnij się, że zostały wykonane dodatkowe kroki opisane w dokumentacji Red Hat [tutaj](https://aka.ms/rhel7grubterminal) podczas resetowania hasła głównego.

1. Naciśnij klawisz "Esc" podczas ponownego uruchamiania maszyny wirtualnej, aby wprowadzić GRUB
1. W GRUB naciśnij klawisz "e", aby edytować wybrany system operacyjny, do którego chcesz przeprowadzić rozruch (zazwyczaj pierwszy wiersz)
1. Znajdź linię jądra na platformie Azure, która zaczyna się od`linux16`
1. Dodaj `rd.break` do końca wiersza, aby upewnić się, że jest dostępna spacja `rd.break` (Zobacz przykład poniżej)
    - Spowoduje to przerwanie procesu rozruchu przed przekazaniem kontroli z `initramfs` do `systemd` , zgodnie z opisem w dokumentacji Red Hat [tutaj](https://aka.ms/rhel7rootpassword).
1. Naciśnij klawisze Ctrl + X, aby wyjść i ponownie uruchomić przy użyciu zastosowanych ustawień
1. Po rozruchu nastąpi przerwanie trybu awaryjnego z systemem plików tylko do odczytu. Wprowadź `mount -o remount,rw /sysroot` w powłoce, aby ponownie zainstalować główny system plików z uprawnieniami do odczytu i zapisu
1. Po rozruchu w trybie jednego użytkownika wpisz polecenie, `chroot /sysroot` Aby przełączyć się do `sysroot` złamanymi
1. Jesteś teraz katalogiem głównym. Hasło główne można zresetować za pomocą programu `passwd` , a następnie użyć powyższych instrukcji, aby wprowadzić tryb pojedynczego użytkownika. Wpisz `reboot -f` , aby przeprowadzić ponowny rozruch po zakończeniu.

![Animowany obraz przedstawiający interfejs wiersza polecenia. Użytkownik wybiera serwer, lokalizuje koniec wiersza jądra i wprowadza określone polecenia.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> Uwaga: uruchomienie w powyższej instrukcji spowoduje przechodzenie do powłoki awaryjnej, dzięki czemu można także wykonywać zadania, takie jak edytowanie `fstab` . Jednak ogólnie przyjęta sugestia to zresetowanie hasła głównego i użycie go do wprowadzania trybu pojedynczego użytkownika. 


## <a name="access-for-centos"></a>Dostęp do CentOS
Podobnie jak Red Hat Enterprise Linux, tryb jednego użytkownika w CentOS wymaga, aby GRUB i użytkownik główny był włączony. 

### <a name="grub-access-in-centos"></a>GRUB dostęp w CentOS
CentOS jest GRUB z włączoną obsługą pola. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną za pomocą `sudo reboot` i naciśnij dowolny klawisz. Zostanie wyświetlony ekran GRUB.

### <a name="single-user-mode-in-centos"></a>Tryb pojedynczego użytkownika w CentOS
Postępuj zgodnie z instrukcjami RHEL powyżej, aby włączyć tryb pojedynczego użytkownika w CentOS.

## <a name="access-for-ubuntu"></a>Dostęp do Ubuntu 
Obrazy Ubuntu nie wymagają hasła głównego. Jeśli system zostanie uruchomiony w trybie jednego użytkownika, można go użyć bez dodatkowych poświadczeń. 

### <a name="grub-access-in-ubuntu"></a>GRUB dostęp w Ubuntu
Aby uzyskać dostęp do GRUB, naciśnij i przytrzymaj klawisz "Esc" podczas uruchamiania maszyny wirtualnej. 

Domyślnie obrazy Ubuntu nie będą automatycznie wyświetlane na ekranie GRUB. Można to zmienić, wykonując następujące instrukcje:
1. Otwórz `/etc/default/grub.d/50-cloudimg-settings.cfg` w wybranym edytorze tekstu
1. Zmień `GRUB_TIMEOUT` wartość na wartość różną od zera.
1. Otwórz `/etc/default/grub` w wybranym edytorze tekstu
1. Skomentuj `GRUB_HIDDEN_TIMEOUT=1` wiersz
1. Należy uruchomić polecenie `sudo update-grub`

### <a name="single-user-mode-in-ubuntu"></a>Tryb pojedynczego użytkownika w Ubuntu
Ubuntu powróci do trybu jednego użytkownika automatycznie, jeśli nie będzie można normalnie przeprowadzić rozruch. Aby ręcznie wprowadzić tryb pojedynczego użytkownika, wykonaj następujące instrukcje:

1. W programie GRUB naciśnij pozycję "e", aby edytować swój wpis rozruchu (Ubuntu Entry)
1. Wyszukaj wiersz rozpoczynający się od `linux` , a następnie wyszukaj`ro`
1. Dodaj `single` po `ro` , aby upewnić się, że istnieje spacja przed i po`single`
1. Naciśnij kombinację klawiszy Ctrl + X, aby przeprowadzić ponowny rozruch przy użyciu tych ustawień i wprowadzić tryb pojedynczego użytkownika.

## <a name="access-for-coreos"></a>Dostęp do CoreOS
Tryb pojedynczego użytkownika w CoreOS wymaga włączenia GRUB. 

### <a name="grub-access-in-coreos"></a>GRUB dostęp w CoreOS
Aby uzyskać dostęp do GRUB, naciśnij dowolny klawisz podczas uruchamiania maszyny wirtualnej.

### <a name="single-user-mode-in-coreos"></a>Tryb pojedynczego użytkownika w CoreOS
CoreOS powróci do trybu jednego użytkownika automatycznie, jeśli nie będzie można normalnie przeprowadzić rozruch. Aby ręcznie wprowadzić tryb pojedynczego użytkownika, wykonaj następujące instrukcje:
1. W programie GRUB naciśnij pozycję "e", aby edytować swój wpis rozruchu
1. Wyszukaj wiersz rozpoczynający się od `linux$` . Powinna istnieć 2, hermetyzowane w różnych klauzulach if/else
1. Dołącz `coreos.autologin=ttyS0` do końca obu `linux$` wierszy
1. Naciśnij kombinację klawiszy Ctrl + X, aby przeprowadzić ponowny rozruch przy użyciu tych ustawień i wprowadzić tryb pojedynczego użytkownika.

## <a name="access-for-suse-sles"></a>Dostęp do SLES SUSE
Nowsze obrazy z SLES 12 SP3 + zezwalają na dostęp za pośrednictwem konsoli szeregowej na wypadek uruchomienia systemu w trybie awaryjnym. 

### <a name="grub-access-in-suse-sles"></a>GRUB dostęp w programie SUSE SLES
Dostęp GRUB w SLES wymaga konfiguracji programu inicjującego za pośrednictwem YaST. Aby to zrobić, wykonaj następujące instrukcje:

1. Użyj protokołu SSH na maszynie wirtualnej SLES i uruchom system `sudo yast bootloader` . Użyj `tab` klawiszy klucz, `enter` klucz i strzałka, aby nawigować po menu. 
1. Przejdź do `Kernel Parameters` i sprawdź `Use serial console` . 
1. Dodaj `serial --unit=0 --speed=9600 --parity=no` do argumentów konsoli

1. Naciśnij klawisz F10, aby zapisać ustawienia i zakończyć
1. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną i naciśnij dowolny klawisz podczas sekwencji rozruchu, aby GRUB się na ekranie
    - Domyślny limit czasu dla GRUB to 1. Można to zmienić, zmieniając `GRUB_TIMEOUT` zmienną w`/etc/default/grub`

![Animowany obraz przedstawiający interfejs wiersza polecenia. Użytkownik wprowadza określony tekst, wybiera określoną opcję i zapisuje ustawienia.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Tryb pojedynczego użytkownika w SUSE SLES
Jeśli SLES nie będzie można normalnie uruchomić, zostanie automatycznie porzucony do powłoki awaryjnej. Aby ręcznie wprowadzić powłokę awaryjną, należy wykonać następujące instrukcje:

1. W programie GRUB naciśnij pozycję "e", aby edytować swój wpis rozruchu (SLES Entry)
1. Wyszukaj wiersz jądra, od którego zostanie uruchomiony program`linux`
1. Dołącz `systemd.unit=emergency.target` na końcu wiersza
1. Naciśnij kombinację klawiszy Ctrl + X, aby ponownie uruchomić te ustawienia, a następnie wprowadź powłokę awaryjną
   > Należy pamiętać, że użytkownik zostanie porzucony do powłoki awaryjnej z systemem plików _tylko do odczytu_ . Aby dokonać edycji plików, należy ponownie zainstalować system plików z uprawnieniami do odczytu i zapisu. Aby to zrobić, wprowadź `mount -o remount,rw /` w powłoce

## <a name="access-for-oracle-linux"></a>Dostęp do Oracle Linux
Podobnie jak Red Hat Enterprise Linux, tryb jednego użytkownika w Oracle Linux wymaga włączenia GRUB oraz użytkownika root. 

### <a name="grub-access-in-oracle-linux"></a>Dostęp GRUB w Oracle Linux
Oracle Linux jest dostępna z włączonym GRUB. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną za pomocą `sudo reboot` i naciśnij klawisz "Esc". Zostanie wyświetlony ekran GRUB.

### <a name="single-user-mode-in-oracle-linux"></a>Tryb pojedynczego użytkownika w Oracle Linux
Postępuj zgodnie z instrukcjami RHEL powyżej, aby włączyć tryb pojedynczego użytkownika w Oracle Linux.

## <a name="next-steps"></a>Następne kroki
* Główna strona dokumentacji systemu Linux w konsoli szeregowej znajduje się [tutaj](../troubleshooting/serial-console-linux.md).
* Korzystanie z konsoli szeregowej dla [wywołań NMI i sysrq](serial-console-nmi-sysrq.md)
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Windows](../troubleshooting/serial-console-windows.md)
* Dowiedz się więcej na temat [diagnostyki rozruchu](../troubleshooting/boot-diagnostics.md)
