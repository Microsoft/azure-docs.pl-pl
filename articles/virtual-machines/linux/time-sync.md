---
title: Synchronizacja czasu dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Synchronizacja czasu dla maszyn wirtualnych Systemu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 7c93c1f525713a90abd71c30a21401b9d1cfcb9f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460906"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Synchronizacja czasu dla maszyn wirtualnych z systemem Linux na platformie Azure

Synchronizacja czasu jest ważne dla zabezpieczeń i korelacji zdarzeń. Czasami jest on używany do implementacji transakcji rozproszonych. Dokładność czasu między wieloma systemami komputerowymi uzyskuje się poprzez synchronizację. Synchronizacja może mieć wpływ na wiele rzeczy, w tym ponowne uruchomienie i ruch sieciowy między źródłem czasu a komputerem pobierającym czas. 

Platforma Azure jest wspierana przez infrastrukturę z systemem Windows Server 2016. System Windows Server 2016 udoskonalił algorytmy używane do korygowania czasu i kondycjonowania zegara lokalnego w celu synchronizacji z czasem UTC.  Funkcja Dokładnego czasu systemu Windows Server 2016 znacznie poprawiła sposób, w jaki usługa VMICTimeSync, która reguluje maszyny wirtualne z hostem, zapewnia dokładny czas. Ulepszenia obejmują dokładniejszy czas początkowy na początku maszyny Wirtualnej lub przywracanie maszyny Wirtualnej i korekcji opóźnienia przerwania. 

