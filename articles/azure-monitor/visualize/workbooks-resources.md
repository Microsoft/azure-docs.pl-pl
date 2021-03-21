---
title: Azure Monitornie parametrów zasobów skoroszytów
description: Dowiedz się, jak używać parametrów zasobów, aby zezwalać na pobieranie zasobów w skoroszytach. Użyj parametrów zasobów, aby ustawić zakres, z którego mają zostać pobrane dane.
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.openlocfilehash: d4a6d04f88544b3a4f9f9c0c0b290d17f5ef148d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725581"
---
# <a name="workbook-resource-parameters"></a>Parametry zasobów skoroszytu

Parametry zasobów umożliwiają pobieranie zasobów w skoroszytach. Jest to przydatne w przypadku ustawiania zakresu, z którego mają zostać pobrane dane. Przykładem jest umożliwienie użytkownikom wybrania zestawu maszyn wirtualnych, których wykresy później będą używać podczas prezentowania danych.

Wartości z funkcji wyboru zasobów mogą pochodzić z kontekstu skoroszytu, listy statycznej lub zapytań dotyczących wykresów zasobów platformy Azure.

## <a name="creating-a-resource-parameter-workbook-resources"></a>Tworzenie parametru zasobu (zasoby skoroszytu)
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `Applications`
    2. Typ parametru: `Resource picker`
    3. Wymagane: `checked`
    4. Zezwalaj na wybór wielokrotny: `checked`
5. Pobierz dane z: `Workbook Resources`
6. Uwzględnij tylko typy zasobów: `Application Insights`
7. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

![Obraz przedstawiający tworzenie parametru zasobu przy użyciu zasobów skoroszytu](./media/workbooks-resources/resource-create.png)

## <a name="creating-a-resource-parameter-azure-resource-graph"></a>Tworzenie parametru zasobu (Wykres zasobów platformy Azure)
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `Applications`
    2. Typ parametru: `Resource picker`
    3. Wymagane: `checked`
    4. Zezwalaj na wybór wielokrotny: `checked`
5. Pobierz dane z: `Query`
    1. Typ zapytania: `Azure Resource Graph`
    2. Opłaty `Use default subscriptions`
    3. W kontrolce zapytania Dodaj ten fragment kodu
    ```kusto
    where type == 'microsoft.insights/components'
    | project value = id, label = name, selected = false, group = resourceGroup
    ```
7. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

![Obraz przedstawiający tworzenie parametru zasobu przy użyciu grafu zasobów platformy Azure](./media/workbooks-resources/resource-query.png)

> [!NOTE]
> Usługa Azure Resource Graph nie jest jeszcze dostępna we wszystkich chmurach. W przypadku wybrania tej metody upewnij się, że jest ona obsługiwana w chmurze docelowej.

[Dokumentacja usługi Azure Resource Graph](../../governance/resource-graph/overview.md)

## <a name="creating-a-resource-parameter--json-list"></a>Tworzenie parametru zasobu (lista JSON)
1. Zacznij od pustego skoroszytu w trybie edycji.
2. Wybierz pozycję _Dodaj parametry_ z linków w skoroszycie.
3. Kliknij niebieski przycisk _Dodaj parametr_ .
4. W okienku Nowy parametr, który jest podręczny ENTER:
    1. Nazwa parametru: `Applications`
    2. Typ parametru: `Resource picker`
    3. Wymagane: `checked`
    4. Zezwalaj na wybór wielokrotny: `checked`
5. Pobierz dane z: `JSON`
    1. W kontrolce zawartość Dodaj ten fragment kodu JSON
    ```json
    [
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeauthentication", "label": "acmeauthentication", "selected":true, "group":"Acme Backend" },
        { "value":"/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/<resource-type>/acmeweb", "label": "acmeweb", "selected":false, "group":"Acme Frontend" }
    ]
    ```
    2. Naciśnij przycisk _Aktualizuj_ niebieską.
6. Opcjonalnie ustaw wartość `Include only resource types` na _Application Insights_
7. Wybierz pozycję "Zapisz" na pasku narzędzi, aby utworzyć parametr.

## <a name="referencing-a-resource-parameter"></a>Odwoływanie się do parametru zasobu
1. Dodaj kontrolkę zapytania do skoroszytu i wybierz zasób Application Insights.
2. Użyj listy rozwijanej _Application Insights_ , aby powiązać parametr z kontrolką. To spowoduje ustawienie zakresu zapytania do zasobów zwracanych przez parametr w czasie wykonywania.
4. W kontrolce KQL Dodaj następujący fragment kodu
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | order by Requests desc
    ```
5. Uruchom zapytanie, aby zobaczyć wyniki. 

![Obraz przedstawiający parametr zasobu, do którego odwołuje się kontrolka zapytania](./media/workbooks-resources/resource-reference.png)

> Takie podejście może służyć do powiązania zasobów z innymi kontrolkami, takimi jak metryki.

## <a name="resource-parameter-options"></a>Opcje parametrów zasobu
| Parametr | Wyjaśnienie | Przykład |
| ------------- |:-------------|:-------------|
| `{Applications}` | Wybrany identyfikator zasobu | _Identyfikator podrzędny/subscriptions/<><grupy zasobów>/Providers/<typ zasobu>/acmeauthentication_ |
| `{Applications:label}` | Etykieta wybranego zasobu | `acmefrontend` |
| `{Applications:value}` | Wartość wybranego zasobu | _"/subscriptions/<identyfikator podrzędny><grupy zasobów>/Providers/<typ zasobu>/acmeauthentication"_ |
| `{Applications:name}` | Nazwa wybranego zasobu | `acmefrontend` |
| `{Applications:resourceGroup}` | Grupa zasobów wybranego zasobu | `acmegroup` |
| `{Applications:resourceType}` | Typ wybranego zasobu | _Microsoft. Insights/składniki_ |
| `{Applications:subscription}` | Subskrypcja wybranego zasobu |  |
| `{Applications:grid}` | Siatka pokazująca właściwości zasobów. Przydatne do renderowania w bloku tekstu podczas debugowania  |  |

## <a name="next-steps"></a>Następne kroki

* [Rozpocznij](./workbooks-overview.md#visualizations) naukę więcej o skoroszytach wiele opcji rozbudowanych wizualizacji.
* [Kontroluj](./workbooks-access-control.md) i udostępniaj dostęp do zasobów skoroszytu.