---
title: Narzędzie diagnostyczne do rozwiązywania problemów z urządzeniem z StorSimple 8000 | Microsoft Docs
description: Ten artykuł zawiera omówienie narzędzia diagnostycznego oraz opis sposobu użycia narzędzia z urządzeniem StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: bf0570cd62a2c329407cfb6dd087020f53cdc991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94954073"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Rozwiązywanie problemów z urządzeniami z serii 8000 przy użyciu narzędzia diagnostyki StorSimple

## <a name="overview"></a>Omówienie

Narzędzie diagnostyki StorSimple diagnozuje problemy związane z systemem, wydajnością, siecią i kondycją składnika sprzętowego dla urządzenia StorSimple. Narzędzia diagnostycznego można użyć w różnych scenariuszach. Te scenariusze obejmują planowanie obciążeń, wdrażanie urządzenia StorSimple, ocenianie środowiska sieciowego i Określanie wydajności urządzenia operacyjnego. Ten artykuł zawiera omówienie narzędzia diagnostycznego oraz opis sposobu użycia narzędzia z urządzeniem StorSimple.

Narzędzie diagnostyczne jest przeznaczone głównie dla urządzeń lokalnych z serii StorSimple 8000 (8100 i 8600).

## <a name="run-diagnostics-tool"></a>Uruchom narzędzie diagnostyczne

To narzędzie można uruchomić za pomocą interfejsu programu Windows PowerShell urządzenia StorSimple. Istnieją dwa sposoby uzyskania dostępu do lokalnego interfejsu urządzenia:

* [Korzystanie z programu w celu nawiązania połączenia z konsolą szeregową urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Uzyskaj zdalny dostęp do narzędzia za pośrednictwem program Windows PowerShell dla usługi StorSimple](storsimple-8000-remote-connect.md).

W tym artykule przyjęto założenie, że nawiązano połączenie z konsolą szeregową urządzenia za pośrednictwem wypełnień.

#### <a name="to-run-the-diagnostics-tool"></a>Aby uruchomić narzędzie diagnostyczne

