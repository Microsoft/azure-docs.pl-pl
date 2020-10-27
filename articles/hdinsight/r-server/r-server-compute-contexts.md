---
title: Opcje kontekstu obliczeniowego dla usług ML w usłudze HDInsight — Azure
description: Dowiedz się więcej na temat różnych opcji kontekstu obliczeniowego dostępnych dla użytkowników korzystających z usług ML w usłudze HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 21781015aa91c9c953d716b9b3399851f25be9b5
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92536338"
---
# <a name="compute-context-options-for-ml-services-on-hdinsight"></a>Opcje kontekstu obliczeniowego dla usług ML w usłudze HDInsight

Usługi ML w usłudze Azure HDInsight kontrolują sposób wykonywania wywołań przez ustawienie kontekstu obliczeniowego. W tym artykule opisano opcje, które są dostępne, aby określić, czy i jak wykonywanie jest równoległe między rdzeniami węzła lub klastra usługi HDInsight.

Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. Węzeł brzegowy umożliwia uruchamianie równoległych funkcji rozproszonych kolekcję funkcji revoscaler na różnych rdzeniach serwera węzła brzegowego. Można je również uruchamiać w węzłach klastra przy użyciu mapy usługi Hadoop w usłudze kolekcję funkcji revoscaler, zmniejszając lub Apache Spark konteksty obliczeniowe.

## <a name="ml-services-on-azure-hdinsight"></a>Usługi w usłudze ML w usłudze Azure HDInsight

[Usługi ml w usłudze Azure HDInsight](r-server-overview.md) zapewniają najnowsze możliwości analityczne na podstawie języka R. Dane przechowywane w kontenerze Apache Hadoop HDFS można używać na koncie [usługi Azure Blob](../../storage/common/storage-introduction.md "Usługa Azure Blob Storage") storage, Data Lake Store lub lokalnym systemie plików systemu Linux. Ze względu na to, że usługa ML jest oparta na języku R typu "open source", tworzone aplikacje oparte na języku R mogą stosować dowolne z pakietów języka R typu "open source". Mogą również korzystać z procedur w [kolekcję funkcji revoscaler](/machine-learning-server/r-reference/revoscaler/revoscaler), pakietu do analizy danych Big Data, który jest dołączony do usługi ml.  

## <a name="compute-contexts-for-an-edge-node"></a>Konteksty obliczeniowe dla węzła krawędzi

Ogólnie rzecz biorąc, skrypt języka R uruchamiany w klastrze usługi ML w węźle brzegowym jest uruchamiany w ramach interpretera języka R w tym węźle. Wyjątkami są te kroki, które wywołują funkcję kolekcję funkcji revoscaler. Wywołania kolekcję funkcji revoscaler są uruchamiane w środowisku obliczeniowym, który jest określany przez ustawienie kontekstu obliczeniowego kolekcję funkcji revoscaler.  Po uruchomieniu skryptu języka R z węzła brzegowego możliwe wartości kontekstu obliczeniowego są następujące:

- lokalna sekwencyjna ( *lokalna* )
- lokalna Parallel ( *localpar* )
- Zmniejszenie mapy
- platforma Spark

Opcje *lokalne* i *localpar* różnią się tylko sposobem wykonywania wywołań **rxExec** . Oba wywołania funkcji RX są wykonywane równolegle przez wszystkie dostępne rdzenie, chyba że określono inaczej przy użyciu opcji kolekcję funkcji revoscaler **numCoresToUse** , na przykład `rxOptions(numCoresToUse=6)` . Opcje wykonywania równoległego zapewniają optymalną wydajność.

Poniższa tabela zawiera podsumowanie różnych opcji kontekstu obliczeniowego w celu ustawienia sposobu wykonywania wywołań:

