---
title: Co to jest Apache Hadoop i MapReduce — Azure HDInsight
description: Wprowadzenie do usługi HDInsight oraz stos i składniki technologii Apache Hadoop.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 1e3fc23dd220a8d9764a64c453e9fb856307ac47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946518"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co to jest Apache Hadoop w usłudze Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) to oryginalna struktura typu „open source” do przetwarzania rozproszonego i analizy zestawów danych big data w klastrach. Ekosystem usługi Hadoop obejmuje powiązane oprogramowanie i narzędzia, w tym Apache Hive, Apache HBase, Spark, Kafka i wiele innych.

Azure HDInsight to w pełni zarządzana usługa analizy typu open source w chmurze dla przedsiębiorstw. Typ klastra Apache Hadoop w usłudze Azure HDInsight umożliwia korzystanie z [Apache Hadoop rozproszony system plików (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html), Apache Hadoop zarządzanie zasobami [przędzy](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) i prosty model programowania [MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) do równoległego przetwarzania i analizowania danych wsadowych.  Klastry Hadoop w usłudze HDInsight są zgodne z [usługą Azure Blob Storage](../../storage/common/storage-introduction.md), [Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-overview.md)lub [Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md).

Aby wyświetlić dostępne składniki stosu technologii Hadoop w usłudze HDInsight, zobacz [Components and versions available with HDInsight](../hdinsight-component-versioning.md) (Składniki i wersje dostępne w usłudze HDInsight). Aby dowiedzieć się więcej o platformie Hadoop w usłudze HDInsight, zobacz [stronę z opisem funkcji platformy Azure w usłudze HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Co to jest MapReduce

[Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) jest platformą oprogramowania do pisania zadań, które przetwarzają ogromną ilość danych. Dane wejściowe są dzielone na niezależne fragmenty. Każdy fragment jest przetwarzany równolegle między węzłami w klastrze. Zadanie MapReduce składa się z dwóch funkcji:

* **Maper**: wykorzystuje dane wejściowe, analizuje je (zazwyczaj z operacjami filtrowania i sortowania) i emituje krotki (pary klucz-wartość)

* **Redukcja**: wykorzystuje krotki emitowane przez mapowanie i wykonuje operację podsumowującą, która tworzy mniejszy, połączony wynik z danych mapowania

Przykład podstawowego zadania MapReduce zliczania wyrazów przedstawiono na poniższym diagramie:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Dane wyjściowe tego zadania to liczba, ile razy każdy wyraz pojawił się w tekście.

* Maper pobiera każdy wiersz z tekstu wejściowego jako dane wejściowe i dzieli go na słowa. Emituje parę klucz/wartość za każdym razem, gdy wyraz wypada wyraz, po którym następuje 1. Dane wyjściowe są sortowane przed wysłaniem go do programu do redukcji.
* Zmniejszenie sumuje te pojedyncze zliczenia dla każdego wyrazu i emituje pojedynczą parę klucz/wartość zawierającą wyraz, a następnie sumę jego wystąpień.

MapReduce można zaimplementować w różnych językach. Język Java jest najpopularniejszą implementacją i jest używany do celów demonstracyjnych w tym dokumencie.

## <a name="development-languages"></a>Języki programistyczne

Języki lub struktury oparte na języku Java i wirtualna maszyna Java mogą być uruchamiane bezpośrednio jako [zadanie MapReduce](..//hadoop/submit-apache-hadoop-jobs-programmatically.md). Przykładem używanym w tym dokumencie jest aplikacja Java MapReduce. W językach innych niż Java, takich jak C#, Python lub autonomiczne pliki wykonywalne, należy użyć **przesyłania strumieniowego Hadoop**.

Przesyłanie strumieniowe w usłudze Hadoop komunikuje się z mapowaniem i zmniejszeniem za pośrednictwem STDIN i STDOUT. Mapowanie i zmniejszenie danych są odczytywane wierszowo z STDIN i zapisywane dane wyjściowe do STDOUT. Każdy wiersz odczytywany lub emitowany przez mapowanie i zmniejszenie musi być w formacie pary klucz/wartość, rozdzielony znakiem tabulacji:

`[key]\t[value]`

Aby uzyskać więcej informacji, zobacz Usługa [przesyłania strumieniowego Hadoop](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Przykłady korzystania z przesyłania strumieniowego Hadoop za pomocą usługi HDInsight można znaleźć w następującym dokumencie:

* [Opracowywanie zadań MapReduce języka C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="where-do-i-start"></a>Od czego zacząć

* [Szybki Start: Tworzenie klastra Apache Hadoop w usłudze Azure HDInsight przy użyciu Azure Portal](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Samouczek: przesyłanie zadań Apache Hadoop w usłudze HDInsight](../hadoop/submit-apache-hadoop-jobs-programmatically.md)
* [Opracowywanie programów MapReduce w języku Java dla usługi Apache Hadoop w usłudze HDInsight](../hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Użyj Apache Hive jako narzędzia wyodrębniania, przekształcania i ładowania (ETL)](../hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md)
* [Wyodrębnianie, przekształcanie i ładowanie (ETL) na dużą skalę](../hadoop/apache-hadoop-etl-at-scale.md)
* [Operacjonalizacja potoku analizy danych](../hdinsight-operationalize-data-pipeline.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra Apache Hadoop w usłudze HDInsight przy użyciu portalu](../hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
* [Tworzenie klastra Apache Hadoop w usłudze HDInsight przy użyciu szablonu usługi ARM](../hadoop/apache-hadoop-linux-tutorial-get-started.md)