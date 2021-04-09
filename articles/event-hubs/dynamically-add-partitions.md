---
title: Dynamiczne dodawanie partycji do centrum zdarzeń w usłudze Azure Event Hubs
description: W tym artykule opisano sposób dynamicznego dodawania partycji do centrum zdarzeń w usłudze Azure Event Hubs.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: e6efdc7bab309f825032555c97f1e1128f5addd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98625269"
---
# <a name="dynamically-add-partitions-to-an-event-hub-apache-kafka-topic-in-azure-event-hubs"></a>Dynamiczne dodawanie partycji do centrum zdarzeń (Apache Kafka tematu) na platformie Azure Event Hubs
Usługa Event Hubs udostępnia funkcję transmisji strumieniowej komunikatów za pośrednictwem partycjonowanego wzorca odbiorców, w ramach którego każdy odbiorca odczytuje tylko konkretny podzbiór (partycję) strumienia komunikatów. Ten wzorzec umożliwia skalowanie w poziomie przetwarzania zdarzeń oraz udostępnia inne funkcje dotyczące strumienia, które są niedostępne w przypadku kolejek i tematów. Partycja to uporządkowana sekwencja zdarzeń przechowywana w centrum zdarzeń. Po nadejściu nowszych zdarzeń są one dodawane na końcu tej sekwencji. Aby uzyskać więcej informacji o partycjach ogólnie, zobacz [partycje](event-hubs-scalability.md#partitions)

Można określić liczbę partycji w momencie tworzenia centrum zdarzeń. W niektórych scenariuszach może być konieczne dodanie partycji po utworzeniu centrum zdarzeń. W tym artykule opisano sposób dynamicznego dodawania partycji do istniejącego centrum zdarzeń. 

> [!IMPORTANT]
> Dynamiczne dodawanie partycji jest dostępne tylko w **dedykowanych** klastrach Event Hubs.

> [!NOTE]
> W przypadku klientów Apache Kafka **centrum zdarzeń** mapuje się do **tematu Kafka**. Aby uzyskać więcej mapowań między usługą Azure Event Hubs i Apache Kafka, zobacz [Mapowanie koncepcyjne Kafka i Event Hubs](event-hubs-for-kafka-ecosystem-overview.md#kafka-and-event-hub-conceptual-mapping)


## <a name="update-the-partition-count"></a>Aktualizowanie liczby partycji
W tej sekcji pokazano, jak zaktualizować liczbę partycji centrum zdarzeń na różne sposoby (program PowerShell, interfejs wiersza polecenia itd.).

### <a name="powershell"></a>PowerShell
Użyj polecenia programu PowerShell [Set-AzureRmEventHub](/powershell/module/azurerm.eventhub/Set-AzureRmEventHub) , aby zaktualizować partycje w centrum zdarzeń. 

```azurepowershell-interactive
Set-AzureRmEventHub -ResourceGroupName MyResourceGroupName -Namespace MyNamespaceName -Name MyEventHubName -partitionCount 12
```

### <a name="cli"></a>Interfejs wiersza polecenia
Użyj [`az eventhubs eventhub update`](/cli/azure/eventhubs/eventhub#az-eventhubs-eventhub-update) polecenia CLI, aby zaktualizować partycje w centrum zdarzeń. 

```azurecli-interactive
az eventhubs eventhub update --resource-group MyResourceGroupName --namespace-name MyNamespaceName --name MyEventHubName --partition-count 12
```

### <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Zaktualizuj wartość `partitionCount` właściwości w szablonie Menedżer zasobów i Wdróż ponownie szablon w celu zaktualizowania zasobu. 

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
Użyj `AlterTopics` interfejsu API (na przykład za pośrednictwem narzędzia interfejsu wiersza polecenia **Kafka-tematów** ), aby zwiększyć liczbę partycji. Aby uzyskać szczegółowe informacje, zobacz [Modyfikowanie tematów Kafka](http://kafka.apache.org/documentation/#basic_ops_modify_topic). 

## <a name="event-hubs-clients"></a>Event Hubs klienci
Przyjrzyjmy się, jak Event Hubs klienci działają po zaktualizowaniu liczby partycji w centrum zdarzeń. 

Po dodaniu partycji do istniejącego centrum danych parzystych klient centrum zdarzeń odbiera od usługi komunikat informujący `MessagingException` klientów, że metadane jednostki (jednostka jest centrum zdarzeń, a metadane to informacje o partycji) zostały zmienione. Klienci będą automatycznie ponownie otwierali linki AMQP, które następnie spowodują pobranie zmienionych informacji o metadanych. Następnie klienci działają normalnie.

### <a name="senderproducer-clients"></a>Klienci nadawcy/producenci
Event Hubs oferuje trzy opcje nadawcy:

- **Nadawca partycji** — w tym scenariuszu klienci wysyłają zdarzenia bezpośrednio do partycji. Chociaż partycje są identyfikowane, a zdarzenia mogą być wysyłane bezpośrednio do nich, nie zalecamy tego wzorca. Dodawanie partycji nie ma wpływu na ten scenariusz. Zalecamy ponowne uruchomienie aplikacji, aby mogły wykrywać nowo dodane partycje. 
- Identyfikator **nadawcy klucza partycji** — w tym scenariuszu klienci wysyłają zdarzenia z kluczem, aby wszystkie zdarzenia należące do tego klucza kończyły się w tej samej partycji. W takim przypadku usługa skrótuje klucz i trasy do odpowiedniej partycji. Aktualizacja liczby partycji może powodować problemy z nieoczekiwaną kolejnością ze względu na zmianę skrótu. Tak więc, jeśli planujesz porządkowanie, upewnij się, że aplikacja zużywa wszystkie zdarzenia z istniejących partycji przed zwiększeniem liczby partycji.
- Nadawca działający w trybie okrężnym **(domyślnie)** — w tym scenariuszu Usługa Event Hubs Round Robins zdarzenia w partycjach, a także używa algorytmu równoważenia obciążenia. Usługa Event Hubs ma świadomość zmian liczby partycji i będzie wysyłać do nowych partycji w ciągu kilku sekund od zmiany liczby partycji.

### <a name="receiverconsumer-clients"></a>Klienci odbiorcy/odbiorcy
Event Hubs zapewnia bezpośrednich odbiorników i łatwą bibliotekę użytkowników nazywaną [hostem procesora zdarzeń (starym zestawem SDK)](event-hubs-event-processor-host.md)  lub [procesorem zdarzeń (nowy zestaw SDK)](event-processor-balance-partition-load.md).

- **Odbiorniki bezpośrednie** — odbiorniki bezpośrednie nasłuchują określonych partycji. Nie ma to żadnego oddziaływania na działanie środowiska uruchomieniowego, gdy partycje są skalowane dla centrum zdarzeń. Aplikacja, która korzysta z bezpośrednich odbiorników, musi zwrócić uwagę na pobranie nowych partycji i przydzielenie odpowiednio odbiorców.
- **Host procesora zdarzeń** — ten klient nie odświeża automatycznie metadanych jednostki. W związku z tym nie można pobrać w celu zwiększenia liczby partycji. Ponowne utworzenie wystąpienia procesora zdarzeń spowoduje pobranie metadanych jednostki, co z kolei spowoduje utworzenie nowych obiektów BLOB dla nowo dodanych partycji. Nie ma to wpływu na istniejące obiekty blob. Zalecane jest ponowne uruchomienie wszystkich wystąpień procesora zdarzeń, aby upewnić się, że wszystkie wystąpienia są świadome nowo dodanych partycji, a Równoważenie obciążenia jest prawidłowo obsługiwane wśród klientów.

    Jeśli używasz starej wersji zestawu .NET SDK ([windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)), Host procesora zdarzeń usuwa istniejący punkt kontrolny po ponownym uruchomieniu, jeśli liczba partycji w punkcie kontrolnym nie jest zgodna z liczbą partycji pobraną z usługi. Takie zachowanie może mieć wpływ na aplikację. 

## <a name="apache-kafka-clients"></a>Apache Kafka klienci
W tej sekcji opisano sposób, w jaki Apache Kafka klienci korzystający z punktu końcowego Kafka systemu Azure Event Hubs zachowywać się, gdy liczba partycji jest zaktualizowana dla centrum zdarzeń. 

Klienci Kafka używający Event Hubs z protokołem Apache Kafka zachowują się inaczej od klientów centrum zdarzeń, którzy korzystają z protokołu AMQP. Kafka klienci aktualizują swoje metadane co `metadata.max.age.ms` milisekundy. Ta wartość jest określana w konfiguracjach klientów. `librdkafka`Biblioteki również używają tej samej konfiguracji. Aktualizacje metadanych informują klientów o zmianach w usłudze, w tym o zwiększeniu liczby partycji. Aby zapoznać się z listą konfiguracji, zobacz [Apache Kafka konfiguracjami Event Hubs](apache-kafka-configurations.md).

### <a name="senderproducer-clients"></a>Klienci nadawcy/producenci
Producenci zawsze mówią, że żądania wysłania zawierają miejsce docelowe partycji dla każdego zestawu produkowanych rekordów. W związku z tym wszystkie tworzenie partycjonowania odbywa się na stronie klienta z widokiem metadanych brokera. Po dodaniu nowych partycji do widoku metadanych producenta będą one dostępne dla żądań producentów.

### <a name="consumerreceiver-clients"></a>Klienci odbiorcy/odbiorcy
Gdy członek grupy odbiorców wykonuje odświeżanie metadanych i pobiera nowo utworzone partycje, ten członek inicjuje ponowne saldo grupy. Metadane klienta zostaną odświeżone dla wszystkich członków grupy, a nowe partycje zostaną przypisane przez przydzielony lidera ponownego równoważenia.

## <a name="recommendations"></a>Zalecenia

- Jeśli używasz klucza partycji z aplikacjami producenta i zależą od tworzenia skrótu klucza w celu zapewnienia uporządkowania partycji, dynamiczne dodawanie partycji nie jest zalecane. 

    > [!IMPORTANT]
    > Podczas gdy istniejące dane zachowują kolejność, skrót partycji zostanie przerwany w przypadku komunikatów, które są szyfrowane po zmianie liczby partycji ze względu na dodanie partycji.
- Dodawanie partycji do istniejącego tematu lub wystąpienia centrum zdarzeń jest zalecane w następujących przypadkach:
    - W przypadku użycia domyślnej metody wysyłania zdarzeń
     - Kafka domyślne strategie partycjonowania, przykład — strategia przypisywania do programu Sticky Notes


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji o partycjach, zobacz [partycje](event-hubs-scalability.md#partitions).

