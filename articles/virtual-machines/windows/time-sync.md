---
title: Synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure
description: Synchronizacja czasu dla maszyn wirtualnych z systemem Windows.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 33c8d58877a73c1ea3055fe52ef8470d8775b6e3
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458032"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure

Synchronizacja czasu jest ważne dla zabezpieczeń i korelacji zdarzeń. Czasami jest on używany do implementacji transakcji rozproszonych. Dokładność czasu między wieloma systemami komputerowymi uzyskuje się poprzez synchronizację. Synchronizacja może mieć wpływ na wiele rzeczy, w tym ponowne uruchomienie i ruch sieciowy między źródłem czasu a komputerem pobierającym czas. 

Platforma Azure jest teraz wspierana przez infrastrukturę z systemem Windows Server 2016. System Windows Server 2016 udoskonalił algorytmy używane do korygowania czasu i kondycjonowania zegara lokalnego w celu synchronizacji z czasem UTC.  System Windows Server 2016 udoskonalił również usługę VMICTimeSync, która reguluje sposób synchronizacji maszyn wirtualnych z hostem w celu uzyskania dokładnego czasu. Ulepszenia obejmują dokładniejszy czas początkowy na początku maszyny Wirtualnej lub korekcji przywracania i przerwania dla próbek dostarczonych do czasu systemu Windows (W32time). 


