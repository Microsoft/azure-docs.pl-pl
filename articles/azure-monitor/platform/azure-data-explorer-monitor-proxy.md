---
title: Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
description: Użyj usługi Azure Eksplorator danych, aby wykonywać zapytania między produktami między usługą Azure Eksplorator danych, Log Analytics obszary robocze i klasyczne aplikacje Application Insights w Azure Monitor.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 8a503a5456fc28bd1b3ebb69c784fc59b3c6e7df
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92050078"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
Klaster usługi Azure Eksplorator danych proxy umożliwia wykonywanie zapytań między produktami między usługą Azure Eksplorator danych, obszarami roboczymi Log Analytics i klasycznymi aplikacjami Application Insights w Azure Monitor. Obszary robocze Log Analytics można mapować w aplikacjach Azure Monitor i klasycznych Application Insights jako klastrów proxy. Następnie można wykonać zapytanie dotyczące klastra proxy przy użyciu narzędzi usługi Azure Eksplorator danych i odwołać się do niego w zapytaniu między klastrami. W tym artykule pokazano, jak nawiązać połączenie z klastrem proxy, dodać klaster proxy do interfejsu użytkownika sieci Web usługi Azure Eksplorator danych i uruchamiać zapytania względem obszarów roboczych Log Analytics lub klasycznych aplikacji Application Insights z usługi Azure Eksplorator danych.

Na poniższym diagramie przedstawiono przepływ serwera proxy usługi Azure Eksplorator danych:

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-monitor-proxy-flow.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::


