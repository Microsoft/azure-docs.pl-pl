---
title: Integrowanie narzędzia Apache Kafka Connect z usługą Azure Event Hubs | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące korzystania z programu Kafka Connect with Azure Event Hubs for Kafka.
ms.topic: how-to
ms.date: 01/06/2021
ms.openlocfilehash: f82dcdafa7921f4a994361371536b2f1ace7cbc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935159"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs"></a>Integrowanie obsługi narzędzia Apache Kafka Connect w usłudze Azure Event Hubs
[Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) to struktura służąca do łączenia i importowania/eksportowania danych z/do dowolnego systemu zewnętrznego, takiego jak MySQL, HDFS i system plików, za pośrednictwem klastra Kafka. Ten samouczek przeprowadzi Cię przez proces używania programu Kafka Connect Framework z Event Hubs.

> [!WARNING]
> Korzystanie z usługi Apache Kafka Connect Framework i jej łączników **nie kwalifikuje się do pomocy technicznej produktu za pomocą usługi Microsoft Azure**.
>
> Apache Kafka Connect przyjmuje, że konfiguracja dynamiczna będzie utrzymywana w kompaktowych tematach, w przeciwnym razie nieograniczony czas przechowywania. Usługa Azure Event Hubs nie [implementuje kompaktowania jako funkcji brokera](event-hubs-federation-overview.md#log-projections) i zawsze nakłada limit przechowywania na podstawie czasu dla zachowanych zdarzeń, z reguły, którą usługa Azure Event Hubs jest aparatem przesyłania strumieniowego zdarzeń w czasie rzeczywistym, a nie z długoterminowym magazynem danych lub konfiguracją.
>
> Chociaż projekt Apache Kafka może być wygodniejszy do mieszania tych ról, platforma Azure uważa, że takie informacje najlepiej są zarządzane w odpowiedniej bazie danych lub w magazynie konfiguracji.
>
> Wiele scenariuszy Apache Kafka Connect będzie funkcjonalnych, ale te różnice koncepcyjne między modelami przechowywania Apache Kafka i Event Hubs platformy Azure mogą spowodować, że niektóre konfiguracje nie będą działać zgodnie z oczekiwaniami. 

Ten samouczek przeprowadzi Cię przez integrację Kafka z centrum zdarzeń i wdrażanie podstawowych łączników FileStreamSource i FileStreamSink. Ta funkcja jest obecnie w wersji zapoznawczej. Te łączniki nie są przeznaczone do użycia w środowisku produkcyjnym — służą one do zaprezentowania pełnego scenariusza narzędzia Kafka Connect, w którym usługa Azure Event Hubs działa jako broker platformy Kafka.

> [!NOTE]
> Ten przykład jest dostępny w witrynie [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

W tym samouczku wykonasz następujące kroki:

> [!div class="checklist"]
> * Tworzenie przestrzeni nazw usługi Event Hubs
> * Klonowanie projektu przykładowego
> * Konfigurowanie narzędzia Kafka Connect dla usługi Event Hubs
> * Uruchamianie narzędzia Kafka Connect
> * Tworzenie łączników

## <a name="prerequisites"></a>Wymagania wstępne
Aby ukończyć ten przewodnik, upewnij się, że dysponujesz następującymi elementami:

- Subskrypcja platformy Azure. Jeśli jej nie masz, [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
- [Usługa Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Platforma Kafka (wersja 1.1.1, Scala w wersji 2.11), dostępna na stronie [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Przeczytaj artykuł z wprowadzeniem [Usługa Event Hubs dla platformy Apache Kafka](./event-hubs-for-kafka-ecosystem-overview.md)

## <a name="create-an-event-hubs-namespace"></a>Tworzenie przestrzeni nazw usługi Event Hubs
Przestrzeń nazw usługi Event Hubs jest wymagana do wysyłania i odbierania zdarzeń z dowolnej usługi Event Hubs. Aby uzyskać instrukcje dotyczące tworzenia przestrzeni nazw i centrum zdarzeń, zobacz [Tworzenie centrum zdarzeń](event-hubs-create.md) . Pobierz parametry połączenia usługi Event Hubs i w pełni kwalifikowaną nazwę domeny (FQDN) w celu późniejszego użycia. Aby uzyskać instrukcje, zobacz [Get an Event Hubs connection string](event-hubs-get-connection-string.md) (Pobieranie parametrów połączenia usługi Event Hubs). 

## <a name="clone-the-example-project"></a>Klonowanie projektu przykładowego
Sklonuj repozytorium usługi Azure Event Hubs i przejdź do podfolderu tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurowanie narzędzia Kafka Connect dla usługi Event Hubs
Przekierowywanie przepływności narzędzia Kafka Connect z platformy Kafka do usługi Event Hubs wymaga minimalnej rekonfiguracji.  W poniższym przykładowym pliku `connect-distributed.properties` pokazano, jak skonfigurować narzędzie Connect do uwierzytelnienia i komunikowania się z punktem końcowym platformy Kafka w usłudze Event Hubs:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

> [!IMPORTANT]
> Zamień na `{YOUR.EVENTHUBS.CONNECTION.STRING}` Parametry połączenia dla przestrzeni nazw Event Hubs. Aby uzyskać instrukcje dotyczące uzyskiwania parametrów połączenia, zobacz [pobieranie parametrów połączenia Event Hubs](event-hubs-get-connection-string.md). Oto Przykładowa konfiguracja: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


## <a name="run-kafka-connect"></a>Uruchamianie narzędzia Kafka Connect

W tym kroku proces roboczy narzędzia Kafka Connect został uruchomiony lokalnie w trybie rozproszonym przy użyciu usługi Event Hubs w celu zachowania stanu klastra.

1. Zapisz powyższy plik `connect-distributed.properties` lokalnie.  Zamień wszystkie wartości w nawiasach klamrowych.
2. Przejdź do lokalizacji platformy Kafka w maszynie.
4. Uruchom polecenie `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  Pojawienie się tekstu `'INFO Finished starting connectors and tasks'` oznacza, że interfejs API REST procesu roboczego narzędzia Connect jest gotowy do interakcji. 

> [!NOTE]
> Kafka Connect używa interfejsu API Kafka AdminClient do automatycznego tworzenia tematów z zalecanymi konfiguracjami, w tym kompaktowania. Z szybkiego sprawdzenia przestrzeni nazw w witrynie Azure Portal wynika, że tematy wewnętrzne procesu roboczego narzędzia Connect zostały utworzone automatycznie.
>
>Tematy wewnętrzne Kafka Connect **muszą używać kompaktowania**.  Zespół Event Hubs nie jest odpowiedzialny za naprawianie nieprawidłowych konfiguracji, jeśli wewnętrzne tematy dotyczące połączenia zostały niepoprawnie skonfigurowane.

### <a name="create-connectors"></a>Tworzenie łączników
Ta sekcja przeprowadzi Cię przez proces tworzenia łączników FileStreamSource i FileStreamSink. 

1. Utwórz katalog dla plików danych wejściowych i wyjściowych.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Utwórz dwa pliki: plik z danymi inicjatora, które odczytuje łącznik FileStreamSource, i plik, w którym zapisuje dane łącznik FileStreamSink.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Utwórz łącznik FileStreamSource.  Zamień wartości w nawiasach klamrowych na ścieżkę do katalogu głównego.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Po uruchomieniu powyższego polecenia w wystąpieniu usługi Event Hubs powinno zostać wyświetlone centrum zdarzeń `connect-quickstart`.
4. Sprawdź stan łącznika źródła.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Opcjonalnie możesz użyć [Eksploratora usługi Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/releases), aby sprawdzić, czy zdarzenia zostały dostarczone do tematu `connect-quickstart`.

5. Utwórz łącznik FileStreamSink.  Ponownie zamień wartości w nawiasach klamrowych na ścieżkę do katalogu głównego.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Sprawdź stan łącznika ujścia.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Sprawdź, czy dane zostały zreplikowane między plikami oraz czy dane są takie same w obu plikach.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Czyszczenie
Narzędzie Kafka Connect tworzy tematy centrum zdarzeń do przechowywania konfiguracji, przesunięć i stanu — te dane są trwale przechowywane nawet po wyłączeniu klastra narzędzia Connect. Jeśli zależy Ci na takiej trwałości, zaleca się usunięcie tych tematów. Możesz też usunąć centrum zdarzeń `connect-quickstart`, które zostało utworzone w ramach tego przewodnika.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Event Hubs Kafka, zobacz następujące artykuły:  

- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Łączenie usługi Akka Streams z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Przewodnik dla deweloperów Apache Kafka dla platformy Azure Event Hubs](apache-kafka-developer-guide.md)
