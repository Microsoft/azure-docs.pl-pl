---
title: Usługa Azure serial Console dla GRUB i tryb jednego użytkownika | Microsoft Docs
description: W tym artykule opisano sposób korzystania z konsoli szeregowej dla GRUB na maszynach wirtualnych platformy Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: alsin
ms.openlocfilehash: 5341cc62a7d02c3072df90becf893dec18427ac2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87439549"
---
# <a name="use-serial-console-to-access-grub-and-single-user-mode"></a>Używanie konsoli szeregowej do uzyskiwania dostępu do GRUB i trybu jednego użytkownika
Całkowite, ujednolicone program inicjujący (GRUB) jest prawdopodobnie pierwszym elementem w przypadku uruchamiania maszyny wirtualnej (VM). Ponieważ jest ona wyświetlana przed uruchomieniem systemu operacyjnego, GRUB nie jest dostępna za pośrednictwem protokołu SSH. W programie GRUB można zmodyfikować konfigurację rozruchu w taki sposób, aby uruchamiała się w trybie jednego użytkownika między innymi.

Tryb jednego użytkownika jest minimalnym środowiskiem o minimalnej funkcjonalności. Może być przydatne do badania problemów z rozruchem, problemów z systemem plików lub problemów z siecią. Mniej usług można uruchomić w tle i, w zależności od runlevel, system plików może nie być nawet automatycznie instalowany.

Tryb pojedynczego użytkownika jest również przydatny w sytuacjach, w których można skonfigurować maszynę wirtualną do akceptowania tylko kluczy SSH w celu zalogowania się. W takim przypadku może być możliwe użycie trybu pojedynczego użytkownika w celu utworzenia konta z uwierzytelnianiem przy użyciu hasła.

> [!NOTE]
> Usługa konsola szeregowa zezwala tylko użytkownikom z poziomem *współautor* lub wyższymi uprawnieniami dostępu do konsoli SZEREGOWEJ maszyny wirtualnej.

Aby wprowadzić tryb pojedynczego użytkownika, należy wprowadzić GRUB podczas uruchamiania maszyny wirtualnej i zmodyfikować konfigurację rozruchu w GRUB. Zobacz szczegółowe instrukcje dotyczące wprowadzania GRUB w następnej sekcji. Ogólnie rzecz biorąc, jeśli maszyna wirtualna została skonfigurowana do wyświetlania GRUB, możesz użyć przycisku Uruchom ponownie w konsoli szeregowej maszyny wirtualnej, aby ponownie uruchomić maszynę wirtualną i wyświetlić GRUB.

![Przycisk ponownego uruchamiania konsoli szeregowej systemu Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-bar.png)

## <a name="general-grub-access"></a>Ogólny dostęp do GRUB
Aby uzyskać dostęp do programu GRUB, uruchom ponownie maszynę wirtualną, gdy zostanie otwarte okienko konsoli szeregowej. Niektóre dystrybucje wymagają wejścia klawiatury do wyświetlania GRUB, a inne automatycznie pokazują GRUB przez kilka sekund, aby umożliwić wprowadzanie danych przez klawiaturę użytkownika w celu anulowania limitu czasu.

Aby mieć dostęp do trybu pojedynczego użytkownika, upewnij się, że GRUB jest włączona na maszynie wirtualnej. W zależności od dystrybucji niektóre czynności konfiguracyjne mogą być konieczne, aby upewnić się, że GRUB jest włączona. Informacje dotyczące dystrybucji znajdują się w następnej sekcji.

### <a name="restart-your-vm-to-access-grub-in-serial-console"></a>Uruchom ponownie maszynę wirtualną, aby uzyskać dostęp do GRUB w konsoli szeregowej
Maszynę wirtualną można uruchomić ponownie w konsoli szeregowej, ustawiając kursor nad przyciskiem **ponownego uruchamiania** , a następnie wybierając polecenie **Uruchom ponownie maszynę wirtualną**. W dolnej części okienka zostanie wyświetlone powiadomienie o ponownym uruchomieniu.