> [!NOTE]
> Serwer proxy usługi Azure Eksplorator danych jest w publicznej wersji zapoznawczej. [Połącz się z serwerem proxy,](#connect-to-the-proxy) aby włączyć funkcję proxy dla klastrów. 

## <a name="connect-to-the-proxy"></a>Łączenie z serwerem proxy
Aby połączyć obszar roboczy Log Analytics lub klasyczną aplikację Application Insights, Otwórz[interfejs użytkownika sieci Web platformy Azure Eksplorator danych](https://dataexplorer.azure.com/clusters). Przed nawiązaniem połączenia z klastrem Log Analytics lub Application Insights należy sprawdzić, czy w menu po lewej stronie jest wyświetlany klaster macierzysty platformy Azure Eksplorator danych (na przykład klaster *pomocy* ).

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::

Kliknij pozycję **Dodaj klaster** , a następnie Dodaj adres URL Log Analytics lub Application Insights klastra w jednym z następujących formatów. 
    
* Dla Log Analytics: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
* Dla Application Insights: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

Kliknij przycisk **Dodaj** , aby nawiązać połączenie.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::
 
> [!NOTE]
> W przypadku dodania połączenia do więcej niż jednego klastra proxy nadaj każdej innej nazwie. W przeciwnym razie wszystkie będą mieć taką samą nazwę w lewym okienku.

Po nawiązaniu połączenia Log Analytics lub klaster Application Insights zostanie wyświetlony w lewym okienku z natywnym klastrem usługi Azure Eksplorator danych. 

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::
 
> [!NOTE]
> Liczba obszarów roboczych, które mogą być mapowane Azure Monitor, jest ograniczona do 100.

## <a name="create-queries-using-azure-monitor-data"></a>Tworzenie zapytań przy użyciu danych Azure Monitor

Można uruchamiać zapytania przy użyciu narzędzi klienckich, które obsługują zapytania Kusto, takie jak Eksplorator Kusto, interfejs użytkownika sieci Web platformy Azure Eksplorator danych, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Jarvis, soczewki i interfejs API REST.

> [!NOTE]
> Funkcja serwera proxy usługi Azure Eksplorator danych jest używana tylko do pobierania danych. Aby uzyskać więcej informacji, zobacz temat [Obsługa funkcji](#function-supportability).

> [!TIP]
> * Nazwa bazy danych powinna mieć taką samą nazwę jak zasób określony w klastrze proxy. W nazwach jest uwzględniana wielkość liter.
> * W przypadku zapytań między klastrami upewnij się, że nazwy aplikacji Application Insights i Log Analytics obszary robocze są poprawne.
>     * Jeśli nazwy zawierają znaki specjalne, są one zastępowane przez kodowanie adresów URL w nazwie klastra proxy. 
>     * Jeśli nazwy zawierają znaki, które nie są zgodne z [regułami nazw identyfikatorów KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names), są zastępowane **-** znakiem kreski.

### <a name="direct-query-from-your-log-analytics-or-application-insights-proxy-cluster"></a>Bezpośrednie zapytanie z klastra Log Analytics lub Application Insights proxy

Uruchamianie zapytań w klastrze Log Analytics lub Application Insights. Sprawdź, czy klaster został wybrany w lewym okienku. 
 
```kusto
Perf | take 10 // Demonstrate query through the proxy on the Log Analaytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-proxy-cluster-and-the-azure-data-explorer-native-cluster"></a>Krzyżowe zapytania dotyczące Log Analytics lub Application Insights klastra proxy i klastra Azure Eksplorator danych Native

Gdy uruchamiasz zapytania między klastrami z serwera proxy, sprawdź, czy w lewym okienku wybrano klaster Azure Eksplorator danych Native. W poniższych przykładach pokazano, jak łączyć tabele klastra Eksplorator danych platformy Azure przy użyciu operatora [Union](/azure/data-explorer/kusto/query/unionoperator) z obszarem roboczym log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```
Użycie [ `join` operatora](/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer)zamiast Union może wymagać [wskazówki](/azure/data-explorer/kusto/query/joinoperator?pivots=azuredataexplorer#join-hints) do uruchomienia jej w klastrze usługi Azure Eksplorator danych Native (a nie na serwerze proxy). 

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Dołączanie danych z klastra Eksplorator danych platformy Azure w jednej dzierżawie przy użyciu zasobu Azure Monitor w innym

Zapytania między dzierżawcami nie są obsługiwane przez serwer proxy usługi Azure Eksplorator danych. Użytkownik jest zalogowany do pojedynczej dzierżawy na potrzeby uruchamiania zapytania obejmującego oba zasoby.

Jeśli zasób Eksplorator danych platformy Azure znajduje się w dzierżawie "A", a obszar roboczy Log Analytics znajduje się w dzierżawie "B", użyj jednej z następujących dwóch metod:

- Usługa Azure Eksplorator danych pozwala dodawać role dla podmiotów zabezpieczeń w różnych dzierżawach. Dodaj swój identyfikator użytkownika w dzierżawie "B" jako autoryzowany użytkownik w klastrze usługi Azure Eksplorator danych. Sprawdź, czy właściwość *["TrustedExternalTenant"](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* w klastrze Eksplorator danych platformy Azure zawiera dzierżawcę "B". Uruchom zapytanie krzyżowe w pełni w dzierżawie "B".

- Użyj [Lighthouse](/azure/lighthouse/) , aby zaprojektować zasób Azure monitor w dzierżawie "A".

### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Nawiązywanie połączenia z klastrami Eksplorator danych platformy Azure od różnych dzierżawców

Eksplorator Kusto automatycznie loguje użytkownika do dzierżawy, do której należy konto użytkownika. Aby uzyskać dostęp do zasobów w innych dzierżawcach przy użyciu tego samego konta użytkownika, `tenantId` musi być jawnie określona w parametrach połączenia: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Obsługa funkcji

Klaster usługi Azure Eksplorator danych proxy obsługuje funkcje zarówno dla Log Analytics, jak i Application Insights. Ta funkcja umożliwia wykonywanie zapytań między klastrami w celu bezpośredniego odwoływania się do Azure Monitor funkcji tabelarycznej.

Serwer proxy obsługuje następujące polecenia:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Na poniższej ilustracji przedstawiono przykład zapytania dotyczącego funkcji tabelarycznej z internetowego interfejsu użytkownika usługi Azure Eksplorator danych. Aby użyć funkcji, uruchom ją w oknie zapytania.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Przepływ serwera proxy Eksploratora danych platformy Azure.":::
 
> [!NOTE]
> Azure Monitor obsługuje tylko funkcje tabelaryczne, które nie obsługują parametrów.

## <a name="additional-syntax-examples"></a>Dodatkowe przykłady składni

Podczas wywoływania Log Analytics lub Application Insights klastrów dostępne są następujące opcje składni:

|Opis składni  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Baza danych w klastrze, która zawiera tylko zdefiniowany zasób w tej subskrypcji (**zalecane w przypadku zapytań między klastrami**) |   klaster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | klaster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| Klaster zawierający wszystkie aplikacje/obszary robocze w tej subskrypcji    |     klaster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    klaster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|Klaster zawierający wszystkie aplikacje/obszary robocze w subskrypcji i są członkami tej grupy zasobów    |   klaster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    klaster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|Klaster zawierający tylko zdefiniowany zasób w tej subskrypcji      |    klaster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  klaster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat [struktury danych log Analytics obszarów roboczych i Application Insights](data-platform-logs.md).
- Dowiedz się, jak [pisać zapytania w usłudze Azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/write-queries).