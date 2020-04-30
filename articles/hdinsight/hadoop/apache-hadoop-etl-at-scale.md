---
title: Wyodrębnianie, przekształcanie i ładowanie (ETL) w skali — Azure HDInsight
description: Dowiedz się, jak wyodrębnianie, przekształcanie i ładowanie jest używane w usłudze HDInsight z Apache Hadoop.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: ee989ccbb2e441256bec71781c538c7761fc7b88
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232244"
---
# <a name="extract-transform-and-load-etl-at-scale"></a>Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę

Wyodrębnianie, przekształcanie i ładowanie (ETL) to proces, za pomocą którego dane są uzyskiwane z różnych źródeł. Zbierane w lokalizacji standardowej, oczyszczone i przetworzone. Ostatecznie załadowane do magazynu danych, z którego mogą być wysyłane zapytania. Starsze procesy ETL importują dane, czyści je, a następnie przechowują w aparacie danych relacyjnych. W usłudze HDInsight wiele różnych składników środowiska Apache Hadoop obsługuje ETL na dużą skalę.

Użycie usługi HDInsight w procesie ETL może być podsumowane przez ten potok:

![Omówienie ETL usługi HDInsight w skali](./media/apache-hadoop-etl-at-scale/hdinsight-etl-at-scale-overview.png)

W poniższych sekcjach opisano wszystkie fazy ETL i powiązane z nimi składniki.

## <a name="orchestration"></a>Aranżacja

Aranżacja obejmuje wszystkie fazy potoku ETL. Zadania ETL w usłudze HDInsight często obejmują kilka różnych produktów współpracujących ze sobą.  Możesz użyć programu Hive do oczyszczenia części danych, natomiast świnia czyści inną część.  Azure Data Factory można użyć do załadowania danych do Azure SQL Database z Azure Data Lake Store.

Aranżacja jest wymagana do uruchomienia odpowiedniego zadania w odpowiednim czasie.

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Oozie działa w ramach klastra usługi HDInsight i jest zintegrowany z stosem Hadoop. Usługa Oozie obsługuje zadania usługi Hadoop dla Apache Hadoop MapReduce, Apache chlewnej, Apache Hive i Apache Sqoop. Oozie może również służyć do planowania zadań specyficznych dla systemu, takich jak programy Java lub skrypty powłoki.

Aby uzyskać więcej informacji, zobacz [Używanie platformy Apache Oozie z usługą Apache Hadoop do definiowania i uruchamiania przepływu pracy w usłudze HDInsight](../hdinsight-use-oozie-linux-mac.md). Zobacz również [operacjonalizować Potok danych](../hdinsight-operationalize-data-pipeline.md).

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory zapewnia możliwości aranżacji w formie platformy jako usługi. Jest to oparta na chmurze usługa integracji danych, która umożliwia tworzenie przepływów pracy opartych na danych w chmurze. Przepływy pracy służące do organizowania i automatyzowania przenoszenia i przekształcania danych.

Za pomocą Azure Data Factory można:

1. Twórz i planuj przepływy pracy oparte na danych (nazywane potokami), które pozyskiwanie danych z różnych magazynów danych.
2. Przetwarzaj i Przekształcaj dane przy użyciu usług obliczeniowych, takich jak Azure HDInsight Hadoop. Lub Spark, Azure Data Lake Analytics, Azure Batch i Azure Machine Learning.
3. Publikować dane wyjściowe w magazynach danych, na przykład Azure SQL Data Warehouse, w celu użycia przez aplikacje analizy biznesowej.

Aby uzyskać więcej informacji na temat Azure Data Factory, zobacz [dokumentację](../../data-factory/introduction.md).

## <a name="ingest-file-storage-and-result-storage"></a>Pozyskiwanie magazynu plików i magazynu wyników

Pliki danych źródłowych są zwykle ładowane do lokalizacji w usłudze Azure Storage lub Azure Data Lake Storage. Pliki mogą być w dowolnym formacie, ale zazwyczaj są plikami prostymi, takimi jak CSV.

### <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage ma konkretne cele adaptacyjne. Zobacz [elementy docelowe skalowalności i wydajności dla usługi BLOB Storage](../../storage/blobs/scalability-targets.md). W przypadku większości węzłów analitycznych usługa Azure Storage jest Najlepsza w przypadku pracy w wielu mniejszych plikach.  Usługa Azure Storage gwarantuje taką samą wydajność, niezależnie od tego, jak duże pliki (o ile przekraczają limity).  Ta gwarancja oznacza, że można przechowywać terabajty i nadal uzyskiwać spójną wydajność, niezależnie od tego, czy używasz podzestawu danych, czy wszystkich danych.

