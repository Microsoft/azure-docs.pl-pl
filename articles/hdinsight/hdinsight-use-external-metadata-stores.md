---
title: Korzystanie z zewnętrznych magazynów metadanych — Azure HDInsight
description: Użyj zewnętrznych magazynów metadanych z klastrami usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 08/06/2020
ms.openlocfilehash: a001f3a13daf40a1af712f09e35d93fd045ea133
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350249"
---
# <a name="use-external-metadata-stores-in-azure-hdinsight"></a>Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight

Usługa HDInsight umożliwia przejęcie kontroli nad danymi i metadanymi przy użyciu zewnętrznych magazynów danych. Ta funkcja jest dostępna dla [Apache Hive magazynu metadanych](#custom-metastore), usługi [Apache Oozie](#apache-oozie-metastore)i [bazy danych Apache Ambari](#custom-ambari-db).

Magazyn metadanych Apache Hive w usłudze HDInsight jest istotną częścią architektury Apache Hadoop. Magazyn metadanych jest centralnym repozytorium schematów. Magazyn metadanych jest używany przez inne narzędzia dostępu do danych Big Data, takie jak Apache Spark, Interactive Query (LLAP), Presto lub Apache świni. Usługa HDInsight używa Azure SQL Database jako magazyn metadanych Hive.

![Architektura magazynu metadanych usługi HDInsight Hive](./media/hdinsight-use-external-metadata-stores/metadata-store-architecture.png)

Istnieją dwa sposoby konfigurowania magazynu metadanych dla klastrów usługi HDInsight:

* [Domyślny magazyn metadanych](#default-metastore)
* [Niestandardowy magazyn metadanych](#custom-metastore)

## <a name="default-metastore"></a>Domyślny magazyn metadanych

Domyślnie Usługa HDInsight tworzy magazyn metadanych dla każdego typu klastra. Zamiast tego można określić niestandardowy magazyn metadanych. Domyślny magazyn metadanych obejmuje następujące zagadnienia:

* Brak dodatkowych kosztów. Usługa HDInsight tworzy magazyn metadanych z każdym typem klastra bez dodatkowych kosztów.

* Każdy domyślny magazyn metadanych jest częścią cyklu życia klastra. Po usunięciu klastra są również usuwane odpowiednie magazyny i metadane.

* Nie można udostępnić domyślnego magazynu metadanych w innych klastrach.

* Domyślnym magazynem metadanych zaleca się tylko w przypadku prostych obciążeń. Obciążenia, które nie wymagają wielu klastrów i nie wymagają metadanych zachowywanych poza cyklem życia klastra.

> [!IMPORTANT]
> Domyślny magazyn metadanych zawiera Azure SQL Database z **limitem jednostek DTU warstwy Podstawowa 5 (bez uaktualnienia).** Odpowiednie dla podstawowych celów testowych. W przypadku obciążeń dużych lub produkcyjnych zalecamy Migrowanie do zewnętrznego magazynu metadanych.

## <a name="custom-metastore"></a>Niestandardowy magazyn metadanych

Usługa HDInsight obsługuje również niestandardowe magazyny metadanych, które są zalecane w przypadku klastrów produkcyjnych:

* Jako magazyn metadanych należy określić własne Azure SQL Database.

* Cykl życia magazynu metadanych nie jest powiązany z cyklem życia klastrów, dzięki czemu można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy Hive, będą zachowywane nawet po usunięciu i ponownym utworzeniu klastra usługi HDInsight.

* Niestandardowy magazyn metadanych umożliwia dołączenie wielu klastrów i typów klastrów do tego magazynu. Na przykład pojedynczy magazyn metadanych może być współużytkowany przez zapytania interaktywne, Hive i klastry Spark w usłudze HDInsight.

* Płacisz za koszt magazynu metadanych (Azure SQL Database) zgodnie z wybranym poziomem wydajności.

* W razie konieczności można skalować w górę magazyn metadanych.

* Klaster i zewnętrzny magazyn metadanych muszą być hostowane w tym samym regionie.

![Przypadek użycia magazynu metadanych Hive usługi HDInsight](./media/hdinsight-use-external-metadata-stores/metadata-store-use-case.png)

### <a name="create-and-config-azure-sql-database-for-the-custom-metastore"></a>Tworzenie i konfiguracja Azure SQL Database dla niestandardowego magazynu metadanych

Przed skonfigurowaniem niestandardowego magazyn metadanych Hive klastra usługi HDInsight należy utworzyć lub mieć istniejące Azure SQL Database.  Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pojedynczej bazy danych w Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal).

Podczas tworzenia klastra Usługa HDInsight musi połączyć się z zewnętrznym magazynem metadanych i zweryfikować swoje poświadczenia. Skonfiguruj reguły zapory Azure SQL Database, aby zezwolić usługom i zasobom platformy Azure na dostęp do serwera. Włącz tę opcję w Azure Portal, wybierając pozycję **Ustaw zaporę serwera**. Następnie wybierz pozycję **nie** poniżej opcji **Odmów dostępu do sieci publicznej** i **tak** poniżej **Zezwól usługom i zasobom platformy Azure na dostęp do tego serwera** w celu Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Tworzenie reguł zapory IP i zarządzanie nimi](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)

Prywatne punkty końcowe dla magazynów SQL są obsługiwane tylko w klastrach utworzonych za pomocą `outbound` ResourceProviderConnection. Aby dowiedzieć się więcej, zobacz tę [dokumentację](./hdinsight-private-link.md).

![przycisk ustawiania zapory serwera](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall1.png)

![Zezwalaj na dostęp do usług platformy Azure](./media/hdinsight-use-external-metadata-stores/configure-azure-sql-database-firewall2.png)

### <a name="select-a-custom-metastore-during-cluster-creation"></a>Wybierz niestandardowy magazyn metadanych podczas tworzenia klastra

Możesz wskazać klaster do wcześniej utworzonego Azure SQL Database w dowolnym momencie. Aby można było utworzyć klaster za pośrednictwem portalu, opcja jest określana na podstawie **ustawień magazynu > Storage**.

![Magazyn metadanych Hive usługi HDInsight Azure Portal](./media/hdinsight-use-external-metadata-stores/azure-portal-cluster-storage-metastore.png)

## <a name="hive-metastore-guidelines"></a>Wskazówki dotyczące magazyn metadanych Hive

> [!NOTE]
> W miarę możliwości używaj niestandardowego magazynu metadanych, aby pomóc w oddzieleniu zasobów obliczeniowych (uruchomiony klaster) i metadanych (przechowywanych w magazynie Metadata). Zacznij od warstwy S2, która zapewnia 50 jednostek DTU i 250 GB miejsca w magazynie. Jeśli widzisz wąskie gardło, możesz skalować bazę danych w górę.

* Jeśli zamierzasz korzystać z wielu klastrów usługi HDInsight, aby uzyskać dostęp do oddzielnych danych, użyj oddzielnej bazy danych dla magazynu metadanych w każdym klastrze. W przypadku udostępniania magazynu metadanych w wielu klastrach usługi HDInsight oznacza to, że w klastrach są używane te same metadane i bazowe pliki danych użytkownika.

* Okresowo twórz kopię zapasową niestandardowego magazynu metadanych. Azure SQL Database automatycznie generuje kopie zapasowe, ale przedział czasu przechowywania kopii zapasowych jest różny. Aby uzyskać więcej informacji, zobacz informacje [o automatycznym SQL Database kopii zapasowych](../azure-sql/database/automated-backups-overview.md).

* Lokalizowanie magazynu metadanych i klastra usługi HDInsight w tym samym regionie. Ta konfiguracja zapewni najwyższą wydajność i najniższe opłaty za ruch wychodzący w sieci.

* Monitoruj magazyn metadanych pod kątem wydajności i dostępności przy użyciu narzędzi do monitorowania Azure SQL Database lub dzienników Azure Monitor.

* Gdy nowa, wyższa wersja usługi Azure HDInsight jest tworzona na podstawie istniejącej niestandardowej bazy danych magazynu metadanych, system uaktualnia schemat magazynu metadanych. Uaktualnienie jest nieodwracalne bez przywracania bazy danych z kopii zapasowej.

* W przypadku udostępniania magazynu metadanych w wielu klastrach upewnij się, że wszystkie klastry są w tej samej wersji usługi HDInsight. Różne wersje programu Hive używają różnych schematów bazy danych magazynu metadanych. Nie można na przykład udostępnić magazynu metadanych w ramach klastrów Hive 2,1 i Hive 3,1

* W usłudze HDInsight 4,0, Spark i Hive używają niezależnych wykazów do uzyskiwania dostępu do tabel SparkSQL lub Hive. Tabela utworzona przez platformę Spark w katalogu Spark. Tabela utworzona przez program Hive znajduje się w katalogu Hive. To zachowanie różni się od usługi HDInsight 3,6, w której znajduje się gałąź Hive i wspólny katalog usługi Spark. Integracja Hive i Spark w usłudze HDInsight 4,0 opiera się na łączniku magazynu Hive (obsługiwane). OBSŁUGIWANE działa jako Most między platformami Spark i Hive. [Dowiedz się więcej o łączniku magazynu Hive](../hdinsight/interactive-query/apache-hive-warehouse-connector.md).

* W usłudze HDInsight 4,0, jeśli chcesz udostępnić magazyn metadanych między platformami Hive i Spark, możesz to zrobić, zmieniając wartość właściwości magazynu metadanych. Catalog. default na Hive w klastrze Spark. Tę właściwość można znaleźć w Ambari Advanced spark2-Hive-site-override. Ważne jest, aby zrozumieć, że udostępnianie magazynu metadanych działa tylko w przypadku zewnętrznych tabel programu Hive, nie będzie to działać, jeśli istnieją wewnętrzne/zarządzane tabele lub tabele w środowisku Hive.  

## <a name="apache-oozie-metastore"></a>Magazyn metadanych Apache Oozie

Apache Oozie to system koordynacji przepływu pracy, który zarządza zadaniami na platformie Hadoop. Usługa Oozie obsługuje zadania Hadoop dla platformy Apache MapReduce, trzody chlewnej, Hive i innych.  Oozie używa magazynu metadanych do przechowywania szczegółowych informacji o przepływach pracy. Aby zwiększyć wydajność podczas korzystania z programu Oozie, można użyć Azure SQL Database jako niestandardowego magazynu metadanych. Magazyn metadanych zapewnia dostęp do danych zadania Oozie po usunięciu klastra.

Aby uzyskać instrukcje dotyczące tworzenia magazynu metadanych Oozie z Azure SQL Database, zobacz [Korzystanie z platformy Apache Oozie dla przepływów pracy](hdinsight-use-oozie-linux-mac.md).

## <a name="custom-ambari-db"></a>Niestandardowa baza danych Ambari

Aby użyć własnej zewnętrznej bazy danych z oprogramowaniem Apache Ambari w usłudze HDInsight, zobacz temat [Custom dataAmbari Database](hdinsight-custom-ambari-db.md).

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie klastrów w usłudze HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i innych](./hdinsight-hadoop-provision-linux-clusters.md)