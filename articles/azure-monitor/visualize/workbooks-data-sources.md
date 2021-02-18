---
title: Azure Monitor skoroszyty ze źródłami danych | Dokumentacja firmy Microsoft
description: Uprość złożone raportowanie ze wstępnie skompilowanymi i niestandardowymi skoroszytami Azure Monitor utworzonymi na podstawie wielu źródeł danych
services: azure-monitor
documentationcenter: ''
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/29/2020
ms.openlocfilehash: ee4436d2b8445f2a1d368fb6c76e04d1ec790d98
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620730"
---
# <a name="azure-monitor-workbooks-data-sources"></a>Azure Monitor skoroszyty ze źródłami danych

Skoroszyty są zgodne z dużą liczbą źródeł danych. Ten artykuł przeprowadzi Cię przez źródła danych, które są obecnie dostępne dla Azure Monitor skoroszytów.

## <a name="logs"></a>Dzienniki

Skoroszyty umożliwiają wykonywanie zapytań względem dzienników z następujących źródeł:

* Dzienniki Azure Monitor (zasoby Application Insights i Log Analytics obszary robocze).
* Dane skoncentrowane na zasobach (dzienniki aktywności)

Autorzy skoroszytu mogą używać zapytań KQL, które przekształcają bazowe dane zasobów, aby wybrać zestaw wyników, który można wizualizować jako tekst, wykresy lub siatki.

![Zrzut ekranu przedstawiający interfejs raportu dzienników skoroszytów](./media/workbooks-data-sources/logs.png)

Autorzy skoroszytu mogą łatwo wykonywać zapytania dotyczące wielu zasobów, tworząc naprawdę ujednolicone, bogate środowisko raportowania.

## <a name="metrics"></a>Metryki

Zasoby platformy Azure emitują [metryki](../platform/data-platform-metrics.md) , do których można uzyskiwać dostęp za pośrednictwem skoroszytów. Dostęp do metryk w skoroszytach można uzyskać za pomocą wyspecjalizowanej kontrolki, która umożliwia określenie zasobów docelowych, odpowiednich metryk i ich agregacji. Te dane można następnie wykreślić na wykresach lub siatkach.

![Zrzut ekranu przedstawiający wykresy metryk skoroszytu dotyczące użycia procesora CPU](./media/workbooks-data-sources/metrics-graph.png)

![Zrzut ekranu przedstawiający interfejs metryk skoroszytu](./media/workbooks-data-sources/metrics.png)

## <a name="azure-resource-graph"></a>Azure Resource Graph

Skoroszyty obsługują zapytania dotyczące zasobów i ich metadanych przy użyciu usługi Azure Resource Graph (ARG). Ta funkcja jest używana głównie do tworzenia niestandardowych zakresów zapytań dla raportów. Zakres zasobów jest wyrażany za pomocą podzestawu KQL, który jest obsługiwany przez argument, który jest często wystarczający dla typowych przypadków użycia.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej Typ zapytania, aby wybrać pozycję Azure Resource Graph i wybrać subskrypcje docelowe. Za pomocą kontrolki zapytanie Dodaj wartość ARG KQL-podzestaw, która wybiera interesujący podzbiór zasobów.

![Zrzut ekranu przedstawiający zapytanie KQL wykresu zasobów platformy Azure](./media/workbooks-data-sources/azure-resource-graph.png)

## <a name="azure-resource-manager"></a>Azure Resource Manager

Skoroszyt obsługuje operacje REST Azure Resource Manager. Pozwala to na wysyłanie zapytań do punktu końcowego management.azure.com bez konieczności podawania własnego tokenu nagłówka autoryzacji.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej Źródło danych, aby wybrać Azure Resource Manager. Podaj odpowiednie parametry, takie jak metoda HTTP, ścieżka URL, nagłówki, parametry adresu URL i/lub treść.

> [!NOTE]
> Tylko `GET` `POST` `HEAD` operacje są obecnie obsługiwane.

## <a name="azure-data-explorer"></a>Azure Data Explorer

