---
title: Przesyłanie strumieniowe na dużą skalę w usłudze Azure HDInsight
description: Jak używać przesyłania strumieniowego danych przy użyciu skalowalnych klastrów Apache w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 3bf036ff413f720f54deef1a62b502668d1d8ac6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872000"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Przesyłanie strumieniowe na dużą skalę w usłudze HDInsight

Rozwiązania do obsługi dużych ilości danych w czasie rzeczywistym działają na danych, które są w ruchu. Zazwyczaj te dane są najbardziej cenne w momencie przybycia. Jeśli strumień danych przychodzących stanie się większy niż może być obsługiwany w tym momencie, może być konieczne ograniczenie zasobów. Alternatywnie klaster usługi HDInsight można skalować w górę, aby spełniał Twoje rozwiązanie przesyłania strumieniowego, dodając węzły na żądanie.

W aplikacji przesyłania strumieniowego co najmniej jedno źródło danych generuje zdarzenia (czasami w milionach na sekundę), które należy szybko pozyskiwać bez porzucania jakichkolwiek użytecznych informacji. Zdarzenia przychodzące są obsługiwane za pomocą *buforowania strumienia*, nazywanego również *kolejką zdarzeń*, przez usługę, taką jak [Apache Kafka](kafka/apache-kafka-introduction.md) lub [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Po zebraniu zdarzeń można analizować dane przy użyciu systemu analizy w czasie rzeczywistym w ramach warstwy *przetwarzania strumienia* , takiej jak [Apache Storm](storm/apache-storm-overview.md) lub [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). Przetworzone dane mogą być przechowywane w systemach przechowywania długoterminowego, takich jak [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/)i wyświetlane w czasie rzeczywistym na pulpicie nawigacyjnym analizy biznesowej, takim jak [Power BI](https://powerbi.microsoft.com), Tableau lub niestandardowa strona sieci Web.

:::image type="content" source="./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png" alt-text="Wzorce przesyłania strumieniowego usługi Azure HDInsight":::

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka zapewnia usługę kolejkowania komunikatów o wysokiej przepływności i małych opóźnieniach, a teraz jest częścią pakietu Apache w oprogramowaniu typu Open Source (OSS). Kafka używa modelu obsługi komunikatów Publikuj i Subskrybuj i przechowuje strumienie danych partycjonowanych bezpiecznie w rozproszonym, replikowanym klastrze. Kafka skaluje się liniowo w miarę wzrostu przepływności.

Aby uzyskać więcej informacji, zobacz [wprowadzenie Apache Kafka w usłudze HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm to rozproszony, odporny na uszkodzenia system obliczeniowy typu open source, który jest zoptymalizowany pod kątem przetwarzania strumieni danych w czasie rzeczywistym za pomocą usługi Hadoop. Podstawową jednostką danych dla zdarzenia przychodzącego jest krotka, która jest niezmiennym zestawem par klucz/wartość. Nieograniczona sekwencja tych krotek tworzy strumień, który pochodzi z elementu Spout. Elementu Spout otacza źródło danych przesyłania strumieniowego (np. Kafka) i emituje krotki. Topologia burzy to sekwencja transformacji dla tych strumieni.

Aby uzyskać więcej informacji, zobacz [co to jest Apache Storm w usłudze Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Przetwarzanie strumieniowe Spark to rozszerzenie platformy Spark, które umożliwia ponowne użycie tego samego kodu, który jest używany do przetwarzania wsadowego. W tej samej aplikacji można łączyć zarówno zapytania wsadowe, jak i interakcyjne. W przeciwieństwie do burzy, przesyłanie strumieniowe w usłudze Spark zapewnia bezstanowe przetwarzanie semantyki. Gdy jest używany w połączeniu z [interfejsem API Direct Kafka](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), który zapewnia, że wszystkie dane Kafka są odbierane przez funkcję przesyłania strumieniowego usługi Spark dokładnie jeden raz, możliwe jest osiągnięcie kompleksowych danych. Jedną z mocnych funkcji przesyłania strumieniowego platformy Spark jest funkcja odporna na uszkodzenia, która umożliwia szybkie odzyskiwanie węzłów o błędach w przypadku używania wielu węzłów w klastrze.

Aby uzyskać więcej informacji, zobacz [co to jest Apache Spark Streaming?](./spark/apache-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Skalowanie klastra

Chociaż można określić liczbę węzłów w klastrze podczas tworzenia, można zwiększyć lub zmniejszyć klaster w celu dopasowania do obciążenia. Wszystkie klastry usługi HDInsight umożliwiają [zmianę liczby węzłów w klastrze](hdinsight-administer-use-portal-linux.md#scale-clusters). Klastry Spark można porzucić bez utraty danych, ponieważ wszystkie dane są przechowywane w usłudze Azure Storage lub Data Lake Storage.

Istnieją zalety oddzielania technologii. Na przykład Kafka jest technologią buforowania zdarzeń, więc bardzo intensywnie korzysta z operacji we/wy i nie wymaga dużej mocy obliczeniowej. W porównaniu z procesorami strumieniowymi, takimi jak Spark streaming, są duże obciążenia, które wymagają bardziej zaawansowanych maszyn wirtualnych. Dzięki wykorzystaniu tych technologii w różnych klastrach można je skalować niezależnie przy użyciu maszyn wirtualnych.

### <a name="scale-the-stream-buffering-layer"></a>Skalowanie warstwy buforowania strumienia

Technologie buforowania strumienia Event Hubs i Kafka obu używają partycji, a konsumenci odczytują te partycje. Skalowanie przepływności danych wejściowych wymaga skalowania w górę liczby partycji, a Dodawanie partycji umożliwia zwiększenie równoległości. W Event Hubs nie można zmienić liczby partycji po wdrożeniu, dlatego ważne jest, aby zacząć od docelowej skali. Za pomocą Kafka można [dodawać partycje](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)nawet wtedy, gdy Kafka przetwarza dane. Kafka udostępnia narzędzie do ponownego przypisywania partycji  `kafka-reassign-partitions.sh` . Usługa HDInsight udostępnia [Narzędzie ponownego równoważenia repliki partycji](https://github.com/hdinsight/hdinsight-kafka-tools),  `rebalance_rackaware.py` . To narzędzie do ponownego równoważenia wywołuje `kafka-reassign-partitions.sh` Narzędzie w taki sposób, że każda replika znajduje się w oddzielnej domenie błędów i domenie aktualizacji, co sprawia, że Kafka stojaka i zwiększa odporność na uszkodzenia.

### <a name="scale-the-stream-processing-layer"></a>Skalowanie warstwy przetwarzania strumienia

Zarówno Apache Storm, jak i obsługa przesyłania strumieniowego Spark Dodawanie węzłów procesu roboczego do swoich klastrów, nawet podczas przetwarzania danych.

Aby skorzystać z nowych węzłów dodanych poprzez skalowanie burzy, należy ponownie zrównoważyć wszystkie topologie burzy uruchomione przed zwiększeniem rozmiaru klastra. To ponowne zrównoważenie można wykonać za pomocą interfejsu użytkownika sieci Web burzy lub jego interfejsu wiersza polecenia. Aby uzyskać więcej informacji, zobacz [dokumentację Apache Storm](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark używa trzech kluczowych parametrów do konfigurowania środowiska, w zależności od wymagań aplikacji: `spark.executor.instances` , `spark.executor.cores` i `spark.executor.memory` . Program *wykonujący* to proces, który jest uruchamiany dla aplikacji Spark. Program wykonujący działa w węźle procesu roboczego i jest odpowiedzialny za wykonywanie zadań aplikacji. Domyślna liczba modułów wykonujących i rozmiary wykonawców dla każdego klastra są obliczane na podstawie liczby węzłów procesu roboczego i rozmiaru węzła procesu roboczego. Te liczby są przechowywane w `spark-defaults.conf` pliku w każdym węźle głównym klastra.

Te trzy parametry można skonfigurować na poziomie klastra dla wszystkich aplikacji uruchamianych w klastrze, a także dla każdej pojedynczej aplikacji. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami dla klastrów Apache Spark](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>Następne kroki

* [Tworzenie i monitorowanie topologii Apache Storm w usłudze Azure HDInsight](storm/apache-storm-quickstart.md)
* [Przykładowe topologie dla systemu Apache Storm w usłudze HDInsight](storm/apache-storm-example-topology.md)
* [Wprowadzenie do Apache Spark w usłudze HDInsight](spark/apache-spark-overview.md)
* [Wprowadzenie do platformy Apache Kafka w usłudze HDInsight](kafka/apache-kafka-get-started.md)