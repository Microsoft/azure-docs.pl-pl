---
title: Agent Health rozwiązanie w Azure Monitor | Microsoft Docs
description: Ten artykuł ma na celu ułatwienie zrozumienia sposobu korzystania z tego rozwiązania do monitorowania kondycji agentów raportowanych bezpośrednio do Log Analytics lub System Center Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2020
ms.openlocfilehash: a498c229acce9359acfb4593ec5f833000ca2c39
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573376"
---
#  <a name="agent-health-solution-in-azure-monitor"></a>Agent Health rozwiązanie w Azure Monitor
Agent Health rozwiązanie na platformie Azure pomaga zrozumieć, że wszyscy agenci są raportowani bezpośrednio do Log Analytics obszaru roboczego w Azure Monitor lub System Center Operations Manager grupie zarządzania połączonej z Azure Monitor, które nie odpowiadają i przesyłają dane operacyjne.  Można także śledzić liczbę wdrożonych agentów i ich geograficzne rozmieszczenie oraz wykonywać inne zapytania dające informacje na temat rozmieszczenia agentów wdrożonych na platformie Azure, w innych środowiskach w chmurze i lokalnie.    

## <a name="prerequisites"></a>Wymagania wstępne
Przed wdrożeniem tego rozwiązania upewnij się, że obecnie są obsługiwane raporty [agenci systemu Windows](../agents/agent-windows.md) w obszarze roboczym log Analytics lub raportowanie do [Operations Manager grupy zarządzania](../agents/om-agents.md) zintegrowanej z Twoim obszarem roboczym.

## <a name="solution-components"></a>Składniki rozwiązania
To rozwiązanie składa się z następujących zasobów, które są dodawane do Twojego obszaru roboczego, i bezpośrednio połączonych agentów lub grupy zarządzania połączonej z programem Operations Manager.

### <a name="management-packs"></a>Pakiety administracyjne
Jeśli grupa zarządzania System Center Operations Manager jest połączona z obszarem roboczym Log Analytics, następujące pakiety administracyjne są instalowane w Operations Manager.  Te pakiety administracyjne są również instalowane na bezpośrednio połączonych komputerach z systemem Windows po dodaniu tego rozwiązania. W przypadku tych pakietów administracyjnych nie trzeba niczego konfigurować ani niczym zarządzać.

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack  (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer).  

Aby uzyskać więcej informacji na temat aktualizowania pakietów administracyjnych rozwiązania, zobacz artykuł [Connect Operations Manager to Log Analytics](../agents/om-agents.md) (Połączenie programu Operations Manager z usługą Log Analytics).

## <a name="configuration"></a>Konfigurowanie
Dodaj Agent Health rozwiązanie do obszaru roboczego Log Analytics przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań](solutions.md). Nie są wymagane żadne dalsze czynności konfiguracyjne.


## <a name="data-collection"></a>Zbieranie danych
### <a name="supported-agents"></a>Obsługiwani agenci
W poniższej tabeli opisano połączone źródła, które obsługuje to rozwiązanie.

| Połączone źródło | Obsługiwane | Opis |
| --- | --- | --- |
| Agenci dla systemu Windows | Tak | Zdarzenia pulsu są zbierane z bezpośrednich agentów systemu Windows.|
| Grupa zarządzania programu System Center Operations Manager | Tak | Zdarzenia pulsu są zbierane z agentów zgłaszanych do grupy zarządzania co 60 sekund, a następnie przekazywane do Azure Monitor. Bezpośrednie połączenie od agentów Operations Manager do Azure Monitor nie jest wymagane. Dane zdarzenia pulsu są przekazywane z grupy zarządzania do obszaru roboczego Log Analytics.|

## <a name="using-the-solution"></a>Użycie rozwiązania
Po dodaniu rozwiązania do obszaru roboczego Log Analytics kafelek **Agent Health** zostanie dodany do pulpitu nawigacyjnego. Ten kafelek pokazuje całkowitą liczbę agentów oraz liczbę nieodpowiadających agentów w ciągu ostatnich 24 godzin.<br><br> ![Kafelek rozwiązania Agent Health na pulpicie nawigacyjnym](./media/solution-agenthealth/agenthealth-solution-tile-homepage.png)

Kliknij kafelek **Agent Health**, aby otworzyć pulpit nawigacyjny rozwiązania **Agent Health**.  Na pulpicie nawigacyjnym znajdują się kolumny wymienione w poniższej tabeli. Każda kolumna zawiera listę dziesięciu najważniejszych zdarzeń według liczby odpowiadającej kryteriom tej kolumny dla określonego zakresu czasu. Możesz uruchomić przeszukiwanie rejestru zwracające pełną listę, wybierając pozycję **Zobacz wszystko** w prawej dolnej części kolumny lub klikając kolumnę nagłówka.

