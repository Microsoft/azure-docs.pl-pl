---
title: Wprowadzenie do rozwiązywania problemów z zasobami
titleSuffix: Azure Network Watcher
description: Ta strona zawiera przegląd możliwości rozwiązywania problemów dotyczących zasobów Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0d0597c2df8731171505a090de6959d8a112c004
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98569984"
---
# <a name="introduction-to-resource-troubleshooting-in-azure-network-watcher"></a>Wprowadzenie do rozwiązywania problemów z zasobami w usłudze Azure Network Watcher

Bramy Virtual Network zapewniają łączność między zasobami lokalnymi i innymi sieciami wirtualnymi w ramach platformy Azure. Monitorowania bram i ich połączeń jest niezbędne do zapewnienia nieprzerwanej komunikacji. Network Watcher oferuje możliwość rozwiązywania problemów z bramami i połączeniami. Tę możliwość można wywołać za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST. Gdy jest wywoływana, Network Watcher diagnozuje kondycję bramy lub połączenie i zwraca odpowiednie wyniki. Żądanie jest długotrwałą transakcją. Wyniki są zwracane po zakończeniu diagnostyki.

![Zrzut ekranu przedstawia diagnostykę Network Watcher V P N.][2]

## <a name="results"></a>Wyniki

Zwrócone wyniki wstępne zawierają ogólny obraz kondycji zasobu. Dokładniejsze informacje można dostarczyć dla zasobów, jak pokazano w poniższej sekcji:

Poniższa lista zawiera wartości zwracane z interfejsem API rozwiązywania problemów:

* **StartTime** — ta wartość jest czasem uruchomienia wywołania interfejsu API rozwiązywania problemów.
* **Endtime** — wartość czasu zakończenia rozwiązywania problemów.
* **kod** — ta wartość jest w złej kondycji, jeśli występuje pojedynczy błąd diagnostyki.
* **wyniki** — wyniki to kolekcja wyników zwróconych w ramach połączenia lub bramy sieci wirtualnej.
    * **ID** — ta wartość jest typem błędu.
    * **Podsumowanie** — ta wartość jest podsumowaniem błędu.
    * **szczegóły** — ta wartość zawiera szczegółowy opis błędu.
    * **recommendedActions** — ta właściwość jest kolekcją zalecanych akcji do wykonania.
      * **actionText** — ta wartość zawiera tekst opisujący czynności, które należy wykonać.
      * **actionUri** — ta wartość zapewnia identyfikator URI dokumentacji dotyczącej sposobu działania.
      * **actionUriText** — ta wartość jest krótkim opisem tekstu akcji.

W poniższych tabelach przedstawiono różne typy błędów (identyfikator w obszarze wyniki z poprzedniej listy), które są dostępne, a jeśli błąd tworzy dzienniki.

### <a name="gateway"></a>Brama

| Typ błędu | Przyczyna | Dziennik|
|---|---|---|
| NoFault (brak błędów) | Gdy nie wykryto błędu |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można znaleźć bramy lub Brama nie została zainicjowana |Nie|
| PlannedMaintenance (planowana konserwacja) |  Wystąpienie bramy jest w trakcie konserwacji  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Ten błąd występuje, gdy trwa aktualizacja użytkownika. Aktualizacja może być operacją zmiany rozmiaru. | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Ten błąd występuje, gdy nie można skontaktować się z podstawowym wystąpieniem bramy z powodu błędu sondowania kondycji. | Nie |
| PlatformInActive (nieaktywna platforma) | Wystąpił problem z platformą. | Nie|
| ServiceNotRunning | Podstawowa usługa nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | Brak połączeń w bramie. Ten błąd jest tylko ostrzeżeniem.| Nie|
| ConnectionsNotConnected | Połączenia nie są połączone. Ten błąd jest tylko ostrzeżeniem.| Tak|
| GatewayCPUUsageExceeded | Bieżące użycie procesora CPU bramy jest > 95%. | Tak |

### <a name="connection"></a>Połączenie

