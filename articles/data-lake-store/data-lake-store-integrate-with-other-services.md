---
title: Integracja Data Lake Storage Gen1 z innymi usługami platformy Azure
description: Dowiedz się, jak zintegrować Azure Data Lake Storage Gen1 z innymi usługami platformy Azure.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 388c0db11c38f3dcdb9c4452b8d7283b2e811885
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108698"
---
# <a name="integrating-azure-data-lake-storage-gen1-with-other-azure-services"></a>Integrowanie Azure Data Lake Storage Gen1 z innymi usługami platformy Azure
Azure Data Lake Storage Gen1 można używać w połączeniu z innymi usługami platformy Azure, aby umożliwić szersze korzystanie z wielu scenariuszy. W poniższym artykule wymieniono usługi, z którymi można zintegrować Data Lake Storage Gen1.

## <a name="use-data-lake-storage-gen1-with-azure-hdinsight"></a>Korzystanie z Data Lake Storage Gen1 z usługą Azure HDInsight
Klaster [usługi Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) , który używa Data Lake Storage Gen1 jako magazynu zgodnego z systemem plików HDFS, można zainicjować. W tej wersji, w przypadku klastrów Hadoop i burzy w systemach Windows i Linux, można użyć Data Lake Storage Gen1 tylko jako dodatkowego magazynu. Takie klastry nadal korzystają z usługi Azure Storage (WASB) jako magazynu domyślnego. Jednak w przypadku klastrów HBase w systemach Windows i Linux można użyć Data Lake Storage Gen1 jako magazynu domyślnego lub dodatkowego magazynu.

Aby uzyskać instrukcje dotyczące udostępniania klastra usługi HDInsight za pomocą Data Lake Storage Gen1, zobacz:

* [Inicjowanie obsługi administracyjnej klastra usługi HDInsight za pomocą Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Inicjowanie obsługi administracyjnej klastra usługi HDInsight z użyciem Data Lake Storage Gen1 jako magazynu domyślnego przy użyciu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Inicjowanie obsługi administracyjnej klastra usługi HDInsight z Data Lake Storage Gen1 jako dodatkowego magazynu przy użyciu Azure PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)

## <a name="use-data-lake-storage-gen1-with-azure-data-lake-analytics"></a>Użyj Data Lake Storage Gen1 z Azure Data Lake Analytics
[Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-overview.md) umożliwia korzystanie z danych Big Data w skali chmury. Dynamicznie udostępnia zasoby i umożliwia analizę terabajtów, a nawet eksabajtowej danych, które mogą być przechowywane w wielu obsługiwanych źródłach danych, jeden z nich Data Lake Storage Gen1. Data Lake Analytics są specjalnie zoptymalizowane pod kątem pracy z Data Lake Storage Gen1 — zapewniają najwyższy poziom wydajności, przepływności i przetwarzanie równoległe dla obciążeń danych Big Data.

Aby uzyskać instrukcje dotyczące korzystania z Data Lake Analytics z Data Lake Storage Gen1, zobacz Wprowadzenie [do Data Lake Analytics przy użyciu Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md).

## <a name="use-data-lake-storage-gen1-with-azure-data-factory"></a>Użyj Data Lake Storage Gen1 z Azure Data Factory
Możesz użyć [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) do pozyskiwania danych z tabel platformy azure, Azure SQL Database, Azure SQL DataWarehouse, Azure Storage BLOB i lokalnych baz danych. Jest to pierwszy obywatel klasy w ekosystemie platformy Azure, Azure Data Factory może służyć do organizowania pozyskiwania danych z tych źródeł do Data Lake Storage Gen1.

Aby uzyskać instrukcje dotyczące korzystania z Azure Data Factory z Data Lake Storage Gen1, zobacz [przenoszenie danych do i z Data Lake Storage Gen1 przy użyciu Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="copy-data-from-azure-storage-blobs-into-data-lake-storage-gen1"></a>Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1
Azure Data Lake Storage Gen1 udostępnia narzędzie wiersza polecenia AdlCopy, które umożliwia kopiowanie danych z usługi Azure Blob Storage do konta Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="copy-data-between-azure-sql-database-and-data-lake-storage-gen1"></a>Kopiuj dane między Azure SQL Database i Data Lake Storage Gen1
Za pomocą oprogramowania Apache Sqoop można importować i eksportować dane między Azure SQL Database i Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [Kopiowanie danych między Data Lake Storage Gen1 i Azure SQL Database przy użyciu Sqoop](data-lake-store-data-transfer-sql-sqoop.md).

## <a name="use-data-lake-storage-gen1-with-stream-analytics"></a>Użyj Data Lake Storage Gen1 z Stream Analytics
Możesz użyć Data Lake Storage Gen1 jako jednego z danych wyjściowych do przechowywania przesyłanych strumieniowo przy użyciu Azure Stream Analytics. Aby uzyskać więcej informacji, zobacz [przesyłanie strumieniowe danych z Azure Storage BLOB do Data Lake Storage Gen1 przy użyciu Azure Stream Analytics](data-lake-store-stream-analytics.md).

## <a name="use-data-lake-storage-gen1-with-power-bi"></a>Użyj Data Lake Storage Gen1 z Power BI
Za pomocą Power BI można importować dane z konta Data Lake Storage Gen1, aby analizować i wizualizować dane. Aby uzyskać więcej informacji, zobacz [Analizowanie danych w Data Lake Storage Gen1 przy użyciu Power BI](data-lake-store-power-bi.md).

## <a name="use-data-lake-storage-gen1-with-data-catalog"></a>Użyj Data Lake Storage Gen1 z Data Catalog
Możesz zarejestrować dane z Data Lake Storage Gen1 w Azure Data Catalog, aby umożliwić ich odnajdywanie w całej organizacji. Aby uzyskać więcej informacji, zobacz [Rejestrowanie danych z Data Lake Storage Gen1 w Azure Data Catalog](data-lake-store-with-data-catalog.md).

## <a name="use-data-lake-storage-gen1-with-sql-server-integration-services-ssis"></a>Używanie Data Lake Storage Gen1 z SQL Server Integration Services (SSIS)
Za pomocą Menedżera połączeń Data Lake Storage Gen1 w programie SSIS można połączyć pakiet SSIS z Data Lake Storage Gen1. Aby uzyskać więcej informacji, zobacz [używanie Data Lake Storage Gen1 z usługami SSIS](/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager).

## <a name="use-data-lake-storage-gen1-with-azure-synapse-analytics"></a>Używanie Data Lake Storage Gen1 z usługą Azure Synapse Analytics
Możesz użyć wielobase, aby załadować dane z Data Lake Storage Gen1 do usługi Azure Synapse Analytics (dawniej SQL Data Warehouse). Aby uzyskać więcej informacji, zobacz [używanie Data Lake Storage Gen1 z usługą Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md).

## <a name="use-data-lake-storage-gen1-with-azure-event-hubs"></a>Korzystanie z Data Lake Storage Gen1 z platformą Azure Event Hubs
Za pomocą Azure Data Lake Storage Gen1 można archiwizować i przechwytywać dane odebrane przez usługę Azure Event Hubs. Aby uzyskać więcej informacji, zobacz [używanie Data Lake Storage Gen1 z usługą Azure Event Hubs](data-lake-store-archive-eventhub-capture.md).

## <a name="see-also"></a>Zobacz także
* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Wprowadzenie do Data Lake Storage Gen1 przy użyciu portalu](data-lake-store-get-started-portal.md)
* [Wprowadzenie do Data Lake Storage Gen1 przy użyciu programu PowerShell](data-lake-store-get-started-powershell.md)