---
title: Rozwiązywanie problemów z usługą Azure VPN Gateway przy użyciu dzienników diagnostycznych
description: Rozwiązywanie problemów z usługą Azure VPN Gateway przy użyciu dzienników diagnostycznych
services: vpn-gateway
author: stegag
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/15/2021
ms.author: stegag
ms.openlocfilehash: 232e084e44696c6aa88a9dd33092c48a96e35f85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772007"
---
# <a name="troubleshoot-azure-vpn-gateway-using-diagnostic-logs"></a>Rozwiązywanie problemów z usługą Azure VPN Gateway przy użyciu dzienników diagnostycznych

W tym artykule opisano różne dzienniki dostępne dla diagnostyki VPN Gateway oraz sposób ich użycia w celu efektywnego rozwiązywania problemów z bramą sieci VPN.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Następujące dzienniki są dostępne na platformie Azure:

|***Nazwa** _ | _ *_Opis_** |
|---        | ---               |
|**GatewayDiagnosticLog** | Zawiera dzienniki diagnostyczne dla zdarzeń konfiguracji bramy, podstawowych zmian i zdarzeń konserwacji. |
|**TunnelDiagnosticLog** | Zawiera zdarzenia zmiany stanu tunelu. Zdarzenia łączenia/rozłączania tunelu zawierają podsumowanie przyczyny zmiany stanu, jeśli ma to zastosowanie. |
|**RouteDiagnosticLog** | Rejestruje zmiany tras statycznych i zdarzeń BGP, które występują na bramie. |
|**IKEDiagnosticLog** | Rejestruje komunikaty i zdarzenia kontroli IKE w bramie. |
|**P2SDiagnosticLog** | Rejestruje komunikaty i zdarzenia kontroli punkt-lokacja w bramie. |

Zwróć uwagę na to, że w tych tabelach jest dostępnych kilka kolumn. W tym artykule prezentujemy tylko najbardziej odpowiednie osoby, aby ułatwić użycie dzienników.

## <a name="set-up-logging"></a><a name="setup"></a>Konfigurowanie rejestrowania

Aby dowiedzieć się, jak skonfigurować zdarzenia dzienników diagnostycznych z usługi Azure VPN Gateway przy użyciu usługi Azure Log Analytics, zobacz [Konfigurowanie alertów dotyczących zdarzeń dzienników diagnostycznych z VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log).

## <a name="gatewaydiagnosticlog"></a><a name="GatewayDiagnosticLog"></a>GatewayDiagnosticLog

Zmiany konfiguracji są poddawane inspekcji w tabeli **GatewayDiagnosticLog** . Wprowadzenie zmian w dziennikach może potrwać kilka minut.

Oto przykładowe zapytanie jako odwołanie.

```
AzureDiagnostics  
| where Category == "GatewayDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup  
| sort by TimeGenerated asc
```

To zapytanie w **GatewayDiagnosticLog** będzie zawierać wiele kolumn.

|***Nazwa** _ | _ *_Opis_** |
|---        | ---               |
|**TimeGenerated** | Sygnatura czasowa każdego zdarzenia w strefie czasowej UTC.|
|**OperationName** |zdarzenie, które wystąpiło. Może to być jedna z *SetGatewayConfiguration, SetConnectionConfiguration, HostMaintenanceEvent, GatewayTenantPrimaryChanged, MigrateCustomerSubscription, GatewayResourceMove, ValidateGatewayConfiguration*.|
|**Wiadomość** | Szczegóły wykonywanej operacji oraz listę wyników zakończonych powodzeniem lub niepowodzeniem.|

W poniższym przykładzie pokazano działanie zarejestrowane w przypadku zastosowania nowej konfiguracji:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-26-set-gateway.png" alt-text="Przykład operacji set Gateway zaobserwowanej w GatewayDiagnosticLog.":::


Należy zauważyć, że SetGatewayConfiguration będzie rejestrowane za każdym razem, gdy konfiguracja zostanie zmodyfikowana zarówno w VPN Gateway, jak i w bramie sieci lokalnej.
Krzyżowe odwołujące się do wyników z tabeli **GatewayDiagnosticLog** z tabelami **TunnelDiagnosticLog** mogą pomóc w ustaleniu, czy awaria połączenia tunelu została uruchomiona w tym samym czasie, gdy konfiguracja została zmieniona, lub w trakcie konserwacji. Jeśli tak, mamy doskonały wskaźnik do możliwej przyczyny głównej.

## <a name="tunneldiagnosticlog"></a><a name="TunnelDiagnosticLog"></a>TunnelDiagnosticLog

Tabela **TunnelDiagnosticLog** jest bardzo przydatna do sprawdzania historycznych Stanów łączności tunelu.

Oto przykładowe zapytanie jako odwołanie.

```
AzureDiagnostics
| where Category == "TunnelDiagnosticLog"
| project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup
| sort by TimeGenerated asc
```

To zapytanie w **TunnelDiagnosticLog** będzie zawierać wiele kolumn.


