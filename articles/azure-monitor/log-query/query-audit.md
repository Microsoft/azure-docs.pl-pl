---
title: Inspekcja zapytań w dziennikach Azure Monitor
description: Szczegóły dzienników inspekcji zapytania dziennika, które udostępniają dane telemetryczne dotyczące zapytań dzienników działających w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/25/2020
ms.openlocfilehash: cb38dcba2f61a432decb56164b816688ad3192d8
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/26/2020
ms.locfileid: "88893751"
---
# <a name="audit-queries-in-azure-monitor-logs-preview"></a>Inspekcja zapytań w dziennikach Azure Monitor (wersja zapoznawcza)
Dzienniki inspekcji zapytań dzienników udostępniają dane telemetryczne dotyczące zapytań dzienników uruchomionych w Azure Monitor. Obejmuje to takie informacje, jak w przypadku uruchomienia zapytania, jego uruchomienia, używanego narzędzia, tekstu zapytania i statystyki wydajności opisującej wykonywanie zapytania.

## <a name="current-limitations"></a>Bieżące ograniczenia
W publicznej wersji zapoznawczej obowiązują następujące ograniczenia:

- Rejestrowane są tylko zapytania skoncentrowane na obszarze roboczym. Zapytania są uruchamiane w trybie skoncentrowanym na zasobach lub uruchamiane w oparciu o Application Insights, które nie są skonfigurowane jako oparte na obszarze roboczym, nie będą rejestrowane.


## <a name="configure-query-auditing"></a>Skonfiguruj inspekcję zapytań
Inspekcja zapytań jest włączona z [ustawieniem diagnostycznym](../platform/diagnostic-settings.md) w obszarze roboczym log Analytics. Dzięki temu możesz wysyłać dane inspekcji do bieżącego obszaru roboczego lub dowolnego innego obszaru roboczego w ramach subskrypcji do usługi Azure Event Hubs do wysyłania poza platformą Azure lub do usługi Azure Storage w celu archiwizacji. 

### <a name="azure-portal"></a>Azure Portal
Dostęp do ustawień diagnostycznych dla obszaru roboczego Log Analytics w Azure Portal w jednej z następujących lokalizacji:

