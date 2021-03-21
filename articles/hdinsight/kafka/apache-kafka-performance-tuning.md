---
title: Optymalizacja wydajności klastrów Apache Kafka HDInsight
description: Zawiera omówienie technik optymalizacji obciążeń Apache Kafka w usłudze Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: d24527efe6adce6f16b7c890f23c755545f5d5a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935895"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optymalizacja wydajności klastrów Apache Kafka HDInsight

Ten artykuł zawiera sugestie dotyczące optymalizacji wydajności obciążeń Apache Kafka w usłudze HDInsight. Koncentruje się na dostosowywaniu konfiguracji producenta i brokera. Istnieją różne sposoby mierzenia wydajności, a Optymalizacja do zastosowania będzie zależeć od potrzeb firmy.

## <a name="architecture-overview"></a>Omówienie architektury

Tematy Kafka są używane do organizowania rekordów. Rekordy są tworzone przez producentów i używane przez odbiorców. Producenci wysyłają rekordy do brokerów Kafka, które następnie przechowują dane. Każdy węzeł procesu roboczego w klastrze usługi HDInsight jest brokerem platformy Kafka.

Tematy dzielą rekordy między brokerami. Podczas korzystania z rekordów można użyć maksymalnie jednego odbiorcy na jedną partycję, aby osiągnąć równoległe przetwarzanie danych.

Replikacja jest używana do duplikowania partycji między węzłami. Chroni przed awarią węzła (brokera). Pojedyncza partycja między grupą replik jest wyznaczony jako lider partycji. Ruch producenta jest kierowany do partycji wiodącej w każdym węźle przy użyciu stanu zarządzanego przez usługę ZooKeeper.

## <a name="identify-your-scenario"></a>Identyfikowanie własnego scenariusza

Apache Kafka wydajność ma dwa główne aspekty — przepływność i czas oczekiwania. Przepływność to maksymalna szybkość, z jaką mogą być przetwarzane dane. Wyższa przepływność jest zwykle lepsza. Opóźnienie to czas potrzebny na przechowywanie lub pobieranie danych. Mniejsze opóźnienie jest zwykle lepsze. Znalezienie odpowiedniej równowagi między przepływem, opóźnieniem i kosztem infrastruktury aplikacji może być trudne. Twoje wymagania dotyczące wydajności będą prawdopodobnie zgodne z jedną z następujących trzech typowych sytuacji w zależności od tego, czy jest wymagana Wysoka przepływność, małe opóźnienia czy obie:

* Wysoka przepływność, małe opóźnienia. W tym scenariuszu wymagana jest Wysoka przepływność i małe opóźnienia (~ 100 ms). Przykładem tego typu aplikacji jest monitorowanie dostępności usług.
* Wysoka przepływność i duże opóźnienia. Ten scenariusz wymaga dużej przepływności (~ 1,5 GB/s), ale może tolerować wyższe opóźnienie (< 250 MS). Przykładem tego typu aplikacji jest pozyskiwanie danych telemetrycznych dla procesów niemal w czasie rzeczywistym, takich jak zabezpieczenia i wykrywanie nieautoryzowanych aplikacji.
* Niska przepływność, małe opóźnienia. Ten scenariusz wymaga małych opóźnień (< 10 ms) do przetwarzania w czasie rzeczywistym, ale może tolerować niższą przepływność. Przykładem tego typu aplikacji jest sprawdzanie pisowni w trybie online i gramatyki.

## <a name="producer-configurations"></a>Konfiguracje producentów