Usługa Azure Storage ma kilka różnych typów obiektów BLOB.  *Dołącz obiekt BLOB* jest doskonałym rozwiązaniem do przechowywania dzienników sieci Web lub danych czujników.  

Wiele obiektów BLOB może być dystrybuowanych na wielu serwerach w celu skalowania w poziomie dostępu do nich. Ale jeden obiekt BLOB może być obsługiwany tylko przez jeden serwer. Obiekty blob można logicznie grupować w kontenerach obiektów blob, ale nie ma to wpływu na partycjonowanie z tego grupowania.

Usługa Azure Storage ma również warstwę interfejsu API WebHDFS dla magazynu obiektów BLOB.  Wszystkie usługi w usłudze HDInsight mogą uzyskiwać dostęp do plików w usłudze Azure Blob Storage na potrzeby czyszczenia i przetwarzania danych. Podobnie jak w przypadku korzystania z systemu plików rozproszonych Hadoop (HDFS).

Dane są zwykle pozyskiwane w usłudze Azure Storage przy użyciu programu PowerShell, zestawu SDK usługi Azure Storage lub AZCopy.

### <a name="azure-data-lake-storage"></a>Azure Data Lake Storage

Azure Data Lake Storage (ADLS) to zarządzane repozytorium do skalowania. Repozytorium dla danych analitycznych, które są zgodne z systemem plików HDFS.  ADLS używa modelu projektowania podobnego do systemu plików HDFS. ADLS oferuje nieograniczoną możliwość adaptacji do całkowitej pojemności i rozmiaru poszczególnych plików. ADLS jest dobre podczas pracy z dużymi plikami, ponieważ duży plik może być przechowywany w wielu węzłach.  Partycjonowanie danych w ADLS odbywa się w tle.  Uzyskujesz ogromne przepływność, aby uruchamiać zadania analityczne z tysiącami współbieżnych modułów uruchamiających, które efektywnie odczytują i zapisują setki terabajtów danych.

Dane są zwykle wprowadzane do ADLS przy użyciu Azure Data Factory. Lub ADLS SDK, usługi AdlCopy, Apache pomocą distcp lub Apache Sqoop.  Które z tych usług są duże, zależy od tego, gdzie są dane.  Jeśli dane są obecnie w istniejącym klastrze usługi Hadoop, można użyć usługi Apache pomocą distcp, AdlCopy lub Azure Data Factory.  W przypadku danych w usłudze Azure Blob Storage można używać Azure Data Lake Storage .NET SDK, Azure PowerShell lub Azure Data Factory.

ADLS jest również zoptymalizowany pod kątem pozyskiwania zdarzeń przy użyciu usługi Azure Event Hub lub Apache Storm.

#### <a name="considerations-for-both-storage-options"></a>Zagadnienia dotyczące obu opcji magazynu

W przypadku przekazywania zestawów danych w zakresie terabajtów opóźnienie sieci może być poważnym problemem, szczególnie w przypadku, gdy dane pochodzą z lokalizacji lokalnej.  W takich przypadkach można użyć poniższych opcji:

* Azure ExpressRoute: usługa Azure ExpressRoute umożliwia tworzenie prywatnych połączeń między centrami danych platformy Azure a infrastrukturą lokalną. Te połączenia zapewniają niezawodną opcję przesyłania dużych ilości danych. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure ExpressRoute](../../expressroute/expressroute-introduction.md).

* Przekazywanie danych w trybie offline. [Usługa Azure Import/Export](../../storage/common/storage-import-export-service.md) umożliwia wysyłanie dysków twardych z danymi do centrum danych platformy Azure. Dane są najpierw przekazywane do obiektów BLOB usługi Azure Storage. Następnie można użyć Azure Data Factory lub narzędzia AdlCopy do kopiowania danych z obiektów BLOB usługi Azure Storage do Data Lake Storage.

### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

Usługa Azure SQL DW to doskonały wybór do przechowywania przygotowanych wyników.  Usługa Azure HDInsight może służyć do wykonywania tych usług dla usługi Azure SQL DW.

Azure SQL Data Warehouse (SQL DW) to relacyjny magazyn baz danych zoptymalizowany pod kątem obciążeń analitycznych.  Usługa Azure SQL DW jest skalowana na podstawie partycjonowanych tabel.  Tabele mogą być partycjonowane w wielu węzłach.  W momencie tworzenia są wybierane węzły magazynu danych SQL Azure.  Mogą one być skalowane po fakcie, ale jest to aktywny proces, który może wymagać przeniesienia danych. Aby uzyskać więcej informacji, zobacz [SQL Data Warehouse-Manage COMPUTE](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).

### <a name="apache-hbase"></a>Apache HBase