- Z menu **Azure monitor** wybierz pozycję **Ustawienia diagnostyczne**, a następnie Znajdź i wybierz obszar roboczy.

    [![Azure Monitor ](media/log-query-audit/diagnostic-setting-monitor.png) ustawień diagnostycznych ](media/log-query-audit/diagnostic-setting-monitor.png#lightbox) 

- Z menu **log Analytics obszary robocze** wybierz obszar roboczy, a następnie wybierz pozycję **Ustawienia diagnostyczne**.

    [![Obszar roboczy ](media/log-query-audit/diagnostic-setting-workspace.png) ustawień diagnostycznych log Analytics ](media/log-query-audit/diagnostic-setting-workspace.png#lightbox) 

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Możesz uzyskać przykładowy szablon Menedżer zasobów z [ustawień diagnostycznych dla log Analytics obszaru roboczego](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-log-analytics-workspace).

## <a name="audit-data"></a>Inspekcja danych
Rekord inspekcji jest tworzony przy każdym uruchomieniu zapytania. Jeśli dane są wysyłane do obszaru roboczego Log Analytics, są one przechowywane w tabeli o nazwie *LAQueryLogs*. W poniższej tabeli opisano właściwości w poszczególnych rekordach danych inspekcji.

| Pole | Opis |
|:---|:---|
| TimeGenerated         | Czas UTC wysłania zapytania. |
| CorrelationId         | Unikatowy identyfikator identyfikujący zapytanie. Może być używany w scenariuszach rozwiązywania problemów podczas kontaktowania się z firmą Microsoft w celu uzyskania pomocy. |
| AADObjectId           | Identyfikator Azure Active Directory konta użytkownika, które uruchomiło zapytanie.  |
| AADTenantId           | Identyfikator dzierżawy konta użytkownika, które uruchomiło zapytanie.  |
| AADEmail              | Adres e-mail dzierżawy konta użytkownika, które uruchomiło zapytanie.  |
| AADClientId           | Identyfikator i rozpoznana nazwa aplikacji użytej do uruchomienia zapytania. |
| RequestClientApp      | Rozpoznana nazwa aplikacji użyta do uruchomienia zapytania. |
| QueryTimeRangeStart   | Początek zakresu czasu wybranego dla zapytania. Ta wartość może nie być wypełniana w niektórych scenariuszach, na przykład gdy zapytanie jest uruchamiane z Log Analytics, a zakres czasu jest określony wewnątrz zapytania, a nie w selektorze czasu. |
| QueryTimeRangeEnd     | Koniec zakresu czasu wybranego dla zapytania. Ta wartość może nie być wypełniana w niektórych scenariuszach, na przykład gdy zapytanie jest uruchamiane z Log Analytics, a zakres czasu jest określony wewnątrz zapytania, a nie w selektorze czasu.  |
| QueryText             | Tekst zapytania, które zostało uruchomione. |
| RequestTarget         | Adres URL interfejsu API został użyty do przesłania zapytania.  |
| RequestContext        | Lista zasobów, względem których zażądano uruchomienia zapytania. Zawiera do trzech tablic ciągów: obszary robocze, aplikacje i zasoby. Subskrypcje lub grupy zasobów zapytania kierowane będą wyświetlane jako *zasoby*. Zawiera obiekt docelowy implikowany przez RequestTarget. |
| RequestContextFilters | Zestaw filtrów określony jako część wywołania zapytania. Obejmuje maksymalnie trzy możliwe tablice ciągów:<br>-ResourceTypes-typ zasobu, aby ograniczyć zakres zapytania<br>-Obszary robocze — lista obszarów roboczych, które ograniczają zapytanie do programu<br>-WorkspaceRegions-lista regionów obszaru roboczego, aby ograniczyć zapytanie |
| ResponseCode          | Kod odpowiedzi HTTP zwrócony podczas przesyłania zapytania. |
| ResponseDurationMs    | Godzina zwrócenia odpowiedzi.  |
| StatsCPUTimeMs       | Łączny czas obliczeń używany do obliczania, analizowania i pobierania danych. Wypełniane tylko wtedy, gdy kwerenda zwraca kod stanu 200. |
| StatsDataProcessedKB | Ilość danych, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na rozmiar tabeli docelowej, użyty zakres czasu, zastosowane filtry i liczbę kolumn, do których istnieją odwołania. Wypełniane tylko wtedy, gdy kwerenda zwraca kod stanu 200. |
| StatsDataProcessedStart | Czas najstarszych danych, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na zapytanie o jawny zakres czasu i filtry. Może to być większe niż jawne przedział czasu z powodu partycjonowania danych. Wypełniane tylko wtedy, gdy kwerenda zwraca kod stanu 200. |
| StatsDataProcessedEnd  |Godzina najnowszych danych, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na zapytanie o jawny zakres czasu i filtry. Może to być większe niż jawne przedział czasu z powodu partycjonowania danych. Wypełniane tylko wtedy, gdy kwerenda zwraca kod stanu 200. |
| StatsWorkspaceCount | Liczba obszarów roboczych, do których uzyskuje dostęp zapytanie. Wypełniane tylko wtedy, gdy kwerenda zwraca kod stanu 200. |
| StatsRegionCount | Liczba regionów, do których uzyskuje dostęp zapytanie. Wypełniane tylko wtedy, gdy kwerenda zwraca kod stanu 200. |



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat [ustawień diagnostycznych](../platform/diagnostic-settings.md).
- Dowiedz się więcej na temat [optymalizowania zapytań dzienników](query-optimization.md).