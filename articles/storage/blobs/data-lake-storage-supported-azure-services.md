---
title: Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2 | Microsoft Docs
description: Dowiedz się, które usługi platformy Azure integrują się z usługą Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 36e1a8a288e1f9b2a8d65ab966b607b594d66f4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653605"
---
# <a name="azure-services-that-support-azure-data-lake-storage-gen2"></a>Usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2

Usług platformy Azure można używać do pozyskiwania danych, wykonywania analiz i tworzenia wizualizacji wizualnych. Ten artykuł zawiera listę obsługiwanych usług platformy Azure, odmknął swój poziom wsparcia i zawiera linki do artykułów, które ułatwiają korzystanie z tych usług w Azure Data Lake Storage Gen2.

## <a name="supported-azure-services"></a>Obsługiwane usługi platformy Azure

Ta tabela zawiera listę usług platformy Azure, których można używać z usługą Azure Data Lake Storage Gen2. Elementy, które pojawiają się w tych tabelach, zmienią się w miarę upływu czasu, gdy obsługa będzie nadal rozszerzana.

> [!NOTE]
> Poziom pomocy technicznej dotyczy tylko sposobu, w jaki usługa jest obsługiwana w Data Lake Storage generacji 2.

|Usługa platformy Azure |Poziom pomocy technicznej |Azure AD |Klucz wspólny| Pokrewne artykuły: |
|---------------|-------------------|---|---|---|
|Azure Data Factory|Ogólnie dostępne|Tak|Tak|[Załaduj dane do Azure Data Lake Storage Gen2 z Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure Databricks|Ogólnie dostępne|Tak|Tak|[Używanie z usługą Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) <br> [Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse) <br>[Samouczek: dostęp Data Lake Storage Gen2 danych za pomocą Azure Databricks przy użyciu platformy Spark](data-lake-storage-use-databricks-spark.md)|
|Centrum zdarzeń Azure|Ogólnie dostępne|Nie|Tak|[Przechwyć zdarzenia za pomocą usługi Azure Event Hubs na platformie Azure Blob Storage lub Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md)|
|Azure Event Grid|Ogólnie dostępne|Tak|Tak|[Samouczek: implementowanie wzorca przechwytywania danych w celu zaktualizowania tabeli różnicowej datakostki](data-lake-storage-events.md)|
|Azure Logic Apps|Ogólnie dostępne|Nie|Tak|[Przegląd — co to jest Azure Logic Apps?](../../logic-apps/logic-apps-overview.md)|
|Azure Machine Learning|Ogólnie dostępne|Tak|Tak|[Dostęp do danych w usługach Azure Storage](../../machine-learning/how-to-access-data.md)|
|Usługa Azure Stream Analytics|Ogólnie dostępne|Tak|Tak|[Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Ruch wychodzący do Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Data Box|Ogólnie dostępne|Nie|Tak|[Używanie Azure Data Box do migrowania danych z lokalnego magazynu systemu plików HDFS do usługi Azure Storage](data-lake-storage-migrate-on-premises-hdfs-cluster.md)|
|HDInsight |Ogólnie dostępne|Tak|Tak|[Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md)<br>[Korzystanie z interfejsu wiersza polecenia systemu plików HDFS z Data Lake Storage Gen2](data-lake-storage-use-hdfs-data-lake-storage.md) <br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)|
|Usługa IoT Hub |Ogólnie dostępne|Tak|Tak|[Używanie routingu komunikatów IoT Hub do wysyłania komunikatów z urządzenia do chmury do różnych punktów końcowych](../../iot-hub/iot-hub-devguide-messages-d2c.md)|
|Power BI|Ogólnie dostępne|Tak|Tak|[Analizowanie danych w Data Lake Storage Gen2 przy użyciu Power BI](/power-query/connectors/datalakestorage)|
|Azure Synapse Analytics (dawniej Azure SQL Data Warehouse)|Ogólnie dostępne|Tak|Tak|[Analizowanie danych na koncie magazynu](../../synapse-analytics/get-started-analyze-storage.md)|
|SQL Server Integration Services (SSIS)|Ogólnie dostępne|Tak|Tak|[Menedżer połączeń usługi Azure Storage](/sql/integration-services/connection-manager/azure-storage-connection-manager)|
|Azure Data Explorer|Ogólnie dostępne|Tak|Tak|[Wykonywanie zapytań dotyczących danych w Azure Data Lake przy użyciu usługi Azure Eksplorator danych](/azure/data-explorer/data-lake-query-data)|
|Azure Cognitive Search|Wersja zapoznawcza|Tak|Tak|[Indeksowanie i Wyszukiwanie Azure Data Lake Storage Gen2 dokumentów (wersja zapoznawcza)](../../search/search-howto-index-azure-data-lake-storage.md)|
|Azure Content Delivery Network|Jeszcze nieobsługiwane|Nie dotyczy|Nie dotyczy|[Indeksowanie i Wyszukiwanie Azure Data Lake Storage Gen2 dokumentów (wersja zapoznawcza)](../../cdn/cdn-overview.md)|
|Azure SQL Database|Jeszcze nieobsługiwane|Nie dotyczy|Nie dotyczy|[Co to jest usługa Azure SQL Database?](../../azure-sql/database/sql-database-paas-overview.md)|

## <a name="see-also"></a>Zobacz też

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Funkcje magazynu obiektów blob dostępne w usłudze Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Platformy Open source obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)