Maszynę wirtualną można również uruchomić ponownie, uruchamiając polecenie SysRq "b", jeśli [sysrq](./serial-console-nmi-sysrq.md) jest włączone. Aby dowiedzieć się, czego można oczekiwać od GRUB po ponownym uruchomieniu, zobacz instrukcje dotyczące dystrybucji w następnych sekcjach.

![Ponowne uruchamianie konsoli szeregowej systemu Linux](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-ubuntu.gif)

## <a name="general-single-user-mode-access"></a>Ogólny dostęp do trybu pojedynczego użytkownika
Użytkownik może potrzebować ręcznego dostępu do trybu pojedynczego użytkownika, jeśli nie skonfigurowano konta z uwierzytelnianiem przy użyciu hasła. Zmodyfikuj konfigurację GRUB, aby ręcznie wprowadzić tryb pojedynczego użytkownika. Po wykonaniu tej czynności Zobacz sekcję "Używanie trybu pojedynczego użytkownika do resetowania lub dodawania hasła", aby uzyskać dalsze instrukcje.

Jeśli maszyna wirtualna nie jest w stanie rozruchowym, dystrybucje często automatycznie porzucają się w trybie jednego użytkownika lub w trybie awaryjnym. Inne dystrybucje mogą jednak wymagać dodatkowej konfiguracji, na przykład w celu skonfigurowania hasła głównego, zanim będą mogły automatycznie porzucić w trybie jednego użytkownika lub awaryjnego.

### <a name="use-single-user-mode-to-reset-or-add-a-password"></a>Aby zresetować lub dodać hasło, użyj trybu pojedynczego użytkownika
Po wybraniu trybu jednego użytkownika Dodaj nowego użytkownika z uprawnieniami sudo, wykonując następujące czynności:
1. Uruchom `useradd <username>` , aby dodać użytkownika.
1. Uruchom, `sudo usermod -a -G sudo <username>` Aby przyznać nowemu użytkownikowi uprawnienia dostępu.
1. Służy `passwd <username>` do ustawiania hasła dla nowego użytkownika. Następnie możesz zalogować się jako nowy użytkownik.


## <a name="access-for-red-hat-enterprise-linux-rhel"></a>Dostęp do Red Hat Enterprise Linux (RHEL)
Jeśli RHEL nie może uruchomić się normalnie, powoduje to automatyczne przekazanie do trybu pojedynczego użytkownika. Niemniej jednak, jeśli nie skonfigurowano dostępu głównego do trybu jednego użytkownika, nie masz hasła głównego i nie można się zalogować. Istnieje obejście problemu (zobacz sekcję "ręczne wprowadzanie trybu pojedynczego użytkownika w RHEL"), ale sugerujemy, aby początkowo skonfigurować dostęp do katalogu głównego.

### <a name="grub-access-in-rhel"></a>GRUB dostęp w RHEL
RHEL jest GRUB z włączoną obsługą pola. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną przez uruchomienie `sudo reboot` , a następnie naciśnij dowolny klawisz. Powinno zostać wyświetlone okienko GRUB. Jeśli nie, upewnij się, że w pliku GRUB są obecne następujące wiersze ( `/etc/default/grub` ):

**RHEL 8**

>[!NOTE]
> Red Hat zaleca użycie Grubby do konfigurowania parametrów wiersza polecenia jądra w RHEL 8 +. Obecnie nie można zaktualizować limitu czasu grub i parametrów terminalu przy użyciu Grubby. Aby zmodyfikować GRUB_CMDLINE_LINUX argument dla wszystkich wpisów rozruchowych, uruchom polecenie `grubby --update-kernel=ALL --args="console=ttyS0,115200 console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"` . Więcej szczegółów można znaleźć [tutaj](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/managing_monitoring_and_updating_the_kernel/configuring-kernel-command-line-parameters_managing-monitoring-and-updating-the-kernel).

```
GRUB_TIMEOUT=5
GRUB_TERMINAL="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"
```

**Dla RHEL 7**

```
GRUB_TIMEOUT=5
GRUB_TERMINAL_OUTPUT="serial console"
GRUB_CMDLINE_LINUX="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 net.ifnames=0"
```