| Kontekst obliczeniowy  | Jak ustawić                      | Kontekst wykonywania                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Lokalna sekwencyjna | rxSetComputeContext ("Local")    | Równoległe wykonywanie na rdzeniach serwera węzła brzegowego, z wyjątkiem wywołań rxExec, które są wykonywane sekwencyjnie |
| Lokalna Parallel   | rxSetComputeContext('localpar') | Równoległe wykonywanie na rdzeniach serwera węzła brzegowego |
| platforma Spark            | Obliczeniowego rxspark ()                       | Równoległe wykonywanie rozproszone za pośrednictwem platformy Spark w węzłach klastra HDI |
| Zmniejszenie mapy       | RxHadoopMR()                    | Równoległe wykonywanie rozproszone za pośrednictwem mapy zmniejsza się między węzłami klastra HDI |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Wskazówki dotyczące podejmowania decyzji dotyczących kontekstu obliczeniowego

Spośród trzech wybranych opcji, które zapewniają równoległe wykonywanie, zależy od rodzaju pracy z analizą, rozmiaru i lokalizacji danych. Nie istnieje prosta formuła informująca o tym, który kontekst obliczeń ma być używany. Istnieją jednak pewne zasady dotyczące identyfikatorów, które mogą pomóc w dokonaniu właściwego wyboru lub, co najmniej, pomóc w zawężaniu wybranych opcji przed uruchomieniem testu porównawczego. Te zasady dotyczące identyfikatorów są następujące:

- Lokalny system plików Linux jest szybszy niż HDFS.
- Powtarzające się analizy są szybsze, jeśli dane są lokalne i są w XDF.
- Preferowane jest przesyłanie strumieniowe małych ilości danych ze źródła danych tekstowych. Jeśli ilość danych jest większa, przed rozpoczęciem analizy przekonwertuj ją na XDF.
- Narzuty kopiowania lub przesyłania strumieniowego danych do węzła brzegowego w celu analizy nie będzie można zarządzać dla bardzo dużych ilości danych.
- ApacheSpark jest szybsze niż zmniejszenie mapy w usłudze Hadoop.

Zgodnie z tymi zasadami następujące sekcje zawierają niektóre ogólne reguły dotyczące wyboru kontekstu obliczeniowego.

### <a name="local"></a>Lokalne

- Jeśli ilość danych do analizy jest mała i nie wymaga powtarzanej analizy, prześlij ją bezpośrednio do procedury analizy przy użyciu *lokalnego* lub *localpar* .
- Jeśli ilość danych do analizy jest mała lub średniej wielkości i wymaga przeprowadzenia ponownej analizy, skopiuj ją do lokalnego systemu plików, zaimportuj ją do XDF i Przeanalizuj ją za pośrednictwem *lokalnego* lub *localpar* .

### <a name="apache-spark"></a>Apache Spark

- Jeśli ilość danych do analizy jest duża, należy zaimportować ją do ramki Dataframe platformy Spark przy użyciu **RxHiveData** lub **RxParquetData** , lub do XDF w systemie plików HDFS (chyba że magazyn jest problemem) i przeanalizować go przy użyciu kontekstu obliczeniowego platformy Spark.

### <a name="apache-hadoop-map-reduce"></a>Zmniejszenie mapy Apache Hadoop

- Używaj mapy zmniejszaj kontekst obliczeń tylko wtedy, gdy wystąpi problem pokonania z kontekstem obliczeń Spark, ponieważ jest on ogólnie wolniejszy.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Wbudowana Pomoc dotycząca rxSetComputeContext
Aby uzyskać więcej informacji i przykłady kontekstów obliczeniowych kolekcję funkcji revoscaler, zobacz Pomoc wbudowaną w języku R na metodzie rxSetComputeContext, na przykład:

```console
> ?rxSetComputeContext
```

Możesz również zapoznać się z [omówieniem rozproszonego przetwarzania](/machine-learning-server/r/how-to-revoscaler-distributed-computing) danych w [dokumentacji Machine Learning Server](/machine-learning-server/).

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o opcjach, które są dostępne, aby określić, czy i jak wykonywanie jest równoległe między rdzeniami węzła lub klastra usługi HDInsight. Aby dowiedzieć się więcej na temat korzystania z usług w usłudze ML z klastrami usługi HDInsight, zobacz następujące tematy:

- [Omówienie usług ML dla Apache Hadoop](r-server-overview.md)
- [Opcje usługi Azure Storage dla usług ML w usłudze HDInsight](r-server-storage.md)