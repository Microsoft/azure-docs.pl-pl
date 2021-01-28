---
title: Migrowanie obciążeń Apache Kafka do usługi Azure HDInsight 4,0
description: Dowiedz się, jak migrować obciążenia Apache Kafka w usłudze HDInsight 3,6 do usługi HDInsight 4,0.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/18/2019
ms.openlocfilehash: 3967a5d96c35e4bac88dcd9a6c1fa95b78a6b2b1
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939119"
---
# <a name="migrate-apache-kafka-workloads-to-azure-hdinsight-40"></a>Migrowanie obciążeń Apache Kafka do usługi Azure HDInsight 4,0

Usługa Azure HDInsight 4,0 oferuje najnowsze składniki Open Source ze znaczącymi ulepszeniami wydajności, łączności i zabezpieczeń. W tym dokumencie opisano sposób migrowania obciążeń Apache Kafka w usłudze HDInsight 3,6 do usługi HDInsight 4,0. Po przeprowadzeniu migracji obciążeń do usługi HDInsight 4,0 możesz użyć wielu nowych funkcji, które nie są dostępne w usłudze HDInsight 3,6.

## <a name="hdinsight-36-kafka-migration-paths"></a>Ścieżki migracji Kafka usługi HDInsight 3,6

Usługa HDInsight 3,6 obsługuje dwie wersje Kafka: 1.0.0 i 1.1.0. Usługa HDInsight 4,0 obsługuje wersje 1.1.0 i 2.1.0. W zależności od wersji programu Kafka i wersji usługi HDInsight, którą chcesz uruchomić, istnieje wiele obsługiwanych ścieżek migracji. Te ścieżki są wyjaśnione poniżej i zilustrowane na poniższym diagramie.

* **Uruchom zarówno Kafka, jak i HDInsight dla najnowszych wersji (zalecane)**: Migruj aplikację HDInsight 3,6 i Kafka 1.0.0 lub 1.1.0 do usługi HDInsight 4,0 z Kafka 2.1.0 (ścieżki D i E poniżej).
* **Uruchom usługi HDInsight w najnowszej wersji, ale Kafka tylko na** nowszą wersję: Migruj aplikację HDInsight 3,6 i Kafka 1.0.0 do usługi HDInsight 4,0 z Kafka 1.1.0 (ścieżka B poniżej).
* **Uruchom usługi HDInsight w najnowszej wersji, Zachowaj wersję Kafka**: Migruj aplikację HDInsight 3,6 i Kafka 1.1.0 do usługi HDInsight 4,0 z Kafka 1.1.0 (ścieżka C poniżej).
* **Uruchom Kafka z nowszą wersją, Zachowaj wersję usługi HDInsight**: Migruj aplikację Kafka 1.0.0 do 1.1.0 i pozostań w usłudze HDInsight 3,6 (ścieżka a poniżej). Należy pamiętać, że ta opcja będzie nadal wymagała wdrożenia nowego klastra. Uaktualnianie wersji Kafka w istniejącym klastrze nie jest obsługiwane. Po utworzeniu klastra z żądaną wersją wykonaj migrację klientów Kafka do korzystania z nowego klastra.

![Ścieżki uaktualniania dla Apache Kafka na 3,6](./media/upgrade-threesix-to-four/apache-kafka-upgrade-path.png)

## <a name="apache-kafka-versions"></a>Wersje Apache Kafka

### <a name="kafka-110"></a>Kafka 1.1.0
  
W przypadku migrowania z programu Kafka 1.0.0 do usługi 1.1.0 można korzystać z następujących nowych funkcji:

* Ulepszenia kontrolera Kafka przyspieszają kontrolowane zamykanie, dzięki czemu możesz ponownie uruchamiać brokerów i szybciej odzyskiwać problemy. 
* Ulepszenia [logiki FetchRequests](https://issues.apache.org/jira/browse/KAFKA-6254) , które umożliwiają posiadanie większej liczby partycji (w tym więcej tematów) w klastrze. 
* Program Kafka Connect obsługuje [nagłówki rekordów](https://issues.apache.org/jira/browse/KAFKA-5142) i [wyrażenia regularne](https://issues.apache.org/jira/browse/KAFKA-3073) dla tematów. 

Aby uzyskać pełną listę aktualizacji, zobacz [Informacje o wersji Apache Kafka 1,1](https://archive.apache.org/dist/kafka/1.1.0/RELEASE_NOTES.html).

### <a name="apache-kafka-210"></a>Apache Kafka 2.1.0

W przypadku migracji do Kafka 2,1 można korzystać z następujących funkcji:

* Lepsza odporność brokera ze względu na udoskonalony protokół replikacji.
* Nowe funkcje w interfejsie API KafkaAdminClient.
* Konfigurowalne zarządzanie przydziałami.
* Obsługa kompresji Zstandard.

Aby uzyskać pełną listę aktualizacji, zobacz informacje o [wersji Apache Kafka 2,0](https://archive.apache.org/dist/kafka/2.0.0/RELEASE_NOTES.html) i [Informacje o wersji programu Apache Kafka 2,1](https://archive.apache.org/dist/kafka/2.1.0/RELEASE_NOTES.html).

## <a name="kafka-client-compatibility"></a>Kafka zgodność klienta

Nowe brokery Kafka obsługują starszych klientów. [Miń-35 — pobranie wersji protokołu](https://cwiki.apache.org/confluence/display/KAFKA/KIP-35+-+Retrieving+protocol+version) wprowadziła mechanizm dynamicznego określania funkcjonalności brokera Kafka i [miń-97: ulepszone Kafka zasady zgodności klienta RPC](https://cwiki.apache.org/confluence/display/KAFKA/KIP-97%3A+Improved+Kafka+Client+RPC+Compatibility+Policy) wprowadziły nowe zasady zgodności i gwarancje dla klienta Java. Wcześniej klient Kafka musiał korzystać z brokera tej samej wersji lub nowszej wersji. Teraz nowsze wersje klientów Java i innych klientów obsługujących MIŃ-35 takie jak `librdkafka` mogą wrócić do starszych typów żądań lub zgłosić odpowiednie błędy, jeśli funkcja nie jest dostępna.

![Uaktualnij zgodność klienta Kafka](./media/upgrade-threesix-to-four/apache-kafka-client-compatibility.png)

Należy pamiętać, że klient obsługuje starszych brokerów.  Aby uzyskać więcej informacji, zobacz [macierz zgodności](https://cwiki.apache.org/confluence/display/KAFKA/Compatibility+Matrix).

## <a name="general-migration-process"></a>Ogólny proces migracji

W poniższych wskazówkach dotyczących migracji przyjęto założenie, Apache Kafka klaster 1.0.0 lub 1.1.0 wdrożony w usłudze HDInsight 3,6 w ramach jednej sieci wirtualnej. Istniejący Broker zawiera kilka tematów i jest aktywnie używany przez producentów i konsumentów.

![Bieżące Kafkane środowisko](./media/upgrade-threesix-to-four/apache-kafka-presumed-environment.png)

Aby ukończyć migrację, wykonaj następujące czynności:

1. **Wdróż nowego klastra usługi HDInsight 4,0 i klientów na potrzeby testu.** Wdróż nowy klaster Kafka usługi HDInsight 4,0. Jeśli można wybrać wiele wersji klastra Kafka, zaleca się wybranie najnowszej wersji. Po wdrożeniu Ustaw pewne parametry zgodnie z potrzebami i Utwórz temat o takiej samej nazwie jak istniejące środowisko. Ponadto w razie konieczności należy ustawić szyfrowanie TLS i BYOK. Następnie sprawdź, czy działa poprawnie z nowym klastrem.

    ![Wdrażanie nowych klastrów usługi HDInsight 4,0](./media/upgrade-threesix-to-four/deploy-new-hdinsight-clusters.png)

1. **Przełącz klaster dla aplikacji producenta i zaczekaj, aż wszystkie dane kolejki zostaną wykorzystane przez bieżących odbiorców.** Gdy nowy klaster usługi HDInsight 4,0 Kafka jest gotowy, przełącz istniejący obiekt docelowy producenta do nowego klastra. Pozostaw je, dopóki istniejąca aplikacja klienta nie wykorzysta wszystkich danych z istniejącego klastra.

    ![Przełącz klaster dla aplikacji producenta](./media/upgrade-threesix-to-four/switch-cluster-producer-app.png)

1. **Przełączenie klastra w aplikacji konsumenta.** Po potwierdzeniu, że istniejąca aplikacja konsumencka zakończyła zużywanie wszystkich danych z istniejącego klastra, należy przełączyć połączenie do nowego klastra.

    ![Przełącz klaster na aplikację konsumenta](./media/upgrade-threesix-to-four/switch-cluster-consumer-app.png)

1. **Usuń stary klaster i przetestuj aplikacje zgodnie z wymaganiami.** Po zakończeniu przełączania i poprawnym działaniu Usuń stary klaster Kafka usługi HDInsight 3,6 i producentów oraz odbiorców użytych w teście zgodnie z wymaganiami.

## <a name="next-steps"></a>Następne kroki

* [Optymalizacja wydajności klastrów Apache Kafka HDInsight](apache-kafka-performance-tuning.md)
* [Szybki Start: Tworzenie klastra Apache Kafka w usłudze Azure HDInsight przy użyciu Azure Portal](apache-kafka-get-started.md)
