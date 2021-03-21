---
title: Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
description: Użyj usługi Azure Eksplorator danych, aby wykonywać zapytania między produktami między usługą Azure Eksplorator danych, Log Analytics obszary robocze i klasyczne aplikacje Application Insights w Azure Monitor.
author: osalzberg
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 65dba60a798b1157a44a7a198b8eba7de1e8fe81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031263"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Wykonywanie zapytań dotyczących danych w Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)

Eksplorator danych platformy Azure obsługuje zapytania obejmujące wiele usług między usługą Azure Eksplorator danych, [Application Insights (AI)](../app/app-insights-overview.md)i [log Analytics (La)](./data-platform-logs.md). Następnie można wykonać zapytanie dotyczące obszaru roboczego Log Analytics/Application Insights przy użyciu narzędzi Eksplorator danych platformy Azure i odwołać się do niego w zapytaniu o wiele usług. W tym artykule przedstawiono sposób tworzenia zapytania krzyżowego i dodawania obszaru roboczego Log Analytics/Application Insights do interfejsu użytkownika sieci Web usługi Azure Eksplorator danych.

Przepływ zapytań między usługami Eksplorator danych platformy Azure: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="przepływ serwera proxy w usłudze Azure Data Explorer.":::

> [!NOTE]
> * Możliwość wykonywania zapytań dotyczących danych Azure Monitor z platformy Azure Eksplorator danych, bezpośrednio z poziomu narzędzi klienckich Eksplorator danych platformy Azure lub pośrednio przez uruchomienie zapytania w klastrze Eksplorator danych platformy Azure, jest w trybie podglądu.
>* Skontaktuj się z zespołem [zapytań międzyusługowych](mailto:adxproxy@microsoft.com) , korzystając z jakichkolwiek pytań.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Dodawanie obszaru roboczego Log Analytics/Application Insights do narzędzia klienckiego platformy Azure Eksplorator danych

