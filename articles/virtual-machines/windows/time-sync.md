---
title: Synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure
description: Synchronizacja czasu dla maszyn wirtualnych z systemem Windows.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: cynthn
ms.openlocfilehash: 85abc77757d31f3b1054a0670ea3f65a4fcb2e52
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555962"
---
# <a name="time-sync-for-windows-vms-in-azure"></a>Synchronizacja czasu dla maszyn wirtualnych z systemem Windows na platformie Azure

Synchronizacja czasu jest ważna dla korelacji zabezpieczeń i zdarzeń. Czasami jest używany dla implementacji transakcji rozproszonych. Dokładność czasu między wieloma systemami komputerowymi uzyskuje się przez synchronizację. Na synchronizację może wpływać wiele rzeczy, w tym ponowny rozruch i ruch sieciowy między źródłem czasu a komputerem, który pobiera czas. 

Platforma Azure jest teraz obsługiwana przez infrastrukturę z systemem Windows Server 2016. W systemie Windows Server 2016 udoskonalono algorytmy służące do korygowania czasu i warunku zegara lokalnego na synchronizację z czasem UTC.  W systemie Windows Server 2016 ulepszona również usługa VMICTimeSync, która reguluje sposób, w jaki maszyny wirtualne są synchronizowane z hostem w celu zapewnienia dokładnego czasu. Udoskonalenia obejmują dokładniejszy czas wstępnego uruchamiania maszyny wirtualnej lub przywracania maszyny wirtualnej i korekty opóźnienia przerwania dla przykładów dostarczonych do usługi czas systemu Windows (W32time). 


