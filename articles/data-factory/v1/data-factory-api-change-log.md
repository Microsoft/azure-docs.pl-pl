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
ms.openlocfilehash: b7936fcd1e4a629a813c4266920f6c34a15cf9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438946"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory — dziennik zmian interfejsu API platformy .NET
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. 

Ten artykuł zawiera informacje o zmianach w Azure Data Factory SDK w określonej wersji. Najnowszy pakiet NuGet dla Azure Data Factory można znaleźć [tutaj](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>4.11.0 wersja
Dodatki do funkcji:

* Dodano następujące typy połączonych usług:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* Dodano następujące typy zestawów danych:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* Dodano następujące typy źródeł kopiowania:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4.10.0 wersja
* Następujące opcjonalne właściwości zostały dodane do formatu TextFormat:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [Użycia](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* Dodano następujące typy połączonych usług:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* Dodano następujące typy zestawów danych:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* Dodano następujące typy źródeł kopiowania:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Dodaj właściwość [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) do AzureMLBatchExecutionActivity
  * Włącz przekazywanie wielu danych wejściowych usługi sieci Web do Azure Machine Learning eksperymentu

## <a name="version-491"></a>4.9.1 wersja
### <a name="bug-fix"></a>Poprawka błędu
* Zaniechanie uwierzytelniania opartego na WebApi dla [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>4.9.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodaj właściwości [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) i [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) do kopiowania. Aby uzyskać szczegółowe informacje na temat tej funkcji, zobacz [przygotowane kopie](data-factory-copy-activity-performance.md#staged-copy) .

### <a name="bug-fix"></a>Poprawka błędu
* Wprowadź Przeciążenie metody [ActivityWindowOperationExtensions. list](https://msdn.microsoft.com/library/mt767915.aspx) , która przyjmuje wystąpienie [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) .
* Oznacz [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) i [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) jako opcjonalne w CopySink.

## <a name="version-480"></a>4.8.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujące opcjonalne właściwości zostały dodane do typu działania kopiowania w celu umożliwienia dostrajania wydajności kopiowania:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>4.7.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano nowy typ StorageFormat typu [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) do kopiowania plików w formacie zoptymalizowanego wiersza kolumnowy (Orc).
* Dodaj właściwości [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) i PolyBaseSettings do SqlDWSink.
  * Umożliwia korzystanie z bazy danych bazowych do kopiowania dane do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse).

## <a name="version-461"></a>Wersja 4.6.1
### <a name="bug-fixes"></a>Poprawki błędów
* Rozwiązuje żądanie HTTP dotyczące wyświetlania okna działania.
  * Usuwa nazwę grupy zasobów i nazwę fabryki danych z ładunku żądania.

## <a name="version-460"></a>4.6.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujące właściwości zostały dodane do [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [Potokmode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Zestawy danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* Następujące właściwości zostały dodane do [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Dodano nowy typ [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) typu [formatu jsonformat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) w celu zdefiniowania zestawów danych, których dane są w formacie JSON.

## <a name="version-450"></a>4.5.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano [operacje listy dla okna działania](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Dodano metody pobierania okien działania z filtrami w oparciu o typy jednostek (czyli fabryki danych, zbiory, potoki i działania).
* Dodano następujące typy połączonych usług:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* Dodano następujące typy zestawów danych:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* Dodano następujące typy źródeł kopiowania:     
  * [Websource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>4.4.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujący typ połączonej usługi został dodany jako źródła danych i ujścia dla działań kopiowania:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Aby uzyskać informacje o pojęciach i przykłady, zobacz [połączoną usługę Azure Storage SAS](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) .

## <a name="version-430"></a>4.3.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Następujące typy połączonych usług Haven zostały dodane jako źródła danych dla działań kopiowania:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Zobacz [przenoszenie danych z systemu plików HDFS przy użyciu Data Factory](data-factory-hdfs-connector.md) , aby uzyskać informacje koncepcyjne i przykłady.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Zobacz [przenoszenie danych z magazynów danych ODBC przy użyciu Azure Data Factory](data-factory-odbc-connector.md) , aby uzyskać informacje koncepcyjne i przykłady.

## <a name="version-420"></a>4.2.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano następujący nowy typ działania: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). Aby uzyskać szczegółowe informacje o działaniu, zobacz [aktualizowanie modeli usługi Azure ml przy użyciu działania Aktualizuj zasób](data-factory-azure-ml-batch-execution-activity.md).
* Do [klasy połączenie](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)dodano nową właściwość [Właściwości updateresourceendpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) .
* Właściwości [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) i [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) zostały dodane do klasy [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) .
* Zezwalaj na konfigurowanie limitów czasu dla wywołań klientów do usługi Data Factory.

## <a name="version-410"></a>4.1.0 wersja
### <a name="feature-additions"></a>Dodatki funkcji
* Dodano następujące typy połączonych usług:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* Dodano następujące typy działań:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* Dodano następujące typy zestawów danych:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* Dodano następujące typy źródła i ujścia dla działania kopiowania:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>4.0.1 wersja
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
Zmieniono nazwy następujących klas. Nowe nazwy były oryginalnymi nazwami klas przed wydaniem 4.0.0.

| Nazwa w 4.0.0 | Nazwa w 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>Wersja 4.0.0
### <a name="breaking-changes"></a>Zmiany powodujące niezgodność
* Nazwy następujących klas/interfejsów zostały zmienione.

| Stara nazwa | Nowa nazwa |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tabela |[Zestaw danych](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

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
* Klasa [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) obsługuje dwie nowe właściwości, **SliceIdentifierColumnName** i **SqlWriterCleanupScript**, aby obsługiwać idempotentne kopiowania do usługi Azure Azure Synapse Analytics. Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz artykuł dotyczący [usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md) .
* Teraz obsługujemy uruchamianie procedury składowanej w odniesieniu do Azure SQL Database i źródeł analiz usługi Azure Synapse w ramach działania kopiowania. Klasy [sqlsource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) i [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) mają następujące właściwości: **SqlReaderStoredProcedureName** i **StoredProcedureParameters**. Aby uzyskać szczegółowe informacje o tych właściwościach, zobacz artykuły dotyczące [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) i [usługi Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) w witrynie Azure.com.  
