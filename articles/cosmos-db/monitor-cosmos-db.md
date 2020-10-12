---
title: Azure Cosmos DB monitorowania | Microsoft Docs
description: Dowiedz się, jak monitorować wydajność i dostępność Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/24/2020
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: 12bf87e16bf4506f2015dd75fb360f8de8399902
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797823"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB monitorowania

Jeśli masz krytyczne aplikacje i procesy biznesowe polegające na zasobach platformy Azure, chcesz monitorować te zasoby pod kątem ich dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez bazy danych usługi Azure Cosmos oraz sposób używania funkcji Azure Monitor do analizowania i generowania alertów dotyczących tych danych.

Dane można monitorować za pomocą metryk po stronie klienta i serwera. Korzystając z metryk po stronie serwera, można monitorować dane przechowywane w Azure Cosmos DB przy użyciu następujących opcji:

* **Monitoruj z portalu Azure Cosmos DB:** Możesz monitorować za pomocą metryk dostępnych na karcie **metryki** konta usługi Azure Cosmos. Metryki na tej karcie obejmują przepływność, magazyn, dostępność, opóźnienie, spójność i metryki na poziomie systemu. Domyślnie te metryki mają okres przechowywania wynoszący 7 dni. Aby dowiedzieć się więcej, zobacz sekcję [monitorowanie danych zebranych z Azure Cosmos DB](#monitoring-from-azure-cosmos-db) w tym artykule.

* **Monitoruj przy użyciu metryk w usłudze Azure Monitor:** Można monitorować metryki konta usługi Azure Cosmos i tworzyć pulpity nawigacyjne na podstawie Azure Monitor. Azure Monitor domyślnie zbiera metryki Azure Cosmos DB, nie skonfigurowano niczego jawnie. Te metryki są zbierane z dokładnością do jednej minuty, stopień szczegółowości może się różnić w zależności od wybranej metryki. Domyślnie te metryki mają okres przechowywania wynoszący 30 dni. Większość metryk dostępnych w poprzednich opcjach jest również dostępna w tych metrykach. W wartościach wymiarów dla metryk, takich jak nazwa kontenera, nie jest rozróżniana wielkość liter. Dlatego należy użyć porównania bez uwzględniania wielkości liter podczas porównywania ciągów dla tych wartości wymiarów. Aby dowiedzieć się więcej, zobacz sekcję [Analizowanie danych metryk](#analyze-metric-data) w tym artykule.

* **Monitoruj z dziennikami diagnostycznymi w Azure Monitor:** Możesz monitorować dzienniki konta usługi Azure Cosmos i tworzyć pulpity nawigacyjne na podstawie Azure Monitor. Dane telemetryczne, takie jak zdarzenia i ślady występujące na drugim poziomie szczegółowości, są przechowywane jako dzienniki. Na przykład, jeśli przepływność kontenera jest zmieniana, właściwości konta Cosmos są zmieniane te zdarzenia są przechwytywane w dziennikach. Te dzienniki można analizować, uruchamiając zapytania dotyczące zebranych danych. Aby dowiedzieć się więcej, zobacz sekcję [Analizowanie danych dziennika](#analyze-log-data) w tym artykule.

* **Monitoruj programowo przy użyciu zestawów SDK:** Konto usługi Azure Cosmos można monitorować programowo przy użyciu zestawów .NET, Java, Python, Node.js SDK i nagłówków w interfejsie API REST. Aby dowiedzieć się więcej, zobacz sekcję [monitorowanie Azure Cosmos DB programowo](#monitor-cosmosdb-programmatically) w tym artykule.

Na poniższej ilustracji przedstawiono różne opcje dostępne do monitorowania konta Azure Cosmos DB przez Azure Portal:

:::image type="content" source="media/monitor-cosmos-db/monitoring-options-portal.png" alt-text="Opcje monitorowania dostępne w Azure Portal" border="false":::

Korzystając z Azure Cosmos DB, na stronie klienta można zebrać szczegóły dotyczące opłaty za żądania, identyfikator działania, informacje o śledzeniu wyjątku/stosu, stan HTTP/kod stanu podrzędnego, ciąg diagnostyczny w celu debugowania wszelkich problemów, które mogą wystąpić. Te informacje są również wymagane, jeśli musisz skontaktować się z zespołem pomocy technicznej Azure Cosmos DB.  

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Azure Cosmos DB tworzy dane monitorowania przy użyciu [Azure monitor](../azure-monitor/overview.md) , który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure, a także zasobów w innych chmurach i lokalnych.

Jeśli nie znasz jeszcze monitorowania usług platformy Azure, Zacznij od artykułu [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) , w którym opisano następujące kwestie:

* Co to jest Azure Monitor?
* Koszty związane z monitorowaniem
* Monitorowanie danych zebranych na platformie Azure
* Konfigurowanie zbierania danych
* Standardowe narzędzia na platformie Azure na potrzeby analizowania danych monitorowania i powiadamiania o nich

Poniższe sekcje dotyczą tego artykułu, opisując określone dane zebrane z Azure Cosmos DB i dostarczając przykłady dotyczące konfigurowania zbierania danych i analizowania tych danych za pomocą narzędzi platformy Azure.

## <a name="azure-monitor-for-azure-cosmos-db"></a>Azure Monitor Azure Cosmos DB

Azure Monitor dla Azure Cosmos DB jest oparta na [funkcjach skoroszytów Azure monitor](../azure-monitor/platform/workbooks-overview.md) i korzysta z tych samych danych monitorowania zebranych dla Cosmos DB opisanych w poniższych sekcjach. Użyj Azure Monitor, aby zapoznać się z ogólną wydajnością, niepowodzeniami, pojemnością i kondycją operacyjną wszystkich zasobów Azure Cosmos DB w ujednoliconym środowisku interaktywnym i wykorzystać inne funkcje Azure Monitor do szczegółowej analizy i generowania alertów. Aby dowiedzieć się więcej, zobacz artykuł [eksplorowanie Azure Monitor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) .

> [!NOTE]
> Podczas tworzenia kontenerów upewnij się, że nie utworzysz dwóch kontenerów o takiej samej nazwie, ale o innej wielkości liter. Wynika to z faktu, że niektóre części platformy Azure nie uwzględniają wielkości liter. może to spowodować pomylenie/kolizję danych telemetrycznych i akcji w kontenerach z takimi nazwami.

## <a name="monitor-data-collected-from-azure-cosmos-db-portal"></a><a id="monitoring-from-azure-cosmos-db"></a> Monitorowanie danych zebranych z portalu Azure Cosmos DB

Azure Cosmos DB gromadzi te same rodzaje danych monitorowania jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych z zasobów platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Aby uzyskać szczegółowe informacje o dziennikach i metrykach utworzonych przez Azure Cosmos DB, zobacz [Informacje o danych monitorowania Azure Cosmos DB](monitor-cosmos-db-reference.md) .

Strona **Przegląd** w Azure Portal dla każdej bazy danych usługi Azure Cosmos zawiera krótki widok użycia bazy danych, w tym jego żądanie i co godzinę użycie rozliczeń. Jest to przydatne informacje, ale tylko niewielka ilość dostępnych danych monitorowania. Niektóre z tych danych są zbierane automatycznie i dostępne do analizy zaraz po utworzeniu bazy danych i włączeniu dodatkowej kolekcji danych z konfiguracją.

:::image type="content" source="media/monitor-cosmos-db/overview-page.png" alt-text="Opcje monitorowania dostępne w Azure Portal":::

## <a name="analyzing-metric-data"></a><a id="analyze-metric-data"></a> Analizowanie danych metryki

Azure Cosmos DB udostępnia niestandardowe środowisko pracy z metrykami. Aby uzyskać szczegółowe informacje na temat korzystania z tego środowiska Azure Cosmos DB, zobacz temat [monitorowanie i debugowanie Azure Cosmos DB metryki z Azure monitor](cosmos-db-azure-monitor-metrics.md) .

Metryki dla Azure Cosmos DB z metrykami z innych usług platformy Azure za pomocą Eksploratora metryk można analizować, otwierając **metryki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje na temat korzystania z tego narzędzia, zobacz [Rozpoczynanie pracy z usługą Azure Eksplorator metryk](../azure-monitor/platform/metrics-getting-started.md) . Wszystkie metryki dla Azure Cosmos DB znajdują się w przestrzeni nazw **Cosmos DB metrykach standardowych**. Podczas dodawania filtru do wykresu można użyć następujących wymiarów z tymi metrykami:

* CollectionName
* DatabaseName
* OperationType
* Region
* Stanu

### <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Wyświetl metryki poziomu operacji dla Azure Cosmos DB

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **monitor** na pasku nawigacyjnym po lewej stronie, a następnie wybierz pozycję **metryki**.

   :::image type="content" source="./media/monitor-cosmos-db/monitor-metrics-blade.png" alt-text="Opcje monitorowania dostępne w Azure Portal":::

1. W okienku **metryki** > **Wybierz zasób** > wybierz wymaganą **subskrypcję**i **grupę zasobów**. W polu **Typ zasobu**wybierz pozycję **konta Azure Cosmos DB**, wybierz jedno z istniejących kont usługi Azure Cosmos i wybierz pozycję **Zastosuj**.

   :::image type="content" source="./media/monitor-cosmos-db/select-cosmosdb-account.png" alt-text="Opcje monitorowania dostępne w Azure Portal":::

1. Następnie możesz wybrać metrykę z listy dostępnych metryk. Można wybrać metryki specyficzne dla jednostek żądań, magazynu, opóźnień, dostępności, Cassandra i innych. Aby uzyskać szczegółowe informacje na temat wszystkich dostępnych metryk na tej liście, zobacz artykuł [metryki według kategorii](monitor-cosmos-db-reference.md) . W tym przykładzie wybieramy **jednostki żądania** i **średnika** jako wartość agregacji.

   Oprócz tych szczegółów można także wybrać **zakres czasu** i **stopień szczegółowości** metryk. Co więcej, można wyświetlić metryki z ostatnich 30 dni.  Po zastosowaniu filtru na podstawie filtru zostanie wyświetlony wykres. W wybranym okresie można zobaczyć średnią liczbę jednostek żądań zużytych na minutę.  

   :::image type="content" source="./media/monitor-cosmos-db/metric-types.png" alt-text="Opcje monitorowania dostępne w Azure Portal":::

### <a name="add-filters-to-metrics"></a>Dodawanie filtrów do metryk

Można również filtrować metryki i wykres wyświetlany przez określoną **CollectionName**, **DatabaseName**, **OperationType**, **region**i **StatusCode**. Aby odfiltrować metryki, wybierz pozycję **Dodaj filtr** i wybierz wymaganą właściwość, taką jak **OperationType** , i wybierz wartość, taką jak **zapytanie**. Następnie Wykres wyświetla jednostki żądania wykorzystane dla operacji zapytania w wybranym okresie. Operacje wykonywane za pośrednictwem procedury składowanej nie są rejestrowane, więc nie są dostępne w ramach metryki operacji.

:::image type="content" source="./media/monitor-cosmos-db/add-metrics-filter.png" alt-text="Opcje monitorowania dostępne w Azure Portal":::

Metryki można grupować przy użyciu opcji **Zastosuj dzielenie** . Na przykład można grupować jednostki żądań na typ operacji i wyświetlać wykres dla wszystkich operacji na raz, jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/monitor-cosmos-db/apply-metrics-splitting.png" alt-text="Opcje monitorowania dostępne w Azure Portal":::

## <a name="analyzing-log-data"></a><a id="analyze-log-data"></a> Analizowanie danych dziennika

Dane w dziennikach Azure Monitor są przechowywane w tabelach, dla których każda tabela ma swój własny zestaw unikatowych właściwości. Azure Cosmos DB przechowuje dane w poniższych tabelach.

| Tabela | Opis |
|:---|:---|
| AzureDiagnostics | Wspólna tabela używana przez wiele usług do przechowywania dzienników zasobów. Dzienniki zasobów z Azure Cosmos DB mogą być identyfikowane za pomocą `MICROSOFT.DOCUMENTDB` .   |
| AzureActivity    | Wspólna tabela przechowująca wszystkie rekordy z dziennika aktywności.

> [!IMPORTANT]
> Po wybraniu opcji **dzienniki** z menu Azure Cosmos DB, log Analytics zostanie otwarty z zakresem zapytania ustawionym na bieżącą bazę danych usługi Azure Cosmos. Oznacza to, że zapytania dziennika będą zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić zapytanie, które zawiera dane z innych baz danych lub danych z innych usług platformy Azure, wybierz pozycję **dzienniki** z menu **Azure monitor** . Aby uzyskać szczegółowe informacje [, zobacz zakres zapytań dzienników i zakres czasu w Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics zapytań w Azure Monitor

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku wyszukiwania **przeszukiwania dzienników** , aby ułatwić monitorowanie kontenerów usługi Azure Cosmos. Te zapytania działają w [nowym języku](../log-analytics/log-analytics-log-search-upgrade.md).

Poniżej przedstawiono zapytania, których można użyć do monitorowania baz danych usługi Azure Cosmos.

* Aby wykonać zapytanie dotyczące wszystkich dzienników diagnostycznych z Azure Cosmos DB przez określony czas:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests"

    ```

* Aby wykonać zapytanie o wszystkie operacje, pogrupowane według zasobu:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Aby wyszukać wszystkie działania użytkownika pogrupowane według zasobu:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="Microsoft.DocumentDb" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a><a id="monitor-cosmosdb-programmatically"></a> Monitoruj Azure Cosmos DB programowo

Metryki na poziomie konta dostępne w portalu, takie jak użycie magazynu kont i łączna liczba żądań, nie są dostępne za pośrednictwem interfejsów API SQL. Można jednak pobrać dane użycia na poziomie kolekcji przy użyciu interfejsów API SQL. Aby pobrać dane na poziomie kolekcji, wykonaj następujące czynności:

* Aby użyć interfejsu API REST, [Wykonaj OPERACJĘ Get na kolekcji](https://msdn.microsoft.com/library/mt489073.aspx). Informacje o przydziale i użyciu dla kolekcji są zwracane w nagłówkach x-MS-Resource-limit i x-MS-Resource-Usage w odpowiedzi.

* Aby użyć zestawu .NET SDK, użyj metody [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) , która zwraca [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) , która zawiera wiele właściwości użycia, takich jak **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**i inne.

Aby uzyskać dostęp do dodatkowych metryk, użyj [zestawu SDK Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights). Dostępne definicje metryk można pobrać, wywołując:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metricDefinitions?api-version=2018-01-01
```

Aby pobrać pojedyncze metryki, użyj następującego formatu:

```http
https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/providers/microsoft.insights/metrics?timespan={StartTime}/{EndTime}&interval={AggregationInterval}&metricnames={MetricName}&aggregation={AggregationType}&`$filter={Filter}&api-version=2018-01-01
```

Aby dowiedzieć się więcej, zobacz artykuł dotyczący [interfejsu API REST usługi Azure Monitoring](../azure-monitor/platform/rest-api-walkthrough.md) .

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje o dziennikach i metrykach utworzonych przez Azure Cosmos DB, zobacz temat [Informacje o danych monitorowania Azure Cosmos DB](monitor-cosmos-db-reference.md) .
* Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
