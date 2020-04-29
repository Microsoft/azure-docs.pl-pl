---
title: Widoki w rozwiązaniach do zarządzania | Microsoft Docs
description: 'Rozwiązania do zarządzania zwykle obejmują co najmniej jeden widok służący do wizualizacji danych.  W tym artykule opisano sposób eksportowania widoku utworzonego przez projektanta widoków i uwzględniania go w rozwiązaniu do zarządzania. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77663049"
---
# <a name="views-in-management-solutions-preview"></a>Widoki w rozwiązaniach do zarządzania (wersja zapoznawcza)
> [!NOTE]
> Jest to wstępna dokumentacja dotycząca tworzenia rozwiązań do zarządzania, które są obecnie dostępne w wersji zapoznawczej. Każdy schemat opisany poniżej może ulec zmianie.    


[Rozwiązania do zarządzania](solutions.md) zwykle obejmują co najmniej jeden widok służący do wizualizacji danych.  W tym artykule opisano sposób eksportowania widoku utworzonego przez [projektanta widoków](../../azure-monitor/platform/view-designer.md) i uwzględniania go w rozwiązaniu do zarządzania.  

> [!NOTE]
> W przykładach w tym artykule są używane parametry i zmienne, które są wymagane lub wspólne dla rozwiązań do zarządzania, a także opisane w artykule [projektowanie i tworzenie rozwiązania do zarządzania na platformie Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Wymagania wstępne
W tym artykule założono, że wiesz już, jak [utworzyć rozwiązanie do zarządzania](solutions-creating.md) i strukturę pliku rozwiązania.

## <a name="overview"></a>Omówienie
Aby dołączyć widok w rozwiązaniu do zarządzania, należy utworzyć **zasób** dla niego w [pliku rozwiązania](solutions-creating.md).  KOD JSON, który opisuje szczegółową konfigurację widoku, jest zazwyczaj skomplikowany, a nie tylko wtedy, gdy typowy autor rozwiązania będzie mógł utworzyć ręcznie.  Najbardziej typową metodą jest utworzenie widoku przy użyciu [projektanta widoków](../../azure-monitor/platform/view-designer.md), wyeksportowanie go, a następnie dodanie jego szczegółowej konfiguracji do rozwiązania.

Poniżej przedstawiono podstawowe kroki umożliwiające dodanie widoku do rozwiązania.  Każdy krok został szczegółowo opisany w poniższych sekcjach.

1. Wyeksportuj widok do pliku.
2. Utwórz zasób widoku w rozwiązaniu.
3. Dodaj szczegóły widoku.

## <a name="export-the-view-to-a-file"></a>Eksportowanie widoku do pliku
Postępuj zgodnie z instrukcjami w [projektancie widoku log Analytics](../../azure-monitor/platform/view-designer.md) , aby wyeksportować widok do pliku.  Wyeksportowany plik będzie w formacie JSON z takimi samymi [elementami jak plik rozwiązania](solutions-solution-file.md).  

Element **resources** pliku widoku będzie miał zasób z typem **Microsoft. OperationalInsights/Workspaces** , który reprezentuje obszar roboczy log Analytics.  Ten element będzie miał podelement z typem **widoków** , które reprezentują widok i zawiera jego szczegółową konfigurację.  Skopiujesz szczegóły tego elementu, a następnie skopiujesz go do rozwiązania.

## <a name="create-the-view-resource-in-the-solution"></a>Tworzenie zasobu widoku w rozwiązaniu
Dodaj następujący zasób widoku do elementu **resources** pliku rozwiązania.  W tym przypadku używane są zmienne opisane poniżej, które trzeba również dodać.  Należy pamiętać, że właściwości **pulpitu nawigacyjnego** i **OverviewTile** są symbolami zastępczymi, które zostaną zastąpione odpowiednimi właściwościami z wyeksportowanego pliku widoku.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Dodaj następujące zmienne do elementu zmienne w pliku rozwiązania i Zastąp wartości dla danego rozwiązania.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Należy pamiętać, że można skopiować cały zasób widoku z wyeksportowanego pliku widoku, ale należy wprowadzić następujące zmiany w celu działania w rozwiązaniu.  

* **Typ** zasobu widoku musi zostać zmieniony z **widoków** na **Microsoft. OperationalInsights/Workspaces**.
* Należy zmienić właściwość **Nazwa** zasobu widoku, aby uwzględnić nazwę obszaru roboczego.
* Zależność w obszarze roboczym musi zostać usunięta, ponieważ zasób obszaru roboczego nie jest zdefiniowany w rozwiązaniu.
* Właściwość **DisplayName** musi zostać dodana do widoku.  Wszystkie **identyfikatory**, **nazwy**i **DisplayName** muszą być zgodne.
* Nazwy parametrów należy zmienić, aby były zgodne z wymaganym zestawem parametrów.
* Zmienne powinny być zdefiniowane w rozwiązaniu i używane w odpowiednich właściwościach.

### <a name="log-analytics-api-version"></a>Wersja interfejsu API Log Analytics
Wszystkie zasoby Log Analytics zdefiniowane w szablonie Menedżer zasobów mają właściwość **apiVersion** , która definiuje wersję interfejsu API, który powinien być używany przez zasób.  Ta wersja różni się w przypadku widoków z zapytaniami korzystającymi ze [starszego i uaktualnionego języka zapytań](../../azure-monitor/log-query/log-query-overview.md).  

 Poniższa tabela zawiera Log Analytics wersje interfejsu API dla widoków w starszych i uaktualnionych obszarach roboczych: 

| Wersja obszaru roboczego | Wersja interfejsu API | Zapytanie |
|:---|:---|:---|
| V1 (starsza wersja)   | 2015-11-01 — wersja zapoznawcza | Starszy format.<br> Przykład: typ = Event EventLevelName = błąd  |
| v2 (uaktualniony) | 2015-11-01 — wersja zapoznawcza | Starszy format.  Konwertowane na uaktualniony format podczas instalacji.<br> Przykład: typ = Event EventLevelName = błąd<br>Przekonwertowano na: zdarzenie &#124;, gdzie EventLevelName = = "Error"  |
| v2 (uaktualniony) | 2017-03-03 — wersja zapoznawcza | Format uaktualnienia. <br>Przykład: zdarzenie &#124;, gdzie EventLevelName = = "Error"  |


## <a name="add-the-view-details"></a>Dodaj szczegóły widoku
Zasób widoku w eksportowanym pliku widoku będzie zawierać dwa elementy w elemencie **Properties** o nazwie **Dashboard** i **OverviewTile** , które zawierają szczegółową konfigurację widoku.  Skopiuj te dwa elementy i ich zawartość do elementu **Właściwości** w widoku zasób w pliku rozwiązania.

## <a name="example"></a>Przykład
Na przykład poniższy przykład pokazuje prosty plik rozwiązania z widokiem.  Wielokropek (...) są wyświetlane dla **pulpitu nawigacyjnego** i zawartości **OverviewTile** ze względu na miejsce.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Następne kroki
* Zapoznaj się ze szczegółowymi informacjami na temat tworzenia [rozwiązań do zarządzania](solutions-creating.md).
* Uwzględnij [elementy Runbook automatyzacji w rozwiązaniu do zarządzania](solutions-resources-automation.md).
