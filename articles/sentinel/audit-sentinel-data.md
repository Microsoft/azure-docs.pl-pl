---
title: Inspekcja zapytań i działań wskaźnikowych platformy Azure | Microsoft Docs
description: W tym artykule opisano sposób inspekcji zapytań i działań wykonywanych na platformie Azure.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: a02be0938b1ab925fb0343351ce1c414cc59c615
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044842"
---
# <a name="audit-azure-sentinel-queries-and-activities"></a>Inspekcja zapytań i działań wskaźnikowych platformy Azure

W tym artykule opisano sposób wyświetlania danych inspekcji dla przebiegów zapytań i działań wykonywanych w obszarze roboczym usługi Azure wskaźnikowych, takich jak wewnętrzne i zewnętrzne wymagania dotyczące zgodności w obszarze roboczym operacje zabezpieczeń (SOC).

Wskaźnik na platformie Azure zapewnia dostęp do:

- Tabela **platformy Azure** , która zawiera szczegółowe informacje o wszystkich działaniach podejmowanych na platformie Azure, takich jak edytowanie reguł alertów. Tabela **usługi Azure** nie rejestruje określonych danych zapytania. Aby uzyskać więcej informacji, zobacz [Inspekcja przy użyciu dzienników aktywności platformy Azure](#auditing-with-azure-activity-logs).

- Tabela **LAQueryLogs** , która zawiera szczegółowe informacje o zapytaniach uruchamianych w log Analytics, w tym kwerendy uruchamiane z platformy Azure — wskaźnikiem. Aby uzyskać więcej informacji, zobacz [Inspekcja przy użyciu LAQueryLogs](#auditing-with-laquerylogs).

> [!TIP]
> Oprócz zapytań ręcznych opisanych w tym artykule badanie wskaźnikowe platformy Azure umożliwia tworzenie wbudowanych skoroszytów, które ułatwiają inspekcję działań w środowisku SOC.
>
> W obszarze **skoroszyty** wskaźnikowe platformy Azure Wyszukaj skoroszyt **inspekcji obszaru roboczego** .



## <a name="auditing-with-azure-activity-logs"></a>Inspekcja przy użyciu dzienników aktywności platformy Azure

Dzienniki inspekcji platformy Azure są przechowywane w [dziennikach aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md), w których tabela **Azure** Activity obejmuje wszystkie akcje wykonane w obszarze roboczym wskaźnikowego platformy Azure.

Tabeli **usługi Azure** Activity można użyć podczas inspekcji działania w środowisku SOC przy użyciu platformy Azure.

**Aby zbadać tabelę usługi Azure**:

1. Połącz źródło danych [aktywności platformy Azure](connect-azure-activity.md) , aby rozpocząć zdarzenia inspekcji przesyłania strumieniowego do nowej tabeli na ekranie **dzienników** o nazwie Azure.

1. Następnie wykonaj zapytanie o dane przy użyciu KQL, tak jak w przypadku każdej innej tabeli.

    Tabela **platformy Azure** obejmuje dane z wielu usług, w tym Azure — wskaźnik. Aby odfiltrować tylko dane z platformy Azure, uruchom zapytanie przy użyciu następującego kodu:

    ```kql
     AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS"
    ```

    Na przykład aby dowiedzieć się, kto był ostatnim użytkownikiem edytującym daną regułę analizy, użyj następującego zapytania (zastępując `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` Identyfikator reguły dla reguły, którą chcesz sprawdzić):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```

Dodaj więcej parametrów do zapytania, aby dowiedzieć się więcej o tabeli **AzureActivities** , w zależności od tego, czego potrzebujesz do raportowania. Poniższe sekcje zawierają inne przykładowe zapytania, które mają być używane podczas przeprowadzania inspekcji przy użyciu danych tabeli **platformy Azure** . 

Aby uzyskać więcej informacji, zobacz [dane wskaźnikowe platformy Azure zawarte w dziennikach aktywności platformy Azure](#azure-sentinel-data-included-in-azure-activity-logs).

### <a name="find-all-actions-taken-by-a-specific-user-in-the-last-24-hours"></a>Znajdź wszystkie akcje podjęte przez określonego użytkownika w ciągu ostatnich 24 godzin

Poniższe zapytanie tabeli **usługi Azure** list zawiera wszystkie akcje podjęte przez określonego użytkownika usługi Azure AD w ciągu ostatnich 24 godzin.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where Caller == "[AzureAD username]"
| where TimeGenerated > ago(1d)
```

### <a name="find-all-delete-operations"></a>Znajdź wszystkie operacje usuwania

Poniższe zapytanie tabeli **platformy Azure** zawiera listę wszystkich operacji usuwania wykonanych w obszarze roboczym wskaźnikowego platformy Azure.

```kql
AzureActivity
| where OperationNameValue contains "SecurityInsights"
| where OperationName contains "Delete"
| where ActivityStatusValue contains "Succeeded"
| project TimeGenerated, Caller, OperationName
``` 


### <a name="azure-sentinel-data-included-in-azure-activity-logs"></a>Dane wskaźnikowe platformy Azure zawarte w dziennikach aktywności platformy Azure
 
Dzienniki inspekcji platformy Azure są przechowywane w [dziennikach aktywności platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)i zawierają następujące typy informacji:

|Operacja  |Typy informacji  |
|---------|---------|
|**Utworzono**     |Zasady alertów <br> Komentarze do wielkości liter <br>Komentarze do zdarzeń <br>Zapisane wyszukiwania<br>Watchlists    <br>Skoroszyty     |
|**Usunięta**     |Zasady alertów <br>Zakładki <br>Łączniki danych <br>Zdarzenia <br>Zapisane wyszukiwania <br>Ustawienia <br>Raporty analizy zagrożeń <br>Watchlists      <br>Skoroszyty <br>Przepływ pracy  |
|**Aktualny**     |  Zasady alertów<br>Zakładki <br> Padkach <br> Łączniki danych <br>Zdarzenia <br>Komentarze do zdarzeń <br>Raporty analizy zagrożeń <br> Skoroszyty <br>Przepływ pracy       |
|     |         |

Możesz również użyć dzienników aktywności platformy Azure, aby sprawdzić autoryzację i licencje użytkownika. 

Na przykład poniższa tabela zawiera listę wybranych operacji znalezionych w dziennikach aktywności platformy Azure z określonym zasobem, z którego pobierane są dane dziennika.

|Nazwa operacji|    Typ zasobu|
|----|----|
|Utwórz lub zaktualizuj skoroszyt  |Microsoft. Insights/skoroszyty|
|Usuń skoroszyt    |Microsoft. Insights/skoroszyty|
|Ustaw przepływ pracy   |Microsoft. Logic/przepływy pracy|
|Usuń przepływ pracy    |Microsoft. Logic/przepływy pracy|
|Utwórz zapisane wyszukiwanie    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Usuń zapisane wyszukiwanie    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Aktualizowanie reguł alertów |Microsoft. SecurityInsights/alertRules|
|Usuń reguły alertów |Microsoft. SecurityInsights/alertRules|
|Aktualizuj akcje odpowiedzi reguły alertu |Microsoft. SecurityInsights/alertRules/Actions|
|Usuń akcje odpowiedzi reguły alertu |Microsoft. SecurityInsights/alertRules/Actions|
|Aktualizuj zakładki   |Microsoft. SecurityInsights/zakładki|
|Usuń zakładki   |Microsoft. SecurityInsights/zakładki|
|Przypadki aktualizacji   |Microsoft. SecurityInsights/sprawy|
|Aktualizacja badania przypadku  |Microsoft. SecurityInsights/sprawy/badania|
|Utwórz Komentarze do wielkości liter   |Microsoft. SecurityInsights/sprawy/Komentarze|
|Aktualizuj łączniki danych |Microsoft. SecurityInsights/dataconnecters|
|Usuń łączniki danych |Microsoft. SecurityInsights/dataconnecters|
|Ustawienia aktualizacji    |Microsoft. SecurityInsights/ustawienia|
| | |

Aby uzyskać więcej informacji, zobacz [schemat zdarzeń dziennika aktywności platformy Azure](../azure-monitor/essentials/activity-log-schema.md).


## <a name="auditing-with-laquerylogs"></a>Inspekcja przy użyciu LAQueryLogs

Tabela **LAQueryLogs** zawiera szczegółowe informacje o zapytaniach dziennika, które są uruchamiane w log Analytics. Ponieważ Log Analytics jest używany jako podstawowy magazyn danych wskaźnikowych platformy Azure, można skonfigurować system do zbierania danych LAQueryLogs w obszarze roboczym wskaźnikowego platformy Azure.

Dane LAQueryLogs obejmują następujące informacje:

- Po uruchomieniu zapytań
- Kto uruchomił zapytania w Log Analytics
- Narzędzie użyte do uruchamiania zapytań w Log Analytics, takich jak Azure — wskaźnik
- Samego tekstu zapytania
- Dane dotyczące wydajności dla każdego uruchomienia zapytania

> [!NOTE]
> - Tabela **LAQueryLogs** zawiera tylko zapytania, które zostały uruchomione w bloku Logs platformy Azure. Nie obejmuje zapytań wykonywanych przez zaplanowane reguły analizy przy użyciu **grafu badania** ani na stronie **łowiectwa** wskaźnikowego platformy Azure.
> - Może wystąpić krótkie opóźnienie między czasem uruchomienia zapytania a zapełnieniem danych w tabeli **LAQueryLogs** . Zalecamy oczekiwanie około 5 minut na wykonanie zapytania dotyczącego danych inspekcji w tabeli **LAQueryLogs** .
>


**Aby zbadać tabelę LAQueryLogs**:

1. Tabela **LAQueryLogs** nie jest domyślnie włączona w obszarze roboczym log Analytics. Aby korzystać z danych **LAQueryLogs** podczas przeprowadzania inspekcji na platformie Azure, należy najpierw włączyć **LAQueryLogs** w obszarze **ustawień diagnostycznych** obszaru roboczego log Analytics.

    Aby uzyskać więcej informacji, zobacz [Inspekcja zapytań w dziennikach Azure monitor](../azure-monitor/logs/query-audit.md).


1. Następnie wykonaj zapytanie o dane przy użyciu KQL, tak jak w przypadku każdej innej tabeli.

    Na przykład następujące zapytanie pokazuje, ile zapytań zostało uruchomionych w ciągu ostatniego tygodnia, w ciągu dnia:

    ```kql
    LAQueryLogs
    | where TimeGenerated > ago(7d)
    | summarize events_count=count() by bin(TimeGenerated, 1d)
    ```

W poniższych sekcjach przedstawiono więcej przykładowych zapytań do uruchomienia w tabeli **LAQueryLogs** podczas przeprowadzania inspekcji działań w środowisku SOC przy użyciu platformy Azure.

### <a name="the-number-of-queries-run-where-the-response-wasnt-ok"></a>Liczba zapytań, które są uruchamiane, gdy odpowiedź nie była "OK"

Poniższe zapytanie tabeli **LAQueryLogs** pokazuje liczbę uruchomionych zapytań, w których odebrano wszystkie inne niż odpowiedź HTTP **200 OK** . Na przykład ta liczba będzie zawierać zapytania, które nie zostały uruchomione.

```kql
LAQueryLogs
| where ResponseCode != 200 
| count 
```

### <a name="show-users-for-cpu-intensive-queries"></a>Pokaż użytkowników na potrzeby zapytań intensywnie korzystających z procesora CPU

Poniższe zapytanie tabeli **LAQueryLogs** wyświetla listę użytkowników, którzy uruchomili większość zapytań intensywnie korzystających z procesora CPU, na podstawie procesora CPU i długości czasu zapytania.

```kql
LAQueryLogs
|summarize arg_max(StatsCPUTimeMs, *) by AADClientId
| extend User = AADEmail, QueryRunTime = StatsCPUTimeMs
| project User, QueryRunTime, QueryText
| order by QueryRunTime desc
```

### <a name="show-users-who-ran-the-most-queries-in-the-past-week"></a>Pokaż użytkowników, którzy uruchomili najwięcej zapytań w ubiegłym tygodniu

Poniższe zapytanie tabeli **LAQueryLogs** wyświetla listę użytkowników, którzy uruchomili najwięcej zapytań w ostatnim tygodniu.

```kql
LAQueryLogs
| where TimeGenerated > ago(7d)
| summarize events_count=count() by AADEmail
| extend UserPrincipalName = AADEmail, Queries = events_count
| join kind= leftouter (
    SigninLogs)
    on UserPrincipalName
| project UserDisplayName, UserPrincipalName, Queries
| summarize arg_max(Queries, *) by UserPrincipalName
| sort by Queries desc
```

## <a name="configuring-alerts-for-azure-sentinel-activities"></a>Konfigurowanie alertów dotyczących działań wskaźnikowych platformy Azure

Aby utworzyć aktywne alerty, możesz użyć zasobów usługi Azure wskaźnikowej do inspekcji.

Jeśli na przykład masz poufne tabele w obszarze roboczym wskaźnikowego platformy Azure, użyj następującego zapytania, aby powiadomić użytkownika za każdym razem, gdy są zapytania o te tabele:

```kql
LAQueryLogs
| where QueryText contains "[Name of sensitive table]"
| where TimeGenerated > ago(1d)
| extend User = AADEmail, Query = QueryText
| project User, Query
```


## <a name="next-steps"></a>Następne kroki

W obszarze wskaźnikowym platformy Azure należy użyć skoroszytu **inspekcji obszaru roboczego** , aby przeprowadzić inspekcję działań w środowisku SOC.

Aby uzyskać więcej informacji, zobacz [Samouczek: wizualizowanie i monitorowanie danych](tutorial-monitor-your-data.md).