---
title: Diagnozowanie połączeń lokalnych za pośrednictwem bramy sieci VPN
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób diagnozowania łączności lokalnej za pośrednictwem bramy sieci VPN przy użyciu usługi Azure Network Watcher Resource Troubleshooting.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3b8b53446799eec3473d63c89672393a35f9787a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670959"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnozowanie połączeń lokalnych za pośrednictwem bram sieci VPN

Usługa Azure VPN Gateway umożliwia tworzenie hybrydowego rozwiązania, które pozwala sprostać potrzebom bezpiecznego połączenia między siecią lokalną i siecią wirtualną platformy Azure. Ponieważ wymagania są unikatowe, jest to wybór lokalnego urządzenia sieci VPN. Platforma Azure obsługuje obecnie [kilka urządzeń sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) , które są stale weryfikowane w ramach partnerstwa z dostawcami urządzeń. Przed skonfigurowaniem lokalnego urządzenia sieci VPN przejrzyj ustawienia konfiguracji specyficzne dla urządzenia. Podobnie usługa Azure VPN Gateway jest skonfigurowana z zestawem [obsługiwanych parametrów protokołu IPSec](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) , które są używane do ustanawiania połączeń. Obecnie nie istnieje sposób na określenie lub wybranie określonej kombinacji parametrów protokołu IPsec z usługi Azure VPN Gateway. Aby można było nawiązać połączenie między środowiskiem lokalnym i platformą Azure, ustawienia lokalnego urządzenia sieci VPN muszą być zgodne z parametrami protokołu IPsec, które są wymagane przez usługę Azure VPN Gateway. Jeśli ustawienia są nieprawidłowe, nastąpi utrata łączności i do momentu rozwiązania tych problemów nie było to proste i zazwyczaj zajęło to godz.

Dzięki funkcji rozwiązywania problemów z usługą Azure Network Watcher można zdiagnozować wszelkie problemy z bramą i połączeniami, a w ciągu kilku minut uzyskać świadomą decyzję o rozwiązaniu problemu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenariusz

Chcesz skonfigurować połączenie lokacja-lokacja między platformą Azure i lokalnymi przy użyciu FortiGate jako VPN Gateway lokalnych. Aby zrealizować ten scenariusz, należy potrzebować następującej konfiguracji:

1. Virtual Network Gateway — VPN Gateway na platformie Azure
1. Brama sieci lokalnej — reprezentacja [VPN Gateway lokalnego (Fortigate)](../vpn-gateway/tutorial-site-to-site-portal.md#LocalNetworkGateway) w chmurze platformy Azure
1. Połączenie lokacja-lokacja (oparte na trasach) — [połączenie między VPN Gateway i routerem lokalnym](../vpn-gateway/tutorial-site-to-site-portal.md#CreateConnection)
1. [Konfigurowanie FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Szczegółowe wskazówki krok po kroku dotyczące konfigurowania konfiguracji lokacja-lokacja można znaleźć przez odwiedzenie: [Tworzenie sieci wirtualnej z połączeniem lokacja-lokacja przy użyciu Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md).

Jedną z krytycznych czynności konfiguracyjnych jest skonfigurowanie parametrów komunikacji IPsec. Każda niepoważna konfiguracja prowadzi do utraty łączności między siecią lokalną a platformą Azure. Obecnie bramy sieci VPN platformy Azure są skonfigurowane do obsługi następujących parametrów protokołu IPsec dla fazy 1. Jak widać w poniższej tabeli, algorytmy szyfrowania obsługiwane przez platformę Azure VPN Gateway to AES256, AES128 i 3DES.

### <a name="ike-phase-1-setup"></a>Konfiguracja usługi IKE fazy 1

| **Właściwość** | **PolicyBased** | **Brama sieci VPN RouteBased i standardowa lub High-Performance** |
| --- | --- | --- |
| Wersja IKE |IKEv1 |IKEv2 |
| Grupa Diffie’ego-Hellmana |Grupa 2 (1024 bity) |Grupa 2 (1024 bity) |
| Metoda uwierzytelniania |Klucz wstępny |Klucz wstępny |
| Algorytmy szyfrowania |AES256 AES128 3DES |AES256 3DES |
| Algorytm skrótu |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Okres istnienia (czas) skojarzenia zabezpieczeń (Security Association — SA) fazy 1 |28 800 sekund |28 800 sekund |

Jako użytkownik musisz skonfigurować FortiGate, ale przykładową konfigurację można znaleźć w witrynie [GitHub](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Nieświadomie skonfigurowano FortiGate do używania algorytmu SHA-512 jako algorytmem wyznaczania wartości skrótu. Ponieważ ten algorytm nie jest obsługiwanym algorytmem dla połączeń opartych na zasadach, połączenie sieci VPN działa.

Te problemy trudno rozwiązać problemy, a główne przyczyny często nie są intuicyjne. W takim przypadku można otworzyć bilet pomocy technicznej, aby uzyskać pomoc dotyczącą rozwiązania problemu. Korzystając z interfejsu API rozwiązywania problemów z usługą Azure Network Watcher, możesz samodzielnie identyfikować te problemy.

## <a name="troubleshooting-using-azure-network-watcher"></a>Rozwiązywanie problemów przy użyciu usługi Azure Network Watcher

Aby zdiagnozować połączenie, Połącz się z Azure PowerShell i zainicjuj `Start-AzNetworkWatcherResourceTroubleshooting` polecenie cmdlet. Szczegóły dotyczące korzystania z tego polecenia cmdlet znajdują się w tematach [Rozwiązywanie problemów Virtual Network brama i połączenia — PowerShell](network-watcher-troubleshoot-manage-powershell.md). Wykonanie tego polecenia cmdlet może potrwać kilka minut.

Po zakończeniu działania polecenia cmdlet możesz przejść do lokalizacji magazynu określonej w poleceniu cmdlet, aby uzyskać szczegółowe informacje na temat problemu i dzienników. Usługa Azure Network Watcher tworzy folder zip zawierający następujące pliki dziennika:

![1][1]

Otwórz plik o nazwie IKEErrors.txt i wyświetla następujący błąd, wskazując problem z błędną konfiguracją ustawienia lokalnego protokołu IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Aby uzyskać szczegółowe Scrubbed-wfpdiag.txt informacje o błędzie, jak w tym przypadku, oznacza to, że `ERROR_IPSEC_IKE_POLICY_MATCH` Klient nie działa prawidłowo.

Inną częstą błędną konfiguracją jest określenie nieprawidłowych kluczy współużytkowanych. Jeśli w powyższym przykładzie określono różne klucze wspólne, IKEErrors.txt pokazuje następujący błąd: `Error: Authentication failed. Check shared key` .

Funkcja rozwiązywania problemów z usługą Azure Network Watcher umożliwia diagnozowanie i rozwiązywanie problemów z VPN Gateway i połączeniem przy użyciu prostego polecenia cmdlet programu PowerShell. Obecnie obsługujemy diagnozowanie następujących warunków i pracujemy nad dodaniem większej ilości warunków.

### <a name="gateway"></a>Brama

| Typ błędu | Przyczyna | Dziennik|
|---|---|---|
| NoFault (brak błędów) | Nie wykryto żadnego błędu. |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można znaleźć bramy lub Brama nie jest obsługiwana. |Nie|
| PlannedMaintenance (planowana konserwacja) |  Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Gdy aktualizacja użytkownika jest w toku. Może to być operacja zmiany rozmiaru. | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Nie można nawiązać połączenia z podstawowym wystąpieniem bramy. Dzieje się tak, gdy sonda kondycji zakończy się niepowodzeniem. | Nie |
| PlatformInActive (nieaktywna platforma) | Wystąpił problem z platformą. | Nie|
| ServiceNotRunning | Podstawowa usługa nie jest uruchomiona. | Nie|
| NoConnectionsFoundForGateway | Brak połączeń w bramie. Jest to tylko ostrzeżenie.| Nie|
| ConnectionsNotConnected | Żadne z połączeń nie są połączone. Jest to tylko ostrzeżenie.| Tak|
| GatewayCPUUsageExceeded | Bieżące użycie procesora CPU przez bramę to > 95%. | Tak |

### <a name="connection"></a>Połączenie

| Typ błędu | Przyczyna | Dziennik|
|---|---|---|
| NoFault (brak błędów) | Nie wykryto żadnego błędu. |Tak|
| GatewayNotFound (nie znaleziono bramy) | Nie można znaleźć bramy lub Brama nie jest obsługiwana. |Nie|
| PlannedMaintenance (planowana konserwacja) | Wystąpienie bramy jest w trakcie konserwacji.  |Nie|
| UserDrivenUpdate (aktualizacja sterowana przez użytkownika) | Gdy aktualizacja użytkownika jest w toku. Może to być operacja zmiany rozmiaru.  | Nie |
| VipUnResponsive (wirtualny adres IP nie odpowiada) | Nie można nawiązać połączenia z podstawowym wystąpieniem bramy. Występuje po niepowodzeniu sondy kondycji. | Nie |
| ConnectionEntityNotFound | Brak konfiguracji połączenia. | Nie |
| ConnectionIsMarkedDisconnected | Połączenie jest oznaczone jako "odłączone". |Nie|
| ConnectionNotConfiguredOnGateway | Usługa bazowa nie ma skonfigurowanego połączenia. | Tak |
| ConnectionMarkedStandby | Podstawowa usługa jest oznaczona jako w stanie wstrzymania.| Tak|
| Authentication | Niezgodność klucza wstępnego. | Tak|
| PeerReachability | Brama równorzędna jest nieosiągalna. | Tak|
| IkePolicyMismatch | Brama równorzędna ma zasady IKE, które nie są obsługiwane przez platformę Azure. | Tak|
| Błąd WfpParse | Wystąpił błąd podczas analizowania dziennika WFP. |Tak|

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak sprawdzać VPN Gateway łączności z programem PowerShell i Azure Automation, odwiedzając [monitorowanie bram sieci VPN za pomocą usługi Azure Network Watcher Rozwiązywanie problemów](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png