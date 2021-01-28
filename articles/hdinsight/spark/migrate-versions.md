---
title: Migrowanie obciążeń Apache Spark 2,1 lub 2,2 do 2,3 lub 2,4 — Azure HDInsight
description: Dowiedz się, jak migrować Apache Spark 2,1 i 2,2 do 2,3 lub 2,4.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944754"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Migrowanie obciążeń Apache Spark 2,1 i 2,2 do 2,3 i 2,4

W tym dokumencie opisano sposób migrowania obciążeń Apache Spark na platformie Spark 2,1 i 2,2 do 2,3 lub 2,4.

Zgodnie z opisem w [informacjach o wersji](../hdinsight-release-notes.md#upcoming-changes), począwszy od 1 lipca 2020, następujące konfiguracje klastra nie będą obsługiwane, a klienci nie będą mogli tworzyć nowych klastrów z tymi konfiguracjami:
 - Platforma Spark 2,1 i 2,2 w klastrze usługi HDInsight 3,6 Spark
 - Platforma Spark 2,3 w klastrze usługi HDInsight 4,0 Spark

Istniejące klastry w tych konfiguracjach będą uruchamiane bez pomocy technicznej firmy Microsoft. Jeśli korzystasz z platformy Spark 2,1 lub 2,2 w usłudze HDInsight 3,6, przejdź do platformy Spark 2,3 w usłudze HDInsight 3,6 w dniu 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej. Jeśli korzystasz z platformy Spark 2,3 w klastrze usługi HDInsight 4,0, przejdź do platformy Spark 2,4 w usłudze HDInsight 4,0 do czerwca 30 2020, aby uniknąć potencjalnych przerw w działaniu systemu/obsługi.

Aby uzyskać ogólne informacje na temat migrowania klastra usługi HDInsight z 3,6 do 4,0, zobacz [Migrowanie klastra usługi HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md). Aby uzyskać ogólne informacje na temat migracji do nowszej wersji Apache Spark, zobacz [Apache Spark: zasady przechowywania wersji](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Wskazówki dotyczące uaktualnień wersji platformy Spark w usłudze HDInsight

| Scenariusz uaktualniania | Mechanism (Mechanizm) | Rzeczy do rozważenia | Integracja z platformą Spark/Hive |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3,6 Spark 2,1 do usługi HDInsight 3,6 Spark 2,3| Ponowne tworzenie klastrów za pomocą usługi HDInsight Spark 2,3 | Zapoznaj się z następującymi artykułami: <br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,2 do 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,1 do 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Bez zmian |
|HDInsight 3,6 Spark 2,2 do usługi HDInsight 3,6 Spark 2,3 | Ponowne tworzenie klastrów za pomocą usługi HDInsight Spark 2,3 | Zapoznaj się z następującymi artykułami: <br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,2 do 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Bez zmian |
| HDInsight 3,6 Spark 2,1 do usługi HDInsight 4,0 Spark 2,4 | Ponowne tworzenie klastrów za pomocą usługi HDInsight 4,0 Spark 2,4 | Zapoznaj się z następującymi artykułami: <br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,3 do 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,2 do 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,1 do 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Integracja z platformami Spark i Hive została zmieniona w usłudze HDInsight 4,0. <br><br> W usłudze HDInsight 4,0, Spark i Hive używają niezależnych wykazów do uzyskiwania dostępu do tabel SparkSQL lub Hive. Tabela utworzona przez platformę Spark w katalogu Spark. Tabela utworzona przez program Hive znajduje się w katalogu Hive. To zachowanie różni się od usługi HDInsight 3,6, w której znajduje się gałąź Hive i wspólny katalog usługi Spark. Integracja Hive i Spark w usłudze HDInsight 4,0 opiera się na łączniku magazynu Hive (obsługiwane). OBSŁUGIWANE działa jako Most między platformami Spark i Hive. Dowiedz się więcej o łączniku magazynu Hive. <br> W usłudze HDInsight 4,0, jeśli chcesz udostępnić magazyn metadanych między platformami Hive i Spark, możesz to zrobić, zmieniając wartość właściwości magazynu metadanych. Catalog. default na Hive w klastrze Spark. Tę właściwość można znaleźć w Ambari Advanced spark2-Hive-site-override. Ważne jest, aby zrozumieć, że udostępnianie magazynu metadanych działa tylko w przypadku zewnętrznych tabel programu Hive, nie będzie to działać, jeśli istnieją wewnętrzne/zarządzane tabele lub tabele w środowisku Hive. <br><br>Aby uzyskać więcej informacji, zapoznaj [się z artykułem Migrowanie obciążeń usługi Azure HDInsight 3,6 w usłudze hdinsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) .<br><br> |
| HDInsight 3,6 Spark 2,2 do usługi HDInsight 4,0 Spark 2,4 | Ponowne tworzenie klastrów za pomocą usługi HDInsight 4,0 Spark 2,4 | Zapoznaj się z następującymi artykułami: <br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,3 do 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: Uaktualnianie z platformy Spark SQL 2,2 do 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Integracja z platformami Spark i Hive została zmieniona w usłudze HDInsight 4,0. <br><br> W usłudze HDInsight 4,0, Spark i Hive używają niezależnych wykazów do uzyskiwania dostępu do tabel SparkSQL lub Hive. Tabela utworzona przez platformę Spark w katalogu Spark. Tabela utworzona przez program Hive znajduje się w katalogu Hive. To zachowanie różni się od usługi HDInsight 3,6, w której znajduje się gałąź Hive i wspólny katalog usługi Spark. Integracja Hive i Spark w usłudze HDInsight 4,0 opiera się na łączniku magazynu Hive (obsługiwane). OBSŁUGIWANE działa jako Most między platformami Spark i Hive. Dowiedz się więcej o łączniku magazynu Hive. <br> W usłudze HDInsight 4,0, jeśli chcesz udostępnić magazyn metadanych między platformami Hive i Spark, możesz to zrobić, zmieniając wartość właściwości magazynu metadanych. Catalog. default na Hive w klastrze Spark. Tę właściwość można znaleźć w Ambari Advanced spark2-Hive-site-override. Ważne jest, aby zrozumieć, że udostępnianie magazynu metadanych działa tylko w przypadku zewnętrznych tabel programu Hive, nie będzie to działać, jeśli istnieją wewnętrzne/zarządzane tabele lub tabele w środowisku Hive. <br><br>Aby uzyskać więcej informacji, zapoznaj [się z artykułem Migrowanie obciążeń usługi Azure HDInsight 3,6 w usłudze hdinsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) .|

## <a name="next-steps"></a>Następne kroki

* [Migrowanie klastra usługi HDInsight do nowszej wersji](../hdinsight-upgrade-cluster.md)
* [Migrowanie obciążeń platformy Azure HDInsight 3,6 do usługi HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md)