>[!NOTE]
>Aby zapoznać się z krótkim omówieniem usługi czas systemu Windows, zapoznaj się z tym [ogólnym omówieniem wideo](https://aka.ms/WS2016TimeVideo).
>
> Aby uzyskać więcej informacji, zobacz [dokładny czas dla systemu Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time). 

## <a name="overview"></a>Omówienie

Dokładność zegara komputerowego jest określana na tym, jak zamyka się zegar komputera do standardowego czasu koordynowanego (UTC). Czas UTC jest definiowany przez wielonarodowy przykład precyzyjne zegara niepodzielne, które mogą być wyłączone tylko przez jedną sekundę w latach 300. Jednak odczytywanie czasu UTC bezpośrednio wymaga specjalnego sprzętu. Zamiast tego serwery czasu są synchronizowane z czasem UTC i są dostępne z innych komputerów w celu zapewnienia skalowalności i niezawodności. Każdy komputer ma uruchomioną usługę synchronizacji czasu, która wie, jakie serwery czasu użyć i okresowo sprawdzają, czy zegar komputera musi zostać skorygowany i w razie potrzeby dostosowuje czas. 

Hosty platformy Azure są synchronizowane z wewnętrznymi serwerami Microsoft Time, które pobierają czas od urządzeń z warstwy 1 należącej do firmy Microsoft przy użyciu anten GPS. Maszyny wirtualne na platformie Azure mogą być zależne od ich hosta w celu przekazania dokładnego czasu (*czasu hosta*) do maszyny wirtualnej lub maszyny wirtualnej mogą bezpośrednio uzyskać czas z serwera czasu lub kombinację obu tych metod. 

Interakcje maszyny wirtualnej z hostem mogą również mieć wpływ na zegar. Podczas [zachowywania konserwacji pamięci](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)maszyny wirtualne są wstrzymywane przez maksymalnie 30 sekund. Na przykład przed rozpoczęciem obsługi zegar maszyny wirtualnej pokazuje 10:00:00 AM i trwa 28 sekund. Po wznowieniu działania maszyny wirtualnej zegar na maszynie wirtualnej nadal będzie wyświetlał 10:00:00 AM, co spowodowałoby 28 sekund. Aby rozwiązać ten problem, usługa VMICTimeSync monitoruje co dzieje się na hoście i pojawia się w celu wyrównania zmian w maszynach wirtualnych.

Usługa VMICTimeSync działa w trybie próbkowania lub synchronizacji i ma wpływ tylko na zegar do przodu. W trybie próbkowania, który wymaga uruchomienia usługi W32Time, usługa VMICTimeSync sonduje hosta co 5 sekund i udostępnia przykłady czasu do W32time. Co około 30 sekund usługa W32Time pobiera najnowszy czas i używa go w celu wywierania wpływu na zegar gościa. Tryb synchronizacji jest uaktywniany w przypadku wznowienia działania gościa lub przekroczenia zegara przez Gościa więcej niż 5 sekund za zegarem hosta. W przypadkach, gdy usługa W32Time jest prawidłowo uruchomiona, ten ostatni przypadek nigdy nie powinien wystąpić.

Bez pracy synchronizacji czasu zegar na maszynie wirtualnej będzie zbierać błędy. Jeśli istnieje tylko jedna maszyna wirtualna, efekt może nie być znaczący, chyba że obciążenie wymaga wysoce dokładnego dokładnych. Jednak w większości przypadków korzystamy z wielu połączonych z nimi maszyn wirtualnych, które używają czasu do śledzenia transakcji, a czas musi być spójny w całym wdrożeniu. Gdy czas między maszynami wirtualnymi jest inny, można zobaczyć następujące efekty:

- Uwierzytelnianie nie powiedzie się. Protokoły zabezpieczeń, takie jak Kerberos lub technologia zależna od certyfikatu, są zależne od systemu. 
- Bardzo trudno jest ustalić, co się stało w systemie, Jeśli dzienniki (lub inne dane) nie zgadzają się na czas. To samo zdarzenie będzie wyglądać tak, jakby miało miejsce w różnym czasie, co utrudnia korelację.
- Jeśli zegar jest wyłączony, rozliczenie może zostać obliczone nieprawidłowo.

Najlepsze wyniki wdrożeń systemu Windows są realizowane przy użyciu systemu Windows Server 2016 jako systemu operacyjnego gościa, co zapewnia możliwość korzystania z najnowszych ulepszeń synchronizacji czasu.

## <a name="configuration-options"></a>Opcje konfiguracji

Istnieją trzy opcje konfigurowania synchronizacji czasu dla maszyn wirtualnych z systemem Windows hostowanych na platformie Azure:

- Czas hosta i time.windows.com. Jest to domyślna konfiguracja używana w obrazach portalu Azure Marketplace.
- Tylko host.
- Użyj innego, zewnętrznego serwera czasu z lub bez użycia czasu hosta.


### <a name="use-the-default"></a>Użyj domyślnego

Domyślnie obrazy maszyn wirtualnych systemu Windows dla usługi W32Time są skonfigurowane pod kątem synchronizacji z dwóch źródeł: 

- Dostawca NtpClient, który pobiera informacje z time.windows.com.
- Usługa VMICTimeSync, używana do przekazywania informacji o czasie hosta do maszyn wirtualnych i wprowadzania poprawek po wstrzymaniu maszyny wirtualnej do konserwacji. Hosty platformy Azure wykorzystują urządzenia warstwy 1 należące do firmy Microsoft w celu zapewnienia dokładnego czasu.

Usługa W32Time preferuje dostawcę czasu w następującej kolejności: poziom warstwy, opóźnienie główne, Dyspersja główna, przesunięcie czasu. W większości przypadków usługa W32Time na maszynie wirtualnej platformy Azure preferuje czas hosta z powodu oceny, co pozwoli na porównanie obu źródeł czasu. 

W przypadku komputerów przyłączonych do domeny, sama sama domena ustala hierarchię synchronizacji czasu, ale katalog główny lasu nadal musi zająć trochę czasu, a poniższe uwagi nadal byłyby prawdziwe.


### <a name="host-only"></a>Tylko Host 

Ponieważ time.windows.com jest publicznym serwerem NTP, synchronizacja czasu wymaga wysyłania ruchu przez Internet, jednak różne opóźnienia pakietów mogą negatywnie wpłynąć na jakość synchronizacji czasu. Usunięcie time.windows.com przez przełączenie na synchronizację tylko dla hosta może czasami poprawić wyniki synchronizacji czasu.

Przełączenie na synchronizację w czasie tylko dla hosta ma sens tylko w przypadku problemów z synchronizacją czasu przy użyciu konfiguracji domyślnej. Wypróbuj synchronizację tylko dla hosta, aby zobaczyć, czy poprawi czas synchronizacji na maszynie wirtualnej. 

### <a name="external-time-server"></a>Zewnętrzny serwer czasu

Jeśli masz określone wymagania dotyczące synchronizacji czasu, istnieje również opcja użycia zewnętrznych serwerów czasu. Zewnętrzne serwery czasu mogą zapewnić określony czas, który może być przydatny dla scenariuszy testowych, zapewniając jednolitość czasu dla maszyn hostowanych w centrach danych firmy Microsoft lub obsługujących sekundy w specjalny sposób.

Serwery zewnętrzne można połączyć z usługą VMICTimeSync i VMICTimeProvider, aby zapewnić wyniki podobne do konfiguracji domyślnej. 

## <a name="check-your-configuration"></a>Sprawdź konfigurację


Sprawdź, czy dostawca czasu NtpClient jest skonfigurowany do używania jawnych serwerów NTP (NTP) lub synchronizacji czasu domeny (NT5DS).

```
w32tm /dumpreg /subkey:Parameters | findstr /i "type"
```

Jeśli maszyna wirtualna korzysta z NTP, zostaną wyświetlone następujące dane wyjściowe:

```
Value Name                 Value Type          Value Data
Type                       REG_SZ              NTP
```

Aby sprawdzić, z jakim serwerem czasu korzysta dostawca czasu NtpClient, w wierszu polecenia z podwyższonym poziomem uprawnień wpisz:

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

Jeśli maszyna wirtualna używa domyślnego, dane wyjściowe będą wyglądać następująco:

```
NtpServer                  REG_SZ              time.windows.com,0x8
```


, Aby zobaczyć, jaki jest aktualnie używany dostawca czasu.

```
w32tm /query /source
```


Poniżej przedstawiono dane wyjściowe, które można zobaczyć, i znaczenie:
    
- **Time.Windows.com** — w konfiguracji domyślnej usługa W32Time uzyska czas od Time.Windows.com. Jakość synchronizacji jest zależna od łączności z Internetem i ma wpływ na opóźnienia pakietów. Są to zwykłe dane wyjściowe, które można uzyskać na komputerze fizycznym.
- **Dostawca synchronizacji czasu z maszyną wirtualną**  — maszyna wirtualna jest synchronizowana z hostem. Są to zwykłe dane wyjściowe, które można uzyskać na maszynie wirtualnej działającej na platformie Azure. 
- *Serwer domeny* — bieżąca maszyna znajduje się w domenie, a domena definiuje hierarchię synchronizacji czasu.
- Inny *serwer* — W32Time został jawnie skonfigurowany do uzyskiwania czasu z tego innego serwera. Jakość synchronizacji czasu zależy od tej jakości serwera czasu.
- **Zegar lokalnego CMOS** -Clock nie jest zsynchronizowany. Możesz uzyskać te dane wyjściowe, jeśli usługa W32Time nie miała wystarczająco dużo czasu do uruchomienia po ponownym uruchomieniu lub gdy wszystkie skonfigurowane źródła czasu są niedostępne.


## <a name="opt-in-for-host-only-time-sync"></a>Zgoda na synchronizację w czasie tylko dla hosta

Platforma Azure nieustannie pracuje przy ulepszaniu synchronizacji czasu na hostach i może zagwarantować, że cała infrastruktura synchronizacji czasu zostanie przymieszczona w centrach danych firmy Microsoft. W przypadku problemów z synchronizacją czasową z domyślną konfiguracją, która preferuje użycie time.windows.com jako podstawowego źródła czasu, można użyć następujących poleceń, aby zadecydować o synchronizacji czasu tylko na hoście.

Oznacz dostawcę VMIC jako włączony. 

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\VMICTimeProvider /v Enabled /t REG_DWORD /d 1 /f
```

Oznacz dostawcę NTPClient jako wyłączony.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\TimeProviders\NtpClient /v Enabled /t REG_DWORD /d 0 /f
```

Uruchom ponownie usługę w32time.

```
net stop w32time && net start w32time
```


## <a name="windows-server-2012-and-r2-vms"></a>Maszyny wirtualne z systemem Windows Server 2012 i R2 

Systemy Windows Server 2012 i Windows Server 2012 R2 mają różne ustawienia domyślne dla synchronizacji czasu. Usługa W32time jest domyślnie konfigurowana w taki sposób, że preferuje niskie obciążenie usługi przez przekroczenie dokładnego czasu. 

Jeśli chcesz przenieść wdrożenia systemu Windows Server 2012 i 2012 R2, aby używać nowszych ustawień domyślnych, które preferują precyzyjne godziny, możesz zastosować poniższe ustawienia.

Zaktualizuj Interwały sondowania i aktualizacji W32Time, aby odpowiadały ustawieniom systemu Windows Server 2016.

```
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MinPollInterval /t REG_DWORD /d 6 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v MaxPollInterval /t REG_DWORD /d 10 /f
reg add HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\w32time\Config /v UpdateInterval /t REG_DWORD /d 100 /f
w32tm /config /update
```

Aby usługa W32Time mogła korzystać z nowych interwałów sondowania, NtpServers musi zostać oznaczona jako z użyciem. Jeśli serwery są opatrzone adnotacją bitflag, która spowodowałaby przesłonięcie tego mechanizmu, a usługa W32Time będzie używać SpecialPollInterval zamiast tego. Upewnij się, że określone serwery NTP używają flagi 0x8 lub bez flagi w ogóle:

Sprawdź, które flagi są używane dla używanych serwerów NTP.

```
w32tm /dumpreg /subkey:Parameters | findstr /i "ntpserver"
```

## <a name="next-steps"></a>Następne kroki

Poniżej znajdują się linki do dodatkowych informacji na temat synchronizacji czasu:

- [Narzędzia usługi Czas systemu Windows i ustawienia](/windows-server/networking/windows-time-service/windows-time-service-tools-and-settings)
- [Udoskonalenia systemu Windows Server 2016 ](/windows-server/networking/windows-time-service/windows-server-2016-improvements)
- [Dokładny czas dla systemu Windows Server 2016](/windows-server/networking/windows-time-service/accurate-time)
- [Obsługa granic w celu skonfigurowania usługi czas systemu Windows dla środowisk o wysokiej dokładności](/windows-server/networking/windows-time-service/support-boundary)
