---
title: Usługa Azure Serial Console dla wywołań SysRq i NMI
description: Korzystanie z konsoli szeregowej dla wywołań SysRq i NMI na maszynach wirtualnych platformy Azure.
author: asinn826
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 5541dec748f31818a0e9485fc0c56b7926ccaae7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758490"
---
# <a name="use-serial-console-for-sysrq-and-nmi-calls"></a>Korzystanie z konsoli szeregowej w przypadku wywołań SysRq i NMI

## <a name="system-request-sysrq"></a>Żądanie systemowe (SysRq)
SysRq to sekwencja kluczy zrozumiała dla jądra systemu operacyjnego Linux, która może wyzwolić zestaw wstępnie zdefiniowanych akcji. Te polecenia są często używane, gdy rozwiązywanie problemów z maszyną wirtualną lub odzyskiwanie nie można wykonać za pośrednictwem tradycyjnej administracji (na przykład, jeśli maszyna wirtualna nie odpowiada). Za pomocą funkcji SysRq usługi Azure Serial Console będzie naśladować naciśnięcie klawisza SysRq i znaków wprowadzonych na klawiaturze fizycznej.

Po dostarczeniu sekwencji SysRq konfiguracja jądra będzie kontrolować sposób reagowania systemu. Aby uzyskać informacje na temat włączania i wyłączania sysRq, zobacz *SysRq Admin Guide* [text](https://aka.ms/kernelorgsysreqdoc) | [markdown](https://aka.ms/linuxsysrq).  

Usługa Azure Serial Console może służyć do wysyłania SysRq do maszyny wirtualnej platformy Azure przy użyciu ikony klawiatury na pasku poleceń pokazano poniżej.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg)

Wybranie opcji "Wyślij polecenie SysRq" spowoduje otwarcie okna dialogowego, które zapewni typowe opcje SysRq lub zaakceptuje sekwencję poleceń SysRq wprowadzonych do okna dialogowego.  Pozwala to na serię SysRq do wykonywania operacji wysokiego poziomu, `REISUB`takich jak bezpieczny restart za pomocą: .

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-sysreq_UI.png)

Nie można używać polecenia SysRq na maszynach wirtualnych, które są zatrzymane lub których jądro jest w stanie niesponsywnym. (na przykład panika jądra).

### <a name="enable-sysrq"></a>Włącz SysRq 
Jak opisano w *powyższym Przewodniku administracyjnym SysRq,* SysRq można skonfigurować w taki sposób, że dostępne są wszystkie, żadne lub tylko niektóre polecenia. Możesz włączyć wszystkie polecenia SysRq za pomocą poniższego kroku, ale nie przetrwa ponownego uruchomienia:
```
echo "1" >/proc/sys/kernel/sysrq
```
Aby konfiguracja SysReq była trwała, można wykonać następujące czynności, aby włączyć wszystkie polecenia SysRq
1. Dodawanie tego *wiersza do /etc/sysctl.conf* <br>
    `kernel.sysrq = 1`
1. Ponowne uruchamianie lub aktualizowanie sysctl przez uruchomienie <br>
    `sysctl -p`

### <a name="command-keys"></a>Klawisze poleceń 
Z SysRq Admin Guide powyżej:

|Polecenie| Funkcja
| ------| ----------- |
|``b``  |   Natychmiast uruchomi ponownie system bez synchronizacji lub odinstalowania dysków.
|``c``  |   Spowoduje to wykonanie awarii systemu przez wyłudczenie wskaźnika NULL. Zostanie podjęta awaria, jeśli zostanie skonfigurowana.
|``d``  |   Pokazuje wszystkie blokady, które są przechowywane.
|``e``  |   Wyślij SIGTERM do wszystkich procesów, z wyjątkiem init.
|``f``  |   Zadzwoni do zabójcy oom, aby zabić proces wieprzowy pamięci, ale nie panikuj, jeśli nic nie może zostać zabite.
|``g``  |   Używany przez kgdb (debuger jądra)
|``h``  |   Wyświetli pomoc (każdy inny klucz niż te wymienione ``h`` tutaj również wyświetli pomoc, ale jest łatwy do zapamiętania :-)
|``i``  |    Wyślij SIGKILL do wszystkich procesów, z wyjątkiem init.
|``j``  |    Siłą "Po prostu rozmrozić" - filesystems zamrożone przez FIFREEZE ioctl.
|``k``  |    Klucz bezpiecznego dostępu (SAK) zabija wszystkie programy na bieżącej konsoli wirtualnej. UWAGA: Zobacz ważne komentarze poniżej w sekcji SAK.
|``l``  |    Pokazuje backtrace stosu dla wszystkich aktywnych procesorów.
|``m``  |    Zrzuci bieżące informacje o pamięci do konsoli.
|``n``  |    Służy do zadań RT nice-stanie
|``o``  |    Wyłączy system (jeśli jest skonfigurowany i obsługiwany).
|``p``  |    Zrzuci bieżące rejestry i flagi do konsoli.
|``q``  |    Zrzuci na listę procesorów wszystkich uzbrojonych hrtimerów (ale nie zwykłych timer_list czasomierzy) i szczegółowe informacje o wszystkich urządzeniach clockevent.
|``r``  |    Wyłącza tryb nieprzetworzony klawiatury i ustawia go na XLATE.
|``s``  |    Spróbuje zsynchronizować wszystkie zainstalowane systemy plików.
|``t``  |    Zrzuci listę bieżących zadań i ich informacje na konsoli.
|``u``  |    Spróbuje ponownie zamontować wszystkie zainstalowane systemy plików tylko do odczytu.
|``v``  |    Zdecydowanie przywraca konsolę framebuffer
|``v``  |    Powoduje zrzut buforu ETM [specyficzne dla ARM]
|``w``  |    Zrzuca zadania, które są w stanie awaryjnym (zablokowanym).
|``x``  |    Używany przez interfejs xmon na platformach ppc/powerpc. Pokaż globalne rejestry PMU na sparc64. Zrzuć wszystkie wpisy TLB na MIPS.
|``y``  |    Pokaż globalne rejestry procesorów [specyficzne dla SPARC-64]
|``z``  |    Zrzut bufora ftrace
|``0``-``9`` | Ustawia poziom dziennika konsoli, kontrolując, które komunikaty jądra będą drukowane na konsoli. (``0``, na przykład sprawi, że tylko komunikaty alarmowe, takie jak PANICs lub OOPSes, dojechają do konsoli).)

