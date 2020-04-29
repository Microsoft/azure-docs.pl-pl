---
title: Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2 | Microsoft Docs
description: Dowiedz się, które usługi platformy Azure integrują się z usługą Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: edfcb1d0bfc87c84620b13ed26ec681fef32f1e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878327"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2

Usług platformy Azure można używać do pozyskiwania danych, wykonywania analiz i tworzenia wizualizacji wizualnych. Ten artykuł zawiera listę obsługiwanych usług platformy Azure, odmknął swój poziom wsparcia i zawiera linki do artykułów, które ułatwiają korzystanie z tych usług w Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Obsługiwane usługi platformy Azure

Ta tabela zawiera listę usług platformy Azure, których można używać z usługą Azure Data Lake Storage Gen2. Elementy, które pojawiają się w tych tabelach, zmienią się w miarę upływu czasu, gdy obsługa będzie nadal rozszerzana.

> [!NOTE]
> Poziom pomocy technicznej dotyczy tylko sposobu, w jaki usługa jest obsługiwana w Data Lake Storage generacji 2.

|Usługa platformy Azure |Poziom pomocy technicznej |Pokrewne artykuły: |
|---------------|-------------------|---|
|Azure Data Factory|Ogólnie dostępne|[Załaduj dane do Azure Data Lake Storage Gen2 z Azure Data Factory](https://docs.microsoft.com/azure/data-factory/load-azure-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Ogólnie dostępne|[Używanie z usługą Azure Databricks](https://docs.azuredatabricks.net/data/data-sources/azure/azure-datalake-gen2.html) <br> [Szybki Start: analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md) <br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/databricks-extract-load-sql-data-warehouse) <br>[Samouczek: dostęp Data Lake Storage Gen2 danych za pomocą Azure Databricks przy użyciu platformy Spark](data-lake-storage-use-databricks-spark.md)|
|Centrum zdarzeń Azure|Ogólnie dostępne|[Przechwyć zdarzenia za pomocą usługi Azure Event Hubs na platformie Azure Blob Storage lub Azure Data Lake Storage](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)|
|Azure Event Grid|Ogólnie dostępne|[Samouczek: implementowanie wzorca przechwytywania danych w celu zaktualizowania tabeli różnicowej datakostki](data-lake-storage-events.md)|
|Azure Logic Apps|Ogólnie dostępne|[Przegląd — co to jest Azure Logic Apps?](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview)|
|Azure Machine Learning|Ogólnie dostępne|[Dostęp do danych w usługach Azure Storage](https://docs.microsoft.com/azure/machine-learning/how-to-access-data)|
|Usługa Azure Stream Analytics|Ogólnie dostępne|[Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-portal) <br> [Ruch wychodzący do Azure Data Lake Gen2](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-define-outputs#blob-storage-and-azure-data-lake-gen2)|
|Data Box|Ogólnie dostępne|[Używanie Azure Data Box do migrowania danych z lokalnego magazynu systemu plików HDFS do usługi Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Ogólnie dostępne|[Używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br>[Korzystanie z interfejsu wiersza polecenia systemu plików HDFS z Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Usługa IoT Hub |Ogólnie dostępne|[Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)|
|Power BI|Ogólnie dostępne|[Analizowanie danych w Data Lake Storage Gen2 przy użyciu Power BI](https://docs.microsoft.com/power-query/connectors/datalakestorage)|
|SQL Data Warehouse|Ogólnie dostępne|[Używanie z usługą Azure SQL Data Warehouse](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-sql-data-warehouse-polybase)|
|Usługi SQL Server Integration Services (SSIS)|Ogólnie dostępne|[Menedżer połączeń usługi Azure Storage](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-storage-connection-manager?view=sql-server-2017)|
|Azure Cognitive Search|Wersja zapoznawcza|[Indeksowanie i Wyszukiwanie Azure Data Lake Storage Gen2 dokumentów (wersja zapoznawcza)](https://docs.microsoft.com/azure/search/search-howto-index-azure-data-lake-storage)|
|Azure Data Explorer|Wersja zapoznawcza|[Wykonywanie zapytań dotyczących danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych](https://docs.microsoft.com/azure/data-explorer/data-lake-query-data)|
|Azure Content Delivery Network|Jeszcze nieobsługiwane|[Indeksowanie i Wyszukiwanie Azure Data Lake Storage Gen2 dokumentów (wersja zapoznawcza)](https://docs.microsoft.com/azure/cdn/cdn-overview)|


## <a name="see-also"></a>Zobacz także

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funkcje magazynu obiektów BLOB dostępne w Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Platformy Open source obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)