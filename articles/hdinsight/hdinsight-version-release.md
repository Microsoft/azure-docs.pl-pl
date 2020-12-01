---
title: HDInsight 4,0 — Omówienie — Azure
description: Porównanie funkcji i ograniczeń usług HDInsight 3.6 i HDInsight 4.0 oraz rekomendacje dotyczące uaktualniania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.openlocfilehash: 2fd7a3e512b79651fdcf6a6ac0c14822361fc263
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350198"
---
# <a name="azure-hdinsight-40-overview"></a>Omówienie usługi Azure HDInsight 4,0

Usługa Azure HDInsight jest jedną z najpopularniejszych usług między klientami korporacyjnymi Apache Hadoop i Apache Spark. HDInsight 4,0 to dystrybucja w chmurze Apache Hadoop składników. Ten artykuł zawiera informacje o najnowszym wydaniu usługi Azure HDInsight i sposobie jej uaktualniania.

## <a name="whats-new-in-hdinsight-40"></a>Co nowego w usłudze HDInsight 4,0?

### <a name="apache-hive-30-and-low-latency-analytical-processing"></a>Apache Hive 3,0 i przetwarzania analitycznego o małym opóźnieniu

Apache Hive przetwarzania analitycznego o małym opóźnieniu (LLAP) używa trwałych serwerów zapytań i buforowania w pamięci. Ten proces zapewnia szybkie wyniki zapytania SQL dotyczące danych w magazynie w chmurze zdalnej. Gałąź Hive LLAP korzysta z zestawu stałych demonów, które wykonują fragmenty zapytań Hive. Wykonywanie zapytań z przetwarzaniem LLAP odbywa się podobnie jak w infrastrukturze Hive bez funkcji LLAP z tą różnicą, że zadania procesów roboczych działają wewnątrz demonów LLAP zamiast w kontenerach.

Przetwarzanie Hive LLAP daje następujące korzyści:

* Możliwość przeprowadzenia głębokiej analizy SQL bez obniżania wydajności i adaptacji. Takie jak złożone sprzężenia, podzapytania, funkcje okna, sortowanie, funkcje zdefiniowane przez użytkownika i złożone agregacje.

* Możliwość wykonywania interaktywnych zapytań względem danych w tym samym magazynie, w którym dane są przygotowywane, co eliminuje konieczność przenoszenia danych z magazynu do innego aparatu na potrzeby przetwarzania analitycznego.

* Wyniki zapytania buforowania umożliwiają ponownie użycie poprzednio obliczonych wyników zapytania. Ta pamięć podręczna oszczędza czas i zasoby poświęcające na uruchamianie zadań klastra wymaganych do zapytania.

### <a name="hive-dynamic-materialized-views"></a>Dynamiczne zmaterializowane widoki Hive

Gałąź Hive obsługuje teraz dynamiczne widoki z materiałami lub wstępne obliczanie odpowiednich podsumowań. Widoki przyspieszają przetwarzanie zapytań w magazynach danych. Zmaterializowane widoki mogą być przechowywane w sposób natywny w infrastrukturze Hive i mogą bezproblemowo korzystać z przyspieszenia LLAP.

### <a name="hive-transactional-tables"></a>Tabele transakcyjne Hive

HDI 4,0 zawiera Apache Hive 3. Gałąź 3 wymaga niepodzielności, spójności, izolacji i trwałości dla tabel transakcyjnych, które znajdują się w magazynie Hive. Tabele zgodne ze standardem ACID oraz przechowywane w nich dane są dostępne i zarządzane za pośrednictwem programu Hive. Dane w tabelach Create, pobierając, Update i Delete (CRUD) muszą mieć format pliku o zoptymalizowanej kolumnie wiersza (ORC). Tabele tylko do wstawiania obsługują wszystkie formaty plików.

* Standard ACID w wersji 2 wprowadza ulepszenia wydajności zarówno w formacie magazynu, jak i aparatu wykonywania.

* Standard ACID jest domyślnie włączony, co umożliwia pełną obsługę aktualizacji danych.

* Ulepszone funkcje ACID pozwalają na aktualizowanie i usuwanie na poziomie wiersza.

* Nie występuje negatywny wpływ na wydajność.

* Nie jest wymagana obsługa zasobników.

* Platforma Spark może odczytywać i zapisywać dane w tabelach Hive ACID za pośrednictwem łącznika magazynu Hive.

Dowiedz się więcej o infrastrukturze [Apache Hive 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/hive-overview/content/hive_whats_new_in_this_release_hive.html).

### <a name="apache-spark"></a>Apache Spark

Platforma Apache Spark pobiera możliwe do zaktualizowania tabele i transakcje ACID za pomocą łącznika magazynu Hive. Łącznik magazynu Hive umożliwia rejestrowanie tabel transakcyjnych Hive jako tabel zewnętrznych na platformie Spark w celu uzyskania dostępu do wszystkich funkcji transakcyjnych. Poprzednie wersje obsługiwały tylko manipulowanie partycjami tabel. Łącznik magazynu Hive obsługuje również przesyłanie strumieniowe ramek danych.  Ten proces umożliwia strumieniowe odczytywanie i zapisywanie w transakcyjnych i strumieniowych tabelach Hive z platformy Spark.

