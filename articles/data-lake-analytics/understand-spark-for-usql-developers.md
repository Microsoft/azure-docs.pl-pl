---
title: Informacje o Apache Sparkch dla deweloperów Azure Data Lake Analytics U-SQL.
description: W tym artykule opisano Apache Spark pojęć, które ułatwiają różnice między programistami U-SQL.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 567574e65fcc1db3ef9e8aea73c6a59be0594f72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132300"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Informacje o platformie Apache Spark dla deweloperów języka U-SQL

Firma Microsoft obsługuje kilka usług analitycznych, takich jak [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) i [Azure HDInsight](../hdinsight/hdinsight-overview.md) , jak również Azure Data Lake Analytics. Wiemy, że wszyscy deweloperzy mają jasne preferencje dotyczące rozwiązań typu "open source", które tworzą potoki analizy. Aby pomóc deweloperom U-SQL zrozumieć Apache Spark i jak można przekształcić skrypty U-SQL na Apache Spark, utworzyliśmy te wskazówki.

Zawiera kilka czynności, które można wykonać, i wiele wariantów.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Procedura przekształcania języka U-SQL na Apache Spark

1. Przekształć potoki aranżacji zadań.

   Jeśli używasz [Azure Data Factory](../data-factory/introduction.md) do organizowania skryptów Azure Data Lake Analytics, musisz dostosować je, aby organizować nowe programy platformy Spark.
2. Zapoznaj się z różnicami między sposobem zarządzania danymi w języku U-SQL i Spark

   Jeśli chcesz przenieść dane z [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) do [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md), musisz skopiować zarówno dane plików, jak i katalog zachowane dane. Należy pamiętać, że Azure Data Lake Analytics obsługuje tylko Azure Data Lake Storage Gen1. Zobacz [Opis formatów danych platformy Spark](understand-spark-data-formats.md)
3. Przekształcanie skryptów U-SQL na platformę Spark

   Przed przeprowadzeniem transformacji skryptów U-SQL należy wybrać usługę analizy. Dostępne są następujące dostępne usługi obliczeniowe:
      - [Azure Data Factory przepływu danych](../data-factory/concepts-data-flow-overview.md) Mapowanie przepływów danych to wizualnie zaprojektowane przekształcenia danych, które umożliwiają inżynierom danych Tworzenie graficznej logiki transformacji danych bez pisania kodu. Chociaż nie jest to odpowiednie do wykonywania złożonych kodów użytkownika, mogą one łatwo przedstawiać tradycyjne przekształcenia przepływu danych, podobne do języka SQL
      - [Usługa Azure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive w usłudze HDInsight jest przystosowany do wyodrębniania, przekształcania i ładowania (ETL) operacji. Oznacza to, że zamierzasz przetłumaczyć skrypty U-SQL na Apache Hive.
      - Apache Spark aparaty, takie jak [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) lub [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) oznacza to, że zamierzasz przetłumaczyć skrypty U-SQL na platformę Spark. Aby uzyskać więcej informacji, zobacz [Omówienie formatów danych platformy Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Zarówno [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) , jak i [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) są usługami klastra i nie są zadaniami bezserwerowymi, takimi jak Azure Data Lake Analytics. Należy rozważyć sposób aprowizacji klastrów w celu uzyskania odpowiedniego współczynnika kosztu/wydajności oraz zarządzania ich okresem istnienia w celu zminimalizowania kosztów. Te usługi mają różne charakterystyki wydajności z kodem użytkownika napisanym w programie .NET, dlatego trzeba będzie zapisywać otoki lub napisać kod w obsługiwanym języku. Aby uzyskać więcej informacji, zobacz [Omówienie formatów danych platformy Spark](understand-spark-data-formats.md), [Opis Apache Spark pojęć dotyczących kodu dla deweloperów U-SQL](understand-spark-code-concepts.md), [.NET dla Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Następne kroki

- [Omówienie formatów danych platformy Spark dla deweloperów U-SQL](understand-spark-data-formats.md)
- [Zrozumienie koncepcji kodu platformy Spark dla deweloperów U-SQL](understand-spark-code-concepts.md)
- [Uaktualnij rozwiązania do analizy danych Big Data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Platforma .NET for Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Przekształcanie danych przy użyciu działania programu Hive platformy Hadoop w Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Przekształcanie danych przy użyciu działania Spark w Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Co to jest platforma Apache Spark w usłudze Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