|***Nazwa** _ | _ *_Opis_** |
|---        | ---               |
|**TimeGenerated** | Sygnatura czasowa każdego zdarzenia w strefie czasowej UTC.|
|**OperationName** | zdarzenie, które wystąpiło. Może to być *TunnelConnected* lub *TunnelDisconnected*.|
| **Wystąpienie \_ s** | wystąpienie roli bramy, które wyzwoliło zdarzenie. Może być to GatewayTenantWorker \_ w \_ 0 lub GatewayTenantWorker \_ w \_ 1, które są nazwami dwóch wystąpień bramy.|
| **Zasób** | wskazuje nazwę bramy sieci VPN. |
| **ResourceGroup** | wskazuje grupę zasobów, w której znajduje się brama.|


Przykładowe dane wyjściowe:

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-16-tunnel-connected.png" alt-text="Przykład zdarzenia połączonego z tunelowaniem w TunnelDiagnosticLog.":::


**TunnelDiagnosticLog** jest bardzo przydatne do rozwiązywania problemów z poprzednimi zdarzeniami dotyczącymi nieoczekiwanych odniesień sieci VPN. Jego lekki charakter oferuje możliwość przeanalizowania dużych przedziałów czasu w ciągu kilku dni z małym nakładem pracy.
Dopiero po zidentyfikowaniu sygnatury czasowej odłączenia można przełączyć się na bardziej szczegółową analizę tabeli **IKEdiagnosticLog** , aby Dig bardziej szczegółowy opis odniesień, które są związane z protokołem IPSec.


