---
title: Zapytanie dotyczące wielu zasobów Eksplorator danych platformy Azure przy użyciu Azure Monitor
description: Użyj Azure Monitor, aby wykonywać zapytania obejmujące wiele produktów między usługą Azure Eksplorator danych, obszarami roboczymi Log Analytics i klasycznymi aplikacjami Application Insights w Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: a800f78df26ce76144994bb9da2cac6271323eb4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103419426"
---
# <a name="cross-resource-query-azure-data-explorer-by-using-azure-monitor"></a>Zapytanie dotyczące wielu zasobów Eksplorator danych platformy Azure przy użyciu Azure Monitor
Azure Monitor obsługuje zapytania międzyusługowe między usługą Azure Eksplorator danych, [Application Insights](../app/app-insights-overview.md)i [log Analytics](../logs/data-platform-logs.md). Następnie można wykonać zapytanie dotyczące klastra usługi Azure Eksplorator danych przy użyciu narzędzi Log Analytics/Application Insights i odwołać się do niego w kwerendzie międzyusługowej. W tym artykule pokazano, jak utworzyć zapytanie międzyusługowe.

Na poniższym diagramie przedstawiono Azure Monitor przepływ między usługami:

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Diagram przedstawiający przepływ zapytań między użytkownikiem, Azure Monitor, serwerem proxy i Eksplorator danych platformy Azure.":::

>[!NOTE]
> Azure Monitor zapytania międzyusługowego jest w publicznej wersji zapoznawczej. Skontaktuj się z [zespołem usługi](mailto:ADXProxy@microsoft.com) , korzystając z jakichkolwiek pytań.

## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Wykonaj zapytania krzyżowe dotyczące Log Analytics lub Application Insights zasobów i platformy Azure Eksplorator danych

Zapytania obejmujące wiele zasobów można uruchamiać przy użyciu narzędzi klienckich, które obsługują zapytania Kusto. Przykłady tych narzędzi obejmują Log Analytics interfejs użytkownika sieci Web, skoroszyty, program PowerShell i interfejs API REST.

Wprowadź identyfikator klastra Eksplorator danych platformy Azure w zapytaniu w ramach `adx` wzorca, a następnie nazwę bazy danych i tabelę.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Zrzut ekranu przedstawiający przykład zapytania międzyusługowego.":::

> [!NOTE]
>* W nazwach baz danych jest rozróżniana wielkość liter.
>* Zapytanie krzyżowe jako alert nie jest obsługiwane.
>* Zidentyfikowanie kolumny sygnatur czasowych w klastrze nie jest obsługiwane, Log Analytics interfejs API zapytań nie zostanie przemieszczony razem z filtrem czasu

## <a name="combine-azure-data-explorer-cluster-tables-with-a-log-analytics-workspace"></a>Łączenie tabel klastrów Eksplorator danych platformy Azure z obszarem roboczym Log Analytics

Użyj `union` polecenia, aby połączyć tabele klastra z obszarem roboczym log Analytics.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Zrzut ekranu pokazujący przykład zapytania międzyusługowego z poleceniem Union.":::

> [!Tip]
> Format skrócony jest dozwolony: *ClusterName* / *InitialCatalog*. Na przykład, `adx('help/Samples')` jest przetłumaczone na `adx('help.kusto.windows.net/Samples')` .

## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Dołączanie danych z klastra Eksplorator danych platformy Azure w jednej dzierżawie przy użyciu zasobu Azure Monitor w innym

Zapytania między różnymi dzierżawcami nie są obsługiwane. Użytkownik jest zalogowany do pojedynczej dzierżawy na potrzeby uruchamiania zapytania obejmującego oba zasoby.

Jeśli zasób Eksplorator danych platformy Azure znajduje się w dzierżawie a, Log Analytics obszar roboczy znajduje się w dzierżawie B, użyj jednej z następujących metod:

*  Usługa Azure Eksplorator danych pozwala dodawać role dla podmiotów zabezpieczeń w różnych dzierżawach. Dodaj swój identyfikator użytkownika w dzierżawie B jako autoryzowany użytkownik w klastrze usługi Azure Eksplorator danych. Sprawdź, czy właściwość [TrustedExternalTenant](/powershell/module/az.kusto/update-azkustocluster) klastra usługi Azure Eksplorator danych zawiera dzierżawcę b. w pełni wykonaj zapytanie w dzierżawie b.
*  Użyj [Lighthouse](../../lighthouse/index.yml) , aby zaprojektować zasób Azure monitor w dzierżawie A.

## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Nawiązywanie połączenia z klastrami Eksplorator danych platformy Azure od różnych dzierżawców

Eksplorator Kusto automatycznie loguje użytkownika do dzierżawy, do której należy konto użytkownika. Aby uzyskać dostęp do zasobów w innych dzierżawcach przy użyciu tego samego konta użytkownika, należy jawnie określić `TenantId` w parametrach połączenia:

`Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=TenantId`

## <a name="next-steps"></a>Następne kroki
* [Pisanie zapytań](/azure/data-explorer/write-queries)
* [Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych](/azure/data-explorer/query-monitor-data)
* [Wykonywanie zapytań dotyczących dzienników wielu zasobów w Azure Monitor](../logs/cross-workspace-query.md)