Po nawiązaniu połączenia z interfejsem programu Windows PowerShell na urządzeniu wykonaj następujące kroki, aby uruchomić polecenie cmdlet.
1. Zaloguj się do konsoli szeregowej urządzenia, wykonując kroki opisane w temacie [Korzystanie z konsoli szeregowej urządzenia](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Wpisz następujące polecenie:

    `Invoke-HcsDiagnostics`

    Jeśli parametr Scope nie zostanie określony, polecenie cmdlet wykonuje wszystkie testy diagnostyczne. Te testy obejmują system, kondycję składnika sprzętowego, Sieć i wydajność. 
    
    Aby uruchomić tylko określony test, określ parametr Scope. Na przykład aby uruchomić tylko test sieci, wpisz

    `Invoke-HcsDiagnostics -Scope Network`

3. Wybierz i skopiuj dane wyjściowe z okna podano do pliku tekstowego do dalszej analizy.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Scenariusze korzystania z narzędzia diagnostycznego

Narzędzia diagnostyczne służą do rozwiązywania problemów z siecią, wydajnością, systemem i kondycją sprzętu systemu. Oto kilka możliwych scenariuszy:

* **Urządzenie w trybie offline** — urządzenie z serii StorSimple 8000 jest w trybie offline. Jednak w interfejsie programu Windows PowerShell wygląda na to, że oba kontrolery są uruchomione.
    * Możesz użyć tego narzędzia, aby określić stan sieci.
         
         > [!NOTE]
         > Nie używaj tego narzędzia do oceny wydajności i ustawień sieciowych na urządzeniu przed rejestracją (lub konfiguracją za pomocą Kreatora instalacji). Prawidłowy adres IP jest przypisywany do urządzenia podczas wykonywania przez Kreatora instalacji i rejestracji. To polecenie cmdlet można uruchomić na urządzeniu, które nie jest zarejestrowane na potrzeby kondycji sprzętowej i systemu. Użyj parametru scope, na przykład:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Trwałe problemy z urządzeniami** — występują problemy z urządzeniem. Na przykład Rejestracja kończy się niepowodzeniem. Można również napotkać problemy z urządzeniem po pomyślnym zarejestrowaniu urządzenia i zakończeniu jego działania.
    * W takim przypadku należy użyć tego narzędzia do wstępnego rozwiązywania problemów przed zarejestrowaniem żądania obsługi w pomoc techniczna firmy Microsoft. Zalecamy uruchomienie tego narzędzia i przechwycenie danych wyjściowych tego narzędzia. Następnie możesz podać te dane wyjściowe, aby obsługiwać rozwiązywanie problemów.
    * Jeśli wystąpią jakieś awarie składnika sprzętowego lub klastra, należy zalogować się do Support request.

* **Niska wydajność urządzenia** — Urządzenie StorSimple działa wolno.
    * W takim przypadku należy uruchomić to polecenie cmdlet z parametrem Scope ustawionym na wydajność. Analizuj dane wyjściowe. Uzyskuje się opóźnienia odczytu i zapisu w chmurze. Użycie zgłoszonych opóźnień jako maksymalnego osiągalnego celu, współczynnika w przypadku wewnętrznego przetwarzania danych, a następnie wdrożenie obciążeń w systemie. Aby uzyskać więcej informacji, przejdź do pozycji [Użyj testu sieci, aby rozwiązać problem z wydajnością urządzenia](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Test diagnostyczny i przykładowe dane wyjściowe

### <a name="hardware-test"></a>Test sprzętu

Ten test określa stan składników sprzętowych, oprogramowanie układowe USM oraz oprogramowanie układowe dysku uruchomione w systemie.

* Raportowane składniki sprzętowe to składniki, których test nie powiódł się lub nie znajdują się w systemie.
* W systemie zgłoszono oprogramowanie układowe USM i wersje oprogramowania układowego dla kontrolera 0, kontrolera 1 i współużytkowanych składników. Aby uzyskać pełną listę składników sprzętowych, przejdź do:

    * [Składniki w obudowie podstawowej](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Składniki w obudowie EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Jeśli test sprzętu zgłasza składniki zakończone niepowodzeniem, należy [zalogować żądanie obsługi za pomocą pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe testu sprzętowego na urządzeniu 8100

Oto przykładowe dane wyjściowe z urządzenia z systemem StorSimple 8100. W urządzeniu modelu 8100 nie ma obudowy EBOD. W związku z tym składniki kontrolera EBOD nie są zgłaszane.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Test systemu

Ten test zgłasza informacje o systemie, dostępne aktualizacje, informacje o klastrze i informacje o usłudze dla Twojego urządzenia.

* Informacje o systemie obejmują model, numer seryjny urządzenia, strefę czasową, stan kontrolera i szczegółowe wersje oprogramowania działające w systemie. Aby zrozumieć różne parametry systemu raportowane jako dane wyjściowe, przejdź do [interpretacji informacji o systemie](#appendix-interpreting-system-information).

* Dostępność aktualizacji umożliwia określenie, czy są dostępne tryby regularne i konserwacyjne oraz skojarzone z nimi nazwy pakietów. Jeśli `RegularUpdates` i `MaintenanceModeUpdates` są `false` , oznacza to, że aktualizacje są niedostępne. Urządzenie jest aktualne.
* Informacje o klastrze zawierają informacje dotyczące różnych składników logicznych wszystkich grup klastrów magazynu HCS i ich odpowiednich stanów. Jeśli w tej sekcji raportu zostanie wyświetlona Grupa klastra w trybie offline, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
* Informacje o usłudze obejmują nazwy i Stany wszystkich usług magazynu HCS i CiS działających na urządzeniu. Te informacje ułatwiają pomoc techniczna firmy Microsoft rozwiązywania problemów z urządzeniem.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe przebiegu testu systemu na urządzeniu 8100

Oto przykładowe dane wyjściowe przebiegu testu systemu na urządzeniu 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Test sieci

Ten test sprawdza poprawność stanu interfejsów sieciowych, portów, serwera DNS i usługi NTP, certyfikatu TLS/SSL, poświadczeń konta magazynu, łączności z serwerami aktualizacji i łączności serwera proxy sieci Web na urządzeniu StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Przykładowe dane wyjściowe testu sieci, gdy jest włączona tylko funkcja DATA0

Oto przykładowe dane wyjściowe urządzenia 8100. W danych wyjściowych można zobaczyć, że:
* Tylko interfejsy sieciowe DATA 0 i DATA 1 są włączone i skonfigurowane.
* DANE 2-5 nie są włączone w portalu.
* Konfiguracja serwera DNS jest prawidłowa i urządzenie może nawiązać połączenie za pośrednictwem serwera DNS.
* Łączność z serwerem NTP jest również dobra.
* Porty 80 i 443 są otwarte. Jednak port 9354 jest zablokowany. Na podstawie [wymagań sieci systemowej](./storsimple-8000-system-requirements.md)należy otworzyć ten port dla komunikacji z usługą Service Bus.
* Certyfikat TLS/SSL jest prawidłowy.
* Urządzenie może nawiązać połączenie z kontem magazynu: _myss8000storageacct_.
* Łączność z serwerami aktualizacji jest prawidłowa.
* Serwer proxy sieci Web nie jest skonfigurowany na tym urządzeniu.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Przykładowe dane wyjściowe testu sieci, gdy DATA0 i DANE1 są włączone

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Test wydajnościowy

Ten test zgłasza wydajność chmury za pośrednictwem opóźnień odczytu i zapisu w chmurze dla Twojego urządzenia. Tego narzędzia można użyć do ustalenia podstawy wydajności chmury, którą można osiągnąć za pomocą StorSimple. Narzędzie raportuje maksymalną wydajność (najlepszy scenariusz dla opóźnień do odczytu i zapisu), które można uzyskać dla danego połączenia.

Ponieważ narzędzie zgłasza maksymalną osiągalną wydajność, można użyć zgłaszanych opóźnień do odczytu i zapisu jako obiektów docelowych podczas wdrażania obciążeń.

Test symuluje rozmiary obiektów BLOB skojarzonych z różnymi typami woluminów na urządzeniu. Regularne i wielopoziomowe kopie zapasowe woluminów przypiętych lokalnie używają rozmiaru obiektu BLOB 64 KB. Dla woluminów warstwowych z opcją archiwum jest zaznaczone pole wyboru Użyj rozmiaru danych obiektów BLOB 512 KB. Jeśli na urządzeniu są skonfigurowane woluminy przypięte na poziomie warstwowym i lokalnie, jest uruchamiany tylko test, który odpowiada rozmiarowi danych obiektów BLOB 64 KB.

Aby użyć tego narzędzia, wykonaj następujące czynności:

1.  Najpierw należy utworzyć mieszany wolumin warstwowy i woluminy warstwowe z zaznaczoną opcją zarchiwizowane. Ta akcja zapewnia, że narzędzie uruchamia testy dla rozmiaru obiektów BLOB 64 KB i 512 KB.

2. Uruchom polecenie cmdlet po utworzeniu i skonfigurowaniu woluminów. Wpisz:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Zanotuj opóźnienia odczytu i zapisu zgłoszone przez narzędzie. Ten test może potrwać kilka minut, zanim zgłosi wyniki.

4. Jeśli czasy połączeń są wszystkie pod oczekiwanym zakresem, wówczas opóźnienia raportowane przez narzędzie mogą być używane jako maksymalne osiągalne miejsce docelowe podczas wdrażania obciążeń. Współczynnik w pewnej obciążeniu dla wewnętrznego przetwarzania danych.

    Jeśli czasy oczekiwania do odczytu i zapisu zgłoszone przez narzędzie diagnostyczne są wysokie:

    1. Skonfiguruj analityka magazynu dla usług BLOB Services i analizuj dane wyjściowe, aby zrozumieć opóźnienia dla konta usługi Azure Storage. Aby uzyskać szczegółowe instrukcje, przejdź do pozycji [Włączanie i konfigurowanie analityka magazynu](../storage/blobs/monitor-blob-storage.md). Jeśli te opóźnienia są również duże i porównywalne z liczbami otrzymanymi z narzędzia diagnostyki StorSimple, należy zarejestrować żądanie obsługi w usłudze Azure Storage.

    2. Jeśli opóźnienia konta magazynu są niskie, skontaktuj się z administratorem sieci, aby zbadać wszelkie problemy z opóźnieniami w sieci.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Przykładowe dane wyjściowe przebiegu testu wydajności na urządzeniu 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Dodatek: interpretowanie informacji o systemie

Poniżej przedstawiono tabelę zawierającą opis różnych parametrów programu Windows PowerShell na mapie informacji o systemie. 

| Parametr programu PowerShell    | Opis  |
|-------------------------|------------------|
| Instance ID             | Każdy kontroler ma unikatowy identyfikator lub skojarzony z nim identyfikator GUID.|
| Nazwa                    | Przyjazna nazwa urządzenia skonfigurowana za pomocą Azure Portal podczas wdrażania urządzenia. Domyślną przyjazną nazwą jest numer seryjny urządzenia. |
| Model                   | Model urządzenia z serii StorSimple 8000. Model może mieć wartość 8100 lub 8600.|
| SerialNumber            | Numer seryjny urządzenia jest przypisany do fabryki i ma 15 znaków. Na przykład 8600-SHX0991003G44HT wskazuje:<br> 8600 — jest modelem urządzenia.<br>SHX — to witryna produkcyjna.<br> 0991003 — jest określonym produktem. <br> G44HT — ostatnie 5 cyfr są zwiększane, aby utworzyć unikatowe numery seryjne. Nie może to być zestaw sekwencyjny.|
| TimeZone                | Strefa czasowa urządzenia zgodnie z konfiguracją w Azure Portal podczas wdrażania urządzenia.|
| CurrentController       | Kontroler, z którym nawiązano połączenie za pomocą interfejsu programu Windows PowerShell urządzenia StorSimple.|
| ActiveController        | Kontroler, który jest aktywny na urządzeniu i kontroluje wszystkie operacje sieciowe i dyskowe. Może to być kontroler 0 lub kontroler 1.  |
| Controller0Status       | Stan kontrolera 0 na urządzeniu. Stan kontrolera może być normalny, w trybie odzyskiwania lub nieosiągalny.|
| Controller1Status       | Stan kontrolera 1 na urządzeniu.  Stan kontrolera może być normalny, w trybie odzyskiwania lub nieosiągalny.|
| Systemmode              | Ogólny stan urządzenia StorSimple. Urządzenie może być normalne, konserwowane lub likwidowane (odpowiada zdezaktywowanemu w Azure Portal).|
| FriendlySoftwareVersion | Przyjazny ciąg, który odpowiada wersji oprogramowania urządzenia. W przypadku systemu z aktualizacją Update 4 przyjazna wersja oprogramowania byłaby StorSimple 8000 serii aktualizacji 4,0.|
| HcsSoftwareVersion      | Wersja oprogramowania magazynu HCS uruchomiona na urządzeniu. Na przykład magazynu HCS wersja oprogramowania odpowiadająca StorSimple 8000 serii aktualizacji 4,0 to 6.3.9600.17820. |
| ApiVersion              | Wersja oprogramowania interfejsu API programu Windows PowerShell urządzenia magazynu HCS.|
| VhdVersion              | Wersja oprogramowania obrazu fabrycznego, z którym urządzenie zostało wysłane. W przypadku zresetowania urządzenia do domyślnych ustawień fabrycznych ta wersja oprogramowania zostanie uruchomiona.|
| OSVersion               | Wersja oprogramowania systemu operacyjnego Windows Server działającego na urządzeniu. Urządzenie StorSimple jest oparte na systemie Windows Server 2012 R2, który odnosi się do 6.3.9600.|
| CisAgentVersion         | Wersja agenta usługi CIS działająca na urządzeniu StorSimple. Ten agent pomaga komunikować się z usługą StorSimple Manager działającą na platformie Azure.|
| MdsAgentVersion         | Wersja odpowiadająca agentowi usług MDS działającemu na urządzeniu StorSimple. Ten agent przenosi dane do usługi monitorowania i diagnostyki (MDS).|
| Lsisas2Version          | Wersja odpowiadająca sterownikom LSI na urządzeniu StorSimple.|
| Pojemność                | Całkowita pojemność urządzenia w bajtach.|
| RemoteManagementMode    | Wskazuje, czy urządzenie może być zarządzane zdalnie za pośrednictwem jego interfejsu programu Windows PowerShell. |
| Fipsmode                | Wskazuje, czy w urządzeniu jest włączony tryb Stany Zjednoczone Federal Information Processing Standard (FIPS). Standard FIPS 140 definiuje algorytmy kryptograficzne zatwierdzone do użycia przez Federalne systemy komputerowe dla instytucji rządowych USA do ochrony poufnych danych. W przypadku urządzeń z uruchomioną aktualizacją Update 4 lub nowszą tryb FIPS jest domyślnie włączony. |

## <a name="next-steps"></a>Następne kroki

* Poznaj [składnię polecenia cmdlet Invoke-HcsDiagnostics](/previous-versions/windows/powershell-scripting/mt795371(v=wps.630)).

* Dowiedz się więcej na temat [rozwiązywania problemów z wdrażaniem](./storsimple-8000-troubleshoot-deployment.md) na urządzeniu StorSimple.