1. Przed nawiązaniem połączenia z klastrem Log Analytics lub Application Insights należy sprawdzić, czy w menu po lewej stronie jest wyświetlany klaster macierzysty platformy Azure Eksplorator danych (na przykład klaster *pomocy* ).

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Klaster platformy Azure Eksplorator danych Native.":::

 W interfejsie użytkownika usługi Azure Eksplorator danych ( https://dataexplorer.azure.com/clusters) Wybierz pozycję **Dodaj klaster**.

2. W oknie **Dodawanie klastra** Dodaj adres URL klastra La lub AI.

    * Dla LA: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * Dla AI: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * Wybierz pozycję **Dodaj**.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="Dodaj klaster.":::
 
>[!NOTE]
>W przypadku dodania połączenia do więcej niż jednego obszaru roboczego Log Analytics/Application Insights nadaj każdej innej nazwie. W przeciwnym razie wszystkie będą mieć taką samą nazwę w lewym okienku.

 Po nawiązaniu połączenia obszar roboczy Log Analytics lub Application Insights zostanie wyświetlony w okienku po lewej stronie, w którym znajduje się natywny klaster Eksplorator danych platformy Azure.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics i klastry Eksplorator danych platformy Azure.":::
 
> [!NOTE]
> Liczba obszarów roboczych, które mogą być mapowane Azure Monitor, jest ograniczona do 100.

## <a name="create-queries-using-azure-monitor-data"></a>Tworzenie zapytań przy użyciu danych Azure Monitor

Można uruchamiać zapytania przy użyciu narzędzi klienckich, które obsługują zapytania Kusto, takie jak Eksplorator Kusto, interfejs użytkownika sieci Web platformy Azure Eksplorator danych, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, soczewki, interfejs API REST.

> [!NOTE]
> Możliwość wykonywania zapytań między usługami jest używana tylko do pobierania danych. Aby uzyskać więcej informacji, zobacz temat [Obsługa funkcji](#function-supportability).

> [!TIP]
> * Nazwa bazy danych powinna mieć taką samą nazwę jak zasób określony w zapytaniu o wiele usług. W nazwach jest uwzględniana wielkość liter.
> * W przypadku zapytań między klastrami upewnij się, że nazwy aplikacji Application Insights i Log Analytics obszary robocze są poprawne.
> * Jeśli nazwy zawierają znaki specjalne, są one zastępowane przez kodowanie adresów URL w zapytaniu o wiele usług.
> * Jeśli nazwy zawierają znaki, które nie są zgodne z [regułami nazw identyfikatorów KQL](/azure/data-explorer/kusto/query/schema-entities/entity-names), są zastępowane **-** znakiem kreski.

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Bezpośrednie zapytanie dotyczące Log Analytics lub Application Insights obszarów roboczych z narzędzi Azure Eksplorator danych Client Tools

Uruchom zapytania dotyczące Log Analytics lub Application Insights obszarów roboczych. Upewnij się, że obszar roboczy jest wybrany w lewym okienku.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="Log Analytics obszarze roboczym zapytań.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Krzyżowe zapytania dotyczące Log Analytics lub Application Insights i klastra platformy Azure Eksplorator danych Native

Podczas uruchamiania zapytań usługi między klastrami upewnij się, że w okienku po lewej stronie jest wybrany klaster Azure Eksplorator danych Native. W poniższych przykładach pokazano, jak łączyć tabele klastrów Eksplorator danych platformy Azure [przy użyciu usługi Union](/azure/data-explorer/kusto/query/unionoperator) z log Analytics obszar roboczy.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Zapytanie o wiele usług z Eksplorator danych platformy Azure.":::

Użycie [ `join` operatora](/azure/data-explorer/kusto/query/joinoperator)zamiast Union może wymagać [`hint`](/azure/data-explorer/kusto/query/joinoperator#join-hints) uruchomienia go w klastrze macierzystym platformy Azure Eksplorator danych.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>Dołączanie danych z klastra Eksplorator danych platformy Azure w jednej dzierżawie przy użyciu zasobu Azure Monitor w innym

Zapytania między różnymi dzierżawcami nie są obsługiwane. Użytkownik jest zalogowany do pojedynczej dzierżawy na potrzeby uruchamiania zapytania obejmującego oba zasoby.

Jeśli zasób Eksplorator danych platformy Azure znajduje się w dzierżawie "A", a obszar roboczy Log Analytics znajduje się w dzierżawie "B", użyj jednej z następujących dwóch metod:

1. Usługa Azure Eksplorator danych pozwala dodawać role dla podmiotów zabezpieczeń w różnych dzierżawach. Dodaj swój identyfikator użytkownika w dzierżawie "B" jako autoryzowany użytkownik w klastrze usługi Azure Eksplorator danych. Sprawdź, czy właściwość *["TrustedExternalTenant"](/powershell/module/az.kusto/update-azkustocluster)* w klastrze Eksplorator danych platformy Azure zawiera dzierżawcę "B". Uruchom zapytanie krzyżowe w pełni w dzierżawie "B".

2. Użyj [Lighthouse](../../lighthouse/index.yml) , aby zaprojektować zasób Azure monitor w dzierżawie "A".
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>Nawiązywanie połączenia z klastrami Eksplorator danych platformy Azure od różnych dzierżawców

Eksplorator Kusto automatycznie loguje użytkownika do dzierżawy, do której należy konto użytkownika. Aby uzyskać dostęp do zasobów w innych dzierżawcach przy użyciu tego samego konta użytkownika, `tenantId` musi być jawnie określona w parametrach połączenia: `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>Obsługa funkcji

Usługi Azure Eksplorator danych krzyżowe zapytania obsługują funkcje dla obu Application Insights i Log Analytics.
Ta funkcja umożliwia wykonywanie zapytań między klastrami w celu bezpośredniego odwoływania się do Azure Monitor funkcji tabelarycznej.
Następujące polecenia są obsługiwane w przypadku zapytania cross Service:

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

Na poniższej ilustracji przedstawiono przykład zapytania dotyczącego funkcji tabelarycznej z internetowego interfejsu użytkownika usługi Azure Eksplorator danych.
Aby użyć funkcji, uruchom ją w oknie zapytania.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Wykonaj zapytanie dotyczące funkcji tabelarycznej z internetowego interfejsu użytkownika usługi Azure Eksplorator danych.":::

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
- Dowiedz się, jak [pisać zapytania w usłudze Azure Eksplorator danych](/azure/data-explorer/write-queries).