>[!NOTE]
>Aby uzyskać szybki przegląd usługi Czas systemu Windows, zapoznaj się z tym [klipem wideo przedstawiającym omówienie wysokiego poziomu](https://aka.ms/WS2016TimeVideo).
>
> Aby uzyskać więcej informacji, zobacz [Dokładny czas dla systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Omówienie

Dokładność zegara komputerowego jest mierzona na podstawie tego, jak blisko zegara komputera jest standard czasu skoordynowanego czasu uniwersalnego (UTC). UTC jest definiowany przez wielonarodową próbkę precyzyjnych zegarów atomowych, które mogą być wyłączone tylko o jedną sekundę w ciągu 300 lat. Ale czytanie UTC bezpośrednio wymaga specjalistycznego sprzętu. Zamiast tego serwery czasu są synchronizowane z czasem UTC i są dostępne z innych komputerów, aby zapewnić skalowalność i niezawodność. Każdy komputer ma uruchomione usługi synchronizacji czasu, która wie, jakich serwerów czasu do użycia i okresowo sprawdza, czy zegar komputera musi zostać poprawiony i dostosowuje czas w razie potrzeby. 

Hosty platformy Azure są synchronizowane z wewnętrznymi serwerami czasu firmy Microsoft, które poświęcają swój czas z urządzeń Stratum 1 należących do firmy Microsoft z antenami GPS. Maszyny wirtualne na platformie Azure może zależeć od ich hosta, aby przekazać dokładny czas *(czas hosta)* na maszynie wirtualnej lub maszyny Wirtualnej można bezpośrednio uzyskać czas z serwera czasu lub kombinacji obu. 

Interakcje maszyny wirtualnej z hostem mogą również wpływać na zegar. Podczas [konserwacji pamięci](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)maszyny wirtualne są wstrzymane na maksymalnie 30 sekund. Na przykład przed rozpoczęciem konserwacji zegar maszyny Wirtualnej pokazuje 10:00:00 i trwa 28 sekund. Po wznowieniu maszyny Wirtualnej zegar na maszynie Wirtualnej będzie nadal wyświetlane 10:00:00 AM, który będzie 28 sekund wyłączony. Aby to poprawić, usługa VMICTimeSync monitoruje to, co dzieje się na hoście i monituje o wprowadzenie zmian na maszynach wirtualnych w celu zrekompensowania.

Usługa VMICTimeSync działa w trybie próbkowania lub synchronizacji i będzie miała wpływ tylko na zegar do przodu. W trybie próbki, który wymaga W32time do uruchomienia, usługa VMICTimeSync sonduje hosta co 5 sekund i zapewnia próbki czasu do W32time. Około co 30 sekund usługa W32time pobiera najnowszą próbkę czasu i używa jej do wpływania na zegar gościa. Tryb synchronizacji aktywuje się, jeśli gość został wznowiony lub jeśli zegar gościa dryfuje więcej niż 5 sekund za zegarem hosta. W przypadkach, gdy usługa W32time jest prawidłowo uruchomiona, ten ostatni przypadek nigdy nie powinien mieć miejsca.

Bez synchronizacji czasu pracy zegar na maszynie Wirtualnej będzie gromadzić błędy. Gdy istnieje tylko jedna maszyna wirtualna, efekt może nie być znaczący, chyba że obciążenie wymaga bardzo dokładnego pomiaru czasu. Ale w większości przypadków mamy wiele połączonych ze sobą maszyn wirtualnych, które używają czasu do śledzenia transakcji, a czas musi być spójny przez całe wdrożenie. Gdy czas między maszynami wirtualnymi jest inny, można zobaczyć następujące efekty:

- Uwierzytelnianie zakończy się niepowodzeniem. Protokoły zabezpieczeń, takie jak Kerberos lub technologia zależna od certyfikatu, opierają się na spójnym czasie w różnych systemach. 
- Bardzo trudno jest dowiedzieć się, co się stało w systemie, jeśli dzienniki (lub inne dane) nie zgadzają się na czas. To samo zdarzenie wyglądałoby tak, jakby miało miejsce w różnym czasie, co utrudniało korelację.
- Jeśli zegar jest wyłączony, rozliczenia mogą być obliczane niepoprawnie.

Najlepsze wyniki dla wdrożeń systemu Windows uzyskuje się przy użyciu systemu Windows Server 2016 jako systemu operacyjnego gościa, co zapewnia, że można użyć najnowszych ulepszeń w synchronizacji czasu.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją trzy opcje konfigurowania synchronizacji czasu dla maszyn wirtualnych z systemem Windows hostowanych na platformie Azure:

- Czas hosta i time.windows.com. Jest to domyślna konfiguracja używana w obrazach portalu Azure Marketplace.
- Tylko host.
- Użyj innego zewnętrznego serwera czasu z czasem hosta lub bez niego.


### <a name="use-the-default"></a>Użyj wartości domyślnej

Domyślnie obrazy maszyn wirtualnych systemu Windows są skonfigurowane do synchronizacji w32time z dwóch źródeł: 

- Dostawca NtpClient, który pobiera informacje od time.windows.com.
- Usługa VMICTimeSync, używana do komunikowania czasu hosta z maszynami wirtualnymi i wprowadzania poprawek po wstrzymaniu maszyny Wirtualnej w celu konserwacji. Hosty platformy Azure używają urządzeń Stratum 1 należących do firmy Microsoft, aby zachować dokładny czas.

w32time wolałby dostawca czasu w następującej kolejności priorytetu: poziom warstwy, opóźnienie korzenia, dyspersja korzenia, przesunięcie czasu. W większości przypadków w32time wolałby time.windows.com do hosta, ponieważ time.windows.com raporty niższe warstwy. 

W przypadku maszyn przyłączonych do domeny sama domena ustanawia hierarchię synchronizacji czasu, ale katalog główny lasu nadal musi zająć trochę czasu, a następujące zagadnienia nadal będą miały wartość true.


### <a name="host-only"></a>Tylko host 

Ponieważ time.windows.com jest publicznym serwerem NTP, synchronizacja z nim wymaga wysyłania ruchu przez Internet, różne opóźnienia pakietów mogą negatywnie wpłynąć na jakość synchronizacji czasu. Usunięcie time.windows.com przez przejście na synchronizację tylko z hostem może czasami poprawić wyniki synchronizacji czasu.

Przełączanie do synchronizacji czasu tylko do hosta ma sens, jeśli występują problemy z synchronizacją czasu przy użyciu konfiguracji domyślnej. Wypróbuj synchronizację tylko z hostem, aby sprawdzić, czy poprawiłoby to synchronizację czasu na maszynie Wirtualnej. 

### <a name="external-time-server"></a>Zewnętrzny serwer czasu

Jeśli masz określone wymagania dotyczące synchronizacji czasu, istnieje również możliwość korzystania z zewnętrznych serwerów czasu. Zewnętrzne serwery czasu mogą zapewnić określony czas, który może być przydatny w scenariuszach testowych, zapewniając jednorodność czasu z maszynami hostowanymi w centrach danych innych firm lub obsługując sekundy przestępne w specjalny sposób.

Serwery zewnętrzne można łączyć z usługą VMICTimeSync i programem VMICTimeProvider, aby zapewnić wyniki podobne do konfiguracji domyślnej. 

## <a name="check-your-configuration"></a>Sprawdź konfigurację


Sprawdź, czy dostawca czasu NtpClient jest skonfigurowany do używania jawnych serwerów NTP (NTP) lub synchronizacji czasu domeny (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Jeśli maszyna wirtualna korzysta z ntp, zobaczysz następujące dane wyjściowe:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Aby zobaczyć, jakiego serwera czasu używa dostawca czasu NtpClient, przy podwyższonym poziomie sterującym typu wiersza polecenia:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Jeśli maszyna wirtualna używa wartości domyślnej, dane wyjściowe będą wyglądać następująco:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


Aby zobaczyć, jaki dostawca czasu jest obecnie używany.

```
w32tm /query /source
```


Oto wyjście można zobaczyć i co to znaczy:
    
- **time.windows.com** - w domyślnej konfiguracji, w32time dostanie czas od time.windows.com. Jakość synchronizacji czasu zależy od połączenia z Internetem i zależy od opóźnień pakietów. Jest to zwykłe dane wyjściowe z domyślnej konfiguracji.
- **Dostawca synchronizacji czasu ic maszyny Wirtualnej** — maszyna wirtualna synchronizuje czas z hosta. Zwykle jest to wynik, jeśli zdecydujesz się na synchronizację czasu tylko do hosta lub NtpServer nie jest obecnie dostępny. 
- *Serwer domeny* — bieżący komputer znajduje się w domenie, a domena definiuje hierarchię synchronizacji czasu.
- *Jakiś inny serwer* - w32time został jawnie skonfigurowany, aby uzyskać czas z tego innego serwera. Jakość synchronizacji czasu zależy od tej jakości serwera czasu.
- **Lokalny zegar CMOS** - zegar jest niezsynchronizowane. Możesz uzyskać ten wynik, jeśli w32time nie ma wystarczająco dużo czasu, aby rozpocząć po ponownym uruchomieniu lub gdy wszystkie skonfigurowane źródła czasu nie są dostępne.


## <a name="opt-in-for-host-only-time-sync"></a>Zgodę na synchronizację czasu tylko do hosta

Platforma Azure stale pracuje nad poprawą synchronizacji czasu na hostach i może zagwarantować, że infrastruktura synchronizacji przez cały czas jest kolokowana w centrach danych należących do firmy Microsoft. Jeśli masz problemy z synchronizacją czasu z domyślną konfiguracją, która woli używać time.windows.com jako głównego źródła czasu, możesz użyć następujących poleceń, aby wyrazić zgodę na synchronizację czasu tylko do hosta.

Oznacz dostawcę VMIC jako włączonego. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Oznacz dostawcę NTPClient jako wyłączonego.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Uruchom ponownie usługę w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Maszyny wirtualne z systemami Windows Server 2012 i R2 

Systemy Windows Server 2012 i Windows Server 2012 R2 mają różne ustawienia domyślne synchronizacji czasu. W32time domyślnie jest skonfigurowany w sposób, który preferuje niskie obciążenie usługi na dokładny czas. 

Jeśli chcesz przenieść wdrożenia systemów Windows Server 2012 i 2012 R2, aby użyć nowszych ustawień domyślnych, które preferują dokładny czas, możesz zastosować następujące ustawienia.

Zaktualizuj interwały sondowania i aktualizacji w32time, aby były zgodne z ustawieniami systemu Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Aby w32time mógł korzystać z nowych interwałów sondowania, NtpServers być oznaczone jako używające ich. Jeśli serwery są oznaczone maską 0x1 bitflag, to zastąpić ten mechanizm i w32time będzie używać SpecialPollInterval zamiast. Upewnij się, że określone serwery NTP używają flagi 0x8 lub w ogóle nie mają flagi:

Sprawdź, jakie flagi są używane dla używanych serwerów NTP.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się linki do więcej szczegółów na temat synchronizacji czasu:

- [Narzędzia usługi Czas systemu Windows i ustawienia](https://docs.microsoft.com/windows-server/networking/windows-time-service/Windows-Time-Service-Tools-and-Settings)
- [Ulepszenia systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Dokładny czas dla systemu Windows Server 2016](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)
- [Obsługa granicy konfigurowania usługi Czas systemu Windows dla środowisk o wysokiej dokładności](https://docs.microsoft.com/windows-server/networking/windows-time-service/support-boundary)


