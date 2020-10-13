---
title: Monitorowanie danych Azure Cosmos DB przy użyciu ustawień diagnostycznych platformy Azure
description: Dowiedz się, jak używać ustawień diagnostycznych platformy Azure do monitorowania wydajności i dostępności danych przechowywanych w Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/05/2020
ms.author: sngun
ms.openlocfilehash: 9284fca6a96441ad5e6c23f9c6920ba184e03086
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801422"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Monitorowanie danych Azure Cosmos DB przy użyciu ustawień diagnostycznych na platformie Azure

Ustawienia diagnostyczne na platformie Azure służą do zbierania dzienników zasobów. Dzienniki zasobów platformy Azure są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. Te dzienniki są przechwytywane na żądanie i są również określane jako "dzienniki płaszczyzny danych". Niektóre przykłady operacji płaszczyzny danych obejmują Delete, INSERT i readFeed. Zawartość tych dzienników różni się w zależności od typu zasobu.

Metryki platformy i dzienniki aktywności są zbierane automatycznie. w związku z tym należy utworzyć ustawienie diagnostyczne w celu zbierania dzienników zasobów lub przesyłania ich dalej poza Azure Monitor. Ustawienie diagnostyczne dla kont usługi Azure Cosmos można włączyć, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do swojego konta usługi Azure Cosmos. Otwórz okienko **Ustawienia diagnostyczne** , a następnie wybierz opcję **Dodaj ustawienie diagnostyczne** .

1. W okienku **Ustawienia diagnostyczne** Wypełnij formularz następującymi szczegółami: 

    * **Nazwa**: Wprowadź nazwę dzienników do utworzenia.

    * Dzienniki mogą być przechowywane na **koncie magazynu**, **przesyłane strumieniowo do centrum zdarzeń** lub **wysyłane do log Analytics**

1. Podczas tworzenia ustawień diagnostycznych należy określić, którą kategorię dzienników należy zebrać. Kategorie dzienników obsługiwane przez Azure Cosmos DB są wymienione poniżej wraz z przykładowym dziennikiem zebranym przez nich:

 * **DataPlaneRequests**: Wybierz tę opcję, aby rejestrować żądania wewnętrznej bazy danych do wszystkich interfejsów API, takich jak SQL, Graph, MongoDB, Cassandra i interfejs API tabel, w Azure Cosmos DB. Właściwości klucza do uwagi: `Requestcharge` , `statusCode` , `clientIPaddress` , i `partitionID` .

    ```json
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontonu, aby obsłużyć żądania do interfejsu API Azure Cosmos DB dla MongoDB. Ten typ dziennika nie jest dostępny dla innych kont interfejsu API. Właściwości klucza do uwagi: `Requestcharge` , `opCode` . Po włączeniu MongoRequests w dziennikach diagnostycznych należy wyłączyć DataPlaneRequests. Zostanie wyświetlony jeden dziennik dla każdego żądania wykonanego w interfejsie API.

    ```json
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **CassandraRequests**: Wybierz tę opcję, aby rejestrować żądania inicjowane przez użytkownika z frontonu, aby obsłużyć żądania do interfejsu API Azure Cosmos DB dla Cassandra. Ten typ dziennika nie jest dostępny dla innych kont interfejsu API. Właściwości klucza, które należy zwrócić uwagę `operationName` , są, `requestCharge` , `piiCommandText` . Po włączeniu CassandraRequests w dziennikach diagnostycznych należy wyłączyć DataPlaneRequests. Zostanie wyświetlony jeden dziennik dla każdego żądania wykonanego w interfejsie API.

   ```json
   { "time": "2020-03-30T23:55:10.9579593Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "CassandraRequests", "operationName": "QuerySelect", "properties": {"activityId": "6b33771c-baec-408a-b305-3127c17465b6","opCode": "<empty>","errorCode": "-1","duration": "0.311900","requestCharge": "1.589237","databaseName": "system","collectionName": "local","retryCount": "<empty>","authorizationTokenType": "PrimaryMasterKey","address": "104.42.195.92","piiCommandText": "{"request":"SELECT key from system.local"}","userAgent": """"}}
   ```