### <a name="distribution-specific-documentation"></a>Dokumentacja specyficzna dla dystrybucji ###
Aby uzyskać dokumentację specyficzną dla dystrybucji na SysRq i kroki konfigurowania systemu Linux do tworzenia zrzutu awaryjnego po otrzymaniu polecenia SysRq "Crash", zobacz poniższe łącza:

#### <a name="ubuntu"></a>Ubuntu ####
 - [Zrzut awaryjny jądra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

#### <a name="red-hat"></a>Red Hat ####
- [Co to jest obiekt SysRq i jak z niego korzystać?](https://access.redhat.com/articles/231663)
- [Jak korzystać z funkcji SysRq do zbierania informacji z serwera RHEL](https://access.redhat.com/solutions/2023)

#### <a name="suse"></a>SUSE ####
- [Konfigurowanie przechwytywania zrzutu rdzenia jądra](https://www.suse.com/support/kb/doc/?id=3374462)

#### <a name="coreos"></a>CoreOS ####
- [Zbieranie dzienników awarii](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="non-maskable-interrupt-nmi"></a>Przerwanie niemaskowalne (NMI) 
Przerwanie niemaskowane (NMI) ma na celu utworzenie sygnału, że oprogramowanie na maszynie wirtualnej nie będzie ignorować. W przeszłości nmi były używane do monitorowania problemów sprzętowych w systemach, które wymagają określonych czasów odpowiedzi.  Obecnie programiści i administratorzy systemu często używają NMI jako mechanizmu do debugowania lub rozwiązywania problemów z systemami, które nie odpowiadają.

Konsola szeregowa może służyć do wysyłania nmi do maszyny wirtualnej platformy Azure przy użyciu ikony klawiatury na pasku poleceń pokazano poniżej. Po dostarczeniu nmi konfiguracji maszyny wirtualnej będzie kontrolować, jak system reaguje.  Systemy operacyjne Linux można skonfigurować tak, aby ulegały awarii i tworzyły zrzut pamięci, a system operacyjny otrzymuje NMI.

![](../media/virtual-machines-serial-console/virtual-machine-serial-console-command-menu.jpg) <br>

W przypadku systemów Linux obsługujących sysctl do konfigurowania parametrów jądra można włączyć błąd podczas odbierania tego NMI, korzystając z następujących czynności:
1. Dodawanie tego *wiersza do /etc/sysctl.conf* <br>
    `kernel.panic_on_unrecovered_nmi=1`
1. Ponowne uruchamianie lub aktualizowanie sysctl przez uruchomienie <br>
    `sysctl -p`

Aby uzyskać więcej informacji na temat `unknown_nmi_panic` `panic_on_io_nmi`konfiguracji `panic_on_unrecovered_nmi`jądra Linuksa, w tym , i , zobacz: [Dokumentacja dla /proc/sys/kernel/*](https://www.kernel.org/doc/Documentation/sysctl/kernel.txt). Aby uzyskać dokumentację specyficzną dla dystrybucji w systemie NMI i kroki konfigurowania systemu Linux do tworzenia zrzutu awaryjnego po otrzymaniu nmi, zobacz poniższe łącza:
 
### <a name="ubuntu"></a>Ubuntu 
 - [Zrzut awaryjny jądra](https://help.ubuntu.com/lts/serverguide/kernel-crash-dump.html)

### <a name="red-hat"></a>Red Hat 
 - [Co to jest NMI i do czego można go używać?](https://access.redhat.com/solutions/4127)
 - [Jak skonfigurować system do awarii po naciśnięciu przełącznika NMI?](https://access.redhat.com/solutions/125103)
 - [Przewodnik po administratorze zrzutu awaryjnego](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/pdf/kernel_crash_dump_guide/kernel-crash-dump-guide.pdf)

### <a name="suse"></a>SUSE 
- [Konfigurowanie przechwytywania zrzutu rdzenia jądra](https://www.suse.com/support/kb/doc/?id=3374462)

### <a name="coreos"></a>CoreOS 
- [Zbieranie dzienników awarii](https://coreos.com/os/docs/latest/collecting-crash-logs.html)

## <a name="next-steps"></a>Następne kroki
* Główna strona dokumentacji Konsoli Szeregowej Linuksa znajduje się [tutaj](serial-console.md).
* Uruchamianie w trybie GRUB za pomocą konsoli szeregowej [i przejście do trybu pojedynczego użytkownika](serial-console-grub-single-user-mode.md)
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Windows](../windows/serial-console.md)
* Dowiedz się więcej o [diagnostyce rozruchu](boot-diagnostics.md)