>[!NOTE]
>Aby uzyskać szybki przegląd usługi Czas systemu Windows, zapoznaj się z tym [klipem wideo przedstawiającym omówienie wysokiego poziomu](https://aka.ms/WS2016TimeVideo).
>
> Aby uzyskać więcej informacji, zobacz [Dokładny czas dla systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Omówienie

Dokładność zegara komputerowego jest mierzona na podstawie tego, jak blisko zegara komputera jest standard czasu skoordynowanego czasu uniwersalnego (UTC). UTC jest definiowany przez wielonarodową próbkę precyzyjnych zegarów atomowych, które mogą być wyłączone tylko o jedną sekundę w ciągu 300 lat. Ale czytanie UTC bezpośrednio wymaga specjalistycznego sprzętu. Zamiast tego serwery czasu są synchronizowane z czasem UTC i są dostępne z innych komputerów, aby zapewnić skalowalność i niezawodność. Każdy komputer ma uruchomione usługi synchronizacji czasu, która wie, jakich serwerów czasu do użycia i okresowo sprawdza, czy zegar komputera musi zostać poprawiony i dostosowuje czas w razie potrzeby. 

Hosty platformy Azure są synchronizowane z wewnętrznymi serwerami czasu firmy Microsoft, które poświęcają swój czas z urządzeń Stratum 1 należących do firmy Microsoft z antenami GPS. Maszyny wirtualne na platformie Azure może zależeć od ich hosta, aby przekazać dokładny czas *(czas hosta)* na maszynie wirtualnej lub maszyny Wirtualnej można bezpośrednio uzyskać czas z serwera czasu lub kombinacji obu. 

Na sprzęcie autonomicznym system operacyjny Linux odczytuje tylko zegar sprzętowy hosta podczas rozruchu. Następnie zegar jest utrzymywany przy użyciu czasomierza przerwania w jądrze Linuksa. W tej konfiguracji zegar będzie dryfować w czasie. W nowszych dystrybucjach systemu Linux na platformie Azure maszyny wirtualne mogą częściej używać dostawcy VMICTimeSync, uwzględnionego w usługach integracji systemu Linux (LIS), do częstszego wykonywania zapytań o aktualizacje zegara z hosta.

Interakcje maszyny wirtualnej z hostem mogą również wpływać na zegar. Podczas [konserwacji pamięci](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)maszyny wirtualne są wstrzymane na maksymalnie 30 sekund. Na przykład przed rozpoczęciem konserwacji zegar maszyny Wirtualnej pokazuje 10:00:00 i trwa 28 sekund. Po wznowieniu maszyny Wirtualnej zegar na maszynie Wirtualnej będzie nadal wyświetlane 10:00:00 AM, który będzie 28 sekund wyłączony. Aby to poprawić, usługa VMICTimeSync monitoruje to, co dzieje się na hoście i monituje o wprowadzenie zmian na maszynach wirtualnych w celu zrekompensowania.

Bez synchronizacji czasu pracy zegar na maszynie Wirtualnej będzie gromadzić błędy. Gdy istnieje tylko jedna maszyna wirtualna, efekt może nie być znaczący, chyba że obciążenie wymaga bardzo dokładnego pomiaru czasu. Ale w większości przypadków mamy wiele połączonych ze sobą maszyn wirtualnych, które używają czasu do śledzenia transakcji, a czas musi być spójny przez całe wdrożenie. Gdy czas między maszynami wirtualnymi jest inny, można zobaczyć następujące efekty:

- Uwierzytelnianie zakończy się niepowodzeniem. Protokoły zabezpieczeń, takie jak Kerberos lub technologia zależna od certyfikatu, opierają się na spójnym czasie w różnych systemach.
- Bardzo trudno jest dowiedzieć się, co się stało w systemie, jeśli dzienniki (lub inne dane) nie zgadzają się na czas. To samo zdarzenie wyglądałoby tak, jakby miało miejsce w różnym czasie, co utrudniało korelację.
- Jeśli zegar jest wyłączony, rozliczenia mogą być obliczane niepoprawnie.



## <a name="configuration-options"></a>Opcje konfiguracji

Zasadniczo istnieją trzy sposoby konfigurowania synchronizacji czasu dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure:

- Domyślna konfiguracja obrazów usługi Azure Marketplace używa zarówno czasu NTP, jak i czasu hosta VMICTimeSync. 
- Tylko host przy użyciu programu VMICTimeSync.
- Użyj innego zewnętrznego serwera czasu z lub bez użycia funkcji hosta VMICTimeSync.


### <a name="use-the-default"></a>Użyj wartości domyślnej

Domyślnie większość obrazów usługi Azure Marketplace dla systemu Linux jest skonfigurowana do synchronizacji z dwóch źródeł: 

- NTP jako podstawowy, który pobiera czas z serwera NTP. Na przykład obrazy Ubuntu 16.04 LTS Marketplace używają **ntp.ubuntu.com**.
- Usługa VMICTimeSync jako pomocnicza, używana do komunikowania czasu hosta z maszynami wirtualnymi i wprowadzania poprawek po wstrzymaniu maszyny Wirtualnej w celu konserwacji. Hosty platformy Azure używają urządzeń Stratum 1 należących do firmy Microsoft, aby zachować dokładny czas.

W nowszych dystrybucjach systemu Linux usługa VMICTimeSync używa protokołu czasu precyzji (PTP), ale wcześniejsze dystrybucje mogą nie obsługiwać PTP i będą powracać do NTP w celu uzyskania czasu od hosta.

Aby potwierdzić, że ntp `ntpq -p` synchronizuje się poprawnie, uruchom polecenie.

### <a name="host-only"></a>Tylko host 

Ponieważ serwery NTP, takie jak time.windows.com i ntp.ubuntu.com są publiczne, synchronizacja czasu z nimi wymaga wysyłania ruchu przez Internet. Różne opóźnienia pakietów mogą negatywnie wpłynąć na jakość synchronizacji czasu. Usunięcie ntp przez przełączenie na synchronizację tylko dla hosta może czasami poprawić wyniki synchronizacji czasu.

Przełączanie do synchronizacji czasu tylko do hosta ma sens, jeśli występują problemy z synchronizacją czasu przy użyciu konfiguracji domyślnej. Wypróbuj synchronizację tylko z hostem, aby sprawdzić, czy poprawiłoby to synchronizację czasu na maszynie wirtualnej. 

### <a name="external-time-server"></a>Zewnętrzny serwer czasu

Jeśli masz określone wymagania dotyczące synchronizacji czasu, istnieje również możliwość korzystania z zewnętrznych serwerów czasu. Zewnętrzne serwery czasu mogą zapewnić określony czas, który może być przydatny w scenariuszach testowych, zapewniając jednorodność czasu z maszynami hostowanymi w centrach danych innych firm lub obsługując sekundy przestępne w specjalny sposób.

Można połączyć zewnętrzny serwer czasu z usługą VMICTimeSync, aby zapewnić wyniki podobne do konfiguracji domyślnej. Połączenie zewnętrznego serwera czasu z programem VMICTimeSync jest najlepszym rozwiązaniem w przypadku rozwiązywania problemów, które mogą być przyczyną wstrzymania maszyn wirtualnych w celu konserwacji. 

## <a name="tools-and-resources"></a>Narzędzia i zasoby

Istnieje kilka podstawowych poleceń do sprawdzania konfiguracji synchronizacji czasu. Dokumentacja dla dystrybucji linuksa będzie miała więcej szczegółów na temat najlepszego sposobu konfigurowania synchronizacji czasu dla tej dystrybucji.

### <a name="integration-services"></a>Usługi integracji

Sprawdź, czy usługa integracji (hv_utils) jest załadowana.

```bash
lsmod | grep hv_utils
```
Powinieneś zobaczyć coś podobnego do tego:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Sprawdź, czy demon usług integracji funkcji Hyper-V jest uruchomiony.

```bash
ps -ef | grep hv
```

Powinieneś zobaczyć coś podobnego do tego:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp"></a>Sprawdź, czy nie ma PTP

W nowszych wersjach systemu Linux źródło zegara PTP (Precision Time Protocol) jest dostępne jako część dostawcy VMICTimeSync. W starszych wersjach systemu Red Hat Enterprise Linux lub CentOS 7.x można pobrać i użyć [usług integracji linuksowej](https://github.com/LIS/lis-next) do zainstalowania zaktualizowanego sterownika. Podczas korzystania z PTP urządzenie Linux będzie w formie /dev/ptp*x*. 

Zobacz, które źródła zegara PTP są dostępne.

```bash
ls /sys/class/ptp
```

W tym przykładzie zwracana wartość to *ptp0*, więc używamy go do sprawdzenia nazwy zegara. Aby zweryfikować urządzenie, sprawdź nazwę zegara.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Powinno to zwrócić **hyperv**.

### <a name="chrony"></a>chrony

W wersjach Ubuntu 19.10 i nowszych, Red Hat Enterprise Linux i CentOS 7.x [chrony](https://chrony.tuxfamily.org/) jest skonfigurowany do używania zegara źródłowego PTP. Zamiast chrony starsze wersje Linuksa używają demona Network Time Protocol (ntpd), który nie obsługuje źródeł PTP. Aby włączyć PTP w tych wersjach, chrony musi być ręcznie zainstalowany i skonfigurowany (w chrony.conf) przy użyciu następującego kodu:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Aby uzyskać więcej informacji na temat Ubuntu i NTP, zobacz [Synchronizacja czasu](https://help.ubuntu.com/lts/serverguide/NTP.html).

Aby uzyskać więcej informacji na temat red hat i NTP, zobacz [Konfigurowanie NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/s1-configure_ntp). 

Aby uzyskać więcej informacji na temat chrony, zobacz [Korzystanie z chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/sect-using_chrony).

Jeśli zarówno źródła chrony, jak i TimeSync są włączone jednocześnie, można oznaczyć jeden jako **preferowany**, który ustawia drugie źródło jako kopię zapasową. Ponieważ usługi NTP nie aktualizują zegara dla dużych pochylenia, z wyjątkiem po długim okresie, VMICTimeSync będzie odzyskać zegar z wstrzymanych zdarzeń maszyny Wirtualnej znacznie szybciej niż narzędzia oparte na NTP sam.

Domyślnie chronyd przyspiesza lub spowalnia zegar systemowy, aby naprawić każdy dryf czasu. Jeśli dryf staje się zbyt duży, chrony nie naprawi dryfu. Aby to przezwyciężyć, `makestep` parametr w **/etc/chrony.conf** można zmienić, aby wymusić timesync, jeśli dryf przekracza określony próg.

 ```bash
makestep 1.0 -1
```

W tym miejscu chrony wymusi aktualizację czasu, jeśli dryf jest większy niż 1 sekundę. Aby zastosować zmiany, uruchom ponownie usługę chronyd:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>Systemd 

Na SUSE i Ubuntu zwalnia przed 19.10, synchronizacja czasu jest skonfigurowany za pomocą [systemd](https://www.freedesktop.org/wiki/Software/systemd/). Aby uzyskać więcej informacji na temat Ubuntu, zobacz [Synchronizacja czasu](https://help.ubuntu.com/lts/serverguide/NTP.html). Aby uzyskać więcej informacji na temat SUSE, zobacz Sekcja 4.5.8 w [SUSE Linux Enterprise Server 12 SP3 Release Notes](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Dokładny czas dla systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time).


