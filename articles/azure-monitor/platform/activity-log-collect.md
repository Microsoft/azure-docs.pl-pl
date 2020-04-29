---
title: Zbieranie i analizowanie dzienników aktywności platformy Azure w Azure Monitor
description: Zbierz dziennik aktywności platformy Azure w Azure Monitor dziennikach i użyj rozwiązania do monitorowania, aby przeanalizować i przeszukać dziennik aktywności platformy Azure we wszystkich subskrypcjach platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/14/2020
ms.openlocfilehash: 098aeaa06a26c57744402722aa3eacc51ea85fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81382867"
---
# <a name="collect-and-analyze-azure-activity-log-in-azure-monitor"></a>Zbieranie i analizowanie dzienników aktywności platformy Azure w Azure Monitor
[Dziennik aktywności platformy Azure](platform-logs-overview.md) to [Dziennik platformy](platform-logs-overview.md) , który zapewnia wgląd w zdarzenia na poziomie subskrypcji, które wystąpiły na platformie Azure. Podczas wyświetlania dziennika aktywności w Azure Portal należy skonfigurować go do wysyłania do Log Analytics obszaru roboczego, aby włączyć dodatkowe funkcje Azure Monitor. W tym artykule opisano sposób wykonywania tej konfiguracji oraz wysyłania dziennika aktywności do usługi Azure Storage i centrów zdarzeń.

Zbieranie dziennika aktywności w obszarze roboczym Log Analytics zapewnia następujące korzyści:

- Brak opłat za pozyskiwanie danych lub pobieranie danych dla danych dziennika aktywności przechowywanych w obszarze roboczym Log Analytics.
- Skorelowanie danych dziennika aktywności z innymi danymi monitorowania zbieranymi przez Azure Monitor.
- Za pomocą zapytań dzienników można wykonywać złożone analizy i uzyskiwać szczegółowe informacje o wpisach dziennika aktywności.
- Alerty dzienników z wpisami aktywności umożliwiają bardziej złożoną logikę alertów.
- Przechowywanie wpisów dziennika aktywności przez dłużej niż 90 dni.
- Konsolidowanie wpisów dzienników z wielu subskrypcji platformy Azure i dzierżawców w jedną lokalizację do analizy razem.

> [!IMPORTANT]
> Zbieranie dzienników między dzierżawcami wymaga [platformy Azure Lighthouse](/azure/lighthouse).

## <a name="collecting-activity-log"></a>Zbieranie dziennika aktywności
Dziennik aktywności jest zbierany automatycznie do [wyświetlania w Azure Portal](activity-log-view.md). Aby zebrać ją w obszarze roboczym Log Analytics lub wysłać do usługi Azure Storage lub Centra zdarzeń, Utwórz [ustawienie diagnostyczne](diagnostic-settings.md). Jest to ta sama metoda, która jest używana przez dzienniki zasobów, dzięki czemu jest spójna dla wszystkich [dzienników platformy](platform-logs-overview.md).  