> [!NOTE]
> W Red Hat dostępna jest również dokumentacja dotycząca rozruchu w trybie ratowniczym, trybu awaryjnego lub trybu debugowania oraz do resetowania hasła głównego. Aby uzyskać instrukcje, zobacz [Edytowanie menu terminalu podczas rozruchu](https://aka.ms/rhel7grubterminal).

### <a name="set-up-root-access-for-single-user-mode-in-rhel"></a>Konfigurowanie dostępu głównego do trybu jednego użytkownika w RHEL
Użytkownik główny jest domyślnie wyłączony. Tryb pojedynczego użytkownika w RHEL wymaga włączenia użytkownika root. Jeśli musisz włączyć tryb pojedynczego użytkownika, użyj następujących instrukcji:

1. Zaloguj się do systemu Red Hat za pośrednictwem protokołu SSH.
1. Przejdź do katalogu głównego.
1. Włącz hasło dla użytkownika root, wykonując następujące czynności:
    * Uruchom `passwd root` (Ustaw silne hasło główne).
1. Upewnij się, że użytkownik główny może logować się tylko za pośrednictwem ttyS0, wykonując następujące czynności: a. Uruchom `edit /etc/ssh/sshd_config` polecenie i upewnij się, że PermitRootLogIn jest ustawiony na `no` .
    b. Uruchom `edit /etc/securetty file` , aby zezwolić na logowanie tylko za pośrednictwem ttyS0.

Teraz, jeśli system jest uruchamiany w trybie jednego użytkownika, można zalogować się przy użyciu hasła głównego.

Alternatywnie, aby włączyć tryb pojedynczego użytkownika w RHEL 7.4 + lub 6,9 +, zobacz [rozruch w trybie jednego użytkownika](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/5/html/installation_guide/s1-rescuemode-booting-single).

### <a name="manually-enter-single-user-mode-in-rhel"></a>Ręczne wprowadzanie trybu pojedynczego użytkownika w RHEL
Jeśli skonfigurowano GRUB i dostęp do katalogu głównego przy użyciu powyższych instrukcji, można wprowadzić tryb pojedynczego użytkownika, wykonując następujące czynności:

1. Aby wprowadzić GRUB, naciśnij klawisz ESC po ponownym uruchomieniu maszyny wirtualnej.
1. W programie GRUB naciśnij pozycję E, aby edytować system operacyjny, w którym chcesz przeprowadzić rozruch. System operacyjny jest zwykle wymieniony w pierwszym wierszu.
1. Znajdź wiersz jądra. Na platformie Azure rozpoczyna się od *linux16*.
1. Naciśnij klawisze Ctrl + E, aby przejść do końca wiersza.
1. Na końcu wiersza Dodaj *System. Unit = ratowniczy. Target*.

    Ta akcja uruchamia użytkownika w trybie jednego użytkownika. Jeśli chcesz używać trybu awaryjnego, Dodaj *systemd. Unit = Nagł. Target* do końca wiersza (zamiast *System. Unit = ratowniczy. Target*).

1. Naciśnij klawisze Ctrl + X, aby wyjść i ponownie uruchomić przy użyciu zastosowanych ustawień.

   Przed wprowadzeniem trybu pojedynczego użytkownika zostanie wyświetlony monit o hasło administratora. To hasło jest utworzone w poprzednich instrukcjach.

    ![Animowany obraz przedstawiający interfejs wiersza polecenia. Użytkownik wybiera serwer, lokalizuje koniec wiersza jądra, a następnie wprowadza określony tekst.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-enter-emergency-shell.gif)

### <a name="enter-single-user-mode-without-root-account-enabled-in-rhel"></a>Wprowadzanie trybu pojedynczego użytkownika bez włączonego konta głównego w RHEL
Jeśli użytkownik główny nie został włączony zgodnie z wcześniejszymi instrukcjami, można nadal zresetować hasło główne, wykonując następujące czynności:

> [!NOTE]
> Jeśli używasz SELinux, podczas resetowania hasła głównego Pamiętaj o wykonaniu dodatkowych kroków opisanych w [dokumentacji Red Hat](https://aka.ms/rhel7grubterminal).

1. Aby wprowadzić GRUB, naciśnij klawisz ESC po ponownym uruchomieniu maszyny wirtualnej.

1. W programie GRUB naciśnij pozycję E, aby edytować system operacyjny, w którym chcesz przeprowadzić rozruch. System operacyjny jest zwykle wymieniony w pierwszym wierszu.
1. Znajdź wiersz jądra. Na platformie Azure rozpoczyna się od *linux16*.
1. Na końcu wiersza Dodaj *Rd. Break* do końca wiersza. Pozostaw spację między wierszem jądra a *Rd. Break*.

    Ta akcja przerywa proces rozruchu przed przekazaniem kontroli z `initramfs` do `systemd` , zgodnie z opisem w [dokumentacji Red Hat](https://aka.ms/rhel7rootpassword).
1. Naciśnij klawisze Ctrl + X, aby wyjść i ponownie uruchomić przy użyciu zastosowanych ustawień.

   Po ponownym uruchomieniu nastąpi przerwanie trybu awaryjnego w systemie plików tylko do odczytu.

1. W powłoce wpisz, `mount -o remount,rw /sysroot` Aby ponownie zainstalować główny system plików z uprawnieniami do odczytu i zapisu.
1. Po przeprowadzeniu rozruchu w trybie jednego użytkownika wpisz polecenie, `chroot /sysroot` Aby przełączyć się do `sysroot` złamanymi.
1. Jesteś teraz w katalogu głównym. Hasło główne można zresetować, wprowadzając `passwd` , a następnie użyć powyższych instrukcji w celu przejścia do trybu pojedynczego użytkownika.
1. Po wykonaniu tych czynności wpisz polecenie, `reboot -f` Aby przeprowadzić ponowny rozruch.

![Animowany obraz przedstawiający interfejs wiersza polecenia. Użytkownik wybiera serwer, lokalizuje koniec wiersza jądra i wprowadza określone polecenia.](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-rhel-emergency-mount-no-root.gif)

> [!NOTE]
> Wykonanie powyższych instrukcji spowoduje przechodzenie do powłoki awaryjnej, dzięki czemu można także wykonywać zadania, takie jak edytowanie `fstab` . Zazwyczaj sugerujemy zresetowanie hasła głównego i użycie go do wprowadzenia trybu jednego użytkownika.

## <a name="access-for-centos"></a>Dostęp do CentOS
Podobnie jak Red Hat Enterprise Linux, tryb jednego użytkownika w CentOS wymaga, aby GRUB i użytkownik główny był włączony.

### <a name="grub-access-in-centos"></a>GRUB dostęp w CentOS
CentOS jest GRUB z włączoną obsługą pola. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną, wprowadzając `sudo reboot` polecenie, a następnie naciśnij dowolny klawisz. Ta akcja powoduje wyświetlenie okienka GRUB.

### <a name="single-user-mode-in-centos"></a>Tryb pojedynczego użytkownika w CentOS
Aby włączyć tryb jednego użytkownika w CentOS, postępuj zgodnie z wcześniejszymi instrukcjami dla RHEL.

## <a name="access-for-ubuntu"></a>Dostęp do Ubuntu
Obrazy Ubuntu nie wymagają hasła głównego. Jeśli system zostanie uruchomiony w trybie jednego użytkownika, można go użyć bez dodatkowych poświadczeń.

### <a name="grub-access-in-ubuntu"></a>GRUB dostęp w Ubuntu
Aby uzyskać dostęp do GRUB, naciśnij i przytrzymaj klawisz Esc podczas uruchamiania maszyny wirtualnej.

Domyślnie obrazy Ubuntu mogą nie wyświetlać automatycznie okienka GRUB. Ustawienie to można zmienić, wykonując następujące czynności:
1. W edytorze tekstów Otwórz plik */etc/default/grub.d/50-cloudimg-Settings.cfg* .

1. Zmień `GRUB_TIMEOUT` wartość na wartość różną od zera.
1. W edytorze tekstów Otwórz */etc/default/grub*.
1. Dodaj komentarz do `GRUB_HIDDEN_TIMEOUT=1` wiersza.
1. Upewnij się, że jest to `GRUB_TIMEOUT_STYLE=menu` linia.
1. Uruchom polecenie `sudo update-grub`.

### <a name="single-user-mode-in-ubuntu"></a>Tryb pojedynczego użytkownika w Ubuntu
Jeśli Ubuntu nie może uruchomić się normalnie, powoduje to automatyczne przekazanie do trybu pojedynczego użytkownika. Aby ręcznie wprowadzić tryb pojedynczego użytkownika, wykonaj następujące czynności:

1. W programie GRUB naciśnij pozycję E, aby edytować swój wpis rozruchu (Ubuntu Entry).
1. Wyszukaj wiersz zaczynający się od systemu *Linux*, a następnie wyszukaj pozycję *ro*.
1. Dodaj element *Single* po elemencie *typu ro*, upewniając się, że istnieje spacja przed i po *pojedynczym*.
1. Naciśnij kombinację klawiszy Ctrl + X, aby przeprowadzić ponowny rozruch przy użyciu tych ustawień i wprowadzić tryb pojedynczego użytkownika.

### <a name="use-grub-to-invoke-bash-in-ubuntu"></a>Użyj GRUB do wywołania bash w Ubuntu
Po wypróbowaniu powyższych instrukcji może wystąpić sytuacja (na przykład zapomniane hasło główne), w której nadal nie można uzyskać dostępu do trybu pojedynczego użytkownika na maszynie wirtualnej Ubuntu. Możesz również powiedzieć jądro, aby uruchomić `/bin/bash` jako init, a nie system init. Ta akcja daje powłokę bash i umożliwia obsługę systemu. Wykonaj następujące instrukcje:

1. W programie GRUB naciśnij pozycję E, aby edytować swój wpis rozruchu (Ubuntu Entry).

1. Wyszukaj wiersz zaczynający się od systemu *Linux*, a następnie wyszukaj pozycję *ro*.
1. Zastąp ciąg *ro* *nierw init =/bin/bash*.

    Ta akcja powoduje zainstalowanie systemu plików jako odczytu i zapisu, `/bin/bash` który jest używany jako proces init.
1. Naciśnij kombinację klawiszy Ctrl + X, aby przeprowadzić ponowny rozruch przy użyciu tych ustawień.

## <a name="access-for-coreos"></a>Dostęp do CoreOS
Tryb jednego użytkownika w CoreOS wymaga włączenia GRUB.

### <a name="grub-access-in-coreos"></a>GRUB dostęp w CoreOS
Aby uzyskać dostęp do GRUB, naciśnij dowolny klawisz, podczas gdy maszyna wirtualna jest uruchamiana.

### <a name="single-user-mode-in-coreos"></a>Tryb pojedynczego użytkownika w CoreOS
Jeśli CoreOS nie może uruchomić się normalnie, powoduje to automatyczne przekazanie do trybu pojedynczego użytkownika. Aby ręcznie wprowadzić tryb pojedynczego użytkownika, wykonaj następujące czynności:

1. W programie GRUB naciśnij pozycję E, aby edytować swój wpis rozruchu.

1. Wyszukaj wiersz rozpoczynający się od systemu *Linux $*. Powinny istnieć dwa wystąpienia wiersza, z których każdy jest hermetyzowany w innym *IF... else* — klauzula.
1. Dołącz *CoreOS. AutoLogin = ttyS0* na końcu każdego systemu *Linux $* line.
1. Naciśnij kombinację klawiszy Ctrl + X, aby przeprowadzić ponowny rozruch przy użyciu tych ustawień i wprowadzić tryb pojedynczego użytkownika.

## <a name="access-for-suse-sles"></a>Dostęp do SLES SUSE
Nowsze obrazy z SLES 12 SP3 + zezwalają na dostęp za pośrednictwem konsoli szeregowej, jeśli system jest uruchamiany w trybie awaryjnym.

### <a name="grub-access-in-suse-sles"></a>GRUB dostęp w programie SUSE SLES
Dostęp GRUB w SLES wymaga konfiguracji programu inicjującego za pośrednictwem YaST. Aby utworzyć konfigurację, wykonaj następujące czynności:

1. Aby zalogować się do maszyny wirtualnej SLES, Użyj protokołu SSH, a następnie uruchom polecenie `sudo yast bootloader` . Naciśnij klawisz Tab, naciśnij klawisz ENTER, a następnie użyj klawiszy strzałek, aby nawigować po menu.

1. Przejdź do **parametrów jądra**, a następnie zaznacz pole wyboru **Użyj konsoli szeregowej** .
1. Dodaj `serial --unit=0 --speed=9600 --parity=no` do argumentów **konsoli** .
1. Naciśnij klawisz F10, aby zapisać ustawienia i zakończyć pracę.
1. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną i naciśnij dowolny klawisz podczas sekwencji rozruchu, aby wyświetlić okienko GRUB.

    Domyślny limit czasu dla GRUB to **1**. To ustawienie można zmodyfikować, zmieniając `GRUB_TIMEOUT` zmienną w pliku */etc/default/grub* .

![Inicjowanie konfiguracji programu inicjującego](../media/virtual-machines-serial-console/virtual-machine-linux-serial-console-sles-yast-grub-config.gif)

### <a name="single-user-mode-in-suse-sles"></a>Tryb jednego użytkownika w SUSE SLES
Jeśli SLES nie może uruchomić się normalnie, nastąpi automatyczne porzucenie do powłoki awaryjnej. Aby ręcznie wprowadzić powłokę awaryjną, wykonaj następujące czynności:

1. W programie GRUB naciśnij pozycję E, aby edytować swój wpis rozruchu (SLES Entry).

1. Wyszukaj wiersz jądra, który rozpoczyna się od systemu *Linux*.
1. Dołącz *systemd. Unit = Nagł. Target* do końca wiersza jądra.
1. Naciśnij kombinację klawiszy Ctrl + X, aby ponownie uruchomić te ustawienia, a następnie wprowadź powłokę awaryjną.

   > [!NOTE]
   > Ta akcja powoduje porzucanie powłoki awaryjnej z systemem plików tylko do odczytu. Aby edytować wszystkie pliki, należy ponownie zainstalować system plików z uprawnieniami do odczytu i zapisu. Aby to zrobić, wpisz `mount -o remount,rw /` w powłoce.

## <a name="access-for-oracle-linux"></a>Dostęp do Oracle Linux
Podobnie jak Red Hat Enterprise Linux, tryb jednego użytkownika w Oracle Linux wymaga GRUB i użytkownika root.

### <a name="grub-access-in-oracle-linux"></a>Dostęp GRUB w Oracle Linux
Oracle Linux jest dostępna z włączonym GRUB. Aby wprowadzić GRUB, uruchom ponownie maszynę wirtualną, uruchamiając `sudo reboot` polecenie, a następnie naciskając klawisz ESC. Ta akcja powoduje wyświetlenie okienka GRUB. Jeśli okienko GRUB nie jest wyświetlane, upewnij się, że wartość `GRUB_TERMINAL` wiersza zawiera *konsolę szeregową* (czyli `GRUB_TERMINAL="serial console"` ). Kompiluj ponownie GRUB z `grub2-mkconfig -o /boot/grub/grub.cfg` .

### <a name="single-user-mode-in-oracle-linux"></a>Tryb jednego użytkownika w Oracle Linux
Aby włączyć tryb jednego użytkownika w Oracle Linux, postępuj zgodnie z wcześniejszymi instrukcjami dla RHEL.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat konsoli szeregowej, zobacz:
* [Dokumentacja konsoli szeregowej systemu Linux](serial-console-linux.md)
* [Używanie konsoli szeregowej do włączania GRUB w różnych dystrybucjach](http://linuxonazure.azurewebsites.net/why-proactively-ensuring-you-have-access-to-grub-and-sysrq-in-your-linux-vm-could-save-you-lots-of-down-time/)
* [Korzystanie z konsoli szeregowej dla wywołań NMI i SysRq](serial-console-nmi-sysrq.md)
* [Konsola szeregowa dla maszyn wirtualnych z systemem Windows](serial-console-windows.md)
* [Diagnostyka rozruchu](boot-diagnostics.md)
