---
title: Ujednolicenie wielu zasobów Application Insights Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera szczegółowe informacje dotyczące używania funkcji w dziennikach Azure Monitor do wykonywania zapytań dotyczących wielu zasobów Application Insights i wizualizacji tych danych.
author: bwren
ms.author: bwren
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 7f49bd23347b53f9a5287973c6b2aba82da50ca7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100619460"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Ujednolicenie wielu zasobów Application Insights Azure Monitor 
W tym artykule opisano sposób wykonywania zapytań i wyświetlania wszystkich danych dziennika Application Insights w jednym miejscu, nawet w przypadku, gdy znajdują się one w różnych subskrypcjach platformy Azure, jako zamiennik dla zaniechania Application Insights Connector. Liczba zasobów Application Insights, które można uwzględnić w pojedynczym zapytaniu, jest ograniczona do 100.

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Zalecane podejście do wykonywania zapytań dotyczących wielu zasobów Application Insights 
Wyświetlanie listy wielu zasobów Application Insights w zapytaniu może być uciążliwe i trudne do utrzymania. Zamiast tego można wykorzystać funkcję do oddzielenia logiki zapytania od zakresu aplikacji.  

W tym przykładzie pokazano, jak można monitorować wiele zasobów Application Insights i wizualizować liczbę nieudanych żądań według nazwy aplikacji.

Utwórz funkcję używającą operatora UNION z listą aplikacji, a następnie Zapisz zapytanie w obszarze roboczym jako funkcję z aliasem *applicationsScoping*. 

W dowolnym momencie możesz modyfikować wymienione aplikacje w portalu, przechodząc do Eksploratora zapytań w obszarze roboczym i wybierając funkcję do edycji, a następnie zapisując lub używając `SavedSearch` polecenia cmdlet programu PowerShell. 

>[!NOTE]
>Ta metoda nie może być używana z alertami dziennika, ponieważ sprawdzanie dostępu do zasobów reguły alertów, w tym obszarów roboczych i aplikacji, odbywa się w czasie tworzenia alertu. Dodawanie nowych zasobów do funkcji po utworzeniu alertu nie jest obsługiwane. Jeśli wolisz używać funkcji dla określania zakresu zasobów w alertach dziennika, musisz zmodyfikować regułę alertu w portalu lub z szablonem Menedżer zasobów, aby zaktualizować zasoby w zakresie. Alternatywnie możesz dołączyć listę zasobów do zapytania alertu dziennika.

`withsource= SourceApp`Polecenie dodaje kolumnę do wyników, które wyznaczają aplikację, która wysłała dziennik. Operator Parse jest opcjonalny w tym przykładzie i służy do wyodrębniania nazwy aplikacji z właściwości SourceApp. 

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

Teraz można przystąpić do używania funkcji applicationsScoping w kwerendzie obejmującej wiele zasobów:  

```
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| render timechart
```

Zapytanie używa schematu Application Insights, mimo że zapytanie jest wykonywane w obszarze roboczym, ponieważ funkcja applicationsScoping zwraca strukturę danych Application Insights. Alias funkcji zwraca Unię żądań ze wszystkich zdefiniowanych aplikacji. Zapytanie następnie filtruje żądania nieudane i wizualizowa trendy według aplikacji.

![Przykład wyników między zapytaniami](media/unify-app-resource-data/app-insights-query-results.png)

>[!NOTE]
>[Zapytania między zasobami](../logs/cross-workspace-query.md) w alertach dziennika są obsługiwane tylko w bieżącym [interfejsie API scheduledQueryRules](/rest/api/monitor/scheduledqueryrules). Jeśli używasz starszej wersji interfejsu API alertów Log Analytics, musisz [przełączyć się do bieżącego interfejsu API](../alerts/alerts-log-api-switch.md). [Zobacz przykładowe szablony](../alerts/alerts-log-create-templates.md).

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Application Insights i Log Analytics różnice w schemacie obszaru roboczego
W poniższej tabeli przedstawiono różnice schematu między Log Analytics i Application Insights.  

| Log Analytics właściwości obszaru roboczego| Właściwości zasobów Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | Argumentu|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | czas trwania |
| AvailabilityMessage | message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | identyfikator |
| AvailabilityTestName | name |
| AvailabilityTimestamp | sygnatura czasowa |
| Przeglądarka | client_browser |
| City (Miasto) | client_city |
| ClientIP | client_IP |
| Computer (Komputer) | cloud_RoleInstance | 
| Kraj | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | name | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | message | 
| Typ | typ |
| OperationID | operation_id |
| OperationName | operation_Name | 
| System operacyjny | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | czas trwania | 
| PageViewName | name | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | czas trwania | 
| RequestID | identyfikator | 
| Żądanie | name | 
| RequestSuccess | powodzenie | 
| ResponseCode | resultCode | 
| Rola | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| Telemetriatype | typ |
| Adres URL | url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Następne kroki

Użyj [wyszukiwania w dzienniku](../logs/log-query-overview.md) , aby wyświetlić szczegółowe informacje dotyczące Application Insights aplikacji.