W poniższych sekcjach opisano niektóre najważniejsze właściwości konfiguracji w celu zoptymalizowania wydajności producentów Kafka. Aby uzyskać szczegółowy opis wszystkich właściwości konfiguracji, zobacz [dokumentację Apache Kafka na temat konfiguracji producentów](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Rozmiar partii

Producenci Apache Kafka mogą złożyć grupy komunikatów (nazywanych partiami), które są wysyłane jako jednostka, która ma być przechowywana w jednej partycji magazynu. Rozmiar wsadu oznacza liczbę bajtów, które muszą być obecne przed przesłaniem tej grupy. Zwiększenie `batch.size` parametru może zwiększyć przepływność, ponieważ zmniejsza to obciążenie związane z przetwarzaniem żądań sieci i operacji we/wy. W obszarze lekkich obciążeń zwiększenie rozmiaru partii może zwiększyć czas oczekiwania na Kafka, ponieważ producent czeka na gotowość partii. W przypadku dużego obciążenia zaleca się zwiększenie rozmiaru partii w celu zwiększenia przepływności i opóźnień.

### <a name="producer-required-acknowledgments"></a>Wymagane potwierdzenia przez producenta

`acks`Konfiguracja wymagana przez producenta określa liczbę potwierdzeń wymaganych przez lidera partycji przed ukończeniem żądania zapisu. To ustawienie wpływa na niezawodność danych i pobiera wartości `0` , `1` lub `-1` . Wartość `-1` oznacza, że przed ukończeniem zapisu należy odebrać potwierdzenie ze wszystkich replik. Ustawienie `acks = -1` zapewnia silniejsze gwarancje związane z utratą danych, ale również skutkuje wyższym opóźnieniem i niższą przepływność. Jeśli wymagania dotyczące aplikacji wymagają wyższej przepływności, spróbuj ustawić `acks = 0` lub `acks = 1` . Należy pamiętać, że nie jest to potwierdzenie, że wszystkie repliki mogą zmniejszyć niezawodność danych.

### <a name="compression"></a>Kompresja

Producenta Kafka można skonfigurować w taki sposób, aby kompresuje komunikaty przed wysłaniem ich do brokerów. `compression.type`Ustawienie określa koder-dekoder kompresji do użycia. Obsługiwane kodery-dekoder kompresji to "gzip", "przyciąganie" i "lz4". Kompresowanie jest korzystne i powinno być brane pod uwagę w przypadku ograniczenia pojemności dysku.

Między dwoma najczęściej używanymi koderami-dekoder `gzip` kompresji `snappy` i `gzip` ma wyższy współczynnik kompresji, co skutkuje obniżeniem użycia dysku przy kosztach większego obciążenia procesora CPU. `snappy`Koder-dekoder zapewnia mniejszą kompresję przy mniejszej obciążeniu procesora. Można zdecydować, który koder-dekoder ma być używany w oparciu o ograniczenia procesora CPU na dysku lub producencie. `gzip` może skompresować dane z częstotliwością pięć razy wyższą niż `snappy` .

Użycie kompresji danych spowoduje zwiększenie liczby rekordów, które mogą być przechowywane na dysku. Może również zwiększyć obciążenie procesora CPU w przypadkach, gdy występuje niezgodność między formatami kompresji używanymi przez producenta i brokera. dane muszą być kompresowane przed wysłaniem, a następnie dekompresowane przed przetworzeniem.

## <a name="broker-settings"></a>Ustawienia brokera

W poniższych sekcjach opisano niektóre najważniejsze ustawienia optymalizacji wydajności brokerów Kafka. Szczegółowe wyjaśnienie wszystkich ustawień brokera można znaleźć [w dokumentacji Apache Kafka na temat konfiguracji producentów](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Liczba dysków

Dyski magazynu mają ograniczoną liczbę IOPS (operacje wejścia/wyjścia na sekundę) i bajty odczytu/zapisu na sekundę. Podczas tworzenia nowych partycji Kafka przechowuje każdą nową partycję na dysku z najmniejszą liczbą istniejących partycji, aby zrównoważyć je na dostępnych dyskach. Pomimo strategii magazynowania przy przetwarzaniu setek replik partycji na każdym dysku Kafka może łatwo zaszeregować dostępną przepływność na dysku. Poniżej znajduje się kompromis między przepływem i kosztami. Jeśli aplikacja wymaga większej przepływności, Utwórz klaster o większej liczbie dysków zarządzanych na brokera. Usługa HDInsight nie obsługuje obecnie dodawania dysków zarządzanych do uruchomionego klastra. Aby uzyskać więcej informacji na temat konfigurowania liczby dysków zarządzanych, zobacz [Konfigurowanie magazynu i skalowalności Apache Kafka w usłudze HDInsight](apache-kafka-scalability.md). Zapoznaj się z kosztami związanymi z zwiększeniem ilości miejsca do magazynowania dla węzłów w klastrze.

### <a name="number-of-topics-and-partitions"></a>Liczba tematów i partycji

Kafka producenci mogą zapisywać w tematach. Kafka konsumentów do odczytu z tematów. Temat jest skojarzony z dziennikiem, który jest strukturą danych na dysku. Kafka dołącza rekordy od producentów do końca dziennika tematu. Dziennik tematu składa się z wielu partycji, które są rozmieszczone na wielu plikach. Te pliki są z kolei rozmieszczane w wielu węzłach klastra Kafka. Konsumenci czytają Kafka tematy w ich erze i mogą wybrać ich pozycję (przesunięcie) w dzienniku tematu.

Każda partycja Kafka jest plikiem dziennika w systemie, a wątki producenci mogą jednocześnie zapisywać w wielu dziennikach. Podobnie, ponieważ każdy wątek konsumencki odczytuje komunikaty z jednej partycji, używanie wielu partycji jest również obsługiwane równolegle.

Zwiększenie gęstości partycji (liczba partycji na brokera) powoduje dodanie narzutu związanego z operacjami metadanych oraz na żądanie partycji/odpowiedź między liderem partycji i jego obserwatorami. Nawet w przypadku braku przepływu danych w programie repliki partycji nadal pobierają dane od liderów, co skutkuje dodatkowym przetwarzaniem żądań wysyłania i odbierania przez sieć.

W przypadku klastrów Apache Kafka 1,1 i nowszych w usłudze HDInsight zalecamy użycie maksymalnie 1000 partycji na brokera, w tym replik. Zwiększenie liczby partycji na brokera zmniejsza przepływność i może również spowodować niedostępność tematu. Aby uzyskać więcej informacji na temat obsługi partycji Kafka, zapoznaj [się z Apache Kafka oficjalnym wpisem w blogu, aby dowiedzieć się więcej o zwiększeniu liczby obsługiwanych partycji w wersji 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Aby uzyskać szczegółowe informacje na temat modyfikowania tematów, zobacz [Apache Kafka: modyfikowanie tematów](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Liczba replik

Wyższa wartość współczynnika replikacji skutkuje dodatkowymi żądaniami między liderem partycji a obserwatorami. W związku z tym wyższym czynnikiem replikacji zużywa więcej dysku i procesora do obsługi dodatkowych żądań, co zwiększa opóźnienie zapisu i zmniejsza przepływność.

Zalecamy używanie co najmniej 3-krotnej replikacji dla Kafka w usłudze Azure HDInsight. Większość regionów świadczenia usługi Azure ma trzy domeny błędów, ale w regionach mających tylko dwie domeny błędów użytkownicy powinni korzystać z replikacji 4x.

Aby uzyskać więcej informacji na temat replikacji, zobacz [Apache Kafka: replikacja](https://kafka.apache.org/documentation/#replication) i [Apache Kafka: zwiększanie współczynnika replikacji](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Następne kroki

* [Processing trillions of events per day with Apache Kafka on Azure (Przetwarzanie bilionów zdarzeń dziennie za pomocą platformy Apache Kafka na platformie Azure)](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Co to jest platforma Apache Kafka w usłudze HDInsight?](apache-kafka-introduction.md)
