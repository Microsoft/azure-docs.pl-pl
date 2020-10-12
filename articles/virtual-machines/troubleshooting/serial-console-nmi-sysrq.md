---
title: Usługa Azure serial Console dla wywołań SysRq i NMI | Microsoft Docs
description: Korzystanie z konsoli szeregowej dla wywołań SysRq i NMI w usłudze Azure Virtual Machines.
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
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 545399e1d7941351ce861ac98d995d5e57006ea1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074364"
---
# <a name="use-the-azure-serial-console-for-sysrq-and-nmi-calls"></a>Korzystanie z konsoli szeregowej platformy Azure na potrzeby wywołań SysRq i NMI

## <a name="system-request-sysrq"></a>Żądanie systemowe (SysRq)
SysRq to sekwencja kluczy zrozumiała dla jądra systemu operacyjnego Linux, która może wyzwalać zestaw wstępnie zdefiniowanych akcji. Te polecenia są często używane, gdy nie można przeprowadzić rozwiązywania problemów lub odzyskiwania maszyny wirtualnej za pomocą tradycyjnego administrowania (na przykład jeśli maszyna wirtualna nie odpowiada). Użycie funkcji SysRq usługi Azure serial Console spowoduje naśladowanie nacisku klawisza SysRq i znaków wprowadzonych na fizycznej klawiaturze.

