---
title: Wykonywanie zapytań między zasobami przy użyciu Azure Monitor | Microsoft Docs
description: W tym artykule opisano, jak można wykonywać zapytania względem zasobów z wielu obszarów roboczych i aplikacji usługi App Insights w ramach subskrypcji.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/22/2020
ms.openlocfilehash: 1a7112b7e21e80e22bbc801d406222eaeefe0e43
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546299"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Wykonywanie zapytań dzienników w Azure Monitor obejmujących obszary robocze i aplikacje

Azure Monitor dzienniki obsługują zapytania w wielu obszarach roboczych Log Analytics i aplikacji Application Insights w tej samej grupie zasobów, w innej grupie zasobów lub innej subskrypcji. Umożliwia to wyświetlanie danych w całym systemie.

Istnieją dwie metody wykonywania zapytań dotyczących danych przechowywanych w wielu obszarach roboczych i aplikacjach:
1. Jawnie przez określenie obszaru roboczego i szczegółów aplikacji. Ta technika została szczegółowo opisana w tym artykule.
2. Niejawnie użycie [zapytań kontekstu zasobów](../platform/design-logs-deployment.md#access-mode). Podczas wykonywania zapytania w kontekście określonego zasobu, grupy zasobów lub subskrypcji, odpowiednie dane zostaną pobrane ze wszystkich obszarów roboczych zawierających dane dla tych zasobów. Application Insights dane przechowywane w aplikacjach nie będą pobierane.

> [!IMPORTANT]
> Jeśli używasz Application Insights danych telemetrycznych w [obszarze roboczym](../app/create-workspace-resource.md) , w obszarze roboczym log Analytics będą przechowywane wszystkie inne dane dziennika. Użyj wyrażenia Workspace (), aby napisać zapytanie zawierające aplikację w wielu obszarach roboczych. W przypadku wielu aplikacji w tym samym obszarze roboczym nie jest wymagane zapytanie między obszarem roboczym.


## <a name="cross-resource-query-limits"></a>Limity zapytania między zasobami 

* Liczba zasobów Application Insights i Log Analytics obszarów roboczych, które można uwzględnić w pojedynczym zapytaniu, jest ograniczona do 100.
* Zapytanie krzyżowe nie jest obsługiwane w projektancie widoków. Możesz utworzyć zapytanie w Log Analytics i przypiąć je do pulpitu nawigacyjnego platformy Azure, aby [wyświetlić wizualizację zapytania dziennika](../learn/tutorial-logs-dashboards.md). 
* Zapytania między zasobami w alertach dziennika są obsługiwane tylko w bieżącym [interfejsie API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Jeśli używasz starszej wersji interfejsu API alertów Log Analytics, musisz [przełączyć się do bieżącego interfejsu API](../platform/alerts-log-api-switch.md).


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Wykonywanie zapytań w obszarach roboczych Log Analytics i z Application Insights
Aby odwołać się do innego obszaru roboczego w zapytaniu, użyj identyfikatora [*obszaru roboczego*](./workspace-expression.md) i dla aplikacji z Application Insights Użyj identyfikatora [*aplikacji*](./app-expression.md) .  

### <a name="identifying-workspace-resources"></a>Identyfikowanie zasobów obszaru roboczego
Poniższe przykłady przedstawiają zapytania w obszarze roboczym Log Analytics, aby zwrócić podsumowania dzienników z tabeli aktualizacji w obszarze roboczym o nazwie *ContosoRetail*. 

Identyfikację obszaru roboczego można wykonać na jeden z kilku sposobów:

* Nazwa zasobu — to czytelna dla człowieka nazwa obszaru roboczego, nazywana również nazwą *składnika*. 

    `workspace("contosoretail-it").Update | count`

* Kwalifikowana nazwa — to "pełna nazwa" obszaru roboczego, składająca się z nazwy subskrypcji, grupy zasobów i nazwy składnika w tym formacie: *subscriptionname/resourceName/ComponentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczny. 
    >

* Identyfikator obszaru roboczego — identyfikator obszaru roboczego jest unikatowym, niezmiennym identyfikatorem przypisanym do każdego obszaru roboczego reprezentowanego jako unikatowy identyfikator globalny (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Identyfikator zasobu platformy Azure — unikatowa tożsamość obszaru roboczego zdefiniowana przez platformę Azure. Identyfikator zasobu jest używany, gdy nazwa zasobu jest niejednoznaczna.  W przypadku obszarów roboczych format: */subscriptions/subscriptionId/ResourceGroups/resourceGroup/Providers/Microsoft. OperationalInsights/Workspaces/ComponentName*.  

    Na przykład:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identyfikowanie aplikacji
Poniższe przykłady zwracają sumaryczną liczbę żądań wykonanych w odniesieniu do aplikacji o nazwie *fabrikamapp* w Application Insights. 

Identyfikowanie aplikacji w Application Insights można wykonać za pomocą wyrażenia *App (identyfikator)* .  Argument *identyfikatora* określa aplikację przy użyciu jednego z następujących elementów:

* Nazwa zasobu — to czytelna dla człowieka nazwa aplikacji, która jest czasami określana jako *Nazwa składnika*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Zidentyfikowanie aplikacji według nazwy zakłada unikatowość we wszystkich dostępnych subskrypcjach. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie kończy się niepowodzeniem ze względu na niejednoznaczność. W takim przypadku należy użyć jednego z innych identyfikatorów.

* Kwalifikowana nazwa — to "pełna nazwa" aplikacji składająca się z nazwy subskrypcji, grupy zasobów i nazwy składnika w tym formacie: *subscriptionname/resourceName/ComponentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczny. 
    >

* ID — identyfikator GUID aplikacji aplikacji.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Identyfikator zasobu platformy Azure — zdefiniowana przez platformę Azure unikatowa tożsamość aplikacji. Identyfikator zasobu jest używany, gdy nazwa zasobu jest niejednoznaczna. Format to: */subscriptions/subscriptionId/ResourceGroups/resourceGroup/Providers/Microsoft. OperationalInsights/Components/ComponentName*.  

    Na przykład:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Wykonywanie zapytania w wielu zasobach
Można wykonywać zapytania dotyczące wielu zasobów z dowolnego wystąpienia zasobów, które mogą być połączone z obszarami roboczymi i aplikacjami.
    
Przykład dla zapytania w dwóch obszarach roboczych:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Używanie zapytania między zasobami dla wielu zasobów
W przypadku korzystania z zapytań między zasobami w celu skorelowania danych z wielu obszarów roboczych Log Analytics i zasobów Application Insights, zapytanie może stać się skomplikowane i trudne do utrzymania. W celu oddzielenia logiki zapytania od zakresu zasobów zapytania, które upraszczają strukturę zapytań, należy korzystać z [funkcji w kwerendach dzienników Azure monitor](functions.md) . W poniższym przykładzie pokazano, jak można monitorować wiele zasobów Application Insights i wizualizować liczbę żądań nieudanych przez nazwę aplikacji. 

Utwórz zapytanie podobne do zakresu zasobów Application Insights. `withsource= SourceApp`Polecenie dodaje kolumnę, która określa nazwę aplikacji, która wysłała dziennik. [Zapisz zapytanie jako funkcję](functions.md#create-a-function) z aliasem _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Teraz można [używać tej funkcji](./functions.md#use-a-function) w zapytaniach między zasobami, takimi jak poniższe. Alias funkcji _applicationsScoping_ zwraca Unię tabeli żądań ze wszystkich zdefiniowanych aplikacji. Zapytanie następnie filtruje żądania nieudane i wizualizowa trendy według aplikacji. Operator _Parse_ jest opcjonalny w tym przykładzie. Wyodrębnia nazwę aplikacji z właściwości _SourceApp_ .

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Ta metoda nie może być używana z alertami dziennika, ponieważ sprawdzanie dostępu do zasobów reguły alertów, w tym obszarów roboczych i aplikacji, odbywa się w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji dla określania zakresu zasobów w alertach dziennika, musisz zmodyfikować regułę alertu w portalu lub z szablonem Menedżer zasobów, aby zaktualizować zasoby w zakresie. Alternatywnie możesz dołączyć listę zasobów do zapytania alertu dziennika.


![Timechart](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [dane dzienników Analizuj w Azure monitor](log-query-overview.md) , aby zapoznać się z omówieniem zapytań dzienników oraz o tym, jak Azure monitor dane dziennika.

