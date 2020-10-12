---
title: 'Azure ExpressRoute: monitorowanie, metryki i alerty'
description: Dowiedz się więcej o monitorowaniu, metrykach i alertach usługi Azure ExpressRoute, korzystając z Azure Monitor, z którego korzysta ten punkt zatrzymania dla wszystkich metryk, alertów, dzienników diagnostycznych na platformie Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 08/25/2020
ms.author: duau
ms.openlocfilehash: 6f502b8ad8ac268cc937150f4effdf9edf8eef15
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91252633"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorowanie, metryki i alerty usługi ExpressRoute

Ten artykuł ułatwia zrozumienie ExpressRoute monitorowania, metryk i alertów przy użyciu Azure Monitor. Azure Monitor to jeden zatrzymywanie dla wszystkich metryk, alertów, dzienników diagnostycznych na całym systemie Azure.
 
>[!NOTE]
>Korzystanie z **metryk klasycznych** nie jest zalecane.
>

## <a name="expressroute-metrics"></a>Metryki ExpressRoute

Aby wyświetlić **metryki**, przejdź do strony *Azure monitor* i kliknij pozycję *metryki*. Aby wyświetlić metryki **ExpressRoute** , odfiltruj według typu zasobu *ExpressRoute obwody*. Aby wyświetlić metryki **Global REACH** , odfiltrować według typów zasobów *ExpressRoute obwody* i wybrać zasób obwodu usługi ExpressRoute, który ma Global REACH włączony. Aby wyświetlić metryki **ExpressRoute Direct** , należy filtrować typ zasobu według *portów ExpressRoute*. 

Po wybraniu metryki zostanie zastosowana domyślna agregacja. Opcjonalnie można zastosować podział, który będzie zawierać metrykę z różnymi wymiarami.

### <a name="available-metrics"></a>Dostępne metryki

