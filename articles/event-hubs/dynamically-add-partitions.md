---
title: Dynamiczne dodawanie partycji do centrum zdarzeń w Azure Event Hubs
description: W tym artykule pokazano, jak dynamicznie dodawać partycje do centrum zdarzeń w Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: aeeee1bcefe58b006dac0b6913aaa609cbeefb8c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775124"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Dynamiczne dodawanie partycji do centrum zdarzeń (Apache Kafka tematu) w Azure Event Hubs
Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów. Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. W przypadku pojawiania się nowych zdarzeń są one dodawane na końcu tej sekwencji. Aby uzyskać więcej ogólnych informacji na temat partycji, zobacz [Partitions (Partycje)](event-hubs-scalability.md#partitions)

Podczas tworzenia centrum zdarzeń można określić liczbę partycji. W niektórych scenariuszach może być konieczne dodanie partycji po utworzeniu centrum zdarzeń. W tym artykule opisano sposób dynamicznego dodawania partycji do istniejącego centrum zdarzeń. 

> [!IMPORTANT]
> Dynamiczne dodawanie partycji jest dostępne tylko w **dedykowanych** Event Hubs klastrach.

> [!NOTE]
> W Apache Kafka usługi centrum zdarzeń **jest mapowe** na temat **platformy Kafka.** Aby uzyskać więcej mapowań między Azure Event Hubs i Apache Kafka, zobacz [Kafka and Event Hubs conceptual mapping (Mapowanie koncepcyjne platformy Kafka i Event Hubs).](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Aktualizowanie liczby partycji
W tej sekcji przedstawiono sposób aktualizowania liczby partycji centrum zdarzeń na różne sposoby (program PowerShell, interfejs wiersza polecenia itd.).

### <a name="powershell"></a>PowerShell
Użyj polecenia [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) programu PowerShell, aby zaktualizować partycje w centrum zdarzeń. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>Interfejs wiersza polecenia
Użyj polecenia [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az_eventhubs_eventhub_update) interfejsu wiersza polecenia, aby zaktualizować partycje w centrum zdarzeń. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Zaktualizuj wartość właściwości w szablonie Resource Manager i ponownie wdeń `partitionCount` szablon, aby zaktualizować zasób. 

```json
    {
        "apiVersion": "2017-04-01",
        "type": "Microsoft.EventHub/namespaces/eventhubs",
        "name": "[concat(parameters('namespaceName'), '/', parameters('eventHubName'))]",
        "location": "[parameters('location')]",
        "dependsOn": [
            "[resourceId('Microsoft.EventHub/namespaces', parameters('namespaceName'))]"
        ],
        "properties": {
            "messageRetentionInDays": 7,
            "partitionCount": 12
        }
    }
```

### <a name="apache-kafka"></a>Apache Kafka
Użyj interfejsu API (na przykład za pomocą narzędzia interfejsu wiersza polecenia `AlterTopics` **kafka-topics),** aby zwiększyć liczbę partycji. Aby uzyskać szczegółowe informacje, [zobacz modyfikowanie tematów platformy Kafka.](http://kafka.apache.org/documentation/#basic_ops_modify_topic) 

## <a name="event-hubs-clients"></a>Event Hubs klientów
Przyjrzyjmy się, jak Event Hubs, gdy liczba partycji jest aktualizowana w centrum zdarzeń. 

Po dodaniu partycji do istniejącego centrum zdarzeń klient centrum zdarzeń otrzymuje z usługi element informując klientów, że metadane jednostki (jednostka to centrum zdarzeń, a metadane to informacje o partycji) zostały `MessagingException` zmienione. Klienci automatycznie ponownie otwierają linki AMQP, które następnie pobierają zmienione informacje metadanych. Klienci działają normalnie.

### <a name="senderproducer-clients"></a>Klienci nadawcy/producenta
Event Hubs udostępnia trzy opcje nadawcy:

- **Nadawca partycji** — w tym scenariuszu klienci wysyłają zdarzenia bezpośrednio do partycji. Mimo że partycje są możliwe do zidentyfikowania i zdarzenia mogą być wysyłane bezpośrednio do nich, nie zalecamy tego wzorca. Dodawanie partycji nie ma wpływu na ten scenariusz. Zalecamy ponowne uruchamianie aplikacji, aby wykrywały nowo dodane partycje. 
- **Nadawca klucza partycji** — w tym scenariuszu klienci wysyłają zdarzenia z kluczem, dzięki czemu wszystkie zdarzenia należące do tego klucza są w tej samej partycji. W takim przypadku usługa skrótuje klucz i kieruje do odpowiedniej partycji. Aktualizacja liczby partycji może powodować problemy poza kolejnością z powodu zmiany skrótu. Dlatego jeśli zależy Ci na kolejności, upewnij się, że aplikacja zużywa wszystkie zdarzenia z istniejących partycji przed zwiększeniem liczby partycji.
- **Nadawca działania okrężnego (wartość domyślna)** — w tym scenariuszu usługa Event Hubs okrężna zaokrągla zdarzenia między partycjami, a także używa algorytmu równoważenia obciążenia. Event Hubs jest świadoma zmian liczby partycji i będzie wysyłać dane do nowych partycji w ciągu kilku sekund od zmiany liczby partycji.

### <a name="receiverconsumer-clients"></a>Klienci odbiorcy/odbiorcy
Event Hubs udostępnia odbiorniki bezpośrednie i łatwą bibliotekę konsumenta o nazwie Host procesora zdarzeń [(stary zestaw SDK)](event-hubs-event-processor-host.md) lub Procesor zdarzeń [(nowy zestaw SDK).](event-processor-balance-partition-load.md)

- **Odbiorniki bezpośrednie** — odbiorniki bezpośrednie nasłuchują określonych partycji. Nie ma to wpływu na ich zachowanie w czasie wykonywania, gdy partycje są skalowane w zewnątrz dla centrum zdarzeń. Aplikacja, która korzysta z odbiorników bezpośrednich, musi zająć się pobieraniem nowych partycji i odpowiedniego przypisywania odbiorników.
- **Host procesora zdarzeń —** ten klient nie odświeża automatycznie metadanych jednostki. W związku z tym nie zwiększy się to w przypadku zwiększenia liczby partycji. Ponowne utworzenie wystąpienia procesora zdarzeń spowoduje pobranie metadanych jednostki, co z kolei spowoduje utworzenie nowych obiektów blob dla nowo dodanych partycji. Nie będzie to mieć wpływu na istniejące już obiekty blob. Zaleca się ponowne uruchomienie wszystkich wystąpień procesora zdarzeń, aby upewnić się, że wszystkie wystąpienia wiedzą o nowo dodanych partycjach, a równoważenie obciążenia jest prawidłowo obsługiwane przez odbiorców.

    Jeśli używasz starej wersji zestawu .NET SDK[(WindowsAzure.ServiceBus),](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)host procesora zdarzeń usuwa istniejący punkt kontrolny po ponownym uruchomieniu, jeśli liczba partycji w punkcie kontrolnym nie jest dopasowana do liczby partycji pobranej z usługi. To zachowanie może mieć wpływ na aplikację. 

## <a name="apache-kafka-clients"></a>Apache Kafka klientów
W tej sekcji opisano Apache Kafka, którzy używają punktu końcowego platformy Kafka usługi Azure Event Hubs, gdy liczba partycji jest aktualizowana dla centrum zdarzeń. 

Klienci platformy Kafka, którzy używają Event Hubs z protokołem Apache Kafka, zachowują się inaczej niż klienci centrum zdarzeń, którzy używają protokołu AMQP. Klienci platformy Kafka aktualizują swoje metadane co `metadata.max.age.ms` milisekundy. Tę wartość należy określić w konfiguracjach klienta. Biblioteki `librdkafka` również używają tej samej konfiguracji. Aktualizacje metadanych informują klientów o zmianach usługi, w tym zwiększa się liczba partycji. Aby uzyskać listę konfiguracji, zobacz [Apache Kafka konfiguracji dla Event Hubs](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Klienci nadawcy/producenta
Producenci zawsze określają, że żądania wysyłania zawierają miejsce docelowe partycji dla każdego zestawu wytwarzanych rekordów. Dlatego wszystkie partycjonowanie danych jest wykonywane po stronie klienta z widokiem producenta metadanych brokera. Po dodaniu nowych partycji do widoku metadanych producenta będą one dostępne dla żądań producenta.

### <a name="consumerreceiver-clients"></a>Klienci odbiorcy
Gdy członek grupy odbiorców wykonuje odświeżanie metadanych i przejmuje nowo utworzone partycje, ten członek inicjuje ponowne równoważenie grupy. Metadane konsumenta zostaną odświeżone dla wszystkich członków grupy, a nowe partycje zostaną przypisane przez przydzielonego lidera ponownego równoważenia.

## <a name="recommendations"></a>Zalecenia

- Jeśli używasz klucza partycji z aplikacjami producenta i korzystasz z funkcji wyznaczania wartości skrótu klucza w celu zapewnienia kolejności w partycji, dynamiczne dodawanie partycji nie jest zalecane. 

    > [!IMPORTANT]
    > Podczas gdy istniejące dane zachowują kolejność, skróty partycji będą uszkodzone w przypadku komunikatów skrótów po zmianie liczby partycji z powodu dodaniem partycji.
- Dodawanie partycji do istniejącego tematu lub wystąpienia centrum zdarzeń jest zalecane w następujących przypadkach:
    - W przypadku używania domyślnej metody wysyłania zdarzeń
     - Domyślne strategie partycjonowania platformy Kafka, na przykład strategia sticky assignor


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat partycji, [zobacz Partycje](event-hubs-scalability.md#partitions).
