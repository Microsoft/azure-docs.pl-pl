---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 01/21/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 44afd8ea4ef2ab06ec31b7528e9776faebc3b4dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98689622"
---
### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jakie porty muszę otworzyć na zaporze? 
Do wysyłania i odbierania zdarzeń można użyć następujących protokołów z usługą Azure Event Hubs:

- Advanced Message Queuing Protocol 1,0 (AMQP)
- Protokół HTTP (Hypertext Transfer Protocol) 1,1 z protokołem TLS (HTTPS)
- Apache Kafka

Zapoznaj się z poniższą tabelą dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikowania się z usługą Azure Event Hubs. 

| Protokół | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [Przewodnik po protokole AMQP](../articles/service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTPS | 443 | Ten port jest używany dla interfejsu API protokołu HTTP/REST i dla gniazd AMQP-over-WebSockets. |
| Kafka | 9093 | Zobacz [używanie Event Hubs z aplikacji Kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md)

Port HTTPS jest wymagany do komunikacji wychodzącej również wtedy, gdy AMQP jest używany przez port 5671, ponieważ kilka operacji zarządzania wykonywanych przez zestawy SDK klienta i pozyskiwania tokenów z Azure Active Directory (gdy są używane) działają za pośrednictwem protokołu HTTPS. 

Oficjalne zestawy Azure SDK zwykle używają protokołu AMQP do wysyłania i otrzymywania zdarzeń z Event Hubs. Opcja protokołu AMQP-over-WebSockets jest uruchamiana przez port TCP 443, podobnie jak w przypadku interfejsu API protokołu HTTP, ale w przeciwnym razie jest taka sama jak w przypadku zwykłego AMQP. Ta opcja ma większe opóźnienie połączeń początkowych ze względu na dodatkowe podróże i nieco bardziej narzuty jako kompromis do udostępniania portu HTTPS. W przypadku wybrania tego trybu port TCP 443 jest wystarczający do komunikacji. Poniższe opcje umożliwiają wybranie trybu AMQP lub AMQP obiektów WebSockets:

| Język | Opcja   |
| -------- | ----- |
| .NET     | [EventHubConnectionOptions. TransportType](/dotnet/api/azure.messaging.eventhubs.eventhubconnectionoptions.transporttype) właściwość z [EventHubsTransportType. AmqpTcp](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) lub [EventHubsTransportType. AmqpWebSockets](/dotnet/api/azure.messaging.eventhubs.eventhubstransporttype) |
| Java     | [com. Microsoft. Azure. eventhubs. EventProcessorClientBuilder. TransportType](/java/api/com.azure.messaging.eventhubs.eventprocessorclientbuilder.transporttype) z [AmqpTransportType. AMQP](/java/api/com.azure.core.amqp.amqptransporttype) lub [AmqpTransportType.AMQP_WEB_SOCKETS](/java/api/com.azure.core.amqp.amqptransporttype) |
| Węzeł  | [EventHubConsumerClientOptions](/javascript/api/@azure/event-hubs/eventhubconsumerclientoptions) ma `webSocketOptions` Właściwość. |
| Python | [EventHubConsumerClient.transport_type](/python/api/azure-eventhub/azure.eventhub.eventhubconsumerclient) z [transportem. AMQP](/python/api/azure-eventhub/azure.eventhub.transporttype) lub [TransportType. AmqpOverWebSocket](/python/api/azure-eventhub/azure.eventhub.transporttype) |

### <a name="what-ip-addresses-do-i-need-to-allow"></a>Jakie adresy IP muszę zezwalać?
Podczas pracy z platformą Azure czasami trzeba zezwolić na określone zakresy adresów IP lub adresy URL w firmowej zaporze lub serwerze proxy, aby uzyskać dostęp do wszystkich usług platformy Azure, których używasz lub których próbujesz użyć. Sprawdź, czy ruch jest dozwolony dla adresów IP używanych przez Event Hubs. Adresy IP używane przez usługę Azure Event Hubs: zobacz [zakres adresów IP platformy Azure i Tagi usług — chmura publiczna](https://www.microsoft.com/download/details.aspx?id=56519).

Sprawdź również, czy adres IP dla przestrzeni nazw jest dozwolony. Aby znaleźć odpowiednie adresy IP w celu zezwolenia na połączenia, wykonaj następujące kroki:

1. Uruchom następujące polecenie w wierszu polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres IP zwrócony w `Non-authoritative answer` . 

Jeśli używasz **nadmiarowości strefy** dla przestrzeni nazw, musisz wykonać kilka dodatkowych czynności: 

1. Najpierw uruchom polecenie nslookup w przestrzeni nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w sekcji **Nieautorytatywna odpowiedź** , która znajduje się w jednym z następujących formatów: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Uruchom polecenie nslookup dla każdego z sufiksów S1, S2 i S3, aby uzyskać adresy IP wszystkich trzech wystąpień uruchomionych w trzech strefach dostępności, 

    > [!NOTE]
    > Adres IP zwrócony przez `nslookup` polecenie nie jest statycznym adresem IP. Jednak pozostaje stałą do momentu usunięcia lub przeniesienia bazowego wdrożenia do innego klastra.

### <a name="what-client-ips-are-sending-events-to-or-receiving-events-from-my-namespace"></a>Jakie adresy IP klientów wysyłają zdarzenia do zdarzeń z mojej przestrzeni nazw lub je odbierały?
Najpierw włącz [filtrowanie adresów IP](../articles/event-hubs/event-hubs-ip-filtering.md) w przestrzeni nazw. 

Następnie Włącz dzienniki diagnostyczne dla [zdarzeń połączeń sieci wirtualnej Event Hubs](../articles/event-hubs/event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) , wykonując instrukcje podane w temacie [Włączanie dzienników diagnostycznych](../articles/event-hubs/event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Zobaczysz adres IP, dla którego odmówiono nawiązania połączenia.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

> [!IMPORTANT]
> Dzienniki sieci wirtualnej są generowane tylko wtedy, gdy przestrzeń nazw zezwala na dostęp z **określonych adresów IP** (reguł filtrów IP). Jeśli nie chcesz ograniczać dostępu do przestrzeni nazw przy użyciu tych funkcji i nadal chcesz uzyskać dzienniki sieci wirtualnych do śledzenia adresów IP klientów łączących się z przestrzenią nazw Event Hubs, możesz użyć następującego obejścia: Włącz filtrowanie adresów IP i Dodaj zakres adresów IPv4 (1.0.0.0/1-255.0.0.0/1). Event Hubs nie obsługuje zakresów adresów IPv6. 

> [!NOTE]
> Obecnie nie jest możliwe ustalenie źródłowego adresu IP pojedynczej wiadomości lub zdarzenia. 