---
title: Azure Monitor parametry czasu dla skoroszytów
description: Dowiedz się, jak ustawić parametry czasu, aby umożliwić użytkownikom ustawianie kontekstu czasu analizy. Parametry czasu są używane przez niemal wszystkie raporty.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: 9f4f5f82423f0ecb4e810eab2d675e95b8b66763
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101717166"
---
# <a name="workbook-time-parameters"></a>Parametry czasu skoroszytu

Parametry czasu umożliwiają użytkownikom ustawienie kontekstu czasu analizy i jest używany przez niemal wszystkie raporty. Jest stosunkowo prosta w konfiguracji i użycia — umożliwienie autorom określenia zakresów czasu do wyświetlenia na liście rozwijanej, włącznie z opcją dla niestandardowych zakresów czasowych. 

## <a name="creating-a-time-parameter"></a>Tworzenie parametru czasu
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `TimeRange`
    2. Typ parametru: `Time range picker`
    3. Wymagane: `checked`
    4. Dostępne zakresy czasu: Ostatnia godzina, ostatnie 12 godzin, ostatnie 24 godziny, ostatnie 48 godzin, ostatnie 3 dni, ostatnie 7 dni i Zezwalaj na wybór niestandardowego zakresu czasu
5. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

    ![Obraz przedstawiający tworzenie parametru zakresu czasu](./media/workbooks-time/time-settings.png)

W ten sposób skoroszyt będzie wyglądał jak w trybie odczytu.

![Obraz przedstawiający parametr zakresu czasu w trybie odczytu](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Odwoływanie się do parametru czasu
### <a name="via-bindings"></a>Za pośrednictwem powiązań
1. Dodaj kontrolkę zapytania do skoroszytu i wybierz zasób Application Insights.
2. Większość formantów skoroszytu obsługuje selektor zakresu _czasu_ . Otwórz listę rozwijaną _zakres czasu_ i wybierz `{TimeRange}` w grupie parametry czasu w dolnej części.
3. To wiąże parametr zakresu czasu z zakresem czasu wykresu. Zakres czasu zapytania przykładowego to teraz ostatnie 24 godziny.
4. Uruchom zapytanie, aby zobaczyć wyniki

    ![Obraz przedstawiający parametr zakresu czasu przywoływany przez powiązania](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>W KQL
1. Dodaj kontrolkę zapytania do skoroszytu i wybierz zasób Application Insights.
2. W KQL Wprowadź filtr zakresu czasu przy użyciu parametru: `| where timestamp {TimeRange}`
3. Spowoduje to rozwinięcie czasu oceny zapytania do `| where timestamp > ago(1d)` , który jest wartością zakresu czasu parametru.
4. Uruchom zapytanie, aby zobaczyć wyniki

    ![Obraz przedstawiający zakres czasu przywoływany w KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>W tekście 
1. Dodaj kontrolkę tekstową do skoroszytu.
2. W obszarze promocji wprowadź `The chosen time range is {TimeRange:label}`
3. Wybierz _gotowe do edycji_
4. Kontrolka Text wyświetli tekst: _wybrany zakres czasu to ostatnie 24 godziny_

## <a name="time-parameter-options"></a>Opcje parametrów czasu
| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etykieta zakresu czasu | Ostatnie 24 godziny |
| `{TimeRange:label}` | Etykieta zakresu czasu | Ostatnie 24 godziny |
| `{TimeRange:value}` | Wartość zakresu czasu | > temu (1D) |
| `{TimeRange:query}` | Zapytanie dotyczące zakresu czasu | > temu (1D) |
| `{TimeRange:start}` | Czas rozpoczęcia zakresu czasu | 3/20/2019 4:18 PM |
| `{TimeRange:end}` | Czas zakończenia zakresu czasu | 3/21/2019 4:18 PM |
| `{TimeRange:grain}` | Ziarno zakresu czasu | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Używanie opcji parametrów w zapytaniu
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](./workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.