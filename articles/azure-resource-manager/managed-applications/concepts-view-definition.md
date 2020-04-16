---
title: Omówienie definicji widoku
description: W tym artykule opisano koncepcję tworzenia definicji widoku dla aplikacji zarządzanych platformy Azure.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: 7a3d2234a140d1fb2eede50e3fe2eef5575da648
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391692"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Wyświetlanie artefaktu definicji w aplikacjach zarządzanych platformy Azure

Definicja widoku jest opcjonalnym artefaktem w aplikacjach zarządzanych platformy Azure. Umożliwia dostosowanie strony przeglądu i dodać więcej widoków, takich jak metryki i zasoby niestandardowe.

Ten artykuł zawiera omówienie artefaktu definicji widoku i jego możliwości.

## <a name="view-definition-artifact"></a>Wyświetlanie artefaktu definicji

Artefakt definicji widoku musi być nazwany **viewDefinition.json** i umieszczony na tym samym poziomie co **createUiDefinition.json** i **mainTemplate.json** w pakiecie zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet zip i opublikować definicję aplikacji [zarządzanej, zobacz Publikowanie definicji aplikacji zarządzanej platformy Azure](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Wyświetlanie schematu definicji

Plik **viewDefinition.json** ma tylko `views` jedną właściwość najwyższego poziomu, która jest tablicą widoków. Każdy widok jest wyświetlany w interfejsie użytkownika aplikacji zarządzanej jako oddzielny element menu w spisie treści. Każdy widok `kind` ma właściwość, która ustawia typ widoku. Musi być ustawiona na jedną z następujących wartości: [Przegląd](#overview), [Metryki](#metrics), [CustomResources](#custom-resources), [Skojarzenia](#associations). Aby uzyskać więcej informacji, zobacz [bieżący schemat JSON dla pliku viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Przykładowy JSON dla definicji widoku:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#",
    "contentVersion": "0.0.0.1",
    "views": [
        {
            "kind": "Overview",
            "properties": {
                "header": "Welcome to your Azure Managed Application",
                "description": "This managed application is for demo purposes only.",
                "commands": [
                    {
                        "displayName": "Test Action",
                        "path": "testAction"
                    }
                ]
            }
        },
        {
            "kind": "Metrics",
            "properties": {
                "displayName": "This is my metrics view",
                "version": "1.0.0",
                "charts": [
                    {
                        "displayName": "Sample chart",
                        "chartType": "Bar",
                        "metrics": [
                            {
                                "name": "Availability",
                                "aggregationType": "avg",
                                "resourceTagFilter": [ "tag1" ],
                                "resourceType": "Microsoft.Storage/storageAccounts",
                                "namespace": "Microsoft.Storage/storageAccounts"
                            }
                        ]
                    }
                ]
            }
        },
        {
            "kind": "CustomResources",
            "properties": {
                "displayName": "Test custom resource type",
                "version": "1.0.0",
                "resourceType": "testCustomResource",
                "createUIDefinition": { },
                "commands": [
                    {
                        "displayName": "Custom Context Action",
                        "path": "testCustomResource/testContextAction",
                        "icon": "Stop",
                        "createUIDefinition": { }
                    }
                ],
                "columns": [
                    {"key": "name", "displayName": "Name"},
                    {"key": "properties.myProperty1", "displayName": "Property 1"},
                    {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
                ]
            }
        },
        {
            "kind": "Associations",
            "properties": {
                "displayName": "Test association resource type",
                "version": "1.0.0",
                "targetResourceType": "Microsoft.Compute/virtualMachines",
                "createUIDefinition": { }
            }
        }
    ]
}
```

## <a name="overview"></a>Omówienie

`"kind": "Overview"`

Po podaniu tego widoku w **pliku viewDefinition.json**zastępuje on domyślną stronę Przegląd w aplikacji zarządzanej.

```json
{
    "kind": "Overview",
    "properties": {
        "header": "Welcome to your Azure Managed Application",
        "description": "This managed application is for demo purposes only.",
        "commands": [
            {
                "displayName": "Test Action",
                "path": "testAction"
            }
        ]
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|nagłówek|Nie|Nagłówek strony przeglądu.|
|description|Nie|Opis aplikacji zarządzanej.|
|polecenia|Nie|Tablica dodatkowych przycisków paska narzędzi strony przeglądu, zobacz [polecenia](#commands).|

![Omówienie](./media/view-definition/overview.png)

## <a name="metrics"></a>Metryki

`"kind": "Metrics"`

Widok metryki umożliwia zbieranie i agregowanie danych z zasobów zarządzanych aplikacji w [metrykach usługi Azure Monitor.](../../azure-monitor/platform/data-platform-metrics.md)

```json
{
    "kind": "Metrics",
    "properties": {
        "displayName": "This is my metrics view",
        "version": "1.0.0",
        "charts": [
            {
                "displayName": "Sample chart",
                "chartType": "Bar",
                "metrics": [
                    {
                        "name": "Availability",
                        "aggregationType": "avg",
                        "resourceTagFilter": [ "tag1" ],
                        "resourceType": "Microsoft.Storage/storageAccounts",
                        "namespace": "Microsoft.Storage/storageAccounts"
                    }
                ]
            }
        ]
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Nie|Wyświetlany tytuł widoku.|
|version|Nie|Wersja platformy używana do renderowania widoku.|
|Wykresy|Tak|Tablica wykresów strony metryki.|

### <a name="chart"></a>Wykres

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł wykresu.|
|typ wykresu|Nie|Wizualizacja do użycia na tym wykresie. Domyślnie używa wykresu liniowego. Obsługiwane typy `Bar, Line, Area, Scatter`wykresów: .|
|metrics|Tak|Tablica metryk do wykreślenia na tym wykresie. Aby dowiedzieć się więcej o metrykach obsługiwanych w witrynie Azure portal, zobacz [Obsługiwane metryki za pomocą usługi Azure Monitor](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metryka

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|name|Tak|Nazwa metryki.|
|Aggregationtype|Tak|Typ agregacji do użycia dla tej metryki. Obsługiwane typy agregacji:`none, sum, min, max, avg, unique, percentile, count`|
|namespace|Nie|Dodatkowe informacje do użycia podczas określania dostawcy poprawnych metryk.|
|resourceTagFilter|Nie|Tablica znaczników zasobów (zostanie `or` oddzielona wyrazem), dla której będą wyświetlane metryki. Dotyczy filtru typu zasobu.|
|resourceType|Tak|Typ zasobu, dla którego będą wyświetlane metryki.|

![Metryki](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Zasoby niestandardowe

`"kind": "CustomResources"`

Można zdefiniować wiele widoków tego typu. Każdy widok reprezentuje **unikatowy** niestandardowy typ zasobu zdefiniowanego przez niestandardowego dostawcę w **pliku mainTemplate.json**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [omówienie dostawców niestandardowych dostawców platformy Azure](../custom-providers/overview.md).

W tym widoku można wykonać operacje GET, PUT, DELETE i POST dla niestandardowego typu zasobu. Operacje POST mogą być globalnymi akcjami niestandardowymi lub akcjami niestandardowymi w kontekście niestandardowego typu zasobu.

```json
{
    "kind": "CustomResources",
    "properties": {
        "displayName": "Test custom resource type",
        "version": "1.0.0",
        "resourceType": "testCustomResource",
        "icon": "Polychromatic.ResourceList",
        "createUIDefinition": { },
        "commands": [
            {
                "displayName": "Custom Context Action",
                "path": "testCustomResource/testContextAction",
                "icon": "Stop",
                "createUIDefinition": { },
            }
        ],
        "columns": [
            {"key": "name", "displayName": "Name"},
            {"key": "properties.myProperty1", "displayName": "Property 1"},
            {"key": "properties.myProperty2", "displayName": "Property 2", "optional": true}
        ]
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł widoku. Tytuł powinien być **unikatowy** dla każdego widoku CustomResources w **pliku viewDefinition.json**.|
|version|Nie|Wersja platformy używana do renderowania widoku.|
|resourceType|Tak|Niestandardowy typ zasobu. Musi być **unikatowy** typ zasobu niestandardowego dostawcy niestandardowego.|
|Ikonę|Nie|Ikona widoku. Lista przykładowych ikon jest zdefiniowana w [schemacie JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|tworzeniedefinicji UI|Nie|Utwórz schemat definicji interfejsu użytkownika w celu utworzenia polecenia zasobu niestandardowego. Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createudefdefition](create-uidefinition-overview.md)|
|polecenia|Nie|Tablica dodatkowych przycisków paska narzędzi w widoku CustomResources, zobacz [polecenia](#commands).|
|columns|Nie|Tablica kolumn zasobu niestandardowego. Jeśli nie `name` zdefiniowano, kolumna będzie wyświetlana domyślnie. Kolumna musi `"key"` `"displayName"`mieć i . Dla klucza, podaj klucz właściwości do wyświetlenia w widoku. Jeśli zagnieżdżona, użyj kropki `"key": "name"` jako `"key": "properties.property1"`ogranicznika, na przykład lub . Dla nazwy wyświetlanej podaj nazwę wyświetlaną właściwości do wyświetlenia w widoku. Goście mogą również `"optional"` zapewnić właściwość. Po ustawieniu wartości true kolumna jest domyślnie ukryta w widoku.|

![Niestandardowe źródła zasobów](./media/view-definition/customresources.png)

## <a name="commands"></a>Polecenia

Polecenia to tablica dodatkowych przycisków paska narzędzi, które są wyświetlane na stronie. Każde polecenie reprezentuje akcję POST z dostawcy niestandardowego platformy Azure zdefiniowaną w **pliku mainTemplate.json**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [omówienie dostawców niestandardowych platformy Azure.](../custom-providers/overview.md)

```json
{
    "commands": [
        {
            "displayName": "Start Test Action",
            "path": "testAction",
            "icon": "Start",
            "createUIDefinition": { }
        },
    ]
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlana nazwa przycisku polecenia.|
|ścieżka|Tak|Nazwa akcji dostawcy niestandardowego. Akcja musi być zdefiniowana w **mainTemplate.json**.|
|Ikonę|Nie|Ikona przycisku polecenia. Lista przykładowych ikon jest zdefiniowana w [schemacie JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|tworzeniedefinicji UI|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia. Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createuidefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Stowarzyszenia

`"kind": "Associations"`

Można zdefiniować wiele widoków tego typu. Ten widok umożliwia łączenie istniejących zasobów z aplikacją zarządzaną za pośrednictwem niestandardowego dostawcy zdefiniowanego w **pliku mainTemplate.json**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [omówienie dostawców niestandardowych dostawców platformy Azure](../custom-providers/overview.md).

W tym widoku można rozszerzyć istniejące `targetResourceType`zasoby platformy Azure na podstawie pliku . Gdy zasób jest zaznaczony, utworzy żądanie dołączania do **publicznego** dostawcy niestandardowego, który może zastosować efekt uboczny do zasobu. 

```json
{
    "kind": "Associations",
    "properties": {
        "displayName": "Test association resource type",
        "version": "1.0.0",
        "targetResourceType": "Microsoft.Compute/virtualMachines",
        "createUIDefinition": { }
    }
}
```

|Właściwość|Wymagany|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł widoku. Tytuł powinien być **unikatowy** dla każdego widoku Skojarzenia w **pliku viewDefinition.json**.|
|version|Nie|Wersja platformy używana do renderowania widoku.|
|targetResourceType|Tak|Typ zasobu docelowego. Jest to typ zasobu, który będzie wyświetlany dla dołączania zasobów.|
|tworzeniedefinicji UI|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia utwórz zasób skojarzenia. Aby zapoznać się z wprowadzeniem do tworzenia definicji interfejsu użytkownika, zobacz [Wprowadzenie do createudefdefition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Szukam pomocy

Jeśli masz pytania dotyczące aplikacji zarządzanych platformy Azure, spróbuj zadać pytanie w sprawie [Przepełnienie stosu](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobne pytanie mogło już zostać zadane i udzielono odpowiedzi, więc sprawdź najpierw przed wysłaniem. Dodaj tag, `azure-managedapps` aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
- Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [omówienie dostawców niestandardowych platformy Azure.](../custom-providers/overview.md)
- Aby utworzyć aplikację zarządzaną platformy Azure przy wsparciu dostawców niestandardowych platformy Azure, zobacz [Samouczek: Tworzenie aplikacji zarządzanej przy zastosowaniu akcji dostawcy niestandardowego i typów zasobów](tutorial-create-managed-app-with-custom-provider.md)
