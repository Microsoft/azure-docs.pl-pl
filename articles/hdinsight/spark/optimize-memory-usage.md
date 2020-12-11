---
title: Optymalizowanie użycia pamięci w Apache Spark — usługa Azure HDInsight
description: Dowiedz się, jak zoptymalizować użycie pamięci w Apache Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: ed10648fb115ae0dc12300f3e48db165fcb4c215
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028159"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Optymalizacja użycia pamięci dla Apache Spark

W tym artykule omówiono sposób optymalizowania zarządzania pamięcią klastra Apache Spark w celu uzyskania najlepszej wydajności w usłudze Azure HDInsight.

## <a name="overview"></a>Omówienie

Platforma Spark działa przez umieszczenie danych w pamięci. Zarządzanie zasobami pamięci jest kluczowym aspektem optymalizacji wykonywania zadań platformy Spark.  Istnieje kilka technik, które można zastosować do wydajnego używania pamięci klastra.

* Preferuj mniejsze partycje danych i konta na potrzeby rozmiaru danych, typów i dystrybucji w strategii partycjonowania.
* Weź pod uwagę nowszą, wydajniejszą [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) , a nie domyślną serializację języka Java.
* Preferuj używanie PRZĘDZy, która jest oddzielana `spark-submit` przez usługę Batch.
* Monitorowanie i dostrajanie ustawień konfiguracji platformy Spark.

Dla odwołania, struktura pamięci platformy Spark i niektóre parametry pamięci modułu wykonującego klucze są wyświetlane na następnym obrazie.

## <a name="spark-memory-considerations"></a>Uwagi dotyczące pamięci platformy Spark

Jeśli używasz Apache Hadoop PRZĘDZy, PRZĘDZa kontroluje pamięć używaną przez wszystkie kontenery w każdym węźle Spark.  Na poniższym diagramie przedstawiono obiekty kluczowe i ich relacje.

![Zarządzanie pamięcią w ramach PRZĘDZy](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Aby rozwiązać komunikaty o braku pamięci, spróbuj wykonać następujące działania:

* Przejrzyj DAGe w celu zarządzania nimi. Zmniejszaj dane źródłowe, które znajdują się na dysku (lub dzielenia), Maksymalizuj, przełączając pojedyncze losowo i zmniejszając ilość wysyłanych danych.
* Preferuj `ReduceByKey` ze stałym limitem pamięci na `GroupByKey` , który zapewnia agregacje, okna i inne funkcje, ale ma limit pamięci nieograniczonej Ann.
* Preferuj `TreeReduce` , która wykonuje więcej pracy na wszystkich wykonawcach lub partycjach, do `Reduce` , które działają na sterowniku.
* Używaj ramek dataframes zamiast obiektów RDD niskiego poziomu.
* Utwórz ComplexType, które hermetyzują akcje, takie jak "pierwsze N", różne agregacje lub operacje okienkowe.

Dodatkowe kroki rozwiązywania problemów można znaleźć [w temacie OutOfMemoryError Exceptions for Apache Spark in Azure HDInsight](apache-spark-troubleshoot-outofmemory.md).

## <a name="next-steps"></a>Następne kroki

* [Optymalizowanie przetwarzania danych dla Apache Spark](optimize-cluster-configuration.md)
* [Optymalizuj magazyn danych dla Apache Spark](optimize-data-storage.md)
* [Optymalizuj konfigurację klastra dla Apache Spark](optimize-cluster-configuration.md)
