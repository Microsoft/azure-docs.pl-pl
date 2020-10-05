---
title: Co to jest Apache HBase w usłudze Azure HDInsight?
description: Wprowadzenie do bazy danych Apache HBase w usłudze HDInsight — bazy danych NoSQL opartej na platformie Hadoop. Dowiedz się więcej o przypadkach użycia i porównaj bazę danych HBase z innymi klastrami Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: afbf9aff09999a34a84d55634a868250fbb6d1ff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "82188964"
---
# <a name="what-is-apache-hbase-in-azure-hdinsight"></a>Co to jest Apache HBase w usłudze Azure HDInsight

[Apache HBase](https://hbase.apache.org/) to baza danych NoSQL, która jest oparta na Apache Hadoop i modelowana po [BigTable](https://cloud.google.com/bigtable/). HBase zapewnia dostęp losowy i silną spójność w przypadku dużych ilości danych w bazie danych bezschematowej. Baza danych jest zorganizowana według rodzin kolumn.

Z perspektywy użytkownika HBase jest podobny do bazy danych. Dane są przechowywane w wierszach i kolumnach tabeli, a dane w wierszu są pogrupowane według rodziny kolumn. HBase to bezschematowa baza danych. Kolumny i typy danych mogą być niezdefiniowane przed użyciem. Kod typu open source zapewnia skalowanie liniowe, umożliwiając obsługę petabajtów danych na tysiącach węzłów. Może polegać na nadmiarowości danych, przetwarzaniu wsadowym i innych funkcjach udostępnianych przez aplikacje rozproszone w środowisku Hadoop.

## <a name="how-is-apache-hbase-implemented-in-azure-hdinsight"></a>Jak jest zaimplementowany Platforma Apache HBase w usłudze Azure HDInsight?

Baza danych HBase w usłudze HDInsight jest oferowana jako zarządzany klaster zintegrowany ze środowiskiem Azure. Klastry są skonfigurowane do przechowywania danych bezpośrednio w [usłudze Azure Storage](./../hdinsight-hadoop-use-blob-storage.md), co zapewnia małe opóźnienia i zwiększoną elastyczność w przypadku opcji wydajności i kosztów. Ta właściwość umożliwia klientom tworzenie interaktywnych witryn sieci Web, które działają z dużymi zestawami danych. Do kompilowania usług, które przechowują dane czujników i danych telemetrycznych z milionów punktów końcowych. I analizować te dane za pomocą zadań Hadoop. HBase i Hadoop to dobre punkty wyjścia dla projektu Big Data na platformie Azure. Usługi umożliwiają aplikacjom w czasie rzeczywistym współpracują z dużymi zestawami danych.

Implementacja usługi HDInsight używa architektury skalowalnej w poziomie programu HBase w celu zapewnienia automatycznej fragmentowania tabel. Oraz silna spójność odczytu i zapisu oraz automatyczne przełączanie do trybu failover. Wydajność jest zwiększona dzięki buforowaniu w pamięci operacji odczytu i przesyłaniu strumieniowemu o wysokiej przepustowości obejmującemu operacje zapisu. Klaster bazy danych HBase można utworzyć w sieci wirtualnej. Aby uzyskać szczegółowe informacje, zobacz temat [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Tworzenie klastrów usługi HDInsight w usłudze Azure Virtual Network).

## <a name="how-is-data-managed-in-hdinsight-hbase"></a>W jaki sposób zarządzane są dane w bazie danych HBase w usłudze HDInsight?

W bazie danych HBase można zarządzać danymi za pomocą poleceń `create`, `get`, `put` i `scan` z poziomu powłoki HBase. Dane są zapisywane w bazie danych przy użyciu polecenia `put` i odczytywane przy użyciu polecenia `get`. Polecenie `scan` jest używane do uzyskiwania danych z wielu wierszy w tabeli. Danymi można również zarządzać przy użyciu interfejsu API w języku C# bazy danych HBase, który udostępnia bibliotekę klienta ponad interfejsem API REST HBase. Do bazy danych HBase można także wykonywać zapytania przy użyciu [Apache Hive](https://hive.apache.org/). Aby zapoznać się z wprowadzeniem do tych modeli programowania, zobacz Wprowadzenie do korzystania z platformy [Apache HBase z Apache Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md). Współprocesory są również dostępne, które umożliwiają przetwarzanie danych w węzłach, które obsługują bazę danych.

> [!NOTE]  
> Platforma Thrift nie jest obsługiwana przez bazę danych HBase w usłudze HDInsight.

## <a name="use-cases-for-apache-hbase"></a>Przypadki użycia dla Apache HBase

Kanoniczny przypadek użycia, dla którego utworzono BigTable (i rozszerzenie HBase) z wyszukiwania w sieci Web. Aparaty wyszukiwania tworzą indeksy, które mapują terminy na zawierające je strony sieci Web. Istnieje jednak wiele innych przypadków użycia, w których baza danych HBase jest przydatna — niektóre z nich są wymienione w tej sekcji.

|Scenariusz |Opis |
|---|---|
|Magazyn wartości klucza|HBase można używać jako magazynu klucz-wartość i są odpowiednie do zarządzania systemami komunikatów. Serwis Facebook używa usługi HBase do obsługi komunikatów i jest idealnym rozwiązaniem do przechowywania komunikacji z Internetem i zarządzania nią. Usługa WebTable używa bazy danych HBase do wyszukiwania tabel wyodrębnianych ze stron sieci Web oraz zarządzania nimi.|
|Dane czujników|Baza danych HBase może służyć do przechwytywania danych gromadzonych przyrostowo z różnych źródeł. Te dane obejmują analizę społecznościową i szeregi czasowe. I utrzymywanie interaktywnych pulpitów nawigacyjnych na bieżąco z trendami i licznikami oraz zarządzanie systemami dziennika inspekcji. Przykłady obejmują Terminal Bloomberg handlowca i otwartą bazę danych szeregów czasowych (OpenTSDB). OpenTSDB przechowuje i zapewnia dostęp do metryk zbieranych na temat kondycji systemów serwerowych.|
|Zapytania w czasie rzeczywistym|[Apache Phoenix](https://phoenix.apache.org/) jest aparatem zapytań SQL dla platformy Apache HBase. Jest on dostępny jako sterownik JDBC i umożliwia wykonywanie zapytań i Zarządzanie tabelami HBase przy użyciu języka SQL.|
|HBase jako platforma|Aplikacje mogą działać w bazie danych HBase, wykorzystując ją jako magazyn danych. Przykładami mogą być Phoenix, OpenTSDB, `Kiji` i Titan. Aplikacje można również integrować z bazą danych HBase. Przykłady obejmują: [Apache Hive](https://hive.apache.org/), Apache chlewnej, [Solr](https://lucene.apache.org/solr/), Apache Storm, Apache Flume, [Apache Impala](https://impala.apache.org/), Apache Spark `Ganglia` i Apache drążenie.|

## <a name="next-steps"></a>Następne kroki

* [Rozpoczynanie pracy z usługą Apache HBase z usługą Apache Hadoop w usłudze HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Create HDInsight clusters on Azure Virtual Network](./apache-hbase-provision-vnet.md) (Tworzenie klastrów usługi HDInsight w usłudze Azure Virtual Network)
* [Konfigurowanie replikacji Apache HBase w usłudze HDInsight](apache-hbase-replication.md)
