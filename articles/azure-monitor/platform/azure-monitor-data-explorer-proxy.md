---
title: Zapytania między zasobami Eksplorator danych platformy Azure przy użyciu Azure Monitor
description: Użyj Azure Monitor, aby wykonywać zapytania między różnymi produktami między usługą Azure Eksplorator danych, Log Analytics obszary robocze i klasyczne aplikacje Application Insights w Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: 8884fd56cfc1e18a0a358d6902f7f8bcbfc1f5d3
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553882"
---
# <a name="cross-resource-query-azure-data-explorer-using-azure-monitor"></a>Zapytania między zasobami Eksplorator danych platformy Azure przy użyciu Azure Monitor
Azure Monitor obsługuje zapytania obejmujące wiele usług między usługą Azure Eksplorator danych, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)i [log Analytics (La)](/azure/azure-monitor/platform/data-platform-logs). Następnie można wykonać zapytanie dotyczące klastra Eksplorator danych platformy Azure przy użyciu narzędzi Log Analytics/Application Insights i odwołać się do niego w zapytaniu o wiele usług. W tym artykule przedstawiono sposób tworzenia zapytania między usługami.

Przepływ Azure Monitor krzyżowej: Usługa :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-monitor-data-explorer-flow.png" alt-text="Azure monitor i usługa azure Eksplorator danych cross Service Flow.":::

>[!NOTE]
>* Azure Monitor proxy jest w prywatnej wersji zapoznawczej — wymagana jest AllowListing.
>* Skontaktuj się z [zespołem usługi](mailto:ADXProxy@microsoft.com) , korzystając z jakichkolwiek pytań.
## <a name="cross-query-your-log-analytics-or-application-insights-resources-and-azure-data-explorer"></a>Krzyżowe wykonywanie zapytań dotyczących zasobów Log Analytics lub Application Insights i platformy Azure Eksplorator danych

Zapytania krzyżowe można uruchamiać przy użyciu narzędzi klienckich, które obsługują zapytania Kusto, takie jak: Log Analytics internetowego interfejsu użytkownika, skoroszytów, programu PowerShell, interfejsu API REST i nie tylko.

* Wprowadź identyfikator klastra Eksplorator danych platformy Azure w zapytaniu w ramach wzorca "ADX", po którym następuje nazwa bazy danych i tabela.

```kusto
adx('https://help.kusto.windows.net/Samples').StormEvents
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-cross-service-query-example.png" alt-text="Przykład zapytania cross Service.":::

> [!NOTE]
>* W nazwach baz danych jest rozróżniana wielkość liter.
>* Zapytanie krzyżowe nie jest obsługiwane.
## <a name="combining-azure-data-explorer-cluster-tables-using-union-and-join-with-la-workspace"></a>Łączenie tabel klastra Eksplorator danych platformy Azure (przy użyciu funkcji Union i join) z obszarem roboczym LA.

```kusto
union customEvents, adx('https://help.kusto.windows.net/Samples').StormEvents
| take 10
```
```kusto
let CL1 = adx('https://help.kusto.windows.net/Samples').StormEvents;
union customEvents, CL1 | take 10
```
:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-union-cross-query.png" alt-text="Przykład zapytania międzyusługowego z Unią.":::

>[!Tip]
>* Format skrócony jest dozwolony — ClusterName/InitialCatalog. Na przykład ADX ("pomoc/przykłady") jest tłumaczony na ADX ("help. Kusto. Windows. NET/Samples")
## <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Dołączanie danych z klastra Eksplorator danych platformy Azure w jednej dzierżawie przy użyciu zasobu Azure Monitor w innym

Zapytania między różnymi dzierżawcami nie są obsługiwane. Użytkownik jest zalogowany do pojedynczej dzierżawy na potrzeby uruchamiania zapytania obejmującego oba zasoby.

Jeśli zasób Eksplorator danych platformy Azure znajduje się w dzierżawie "A", a obszar roboczy Log Analytics znajduje się w dzierżawie "B", użyj jednej z następujących dwóch metod:

*  Usługa Azure Eksplorator danych pozwala dodawać role dla podmiotów zabezpieczeń w różnych dzierżawach. Dodaj swój identyfikator użytkownika w dzierżawie "B" jako autoryzowany użytkownik w klastrze usługi Azure Eksplorator danych. Sprawdź, czy właściwość *["TrustedExternalTenant"](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* w klastrze Eksplorator danych platformy Azure zawiera dzierżawcę "B". Uruchom zapytanie krzyżowe w pełni w dzierżawie "B".
*  Użyj [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) , aby zaprojektować zasób Azure monitor w dzierżawie "A".
## <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Nawiązywanie połączenia z klastrami Eksplorator danych platformy Azure od różnych dzierżawców

Eksplorator Kusto automatycznie loguje użytkownika do dzierżawy, do której należy konto użytkownika. Aby uzyskać dostęp do zasobów w innych dzierżawcach przy użyciu tego samego konta użytkownika, `tenantId` musi być jawnie określona w parametrach połączenia: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="next-steps"></a>Następne kroki
* [Zapytania zapisu](https://docs.microsoft.com/azure/data-explorer/write-queries)
* [Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/query-monitor-data)
* [Wykonywanie zapytań dotyczących dzienników wielu zasobów w Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/cross-workspace-query)






