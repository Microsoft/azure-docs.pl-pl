---
title: Wyodrębnianie, przekształcanie i ładowanie (ETL) w skali — Azure HDInsight
description: Dowiedz się, jak wyodrębnianie, przekształcanie i ładowanie jest używane w usłudze HDInsight z Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: e7a8a72d0669f39cc27c997d83af1e6272d045a6
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704156"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę

Wyodrębnianie, przekształcanie i ładowanie (ETL) to proces, za pomocą którego dane są uzyskiwane z różnych źródeł. Dane są zbierane w lokalizacji standardowej, oczyszczonej i przetworzonej. Ostatecznie dane są ładowane do magazynu danych, z którego mogą być wysyłane zapytania. Starsze procesy ETL importują dane, czyści je, a następnie przechowują w aparacie danych relacyjnych. Dzięki usłudze Azure HDInsight różne składniki środowiska Apache Hadoop obsługują ETL na dużą skalę.

Korzystanie z usługi HDInsight w procesie ETL jest sumowane według tego potoku:

![Omówienie ETL usługi HDInsight w skali](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

W poniższych sekcjach opisano wszystkie fazy ETL i powiązane z nimi składniki.

## <a name="orchestration"></a>Aranżacja

Aranżacja obejmuje wszystkie fazy potoku ETL. Zadania ETL w usłudze HDInsight często obejmują kilka różnych produktów współpracujących ze sobą. Na przykład:

- Możesz użyć Apache Hive, aby wyczyścić część danych, i Apache świni, aby oczyścić kolejną część.
- Azure Data Factory można użyć do załadowania danych do Azure SQL Database z Azure Data Lake Store.

Aranżacja jest wymagana do uruchomienia odpowiedniego zadania w odpowiednim czasie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Oozie działa w ramach klastra usługi HDInsight i jest zintegrowany z stosem Hadoop. Usługa Oozie obsługuje zadania usługi Hadoop dla Apache Hadoop MapReduce, trzody chlewnej, Hive i Sqoop. Za pomocą Oozie można zaplanować zadania specyficzne dla systemu, takie jak programy Java lub skrypty powłoki.

Aby uzyskać więcej informacji, zobacz [Używanie platformy Apache Oozie z usługą Apache Hadoop do definiowania i uruchamiania przepływu pracy w usłudze HDInsight](../hdinsight-use-oozie-linux-mac.md). Zobacz również [operacjonalizować Potok danych](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory zapewnia możliwości aranżacji w formie platformy jako usługi (PaaS). Azure Data Factory to usługa integracji danych w chmurze. Umożliwia tworzenie przepływów pracy opartych na danych na potrzeby organizowania i automatyzowania przenoszenia i przekształcania danych.

Użyj Azure Data Factory, aby:

1. Tworzenie i planowanie przepływów pracy opartych na danych. Te potoki pobierają dane z różnych magazynów danych.
1. Przetwarzaj i Przekształcaj dane przy użyciu usług obliczeniowych, takich jak HDInsight lub Hadoop. Dla tego kroku można także użyć platformy Spark, Azure Data Lake Analytics, Azure Batch lub Azure Machine Learning.
1. Publikowanie danych wyjściowych w magazynach danych, takich jak Azure Synapse Analytics, do użycia przez aplikacje analizy biznesowej.

Aby uzyskać więcej informacji na temat Azure Data Factory, zobacz [dokumentację](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Pozyskiwanie magazynu plików i magazynu wyników

Pliki danych źródłowych są zwykle ładowane do lokalizacji w usłudze Azure Storage lub Azure Data Lake Storage. Pliki są zwykle w formacie płaskim, takim jak wolumin CSV. Mogą jednak być w dowolnym formacie.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage ma konkretne cele adaptacyjne. Aby uzyskać więcej informacji [, zobacz cele skalowalności i wydajności dla usługi BLOB Storage](../../storage/blobs/scalability-targets.md) . W przypadku większości węzłów analitycznych usługa Azure Storage jest Najlepsza w przypadku pracy w wielu mniejszych plikach. O ile nie korzystasz z limitów konta, usługa Azure Storage gwarantuje tę samą wydajność bez względu na to, jak duże są pliki. Możesz przechowywać terabajty danych i nadal uzyskać spójną wydajność. Ta instrukcja ma zastosowanie w przypadku korzystania z podzestawów lub wszystkich danych.

Usługa Azure Storage ma kilka typów obiektów BLOB. *Dołącz obiekt BLOB* jest doskonałym rozwiązaniem do przechowywania dzienników sieci Web lub danych czujników.

Wiele obiektów BLOB może być dystrybuowanych na wielu serwerach w celu skalowania w poziomie dostępu do nich. Ale jeden obiekt BLOB jest obsługiwany tylko przez jeden serwer. Chociaż obiekty blob można logicznie grupować w kontenerach obiektów blob, nie ma żadnych implikacji partycjonowania z tego grupowania.

Usługa Azure Storage ma warstwę interfejsu API WebHDFS dla magazynu obiektów BLOB. Wszystkie usługi HDInsight mogą uzyskiwać dostęp do plików w usłudze Azure Blob Storage na potrzeby czyszczenia i przetwarzania danych. Jest to podobne do tego, jak te usługi używają rozproszony system plików Hadoop (HDFS).

Dane są zwykle pozyskiwane w usłudze Azure Storage za pomocą programu PowerShell, zestawu SDK usługi Azure Storage lub AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage to zarządzane repozytorium do skalowania dla danych analitycznych. Jest on zgodny z i używa modelu projektowania podobnego do systemu plików HDFS. Data Lake Storage oferuje nieograniczoną możliwość adaptacji do całkowitej pojemności i rozmiaru poszczególnych plików. Jest to dobry wybór podczas pracy z dużymi plikami, ponieważ mogą one być przechowywane w wielu węzłach. Partycjonowanie danych w Data Lake Storage jest wykonywane w tle. Uzyskujesz ogromne przepływność, aby uruchamiać zadania analityczne z tysiącami współbieżnych modułów uruchamiających, które efektywnie odczytują i zapisują setki terabajtów danych.

Dane są zwykle wprowadzane do Data Lake Storage przez Azure Data Factory. Można również użyć zestawów SDK Data Lake Storage, usługi AdlCopy, Apache pomocą distcp lub Apache Sqoop. Wybrana usługa zależy od lokalizacji danych. Jeśli znajduje się w istniejącym klastrze usługi Hadoop, możesz użyć platformy Apache pomocą distcp, usługi AdlCopy lub Azure Data Factory. W przypadku danych w usłudze Azure Blob Storage można użyć Azure Data Lake Storage .NET SDK, Azure PowerShell lub Azure Data Factory.

Data Lake Storage jest zoptymalizowany pod kątem pozyskiwania zdarzeń za pomocą usługi Azure Event Hubs lub Apache Storm.

### <a name="considerations-for-both-storage-options"></a>Zagadnienia dotyczące obu opcji magazynu

W przypadku przekazywania zestawów danych w zakresie terabajtów opóźnienie sieci może być istotnym problemem. Jest to szczególnie istotne, jeśli dane pochodzą z lokalizacji lokalnej. W takich przypadkach można użyć następujących opcji:

- **ExpressRoute Azure:** Tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Te połączenia zapewniają niezawodną opcję przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

- **Przekazywanie danych z dysków twardych:** [Usługa Azure Import/Export](../../import-export/storage-import-export-service.md) umożliwia wysyłanie dysków twardych z danymi do centrum danych platformy Azure. Dane są najpierw przekazywane do usługi Azure Blob Storage. Następnie można użyć Azure Data Factory lub narzędzia AdlCopy do kopiowania danych z usługi Azure Blob Storage do Data Lake Storage.

### <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Usługa Azure Synapse Analytics jest odpowiednim rozwiązaniem do przechowywania przygotowanych wyników. Usługa Azure HDInsight umożliwia wykonywanie tych usług na platformie Azure Synapse Analytics.

Usługa Azure Synapse Analytics to relacyjny magazyn baz danych zoptymalizowany pod kątem obciążeń analitycznych. Skaluje się na podstawie partycjonowanych tabel. Tabele mogą być partycjonowane w wielu węzłach. Węzły są wybierane podczas tworzenia. Mogą one być skalowane po fakcie, ale jest to aktywny proces, który może wymagać przeniesienia danych. Aby uzyskać więcej informacji, zobacz [Zarządzanie obliczeniami w usłudze Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase jest magazynem klucz/wartość dostępnym w usłudze Azure HDInsight. Jest to baza danych open source NoSQL, która jest oparta na usłudze Hadoop i modelowana po usłudze Google BigTable. HBase zapewnia dostęp losowy i silną spójność w przypadku dużych ilości danych bez struktury i z częściową strukturą.

Ponieważ HBase jest bazą danych bez schematu, nie trzeba definiować kolumn i typów danych przed ich użyciem. Dane są przechowywane w wierszach tabeli i są pogrupowane według rodziny kolumn.

Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. HBase opiera się na nadmiarowości danych, przetwarzaniu wsadowym i innych funkcjach, które są dostarczane przez aplikacje rozproszone w środowisku Hadoop.

HBase jest dobrym miejscem docelowym dla danych czujników i dzienników do przyszłej analizy.

HBase adaptacja zależy od liczby węzłów w klastrze usługi HDInsight.

### <a name="azure-sql-databases"></a>Bazy danych SQL Azure

Platforma Azure oferuje trzy relacyjne bazy danych PaaS:

* [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md) jest implementacją Microsoft SQL Server. Aby uzyskać więcej informacji na temat wydajności, zobacz [dostrajanie wydajności w Azure SQL Database](../../azure-sql/database/performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md)  to implementacja programu Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) to implementacja PostgreSQL.

Dodaj więcej procesorów i pamięci, aby skalować te produkty.  Możesz również użyć dysków Premium z produktami w celu uzyskania lepszej wydajności operacji we/wy.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services to analityczny aparat danych używany w ramach pomocy technicznej decyzyjnej i analizy biznesowej. Udostępnia dane analityczne dla raportów firmowych i aplikacji klienckich, takich jak Power BI. Dane analityczne działają również z programem Excel, raportami SQL Server Reporting Services i innymi narzędziami do wizualizacji danych.

Skalowanie modułów analizy przez zmianę warstw dla każdego pojedynczego modułu. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Wyodrębnij i Załaduj

Po utworzeniu danych na platformie Azure Możesz użyć wielu usług, aby wyodrębnić i załadować je do innych produktów. Usługa HDInsight obsługuje Sqoop i Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop to narzędzie służące do wydajnego transferu danych między strukturalnymi i częściowo strukturalnymi źródłami danych.

Sqoop używa MapReduce do importowania i eksportowania danych, w celu zapewnienia równoległej operacji i odporności na uszkodzenia.

### <a name="apache-flume"></a>Apache Flume

Apache Flume to dystrybuowana, niezawodna i dostępna usługa służąca do wydajnego zbierania, agregowania i przemieszczania dużych ilości danych dzienników. Elastyczna architektura jest oparta na przepływach danych przesyłanych strumieniowo. Flume jest niezawodna i odporna na uszkodzenia z mechanizmami niezawodności możliwość dostosowania. Ma wiele mechanizmów trybu failover i odzyskiwania. Flume korzysta z prostego rozszerzalnego modelu danych, który umożliwia korzystanie z aplikacji analitycznych w trybie online.

Nie można używać Apache Flume z usługą Azure HDInsight. Jednak lokalna instalacja usługi Hadoop może używać Flume do wysyłania danych do magazynu obiektów blob platformy Azure lub Azure Data Lake Storage. Aby uzyskać więcej informacji, zobacz [Korzystanie z platformy Apache Flume z usługą HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Przekształcanie

Gdy dane istnieją w wybranej lokalizacji, należy je wyczyścić, połączyć lub przygotować do określonego wzorca użycia. Usługi Hive, świnie i Spark SQL są dobrym wyborami dla tego rodzaju pracy. Są one obsługiwane w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

- [Używanie Apache Hive jako narzędzia ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
- [Przenoszenie danych z Azure SQL Database do tabeli Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)