| Typ błędu | Przyczyna | Dziennik|
|---|---|---|
| NoFault (brak błędów) | Gdy nie wykryto błędu |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można znaleźć bramy lub Brama nie została zainicjowana |Nie|
| PlannedMaintenance (planowana konserwacja) | Wystąpienie bramy jest w trakcie konserwacji  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Ten błąd występuje, gdy trwa aktualizacja użytkownika. Aktualizacja może być operacją zmiany rozmiaru.  | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Ten błąd występuje, gdy nie można skontaktować się z podstawowym wystąpieniem bramy z powodu błędu sondowania kondycji. | Nie |
| ConnectionEntityNotFound | Brak konfiguracji połączenia | Nie |
| ConnectionIsMarkedDisconnected | Połączenie jest oznaczone jako "rozłączone" |Nie|
| ConnectionNotConfiguredOnGateway | Usługa bazowa nie ma skonfigurowanego połączenia. | Tak |
| ConnectionMarkedStandby | Podstawowa usługa jest oznaczona jako w stanie wstrzymania.| Tak|
| Authentication | Niezgodność klucza wstępnego | Tak|
| PeerReachability | Brama równorzędna jest nieosiągalna. | Tak|
| IkePolicyMismatch | Brama równorzędna ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Tak|
| Błąd WfpParse | Wystąpił błąd podczas analizowania dziennika WFP. |Tak|

## <a name="supported-gateway-types"></a>Obsługiwane typy bram

W poniższej tabeli wymieniono bramy i połączenia obsługiwane przez Network Watcher rozwiązywania problemów:

| Brama lub połączenie | Obsługiwane  |
|---------|---------|
|**Typy bram**   |         |
|VPN      | Obsługiwane        |
|ExpressRoute | Nieobsługiwane |
|**Typy sieci VPN** | |
|Oparta na trasach | Obsługiwane|
|Oparte na zasadach | Nieobsługiwane|
|**Typy połączeń**||
|IPsec| Obsługiwane|
|VNet2Vnet| Obsługiwane|
|ExpressRoute| Nieobsługiwane|
|VPNClient| Nieobsługiwane|

## <a name="log-files"></a>Pliki dziennika

Pliki dziennika rozwiązywania problemów z zasobami są przechowywane na koncie magazynu po zakończeniu rozwiązywania problemów z zasobami. Na poniższej ilustracji przedstawiono przykładową zawartość wywołania, które spowodowało błąd.

![plik zip][1]

> [!NOTE]
> W niektórych przypadkach tylko podzestaw plików dzienników jest zapisywana w magazynie.

