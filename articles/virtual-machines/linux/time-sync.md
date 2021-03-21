---
title: Synchronizacja czasu dla maszyn wirtualnych z systemem Linux na platformie Azure
description: Synchronizacja czasu dla maszyn wirtualnych z systemem Linux.
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552647"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Synchronizacja czasu dla maszyn wirtualnych z systemem Linux na platformie Azure

Synchronizacja czasu jest ważna dla korelacji zabezpieczeń i zdarzeń. Czasami jest używany dla implementacji transakcji rozproszonych. Dokładność czasu między wieloma systemami komputerowymi uzyskuje się przez synchronizację. Na synchronizację może wpływać wiele rzeczy, w tym ponowny rozruch i ruch sieciowy między źródłem czasu a komputerem, który pobiera czas. 

Platforma Azure jest obsługiwana przez infrastrukturę z systemem Windows Server 2016. W systemie Windows Server 2016 udoskonalono algorytmy służące do korygowania czasu i warunku zegara lokalnego na synchronizację z czasem UTC.  Funkcja dokładnego czasu systemu Windows Server 2016 znacznie poprawiła sposób, w jaki usługa VMICTimeSync, która zarządza maszynami wirtualnymi na hoście w celu dokładnego czasu. Udoskonalenia obejmują dokładniejszy czas początkowy w przypadku uruchamiania maszyny wirtualnej lub przywracania maszyny wirtualnej i korekcji opóźnienia przerwań. 

