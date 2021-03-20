---
title: Informacje o formatach Apache Spark danych dla deweloperów Azure Data Lake Analytics U-SQL.
description: W tym artykule opisano Apache Spark pojęć, które ułatwiają U_SQL deweloperom zrozumienie różnic między formatami danych U-SQL i Spark.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 399914186ce9de62ef46b682c8d4a6e51426cc26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221115"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Zrozumienie różnic między formatami danych U-SQL i Spark

Jeśli chcesz użyć obu [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) lub [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md), zalecamy przeprowadzenie migracji danych z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Oprócz przechodzenia plików, warto również udostępnić dane przechowywane w tabelach U-SQL dostępnych dla platformy Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Przenoszenie danych przechowywanych w plikach Azure Data Lake Storage Gen1

Dane przechowywane w plikach można przenosić na różne sposoby:

- Napisz potok [Azure Data Factory](../data-factory/introduction.md) , aby skopiować dane z konta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do konta [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) .
- Napisz zadanie Spark, które odczytuje dane z konta [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) i zapisuje je na koncie [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . Na podstawie Twojego przypadku użycia możesz chcieć napisać go w innym formacie, takim jak Parquet, jeśli nie ma potrzeby zachowywania oryginalnego formatu pliku.

Zalecamy zapoznanie się z artykułem [Uaktualnianie rozwiązań do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Przenoszenie danych przechowywanych w tabelach U-SQL

Tabele U-SQL nie są rozpoznawane przez platformę Spark. Jeśli przechowujesz dane w tabelach U-SQL, uruchomisz zadanie U-SQL, które wyodrębnia dane tabeli i zapisuje je w formacie, który jest rozpoznawany przez platformę Spark. Najbardziej odpowiednim formatem jest utworzenie zestawu plików Parquet po układ folderu magazyn metadanych Hive.

Dane wyjściowe można osiągnąć w języku U-SQL za pomocą wbudowanego wypełniania Parquet oraz przy użyciu dynamicznego partycjonowania danych wyjściowych z zestawami plików w celu utworzenia folderów partycji. [Przetwarzaj więcej plików niż kiedykolwiek, a korzystanie z Parquet](/archive/blogs/azuredatalake/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) zawiera przykład sposobu tworzenia takich danych.

Po wykonaniu tej transformacji dane są kopiowane, jak opisano w rozdziale [przenoszenie danych przechowywanych w plikach Azure Data Lake Storage Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files).

## <a name="caveats"></a>Zastrzeżenia

- Semantyka danych podczas kopiowania plików kopia będzie miała miejsce na poziomie bajtów. Dlatego te same dane powinny być wyświetlane na koncie [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) . Należy jednak zauważyć, że platforma Spark może interpretować niektóre znaki inaczej. Na przykład w pliku CSV może być używana inna wartość domyślna dla ogranicznika wiersza.
    Ponadto, jeśli kopiujesz dane wpisane (z tabel), Parquet i Spark mogą mieć różną precyzję i skalę dla niektórych wartości z określonym typem (na przykład zmiennoprzecinkowych) i mogą traktować wartości null inaczej. Na przykład U-SQL ma semantykę języka C# dla wartości null, podczas gdy platforma Spark ma logikę o wartości null.

- Organizacja danych (partycjonowanie) tabele U-SQL zapewniają partycjonowanie na poziomie dwóch poziomów. Poziom zewnętrzny ( `PARTITIONED BY` ) ma wartość i jest mapowany głównie do schematu partycjonowania Hive/Spark przy użyciu hierarchii folderów. Musisz się upewnić, że wartości null są mapowane do właściwego folderu. Wewnętrzny poziom ( `DISTRIBUTED BY` ) w programie U-SQL oferuje 4 schematy dystrybucji: działania okrężne, zakres, mieszanie i mieszanie bezpośrednie.
    Tabele Hive/Spark obsługują tylko partycjonowanie i partycjonowanie skrótów przy użyciu innej funkcji mieszania niż U-SQL. Podczas wyprowadzania danych z tabeli U-SQL prawdopodobnie będziesz mieć możliwość mapowania na partycjonowanie wartości dla platformy Spark i może być konieczne przeprowadzenie dalszej strojenia układu danych w zależności od końcowych zapytań platformy Spark.

## <a name="next-steps"></a>Następne kroki

- [Zrozumienie koncepcji kodu platformy Spark dla deweloperów U-SQL](understand-spark-code-concepts.md)
- [Uaktualnij rozwiązania do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)
- [Platforma .NET for Apache Spark](/dotnet/spark/what-is-apache-spark-dotnet)
- [Przekształcanie danych przy użyciu działania Spark w Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Przekształcanie danych przy użyciu działania programu Hive platformy Hadoop w Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Co to jest platforma Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)