---
title: Scenariusze danych dotyczące Azure Data Lake Storage Gen2 | Microsoft Docs
description: Poznaj różne scenariusze i narzędzia, za pomocą których dane mogą być pozyskiwane, przetwarzane, pobierane i wizualizowane w Data Lake Storage Gen2 (wcześniej znane jako Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: eea7fb073cdf99ee1f4257f6824375a6502a4fad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913627"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>Używanie Azure Data Lake Storage Gen2 do wymagań dotyczących danych Big Data

Istnieją cztery kluczowe etapy przetwarzania danych Big Data:

> [!div class="checklist"]
> * Pozyskiwanie dużych ilości danych w magazynie danych w czasie rzeczywistym lub w partiach
> * Przetwarzanie danych
> * Pobieranie danych
> * Wizualizowanie danych

W tym artykule przedstawiono opcje i narzędzia dla każdej fazy przetwarzania.

Aby uzyskać pełną listę usług platformy Azure, których można używać z usługą Azure Data Lake Storage Gen2, zobacz [integrowanie Azure Data Lake Storage z usługami platformy Azure](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Pozyskiwanie danych w Data Lake Storage Gen2

W tej sekcji przedstawiono różne źródła danych oraz różne sposoby, w których dane mogą być pozyskiwane na koncie Data Lake Storage Gen2.

![Pozyskiwanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/ingest-data.png "Pozyskiwanie danych w Data Lake Storage Gen2")

### <a name="ad-hoc-data"></a>Dane ad hoc

Reprezentuje to mniejsze zestawy danych, które są używane do prototypowania aplikacji danych Big Data. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych. 

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych ad hoc.

| Źródło danych | Pozyskiwanie przy użyciu |
| --- | --- |
| Komputer lokalny |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)<br><br>[Eksplorator usługi Storage](https://azure.microsoft.com/features/storage-explorer/)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)<br><br>[Pomocą distcp uruchomione w klastrze usługi HDInsight](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Dane przesyłane strumieniowo

Reprezentuje to dane, które mogą być generowane przez różne źródła, takie jak aplikacje, urządzenia, czujniki itp. Te dane można pozyskać do Data Lake Storage Gen2 za pomocą różnych narzędzi. Te narzędzia zwykle przechwytuje i przetwarzają dane na zdarzeniach w czasie rzeczywistym, a następnie zapisują zdarzenia w partiach do Data Lake Storage Gen2 tak, aby mogły być dalej przetwarzane.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych przesyłanych strumieniowo.

|Narzędzie | Wskazówki |
|---|--|
|Usługa Azure Stream Analytics|[Szybki start: tworzenie zadania usługi Stream Analytics przy użyciu witryny Azure Portal](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Ruch wychodzący do Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight — burza | [Zapisywanie do Apache Hadoop HDFS z Apache Storm w usłudze HDInsight](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Dane relacyjne

Możesz również uzyskać dane źródłowe z relacyjnych baz danych. Przez pewien czas relacyjne bazy danych zbierają ogromne ilości danych, które mogą zapewnić kluczowe informacje, jeśli są przetwarzane za pośrednictwem potoku danych Big Data. Aby przenieść takie dane do Data Lake Storage Gen2, można użyć następujących narzędzi.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych relacyjnych.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dziennika serwera sieci Web (przekazywanie przy użyciu aplikacji niestandardowych)

Ten typ zestawu danych jest specyficzny dla tego typu, ponieważ analiza danych dzienników serwera sieci Web jest typowym przypadkiem użycia dla aplikacji Big Data i wymaga przekazywania dużych ilości plików dziennika do Data Lake Storage Gen2. Możesz użyć dowolnego z poniższych narzędzi do pisania własnych skryptów lub aplikacji w celu przekazania takich danych.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych dziennika serwera sieci Web.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](../../data-factory/copy-activity-overview.md)  |
|Interfejs wiersza polecenia platformy Azure|[Interfejs wiersza polecenia platformy Azure](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

Do przekazywania danych dziennika serwera sieci Web, a także do przekazywania innych rodzajów danych (np. danych społecznościowych społecznych), dobrym rozwiązaniem jest napisanie własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia ona elastyczność dołączania składnika do przekazywania danych w ramach większej ilości danych Big Data. W niektórych przypadkach ten kod może mieć postać skryptu lub prostego narzędzia wiersza polecenia. W innych przypadkach kod może służyć do integrowania przetwarzania danych Big Data z aplikacją biznesową lub rozwiązaniem.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight

Większość typów klastrów usługi HDInsight (Hadoop, HBase, burza) obsługuje Data Lake Storage Gen2 jako repozytorium magazynu danych. Klastry HDInsight uzyskują dostęp do danych z obiektów BLOB usługi Azure Storage (WASB). Aby uzyskać lepszą wydajność, można skopiować dane z WASB do konta Data Lake Storage Gen2 skojarzonego z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

Poniżej znajduje się lista narzędzi, których można użyć do pozyskiwania danych skojarzonych z klastrami usługi HDInsight.

|Narzędzie | Wskazówki |
|---|--|
|Apache pomocą distcp | [Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Narzędzie AzCopy | [Transferowanie danych za pomocą AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Kopiowanie danych do lub z Azure Data Lake Storage Gen2 przy użyciu Azure Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaSych klastrach Hadoop

Duże ilości danych mogą być przechowywane w istniejących klastrach usługi Hadoop lokalnie na maszynach przy użyciu systemu plików HDFS. Klastry Hadoop mogą znajdować się w lokalnym wdrożeniu lub znajdować się w klastrze IaaS na platformie Azure. Mogą istnieć wymagania, aby skopiować takie dane do Azure Data Lake Storage Gen2 na potrzeby jednego podejścia lub w sposób cykliczny. Istnieją różne opcje, których można użyć w celu osiągnięcia tego celu. Poniżej znajduje się lista alternatyw i skojarzonych z nimi zalet.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Użyj Azure Data Factory (ADF), aby skopiować dane bezpośrednio z klastrów usługi Hadoop do Azure Data Lake Storage Gen2 |[ADF jako źródło danych obsługuje system plików HDFS](../../data-factory/connector-hdfs.md) |System ADF oferuje wbudowaną obsługę systemu plików HDFS oraz kompleksowe zarządzanie i monitorowanie w pierwszej klasie |Wymaga wdrożenia bramy Zarządzanie danymi w środowisku lokalnym lub w klastrze IaaS |
| Użyj pomocą distcp, aby skopiować dane z platformy Hadoop do usługi Azure Storage. Następnie skopiuj dane z usługi Azure Storage do Data Lake Storage Gen2 przy użyciu odpowiedniego mechanizmu. |Dane z usługi Azure Storage można kopiować do Data Lake Storage Gen2 przy użyciu: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AzCopy](../common/storage-use-azcopy-v10.md)</li><li>[Usługa Apache pomocą distcp uruchomiona w klastrach usługi HDInsight](data-lake-storage-use-distcp.md)</li></ul> |Można używać narzędzi open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Bardzo duże zestawy danych

Do przekazywania zestawów danych, które mają zakres w kilku terabajtach, korzystanie z metod opisanych powyżej może być czasami powolne i kosztowne. W takich przypadkach można użyć usługi Azure ExpressRoute.  

Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną. Zapewnia to niezawodne rozwiązanie do przenoszenia dużych ilości danych. Aby dowiedzieć się więcej, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

## <a name="process-the-data"></a>Przetwarzanie danych

Gdy dane są dostępne w Data Lake Storage Gen2 można przeprowadzić analizę tych danych przy użyciu obsługiwanych aplikacji danych Big Data. 

![Analizowanie danych w Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/analyze-data.png "Analizowanie danych w Data Lake Storage Gen2")

Poniżej znajduje się lista narzędzi, których można użyć do uruchamiania zadań analizy danych na danych przechowywanych w Data Lake Storage Gen2.

|Narzędzie | Wskazówki |
|---|--|
|Azure HDInsight | [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)<br><br>[Szybki Start: analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Samouczek: Wyodrębnianie, przekształcanie i ładowanie danych przy użyciu usługi Azure Databricks](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Wizualizacja danych

Użyj łącznika Power BI, aby utworzyć wizualną reprezentację danych przechowywanych w Data Lake Storage Gen2. Zobacz [Analizowanie danych w Azure Data Lake Storage Gen2 przy użyciu Power BI](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Pobieranie danych

Może być również konieczne pobranie lub przeniesienie danych z Azure Data Lake Storage Gen2 w scenariuszach takich jak:

* Przenieś dane do innych repozytoriów do interfejsu z istniejącymi potokami przetwarzania danych. Na przykład możesz chcieć przenieść dane z Data Lake Storage Gen2 do Azure SQL Database lub wystąpienia SQL Server.

* Pobieranie danych na komputer lokalny na potrzeby przetwarzania w środowiskach IDE podczas budowania prototypów aplikacji.

![Dane wyjściowe z Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Dane wyjściowe z Data Lake Storage Gen2")

Poniżej znajduje się lista narzędzi, których można użyć do pobierania danych z Data Lake Storage Gen2.

|Narzędzie | Wskazówki |
|---|--|
|Azure Data Factory | [Działanie kopiowania w usłudze Azure Data Factory](../../data-factory/copy-activity-overview.md) |
|Apache pomocą distcp | [Użyj pomocą distcp do kopiowania danych między obiektami BLOB usługi Azure Storage a Azure Data Lake Storage Gen2](./data-lake-storage-use-distcp.md) |
|Eksplorator usługi Azure Storage|[Zarządzanie katalogami, plikami i listami ACL w usłudze Azure Data Lake Storage Gen2 za pomocą Eksploratora usługi Azure Storage](data-lake-storage-explorer.md)|
|Narzędzie AzCopy|[Transferowanie danych za pomocą AzCopy i magazynu obiektów BLOB](../common/storage-use-azcopy-blobs.md)|