Wskazówki dotyczące rozwiązywania problemów:
- Jeśli zobaczysz zdarzenie rozłączenia w jednym wystąpieniu bramy, a następnie zdarzenie połączenia w **innym** wystąpieniu bramy w ciągu kilku sekund, zobaczysz w trybie failover bramy. Zwykle jest to oczekiwane zachowanie spowodowane konserwacją w wystąpieniu bramy. Aby dowiedzieć się więcej na temat tego zachowania, zobacz [Informacje o nadmiarowości bramy sieci VPN platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable#about-azure-vpn-gateway-redundancy).
- Takie samo zachowanie zostanie zaobserwowane, jeśli celowo uruchomisz Reset bramy po stronie platformy Azure, co spowoduje ponowne uruchomienie wystąpienia aktywnej bramy. Aby dowiedzieć się więcej na temat tego zachowania, zobacz [resetowanie VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-resetgw-classic).
- Jeśli zostanie wyświetlone zdarzenie rozłączenia w jednym wystąpieniu bramy, a następnie zdarzenie połączenia w **tym samym** wystąpieniu bramy w ciągu kilku sekund, może się okazać, że wystąpi błąd sieci powodujący limit czasu DPD lub odłączenie wysyłane błędnie przez urządzenie lokalne.

## <a name="routediagnosticlog"></a><a name="RouteDiagnosticLog"></a>RouteDiagnosticLog

Tabela **RouteDiagnosticLog** śledzi aktywność dla statycznie zmodyfikowanych tras lub tras odebranych za pośrednictwem protokołu BGP.

Oto przykładowe zapytanie jako odwołanie.

```
AzureDiagnostics
| where Category == "RouteDiagnosticLog"
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

To zapytanie w **RouteDiagnosticLog** będzie zawierać wiele kolumn.

|***Nazwa** _ | _ *_Opis_** |
|---        | ---               |
|**TimeGenerated** | Sygnatura czasowa każdego zdarzenia w strefie czasowej UTC.|
|**OperationName** | zdarzenie, które wystąpiło. Może to być *StaticRouteUpdate, BgpRouteUpdate, BgpConnectedEvent, BgpDisconnectedEvent*.|
| **Wiadomość** | Szczegóły wykonywanej operacji.|

Dane wyjściowe będą zawierać przydatne informacje na temat elementów równorzędnych protokołu BGP połączonych/rozłączonych i wymienianych tras.

Przykład:


:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-31-bgp-route.png" alt-text="Przykład działania wymiany tras BGP widoczny w RouteDiagnosticLog.":::


## <a name="ikediagnosticlog"></a><a name="IKEDiagnosticLog"></a>IKEDiagnosticLog

Tabela **IKEDiagnosticLog** zawiera pełne rejestrowanie debugowania dla protokołu IKE/IPSec. Jest to bardzo przydatne w przypadku rozwiązywania problemów z odłączaniem lub niemożności połączenia sieci VPN.

Oto przykładowe zapytanie jako odwołanie.

```
AzureDiagnostics  
| where Category == "IKEDiagnosticLog" 
| extend Message1=Message
| parse Message with * "Remote " RemoteIP ":" * "500: Local " LocalIP ":" * "500: " Message2
| extend Event = iif(Message has "SESSION_ID",Message2,Message1)
| project TimeGenerated, RemoteIP, LocalIP, Event, Level 
| sort by TimeGenerated asc
```

To zapytanie w **IKEDiagnosticLog** będzie zawierać wiele kolumn.


|***Nazwa** _ | _ *_Opis_** |
|---        | ---               |
|**TimeGenerated** | Sygnatura czasowa każdego zdarzenia w strefie czasowej UTC.|
| **RemoteIP** | adres IP lokalnego urządzenia sieci VPN. W rzeczywistych scenariuszach warto odfiltrować według adresu IP odpowiedniego urządzenia lokalnego, które zawiera więcej niż jeden. |
|**LocalIP** | adres IP VPN Gateway rozwiązywania problemów. W rzeczywistych scenariuszach warto odfiltrować według adresu IP odpowiedniej bramy sieci VPN w ramach subskrypcji. |
|**Zdarzenie** | zawiera komunikat diagnostyczny przydatny do rozwiązywania problemów. Zwykle zaczynają się od słowa kluczowego i zapoznaj się z akcjami wykonywanymi przez bramę platformy Azure: **\[ send \]** wskazuje zdarzenie spowodowane przez pakiet protokołu IPSec wysyłany przez bramę platformy Azure.  **\[ Odebrano \]** wskazuje zdarzenie w sekwencji w ramach pakietu otrzymanego z urządzenia lokalnego.  Wartość **\[ lokalna \]** wskazuje akcję wykonywaną lokalnie przez bramę platformy Azure. |


Zwróć uwagę, jak kolumny RemoteIP, LocalIP i Event nie znajdują się na liście oryginalnych kolumn w bazie danych AzureDiagnostics, ale są dodawane do zapytania przez analizowanie danych wyjściowych kolumny "Message" w celu uproszczenia jej analizy.

Wskazówki dotyczące rozwiązywania problemów:

- Aby zidentyfikować początek negocjacji protokołu IPSec, należy znaleźć początkowy \_ komunikat inicjowania skojarzenia zabezpieczeń. Taki komunikat może być wysyłany po obu stronach tunelu. Każdy z nich wysyła pierwszy pakiet nazywany "inicjatorem" w terminologii protokołu IPsec, podczas gdy druga strona stanie się "obiektem odpowiadającym". Pierwszy \_ komunikat init skojarzenia zabezpieczeń jest zawsze taki, gdzie rCookie = 0.

- W przypadku niepowodzenia ustanowienia tunelu IPsec platforma Azure będzie ponawiać próbę co kilka sekund. Z tego powodu problemy związane z rozwiązywaniem problemów z siecią VPN są bardzo wygodne w IKEdiagnosticLog, ponieważ nie trzeba czekać na określony czas, aby odtworzyć problem. Ponadto niepowodzenie będzie teoretycznie zawsze takie samo przy każdej próbie, więc w dowolnym momencie można po prostu powiększyć do jednej nieprzerwanej negocjacji.

- Inicjowanie skojarzenia zabezpieczeń \_ zawiera parametry protokołu IPSec, które mają być używane przez element równorzędny dla tej negocjacji protokołu IPSec. Oficjalny dokument   
[Domyślne parametry protokołu IPSec/IKE](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices#ipsec) wyświetlają parametry protokołu IPSec obsługiwane przez bramę platformy Azure z ustawieniami domyślnymi.


## <a name="p2sdiagnosticlog"></a><a name="P2SDiagnosticLog"></a>P2SDiagnosticLog

Ostatnia dostępna tabela dla diagnostyki sieci VPN to **P2SDiagnosticLog**. Ta tabela śledzi aktywność punktu do lokacji.

Oto przykładowe zapytanie jako odwołanie.

```
AzureDiagnostics  
| where Category == "P2SDiagnosticLog"  
| project TimeGenerated, OperationName, Message, Resource, ResourceGroup
```

To zapytanie w **P2SDiagnosticLog** będzie zawierać wiele kolumn.

|***Nazwa** _ | _ *_Opis_** |
|---        | ---               |
|**TimeGenerated** | Sygnatura czasowa każdego zdarzenia w strefie czasowej UTC.|
|**OperationName** | zdarzenie, które wystąpiło. Zostanie *P2SLogEvent*.|
| **Wiadomość** | Szczegóły wykonywanej operacji.|

W danych wyjściowych zostaną wyświetlone wszystkie ustawienia lokacji, które zostały zastosowane przez bramę, a także zasady protokołu IPsec.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-28-p2s-log-event.png" alt-text="Przykład punktu do połączenia z lokacją widziany w P2SDiagnosticLog.":::

Ponadto, za każdym razem, gdy klient nawiąże połączenie za pośrednictwem protokołu IKEv2 lub OpenVPN punkt z lokacją, w tabeli będzie można rejestrować aktywność pakietów, konwersacje EAP/RADIUS i udane/niepowodzenie.

:::image type="content" source="./media/troubleshoot-vpn-with-azure-diagnostics/image-29-eap.png" alt-text="Przykład uwierzytelniania EAP widziany w P2SDiagnosticLog.":::

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować alerty dotyczące dzienników zasobów tunelu, zobacz [Konfigurowanie alertów dotyczących dzienników zasobów VPN Gateway](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md).

