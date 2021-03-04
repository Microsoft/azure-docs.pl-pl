---
title: Wyświetlanie zależności aplikacji za pomocą usługi VM Insights
description: Mapa to funkcja szczegółowych informacji o maszynach wirtualnych. Automatycznie odnajduje składniki aplikacji w systemach Windows i Linux oraz mapuje komunikację między usługami. Ten artykuł zawiera szczegółowe informacje dotyczące korzystania z funkcji map w różnych scenariuszach.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: 267ec0ed01fb85e9e29b0b8c9294d20d610db8fb
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046571"
---
# <a name="use-the-map-feature-of-vm-insights-to-understand-application-components"></a>Użyj funkcji map usługi VM Insights, aby zrozumieć składniki aplikacji
W usłudze site Insights można wyświetlać odnalezione składniki aplikacji na maszynach wirtualnych z systemem Windows i Linux, które działają na platformie Azure lub w środowisku. Można obserwować maszyny wirtualne na dwa sposoby. Wyświetl mapę bezpośrednio z maszyny wirtualnej lub Wyświetl mapę z Azure Monitor, aby wyświetlić składniki w różnych grupach maszyn wirtualnych. Ten artykuł pomoże Ci zrozumieć te dwie metody wyświetlania i sposób korzystania z funkcji map. 

