---
title: Wprowadzenie do platformy Apache Kafka w usłudze HDInsight — Azure
description: 'Uzyskaj informacje o platformie Apache Kafka w usłudze HDInsight: artykuł zawiera opis platformy, jej działania oraz informacje o tym, gdzie można znaleźć przykłady i informacje ułatwiające rozpoczęcie pracy.'
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: c6209ae985f8e59e1acae2d8fd6a1c821acae5a7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91449547"
---
# <a name="what-is-apache-kafka-in-azure-hdinsight"></a>Co to jest Apache Kafka w usłudze Azure HDInsight

[Apache Kafka](https://kafka.apache.org) to rozproszona platforma przesyłania strumieniowego typu „open source”, która umożliwia tworzenie aplikacji i potoków danych przesyłania strumieniowego w czasie rzeczywistym. Ponadto platforma Kafka oferuje funkcję brokera komunikatów, która działa podobnie do kolejki komunikatów. Pozwala ona publikować i subskrybować w nazwanych strumieniach danych.

Cechy charakterystyczne platformy Kafka w usłudze HDInsight są następujące:

* Jest to usługa zarządzana, która zapewnia uproszczony proces konfiguracji. W wyniku powstaje konfiguracja przetestowana i obsługiwana przez firmę Microsoft.

* Firma Microsoft gwarantuje 99,9% czasu sprawnego działania platformy Kafka zgodnie z Umową dotyczącą poziomu usług (SLA). Aby uzyskać więcej informacji, zobacz dokument [HDInsight — umowa SLA](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/).

* Jako magazynu zapasowego platforma Kafka używa funkcji Dyski zarządzane platformy Azure. Funkcja Dyski zarządzane zapewnia do 16 TB pamięci masowej dla każdego brokera platformy Kafka. Aby uzyskać informacje dotyczące konfigurowania dysków zarządzanych na platformie Kafka w usłudze HDInsight, zobacz [Increase scalability of Apache Kafka on HDInsight](apache-kafka-scalability.md) (Zwiększanie skalowalności platformy Apache Kafka w usłudze HDInsight).

    Aby uzyskać więcej informacji o funkcji Dyski zarządzane, zobacz artykuł [Funkcja Dyski zarządzane platformy Azure](../../virtual-machines/managed-disks-overview.md).

* Platforma Kafka została zaprojektowana jako jednowymiarowy widok regału. Platforma Azure rozdziela ten regał na dwa wymiary — domeny aktualizacji (UD) i domeny błędów (FD). Firma Microsoft udostępnia narzędzia, za pomocą których można ponownie zrównoważyć partycje i repliki platformy Kafka między domenami aktualizacji i błędów.

    Aby uzyskać więcej informacji, zobacz [High availability with Apache Kafka on HDInsight](apache-kafka-high-availability.md) (Wysoka dostępność na platformie Apache Kafka w usłudze HDInsight).

* Usługa HDInsight umożliwia zmianę liczby węzłów procesu roboczego (które hostują brokera platformy Kafka) po utworzeniu klastra. Skalowanie w górę można wykonać z poziomu Azure Portal, Azure PowerShell i innych interfejsów zarządzania platformy Azure. W przypadku platformy Kafka po wykonaniu operacji skalowania należy przeprowadzić ponowne równoważenie replik partycji. Ponowne równoważenie partycji na platformie Kafka umożliwia skorzystanie z nowej liczby węzłów procesu roboczego.

   Usługa HDInsight Kafka nie obsługuje skalowania w dół ani zmniejsza liczbę brokerów w klastrze. Jeśli podjęta zostanie próba zmniejszenia liczby węzłów, `InvalidKafkaScaleDownRequestErrorCode` zwracany jest błąd.

    Aby uzyskać więcej informacji, zobacz [High availability with Apache Kafka on HDInsight](apache-kafka-high-availability.md) (Wysoka dostępność na platformie Apache Kafka w usłudze HDInsight).

* Do monitorowania platformy Kafka w usłudze HDInsight można użyć dzienników usługi Azure Monitor. Dzienniki usługi Azure Monitor wydobywają informacje dotyczące maszyn wirtualnych, takie jak metryki dysków i kart sieciowych oraz metryki JMX z platformy Kafka.

    Aby uzyskać więcej informacji, zobacz [Analyze logs for Apache Kafka on HDInsight](apache-kafka-log-analytics-operations-management.md) (Analizowanie dzienników na potrzeby platformy Apache Kafka w usłudze HDInsight).

## <a name="apache-kafka-on-hdinsight-architecture"></a>Architektura platformy Apache Kafka w usłudze HDInsight

Poniższy diagram przedstawia typową konfigurację platformy Kafka korzystającą z grup konsumentów, partycjonowania i replikacji w celu zapewnienia równoległego odczytu zdarzeń przy zachowaniu odporności na uszkodzenia:

![Diagram konfiguracji klastra platformy Kafka](./media/apache-kafka-introduction/kafka-cluster-diagram.png)

Usługa Apache ZooKeeper zarządza stanem klastra platformy Kafka. Usługę Zookeeper zaprojektowano pod kątem obsługi jednoczesnych, odpornych transakcji o małych opóźnieniach.

Platforma Kafka przechowuje rekordy (dane) w **tematach**. Rekordy są tworzone przez **producentów** i używane przez **odbiorców**. Producenci wysyłają rekordy do **brokerów** platformy Kafka. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest brokerem platformy Kafka.

Tematy dzielą rekordy między brokerami. Podczas korzystania z rekordów można użyć maksymalnie jednego odbiorcy na jedną partycję, aby osiągnąć równoległe przetwarzanie danych.

Dzięki replikacji zapewniono duplikowanie partycji w węzłach, co gwarantuje ochronę przed awariami węzła (brokera). Partycja wiodąca jest oznaczona symbolem *(L)* na diagramie. Ruch producenta jest kierowany do partycji wiodącej w każdym węźle przy użyciu stanu zarządzanego przez usługę ZooKeeper.

## <a name="why-use-apache-kafka-on-hdinsight"></a>Dlaczego warto używać platformy Apache Kafka w usłudze HDInsight?

Poniżej przedstawiono typowe zadania i wzorce, które mogą być wykonywane przy użyciu platformy Kafka w usłudze HDInsight:

|Zastosowanie |Opis |
|---|---|
|Replikacja danych Apache Kafka|platforma Kafka udostępnia narzędzie MirrorMaker, które replikuje dane między klastrami Kafka. Aby uzyskać informacje na temat używania narzędzia MirrorMaker, zobacz [Replicate Apache Kafka topics with Apache Kafka on HDInsight](apache-kafka-mirroring.md) (Replikowanie tematów platformy Apache Kafka na platformie Apache Kafka w usłudze HDInsight).|
|Wzorzec komunikatów publikowania/subskrybowania|platforma Kafka udostępnia interfejs API producenta do publikowania rekordów w temacie platformy Kafka. Interfejs API klienta jest używany podczas subskrybowania tematu. Aby uzyskać więcej informacji, zobacz [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Apache Kafka w usłudze HDInsight).|
|Przetwarzanie strumienia|platforma Kafka jest często używana z systemem Apache Storm lub platformą Spark na potrzeby przetwarzania strumienia w czasie rzeczywistym. Na platformie Kafka 0.10.0.0 (usługa HDInsight w wersji 3.5 i 3.6) wprowadzono interfejs API przesyłania strumieniowego, który umożliwia tworzenie rozwiązań do transmisji strumieniowej bez konieczności stosowania rozwiązań Storm i Spark. Aby uzyskać więcej informacji, zobacz [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Apache Kafka w usłudze HDInsight).|
|Skalowanie w poziomie|platforma Kafka dzieli strumienie na partycje w różnych węzłach klastra usługi HDInsight. Procesy klientów mogą być kojarzone z poszczególnymi partycjami, aby umożliwić równoważenie obciążenia podczas korzystania z rekordów. Aby uzyskać więcej informacji, zobacz [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Apache Kafka w usłudze HDInsight).|
|Dostarczanie w określonej kolejności|rekordy na każdej partycji są przechowywane w strumieniu w tej samej kolejności, w której zostały odebrane. Skojarzenie jednego procesu klienta z jedną partycją pozwala zagwarantować, że rekordy są przetwarzane we właściwej kolejności. Aby uzyskać więcej informacji, zobacz [Start with Apache Kafka on HDInsight](apache-kafka-get-started.md) (Rozpoczynanie pracy z platformą Apache Kafka w usłudze HDInsight).|
|Obsługa komunikatów|platforma Kafka obsługuje wzorzec przesyłania komunikatów dotyczących publikowania i subskrybowania, dlatego jest często używana jako broker komunikatów.|
|Śledzenie działań|platforma Kafka rejestruje rekordy w określonej kolejności, dlatego może służyć do śledzenia i ponownego tworzenia działań. Mogą to być na przykład działania użytkownika w witrynie sieci Web lub aplikacji.|
|Agregacja|przetwarzanie strumienia pozwala agregować informacje z różnych strumieni w celu łączenia i centralizowania informacji w formie danych operacyjnych.|
|Przekształcenia|przetwarzanie strumienia umożliwia łączenie i urozmaicanie danych z wielu tematów wejściowych w formie tematów wyjściowych.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak korzystać z platformy Apache Kafka w usłudze HDInsight, użyj następujących linków:

* [Szybki Start: tworzenie platformy Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md)

* [Korzystanie z Kafka z serwerem proxy REST](rest-proxy.md)

* [Samouczek: korzystanie z systemu Apache Spark z platformą Apache Kafka w usłudze HDInsight](../hdinsight-apache-spark-with-kafka.md)

* [Samouczek: korzystanie z systemu Apache Storm z platformą Apache Kafka w usłudze HDInsight](../hdinsight-apache-storm-with-kafka.md)