| Kolumna | Opis |
|--------|-------------|
| Liczba agentów w miarę upływu czasu | Trend liczby agentów w okresie siedmiu dni dla agentów systemu Linux i Windows.|
| Liczba nieodpowiadających agentów | Lista agentów, którzy nie wysłali pulsu w ciągu ostatnich 24 godzin.|
| Rozkład według typu systemu operacyjnego | Rozkład liczby agentów systemu Windows i Linux w Twoim środowisku.|
| Rozkład według wersji agenta | Rozkład różnych wersji agentów zainstalowanych w Twoim środowisku i liczba agentów dla każdej z nich.|
| Rozkład według kategorii agenta | Rozkład różnych kategorii agentów wysyłających zdarzenia pulsu: bezpośredni agenci, agenci programu OpsMgr i serwer zarządzania programu OpsMgr.|
| Rozkład według grupy zarządzania | Partycja różnych Operations Managerych grup zarządzania w danym środowisku.|
| Lokalizacja geograficzna agentów | Partycja różnych krajów/regionów, w których masz agentów i łączna liczba agentów zainstalowanych w poszczególnych krajach/regionach.|
| Liczba zainstalowanych bram | Liczba serwerów, na których zainstalowano bramę Log Analytics, oraz listę tych serwerów.|

![Przykład pulpitu nawigacyjnego rozwiązania Agent Health](./media/solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="azure-monitor-log-records"></a>Azure Monitor rekordy dziennika
Rozwiązanie tworzy jeden typ rekordu w obszarze roboczym Log Analytics.  

### <a name="heartbeat-records"></a>Rekordy Heartbeat
Tworzony jest rekord o typie **Heartbeat**.  Te rekordy mają właściwości podane w poniższej tabeli.  

| Właściwość | Opis |
| --- | --- |
| `Type` | *Puls*|
| `Category` | Wartością jest *Direct Agent*, *SCOM Agent* lub *SCOM Management Server*.|
| `Computer` | Nazwa komputera.|
| `OSType` | System operacyjny Windows lub Linux.|
| `OSMajorVersion` | Wersja główna systemu operacyjnego.|
| `OSMinorVersion` | Wersja pomocnicza systemu operacyjnego.|
| `Version` | Log Analytics agenta lub Operations Manager wersja agenta.|
| `SCAgentChannel` | Wartością jest *Direct* i/lub *SCManagementServer*.|
| `IsGatewayInstalled` | Jeśli zainstalowano bramę Log Analytics, wartość jest *równa true*; w przeciwnym razie wartość jest *równa false*.|
| `ComputerIP` | Publiczny adres IP komputera. Na maszynach wirtualnych platformy Azure zostanie wyświetlony publiczny adres IP, jeśli jest dostępny. W przypadku maszyn wirtualnych używających prywatnych adresów IP spowoduje to wyświetlenie adresu usługi Azure translator adresów sieciowych (nie jest to prywatny adres.). |
| `RemoteIPCountry` | Lokalizacja geograficzna, w której wdrożony jest komputer.|
| `ManagementGroupName` | Nazwa grupy zarządzania programu Operations Manager.|
| `SourceComputerId` | Unikatowy identyfikator komputera.|
| `RemoteIPLongitude` | Długość geograficzna lokalizacji geograficznej komputera.|
| `RemoteIPLatitude` | Szerokość geograficzna lokalizacji geograficznej komputera.|

Każdy agent raportowany do Operations Managerego serwera zarządzania wyśle dwa pulsy, a wartość właściwości SCAgentChannel będzie zawierać zarówno **bezpośrednie** , jak i **SCManagementServer** , w zależności od tego, jakie źródła danych i rozwiązania monitorowania zostały włączone w ramach subskrypcji. W przypadku odwołania dane z rozwiązań są wysyłane bezpośrednio z Operations Manager serwera zarządzania do Azure Monitor lub z powodu ilości danych zbieranych w agencie są wysyłane bezpośrednio z agenta do Azure Monitor. W przypadku zdarzeń pulsu, które mają wartość **SCManagementServer**, wartość ComputerIP jest adresem IP serwera zarządzania, ponieważ dane są faktycznie przekazywane przez niego.  W przypadku pulsów z parametrem SCAgentChannel ustawionym na wartość **Direct** jest to publiczny adres IP agenta.  

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników
Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów zbieranych przez to rozwiązanie.

| Zapytanie | Opis |
|:---|:---|
| Heartbeat &#124; distinct Computer |Łączna liczba agentów |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |Liczba nieodpowiadających agentów w ciągu ostatnich 24 godzin |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |Liczba nieodpowiadających agentów w ciągu ostatnich 15 minut |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Komputery w trybie online (w ciągu ostatnich 24 godzin) |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |Całkowita liczba agentów w trybie offline w ciągu ostatnich 30 minut (dla ostatnich 24 godzin) |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Pobierz trend liczby agentów w miarę upływu czasu według wartości OSType|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |Rozkład według typu systemu operacyjnego |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |Rozkład według wersji agenta |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |Rozkład według kategorii agenta |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | Rozkład według grupy zarządzania |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |Lokalizacja geograficzna agentów |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |Liczba zainstalowanych bram Log Analytics |




## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [alertach w Azure monitor](../alerts/alerts-overview.md) , aby uzyskać szczegółowe informacje na temat generowania alertów z zapytań dzienników. 