Po dostarczeniu sekwencji SysRq Konfiguracja jądra będzie kontrolować sposób reagowania systemu. Aby uzyskać informacje na temat włączania i wyłączania usługi SysRq, zobacz tekst *przewodnika administratora sysrq* [text](https://aka.ms/kernelorgsysreqdoc)  |  [markdown](https://aka.ms/linuxsysrq).

Konsoli szeregowej platformy Azure można użyć do wysyłania SysRq do maszyny wirtualnej platformy Azure przy użyciu ikony klawiatury na pasku poleceń przedstawionym poniżej.

![Zrzut ekranu konsoli szeregowej platformy Azure. Ikona klawiatury zostanie wyróżniona, a jej menu jest widoczne. To menu zawiera element polecenia Send SysRq.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Wybranie polecenia "Wyślij SysRq polecenie" spowoduje otwarcie okna dialogowego, w którym będą dostępne typowe opcje SysRq lub zaakceptowania sekwencji poleceń SysRq wprowadzonych w oknie dialogowym.  Dzięki temu w serii SysRq można wykonywać operacje wysokiego poziomu, takie jak bezpieczny ponowny rozruch przy użyciu: `REISUB` .

![Zrzut ekranu przedstawiający okno dialogowe wysyłanie polecenia SysRq do gościa. Opcja wprowadzania poleceń jest zaznaczona, a pole polecenia zawiera REISUB.](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Nie można użyć polecenia SysRq na maszynach wirtualnych, które są zatrzymane lub których jądro jest w stanie innym niż odpowiada. (na przykład awaryjnego jądra).

### <a name="enable-sysrq"></a>Włącz SysRq
Zgodnie z opisem w powyższym *podręczniku administratora sysrq* , sysrq można skonfigurować tak, aby nie były dostępne tylko niektóre polecenia. Wszystkie polecenia SysRq można włączyć za pomocą poniższego kroku, ale nie spowoduje to ponownego uruchomienia:
```
echo "1" >/proc/sys/kernel/sysrq
```
Aby konfiguracja SysReq trwała, można wykonać następujące czynności, aby włączyć wszystkie polecenia SysRq
1. Dodawanie tego wiersza do */etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Ponowne uruchamianie lub aktualizowanie sysctl przez uruchomienie <br>
    `sysctl -p`

### <a name="command-keys"></a>Klucze poleceń
W podręczniku administratora SysRq powyżej:

|Polecenie| Funkcja
| ------| ----------- |
|``b``  |   Natychmiast ponownie uruchomi system bez synchronizowania lub odinstalowywania dysków.
|``c``  |   Przeprowadzi awarię systemu przez odwołanie do PUSTEgo wskaźnika. W przypadku skonfigurowania zrzutu awaryjnego zostanie wykonane.
|``d``  |   Pokazuje wszystkie blokady, które są przechowywane.
|``e``  |   Wyślij SIGTERM do wszystkich procesów, z wyjątkiem init.
|``f``  |   Wywoła OOM killer, aby skasować proces Hog pamięci, ale nie awaryjnego, jeśli nic nie może zostać zabite.
|``g``  |   Używane przez KGDB (debuger jądra)
|``h``  |   Wyświetla pomoc (wszystkie inne klucze niż wymienione w tym miejscu również wyświetlą pomoc, ale ``h`` łatwą do zapamiętania:-)
|``i``  |    Wyślij SIGKILL do wszystkich procesów, z wyjątkiem init.
|``j``  |    Wymuś "po prostu rozmrożenie" — system plików zamrożony przez polecenie IOCTL FIFREEZE.
|``k``  |    Klucz dostępu Secure (SAK) kasuje wszystkie programy w bieżącej konsoli wirtualnej. Uwaga: Zobacz ważne komentarze poniżej w sekcji SAK.
|``l``  |    Pokazuje ślad stosu dla wszystkich aktywnych procesorów CPU.
|``m``  |    Spowoduje zrzut informacji o bieżącej pamięci do konsoli programu.
|``n``  |    Służy do zwiększania możliwości zadań RT
|``o``  |    Program zamknie system (jeśli jest skonfigurowany i obsługiwany).
|``p``  |    Spowoduje zrzut bieżących rejestrów i flag do konsoli programu.
|``q``  |    Spowoduje zrzut na listę wszystkich procesorów hrtimers (ale nie zwykłych czasomierzy timer_list) i szczegółowych informacji o wszystkich urządzeniach clockevent.
|``r``  |    Wyłącza tryb RAW klawiatury i ustawia go na XLATE.
|``s``  |    Spróbuje zsynchronizować wszystkie zainstalowane systemy plików.
|``t``  |    Spowoduje zrzut listy bieżących zadań i ich informacji do konsoli programu.
|``u``  |    Spróbuje ponownie zainstalować wszystkie zainstalowane systemy plików tylko do odczytu.
|``v``  |    Wymuszone przywrócenie konsoli bufor ramki
|``v``  |    Powoduje zrzut bufora ETM [specyficzne dla ARM]
|``w``  |    Zrzuca zadania, które są w stanie niedostępnym (zablokowanym).
|``x``  |    Używany przez interfejs xmon na platformach PPC/PowerPC. Pokaż globalne rejestry PMU na sparc64. Zrzuć wszystkie wpisy TLB na MIPS.
|``y``  |    Pokaż globalne rejestry procesora [SPARC-64 specyficzne]
|``z``  |    Zrzuć bufor ftrace
|``0``-``9`` | Ustawia poziom rejestrowania konsoli, kontrolując, które komunikaty jądra będą drukowane w konsoli programu. ( ``0`` na przykład może to spowodować, że tylko wiadomości awaryjne, takie jak rozruchem lub zostałyby wprowadzone do konsoli).

### <a name="distribution-specific-documentation"></a>Dokumentacja dotycząca dystrybucji ###
Aby uzyskać dokumentację dotyczącą dystrybucji na SysRq i kroki konfigurowania systemu Linux w celu utworzenia zrzutu awaryjnego, gdy odbierze polecenie SysRq "Crash", zobacz poniższe linki:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Zrzut awaryjny jądra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Co to jest obiekt SysRq i jak go używać?](https://access.redhat.com/articles/231663)
- [Jak zbierać informacje z serwera RHEL przy użyciu funkcji SysRq](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurowanie przechwytywania zrzutu rdzeni jądra](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Zbieranie dzienników awarii](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Przerwanie z maską (NMI)
Przerwanie z maską (NMI) zostało zaprojektowane w celu utworzenia sygnału, że oprogramowanie na maszynie wirtualnej nie zostanie zignorowane. Historycznie użyto NMIs do monitorowania problemów sprzętowych w systemach, które wymagały określonych czasów odpowiedzi.  Obecnie programiści i Administratorzy systemu często używają NMI jako mechanizmu debugowania lub rozwiązywania problemów z nieodpowiadającymi systemami.

Konsola szeregowa może służyć do wysyłania NMI do maszyny wirtualnej platformy Azure przy użyciu ikony klawiatury na pasku poleceń przedstawionym poniżej. Po dostarczeniu NMI konfiguracja maszyny wirtualnej będzie kontrolować sposób reagowania systemu.  Systemy operacyjne Linux można skonfigurować w taki sposób, aby awaria i utworzyć zrzut pamięci system operacyjny odbiera NMI.

![Zrzut ekranu konsoli szeregowej. Ikona klawiatury zostanie wyróżniona, a jej menu jest widoczne. To menu zawiera pozycję Wyślij element przerwania bez maskowania.](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

### <a name="enable-nmi"></a>Włącz NMI
W przypadku systemów Linux, które obsługują sysctl do konfigurowania parametrów jądra, można włączyć awaryjnego podczas uzyskiwania tego NMI za pomocą następującego polecenia:
1. Dodawanie tego wiersza do */etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Ponowne uruchamianie lub aktualizowanie sysctl przez uruchomienie <br>
    `sysctl -p`

Aby uzyskać więcej informacji na temat konfiguracji jądra systemu Linux, w tym `unknown_nmi_panic` , `panic_on_io_nmi` , i `panic_on_unrecovered_nmi` , zobacz: [Dokumentacja dla/proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Aby uzyskać dokumentację dotyczącą dystrybucji na NMI i kroki konfigurowania systemu Linux w celu utworzenia zrzutu awaryjnego po odebraniu NMI, zobacz poniższe linki:

### <a name="ubuntu"></a>Ubuntu
 - [Zrzut awaryjny jądra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat
 - [Co to jest NMI i czego można z niej korzystać?](https://access.redhat.com/solutions/4127)
 - [Jak skonfigurować system do awarii, gdy przełącznik NMI jest wypychany?](https://access.redhat.com/solutions/125103)
 - [Podręcznik administratora zrzutu awaryjnego](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE
- [Konfigurowanie przechwytywania zrzutu rdzeni jądra](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS
- [Zbieranie dzienników awarii](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Następne kroki
* Główna strona dokumentacji systemu Linux w konsoli szeregowej znajduje się [tutaj](serial-console-linux.md).
* Używanie konsoli szeregowej do rozruchu w [grub i wprowadzania trybu pojedynczego użytkownika](serial-console-grub-single-user-mode.md)
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Windows](serial-console-windows.md)
* Dowiedz się więcej na temat [diagnostyki rozruchu](boot-diagnostics.md)