---
title: Wysoka dostępność dzięki Apache Kafka usługi Azure HDInsight
description: Dowiedz się, jak zapewnić wysoką dostępność dzięki platformie Apache Kafka w usłudze Azure HDInsight. Dowiedz się, jak przeprowadzić ponowne równoważenie replik partycji na platformie Kafka, tak aby znajdowały się one w różnych domenach błędów w regionie świadczenia usługi Azure, który udostępnia usługę HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e3f31bc1876a0c1f90ad4882ff782be25b4b6ed6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932752"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Wysoka dostępność danych dzięki platformie Apache Kafka w usłudze HDInsight

Dowiedz się, jak skonfigurować repliki partycji dla Apache Kafka tematów, aby wykorzystać podstawową konfigurację stojaka sprzętowego. Ta konfiguracja zapewnia dostępność danych przechowywanych na platformie Apache Kafka w usłudze HDInsight.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Błędy i aktualizowanie domen przy użyciu Apache Kafka

Domena błędów to logiczna grupa bazowego sprzętu w centrum danych platformy Azure. Wszystkie domeny błędów korzystają ze wspólnego źródła zasilania i przełącznika sieciowego. Maszyny wirtualne i dyski zarządzane, które implementują węzły w klastrze usługi HDInsight są rozdzielone między te domeny błędów. Taka architektura ogranicza wpływ potencjalnych awarii sprzętu fizycznego.

W każdym regionie świadczenia usługi Azure znajduje się określona liczba domen błędów. Aby uzyskać listę domen i informacje o liczbie zawartych w nich domen błędów, zobacz dokument [Zestawy dostępności](../../virtual-machines/availability.md#availability-sets).

> [!IMPORTANT]  
> Platforma Kafka nie uwzględnia domen błędów. W przypadku utworzenia tematu na platformie Kafka wszystkie repliki partycji mogą być przechowywane w tej samej domenie błędów. Aby rozwiązać ten problem, usługa HDInsight udostępnia [narzędzie do ponownego równoważenia partycji platformy Kafka](https://github.com/hdinsight/hdinsight-kafka-tools).

## <a name="when-to-rebalance-partition-replicas"></a>Kiedy należy stosować ponowne równoważenie replik partycji

Aby zapewnić najwyższą dostępność danych na platformie Kafka, należy stosować ponowne równoważenie replik partycji dla tematu w następujących sytuacjach:

* Po utworzeniu nowego tematu lub partycji

* Po przeskalowaniu klastra w górę

## <a name="replication-factor"></a>Współczynnik replikacji

> [!IMPORTANT]  
> Zalecamy wybranie regionu świadczenia usługi Azure zawierającego trzy domeny błędów oraz użycie współczynnika replikacji o wartości 3.

Jeśli musisz wybrać region, który zawiera tylko dwie domeny błędów, użyj współczynnika replikacji o wartości 4, aby równomiernie rozłożyć repliki na dwie domeny błędów.

Przykład tworzenia tematów i ustawiania współczynnika replikacji znajdują się w dokumencie [Rozpocznij od Apache Kafka w usłudze HDInsight](apache-kafka-get-started.md) .

## <a name="how-to-rebalance-partition-replicas"></a>Jak zastosować ponowne równoważenie replik partycji

Użyj [Narzędzia do ponownego równoważenia Apache Kafka partycji](https://github.com/hdinsight/hdinsight-kafka-tools) , aby ponownie zrównoważyć wybrane tematy. Narzędzie to należy uruchomić w sesji połączenia SSH z węzłem głównym klastra Kafka.

Aby uzyskać więcej informacji dotyczących nawiązywania połączenia z usługą HDInsight przy użyciu protokołu SSH, zobacz dokument [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="next-steps"></a>Następne kroki

* [Skalowalność Apache Kafka w usłudze HDInsight](apache-kafka-scalability.md)
* [Dublowanie przy użyciu Apache Kafka w usłudze HDInsight](apache-kafka-mirroring.md)