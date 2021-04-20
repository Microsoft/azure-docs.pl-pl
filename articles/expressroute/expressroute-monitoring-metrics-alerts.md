---
title: 'Azure ExpressRoute: Monitorowanie, metryki i alerty'
description: Dowiedz się Azure ExpressRoute o monitorowaniu, metrykach i alertach przy użyciu Azure Monitor, czyli jednego centrum dla wszystkich metryk, alertów i dzienników diagnostycznych na platformie Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 04/07/2021
ms.author: duau
ms.openlocfilehash: 44ddf54aac61ab00009e7e2cc820b38074c5e8c3
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725786"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorowanie, metryki i alerty usługi ExpressRoute

Ten artykuł ułatwia zrozumienie monitorowania, metryk i alertów usługi ExpressRoute przy użyciu Azure Monitor. Azure Monitor to jedno centrum dla wszystkich metryk, alertów i dzienników diagnostycznych na całej platformie Azure.
 
>[!NOTE]
>Używanie **metryk klasycznych nie** jest zalecane.
>

## <a name="expressroute-metrics"></a>Metryki expressRoute

Aby wyświetlić **metryki,** przejdź do *strony Azure Monitor* i wybierz *pozycję Metryki.* Aby wyświetlić **metryki expressRoute,** filtruj według obwodów *expressRoute typu zasobu*. Aby wyświetlić **Global Reach,** odfiltruj według obwodów *usługi ExpressRoute* typu zasobu i wybierz zasób obwodu usługi ExpressRoute, który Global Reach włączony. Aby wyświetlić **ExpressRoute Direct** metryki, odfiltruj typ zasobu według *portów expressroute.* 

Po wybraniu metryki zostanie zastosowana domyślna agregacja. Opcjonalnie możesz zastosować podział, który spowoduje wyświetlanie metryki o różnych wymiarach.

### <a name="aggregation-types"></a>Typy agregacji:

Eksplorator metryk obsługuje typy agregacji SUM, MAX, MIN, AVG [i](../azure-monitor/essentials/metrics-charts.md#aggregation)COUNT. Podczas przeglądania szczegółowych informacji dla każdej metryki usługi ExpressRoute należy użyć zalecanego typu agregacji.

* Suma: suma wszystkich wartości przechwyconych w interwale agregacji. 
* Liczba: liczba pomiarów przechwyconych w interwale agregacji. 
* Średnia: średnia wartości metryk przechwyconych w interwale agregacji. 
* Min. Najmniejsza wartość przechwycona w interwale agregacji. 
* Maksimum: największa wartość przechwycona w interwale agregacji. 

### <a name="available-metrics"></a>Dostępne metryki

|**Metryka**|**Kategoria**|**Wymiary**|**Funkcje**|
| --- | --- | --- | --- |
|Dostępność ARP|Dostępność|<ui><li>Komunikacja równorzędna (podstawowy/pomocniczy router expressRoute)</ui></li><ui><li> Typ komunikacji równorzędnej (prywatna/publiczna/microsoft)</ui></li>|ExpressRoute|
|Dostępność protokołu BGP|Dostępność|<ui><li> Komunikacja równorzędna (podstawowy/pomocniczy router expressRoute)</ui></li><ui><li> Typ komunikacji równorzędnej</ui></li>|ExpressRoute|
|BitsInPerSecond|Ruch|<ui><li> Typ komunikacji równorzędnej (ExpressRoute)</ui></li><ui><li>Link (ExpressRoute Direct)</ui></li>|<li>ExpressRoute</li><li>Usługa ExpressRoute Direct</li><ui><li>Połączenie bramy usługi ExpressRoute</ui></li>|
|BitsOutPerSecond|Ruch| <ui><li>Typ komunikacji równorzędnej (ExpressRoute)</ui></li><ui><li> Link (ExpressRoute Direct) |<ui><li>ExpressRoute<ui><li>ExpressRoute Direct</ui></li><ui><li>Połączenie bramy usługi ExpressRoute</ui></li>|
|Wykorzystanie procesora|Wydajność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|Liczba pakietów na sekundę|Wydajność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|Liczba tras anonsowanych do elementu równorzędnego |Dostępność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|Liczba tras nauczone z elementu równorzędnego |Dostępność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|Częstotliwość zmian tras |Dostępność| <ui><li>Wystąpienie</ui></li>|ExpressRoute Virtual Network Gateway|
|Liczba maszyn wirtualnych w Virtual Network |Dostępność| Nie dotyczy |ExpressRoute Virtual Network Gateway|
|GlobalReachBitsInPerSecond|Ruch|<ui><li>Klucz równorzędnego obwodu (klucz usługi)</ui></li>|Global Reach|
|GlobalReachBitsOutPerSecond|Ruch|<ui><li>Klucz równorzędnego obwodu (klucz usługi)</ui></li>|Global Reach|
|AdminState|Łączność fizyczna|Link|Usługa ExpressRoute Direct|
|LineProtocol|Łączność fizyczna|Link|Usługa ExpressRoute Direct|
|RxLightLevel|Łączność fizyczna|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|Usługa ExpressRoute Direct|
|TxLightLevel|Łączność fizyczna|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|Usługa ExpressRoute Direct|
>[!NOTE]
>Użycie *globalGlobalReachBitsInPerSecond* i *GlobalGlobalReachBitsOutPerSecond* będzie widoczne tylko wtedy, gdy zostanie nawiązane Global Reach co najmniej jedno połączenie sieciowe.
>

## <a name="circuits-metrics"></a>Metryki obwodów

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bity we/wy — metryki dla wszystkich komunikacji równorzędnych

Typ agregacji: *Średnio*

Metryki można wyświetlać we wszystkich komunikacji równorzędnych w danym obwodzie expressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg" alt-text="metryki obwodu":::

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity wejdą i wychodzące — metryki na komunikacji równorzędnej

Typ agregacji: *Średnio*

Metryki prywatnej, publicznej i komunikacji równorzędnej firmy Microsoft można wyświetlać w bitach na sekundę.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg" alt-text="metryki na komunikacji równorzędnej":::

### <a name="bgp-availability---split-by-peer"></a>Dostępność protokołu BGP — podział według elementu równorzędnego  

Typ agregacji: *Średnio*

Dostępność protokołu BGP (warstwa 3) komunikacji równorzędnej i elementów równorzędnych (podstawowych i pomocniczych routerów usługi ExpressRoute) jest niemal w czasie rzeczywistym. Ten pulpit nawigacyjny pokazuje, że stan podstawowej sesji protokołu BGP jest równy prywatnej komunikacji równorzędnej, a stan drugiej sesji protokołu BGP nie jest w przypadku prywatnej komunikacji równorzędnej. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg" alt-text="Dostępność protokołu BGP na elementu równorzędnego":::

### <a name="arp-availability---split-by-peering"></a>Dostępność usługi ARP — podział według komunikacji równorzędnej  

Typ agregacji: *Średnio*

Możesz wyświetlić dostępność łączności [ARP](./expressroute-troubleshooting-arp-resource-manager.md) (warstwy 3) w czasie niemal rzeczywistym między komunikacja równorzędna i komunikacja równorzędna (podstawowe i pomocnicze routery usługi ExpressRoute). Ten pulpit nawigacyjny pokazuje, że stan sesji ARP prywatnej komunikacji równorzędnej jest w obu równorzędnych, ale nie dotyczy komunikacji równorzędnej firmy Microsoft dla obu elementów równorzędnych. Domyślna agregacja (średnia) została wykorzystana w obu równorzędnych.  

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg" alt-text="Dostępność ARP na elementu równorzędnego":::

## <a name="expressroute-direct-metrics"></a>ExpressRoute Direct metryki

### <a name="admin-state---split-by-link"></a>Stan administratora — podział według linku

Typ agregacji: *Średnio*

Stan administratora można wyświetlić dla każdego linku ExpressRoute Direct pary portów. Stan administratora określa, czy port fizyczny jest wł. czy wyłączony. Ten stan jest wymagany do przekazania ruchu przez ExpressRoute Direct połączenia.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg" alt-text="Stan administratora ER Direct":::

### <a name="bits-in-per-second---split-by-link"></a>Bity w ciągu sekundy — podział według linku

Typ agregacji: *Średnio*

Bity w ciągu sekundy można wyświetlić w obu linkach ExpressRoute Direct pary portów. Monitoruj ten pulpit nawigacyjny, aby porównać przepustowość ruchu przychodzącego dla obu linków.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg" alt-text="Bity bezpośrednie ER na sekundę":::

### <a name="bits-out-per-second---split-by-link"></a>Bity na sekundę — podział według linku

Typ agregacji: *Średnio*

Możesz również wyświetlić bity na sekundę dla obu łączy pary ExpressRoute Direct portów. Monitoruj ten pulpit nawigacyjny, aby porównać przepustowość wychodzącą dla obu linków.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg" alt-text="Bity bezpośrednie ER na sekundę":::

### <a name="line-protocol---split-by-link"></a>Protokół liniowy — podział według linku

Typ agregacji: *Średnio*

Protokół liniowy można wyświetlić w każdym połączeniu pary ExpressRoute Direct portów. Protokół liniowy wskazuje, czy połączenie fizyczne jest uruchomione na ExpressRoute Direct. Monitoruj ten pulpit nawigacyjny i ustaw alerty, aby wiedzieć, kiedy połączenie fizyczne nie zostało nawiązaniu połączenia.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg" alt-text="Protokół ER Direct Line":::

### <a name="rx-light-level---split-by-link"></a>Rx Light Level — podział według linku

Typ agregacji: *Średnio*

Możesz wyświetlić poziom światła Rx (poziom światła odbierany ExpressRoute Direct **odbierany** przez port ) dla każdego portu. Poziomy światła dobrej kondycji Rx zazwyczaj należą do zakresu od -10 dBm do 0 dBm. Ustaw alerty, aby były powiadamiane, jeśli poziom światła Rx znajduje się poza zakresem dobrej kondycji.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg" alt-text="ER Direct line Rx Light Level":::

### <a name="tx-light-level---split-by-link"></a>Tx Light Level — podział według linku

Typ agregacji: *Średnio*

Możesz wyświetlić poziom światła Tx (poziom światła przesyłany przez port ExpressRoute Direct **)** dla każdego portu. Poziomy światła TX w dobrej kondycji zazwyczaj należą do zakresu od -10 dBm do 0 dBm. Ustaw alerty, aby były powiadamiane, jeśli poziom światła Tx mieści się poza zakresem dobrej kondycji.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg" alt-text="ER Direct line Tx Light Level":::

## <a name="expressroute-virtual-network-gateway-metrics"></a>Metryki bramy Virtual Network ExpressRoute

Typ agregacji: *Średnio*

Podczas wdrażania bramy usługi ExpressRoute platforma Azure zarządza obliczeniami i funkcjami bramy. Aby lepiej zrozumieć wydajność bramy, dostępnych jest sześć metryk bramy:

* Wykorzystanie procesora
* Liczba pakietów na sekundę
* Liczba tras anonsowanych do elementów równorzędnych
* Liczba tras nauczone z elementów równorzędnych
* Częstotliwość zmian tras
* Liczba maszyn wirtualnych w sieci wirtualnej  

Zdecydowanie zaleca się ustawienie alertów dla każdej z tych metryk, aby mieć świadomość, kiedy brama może mieć problemy z wydajnością.

### <a name="cpu-utilization---split-instance"></a>Wykorzystanie procesora CPU — dzielenie wystąpienia

Typ agregacji: *Średnio*

Można wyświetlić wykorzystanie procesora CPU poszczególnych wystąpień bramy. Użycie procesora CPU może przez krótki czas szczytować się podczas rutynowej konserwacji hosta, ale długotrwałe wysokie wykorzystanie procesora MOŻE wskazywać, że brama osiąga wąskie gardło wydajności. Zwiększenie rozmiaru bramy usługi ExpressRoute może rozwiązać ten problem. Ustaw alert o tym, jak często użycie procesora CPU przekracza określony próg.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/cpu-split.jpg" alt-text="Zrzut ekranu przedstawiający użycie procesora CPU — metryki podziału.":::

### <a name="packets-per-second---split-by-instance"></a>Pakiety na sekundę — podział według wystąpienia

Typ agregacji: *Średnio*

Ta metryka przechwytuje liczbę pakietów przychodzących, które przejdą przez bramę usługi ExpressRoute. Jeśli brama odbiera ruch z sieci lokalnej, powinien być w tym miejscu wyświetlony spójny strumień danych. Ustaw alert dla sytuacji, gdy liczba pakietów na sekundę spadnie poniżej progu wskazującego, że brama nie odbiera już ruchu.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/pps-split.jpg" alt-text="Zrzut ekranu przedstawiający pakiety na sekundę — metryki podziału.":::

### <a name="count-of-routes-advertised-to-peer---split-by-instance"></a>Liczba tras anonsowanych do elementu równorzędnego — podział według wystąpienia

Typ agregacji: *Liczba*

Ta metryka to liczba tras, które brama usługi ExpressRoute reklamuje do obwodu. Przestrzenie adresowe mogą obejmować sieci wirtualne połączone za pomocą komunikacji równorzędnej sieci wirtualnych i korzystające ze zdalnej bramy usługi ExpressRoute. Należy oczekiwać, że liczba tras pozostanie spójna, chyba że w przestrzeniach adresowych sieci wirtualnej często występują zmiany. Ustaw alert dla sytuacji, gdy liczba anonsowanych tras spadnie poniżej progu liczby przestrzeni adresowych sieci wirtualnej, o których wiesz.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-advertised-to-peer.png" alt-text="Zrzut ekranu przedstawiający liczbę tras anonsowanych do elementu równorzędnego.":::

### <a name="count-of-routes-learned-from-peer---split-by-instance"></a>Liczba tras wyuczonych z elementu równorzędnego — podział według wystąpienia

Typ agregacji: *Maks.*

Ta metryka pokazuje liczbę tras, które brama usługi ExpressRoute uczy się od elementów równorzędnych połączonych z obwodem usługi ExpressRoute. Te trasy mogą pochodzić z innej sieci wirtualnej podłączonej do tego samego obwodu lub nauczony ze środowiska lokalnego. Ustaw alert dla sytuacji, gdy liczba naucznych tras spadnie poniżej określonego progu. Może to oznaczać, że brama ma problem z wydajnością lub zdalne sieci równorzędne nie reklamują już tras do obwodu usługi ExpressRoute. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/count-of-routes-learned-from-peer.png" alt-text="Zrzut ekranu przedstawiający liczbę tras nauczone z elementu równorzędnego.":::

### <a name="frequency-of-routes-change---split-by-instance"></a>Częstotliwość zmiany tras — podział według wystąpienia

Typ agregacji: *Suma*

Ta metryka przedstawia częstotliwość poznanych tras lub anonsowanych do zdalnych elementów równorzędnych. Najpierw należy zbadać urządzenia lokalne, aby zrozumieć, dlaczego sieć zmienia się tak często. Duża częstotliwość zmiany tras może wskazywać na problem z wydajnością bramy usługi ExpressRoute, w którym skalowanie w górę sku bramy może rozwiązać ten problem. Ustaw alert dla progu częstotliwości, aby wiedzieć, kiedy brama usługi ExpressRoute widzi nietypowe zmiany tras.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/frequency-of-routes-changed.png" alt-text="Zrzut ekranu przedstawiający częstotliwość zmieniania metryk tras.":::

### <a name="number-of-vms-in-the-virtual-network"></a>Liczba maszyn wirtualnych w Virtual Network

Typ agregacji: *Maks.*

Ta metryka przedstawia liczbę maszyn wirtualnych używających bramy usługi ExpressRoute. Liczba maszyn wirtualnych może obejmować maszyny wirtualne z równorzędnych sieci wirtualnych, które korzystają z tej samej bramy usługi ExpressRoute. Ustaw alert dla tej metryki, jeśli liczba maszyn wirtualnych przekroczy określony próg, który może mieć wpływ na wydajność bramy. 

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/number-of-virtual-machines-virtual-network.png" alt-text="Zrzut ekranu przedstawiający liczbę maszyn wirtualnych w metryce sieci wirtualnej.":::

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Połączenia bramy usługi ExpressRoute w bitach/sekundach

Typ agregacji: *Średnio*

Ta metryka przedstawia użycie przepustowości dla określonego połączenia z obwodem expressRoute.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/erconnections.jpg" alt-text="Zrzut ekranu przedstawiający metrykę użycia przepustowości połączenia bramy.":::

## <a name="alerts-for-expressroute-gateway-connections"></a>Alerty dotyczące połączeń bramy usługi ExpressRoute

1. Aby skonfigurować alerty, przejdź do Azure Monitor , **a** następnie wybierz pozycję **Alerty.**

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg" alt-text="alerty":::
2. Wybierz **pozycję +Wybierz element docelowy** i wybierz zasób połączenia bramy usługi ExpressRoute.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg" alt-text="Docelowego":::
3. Zdefiniuj szczegóły alertu.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg" alt-text="grupa akcji":::
4. Zdefiniuj i dodaj grupę akcji.

   :::image type="content" source="./media/expressroute-monitoring-metrics-alerts/actiongroup.png" alt-text="dodawanie grupy akcji":::

## <a name="alerts-based-on-each-peering"></a>Alerty oparte na każdej komunikacji równorzędnej

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg" alt-text="każda komunikacja równorzędna":::

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurowanie alertów dla dzienników aktywności w obwodach

W **kryteriach alertu** możesz wybrać pozycję **Dziennik aktywności** dla opcji Typ sygnału, a następnie wybrać pozycję Sygnał.

:::image type="content" source="./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg" alt-text="dzienniki aktywności":::

## <a name="more-metrics-in-log-analytics"></a>Więcej metryk w u usługi Log Analytics

Możesz również wyświetlić metryki expressRoute, przechodząc do zasobu obwodu expressRoute i wybierając *kartę Dzienniki.* W przypadku wszelkich zapytań dotyczących metryk dane wyjściowe będą zawierać poniższe kolumny.

|**Kolumna**|**Typ**|**Opis**|
| --- | --- | --- |
|TimeGrain|ciąg|PT1M (wartości metryk są wypychane co minutę)|
|Liczba|liczba rzeczywista|Zwykle równe 2 (każde MSEE wypycha pojedynczą wartość metryki co minutę)|
|Minimum|liczba rzeczywista|Minimum dwóch wartości metryk wypchnięć przez dwa metryce MNEE|
|Maksimum|liczba rzeczywista|Maksymalna z dwóch wartości metryk wypchnięć przez dwa MNEE|
|Średnia|liczba rzeczywista|Równe (minimum + maksimum)/2|
|Łącznie|liczba rzeczywista|Suma dwóch wartości metryk z obu MNEE (główna wartość, na których należy skoncentrować się dla metryk, dla których jest zapytanie)|
  
## <a name="next-steps"></a>Następne kroki

Skonfiguruj połączenie usługi ExpressRoute.
  
* [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
* [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
* [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
