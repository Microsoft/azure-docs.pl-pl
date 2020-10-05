---
title: Co to są Apache Hadoop i MapReduce — Azure HDInsight
description: Wprowadzenie do usługi HDInsight oraz stos i składniki technologii Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 5e5f02b1684e56496778ab677aa9dc46e7dcd9aa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87086527"
---
# <a name="what-is-apache-hadoop-in-azure-hdinsight"></a>Co to jest Apache Hadoop w usłudze Azure HDInsight?

[Apache Hadoop](https://hadoop.apache.org/) to oryginalna struktura typu „open source” do przetwarzania rozproszonego i analizy zestawów danych big data w klastrach. Ekosystem usługi Hadoop obejmuje powiązane oprogramowanie i narzędzia, w tym Apache Hive, Apache HBase, Spark, Kafka i wiele innych.

Azure HDInsight to w pełni zarządzana usługa analizy typu open source w chmurze dla przedsiębiorstw. Typ klastra Apache Hadoop w usłudze Azure HDInsight umożliwia korzystanie z systemu HDFS, zarządzanie zasobami PRZĘDZy oraz prosty model programowania MapReduce do równoległego przetwarzania i analizowania danych wsadowych.

Aby wyświetlić dostępne składniki stosu technologii Hadoop w usłudze HDInsight, zobacz [Components and versions available with HDInsight](../hdinsight-component-versioning.md) (Składniki i wersje dostępne w usłudze HDInsight). Aby dowiedzieć się więcej o platformie Hadoop w usłudze HDInsight, zobacz [stronę z opisem funkcji platformy Azure w usłudze HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="what-is-mapreduce"></a>Co to jest MapReduce

Apache Hadoop MapReduce jest platformą oprogramowania do pisania zadań, które przetwarzają ogromną ilość danych. Dane wejściowe są dzielone na niezależne fragmenty. Każdy fragment jest przetwarzany równolegle między węzłami w klastrze. Zadanie MapReduce składa się z dwóch funkcji:

* **Maper**: wykorzystuje dane wejściowe, analizuje je (zazwyczaj z operacjami filtrowania i sortowania) i emituje krotki (pary klucz-wartość)

* **Redukcja**: wykorzystuje krotki emitowane przez mapowanie i wykonuje operację podsumowującą, która tworzy mniejszy, połączony wynik z danych mapowania

Przykład podstawowego zadania MapReduce zliczania wyrazów przedstawiono na poniższym diagramie:

 ![HDI. WordCountDiagram](./media/apache-hadoop-introduction/hdi-word-count-diagram.gif)

Dane wyjściowe tego zadania to liczba, ile razy każdy wyraz pojawił się w tekście.

* Maper pobiera każdy wiersz z tekstu wejściowego jako dane wejściowe i dzieli go na słowa. Emituje parę klucz/wartość za każdym razem, gdy wyraz wypada wyraz, po którym następuje 1. Dane wyjściowe są sortowane przed wysłaniem go do programu do redukcji.
* Zmniejszenie sumuje te pojedyncze zliczenia dla każdego wyrazu i emituje pojedynczą parę klucz/wartość zawierającą wyraz, a następnie sumę jego wystąpień.

MapReduce można zaimplementować w różnych językach. Język Java jest najpopularniejszą implementacją i jest używany do celów demonstracyjnych w tym dokumencie.

## <a name="development-languages"></a>Języki programistyczne

Języki lub struktury oparte na języku Java i wirtualna maszyna Java mogą być uruchamiane bezpośrednio jako zadanie MapReduce. Przykładem używanym w tym dokumencie jest aplikacja Java MapReduce. W językach innych niż Java, takich jak C#, Python lub autonomiczne pliki wykonywalne, należy użyć **przesyłania strumieniowego Hadoop**.

Przesyłanie strumieniowe w usłudze Hadoop komunikuje się z mapowaniem i zmniejszeniem za pośrednictwem STDIN i STDOUT. Mapowanie i zmniejszenie danych są odczytywane wierszowo z STDIN i zapisywane dane wyjściowe do STDOUT. Każdy wiersz odczytywany lub emitowany przez mapowanie i zmniejszenie musi być w formacie pary klucz/wartość, rozdzielony znakiem tabulacji:

`[key]\t[value]`

Aby uzyskać więcej informacji, zobacz Usługa [przesyłania strumieniowego Hadoop](https://hadoop.apache.org/docs/current/hadoop-streaming/HadoopStreaming.html).

Przykłady korzystania z przesyłania strumieniowego Hadoop za pomocą usługi HDInsight można znaleźć w następującym dokumencie:

* [Opracowywanie zadań MapReduce języka C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie klastra Apache Hadoop w usłudze HDInsight](apache-hadoop-linux-create-cluster-get-started-portal.md)