|**Metryka**|**Kategoria**|**Wymiary**|**Funkcje:**|
| --- | --- | --- | --- |
|Dostępność protokołu ARP|Dostępność|<ui><li>Węzeł równorzędny (podstawowy/pomocniczy router ExpressRoute)</ui></li><ui><li> Typ komunikacji równorzędnej (prywatny/publiczny/Microsoft)</ui></li>|ExpressRoute|
|Dostępność protokołu BGP|Dostępność|<ui><li> Węzeł równorzędny (podstawowy/pomocniczy router ExpressRoute)</ui></li><ui><li> Typ komunikacji równorzędnej</ui></li>|ExpressRoute|
|BitsInPerSecond|Ruch|<ui><li> Typ komunikacji równorzędnej (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>Usługa ExpressRoute Direct|
|BitsOutPerSecond|Ruch| <ui><li>Typ komunikacji równorzędnej (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute bezpośrednie</ui></li> |
|Wykorzystanie procesora|Wydajność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|Liczba pakietów na sekundę|Wydajność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|GlobalReachBitsInPerSecond|Ruch|<ui><li>Skey obwodu równorzędnego (klucz usługi)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Ruch|<ui><li>Skey obwodu równorzędnego (klucz usługi)</ui></li>|Global Reach|
|AdminState|Łączność fizyczna|Łącze|Usługa ExpressRoute Direct|
|LineProtocol|Łączność fizyczna|Łącze|Usługa ExpressRoute Direct|
|RxLightLevel|Łączność fizyczna|<ui><li>Powiązań</ui></li><ui><li>Ścieżka</ui></li>|Usługa ExpressRoute Direct|
|TxLightLevel|Łączność fizyczna|<ui><li>Powiązań</ui></li><ui><li>Ścieżka</ui></li>|Usługa ExpressRoute Direct|
>[!NOTE]
>Użycie *GlobalGlobalReachBitsInPerSecond* i *GlobalGlobalReachBitsOutPerSecond* będzie widoczne tylko wtedy, gdy zostanie nawiązane co najmniej jedno połączenie Global REACH.
>

## <a name="circuits-metrics"></a>Metryki obwodów

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bity w i out-Metrics dla wszystkich komunikacji równorzędnych

Możesz wyświetlić metryki dla wszystkich komunikacji równorzędnych w danym obwodzie ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metryki obwodu":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity w i out — metryki na komunikację równorzędną

Metryki dla prywatnych, publicznych i komunikacji równorzędnej firmy Microsoft można wyświetlić w bitach na sekundę.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metryki obwodu":::

### <a name="bgp-availability---split-by-peer"></a>Dostępność protokołu BGP — dzielenie przez element równorzędny  

Możesz przeglądać w czasie rzeczywistym dostępność protokołu BGP w przypadku komunikacji równorzędnej i elementów równorzędnych (podstawowych i pomocniczych routerów ExpressRoute). Ten pulpit nawigacyjny pokazuje podstawową sesję protokołu BGP dla prywatnej komunikacji równorzędnej, a druga sesja BGP dla prywatnej komunikacji równorzędnej. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="metryki obwodu":::

### <a name="arp-availability---split-by-peering"></a>Dostępność protokołu ARP — dzielenie według komunikacji równorzędnej  

Możesz przeglądać w czasie rzeczywistym dostępność [protokołu ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) między komunikacjami równorzędnymi i równorzędnymi (podstawowymi i pomocniczymi routerami ExpressRoute). Ten pulpit nawigacyjny pokazuje prywatną komunikację równorzędną ARP w ramach obu elementów równorzędnych, ale kończy się w przypadku komunikacji równorzędnej firmy Microsoft między różnymi elementami równorzędnymi. Agregacja domyślna (średnia) została wykorzystana w obu elementach równorzędnych.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="metryki obwodu":::

## <a name="expressroute-direct-metrics"></a>Metryki bezpośrednie ExpressRoute

### <a name="admin-state---split-by-link"></a>Stan administratora — dzielenie przez łącze

Można wyświetlić stan administratora dla każdego linku pary portów ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="metryki obwodu":::

### <a name="bits-in-per-second---split-by-link"></a>Bity w przedziale na sekundę przez łącze

Bity można wyświetlić w ciągu sekundy dla obu linków pary portów bezpośredniego ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="metryki obwodu":::

### <a name="bits-out-per-second---split-by-link"></a>Bity wychodzące z dzielenia na sekundę przez łącze

Istnieje również możliwość wyświetlenia bitów na sekundę dla obu linków pary portów bezpośredniego ExpressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="metryki obwodu":::

### <a name="line-protocol---split-by-link"></a>Protokół wiersza — dzielenie przez łącze

Możesz wyświetlić protokół wiersza dla każdego łącza pary portów ExpressRoute Direct.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="metryki obwodu":::

### <a name="rx-light-level---split-by-link"></a>Poziom oświetlenia odbierania — dzielenie przez łącze

Możesz wyświetlić poziom światła RX (poziom jasny, który **odbiera**port bezpośredni ExpressRoute) dla każdego portu. Poziomy światła RX w dobrej kondycji zwykle mieszczą się w zakresie od-10 do 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="metryki obwodu":::

### <a name="tx-light-level---split-by-link"></a>Poziom oświetlenia wysyłania — dzielenie przez łącze

Możesz wyświetlić poziom światła TX (poziom jasny, który jest **przesyłany**przez port bezpośredni ExpressRoute) dla każdego portu. Poziom oświetlenia w dobrej kondycji zwykle mieści się w zakresie od-10 do 0 dBm

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="metryki obwodu":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metryki bramy Virtual Network ExpressRoute

### <a name="cpu-utilization---split-instance"></a>Użycie procesora CPU — podzielone wystąpienie

Można wyświetlić użycie procesora CPU przez wystąpienia bramy.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="metryki obwodu":::

### <a name="packets-per-second---split-by-instance"></a>Pakiety na sekundę — dzielenie według wystąpienia

Można wyświetlać pakiety na sekundę przechodzące przez bramę.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="metryki obwodu":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Połączenia bramy ExpressRoute w bitach/s

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="metryki obwodu":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alerty dla połączeń bramy ExpressRoute

1. Aby skonfigurować alerty, przejdź do **Azure monitor**, a następnie wybierz pozycję **alerty**.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="metryki obwodu":::
2. Kliknij pozycję **+ Wybierz element docelowy** , a następnie wybierz pozycję zasób połączenia bramy ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="metryki obwodu":::
3. Zdefiniuj szczegóły alertu.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="metryki obwodu":::
4. Zdefiniuj i Dodaj grupę akcji.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="metryki obwodu":::

## <a name="alerts-based-on-each-peering"></a>Alerty oparte na poszczególnych komunikacji równorzędnej

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="metryki obwodu":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurowanie alertów dotyczących dzienników aktywności na obwodach

W **kryteriach alertów**możesz wybrać pozycję **Dziennik aktywności** dla typu sygnału i wybrać sygnał.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="metryki obwodu":::

## <a name="additional-metrics-in-log-analytics"></a>Dodatkowe metryki w Log Analytics

Możesz również wyświetlić metryki ExpressRoute, przechodząc do zasobu obwodu ExpressRoute i wybierając kartę *dzienniki* . W przypadku wszystkich metryk, które wykonuje kwerenda, dane wyjściowe będą zawierać poniższe kolumny.

|**Kolumna**|**Typ**|**Opis**|
| --- | --- | --- |
|TimeGrain|ciąg|PT1M (wartości metryk są wypychane co minutę)|
|Liczba|liczba rzeczywista|Zwykle równe 2 (każdy MSEE wypychanie pojedynczej wartości metryki co minutę)|
|Minimum|liczba rzeczywista|Minimum dwóch wartości metryk wypychanych przez dwa MSEE|
|Maksimum|liczba rzeczywista|Maxiumum z dwóch wartości metryk wypychanych przez dwa MSEE|
|Średnia|liczba rzeczywista|Równe (minimum + maksimum)/2|
|Łącznie|liczba rzeczywista|Suma dwóch wartości metryk z obu MSEE (wartość główna, na którą ma zostać nadana koncentracja dla metryki zapytania)|
  
## <a name="next-steps"></a>Następne kroki

Skonfiguruj połączenie usługi ExpressRoute.
  
* [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
* [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
* [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