* **QueryRuntimeStatistics**: Wybierz tę opcję, aby zarejestrować tekst zapytania, który został wykonany. Ten typ dziennika jest dostępny tylko dla kont interfejsu API SQL.

    ```json
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Wybierz tę opcję, aby rejestrować statystyki kluczy partycji. Jest to obecnie reprezentowane z rozmiarem magazynu (KB) kluczy partycji. Zapoznaj się z sekcją [Rozwiązywanie problemów przy użyciu zapytań diagnostycznych platformy Azure](#diagnostic-queries) w tym artykule. Na przykład zapytania, które używają "PartitionKeyStatistics". Dziennik jest emitowany w oparciu o trzy pierwsze klucze partycji, które zajmują większość magazynu danych. Ten dziennik zawiera dane takie jak identyfikator subskrypcji, nazwa regionu, nazwa bazy danych, nazwa kolekcji, klucz partycji i rozmiar magazynu w KILOBAJTach.

    ```json
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: ten dziennik raportuje zagregowane użycie jednostek ru na sekundę z kluczy partycji. Obecnie Azure Cosmos DB raporty kluczy partycji tylko dla kont interfejsu API SQL i dla operacji odczytu/zapisu i procedury składowanej. inne interfejsy API i typy operacji nie są obsługiwane. W przypadku innych interfejsów API kolumna klucza partycji w tabeli dzienników diagnostycznych będzie pusta. Ten dziennik zawiera dane takie jak identyfikator subskrypcji, nazwa regionu, nazwa bazy danych, nazwa kolekcji, klucz partycji, typ operacji oraz opłata za żądanie. Zapoznaj się z sekcją [Rozwiązywanie problemów przy użyciu zapytań diagnostycznych platformy Azure](#diagnostic-queries) w tym artykule. Na przykład zapytania, które używają "PartitionKeyRUConsumption". 

* **ControlPlaneRequests**: ten dziennik zawiera szczegółowe informacje o operacjach płaszczyzny kontroli, takich jak tworzenie konta, Dodawanie lub usuwanie regionu, aktualizowanie ustawień replikacji konta itd. Ten typ dziennika jest dostępny dla wszystkich typów interfejsów API, które zawierają SQL (rdzeń), MongoDB, Gremlin, Cassandra, interfejs API tabel.

* **Żądania**: zaznacz tę opcję, aby zbierać dane metryk z Azure Cosmos DB do miejsc docelowych w ustawieniu diagnostyki. Są to te same dane, które są zbierane automatycznie w ramach metryk platformy Azure. Zbieraj dane metryk z dziennikami zasobów, aby analizować jednocześnie oba rodzaje danych i wysyłać dane metryk poza Azure Monitor.

Aby uzyskać szczegółowe informacje na temat sposobu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell, zobacz [Tworzenie ustawień diagnostycznych w celu zbierania dzienników i metryk platformy w artykule platformy Azure](../azure-monitor/platform/diagnostic-settings.md) .


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a> Rozwiązywanie problemów z zapytaniami diagnostycznymi

1. Jak wykonać zapytanie o operacje trwające dłużej niż 3 ms:

   ```Kusto
   AzureDiagnostics 
   | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by clientIpAddress_s, TimeGenerated
   ```

1. Jak wykonać zapytanie dotyczące agenta użytkownika, na którym uruchomiono operacje:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | summarize count() by OperationName, userAgent_s
   ```

1. Jak wykonać zapytanie o długotrwałe operacje:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
   | project TimeGenerated , duration_s 
   | summarize count() by bin(TimeGenerated, 5s)
   | render timechart
   ```
    
1. Jak uzyskać statystykę klucza partycji, aby oszacować pochylenie w 3 najważniejszych partycjach dla konta bazy danych:

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
   | project SubscriptionId, regionName_s, databaseName_s, collectionName_s, partitionKey_s, sizeKb_d, ResourceId 
   ```

1. Jak uzyskać opłaty za żądania dla kosztownych zapytań?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Jak znaleźć operacje, które są wykonywane większością RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```

1. Jak uzyskać wszystkie zapytania, które zużywają więcej niż 100 RU/s przyłączone do danych z **DataPlaneRequests** i **QueryRunTimeStatistics**.

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Jak uzyskać opłaty za żądania i czas trwania zapytania?

   ```kusto
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "QueryRuntimeStatistics"
   | join (
   AzureDiagnostics
   | where TimeGenerated >= ago(24hr)
   | where Category == "DataPlaneRequests"
   ) on $left.activityId_g == $right.activityId_g
   | project databasename_s, collectionname_s, OperationName1 , querytext_s,requestCharge_s1, duration_s1, bin(TimeGenerated, 1min)
   ```


1. Jak uzyskać dystrybucję dla różnych operacji?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Jaka jest maksymalna przepływność używana przez partycję?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Jak uzyskać informacje o użyciu kluczy partycji RU/s na sekundę?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Jak uzyskać koszt żądania dla określonego klucza partycji

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Jak uzyskać najważniejsze klucze partycji z większością jednostek RU/s używanych w określonym przedziale czasu? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Jak pobrać dzienniki dla kluczy partycji, których rozmiar magazynu przekracza 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Jak uzyskać opóźnienia replikacji poziomie P99 lub P50 dla operacji, zażądać opłaty lub długości odpowiedzi?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2d)
   | summarize
   percentile(todouble(responseLength_s), 50), percentile(todouble(responseLength_s), 99), max(responseLength_s),
   percentile(todouble(requestCharge_s), 50), percentile(todouble(requestCharge_s), 99), max(requestCharge_s),
   percentile(todouble(duration_s), 50), percentile(todouble(duration_s), 99), max(duration_s),
   count()
   by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
 
1. Jak uzyskać dzienniki Controlplane?
 
   Pamiętaj, aby przełączyć się na flagę, zgodnie z opisem w artykule [wyłączanie dostępu do zapisu metadanych opartych na kluczach](audit-control-plane-logs.md#disable-key-based-metadata-write-access) i wykonywanie operacji przy użyciu Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub Azure Resource Manager.
 
   ```Kusto  
   AzureDiagnostics 
   | where Category =="ControlPlaneRequests"
   | summarize by OperationName 
   ```


## <a name="next-steps"></a>Następne kroki

* [Azure Monitor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB](use-metrics.md)
