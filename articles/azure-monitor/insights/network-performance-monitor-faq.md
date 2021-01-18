---
title: Często zadawane pytania — Network Performance Monitor rozwiązanie na platformie Azure | Microsoft Docs
description: W tym artykule opisano często zadawane pytania dotyczące Network Performance Monitor na platformie Azure. Network Performance Monitor (NPM) ułatwia monitorowanie wydajności sieci niemal w czasie rzeczywistym oraz wykrywanie i lokalizowanie wąskich gardeł wydajności sieci.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 8e26a4d996e4c1767856b3dd98ae318d8992a89b
ms.sourcegitcommit: 61d2b2211f3cc18f1be203c1bc12068fc678b584
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562696"
---
# <a name="network-performance-monitor-solution-faq"></a>Rozwiązanie Network Performance Monitor — często zadawane pytania

![Symbol Network Performance Monitor](media/network-performance-monitor-faq/npm-symbol.png)

W tym artykule zapoznaj się z często zadawanymi pytaniami dotyczącymi Network Performance Monitor (NPM) na platformie Azure

[Network Performance Monitor](../../networking/network-monitoring-overview.md) to rozwiązanie [hybrydowego monitorowania sieci](./network-performance-monitor-performance-monitor.md) opartego na chmurze, które ułatwia monitorowanie wydajności sieci między różnymi punktami infrastruktury sieciowej. Pomaga również monitorować łączność sieciową z [punktami końcowymi usługi i aplikacji](./network-performance-monitor-service-connectivity.md) oraz [monitorować wydajność usługi Azure ExpressRoute](./network-performance-monitor-expressroute.md). 

Network Performance Monitor wykrywa problemy z siecią, takie jak ruch blackholing, błędy routingu i problemy, które nie są w stanie wykryć konwencjonalnych metod monitorowania sieci. Rozwiązanie generuje alerty i powiadamia użytkownika, gdy nastąpi naruszenie progu związanego z połączeniem sieciowym. Ponadto gwarantuje ono szybkie wykrywanie problemów z wydajnością sieci i lokalizuje źródło problemu w określonym segmencie lub urządzeniu w sieci. 

Więcej informacji na temat różnych możliwości obsługiwanych przez [Network Performance Monitor](../../networking/network-monitoring-overview.md) jest dostępnych w trybie online.

## <a name="set-up-and-configure-agents"></a>Konfigurowanie i konfigurowanie agentów

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jakie są wymagania dotyczące platformy dla węzłów, które mają być używane do monitorowania przez NPM?
Poniżej wymieniono wymagania dotyczące platformy dla różnych możliwości NPM:

- Funkcje monitora wydajności NPM i monitora łączności usług obsługują zarówno system Windows Server, jak i komputery klienckie z systemem Windows. Obsługiwane są wersje systemu operacyjnego Windows Server z dodatkiem SP1 lub nowszym 2008. Obsługiwane wersje komputerów stacjonarnych i klienckich systemu Windows to Windows 10, Windows 8.1, Windows 8 i Windows 7. 
- NPM ExpressRoute monitor obsługuje tylko system operacyjny Windows Server (2008 z dodatkiem SP1 lub nowszym).

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Czy można używać maszyn z systemem Linux jako węzłów monitorowania w NPM?
Możliwość monitorowania sieci przy użyciu węzłów opartych na systemie Linux jest teraz ogólnie dostępna. Dostępu agenta [tutaj](../../virtual-machines/extensions/oms-linux.md). Agenci systemu Linux oferują możliwość monitorowania tylko dla funkcji monitora wydajności NPM i nie są dostępne na potrzeby funkcji Monitor łączności usługi i ExpressRoute

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Jakie są wymagania dotyczące rozmiaru węzłów, które mają być używane do monitorowania przez NPM?
Aby można było uruchomić rozwiązanie NPM na maszynach wirtualnych węzła do monitorowania sieci, węzły powinny mieć co najmniej 500 MB pamięci i jeden rdzeń. Nie musisz używać oddzielnych węzłów do uruchamiania NPM. Rozwiązanie można uruchomić na węzłach, które mają uruchomione inne obciążenia. Rozwiązanie ma możliwość zatrzymania procesu monitorowania, jeśli używa więcej niż 5% procesora CPU.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Aby użyć NPM, czy należy połączyć węzły jako bezpośredni Agent czy za pośrednictwem System Center Operations Manager?
Zarówno Monitor wydajności, jak i funkcje monitora łączności usług obsługują węzły [połączone jako agenci bezpośredni](../platform/agent-windows.md) i [połączone za pośrednictwem Operations Manager](../platform/om-agents.md).