Aby uzyskać instrukcje dotyczące pobierania plików z kont usługi Azure Storage, zobacz Rozpoczynanie [pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md). Inne narzędzie, które może być używane, jest Eksplorator usługi Storage. Więcej informacji na temat Eksplorator usługi Storage można znaleźć tutaj przy użyciu następującego linku: [Eksplorator usługi Storage](https://storageexplorer.com/)

### <a name="connectionstatstxt"></a>ConnectionStats.txt

Plik **ConnectionStats.txt** zawiera ogólne statystyki połączenia, w tym bajty przychodzące i wychodzące, stan połączenia oraz czas nawiązywania połączenia.

> [!NOTE]
> Jeśli wywołanie interfejsu API rozwiązywania problemów zwróci dobrą kondycję, jedyną czynnością zwróconą w pliku zip jest plik **ConnectionStats.txt** .

Zawartość tego pliku jest podobna do poniższego przykładu:

```
Connectivity State : Connected
Remote Tunnel Endpoint :
Ingress Bytes (since last connected) : 288 B
Egress Bytes (Since last connected) : 288 B
Connected Since : 2/1/2017 8:22:06 PM
```

### <a name="cpustatstxt"></a>CPUStats.txt

Plik **CPUStats.txt** zawiera użycie procesora CPU i pamięci dostępne w czasie testowania.  Zawartość tego pliku jest podobna do poniższego przykładu:

```
Current CPU Usage : 0 % Current Memory Available : 641 MBs
```

### <a name="ikeerrorstxt"></a>IKEErrors.txt

Plik **IKEErrors.txt** zawiera wszystkie błędy IKE, które zostały znalezione podczas monitorowania.

Poniższy przykład pokazuje zawartość pliku IKEErrors.txt. Błędy mogą się różnić w zależności od problemu.

```
Error: Authentication failed. Check shared key. Check crypto. Check lifetimes. 
     based on log : Peer failed with Windows error 13801(ERROR_IPSEC_IKE_AUTH_FAIL)
Error: On-prem device sent invalid payload. 
     based on log : IkeFindPayloadInPacket failed with Windows error 13843(ERROR_IPSEC_IKE_INVALID_PAYLOAD)
```

### <a name="scrubbed-wfpdiagtxt"></a>Scrubbed-wfpdiag.txt

Plik dziennika **Scrubbed-wfpdiag.txt** zawiera dziennik WFP. Ten dziennik zawiera rejestrowanie błędów porzucenia pakietów i IKE/AuthIP.

Poniższy przykład pokazuje zawartość pliku Scrubbed-wfpdiag.txt. W tym przykładzie klucz współużytkowany połączenia jest niepoprawny, ponieważ może być widoczny z trzeciego wiersza od dołu. Poniższy przykład jest tylko fragmentem całego dziennika, ponieważ dziennik może być długi w zależności od problemu.

```
...
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Deleted ICookie from the high priority thread pool list
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|IKE diagnostic event:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Event Header:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Timestamp: 1601-01-01T00:00:00.000Z
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Flags: 0x00000106
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Local address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    Remote address field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IP version field set
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP version: IPv4
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  IP protocol: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local address: 13.78.238.92
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote address: 52.161.24.36
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Local Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Remote Port: 0
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Application ID:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  User SID: <invalid>
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Failure type: IKE/Authip Main Mode Failure
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|Type specific info:
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure error code:0x000035e9
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|    IKE authentication credentials are unacceptable
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|
[0]0368.03A4::02/02/2017-17:36:01.496 [ikeext] 3038|52.161.24.36|  Failure point: Remote
...
```

### <a name="wfpdiagtxtsum"></a>wfpdiag.txt. sum

Plik **wfpdiag.txt. sum** jest dziennikiem przedstawiającym przetworzone bufory i zdarzenia.

Poniższy przykład to zawartość pliku wfpdiag.txt. sum.
```
Files Processed:
    C:\Resources\directory\924336c47dd045d5a246c349b8ae57f2.GatewayTenantWorker.DiagnosticsStorage\2017-02-02T17-34-23\wfpdiag.etl
Total Buffers Processed 8
Total Events  Processed 2169
Total Events  Lost      0
Total Format  Errors    0
Total Formats Unknown   486
Elapsed Time            330 sec
+-----------------------------------------------------------------------------------+
|EventCount    EventName            EventType   TMF                                 |
+-----------------------------------------------------------------------------------+
|        36    ikeext               ike_addr_utils_c844  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        12    ikeext               ike_addr_utils_c857  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|        96    ikeext               ike_addr_utils_c832  a0c064ca-d954-350a-8b2f-1a7464eef8b6|
|         6    ikeext               ike_bfe_callbacks_c133  1dc2d67f-8381-6303-e314-6c1452eeb529|
|         6    ikeext               ike_bfe_callbacks_c61  1dc2d67f-8381-6303-e314-6c1452eeb529|
|        12    ikeext               ike_sa_management_c5698  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c8447  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c494  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c642  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|         6    ikeext               ike_sa_management_c3162  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
|        12    ikeext               ike_sa_management_c3307  7857a320-42ee-6e90-d5d9-3f414e3ea2d3|
```

## <a name="considerations"></a>Zagadnienia do rozważenia 
* Można uruchamiać tylko jedną operację rozwiązywania problemów na subskrypcję. Aby uruchomić kolejną operację rozwiązywania problemów, poczekaj na ukończenie poprzedniej. Wyzwól więcej operacji, podczas gdy poprzedni nie zostanie zakończony, spowoduje to, że kolejne operacje kończą się niepowodzeniem. 
* Usterka interfejsu wiersza polecenia: Jeśli korzystasz z interfejsu CLI platformy Azure, aby uruchomić polecenie, VPN Gateway i konto magazynu musi znajdować się w tej samej grupie zasobów. Klienci z zasobami w różnych grupach zasobów mogą korzystać z programu PowerShell lub Azure Portal zamiast tego.  


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak zdiagnozować problem z bramą lub połączeniem bramy, zobacz [diagnozowanie problemów z komunikacją między sieciami](diagnose-communication-problem-between-networks.md).
<!--Image references-->

[1]: ./media/network-watcher-troubleshoot-overview/GatewayTenantWorkerLogs.png
[2]: ./media/network-watcher-troubleshoot-overview/portal.png
