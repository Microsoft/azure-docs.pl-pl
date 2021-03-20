---
title: Scenariusze danych dotyczące Data Lake Storage Gen1 | Microsoft Docs
description: Poznaj różne scenariusze i narzędzia, za pomocą których dane mogą być pozyskiwane, przetwarzane, pobierane i wizualizowane w Data Lake Storage Gen1 (wcześniej znane jako Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702501"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>Używanie Azure Data Lake Storage Gen1 do wymagań dotyczących danych Big Data

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Istnieją cztery kluczowe etapy przetwarzania danych Big Data:

* Pozyskiwanie dużych ilości danych w magazynie danych w czasie rzeczywistym lub w partiach
* Przetwarzanie danych
* Pobieranie danych
* Wizualizowanie danych

W tym artykule przedstawiono te etapy, w odniesieniu do Azure Data Lake Storage Gen1 zrozumienie opcji i narzędzi dostępnych do osiągnięcia potrzeb związanych z danymi Big Data.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Pozyskiwanie danych w Data Lake Storage Gen1
W tej sekcji przedstawiono różne źródła danych oraz różne sposoby, w których dane mogą być pozyskiwane na koncie Data Lake Storage Gen1.

![Pozyskiwanie danych w Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/ingest-data.png "Pozyskiwanie danych w Data Lake Storage Gen1")

### <a name="ad-hoc-data"></a>Dane ad hoc
Reprezentuje to mniejsze zestawy danych, które są używane do prototypowania aplikacji danych Big Data. Istnieją różne sposoby pozyskiwania danych ad hoc w zależności od źródła danych.

| Źródło danych | Pozyskiwanie przy użyciu |
| --- | --- |
| Komputer lokalny |<ul> <li>[Azure Portal](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)</li> <li>[Korzystanie z narzędzi Data Lake Tools for Visual Studio](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[Narzędzie AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Pomocą distcp uruchomione w klastrze usługi HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Dane przesyłane strumieniowo
Reprezentuje to dane, które mogą być generowane przez różne źródła, takie jak aplikacje, urządzenia, czujniki itp. Te dane można pozyskać do Data Lake Storage Gen1 za pomocą różnych narzędzi. Te narzędzia zwykle przechwytuje i przetwarzają dane na zdarzeniach w czasie rzeczywistym, a następnie zapisują zdarzenia w partiach do Data Lake Storage Gen1 tak, aby mogły być dalej przetwarzane.

Poniżej znajdują się narzędzia, których można użyć:

* [Azure Stream Analytics](../stream-analytics/stream-analytics-define-outputs.md) — zdarzenia pozyskane do Event Hubs mogą być zapisywane w Azure Data Lake Storage Gen1 przy użyciu Azure Data Lake Storage Gen1 danych wyjściowych.
* [Burza usługi Azure HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md) — dane można zapisywać bezpośrednio do Data Lake Storage Gen1 z klastra burzy.
* [Klasy eventprocessorhost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) — można odbierać zdarzenia z Event Hubs a następnie zapisywać je do Data Lake Storage Gen1 przy użyciu [zestawu SDK Data Lake Storage Gen1 .NET](data-lake-store-get-started-net-sdk.md).

### <a name="relational-data"></a>Dane relacyjne
Możesz również uzyskać dane źródłowe z relacyjnych baz danych. Przez pewien czas relacyjne bazy danych zbierają ogromne ilości danych, które mogą zapewnić kluczowe informacje, jeśli są przetwarzane za pośrednictwem potoku danych Big Data. Aby przenieść takie dane do Data Lake Storage Gen1, można użyć następujących narzędzi.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Dane dziennika serwera sieci Web (przekazywanie przy użyciu aplikacji niestandardowych)
Ten typ zestawu danych jest specyficzny dla tego typu, ponieważ analiza danych dzienników serwera sieci Web jest typowym przypadkiem użycia dla aplikacji Big Data i wymaga przekazywania dużych ilości plików dziennika do Data Lake Storage Gen1. Możesz użyć dowolnego z poniższych narzędzi do pisania własnych skryptów lub aplikacji w celu przekazania takich danych.

* [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw SDK Azure Data Lake Storage Gen1 .NET](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

Do przekazywania danych dziennika serwera sieci Web, a także do przekazywania innych rodzajów danych (np. danych społecznościowych społecznych), dobrym rozwiązaniem jest napisanie własnych niestandardowych skryptów/aplikacji, ponieważ zapewnia ona elastyczność dołączania składnika do przekazywania danych w ramach większej ilości danych Big Data. W niektórych przypadkach ten kod może mieć postać skryptu lub prostego narzędzia wiersza polecenia. W innych przypadkach kod może służyć do integrowania przetwarzania danych Big Data z aplikacją biznesową lub rozwiązaniem.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Dane skojarzone z klastrami usługi Azure HDInsight
Większość typów klastrów usługi HDInsight (Hadoop, HBase, burza) obsługuje Data Lake Storage Gen1 jako repozytorium magazynu danych. Klastry HDInsight uzyskują dostęp do danych z obiektów BLOB usługi Azure Storage (WASB). Aby uzyskać lepszą wydajność, można skopiować dane z WASB do konta Data Lake Storage Gen1 skojarzonego z klastrem. Aby skopiować dane, można użyć następujących narzędzi.

* [Apache pomocą distcp](data-lake-store-copy-data-wasb-distcp.md)
* [Usługa AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>Dane przechowywane w lokalnych lub IaaSych klastrach Hadoop
Duże ilości danych mogą być przechowywane w istniejących klastrach usługi Hadoop lokalnie na maszynach przy użyciu systemu plików HDFS. Klastry Hadoop mogą znajdować się w lokalnym wdrożeniu lub znajdować się w klastrze IaaS na platformie Azure. Mogą istnieć wymagania, aby skopiować takie dane do Azure Data Lake Storage Gen1 na potrzeby jednego podejścia lub w sposób cykliczny. Istnieją różne opcje, których można użyć w celu osiągnięcia tego celu. Poniżej znajduje się lista alternatyw i skojarzonych z nimi zalet.

| Podejście | Szczegóły | Zalety | Zagadnienia do rozważenia |
| --- | --- | --- | --- |
| Użyj Azure Data Factory (ADF), aby skopiować dane bezpośrednio z klastrów usługi Hadoop do Azure Data Lake Storage Gen1 |[ADF jako źródło danych obsługuje system plików HDFS](../data-factory/connector-hdfs.md) |System ADF oferuje wbudowaną obsługę systemu plików HDFS oraz kompleksowe zarządzanie i monitorowanie w pierwszej klasie |Wymaga wdrożenia bramy Zarządzanie danymi w środowisku lokalnym lub w klastrze IaaS |
| Eksportuj dane z usługi Hadoop jako pliki. Następnie skopiuj pliki do Azure Data Lake Storage Gen1 przy użyciu odpowiedniego mechanizmu. |Można kopiować pliki do Azure Data Lake Storage Gen1 przy użyciu: <ul><li>[Azure PowerShell dla systemu operacyjnego Windows](data-lake-store-get-started-powershell.md)</li><li>[Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)</li><li>Aplikacja niestandardowa korzystająca z dowolnego zestawu Data Lake Storage Gen1 SDK</li></ul> |Szybkie rozpoczynanie pracy. Można dostosować operacje przekazywania |Wieloetapowy proces, który obejmuje wiele technologii. Zarządzanie i monitorowanie zwiększy się w miarę upływu czasu, uwzględniając dostosowany charakter narzędzi |
| Użyj pomocą distcp, aby skopiować dane z platformy Hadoop do usługi Azure Storage. Następnie skopiuj dane z usługi Azure Storage do Data Lake Storage Gen1 przy użyciu odpowiedniego mechanizmu. |Dane z usługi Azure Storage można kopiować do Data Lake Storage Gen1 przy użyciu: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[Narzędzie AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[Usługa Apache pomocą distcp uruchomiona w klastrach usługi HDInsight](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Można używać narzędzi open source. |Wieloetapowy proces, który obejmuje wiele technologii |

### <a name="really-large-datasets"></a>Bardzo duże zestawy danych
Do przekazywania zestawów danych, które mają zakres w kilku terabajtach, korzystanie z metod opisanych powyżej może być czasami powolne i kosztowne. W takich przypadkach można użyć poniższych opcji.

* **Korzystanie z usługi Azure ExpressRoute**. Usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure i infrastrukturą lokalną. Zapewnia to niezawodne rozwiązanie do przenoszenia dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure ExpressRoute](../expressroute/expressroute-introduction.md).
* **Przekazywanie danych w trybie offline**. Jeśli korzystanie z usługi Azure ExpressRoute nie jest możliwe z jakiegokolwiek powodu, można użyć [usługi Azure Import/Export](../import-export/storage-import-export-service.md) do dostarczania dysków twardych z danymi do centrum danych platformy Azure. Dane są najpierw przekazywane do obiektów BLOB usługi Azure Storage. Następnie można użyć narzędzia [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) lub [AdlCopy](data-lake-store-copy-data-azure-storage-blob.md) , aby skopiować dane z obiektów BLOB usługi Azure Storage do Data Lake Storage Gen1.

  > [!NOTE]
  > W przypadku korzystania z usługi Import/Export rozmiary plików na dyskach dostarczanych do centrum danych platformy Azure nie powinny przekraczać 195 GB.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Przetwarzanie danych przechowywanych w Data Lake Storage Gen1
Gdy dane są dostępne w Data Lake Storage Gen1 można przeprowadzić analizę tych danych przy użyciu obsługiwanych aplikacji danych Big Data. Obecnie można użyć usługi Azure HDInsight i Azure Data Lake Analytics, aby uruchamiać zadania analizy danych na danych przechowywanych w Data Lake Storage Gen1.

![Analizowanie danych w Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/analyze-data.png "Analizowanie danych w Data Lake Storage Gen1")

Możesz zapoznać się z poniższymi przykładami.

* [Tworzenie klastra usługi HDInsight z Data Lake Storage Gen1 jako magazyn](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Użyj Azure Data Lake Analytics z Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Pobieranie danych z Data Lake Storage Gen1
Może być również konieczne pobranie lub przeniesienie danych z Azure Data Lake Storage Gen1 w scenariuszach takich jak:

* Przenieś dane do innych repozytoriów do interfejsu z istniejącymi potokami przetwarzania danych. Na przykład możesz chcieć przenieść dane z Data Lake Storage Gen1 do Azure SQL Database lub SQL Server.
* Pobieranie danych na komputer lokalny na potrzeby przetwarzania w środowiskach IDE podczas budowania prototypów aplikacji.

![Dane wyjściowe z Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Dane wyjściowe z Data Lake Storage Gen1")

W takich przypadkach można użyć dowolnej z następujących opcji:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache pomocą distcp](data-lake-store-copy-data-wasb-distcp.md)

Możesz również użyć następujących metod, aby napisać własny skrypt/aplikację do pobierania danych z Data Lake Storage Gen1.

* [Interfejs wiersza polecenia platformy Azure](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Zestaw SDK Azure Data Lake Storage Gen1 .NET](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Wizualizowanie danych w Data Lake Storage Gen1
Za pomocą różnych usług można tworzyć wizualne reprezentacje danych przechowywanych w Data Lake Storage Gen1.

![Wizualizowanie danych w Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/visualize-data.png "Wizualizowanie danych w Data Lake Storage Gen1")

* Korzystając z Azure Data Factory, można [przenieść dane z Data Lake Storage Gen1 do usługi Azure Synapse Analytics](../data-factory/copy-activity-overview.md)
* Następnie możesz [zintegrować Power BI z usługą Azure Synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) , aby utworzyć wizualną reprezentację danych.