Skoroszyty obsługują teraz wykonywanie zapytań z klastrów [usługi Azure Eksplorator danych](/azure/data-explorer/) przy użyciu zaawansowanego języka zapytań [Kusto](/azure/kusto/query/index) .

![Zrzut ekranu przedstawiający okno zapytania Kusto](./media/workbooks-data-sources/data-explorer.png)

## <a name="workload-health"></a>Kondycja obciążenia

Azure Monitor ma funkcję, która aktywnie monitoruje dostępność i wydajność systemów operacyjnych gościa z systemem Windows lub Linux. Azure Monitor modeluje składniki i ich relacje, kryteria służące do mierzenia kondycji tych składników oraz składniki ostrzegające o wykryciu złej kondycji. Skoroszyty umożliwiają użytkownikom korzystanie z tych informacji w celu tworzenia rozbudowanych raportów interaktywnych.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej **typ zapytania** , aby wybrać opcję kondycja obciążenia i wybrać opcję subskrypcja, Grupa zasobów lub zasoby maszyn wirtualnych. Użyj listy rozwijanej filtr kondycji, aby wybrać interesujący podzbiór zdarzeń związanych z kondycją dla potrzeb analitycznych.

![Zrzut ekranu zapytania o alerty](./media/workbooks-data-sources/workload-health.png)

## <a name="azure-resource-health"></a>Usługa Azure Resource Health

Skoroszyty obsługują uzyskiwanie informacji o kondycji zasobów platformy Azure i łączenie ich z innymi źródłami danych w celu tworzenia zaawansowanych, interaktywnych raportów o kondycji

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej **typ zapytania** , aby wybrać opcję kondycja platformy Azure, a następnie wybierz zasoby docelowe. Użyj listy rozwijanej filtr kondycji, aby wybrać interesujący podzbiór problemów dotyczących zasobów dla potrzeb analitycznych.

![Zrzut ekranu zapytania alertów, który zawiera listy filtrów kondycji.](./media/workbooks-data-sources/resource-health.png)

## <a name="change-analysis-preview"></a>Analiza zmian (wersja zapoznawcza)

Aby kontrolka zapytania korzystała z [analizy zmian aplikacji](../app/change-analysis.md) jako źródła danych, Użyj listy rozwijanej *Źródło danych* i wybierz polecenie *Zmień analizę (wersja zapoznawcza)* i wybierz pojedynczy zasób. Można wyświetlić zmiany w ciągu ostatnich 14 dni. Lista rozwijana *poziomu* może służyć do filtrowania między zmianami "ważne", "normalne" i "zakłócenia", a ta lista rozwijana obsługuje parametry skoroszytu typu [listy rozwijanej](workbooks-dropdowns.md).

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu skoroszytu z analizą zmian](./media/workbooks-data-sources/change-analysis-data-source.png)

## <a name="merge-data-from-different-sources"></a>Scalanie danych z różnych źródeł

Często konieczne jest zebranie danych z różnych źródeł, które zwiększają możliwości wglądu w szczegółowe dane. Przykład rozszerza informacje o aktywnym alercie z powiązanymi danymi metryki. Dzięki temu użytkownicy mogą zobaczyć nie tylko efekt (aktywny Alert), ale również potencjalne przyczyny (na przykład duże użycie procesora). Domena monitorowania ma wiele takich skorelowanych źródeł danych, które często mają kluczowe znaczenie dla przepływu pracy Klasyfikacja i diagnostyki.

Skoroszyty to nie tylko zapytania o różne źródła danych, ale udostępniają także proste formanty umożliwiające Scalanie lub sprzęganie danych w celu uzyskania szczegółowych informacji. `merge`Formant jest sposobem osiągnięcia tego celu.

Poniższy przykład łączy dane alertów z danymi wydajności maszyny wirtualnej usługi log Analytics w celu uzyskania bogatej siatki szczegółowych informacji.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu skoroszytu z kontrolką scalania, który łączy dane alertu i usługi log Analytics](./media/workbooks-data-sources/merge-control.png)

Skoroszyty obsługują różne scalenia:

* Wewnętrzne unikatowe sprzężenie
* Pełne sprzężenie wewnętrzne
* Pełne sprzężenie zewnętrzne
* Lewe sprzężenie zewnętrzne
* Prawe sprzężenie zewnętrzne
* Lewe odłączanie
* Prawe odłączanie
* Lewe sprzężenie
* Prawo do przyłączania
* Unia
* Duplikuj tabelę

## <a name="json"></a>JSON

Dostawca JSON umożliwia utworzenie wyniku zapytania ze statycznej zawartości JSON. Jest najczęściej używany w parametrach do tworzenia parametrów listy rozwijanej wartości statycznych. Proste tablice JSON lub obiekty zostaną automatycznie przekonwertowane na wiersze siatki i kolumny.  W celu uzyskania bardziej szczegółowych zachowań można skonfigurować kolumny za pomocą karty wyniki i ustawień wykryto.

Ten dostawca obsługuje [wykryto](workbooks-jsonpath.md).

## <a name="alerts-preview"></a>Alerty (wersja zapoznawcza)

> [!NOTE]
> Sugerowany sposób wykonywania zapytań dotyczących informacji o alertach platformy Azure polega na użyciu źródła danych [grafu zasobów platformy Azure](#azure-resource-graph) , badając `AlertsManagementResources` tabelę.
>
> Przykłady można znaleźć w temacie [odwołanie do tabeli grafu zasobów platformy Azure](../../governance/resource-graph/reference/supported-tables-resources.md)lub [szablon alertów](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Azure%20Resources/Alerts/Alerts.workbook) .
>
> Źródło danych alertów pozostanie dostępne przez pewien czas podczas przejścia przez autorów do użycia argumentu ARG. Nie zaleca się używania tego źródła danych w szablonach. 

Skoroszyty umożliwiają użytkownikom wizualizację aktywnych alertów związanych ze swoimi zasobami. Ograniczenia: Źródło danych alertów wymaga dostępu do odczytu do subskrypcji w celu zbadania zasobów i może nie wyświetlać nowszych rodzajów alertów. 

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej _Źródło danych_ w celu wybrania _alertów (wersja zapoznawcza)_ i wybierz subskrypcje, grupy zasobów lub zasoby docelowe. Użyj listy rozwijanej filtr alertów, aby wybrać interesujący podzbiór alertów dla potrzeb analitycznych.

## <a name="custom-endpoint"></a>Niestandardowy punkt końcowy

Skoroszyty obsługują pobieranie danych z dowolnego źródła zewnętrznego. Jeśli dane znajdują się poza platformą Azure, możesz je przenieść do skoroszytów przy użyciu tego typu źródła danych.

Aby formant zapytania używał tego źródła danych, Użyj listy rozwijanej _Źródło danych_ w celu wybrania _niestandardowego punktu końcowego_. Podaj odpowiednie parametry, takie jak `Http method` , `url` , `headers` , `url parameters` i/lub `body` . Upewnij się, że źródło danych obsługuje mechanizm [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) w przeciwnym razie żądanie zakończy się niepowodzeniem.

Aby uniknąć automatycznego wykonywania wywołań niezaufanych hostów podczas korzystania z szablonów, użytkownik musi oznaczyć używane hosty jako zaufane. Można to zrobić przez kliknięcie przycisku _Dodaj jako zaufany_ lub dodanie go jako zaufanego hosta w ustawieniach skoroszytu. Te ustawienia zostaną zapisane w [przeglądarkach, które obsługują IndexDb z internetowymi](https://caniuse.com/#feat=indexeddb)procesami roboczymi.

> [!NOTE]
> Nie zapisuj żadnych wpisów tajnych w żadnym z pól ( `headers` , `parameters` , `body` , `url` ), ponieważ będą one widoczne dla wszystkich użytkowników skoroszytu.

Ten dostawca obsługuje [wykryto](workbooks-jsonpath.md).

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](../platform/workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](../platform/workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.
* [Wskazówki dotyczące optymalizacji zapytań Log Analytics](../log-query/query-optimization.md)