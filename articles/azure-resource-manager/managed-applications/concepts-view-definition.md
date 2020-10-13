---
title: Przegląd definicji widoku
description: Opisuje koncepcję tworzenia definicji widoku dla Azure Managed Applications.
ms.topic: conceptual
ms.author: lazinnat
author: lazinnat
ms.date: 06/12/2019
ms.openlocfilehash: bff846b4b64778d5e40ea7f08f88faf3dde81d9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91371613"
---
# <a name="view-definition-artifact-in-azure-managed-applications"></a>Wyświetl artefakt definicji w Azure Managed Applications

Definicja widoku to opcjonalny artefakt w Azure Managed Applications. Umożliwia ona Dostosowywanie strony przegląd i Dodawanie kolejnych widoków, takich jak metryki i zasoby niestandardowe.

Ten artykuł zawiera omówienie artefaktu definicji widoku i jego możliwości.

## <a name="view-definition-artifact"></a>Wyświetlanie artefaktu definicji

Artefakt definicji widoku musi mieć nazwę **viewDefinition.json** i umieszczony na tym samym poziomie co **createUiDefinition.json** i **mainTemplate.js** w pakiecie. zip, który tworzy definicję aplikacji zarządzanej. Aby dowiedzieć się, jak utworzyć pakiet ZIP i opublikować definicję aplikacji zarządzanej, zobacz temat [Publikowanie definicji aplikacji zarządzanej przez platformę Azure](publish-service-catalog-app.md)

## <a name="view-definition-schema"></a>Widok schematu definicji