W przypadku możliwości monitorowania ExpressRoute węzły platformy Azure powinny być połączone tylko z agentami bezpośrednimi. Węzły platformy Azure, które są połączone za pomocą Operations Manager nie są obsługiwane. W przypadku węzłów lokalnych węzły połączone jako agenci bezpośredni i za pośrednictwem Operations Manager są obsługiwane na potrzeby monitorowania obwodu usługi ExpressRoute.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Który protokół z protokołów TCP i ICMP powinien być wybierany do monitorowania?
W przypadku monitorowania sieci przy użyciu węzłów opartych na systemie Windows Server zalecamy użycie protokołu TCP jako protokołu monitorowania, ponieważ zapewnia lepszą dokładność. 

Protokół ICMP jest zalecany dla komputerów stacjonarnych/klienckich z systemem Windows. Ta platforma nie zezwala na wysyłanie danych TCP za pośrednictwem gniazd nieprzetworzonych, które NPM używają do odnajdywania topologii sieci.

Więcej szczegółowych informacji na temat względnych zalet poszczególnych protokołów można znaleźć [tutaj](./network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Jak skonfigurować węzeł do obsługi monitorowania przy użyciu protokołu TCP?
Aby węzeł obsługiwał monitorowanie przy użyciu protokołu TCP: 
* Upewnij się, że platformą jest system Windows Server (2008 z dodatkiem SP1 lub nowszym).
* Uruchom skrypt [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell w węźle. Zapoznaj się z [instrukcjami](./network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) , aby uzyskać więcej szczegółów.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Jak mogę zmienić port TCP używany przez NPM do monitorowania?
Można zmienić port TCP używany przez NPM do monitorowania, uruchamiając skrypt [EnableRules.ps1](https://aka.ms/npmpowershellscript) . Musisz wprowadzić numer portu, który ma być używany jako parametr. Na przykład, aby włączyć protokół TCP na porcie 8060, uruchom polecenie `EnableRules.ps1 8060` . Upewnij się, że używasz tego samego portu TCP na wszystkich węzłach używanych do monitorowania.

Skrypt konfiguruje tylko zaporę systemu Windows lokalnie. Jeśli masz reguły zapory sieciowej lub sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), upewnij się, że zezwalają na ruch przeznaczony dla portu TCP używanego przez NPM.

### <a name="how-many-agents-should-i-use"></a>Ilu agentów należy użyć?
Należy używać co najmniej jednego agenta dla każdej podsieci, która ma być monitorowana.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Jaka jest maksymalna liczba agentów, których można użyć lub pojawia się błąd ".... Osiągnięto limit konfiguracji "?
NPM ogranicza liczbę adresów IP do 5000 adresów IP dla obszaru roboczego. Jeśli węzeł ma adresy IPv4 i IPv6, wartość ta będzie wynosić 2 adresy IP dla tego węzła. W związku z tym limit 5000 adresów IP decyduje o górnym limicie liczby agentów. Nieaktywnych agentów można usunąć z karty węzły w NPM >> Configure. NPM przechowuje również historię wszystkich adresów IP, które zostały kiedykolwiek przypisane do maszyny wirtualnej hostującym agenta, a każda z nich jest traktowana jako oddzielny adres IP, który ma do tego górny limit 5000 adresów IP. Aby zwolnić adresy IP dla obszaru roboczego, możesz użyć strony węzły do usunięcia adresów IP, które nie są używane.

## <a name="monitoring"></a>Monitorowanie

### <a name="how-are-loss-and-latency-calculated"></a>Jak są obliczane straty i opóźnienia
Agenci źródłowi wysyłają żądania protokołu TCP SYN (w przypadku wybrania protokołu TCP do monitorowania) lub żądań echa protokołu ICMP (Jeśli protokół ICMP jest wybierany jako protokołu monitorowania) do docelowego adresu IP w regularnych odstępach czasu, aby upewnić się, że wszystkie ścieżki między kombinacją adresu IP źródłowej są pokryte. Procent odebranych pakietów i czas rundy pakietu jest mierzony w celu obliczenia utraty i opóźnienia każdej ścieżki. Te dane są agregowane względem interwału sondowania i wszystkich ścieżek w celu uzyskania zagregowanych wartości utraty i opóźnienia dla kombinacji adresów IP dla określonego interwału sondowania.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Z jaką częstotliwością Agent źródłowy wysyła pakiety do miejsca docelowego na potrzeby monitorowania?
W przypadku funkcji monitorowania wydajności i monitorowania ExpressRoute Źródło wysyła pakiety co 5 sekund i rejestruje miary sieci. Te dane są agregowane w ciągu 3-minutowego interwału sondowania, aby obliczyć średnią i szczytową wartość utraty i opóźnienia. W przypadku możliwości monitora łączności usług częstotliwość wysyłania pakietów do miary sieci jest określana przez częstotliwość wprowadzoną przez użytkownika dla określonego testu podczas konfigurowania testu.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Ile pakietów jest wysyłanych do monitorowania?
Liczba pakietów wysłanych przez agenta źródłowego do miejsca docelowego w trakcie sondowania jest dostosowywana i jest określana przez nasz algorytm własnościowy, który może być różny dla różnych topologii sieci. Większa liczba ścieżek sieciowych między kombinacją adresu IP źródł-docelowy, czyli liczbą wysyłanych pakietów. System gwarantuje, że wszystkie ścieżki między kombinacją adresu IP źródł-docelowy są pokryte.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Jak NPM odnajdywanie topologii sieci między źródłem a miejscem docelowym?
NPM używa algorytmu własnościowego opartego na traceroute do odnajdywania wszystkich ścieżek i przeskoków między źródłem a miejscem docelowym.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Czy NPM dostarcza informacje o poziomie routingu i przełączania 
Chociaż NPM może wykryć wszystkie możliwe trasy między agentem źródłowym a miejscem docelowym, nie zapewnia widoczności trasy wykonanej przez pakiety wysyłane przez określone obciążenia. Rozwiązanie może pomóc w zidentyfikowaniu ścieżek i przeskoków sieci, które zwiększają opóźnienia niż oczekiwano.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Dlaczego niektóre ścieżki są w złej kondycji?
Między źródłowym i docelowym adres IP może istnieć różne ścieżki sieciowe, a każda ścieżka może mieć inną wartość utraty i opóźnienia. NPM oznacza te ścieżki jako w złej kondycji (oznaczone czerwonym kolorem), dla których wartości utraty i/lub opóźnienia są większe niż odpowiedni próg ustawiony w konfiguracji monitorowania.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Co oznacza skok w kolorze czerwonym w mapie topologii sieci?
Jeśli przeskok ma kolor czerwony, oznacza to, że jest on częścią co najmniej jednej ścieżki w złej kondycji. NPM oznacza tylko ścieżki jako w złej kondycji, ale nie podzieli stan kondycji każdej ścieżki. Aby zidentyfikować przeskoki problematycznych, można wyświetlić opóźnienie przeskoku do przeskoku i oddzielić je, dodając więcej niż oczekiwane opóźnienie.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Jak działa lokalizacja błędów w Monitorze wydajności?
NPM używa mechanizmu probabilistyczne do przypisywania prawdopodobieństwa błędów do każdej ścieżki sieciowej, segmentu sieci i przeskoków sieci w zależności od liczby ścieżek w złej kondycji, które są częścią. Ponieważ segmenty i przeskoki sieciowe stają się częścią większej liczby ścieżek w złej kondycji, wzrasta prawdopodobieństwo wystąpienia błędu. Ten algorytm działa najlepiej, gdy istnieje wiele węzłów z NPM agentem podłączonym do siebie, ponieważ zwiększa to punkty danych do obliczenia prawdopodobieństwa błędów.

### <a name="how-can-i-create-alerts-in-npm"></a>Jak mogę utworzyć alerty w programie NPM?
Obecnie tworzenie alertów z interfejsu użytkownika NPM kończy się niepowodzeniem z powodu znanego problemu. [Utwórz alerty ręcznie](../platform/alerts-log.md).

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Jakie są domyślne Log Analytics zapytania dotyczące alertów
Zapytanie monitora wydajności

```kusto
NetworkMonitoring
 | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
```

Zapytanie monitora łączności usług

```kusto
NetworkMonitoring
 | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
```

Zapytania monitora ExpressRoute: zapytanie obwodu

```kusto
NetworkMonitoring
 | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"
```

Prywatna komunikacja równorzędna

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"
```

Komunikacja równorzędna firmy Microsoft

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"
```

Wspólne zapytanie

```kusto
NetworkMonitoring
 | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy")
```

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Czy NPM monitorować routery i serwery jako pojedyncze urządzenia?
NPM identyfikują adres IP i nazwę hosta odpowiednich przeskoków sieci (przełączniki, routery, serwery itp.) między źródłowym i docelowym adresem IP. Identyfikuje także opóźnienie między tymi zidentyfikowanymi przeskokami. Nie monitorują osobno tych bazowych przeskoków.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Czy NPM można użyć do monitorowania łączności sieciowej między platformą Azure i AWS?
Tak. Aby uzyskać szczegółowe informacje, zobacz artykuł [monitorowanie sieci Azure, AWS i lokalnych](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor) .

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Czy użycie przepustowości ExpressRoute jest przychodzące lub wychodzące?
Użycie przepustowości to całkowita przepustowość przychodząca i wychodząca. Jest on wyrażony w bitach na sekundę.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Czy można uzyskać informacje o przepustowości przychodzące i wychodzące dla ExpressRoute?
Wartości przychodzące i wychodzące dla przepustowości podstawowej i pomocniczej mogą być przechwytywane.

Aby uzyskać informacje na temat poziomu komunikacji równorzędnej firmy Microsoft, użyj poniższego zapytania w przeszukiwaniu dzienników

```kusto
NetworkMonitoring
 | where SubType == "ERMSPeeringUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond 
```

W przypadku prywatnych informacji o poziomie komunikacji równorzędnej Użyj poniższego zapytania w przeszukiwaniu dzienników

```kusto
NetworkMonitoring
 | where SubType == "ERVNetConnectionUtilization"
 | project CircuitName,PeeringName,BitsInPerSecond,BitsOutPerSecond
```

Aby uzyskać informacje o poziomie obwodu, użyj poniższego zapytania w przeszukiwaniu dzienników

```kusto
NetworkMonitoring
 | where SubType == "ERCircuitTotalUtilization"
 | project CircuitName, BitsInPerSecond, BitsOutPerSecond
```

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Które regiony są obsługiwane przez Monitor wydajności NPM?
NPM może monitorować łączność między sieciami w dowolnej części świata z obszaru roboczego, który jest hostowany w jednym z [obsługiwanych regionów](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Które regiony są obsługiwane przez Monitor łączności usługi NPM?
NPM może monitorować łączność z usługami w dowolnej części świata z obszaru roboczego, który jest hostowany w jednym z [obsługiwanych regionów](./network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Które regiony są obsługiwane przez Monitor ExpressRoute NPM?
NPM może monitorować obwody usługi ExpressRoute znajdujące się w dowolnym regionie platformy Azure. Aby dołączyć do NPM, musisz mieć obszar roboczy Log Analytics, który musi być hostowany w jednym z [obsługiwanych regionów](../../expressroute/how-to-npm.md)

## <a name="troubleshoot"></a>Rozwiązywanie problemów

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Dlaczego niektóre przeskoki oznaczono jako niezidentyfikowane w widoku topologii sieci?
NPM używa zmodyfikowanej wersji traceroute do odnajdywania topologii od agenta źródłowego do miejsca docelowego. Niezidentyfikowany przeskok oznacza, że przeskok sieci nie odpowiedział na żądanie traceroute agenta źródłowego. Jeśli trzy kolejne przeskoki sieci nie odpowiadają na traceroute agenta, to rozwiązanie oznaczy nieodpowiadające chmiel jako niezidentyfikowane i nie próbuje odnaleźć większej liczby przeskoków.

Przeskok może nie reagować na traceroute w co najmniej jednym z poniższych scenariuszy:

* Routery zostały skonfigurowane w taki sposób, aby nie ujawniali swojej tożsamości.
* Urządzenia sieciowe nie zezwalają na ruch ICMP_TTL_EXCEEDED.
* Zapora blokuje ICMP_TTL_EXCEEDED odpowiedzi z urządzenia sieciowego.

Gdy jeden z punktów końcowych znajduje się na platformie Azure, traceroute pokazuje niezidentyfikowane przeskoki, ponieważ infrastruktura platformy Azure nie ujawnia tożsamości do traceroute. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Otrzymuję alerty dotyczące testów w złej kondycji, ale nie widzę wysokich wartości w grafie strat i opóźnień NPM. Jak mogę sprawdzić, co jest w złej kondycji?
NPM zgłasza alert, jeśli opóźnienie końca między źródłem a miejscem docelowym przekracza próg dla każdej ścieżki między nimi. Niektóre sieci mają wiele ścieżek łączących te same źródła i miejsce docelowe. NPM wywołuje alert, każda ścieżka jest w złej kondycji. Utrata i opóźnienie widoczne na wykresach to średnia wartość dla wszystkich ścieżek, dlatego nie może być pokazywana dokładna wartość pojedynczej ścieżki. Aby zrozumieć, gdzie próg został naruszony, poszukaj kolumny "podtyp" w alercie. Jeśli problem jest spowodowany przez ścieżkę, wartość podtypu będzie wartość networkpath ((dla testów monitora wydajności), EndpointPath (dla testów monitora łączności usług) i ExpressRoutePath (dla testów monitora ExpressRotue). 

Przykładowe zapytanie do znalezienia jest w złej kondycji:

```kusto
NetworkMonitoring
 | where ( SubType == "ExpressRoutePath")
 | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
 | project SubType, LossHealthState, LatencyHealthState, MedianLatency
```

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Dlaczego mój test jest wyświetlany w złej kondycji, ale topologia nie 
NPM śledzi kompleksową utratę, opóźnienia i topologię w różnych interwałach. Utrata i opóźnienie są mierzone co 5 sekund i agregowane co trzy minuty (dla monitora wydajności i monitora trasy Express), podczas gdy topologia jest obliczana przy użyciu traceroute co 10 minut. Na przykład, od 3:44 do 4:04, topologia może być aktualizowana trzy razy (3:44, 3:54, 4:04), ale utrata i opóźnienie są aktualizowane na siedem razy (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). Topologia wygenerowana na 3:54 będzie renderowana dla strat i opóźnień, które są obliczane w 3:56, 3:59 i 4:02. Załóżmy, że otrzymasz alert informujący o złej kondycji obwodu w 3:59. Zaloguj się do NPM i spróbuj ustawić czas topologii na 3:59. NPM będzie renderować topologię wygenerowaną w 3:54. Aby zrozumieć ostatnią znaną topologię sieci, porównaj pola TimeProcessed (czas, w którym obliczono utratę i opóźnienia) i TracerouteCompletedTime (czas, w którym została obliczona topologia). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Różnica między polami E2EMedianLatency i AvgHopLatencyList w tabeli NetworkMonitoring
E2EMedianLatency to opóźnienie, które jest aktualizowane co trzy minuty po agregowaniu wyników testów ping protokołu TCP, a AvgHopLatencyList jest aktualizowany co 10 minut w oparciu o traceroute. Aby zrozumieć dokładnie czas, w którym E2EMedianLatency został obliczony, użyj pola TimeProcessed. Aby zrozumieć dokładnie czas, w którym traceroute zostało ukończone i zaktualizowane AvgHopLatencyList, użyj pola TracerouteCompletedTime

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Dlaczego liczby opóźnień przeskoków z skoku różnią się od HopLatencyValues 
HopLatencyValues są źródłem do punktu końcowego.
Na przykład: przeskoki-A, B, C. AvgHopLatency-10, 15, 20. Oznacza to, że źródło do opóźnienia = 10, źródło do B opóźnienia = 15 i opóźnienie źródła do C wynosi 20. Interfejs użytkownika obliczy opóźnienie przeskoków A B jako 5 w topologii

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>Rozwiązanie pokazuje 100% strat, ale istnieje łączność między źródłem a miejscem docelowym
Taka sytuacja może wystąpić, Jeśli Zapora hosta lub Zapora pośrednia (Zapora sieciowa lub usługa Azure sieciowej grupy zabezpieczeń) blokuje komunikację między agentem źródłowym a miejscem docelowym przez port używany do monitorowania przez NPM (domyślnie port jest 8084, chyba że klient został zmieniony).

* Aby sprawdzić, czy Zapora hosta nie blokuje komunikacji na wymaganym porcie, Wyświetl stan kondycji węzłów źródłowych i docelowych z następującego widoku: Network Performance Monitor-> konfiguracja-> węzły. 
  Jeśli są w złej kondycji, Wyświetl instrukcje i wykonaj działania naprawcze. Jeśli węzły są w dobrej kondycji, przejdź do kroku b. poniżej.
* Aby sprawdzić, czy pośrednia Zapora sieciowa lub usługa Azure sieciowej grupy zabezpieczeń nie blokuje komunikacji na wymaganym porcie, użyj narzędzia PsPing innej firmy, korzystając z poniższych instrukcji:
  * Narzędzie psping jest dostępne do pobrania w [tym miejscu](/sysinternals/downloads/psping) 
  * Uruchom następujące polecenie w węźle źródłowym.
    * psping-n 15 \<destination node IPAddress\> :P ortnumber domyślnie używa portu 8084. Na wypadek, gdyby został on jawnie zmieniony przy użyciu skryptu EnableRules.ps1, wprowadź niestandardowy numer portu, którego używasz). To jest polecenie ping z maszyny Azure do lokalnego
* Sprawdź, czy polecenia ping zostały wykonane pomyślnie. W przeciwnym razie wskazuje, że pośrednia Zapora sieciowa lub usługa Azure sieciowej grupy zabezpieczeń blokuje ruch na tym porcie.
* Teraz uruchom polecenie z węzła docelowego do adresu IP węzła źródłowego.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Istnieje utrata z węzła A do B, ale nie z węzła B do. Dlaczego?
Ponieważ ścieżki sieciowe między A A B mogą różnić się od ścieżek sieciowych między B a A, można zaobserwować różne wartości utraty i opóźnienia.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Dlaczego wszystkie obwody usługi ExpressRoute i połączenia komunikacji równorzędnej nie są wykrywane?
NPM teraz odnajduje obwody usługi ExpressRoute i połączenia komunikacji równorzędnej we wszystkich subskrypcjach, do których użytkownik ma dostęp. Wybierz wszystkie subskrypcje, w których są połączone zasoby usługi Express Route, i Włącz monitorowanie dla każdego wykrytego zasobu. NPM szuka obiektów połączeń podczas odnajdywania prywatnej komunikacji równorzędnej, dlatego należy sprawdzić, czy sieć wirtualna jest skojarzona z usługą komunikacji równorzędnej. Usługa NPM nie wykrywa obwodów i komunikacji równorzędnej, które znajdują się w innej dzierżawie niż obszar roboczy Log Analytics.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Funkcja monitora ER ma komunikat diagnostyczny "ruch nie przechodzi przez żaden obwód". Co to oznacza?

Może istnieć scenariusz, w którym istnieje zdrowe połączenie między węzłami lokalnymi i Azure, ale ruch nie przechodzi przez obwód ExpressRoute skonfigurowany do monitorowania przez NPM. 

Może się tak zdarzyć, jeśli:

* Obwód ER nie działa.
* Filtry tras są konfigurowane w taki sposób, że zapewniają priorytet innym trasom (na przykład połączenie sieci VPN lub inny obwód usługi ExpressRoute) przez zamierzony obwód ExpressRoute. 
* Węzły lokalne i platformy Azure wybrane do monitorowania obwodu usługi ExpressRoute w konfiguracji monitorowania nie mają łączności ze sobą za pośrednictwem zamierzonego obwodu usługi ExpressRoute. Upewnij się, że wybrano poprawne węzły, które mają łączność ze sobą za pośrednictwem obwodu usługi ExpressRoute, który ma być monitorowany.

### <a name="why-does-expressroute-monitor-report-my-circuitpeering-as-unhealthy-when-it-is-available-and-passing-data"></a>Dlaczego monitor ExpressRoute zgłasza mój obwód/komunikację równorzędną w złej kondycji, gdy jest dostępny i przekazuje dane.
Monitor ExpressRoute porównuje wartości wydajności sieci (utrata, opóźnienie i wykorzystanie przepustowości) zgłoszone przez agentów/usługę z progami ustawionymi podczas konfiguracji. W przypadku obwodu, jeśli zgłoszone wykorzystanie przepustowości jest większe niż próg ustawiony w konfiguracji, obwód zostanie oznaczony jako w złej kondycji. W przypadku komunikacji równorzędnej w przypadku, gdy zgłoszono, że utrata, opóźnienie lub wykorzystanie przepustowości jest większe niż próg ustawiony w konfiguracji, Komunikacja równorzędna jest oznaczana jako zła. NPM nie wykorzystuje metryk ani żadnej innej formy danych, aby deicde stan kondycji.

### <a name="why-does-expressroute-monitorbandwidth-utilisation-report-a-value-differrent-from-metrics-bits-inout"></a>Dlaczego ExpressRoute Wykorzystaj Monitor'bandwidth, zgłoś wartość innej z bitów metryk w/out
W przypadku monitora ExpressRoute przepustowość utiliation to średnia przepustowość przychodząca i wychodząca w ciągu ostatnich 20 minut, wyrażona w bitach na sekundę. W przypadku metryk usługi Express Route bit in/out jest na minutę punktów danych. Wewnętrznie zestaw danych używany dla obu tych obiektów jest taki sam, ale agregacja valies między metrykami NPM i ER. Aby zapoznać się z szczegółowym, minutowym monitorowaniem i szybkimi alertami, zalecamy ustawienie alertów bezpośrednio w metrykach usługi ER

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Podczas konfigurowania monitorowania obwodu usługi ExpressRoute nie są wykrywane węzły platformy Azure.
Taka sytuacja może wystąpić, jeśli węzły platformy Azure są połączone za pomocą Operations Manager. Funkcja monitor ExpressRoute obsługuje tylko te węzły platformy Azure, które są połączone jako agenci bezpośrednio.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Nie mogę wykryć przez obwody usługi ExpressRoute w portalu pakietu OMS
Chociaż NPM można użyć zarówno z Azure Portal, jak i portalu pakietu OMS, Wykrywanie obwodu w funkcji monitorowania ExpressRoute działa tylko za pośrednictwem Azure Portal. Po odnalezieniu obwodów za pomocą Azure Portal można użyć możliwości w jednym z dwóch portali. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>W ramach możliwości monitora łączności usług, czasu odpowiedzi usługi, utraty sieci, a opóźnienie są wyświetlane jako NA
Może się tak zdarzyć, jeśli co najmniej jeden ma wartość PRAWDA:

* Usługa nie działa.
* Węzeł używany do sprawdzania łączności sieciowej z usługą nie działa.
* Wartość docelowa wprowadzona w konfiguracji testu jest niepoprawna.
* Węzeł nie ma żadnej łączności sieciowej.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>W funkcji Monitor łączności usługi jest pokazywany prawidłowy czas odpowiedzi usługi, ale utrata sieci, a także opóźnienie są wyświetlane jako NA
 Może się tak zdarzyć, jeśli co najmniej jeden ma wartość PRAWDA:

* Jeśli węzeł używany do sprawdzania łączności sieciowej z usługą jest komputerem klienckim z systemem Windows, usługa docelowa blokuje żądania protokołu ICMP lub Zapora sieciowa blokuje żądania protokołu ICMP, które pochodzą z tego węzła.
* Pole wyboru Wykonaj pomiary sieci jest puste w konfiguracji testu.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>W funkcji Monitor łączności usług czas odpowiedzi usługi to, ale utrata połączenia sieciowego, a także opóźnienia są prawidłowe
Taka sytuacja może wystąpić, jeśli usługa docelowa nie jest aplikacją internetową, ale test jest skonfigurowany jako test sieci Web. Edytuj konfigurację testu i wybierz typ testowy jako sieć zamiast sieci Web.

## <a name="miscellaneous"></a>Różne

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Czy istnieje wpływ na wydajność w węźle używanym do monitorowania?
Proces NPM jest skonfigurowany do zatrzymania, jeśli wykorzystuje ponad 5% zasobów procesora CPU hosta. Ma to na celu zapewnienie, że można nadal korzystać z węzłów dla ich zwykłych obciążeń bez wpływu na wydajność.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Czy NPM edytować reguły zapory na potrzeby monitorowania?
NPM tworzy tylko lokalną regułę zapory systemu Windows w węzłach, na których działa skrypt EnableRules.ps1 PowerShell, aby umożliwić agentom tworzenie połączeń TCP ze sobą na określonym porcie. Rozwiązanie nie modyfikuje żadnej reguły zapory sieciowej ani sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń).

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Jak sprawdzić kondycję węzłów używanych do monitorowania?
Aby wyświetlić stan kondycji węzłów używanych do monitorowania, należy wykonać następujące czynności: Network Performance Monitor-> Configuration-> nodes. Jeśli węzeł jest w złej kondycji, możesz wyświetlić szczegóły błędu i wykonać sugerowaną akcję.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Czy NPMe liczby opóźnień raportów w mikrosekundach?
NPM zaokrągla liczbę opóźnień w interfejsie użytkownika i w milisekundach. Te same dane są przechowywane na wyższym poziomie szczegółowości (czasami do czterech miejsc dziesiętnych).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat Network Performance Monitor, odwołując się do [Network Performance Monitor rozwiązania na platformie Azure](./network-performance-monitor.md).