Aby uzyskać informacje o konfigurowaniu szczegółowych informacji o maszynach wirtualnych, zobacz [Włączanie usługi VM Insights](./vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Wprowadzenie do obsługi mapy
Przed przekazaniem do środowiska mapy należy zrozumieć, jak prezentuje i wizualizuje informacje. Niezależnie od tego, czy funkcja map jest wybierana bezpośrednio z maszyny wirtualnej, czy z Azure Monitor, funkcja map przedstawia spójne środowisko. Jedyną różnicą jest to, że z Azure Monitor jedna mapa pokazuje wszystkie elementy członkowskie aplikacji lub klastra wielowarstwowego.

Funkcja map wizualizowa zależności maszyn wirtualnych przez odnajdywanie uruchomionych procesów z: 

- Aktywne połączenia sieciowe między serwerami.
- Opóźnienie połączenia przychodzącego i wychodzącego.
- Porty dla dowolnej architektury połączonej z protokołem TCP w określonym przedziale czasu.  
 
Rozwiń maszynę wirtualną, aby wyświetlić szczegóły procesu i tylko te procesy, które komunikują się z maszyną wirtualną. Grupa klientów pokazuje liczbę klientów frontonu, którzy łączą się z maszyną wirtualną. Grupy portów serwera przedstawiają liczbę serwerów zaplecza, z którymi łączy się maszyna wirtualna. Rozwiń grupę portów serwera, aby wyświetlić szczegółową listę serwerów, które łączą się za pośrednictwem tego portu.  

Po wybraniu maszyny wirtualnej w okienku **Właściwości** po prawej stronie są wyświetlane właściwości maszyny wirtualnej. Właściwości obejmują informacje o systemie zgłoszone przez system operacyjny, właściwości maszyny wirtualnej platformy Azure oraz wykres pierścieniowy, który podsumowuje odnalezione połączenia. 

![Okienko właściwości](./media/vminsights-maps/properties-pane-01.png)

Po prawej stronie okienka wybierz pozycję **Rejestruj zdarzenia** , aby wyświetlić listę danych wysłanych przez maszynę wirtualną do Azure monitor. Te dane są dostępne do wykonywania zapytań.  Wybierz dowolny typ rekordu, aby otworzyć stronę **dzienniki** , gdzie zobaczysz wyniki dla tego typu rekordu. Zobaczysz również wstępnie skonfigurowane zapytanie, które jest filtrowane względem maszyny wirtualnej.  

![Okienko zdarzenia dziennika](./media/vminsights-maps/properties-pane-logs-01.png)

Zamknij stronę **dzienniki** i wróć do okienka **Właściwości** . W tym celu wybierz pozycję **alerty** , aby wyświetlić alerty kryteriów kondycji maszyny wirtualnej. Funkcja map integruje się z alertami platformy Azure w celu wyświetlenia alertów dla wybranego serwera w wybranym zakresie czasu. Na serwerze zostanie wyświetlona ikona bieżące alerty, a w okienku **alerty komputera** są wyświetlane alerty. 

![Okienko alerty](./media/vminsights-maps/properties-pane-alerts-01.png)

Aby funkcja mapy wyświetlała odpowiednie alerty, Utwórz regułę alertu, która ma zastosowanie do określonego komputera:

- Dołącz klauzulę do grupowania alertów według komputera (na przykład **według interwału komputerowego 1 minuty**).
- Podstawowy alert dotyczący metryki.

Aby uzyskać więcej informacji na temat alertów platformy Azure i tworzenia reguł alertów, zobacz [ujednolicone alerty w Azure monitor](../alerts/alerts-overview.md).

W prawym górnym rogu opcja **Legenda** opisuje symbole i role na mapie. Aby bliżej obejrzeć mapę i przenieść ją wokół siebie, Użyj kontrolek powiększenia w prawym dolnym rogu. Możesz ustawić poziom powiększenia i dopasować mapę do rozmiaru strony.  

## <a name="connection-metrics"></a>Metryki połączeń
W okienku **połączenia** są wyświetlane metryki standardowe dla wybranego połączenia z maszyny wirtualnej za pośrednictwem portu TCP. Metryki obejmują czas odpowiedzi, żądania na minutę, przepływność ruchu i linki.  

![Wykresy połączeń sieciowych w okienku połączenia](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Nieudane połączenia
Mapa pokazuje nieudane połączenia dla procesów i komputerów. Czerwona linia czerwony oznacza, że system klienta nie dociera do procesu lub portu. W przypadku systemów, które używają agenta zależności, Agent zgłasza nieudane próby połączenia. Funkcja map monitoruje proces, obserwując gniazda TCP, które nie mogą nawiązać połączenia. Przyczyną tego błędu może być Zapora, błędna konfiguracja klienta lub serwera lub niedostępna usługa zdalna.

![Nieudane połączenie na mapie](./media/vminsights-maps/map-group-failed-connection-01.png)

Zrozumienie nieudanych połączeń może pomóc w rozwiązywaniu problemów, weryfikowaniu migracji, analizowaniu zabezpieczeń i zrozumieniu ogólnej architektury usługi. Nieudane połączenia są czasami nieszkodliwe, ale często wskazują na problem. Połączenia mogą się nie powieść, na przykład w sytuacji, gdy środowisko trybu failover nagle staje się niedostępne lub gdy dwie warstwy aplikacji nie mogą komunikować się ze sobą po migracji do chmury.

### <a name="client-groups"></a>Grupy klientów
Na mapie grupy klientów reprezentują komputery klienckie, które łączą się z zmapowaną maszyną. Pojedyncza grupa klientów reprezentuje klientów dla danego procesu lub komputera.

![Grupa klientów na mapie](./media/vminsights-maps/map-group-client-groups-01.png)

Aby wyświetlić monitorowanych klientów i adresy IP systemów w grupie klientów, wybierz grupę. Zawartość grupy zostanie wyświetlona poniżej.  

![Lista adresów IP grup klientów na mapie](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Jeśli grupa zawiera monitorowanych i niemonitorowanych klientów, można wybrać odpowiednią sekcję wykresu pierścieniowego grupy, aby odfiltrować klientów.

### <a name="server-port-groups"></a>Grupy portów serwera
Grupy portów serwera reprezentują porty na serwerach, które mają połączenia przychodzące z zamapowanej maszyny. Grupa zawiera port serwera i liczbę serwerów, które mają połączenia z tym portem. Wybierz grupę, aby wyświetlić poszczególne serwery i połączenia. 

![Grupa portów serwera na mapie](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Jeśli grupa zawiera monitorowane i niemonitorowane serwery, możesz wybrać odpowiednią sekcję wykresu pierścieniowego grupy, aby odfiltrować serwery.

## <a name="view-a-map-from-a-vm"></a>Wyświetlanie mapy z poziomu maszyny wirtualnej 

Aby uzyskać dostęp do usługi VM Insights bezpośrednio z maszyny wirtualnej:

1. W Azure Portal wybierz pozycję **Virtual Machines**. 
2. Z listy wybierz maszynę wirtualną. W sekcji **monitorowanie** wybierz pozycję **szczegółowe** dane.  
3. Wybierz kartę **Mapa** .

Mapa wizualizuje zależności maszyn wirtualnych przez odnajdywanie uruchomionych grup procesów i procesów, które mają aktywne połączenia sieciowe w określonym przedziale czasu.  

Domyślnie mapa pokazuje ostatnie 30 minut. Aby zobaczyć, jak wyszukiwane są zależności w przeszłości, można wykonać zapytanie o historyczne zakresy czasu o wartości do godziny. Aby uruchomić zapytanie, użyj selektora **TimeRange** w lewym górnym rogu. Na przykład podczas zdarzenia lub przed zmianą może zostać uruchomione zapytanie.  

![Zrzut ekranu karty Mapa w sekcji monitorowanie szczegółowych informacji Azure Portal przedstawiający diagram zależności między maszynami wirtualnymi.](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Wyświetlanie mapy z zestawu skalowania maszyn wirtualnych

Aby uzyskać dostęp do usługi VM Insights bezpośrednio z zestawu skalowania maszyn wirtualnych:

1. W Azure Portal wybierz pozycję **zestawy skalowania maszyn wirtualnych**.
2. Z listy wybierz maszynę wirtualną. Następnie w sekcji **monitorowanie** wybierz pozycję **szczegółowe** dane.  
3. Wybierz kartę **Mapa** .

Mapa wizualizuje wszystkie wystąpienia w zestawie skalowania jako węzeł grupy wraz z zależnościami grupy. Rozwinięty węzeł zawiera listę wystąpień w zestawie skalowania. Można przewijać te wystąpienia 10 w danym momencie. 

Aby załadować mapę dla określonego wystąpienia, najpierw wybierz to wystąpienie na mapie. Następnie wybierz przycisk **wielokropka** (...) z prawej strony i wybierz polecenie **Załaduj mapę serwera**. W wyświetlonej mapie widoczne są grupy procesów i procesy, które mają aktywne połączenia sieciowe w określonym przedziale czasu. 

Domyślnie mapa pokazuje ostatnie 30 minut. Aby zobaczyć, jak wyszukiwane są zależności w przeszłości, można wykonać zapytanie o historyczne zakresy czasu o wartości do godziny. Aby uruchomić zapytanie, użyj selektora **TimeRange** . Na przykład podczas zdarzenia lub przed zmianą może zostać uruchomione zapytanie.

![Zrzut ekranu karty Mapa w sekcji monitorowanie szczegółowych informacji Azure Portal przedstawiający diagram zależności między zestawami skalowania maszyn wirtualnych.](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Możesz również uzyskać dostęp do mapy dla określonego wystąpienia z widoku **wystąpień** dla zestawu skalowania maszyn wirtualnych. W sekcji **Ustawienia** przejdź do pozycji informacje o **wystąpieniach**  >  .

## <a name="view-a-map-from-azure-monitor"></a>Wyświetl mapę z Azure Monitor

W Azure Monitor funkcja map zapewnia globalny widok maszyn wirtualnych i ich zależności. Aby uzyskać dostęp do funkcji map w Azure Monitor:

1. W Azure Portal wybierz pozycję **Monitoruj**. 
2. W sekcji **szczegółowe informacje** wybierz **Virtual Machines**.
3. Wybierz kartę **Mapa** .

   ![Mapa Azure Monitor przegląd wielu maszyn wirtualnych](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Wybierz obszar roboczy przy użyciu selektora **obszaru roboczego** w górnej części strony. Jeśli masz więcej niż jeden obszar roboczy Log Analytics, wybierz obszar roboczy, który jest włączony w rozwiązaniu i który ma do niego raportowanie maszyn wirtualnych. 

Selektor **grup** zwraca subskrypcje, grupy zasobów, [grupy komputerów](../logs/computer-groups.md)i zestawy skalowania maszyn wirtualnych, które są powiązane z wybranym obszarem roboczym. Wybór ma zastosowanie tylko do funkcji map i nie jest przenoszone na wydajność ani kondycję.

Domyślnie mapa pokazuje ostatnie 30 minut. Aby zobaczyć, jak wyszukiwane są zależności w przeszłości, można wykonać zapytanie o historyczne zakresy czasu o wartości do godziny. Aby uruchomić zapytanie, użyj selektora **TimeRange** . Na przykład podczas zdarzenia lub przed zmianą może zostać uruchomione zapytanie.  

## <a name="next-steps"></a>Następne kroki

Aby zidentyfikować wąskie gardła, sprawdzić wydajność i zrozumieć ogólne wykorzystanie maszyn wirtualnych, zobacz [Wyświetlanie stanu wydajności dla szczegółowych informacji o maszynie wirtualnej](vminsights-performance.md).