Aby utworzyć ustawienie diagnostyczne dla dziennika aktywności, wybierz pozycję **Ustawienia diagnostyczne** z menu **dziennik aktywności** w Azure monitor. Aby uzyskać szczegółowe informacje na temat tworzenia tego ustawienia, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników platformy i metryk na platformie Azure](diagnostic-settings.md) . Aby uzyskać opis kategorii, które można filtrować, zobacz [Kategorie w dzienniku aktywności](activity-log-view.md#categories-in-the-activity-log) . Jeśli masz jakieś starsze ustawienia, upewnij się, że zostały wyłączone przed utworzeniem ustawienia diagnostycznego. Włączenie obu włączonych może spowodować zduplikowanie danych.

![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)


> [!NOTE]
> Obecnie ustawienie diagnostyczne poziomu subskrypcji można utworzyć tylko przy użyciu szablonu Azure Portal i Menedżer zasobów. 


## <a name="legacy-settings"></a>Starsze ustawienia 
Gdy ustawienia diagnostyczne są preferowaną metodą wysyłania dziennika aktywności do różnych miejsc docelowych, starsze metody będą nadal działały, jeśli nie zastąpisz ustawień diagnostycznych. Ustawienia diagnostyczne mają następujące zalety niż starsze metody i zaleca się zaktualizowanie konfiguracji:

- Spójna Metoda zbierania wszystkich dzienników platformy.
- Zbieraj dziennik aktywności między wieloma subskrypcjami i dzierżawcami.
- Filtruj kolekcję, aby zbierać dzienniki tylko dla określonych kategorii.
- Zbierz wszystkie kategorie dziennika aktywności. Niektóre kategorie nie są zbierane przy użyciu starszej metody.
- Szybsze opóźnienie w przypadku pozyskiwania dziennika. Poprzednia metoda ma około 15 minut opóźnienia, podczas gdy ustawienia diagnostyczne są dodawane tylko o 1 minutę.



### <a name="log-profiles"></a>Profile dziennika
Profile dzienników są starszą metodą wysyłania dziennika aktywności do usługi Azure Storage lub centrów zdarzeń. Poniższa procedura umożliwia kontynuowanie pracy z profilem dziennika lub wyłączenie go w celu migracji do ustawień diagnostycznych.

1. Z menu **Azure monitor** w Azure Portal wybierz pozycję **Dziennik aktywności**.
3. Kliknij pozycję **Ustawienia diagnostyczne**.

   ![Ustawienia diagnostyczne](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Kliknij transparent purpurowy dla starszego środowiska.

    ![Starsze środowisko](media/diagnostic-settings-subscription/legacy-experience.png)

### <a name="log-analytics-workspace"></a>Obszar roboczy usługi Log Analytics
Starsza Metoda zbierania dziennika aktywności w obszarze roboczym Log Analytics nawiązuje połączenie dziennika w konfiguracji obszaru roboczego. 

1. Z menu **log Analytics obszary robocze** w Azure Portal wybierz obszar roboczy, w którym ma zostać zebrany dziennik aktywności.
1. W sekcji **źródła danych obszaru roboczego** menu obszaru roboczego wybierz pozycję **Dziennik aktywności platformy Azure**.
1. Kliknij subskrypcję, którą chcesz połączyć.

    ![Obszary robocze](media/activity-log-collect/workspaces.png)

1. Kliknij przycisk **Połącz** , aby połączyć dziennik aktywności w subskrypcji z wybranym obszarem roboczym. Jeśli subskrypcja jest już połączona z innym obszarem roboczym, kliknij przycisk **Rozłącz** jako pierwszy, aby rozłączyć ją.

    ![Połącz obszary robocze](media/activity-log-collect/connect-workspace.png)


Aby wyłączyć to ustawienie, wykonaj tę samą procedurę i kliknij przycisk **Rozłącz** , aby usunąć subskrypcję z obszaru roboczego.


## <a name="analyze-activity-log-in-log-analytics-workspace"></a>Analizowanie dziennika aktywności w Log Analytics obszarze roboczym
Po połączeniu dziennika aktywności z obszarem roboczym Log Analytics w obszarze roboczym wpisy zostaną zapisaniu do tabeli o nazwie *Azure* , którą można pobrać przy użyciu [zapytania dziennika](../log-query/log-query-overview.md). Struktura tej tabeli różni się w zależności od [kategorii wpisu dziennika](activity-log-view.md#categories-in-the-activity-log). Zobacz [schemat zdarzeń dziennika aktywności platformy Azure](activity-log-schema.md) , aby uzyskać opis każdej kategorii.


### <a name="data-structure-changes"></a>Zmiany struktury danych
Ustawienia diagnostyczne zbierają te same dane, co w przypadku starszej metody służącej do zbierania dziennika aktywności ze zmianami struktury tabeli *usługi Azure* .

Kolumny w poniższej tabeli zostały zaniechane w zaktualizowanym schemacie. Nadal istnieją w *usłudze Azure* , ale nie będą miały żadnych danych. Zastąpienie tych kolumn nie jest nowe, ale zawierają te same dane, co kolumna przestarzała. Są one w innym formacie, więc może być konieczne zmodyfikowanie zapytań dziennika, które z nich korzystają. 

| Kolumna przestarzała | Kolumna zastępująca |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNameValue     |
| ResourceProvider  | ResourceProviderValue  |

> [!IMPORTANT]
> W niektórych przypadkach wartości w tych kolumnach mogą być pisane wielkimi literami. Jeśli masz zapytanie zawierające te kolumny, należy użyć [operatora = ~](https://docs.microsoft.com/azure/kusto/query/datatypes-string-operators) do wykonania porównania bez uwzględniania wielkości liter.

Następująca kolumna została dodana do *usługi Azure* w zaktualizowanym schemacie:

- Authorization_d
- Claims_d
- Properties_d


## <a name="activity-logs-analytics-monitoring-solution"></a>Rozwiązanie do monitorowania dzienników aktywności
Rozwiązanie do monitorowania Log Analytics platformy Azure zostanie wycofane wkrótce i zastąpione przez skoroszyt przy użyciu zaktualizowanego schematu w obszarze roboczym Log Analytics. Nadal możesz użyć rozwiązania, jeśli jest już włączone, ale może być używane tylko wtedy, gdy zbierasz dziennik aktywności przy użyciu starszych ustawień. 



### <a name="use-the-solution"></a>Korzystanie z rozwiązania
Do monitorowania są dostępne rozwiązania z menu **Monitoruj** w Azure Portal. Wybierz pozycję **więcej** w sekcji **szczegółowe informacje** , aby otworzyć stronę **Przegląd** z kafelkami rozwiązania. Kafelek **dzienniki aktywności platformy Azure** przedstawia liczbę rekordów operacji **platformy Azure** w obszarze roboczym.

![Kafelek dzienników aktywności platformy Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Kliknij kafelek **dzienniki aktywności platformy Azure** , aby otworzyć widok **dzienniki aktywności platformy Azure** . Widok zawiera części wizualizacji w poniższej tabeli. Każda część wykazuje do 10 elementów spełniających kryteria tych części dla określonego zakresu czasu. Można uruchomić kwerendę dziennika, która zwraca wszystkie zgodne rekordy, klikając pozycję **Zobacz wszystko** u dołu części.

![Pulpit nawigacyjny dzienników aktywności platformy Azure](media/collect-activity-logs/activity-log-dash.png)


### <a name="enable-the-solution-for-new-subscriptions"></a>Włącz rozwiązanie dla nowych subskrypcji
Nie będzie już można dodawać rozwiązania Logs dzienników aktywności do subskrypcji przy użyciu Azure Portal. Można dodać go przy użyciu poniższej procedury z szablonem usługi Resource Manager. 

1. Skopiuj poniższy kod JSON do pliku o nazwie *ActivityLogTemplate*. JSON.

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "defaultValue": "my-workspace",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "east us",
              "west us",
              "australia central",
              "west europe"
            ],
            "defaultValue": "australia central",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        }
      },
        "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "features": {
                    "searchVersion": 2
                }
            }
        },
        {
            "type": "Microsoft.OperationsManagement/solutions",
            "apiVersion": "2015-11-01-preview",
            "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
            ],
            "plan": {
                "name": "[concat('AzureActivity(', parameters('workspaceName'),')')]",
                "promotionCode": "",
                "product": "OMSGallery/AzureActivity",
                "publisher": "Microsoft"
            },
            "properties": {
                "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]",
                "containedResources": [
                    "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName')), '/views/AzureActivity(',parameters('workspaceName'))]"
                ]
            }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/datasources",
          "kind": "AzureActivityLog",
          "name": "[concat(parameters('workspaceName'), '/', subscription().subscriptionId)]",
          "apiVersion": "2015-11-01-preview",
          "location": "[parameters('location')]",
          "dependsOn": [
              "[parameters('WorkspaceName')]"
          ],
          "properties": {
              "linkedResourceId": "[concat(subscription().Id, '/providers/microsoft.insights/eventTypes/management')]"
          }
        }
      ]
    }    
    ```

2. Wdróż szablon przy użyciu następujących poleceń programu PowerShell:

    ```PowerShell
    Connect-AzAccount
    Select-AzSubscription <SubscriptionName>
    New-AzResourceGroupDeployment -Name activitysolution -ResourceGroupName <ResourceGroup> -TemplateFile <Path to template file>
    ```


## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dzienniku aktywności](platform-logs-overview.md).
- Dowiedz się więcej o [platformie danych Azure monitor](data-platform.md).
- Użyj [zapytań dzienników](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe informacje z dziennika aktywności.
