---
title: Co to jest Apache HBase w usłudze Azure HDInsight?
description: Wprowadzenie do bazy danych Apache HBase w usłudze HDInsight — bazy danych NoSQL opartej na platformie Hadoop. Dowiedz się więcej o przypadkach użycia i porównaj bazę danych HBase z innymi klastrami Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: e977d4d68a330f57c4b53da4270e4515d4e69ee0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729820"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co to jest Apache HBase w usłudze Azure HDInsight

[Apache HBase](https://hbase.apache.org/) jest open-source, NoSQL bazy danych, która jest zbudowana na Apache Hadoop i wzorowany na [Google BigTable](https://cloud.google.com/bigtable/). HBase zapewnia dostęp losowy i silną spójność dla dużych ilości danych w bazie danych bez schematu. Baza danych jest zorganizowana według rodzin kolumn.

Z punktu widzenia użytkownika HBase jest podobny do bazy danych. Dane są przechowywane w wierszach i kolumnach tabeli, a dane w wierszu są pogrupowane według rodziny kolumn. HBase jest bazą danych bez schematu. Kolumny i typy danych mogą być niezdefiniowane przed ich użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Może polegać na nadmiarowość danych, przetwarzania wsadowego i innych funkcji, które są dostarczane przez aplikacje rozproszone w środowisku Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak apache HBase jest implementowany w usłudze Azure HDInsight?

Baza danych HBase w usłudze HDInsight jest oferowana jako zarządzany klaster zintegrowany ze środowiskiem Azure. Klastry są skonfigurowane do przechowywania danych bezpośrednio w [usłudze Azure Storage,](./../hdinsight-hadoop-use-blob-storage.md)co zapewnia małe opóźnienia i większą elastyczność w wyborze wydajności i kosztów. Ta właściwość umożliwia klientom tworzenie interaktywnych witryn sieci Web, które pracują z dużymi zestawami danych. Aby utworzyć usługi, które przechowują dane czujników i telemetrii z milionów punktów końcowych. I do analizy tych danych z zadaniami Hadoop. HBase i Hadoop są dobrymi punktami wyjścia dla projektu dużych zbiorów danych na platformie Azure. Usługi mogą włączyć aplikacje w czasie rzeczywistym do pracy z dużych zestawów danych.

Implementacja HDInsight używa architektury skalowania w poziomie bazy HBase w celu zapewnienia automatycznego dzielenia na fragmenty tabel. I silna spójność dla odczytów i zapisów oraz automatyczna funkcja pracy awaryjnej. Wydajność jest zwiększona dzięki buforowaniu w pamięci operacji odczytu i przesyłaniu strumieniowemu o wysokiej przepustowości obejmującemu operacje zapisu. Klaster bazy danych HBase można utworzyć w sieci wirtualnej. Aby uzyskać szczegółowe informacje, zobacz temat [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Tworzenie klastrów usługi HDInsight w usłudze Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>W jaki sposób zarządzane są dane w bazie danych HBase w usłudze HDInsight?

W bazie danych HBase można zarządzać danymi za pomocą poleceń `create`, `get`, `put` i `scan` z poziomu powłoki HBase. Dane są zapisywane w bazie danych przy użyciu polecenia `put` i odczytywane przy użyciu polecenia `get`. Polecenie `scan` jest używane do uzyskiwania danych z wielu wierszy w tabeli. Danymi można również zarządzać przy użyciu interfejsu API w języku C# bazy danych HBase, który udostępnia bibliotekę klienta ponad interfejsem API REST HBase. Bazy danych HBase można również zbadać za pomocą [Apache Hive](https://hive.apache.org/). Aby zapoznać się z wprowadzeniem do tych modeli programowania, zobacz [Wprowadzenie do korzystania z bazy apache HBase z apache Hadoop w programie HDInsight](./apache-hbase-tutorial-get-started-linux.md). Współprocesory są również dostępne, które umożliwiają przetwarzanie danych w węzłach, które obsługują bazę danych.

> [!NOTE]  
> Platforma Thrift nie jest obsługiwana przez bazę danych HBase w usłudze HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Przypadki użycia dla Apache HBase

Kanoniczny przypadek użycia, dla którego BigTable (i przez rozszerzenie, HBase) został utworzony z wyszukiwania w internecie. Aparaty wyszukiwania tworzą indeksy, które mapują terminy na zawierające je strony sieci Web. Istnieje jednak wiele innych przypadków użycia, w których baza danych HBase jest przydatna — niektóre z nich są wymienione w tej sekcji.

|Scenariusz |Opis |
|---|---|
|Magazyn wartości klucza|HBase może służyć jako magazyn klucz wartość i nadaje się do zarządzania systemami wiadomości. Facebook używa HBase dla swojego systemu wiadomości i jest idealny do przechowywania i zarządzania komunikacją internetową. Usługa WebTable używa bazy danych HBase do wyszukiwania tabel wyodrębnianych ze stron sieci Web oraz zarządzania nimi.|
|Dane czujników|Baza danych HBase może służyć do przechwytywania danych gromadzonych przyrostowo z różnych źródeł. Dane te obejmują analitykę społeczną i szeregi czasowe. I aktualizowanie interaktywnych pulpitów nawigacyjnych z trendami i licznikami oraz zarządzanie systemami dzienników inspekcji. Przykłady obejmują terminal handlowy Bloomberg i open time series database (OpenTSDB). OpenTSDB przechowuje i zapewnia dostęp do metryk zebranych na temat kondycji systemów serwerowych.|
|Zapytania w czasie rzeczywistym|[Apache Phoenix](https://phoenix.apache.org/) jest aparatem zapytań SQL dla Apache HBase. Jest dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i zarządzanie tabelami HBase przy użyciu języka SQL.|
|HBase jako platforma|Aplikacje mogą działać w bazie danych HBase, wykorzystując ją jako magazyn danych. Przykłady obejmują Phoenix, OpenTSDB `Kiji`i Titan. Aplikacje można również integrować z bazą danych HBase. Przykłady obejmują: [Apache Hive](https://hive.apache.org/), Apache Pig, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark i `Ganglia`Apache Drill.|

## <a name="next-steps"></a>Następne kroki

* [Zacznij korzystać z Apache HBase z Apache Hadoop w HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Tworzenie klastrów usługi HDInsight w usłudze Azure Virtual Network)
* [Konfigurowanie replikacji Apache HBase w udziale usługi HDInsight](apache-hbase-replication.md)
