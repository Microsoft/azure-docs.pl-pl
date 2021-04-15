---
title: Wykonywanie zapytań między zasobami za pomocą Azure Monitor | Microsoft Docs
description: W tym artykule opisano sposób wykonywania zapytań względem zasobów z wielu obszarów roboczych i aplikacji App Insights w ramach subskrypcji.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/11/2021
ms.openlocfilehash: 19cc85751fc5e4a165b646ac89d9d6b6e90c4408
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379557"
---
# <a name="perform-log-query-in-azure-monitor-that-span-across-workspaces-and-apps"></a>Wykonywanie zapytań dziennika w Azure Monitor, które obejmują obszary robocze i aplikacje

Azure Monitor obsługuje zapytania w wielu obszarach roboczych usługi Log Analytics Application Insights aplikacji w tej samej grupie zasobów, innej grupie zasobów lub innej subskrypcji. Zapewnia to widok danych dla całego systemu.

Istnieją dwie metody wykonywania zapytań dotyczących danych przechowywanych w wielu obszarach roboczych i aplikacjach:
1. Jawnie, określając szczegóły obszaru roboczego i aplikacji. Ta technika jest szczegółowo opisane w tym artykule.
2. Niejawnie przy [użyciu zapytań kontekstu zasobu](./design-logs-deployment.md#access-mode). Podczas wykonywania zapytań w kontekście określonego zasobu, grupy zasobów lub subskrypcji odpowiednie dane zostaną pobrane ze wszystkich obszarów roboczych, które zawierają dane dla tych zasobów. Application Insights, które są przechowywane w aplikacjach, nie zostaną pobrane.

> [!IMPORTANT]
> Jeśli używasz danych telemetrycznych zasobów opartych na [Application Insights](../app/create-workspace-resource.md) obszarze roboczym usługi Log Analytics ze wszystkimi innymi danymi dziennika. Użyj wyrażenia workspace(), aby napisać zapytanie, które zawiera aplikację w wielu obszarach roboczych. W przypadku wielu aplikacji w tym samym obszarze roboczym nie jest potrzebne zapytanie między obszarami roboczymi.


## <a name="cross-resource-query-limits"></a>Limity zapytań między zasobami 

* Liczba zasobów Application Insights i obszarów roboczych usługi Log Analytics, które można uwzględnić w pojedynczym zapytaniu, jest ograniczona do 100.
* Zapytania między zasobami nie są obsługiwane w Projektant widoków. Możesz utworzyć zapytanie w usłudze Log Analytics i przypiąć je do pulpitu nawigacyjnego platformy Azure, aby zwizualizować zapytanie dziennika [lub](../visualize/tutorial-logs-dashboards.md) dołączyć je do [skoroszytów.](../visualize/workbooks-overview.md)
* Zapytania między zasobami w alertach dzienników są obsługiwane tylko w bieżącym interfejsie [API scheduledQueryRules.](/rest/api/monitor/scheduledqueryrules) Jeśli używasz starszego interfejsu API alertów usługi Log Analytics, musisz przełączyć się do [bieżącego interfejsu API.](../alerts/alerts-log-api-switch.md)


## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Wykonywanie zapytań w obszarach roboczych usługi Log Analytics i z Application Insights
Aby odwołać się do innego [](../logs/workspace-expression.md) obszaru roboczego w zapytaniu, użyj identyfikatora obszaru roboczego, a w przypadku aplikacji z Application Insights użyj [*identyfikatora*](./app-expression.md) aplikacji.  

### <a name="identifying-workspace-resources"></a>Identyfikowanie zasobów obszaru roboczego
W poniższych przykładach pokazano zapytania w obszarach roboczych usługi Log Analytics, które zwracają podsumowane liczby dzienników z tabeli Aktualizacji w obszarze roboczym o *nazwie contosoretail-it.* 

Identyfikowanie obszaru roboczego można wykonać na jeden z kilku sposobów:

* Nazwa zasobu — czytelna dla człowieka nazwa obszaru roboczego, czasami nazywana *nazwą składnika*. 

    >[!IMPORTANT]
    >Ponieważ nazwy aplikacji i obszarów roboczych nie są unikatowe, ten identyfikator może być niejednoznaczny. Zaleca się, aby odwołanie było według kwalifikowanej nazwy, identyfikatora obszaru roboczego lub identyfikatora zasobu platformy Azure.

    `workspace("contosoretail-it").Update | count`

* Kwalifikowana nazwa — to "pełna nazwa" obszaru roboczego składająca się z nazwy subskrypcji, grupy zasobów i nazwy składnika w tym formacie: *subscriptionName/resourceGroup/componentName.* 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count`

    >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczny.

* Identyfikator obszaru roboczego — identyfikator obszaru roboczego jest unikatowym, niezmiennym identyfikatorem przypisanym do każdego obszaru roboczego reprezentowanego jako unikatowy identyfikator globalny (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* Identyfikator zasobu platformy Azure — unikatowa tożsamość obszaru roboczego zdefiniowana przez platformę Azure. Identyfikatora zasobu używa się, gdy nazwa zasobu jest niejednoznaczna.  W przypadku obszarów roboczych format jest: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/workspaces/componentName.*  

    Na przykład:
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identyfikowanie aplikacji
Poniższe przykłady zwracają podsumowaną liczbę żądań wykonanych względem aplikacji o nazwie *fabrikamapp* w Application Insights. 

Identyfikowanie aplikacji w Application Insights można wykonać za pomocą wyrażenia *app(Identifier).*  Argument *Identyfikator* określa aplikację przy użyciu jednej z następujących czynności:

* Nazwa zasobu — czytelna dla człowieka nazwa aplikacji, czasami nazywana *nazwą składnika*.  

    `app("fabrikamapp")`

    >[!NOTE]
    >Identyfikowanie aplikacji według nazwy zakłada unikatowość we wszystkich dostępnych subskrypcjach. Jeśli masz wiele aplikacji o określonej nazwie, zapytanie kończy się niepowodzeniem z powodu niejednoznaczności. W takim przypadku należy użyć jednego z innych identyfikatorów.

* Kwalifikowana nazwa — jest "pełną nazwą" aplikacji składającą się z nazwy subskrypcji, grupy zasobów i nazwy składnika w tym formacie: *subscriptionName/resourceGroup/componentName.* 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Ponieważ nazwy subskrypcji platformy Azure nie są unikatowe, ten identyfikator może być niejednoznaczny. 
    >

* IDENTYFIKATOR — identyfikator GUID aplikacji.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* Identyfikator zasobu platformy Azure — unikatowa tożsamość aplikacji zdefiniowana przez platformę Azure. Identyfikatora zasobu używa się, gdy nazwa zasobu jest niejednoznaczna. Format: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft. OperationalInsights/components/componentName*.  

    Na przykład:
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Wykonywanie zapytania w wielu zasobach
Zapytania o wiele zasobów można zapytania z dowolnego wystąpienia zasobu. Mogą to być połączone obszary robocze i aplikacje.
    
Przykład zapytania w dwóch obszarach roboczych:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Używanie zapytania między zasobami dla wielu zasobów
W przypadku używania zapytań między zasobami do korelowania danych z wielu obszarów roboczych usługi Log Analytics i Application Insights zasobów zapytanie może stać się złożone i trudne do utrzymania. Należy korzystać z [funkcji w Azure Monitor zapytań](./functions.md) dziennika, aby oddzielić logikę zapytania od zakresu zasobów zapytań, co upraszcza strukturę zapytań. W poniższym przykładzie pokazano, jak monitorować wiele zasobów Application Insights i wizualizować liczbę żądań, które zakończyły się niepowodzeniem, według nazwy aplikacji. 

Utwórz zapytanie podobne do poniższego, które odwołuje się do zakresu Application Insights zasobów. Polecenie `withsource= SourceApp` dodaje kolumnę, która wyznacza nazwę aplikacji, która wysłała dziennik. [Zapisz zapytanie jako funkcję z](./functions.md#create-a-function) aliasem _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Teraz możesz [użyć tej funkcji w](./functions.md#use-a-function) zapytaniu między zasobami, jak po poniższym. Alias funkcji _applicationsScoping_ zwraca unię tabeli requests ze wszystkich zdefiniowanych aplikacji. Następnie zapytanie filtruje żądania, które zakończyły się niepowodzeniem, i wizualizuje trendy według aplikacji. W tym przykładzie operator _parse_ jest opcjonalny. Wyodrębnia nazwę aplikacji z _właściwości SourceApp._

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

>[!NOTE]
> Tej metody nie można używać z alertami dzienników, ponieważ weryfikacja dostępu zasobów reguły alertu, w tym obszarów roboczych i aplikacji, jest wykonywana w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji na potrzeby zakresu zasobów w alertach dzienników, musisz edytować regułę alertu w portalu lub za pomocą szablonu Resource Manager, aby zaktualizować zasoby w zakresie. Alternatywnie możesz uwzględnić listę zasobów w zapytaniu alertu dziennika.


![Schemat czasowy](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Następne kroki

- Przejrzyj [temat Analyze log data in Azure Monitor](./log-query-overview.md) (Analizowanie danych dziennika w programie), aby zapoznać się z omówieniem zapytań dotyczących dzienników i Azure Monitor struktury danych dziennika.