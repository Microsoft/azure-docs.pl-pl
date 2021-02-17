---
title: Network Performance Monitor rozwiązanie na platformie Azure Log Analytics | Microsoft Docs
description: Korzystając z funkcji monitorowania ExpressRoute w Network Performance Monitor, można monitorować kompleksową łączność i wydajność między biurami oddziałów i platformą Azure za pośrednictwem usługi Azure ExpressRoute.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 477512c0b1de8548e2aa1e2971a5d831e84d2139
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578371"
---
# <a name="expressroute-monitor"></a>Monitor ExpressRoute

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego w Network Performance Monitor. Można nadal korzystać z testów utworzonych przed 1 lipca 2021. Aby zminimalizować zakłócenia działania usługi dla bieżących obciążeń, należy [migrować testy z Network Performance Monitor do nowego monitora połączeń](https://docs.microsoft.com/azure/network-watcher/migrate-to-connection-monitor-from-network-performance-monitor) na platformie Azure Network Watcher przed 29 lutego 2024.

Korzystając z możliwości usługi Azure ExpressRoute monitor w [Network Performance Monitor](network-performance-monitor.md) , można monitorować kompleksową łączność i wydajność między oddziałami i platformą Azure za pośrednictwem usługi Azure ExpressRoute. Najważniejsze zalety: 

- Autowykrywanie obwodów usługi ExpressRoute skojarzonych z Twoją subskrypcją.
- Śledzenie wykorzystania przepustowości, strat i opóźnień w ramach obwodu, komunikacji równorzędnej i poziomu Virtual Network platformy Azure dla usługi ExpressRoute.
- Odnajdywanie topologii sieci obwodów usługi ExpressRoute.

![Monitor ExpressRoute](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację Network Performance Monitor, Otwórz [rozwiązanie Network Performance Monitor](network-performance-monitor.md) i wybierz pozycję **Konfiguruj**.

### <a name="configure-network-security-group-rules"></a>Skonfiguruj reguły sieciowej grupy zabezpieczeń 
W przypadku serwerów na platformie Azure, które są używane do monitorowania za pośrednictwem Network Performance Monitor, skonfiguruj reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w celu zezwalania na ruch TCP na porcie używanym przez Network Performance Monitor dla transakcji syntetycznych. Domyślnym portem jest 8084. Ta konfiguracja pozwala agentowi Log Analytics zainstalowanemu na maszynach wirtualnych platformy Azure komunikować się z lokalnym agentem monitorowania. 

Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń, zobacz [Network Security Groups](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Przed kontynuowaniem tego kroku Zainstaluj agenta lokalnego serwera i agenta serwera Azure, a następnie uruchom skrypt EnableRules.ps1 PowerShell. 

 
### <a name="discover-expressroute-peering-connections"></a>Odnajdywanie połączeń komunikacji równorzędnej ExpressRoute 
 
1. Wybierz widok **komunikacji równorzędnej ExpressRoute** .
2. Wybierz pozycję **Odkryj teraz** , aby odnaleźć wszystkie prywatne sieci równorzędne ExpressRoute połączone z sieciami wirtualnymi w ramach subskrypcji platformy Azure połączonej z tym obszarem roboczym usługi Azure log Analytics.

    >[!NOTE]
    > Obecnie rozwiązanie odnajduje tylko prywatne połączenia równorzędne ExpressRoute. 

    >[!NOTE]
    > Odnajdywane są tylko prywatne elementy równorzędne połączone z sieciami wirtualnymi skojarzonymi z subskrypcją połączoną z tym obszarem roboczym Log Analytics. Jeśli ExpressRoute jest połączony z sieciami wirtualnymi spoza subskrypcji połączonej z tym obszarem roboczym, Utwórz obszar roboczy Log Analytics w tych subskrypcjach. Następnie użyj Network Performance Monitor do monitorowania tych komunikacji równorzędnej. 

    ![Konfiguracja monitora ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   Po zakończeniu odnajdywania odnalezione połączenia prywatnej komunikacji równorzędnej są wyświetlane w tabeli. Monitorowanie tych komunikacji równorzędnej jest początkowo w stanie wyłączenia. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Włącz monitorowanie połączeń komunikacji równorzędnej ExpressRoute 

1. Wybierz połączenie prywatnej komunikacji równorzędnej, które chcesz monitorować.
2. W okienku po prawej stronie zaznacz pole wyboru **Monitoruj tę komunikację równorzędną** . 
3. Jeśli zamierzasz utworzyć zdarzenia dotyczące kondycji dla tego połączenia, wybierz pozycję **Włącz monitorowanie kondycji dla tej komunikacji równorzędnej**. 
4. Wybierz pozycję warunki monitorowania. Możesz ustawić progi niestandardowe dla generowania zdarzeń dotyczących kondycji, wprowadzając wartości progowe. Za każdym razem, gdy wartość warunku spadnie powyżej wartości progowej dla połączenia komunikacji równorzędnej, generowane jest zdarzenie kondycji. 
5. Wybierz pozycję **Dodaj agentów** , aby wybrać agentów monitorowania, których zamierzasz używać do monitorowania tego połączenia komunikacji równorzędnej. Upewnij się, że dodano agentów na obu końcach połączenia. Potrzebujesz co najmniej jednego agenta w sieci wirtualnej podłączonej do tej komunikacji równorzędnej. Wymagany jest również co najmniej jeden lokalny Agent połączony z tą komunikację równorzędną. 
6. Wybierz pozycję **Zapisz** , aby zapisać konfigurację. 

   ![Konfiguracja monitorowania ExpressRoute](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Po włączeniu reguł i wybraniu wartości i agentów odczekaj od 30 do 60 minut na wypełnianie wartości i wyświetlenie kafelków **monitorowania ExpressRoute** . Gdy widzisz kafelki monitorowania, obwody usługi ExpressRoute i zasoby połączeń są teraz monitorowane przez Network Performance Monitor. 

>[!NOTE]
> Ta funkcja działa w niezawodnym obszarze roboczym, który został uaktualniony do nowego języka zapytań.

## <a name="walkthrough"></a>Przewodnik 

Pulpit nawigacyjny Network Performance Monitor zawiera omówienie kondycji obwodów usługi ExpressRoute i połączeń komunikacji równorzędnej. 

![Pulpit nawigacyjny Network Performance Monitor](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Lista obwodów 

Aby wyświetlić listę wszystkich monitorowanych obwodów usługi ExpressRoute, wybierz kafelek obwodów usługi ExpressRoute. Możesz wybrać obwód i wyświetlić jego stan kondycji, wykresy trendu dotyczące utraty pakietów, wykorzystania przepustowości i opóźnień. Wykresy są interaktywne. Można wybrać niestandardowe okno czasowe do kreślenia wykresów. Przeciągnij wskaźnik myszy nad obszar na wykresie, aby powiększyć i zobaczyć szczegółowe punkty danych. 

![Lista obwodów usługi ExpressRoute](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Trendy utraty, opóźnień i przepływności 

Wykresy o wykorzystaniu przepustowości, opóźnieniu i utracie są interaktywne. Możesz powiększyć do dowolnej sekcji tych wykresów za pomocą kontrolek myszy. Można także zobaczyć przepustowość, opóźnienie i dane utraty dla innych interwałów. W lewym górnym rogu przycisku **Akcje** wybierz pozycję  **Data/godzina**. 

![Opóźnienie ExpressRoute](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Lista komunikacji równorzędnych 

Aby wyświetlić listę wszystkich połączeń z sieciami wirtualnymi za pośrednictwem prywatnej komunikacji równorzędnej, wybierz kafelek **prywatne elementy równorzędne** na pulpicie nawigacyjnym. W tym miejscu możesz wybrać połączenie sieci wirtualnej i wyświetlić jego stan kondycji, wykresy trendu dotyczące utraty pakietów, wykorzystania przepustowości i opóźnień. 

![Komunikacja równorzędna ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Topologia obwodu 

Aby wyświetlić topologię obwodu, wybierz kafelek **topologia** . Ta akcja powoduje przejście do widoku topologia wybranego obwodu lub komunikacji równorzędnej. Diagram topologii zapewnia opóźnienie dla każdego segmentu w sieci, a każdy przeskok warstwy 3 jest reprezentowany przez węzeł diagramu. Wybranie przeskoku spowoduje wyświetlenie większej liczby szczegółów dotyczących przeskoku. Aby zwiększyć poziom widoczności w celu uwzględnienia przeskoków lokalnych, przesuń suwak w obszarze **filtry**. Przesuwanie paska suwaka w lewo lub w prawo powoduje zwiększenie lub zmniejszenie liczby przeskoków w grafie topologii. Opóźnienie w poszczególnych segmentach jest widoczne, co umożliwia szybszą izolację segmentów o dużej opóźnieniu w sieci.

![Topologia ExpressRoute](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Szczegółowy widok topologii obwodu 

Ten widok przedstawia połączenia sieci wirtualnej. 

![ExpressRoute połączenia sieci wirtualnej](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnostyka 

Network Performance Monitor pomaga zdiagnozować kilka problemów z łącznością z obwodem. Poniżej wymieniono niektóre problemy, które można wyświetlić.

Można wyświetlić kody powiadomień i ustawić dla nich alerty za pośrednictwem **LogAnalytics**. Na stronie **Diagnostyka npm** można zobaczyć opisy dla każdego wyzwalanego komunikatu diagnostycznego.

| Kod powiadomienia (dzienniki) | Opis |
| --- | --- |
| 5501 | Nie można przejść przez pomocnicze połączenie obwodu usługi ExpressRoute |
| 5502 | Nie można przejść przez podstawowe połączenie obwodu usługi ExpressRoute |
| 5503 | Nie znaleziono obwodu dla subskrypcji połączonej z obszarem roboczym | 
| 5508 | Nie można określić, czy ruch przechodzi przez wszystkie obwody ścieżki |
| 5510 | Ruch nie przechodzi przez zamierzony obwód | 
| 5511 | Ruch nie przechodzi przez zamierzoną sieć wirtualną | 

**Obwód nie działa.** Network Performance Monitor powiadamia, gdy tylko połączenie między zasobami lokalnymi i sieciami wirtualnymi platformy Azure zostanie utracone. To powiadomienie ułatwia podejmowanie aktywnej akcji przed odebraniem eskalacji użytkowników i skróceniem przestojów.

![Obwód ExpressRoute nie działa](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Ruch nie przechodzi przez zamierzony obwód.** Network Performance Monitor powiadamia użytkownika, gdy ruch nie przechodzi przez zamierzony obwód ExpressRoute. Ten problem może wystąpić, jeśli obwód nie działa i ruch przechodzi przez trasę kopii zapasowej. Może się również zdarzyć, że występuje problem z routingiem. Te informacje ułatwiają aktywne zarządzanie wszelkimi problemami z konfiguracją w zasadach routingu i upewnij się, że jest używana najbardziej optymalna i bezpieczna trasa. 

 

**Ruch, który nie przechodzi przez obwód podstawowy.** Network Performance Monitor powiadamia, gdy ruch przechodzi przez pomocniczy obwód ExpressRoute. Mimo że w tym przypadku nie wystąpią żadne problemy z łącznością, proaktywnie Rozwiązywanie problemów z obwodem podstawowym jest lepiej przygotowane. 

 
![Przepływ ruchu ExpressRoute](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Spadek wydajności z powodu szczytowego użycia.** Możesz skorelować trend wykorzystania przepustowości z trendem opóźnień, aby ustalić, czy spadek obciążenia platformy Azure jest spowodowany szczytem wykorzystania przepustowości. Następnie można odpowiednio podjąć odpowiednie działania.

![Wykorzystanie przepustowości ExpressRoute](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>Następne kroki
[Wyszukaj dzienniki](../logs/log-query-overview.md) , aby wyświetlić szczegółowe rekordy danych wydajności sieci.