Funkcje wykonawcze platformy Spark mogą łączyć się bezpośrednio z demonami Hive LLAP na potrzeby pobierania i aktualizowania danych w sposób transakcyjny, co umożliwia zachowanie kontroli nad danymi przez infrastrukturę Hive.

Platforma Apache Spark w usłudze HDInsight 4.0 obsługuje następujące scenariusze:

* Uruchamianie szkolenia modelu uczenia maszynowego z wykorzystaniem tej samej tabeli transakcyjnej, która jest używana na potrzeby raportowania.
* Wykorzystanie transakcji ACID w celu bezpiecznego dodania kolumn z modelu Spark ML do tabeli Hive.
* Uruchamianie zadania przesyłania strumieniowego platformy Spark podczas zestawiania zmian z tabeli przesyłania strumieniowego Hive.
* Tworzenie plików ORC bezpośrednio na podstawie zadania przesyłania strumieniowego ze strukturą platformy Spark.

Nie trzeba już martwić się o przypadkowe próby dostępu do tabel transakcyjnych usługi Hive bezpośrednio z platformy Spark. Wynikowe niespójne wyniki, zduplikowane dane lub uszkodzenie danych. W usłudze HDInsight 4,0 tabele i tabele Hive platformy Spark są przechowywane w oddzielnych magazynach. Za pomocą łącznika magazynu danych Hive możesz jawnie zarejestrować tabele transakcyjne programu Hive jako tabele zewnętrzne platformy Spark.

Dowiedz się więcej o platformie [Apache Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/spark-overview/content/analyzing_data_with_apache_spark.html).

### <a name="apache-oozie"></a>Apache Oozie

Usługa HDI 4.0 zawiera system Apache Oozie 4.3.1 z następującymi zmianami:

* W systemie Oozie nie można już uruchamiać akcji programu Hive. Usunięto interfejs Hive CLI i zastąpiono go usługą BeeLine.

* Niepożądane zależności można wykluczyć z biblioteki udziałów, umieszczając wzorzec wykluczania w pliku **job.properties**.

Dowiedz się więcej o systemie [Apache Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/patch_oozie.html).

## <a name="how-to-upgrade-to-hdinsight-40"></a>Jak uaktualnić do usługi HDInsight 4,0

Dokładnie Przetestuj składniki przed implementacją najnowszej wersji w środowisku produkcyjnym. Usługa HDInsight 4,0 jest dostępna, aby rozpocząć proces uaktualniania. HDInsight 3,6 jest opcją domyślną, aby zapobiec przypadkowemu Mishaps.

Nie ma żadnej obsługiwanej ścieżki uaktualnienia z poprzednich wersji usługi HDInsight do usługi HDInsight 4,0. Ponieważ formaty magazynu metadanych i obiektów BLOB zostały zmienione, 4,0 nie jest zgodne z poprzednimi wersjami. Ważne jest, aby zachować nowe środowisko usługi HDInsight 4,0 niezależnie od bieżącego środowiska produkcyjnego. W przypadku wdrożenia usługi HDInsight 4,0 w bieżącym środowisku magazyn metadanych zostanie trwale uaktualniony.  

## <a name="limitations"></a>Ograniczenia

* Usługa HDInsight 4,0 nie obsługuje MapReduce dla Apache Hive. Zamiast tego należy użyć środowiska Apache Tez. Dowiedz się więcej o środowisku [Apache Tez](https://tez.apache.org/).
* Usługa HDInsight 4,0 nie obsługuje Apache Storm.
* Usługa HDInsight 4,0 nie obsługuje typu klastra usługi ML.
* Widok Hive jest dostępny tylko w przypadku klastrów usługi HDInsight 4,0 z numerem wersji równym lub większym niż 4,1. Ten numer wersji jest dostępny w wersji > administratora.
* Interpreter powłoki w programie Apache Zeppelin nie jest obsługiwany w przypadku klastrów Spark i Interactive zapytania zapytań.
* Nie można *wyłączyć* funkcji LLAP w klastrze Spark LLAP. Można tylko wyłączyć funkcję LLAP.
* Azure Data Lake Storage Gen2 nie można zapisać notesów Jupyter w klastrze Spark.
* Domyślnie program Apache świni działa w tez, ale można go zmienić na MapReduce
* Integracja programu Spark SQL Ranger dla zabezpieczeń wierszy i kolumn jest przestarzała
* Platforma Spark 2,4 i Kafka 2,1 są dostępne w usłudze HDInsight 4,0, więc platformy Spark 2,3 i Kafka 1,1 nie są już obsługiwane. Zalecamy używanie platformy Spark 2,4 & Kafka 2,1 i nowszych w usłudze HDInsight 4,0.

## <a name="next-steps"></a>Następne kroki

* [HBase — Przewodnik migracji](./hbase/apache-hbase-migrate-new-version.md)
* [Przewodnik migracji Hive](./interactive-query/apache-hive-migrate-workloads.md)
* [Kafka — Przewodnik migracji](./kafka/migrate-versions.md)
* [Przewodnik migracji platformy Spark](./spark/migrate-versions.md)
* [Dokumentacja usługi Azure HDInsight](index.yml)
* [Uwagi do wersji](hdinsight-release-notes.md)