> [!NOTE]
> Aby zapoznać się z krótkim omówieniem usługi czas systemu Windows, zapoznaj się z tym [ogólnym omówieniem wideo](https://aka.ms/WS2016TimeVideo).
>
> Aby uzyskać więcej informacji, zobacz [dokładny czas dla systemu Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Omówienie

Dokładność zegara komputerowego jest określana na tym, jak zamyka się zegar komputera do standardowego czasu koordynowanego (UTC). Czas UTC jest definiowany przez wielonarodowy przykład precyzyjne zegara niepodzielne, które mogą być wyłączone tylko przez jedną sekundę w latach 300. Jednak odczytywanie czasu UTC bezpośrednio wymaga specjalnego sprzętu. Zamiast tego serwery czasu są synchronizowane z czasem UTC i są dostępne z innych komputerów w celu zapewnienia skalowalności i niezawodności. Każdy komputer ma uruchomioną usługę synchronizacji czasu, która wie, jakie serwery czasu użyć i okresowo sprawdzają, czy zegar komputera musi zostać skorygowany i w razie potrzeby dostosowuje czas. 

Hosty platformy Azure są synchronizowane z wewnętrznymi serwerami Microsoft Time, które pobierają czas od urządzeń z warstwy 1 należącej do firmy Microsoft przy użyciu anten GPS. Maszyny wirtualne na platformie Azure mogą być zależne od ich hosta w celu przekazania dokładnego czasu (*czasu hosta*) do maszyny wirtualnej lub maszyny wirtualnej mogą bezpośrednio uzyskać czas z serwera czasu lub kombinację obu tych metod. 

Na sprzęcie autonomicznym system operacyjny Linux odczytuje tylko zegar sprzętu hosta podczas rozruchu. Następnie zegar jest obsługiwany przy użyciu czasomierza przerwania w jądrze systemu Linux. W tej konfiguracji zegar będzie przekroczyć czas. W przypadku nowszych dystrybucji systemu Linux na platformie Azure maszyny wirtualne mogą używać dostawcy VMICTimeSync, zawartego w usługach Linux Integration Services (LIS), do wykonywania zapytań o aktualizacje zegarów z hosta częściej.

Interakcje maszyny wirtualnej z hostem mogą również mieć wpływ na zegar. Podczas [zachowywania konserwacji pamięci](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)maszyny wirtualne są wstrzymywane przez maksymalnie 30 sekund. Na przykład przed rozpoczęciem obsługi zegar maszyny wirtualnej pokazuje 10:00:00 AM i trwa 28 sekund. Po wznowieniu działania maszyny wirtualnej zegar na maszynie wirtualnej nadal będzie wyświetlał 10:00:00 AM, co spowodowałoby 28 sekund. Aby rozwiązać ten problem, usługa VMICTimeSync monitoruje co dzieje się na hoście i pojawia się w celu wyrównania zmian w maszynach wirtualnych.

Bez pracy synchronizacji czasu zegar na maszynie wirtualnej będzie zbierać błędy. Jeśli istnieje tylko jedna maszyna wirtualna, efekt może nie być znaczący, chyba że obciążenie wymaga wysoce dokładnego dokładnych. Jednak w większości przypadków korzystamy z wielu połączonych z nimi maszyn wirtualnych, które używają czasu do śledzenia transakcji, a czas musi być spójny w całym wdrożeniu. Gdy czas między maszynami wirtualnymi jest inny, można zobaczyć następujące efekty:

- Uwierzytelnianie nie powiedzie się. Protokoły zabezpieczeń, takie jak Kerberos lub technologia zależna od certyfikatu, są zależne od systemu.
- Bardzo trudno jest ustalić, co się stało w systemie, Jeśli dzienniki (lub inne dane) nie zgadzają się na czas. To samo zdarzenie będzie wyglądać tak, jakby miało miejsce w różnym czasie, co utrudnia korelację.
- Jeśli zegar jest wyłączony, rozliczenie może zostać obliczone nieprawidłowo.



## <a name="configuration-options"></a>Opcje konfiguracji

Zwykle istnieją trzy sposoby konfigurowania synchronizacji czasu dla maszyn wirtualnych z systemem Linux hostowanych na platformie Azure:

- Konfiguracja domyślna dla obrazów w portalu Azure Marketplace obejmuje zarówno czas NTP, jak i czas hosta VMICTimeSync. 
- Host-tylko przy użyciu VMICTimeSync.
- Użyj innego, zewnętrznego serwera czasu z lub bez użycia VMICTimeSync hosta.


### <a name="use-the-default"></a>Użyj domyślnego

Domyślnie większość obrazów w portalu Azure Marketplace dla systemu Linux jest skonfigurowanych do synchronizacji z dwóch źródeł: 

- NTP jako podstawowa, która pobiera czas z serwera NTP. Na przykład Ubuntu 16,04 LTS Marketplace używa **NTP.Ubuntu.com**.
- Usługa VMICTimeSync jako pomocnicza, używana do przekazywania informacji o czasie hosta do maszyn wirtualnych i wprowadzania poprawek po wstrzymaniu maszyny wirtualnej do konserwacji. Hosty platformy Azure wykorzystują urządzenia warstwy 1 należące do firmy Microsoft w celu zapewnienia dokładnego czasu.

W przypadku nowszych dystrybucji systemu Linux usługa VMICTimeSync udostępnia źródło zegara sprzętowego protokołu (PTP), ale wcześniejsze dystrybucje mogą nie zapewniać tego źródła zegara i przewracają do NTP w celu uzyskania czasu od hosta.

Aby potwierdzić, że NTP jest prawidłowo synchronizowana, uruchom `ntpq -p` polecenie.

### <a name="host-only"></a>Tylko Host 

Ponieważ serwery NTP, takie jak time.windows.com i ntp.ubuntu.com, są publiczne, synchronizacja czasu wymaga wysyłania ruchu przez Internet. Różne opóźnienia pakietów mogą negatywnie wpłynąć na jakość synchronizacji czasu. Usunięcie NTP przez przełączenie na synchronizację tylko hosta może czasami poprawić wyniki synchronizacji czasu.

Przełączenie na synchronizację w czasie tylko dla hosta ma sens tylko w przypadku problemów z synchronizacją czasu przy użyciu konfiguracji domyślnej. Wypróbuj synchronizację tylko z hostem, aby sprawdzić, czy poprawisz synchronizację czasu na maszynie wirtualnej. 

### <a name="external-time-server"></a>Zewnętrzny serwer czasu

Jeśli masz określone wymagania dotyczące synchronizacji czasu, istnieje również opcja użycia zewnętrznych serwerów czasu. Zewnętrzne serwery czasu mogą zapewnić określony czas, który może być przydatny dla scenariuszy testowych, zapewniając jednolitość czasu dla maszyn hostowanych w centrach danych firmy Microsoft lub obsługujących sekundy w specjalny sposób.

Można połączyć zewnętrzny serwer czasu z usługą VMICTimeSync, aby zapewnić wyniki podobne do konfiguracji domyślnej. Połączenie zewnętrznego serwera czasu z programem VMICTimeSync jest najlepszą opcją do rozwiązywania problemów, które mogą wystąpić w przypadku wstrzymania maszyn wirtualnych do konserwacji. 

## <a name="tools-and-resources"></a>Narzędzia i zasoby

Istnieją podstawowe polecenia służące do sprawdzania konfiguracji synchronizacji czasu. Dokumentacja dystrybucji systemu Linux będzie zawierać więcej szczegółowych informacji na temat najlepszego sposobu konfigurowania synchronizacji czasu dla tej dystrybucji.

### <a name="integration-services"></a>Usługi integracji

Sprawdź, czy usługa integracji (hv_utils) jest załadowana.

```bash
lsmod | grep hv_utils
```
Powinieneś wyglądać podobnie do tego:

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Sprawdź, czy demon usług integracji funkcji Hyper-V jest uruchomiony.

```bash
ps -ef | grep hv
```

Powinieneś wyglądać podobnie do tego:

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>Sprawdź Źródło zegara PTP

W przypadku nowszych wersji systemu Linux Źródło zegara protokołu czasu (PTP) jest dostępne jako część dostawcy VMICTimeSync. W starszych wersjach Red Hat Enterprise Linux lub CentOS 7. x [usługi integracji z systemem Linux](https://github.com/LIS/lis-next) można pobrać i użyć do zainstalowania zaktualizowanego sterownika. Gdy źródło zegara PTP jest dostępne, urządzenie z systemem Linux będzie miało postać/dev/PTP *x*. 

Zobacz, które źródła zegara PTP są dostępne.

```bash
ls /sys/class/ptp
```

W tym przykładzie zwracaną wartością jest *ptp0*, więc używamy jej do sprawdzania nazwy zegara. Aby sprawdzić urządzenie, Sprawdź nazwę zegara.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

Powinno to zwrócić `hyperv` .

### <a name="chrony"></a>chrony

W systemie Ubuntu 19,10 i nowszych wersjach Red Hat Enterprise Linux i CentOS 8. x [chrony](https://chrony.tuxfamily.org/) jest skonfigurowany do używania zegara źródłowego PTP. Zamiast chrony starsze wersje systemu Linux używają demona protokołu czasu sieciowego (ntpd), która nie obsługuje źródeł PTP. Aby włączyć PTP w tych wersjach, chrony musi być ręcznie zainstalowana i skonfigurowana (w chrony. conf) przy użyciu następującego kodu:

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Aby uzyskać więcej informacji na temat Ubuntu i NTP, zobacz [synchronizacja czasu](https://ubuntu.com/server/docs/network-ntp).

Aby uzyskać więcej informacji na temat Red Hat i NTP, zobacz [Konfigurowanie NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP). 

Aby uzyskać więcej informacji na temat chrony, zobacz [using chrony](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony).

Jeśli zarówno źródła chrony, jak i VMICTimeSync są włączone jednocześnie, można oznaczyć jeden jako **preferowany**, który ustawia inne źródło jako kopię zapasową. Ponieważ usługi NTP nie aktualizują zegara w przypadku dużych pochylenia, chyba że jest to długi czas, VMICTimeSync odzyska zegar od wstrzymanych zdarzeń maszyny wirtualnej znacznie szybciej niż w przypadku narzędzi opartych na protokole NTP.

Domyślnie program chronyd przyspiesza lub spowalnia zegar systemowy w celu naprawienia dowolnego przekroczenia czasu. Jeśli dryf zostanie zbyt duży, chrony nie uda się naprawić dryfu. Aby je przezwyciężyć, `makestep` parametr w **/etc/chrony.conf** można zmienić, aby wymusić synchronizację czasu, jeśli dryf przekroczy określony próg.

 ```bash
makestep 1.0 -1
```

W tym miejscu chrony wymusić aktualizację czasu, jeśli dryf jest większy niż 1 sekunda. Aby zastosować zmiany, uruchom ponownie usługę chronyd:

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>usługę systemd 

W wersjach SUSE i Ubuntu przed 19,10, synchronizacja czasu jest konfigurowana przy użyciu [systemu](https://www.freedesktop.org/wiki/Software/systemd/). Aby uzyskać więcej informacji na temat Ubuntu, zobacz [synchronizacja czasu](https://help.ubuntu.com/lts/serverguide/NTP.html). Aby uzyskać więcej informacji na temat SUSE, zobacz sekcję 4.5.8 (informacje o [wersji programu SUSE Linux Enterprise Server 12 SP3](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [dokładny czas dla systemu Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time).


