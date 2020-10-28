---
title: Data Factory — dziennik zmian interfejsu API platformy .NET
description: Opisuje istotne zmiany, Dodatki funkcji, poprawki błędów i tak dalej, w określonej wersji interfejsu API platformy .NET dla Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: 24e468007e0e5ea849ac4d7f945b0aaf6377e580
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92633811"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory — dziennik zmian interfejsu API platformy .NET
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Ten artykuł zawiera informacje o zmianach w Azure Data Factory SDK w określonej wersji. Najnowszy pakiet NuGet dla Azure Data Factory można znaleźć [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>4.11.0 wersja
Dodatki do funkcji:

* Dodano następujące typy połączonych usług:
  * [OnPremisesMongoDbLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesmongodblinkedservice)
  * [AmazonRedshiftLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.amazonredshiftlinkedservice)
  * [AwsAccessKeyLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.awsaccesskeylinkedservice)
* Dodano następujące typy zestawów danych:
  * [MongoDbCollectionDataset](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbcollectiondataset)
  * [AmazonS3Dataset](/dotnet/api/microsoft.azure.management.datafactories.models.amazons3dataset)
* Dodano następujące typy źródeł kopiowania:
  * [MongoDbSource](/dotnet/api/microsoft.azure.management.datafactories.models.mongodbsource)

## <a name="version-4100"></a>4.10.0 wersja
* Następujące opcjonalne właściwości zostały dodane do formatu TextFormat:
  * [SkipLineCount](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [Użycia](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
  * [TreatEmptyAsNull](/dotnet/api/microsoft.azure.management.datafactories.models.textformat)
* Dodano następujące typy połączonych usług:
  * [OnPremisesCassandraLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice)
  * [SalesforceLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.salesforcelinkedservice)
* Dodano następujące typy zestawów danych:
  * [OnPremisesCassandraTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset)
* Dodano następujące typy źródeł kopiowania:
  * [CassandraSource](/dotnet/api/microsoft.azure.management.datafactories.models.cassandrasource)
* Dodaj właściwość [WebServiceInputs](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity) do AzureMLBatchExecutionActivity
  * Włącz przekazywanie wielu danych wejściowych usługi sieci Web do Azure Machine Learning eksperymentu

## <a name="version-491"></a>4.9.1 wersja
### <a name="bug-fix"></a>Poprawka błędu
* Zaniechanie uwierzytelniania opartego na WebApi dla [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice).

## <a name="version-490"></a>4.9.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodaj właściwości [EnableStaging](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity) i [StagingSettings](/dotnet/api/microsoft.azure.management.datafactories.models.stagingsettings) do kopiowania. Aby uzyskać szczegółowe informacje na temat tej funkcji, zobacz [przygotowane kopie](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Poprawka błędu
* Wprowadź Przeciążenie metody [ActivityWindowOperationExtensions. list](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions) , która przyjmuje wystąpienie [ActivityWindowsByActivityListParameters](/dotnet/api/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters) .
* Oznacz [WriteBatchSize](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) i [WriteBatchTimeout](/dotnet/api/microsoft.azure.management.datafactories.models.copysink) jako opcjonalne w CopySink.

## <a name="version-480"></a>4.8.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujące opcjonalne właściwości zostały dodane do typu działania kopiowania w celu umożliwienia dostrajania wydajności kopiowania:
  * [ParallelCopies](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)
  * [CloudDataMovementUnits](/dotnet/api/microsoft.azure.management.datafactories.models.copyactivity)

## <a name="version-470"></a>4.7.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano nowy typ StorageFormat typu [OrcFormat](/dotnet/api/microsoft.azure.management.datafactories.models.orcformat) do kopiowania plików w formacie zoptymalizowanego wiersza kolumnowy (Orc).
* Dodaj właściwości [AllowPolyBase](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) i PolyBaseSettings do SqlDWSink.
  * Umożliwia korzystanie z bazy danych bazowych do kopiowania dane do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse).

## <a name="version-461"></a>Wersja 4.6.1
### <a name="bug-fixes"></a>Poprawki błędów
* Rozwiązuje żądanie HTTP dotyczące wyświetlania okna działania.
  * Usuwa nazwę grupy zasobów i nazwę fabryki danych z ładunku żądania.

## <a name="version-460"></a>4.6.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujące właściwości zostały dodane do [PipelineProperties](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties):
  * [Potokmode](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [ExpirationTime](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
  * [Zestawy danych](/dotnet/api/microsoft.azure.management.datafactories.models.pipelineproperties)
* Następujące właściwości zostały dodane do [PipelineRuntimeInfo](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo):
  * [PipelineState](/dotnet/api/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo)
* Dodano nowy typ [StorageFormat](/dotnet/api/microsoft.azure.management.datafactories.models.storageformat) typu [formatu jsonformat](/dotnet/api/microsoft.azure.management.datafactories.models.jsonformat) w celu zdefiniowania zestawów danych, których dane są w formacie JSON.

## <a name="version-450"></a>4.5.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano [operacje listy dla okna działania](/dotnet/api/microsoft.azure.management.datafactories.activitywindowoperationsextensions).
  * Dodano metody pobierania okien działania z filtrami w oparciu o typy jednostek (czyli fabryki danych, zbiory, potoki i działania).
* Dodano następujące typy połączonych usług:
  * [ODataLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.odatalinkedservice), [WebLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.weblinkedservice)
* Dodano następujące typy zestawów danych:
  * [ODataResourceDataset](/dotnet/api/microsoft.azure.management.datafactories.models.odataresourcedataset), [WebTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.webtabledataset)
* Dodano następujące typy źródeł kopiowania:     
  * [Websource](/dotnet/api/microsoft.azure.management.datafactories.models.websource)

## <a name="version-440"></a>4.4.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujący typ połączonej usługi został dodany jako źródła danych i ujścia dla działań kopiowania:
  * [AzureStorageSasLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice). Aby uzyskać informacje o pojęciach i przykłady, zobacz [połączoną usługę Azure Storage SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>4.3.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujące typy połączonych usług Haven zostały dodane jako źródła danych dla działań kopiowania:
  * [HdfsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.hdfslinkedservice). Zobacz [przenoszenie danych z systemu plików HDFS przy użyciu Data Factory](data-factory-hdfs-connector.md) , aby uzyskać informacje koncepcyjne i przykłady.
  * [OnPremisesOdbcLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice). Zobacz [przenoszenie danych z magazynów danych ODBC przy użyciu Azure Data Factory](data-factory-odbc-connector.md) , aby uzyskać informacje koncepcyjne i przykłady.

## <a name="version-420"></a>4.2.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano następujący nowy typ działania: [AzureMLUpdateResourceActivity](/dotnet/api/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity). Aby uzyskać szczegółowe informacje o działaniu, zobacz [aktualizowanie modeli usługi Azure ml przy użyciu działania Aktualizuj zasób](data-factory-azure-ml-batch-execution-activity.md).
* Do [klasy połączenie](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice)dodano nową właściwość [Właściwości updateresourceendpoint](/dotnet/api/microsoft.azure.management.datafactories.models.azuremllinkedservice) .
* Właściwości [LongRunningOperationInitialTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) i [LongRunningOperationRetryTimeout](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) zostały dodane do klasy [DataFactoryManagementClient](/dotnet/api/microsoft.azure.management.datafactories.datafactorymanagementclient) .
* Zezwalaj na konfigurowanie limitów czasu dla wywołań klientów do usługi Data Factory.

## <a name="version-410"></a>4.1.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano następujące typy połączonych usług:
  * [AzureDataLakeStoreLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
  * [AzureDataLakeAnalyticsLinkedService](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* Dodano następujące typy działań:
  * [DataLakeAnalyticsUSQLActivity](/dotnet/api/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity)
* Dodano następujące typy zestawów danych:
  * [AzureDataLakeStoreDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoredataset)
* Dodano następujące typy źródła i ujścia dla działania kopiowania:
  * [AzureDataLakeStoreSource](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresource)
  * [AzureDataLakeStoreSink](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestoresink)

## <a name="version-401"></a>4.0.1 wersja
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
Zmieniono nazwy następujących klas. Nowe nazwy były oryginalnymi nazwami klas przed wydaniem 4.0.0.

| Nazwa w 4.0.0 | Nazwa w 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset) |
| AzureSqlDataset |[AzureSqlTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuresqltabledataset) |
| AzureDataset |[AzureTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.azuretabledataset) |
| OracleDataset |[OracleTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.oracletabledataset) |
| RelationalDataset |[RelationalTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.relationaltabledataset) |
| SqlServerDataset |[SqlServerTableDataset](/dotnet/api/microsoft.azure.management.datafactories.models.sqlservertabledataset) |

## <a name="version-400"></a>Wersja 4.0.0
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Nazwy następujących klas/interfejsów zostały zmienione.

| Stara nazwa | Nowa nazwa |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](/dotnet/api/microsoft.azure.management.datafactories.idatasetoperations) |
| Tabela |[Zestawu](/dotnet/api/microsoft.azure.management.datafactories.models.dataset) |
| TableProperties |[DatasetProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasetproperties) |
| TableTypeProprerties |[DatasetTypeProperties](/dotnet/api/microsoft.azure.management.datafactories.models.datasettypeproperties) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse) |
| TableGetResponse |[DatasetGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetgetresponse) |
| TableListResponse |[DatasetListResponse](/dotnet/api/microsoft.azure.management.datafactories.models.datasetlistresponse) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](/dotnet/api/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters) |

* Metody **list** zwracają teraz wyniki stronicowania. Jeśli odpowiedź zawiera niepustą Właściwość **Nextlink** , aplikacja kliencka musi kontynuować pobieranie następnej strony do momentu zwrócenia wszystkich stron.  Oto przykład:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* Interfejs API potoku **list** zwraca tylko podsumowanie potoku, a nie pełne szczegóły. Na przykład działania w podsumowaniu potoku zawierają tylko nazwę i typ.

### <a name="feature-additions"></a>Dodatki funkcji
* Klasa [SqlDWSink](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsink) obsługuje dwie nowe właściwości, **SliceIdentifierColumnName** i **SqlWriterCleanupScript** , aby obsługiwać idempotentne kopiowania do usługi Azure Azure Synapse Analytics. Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz artykuł dotyczący [usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) .
* Teraz obsługujemy uruchamianie procedury składowanej w odniesieniu do Azure SQL Database i źródeł analiz usługi Azure Synapse w ramach działania kopiowania. Klasy [sqlsource](/dotnet/api/microsoft.azure.management.datafactories.models.sqlsource) i [SqlDWSource](/dotnet/api/microsoft.azure.management.datafactories.models.sqldwsource) mają następujące właściwości: **SqlReaderStoredProcedureName** i **StoredProcedureParameters** . Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz artykuły dotyczące [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) i [usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) w witrynie Azure.com.