Apache HBase jest magazynem klucz-wartość dostępnym w usłudze Azure HDInsight.  Apache HBase jest bazą danych NoSQL typu open source opartą na platformie Hadoop i wzorowaną na bazie danych Google BigTable. HBase zapewnia dostęp losowy i silną spójność w przypadku dużych ilości danych bez struktury i częściową strukturą. Dane w bazie danych bezschemat zorganizowane według rodzin kolumn.

Dane są przechowywane w wierszach tabeli, a dane w obrębie wiersza są zgrupowane według rodziny kolumn. HBase to bezschematowa baza danych. Nie trzeba definiować kolumn i typów danych przechowywanych w nich przed ich użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. HBase może polegać na nadmiarowości danych, przetwarzaniu wsadowym i innych funkcjach, które są dostarczane przez aplikacje rozproszone w środowisku Hadoop.

HBase to doskonałe miejsce docelowe dla danych czujników i dzienników do przyszłej analizy.

HBase adaptacja zależy od liczby węzłów w klastrze usługi HDInsight.

### <a name="azure-sql-database-and-azure-database"></a>Azure SQL Database i Azure Database

Platforma Azure oferuje trzy różne relacyjne bazy danych jako platformę jako usługę (PAAS).

* [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) jest implementacją Microsoft SQL Server. Aby uzyskać więcej informacji na temat wydajności, zobacz [dostrajanie wydajności w Azure SQL Database](../../sql-database/sql-database-performance-guidance.md).
* [Azure Database for MySQL](../../mysql/overview.md) to implementacja programu Oracle MySQL.
* [Azure Database for PostgreSQL](../../postgresql/quickstart-create-server-database-portal.md) to implementacja PostgreSQL.

Te produkty można skalować w górę, co oznacza, że są skalowane przez dodanie większej liczby procesorów i pamięci.  Możesz również użyć dysków Premium z produktami w celu uzyskania lepszej wydajności operacji we/wy.

## <a name="azure-analysis-services"></a>Azure Analysis Services

Azure Analysis Services (AAS) to analityczny aparat danych używany w ramach pomocy technicznej decyzyjnej i analizy biznesowej. Firma AAS udostępnia dane analityczne dla raportów firmowych i aplikacji klienckich, takich jak Power BI. Również program Excel, raporty usług Reporting Services i inne narzędzia do wizualizacji danych.

Moduły analizy można skalować przez zmianę warstw dla poszczególnych modułów.  Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="extract-and-load"></a>Wyodrębnij i Załaduj

Gdy dane istnieją na platformie Azure, możesz użyć wielu usług, aby wyodrębnić i załadować je do innych produktów.  Usługa HDInsight obsługuje Sqoop i Flume.

### <a name="apache-sqoop"></a>Apache Sqoop

Apache Sqoop to narzędzie służące do wydajnego transferu danych między strukturalnymi i częściowo strukturalnymi źródłami danych.

Sqoop używa MapReduce do importowania i eksportowania danych, w celu zapewnienia równoległej operacji i odporności na uszkodzenia.

### <a name="apache-flume"></a>Apache Flume

`Apache Flume`jest dystrybuowaną, niezawodną i dostępną usługą umożliwiającą wydajne zbieranie, agregowanie i przemieszczanie dużych ilości danych dzienników. Flume ma elastyczną architekturę opartą na przepływach danych przesyłanych strumieniowo. Flume jest niezawodna i odporna na uszkodzenia z mechanizmami niezawodności możliwość dostosowania i wieloma mechanizmami trybu failover i odzyskiwania. Flume korzysta z prostego rozszerzalnego modelu danych, który umożliwia aplikacji analitycznych online.

Nie można używać Apache Flume z usługą Azure HDInsight.  Lokalna instalacja usługi Hadoop może używać Flume do wysyłania danych do obiektów blob magazynu Azure lub Azure Data Lake Storage.  Aby uzyskać więcej informacji, zobacz [Korzystanie z platformy Apache Flume z usługą HDInsight](https://web.archive.org/web/20190217104751/https://blogs.msdn.microsoft.com/bigdatasupport/2014/03/18/using-apache-flume-with-hdinsight/).

## <a name="transform"></a>Przekształcanie

Gdy dane istnieją w wybranej lokalizacji, należy je wyczyścić, połączyć lub przygotować do określonego wzorca użycia.  Usługi Hive, świnie i Spark SQL są dobrym wyborami dla tego rodzaju pracy.  Są one obsługiwane w usłudze HDInsight.

## <a name="next-steps"></a>Następne kroki

* [Używanie Apache Hive jako narzędzia ETL](apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Używanie Azure Data Lake Storage Gen2 z klastrami usługi Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
* [Przenoszenie danych z Azure SQL Database do tabeli Apache Hive](./apache-hadoop-use-sqoop-mac-linux.md)