**viewDefinition.jsw** pliku ma tylko jedną właściwość najwyższego poziomu `views` , która jest tablicą widoków. Każdy widok jest wyświetlany w interfejsie użytkownika aplikacji zarządzanej jako osobny element menu w spisie treści. Każdy widok ma `kind` Właściwość, która ustawia typ widoku. Musi być ustawiona na jedną z następujących wartości: [Overview](#overview), [Metrics](#metrics), [CustomResources](#custom-resources), [Associations](#associations). Aby uzyskać więcej informacji, zobacz bieżący [schemat JSON dla viewDefinition.json](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).

Przykładowy kod JSON dla definicji widoku:

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

Po udostępnieniu tego widoku w **viewDefinition.js**on zastępuje domyślną stronę przeglądu w aplikacji zarządzanej.

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

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|header|Nie|Nagłówek strony przegląd.|
|description|Nie|Opis aplikacji zarządzanej.|
|polecenia|Nie|Tablica dodatkowych przycisków paska narzędzi na stronie Przegląd, zobacz [polecenia](#commands).|

![Zrzut ekranu przedstawia przegląd aplikacji zarządzanej z kontrolką akcji testowej w celu uruchomienia aplikacji demonstracyjnej.](./media/view-definition/overview.png)

## <a name="metrics"></a>Metryki

`"kind": "Metrics"`

Widok metryki umożliwia zbieranie i agregowanie danych z zasobów aplikacji zarządzanych w ramach [metryk Azure monitor](../../azure-monitor/platform/data-platform-metrics.md).

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

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Nie|Wyświetlany tytuł widoku.|
|Wersja|Nie|Wersja platformy używana do renderowania widoku.|
|schematy|Tak|Tablica wykresów na stronie metryk.|

### <a name="chart"></a>Wykres

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł wykresu.|
|wykrestype|Nie|Wizualizacja, która ma być używana na potrzeby tego wykresu. Domyślnie używa wykresu liniowego. Obsługiwane typy wykresów: `Bar, Line, Area, Scatter` .|
|metrics|Tak|Tablica metryk do wykreślenia na tym wykresie. Aby dowiedzieć się więcej o metrykach obsługiwanych w Azure Portal, zobacz temat [obsługiwane metryki z Azure monitor](../../azure-monitor/platform/metrics-supported.md)|

### <a name="metric"></a>Metryka

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|name|Tak|Nazwa metryki.|
|agregacja|Tak|Typ agregacji, który ma być używany dla tej metryki. Obsługiwane typy agregacji: `none, sum, min, max, avg, unique, percentile, count`|
|namespace|Nie|Dodatkowe informacje do użycia podczas określania poprawnego dostawcy metryk.|
|resourceTagFilter|Nie|Tablica tagów zasobów (zostanie oddzielona od `or` słowa), dla której będą wyświetlane metryki. Dotyczy filtru typu zasobu.|
|resourceType|Tak|Typ zasobu, dla którego będą wyświetlane metryki.|

![Zrzut ekranu przedstawia stronę monitorowania o nazwie mój widok metryki dla aplikacji zarządzanej.](./media/view-definition/metrics.png)

## <a name="custom-resources"></a>Zasoby niestandardowe

`"kind": "CustomResources"`

Można zdefiniować wiele widoków tego typu. Każdy widok przedstawia **unikatowy** niestandardowy typ zasobu od niestandardowego dostawcy zdefiniowanego w **mainTemplate.json**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](../custom-providers/overview.md).

W tym widoku można wykonywać operacje GET, PUT, DELETE i POST dla niestandardowego typu zasobu. Operacje POST mogą być globalnymi akcjami niestandardowymi lub akcjami niestandardowymi w kontekście niestandardowego typu zasobu.

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

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł widoku. Tytuł powinien być **unikatowy** dla każdego widoku CustomResources w **viewDefinition.js**.|
|Wersja|Nie|Wersja platformy używana do renderowania widoku.|
|resourceType|Tak|Niestandardowy typ zasobu. Musi być **unikatowym** niestandardowym typem zasobu niestandardowego dostawcy.|
|ikona|Nie|Ikona widoku. Lista przykładowych ikon jest zdefiniowana w [schemacie JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia Utwórz zasób niestandardowy. Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md)|
|polecenia|Nie|Tablica dodatkowych przycisków paska narzędzi widoku CustomResources, zobacz [polecenia](#commands).|
|columns|Nie|Tablica kolumn zasobu niestandardowego. Jeśli nie zdefiniowano `name` kolumny, będzie ona wyświetlana domyślnie. Kolumna musi zawierać `"key"` i `"displayName"` . W polu klucz Podaj klucz właściwości, która ma być wyświetlana w widoku. Jeśli jest zagnieżdżony, użyj kropki jako ogranicznika, na przykład `"key": "name"` lub `"key": "properties.property1"` . W polu Nazwa wyświetlana Podaj nazwę wyświetlaną właściwości, która ma być wyświetlana w widoku. Możesz również podać `"optional"` Właściwość. Po ustawieniu na wartość true kolumna jest domyślnie ukryta w widoku.|

![Zrzut ekranu przedstawia stronę zasobów o nazwie test niestandardowy typ zasobu i akcję kontrolki niestandardowe kontekstu.](./media/view-definition/customresources.png)

## <a name="commands"></a>Polecenia

Polecenia to tablica dodatkowych przycisków paska narzędzi, które są wyświetlane na stronie. Każde polecenie reprezentuje akcję POST z niestandardowego dostawcy platformy Azure, która została zdefiniowana w **mainTemplate.json**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie dostawców niestandardowych platformy Azure](../custom-providers/overview.md).

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

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlana nazwa przycisku polecenia.|
|path|Tak|Nazwa akcji dostawcy niestandardowego. Akcja musi być zdefiniowana w **mainTemplate.jsna**.|
|ikona|Nie|Ikona przycisku polecenia. Lista przykładowych ikon jest zdefiniowana w [schemacie JSON](https://schema.management.azure.com/schemas/viewdefinition/0.0.1-preview/ViewDefinition.json#).|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia. Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).|

## <a name="associations"></a>Związku

`"kind": "Associations"`

Można zdefiniować wiele widoków tego typu. Ten widok pozwala połączyć istniejące zasoby z zarządzaną aplikacją za pomocą dostawcy niestandardowego zdefiniowanego w **mainTemplate.json**. Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie usługi Custom Providers w wersji zapoznawczej](../custom-providers/overview.md).

W tym widoku można rozłożyć istniejące zasoby platformy Azure na podstawie `targetResourceType` . Po wybraniu zasobu zostanie utworzone żądanie dołączania do **publicznego** dostawcy niestandardowego, które może zastosować efekt uboczny do zasobu. 

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

|Właściwość|Wymagane|Opis|
|---------|---------|---------|
|displayName|Tak|Wyświetlany tytuł widoku. Tytuł powinien być **unikatowy** dla każdego widoku skojarzenia w **viewDefinition.jsna**.|
|Wersja|Nie|Wersja platformy używana do renderowania widoku.|
|targetResourceType|Tak|Docelowy typ zasobu. Jest to typ zasobu, który będzie wyświetlany na potrzeby dołączania do zasobów.|
|createUIDefinition|Nie|Utwórz schemat definicji interfejsu użytkownika dla polecenia Utwórz zasób skojarzenia. Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md)|

## <a name="looking-for-help"></a>Szukasz pomocy

Jeśli masz pytania dotyczące Azure Managed Applications, spróbuj zadać pytanie na [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-managedapps). Podobne pytanie mogło zostać już zgłoszone i nie udzielono odpowiedzi, więc najpierw należy sprawdzić przed opublikowaniem. Dodaj tag, `azure-managedapps` Aby uzyskać szybką odpowiedź!

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z wprowadzeniem do aplikacji zarządzanych, zobacz [Azure Managed Application overview](overview.md) (Omówienie aplikacji zarządzanych platformy Azure).
- Aby zapoznać się z wprowadzeniem do dostawców niestandardowych, zobacz [Omówienie dostawców niestandardowych platformy Azure](../custom-providers/overview.md).
- Aby utworzyć aplikację zarządzaną platformy Azure z dostawcami niestandardowymi platformy Azure, zobacz [Samouczek: Tworzenie aplikacji zarządzanej przy użyciu akcji dostawcy niestandardowego i typów zasobów](tutorial-create-managed-app-with-custom-provider.md)
