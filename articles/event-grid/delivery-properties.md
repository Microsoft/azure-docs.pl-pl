---
title: Azure Event Grid — Ustawianie nagłówków niestandardowych dla dostarczonych zdarzeń
description: Opisuje, jak można ustawić niestandardowe nagłówki (lub właściwości dostarczania) dla dostarczonych zdarzeń.
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: e2eff6b0d1dc78f0d558bb8e4e1ad79c62c52657
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630530"
---
# <a name="delivery-with-custom-headers"></a>Dostarczanie z nagłówkami niestandardowymi
Subskrypcje zdarzeń umożliwiają konfigurowanie nagłówków HTTP, które są uwzględnione w dostarczonych zdarzeniach. Ta funkcja umożliwia ustawienie niestandardowych nagłówków, które są wymagane przez miejsce docelowe. Podczas tworzenia subskrypcji zdarzeń można skonfigurować maksymalnie 10 nagłówków. Każda wartość nagłówka nie powinna być większa niż 4 096 (4K) bajtów.

Można ustawić niestandardowe nagłówki dla zdarzeń, które są dostarczane do następujących miejsc docelowych:

- Elementy webhook
- Tematy Azure Service Bus i kolejki
- Azure Event Hubs
- Połączenia hybrydowe przekaźnika

Podczas tworzenia subskrypcji zdarzeń w Azure Portal można użyć karty **Właściwości dostarczania** do ustawienia niestandardowych nagłówków HTTP. Ta strona umożliwia ustawianie stałych i dynamicznych wartości nagłówków.

## <a name="setting-static-header-values"></a>Ustawianie statycznych wartości nagłówka
Aby ustawić nagłówki o stałej wartości, podaj nazwę nagłówka i jego wartość w odpowiednich polach:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Właściwości dostarczania-static":::

Możesz chcieć sprawdzić, czy **jest to wpis tajny?** podczas udostępniania poufnych danych. Dane poufne nie będą wyświetlane na Azure Portal. 

## <a name="setting-dynamic-header-values"></a>Ustawianie dynamicznych wartości nagłówka
Możesz ustawić wartość nagłówka na podstawie właściwości w zdarzeniu przychodzącym. Użyj składni wykryto, aby odwołać się do wartości właściwości przychodzącego zdarzenia, która ma być używana jako wartość nagłówka w żądaniach wychodzących. Na przykład, aby ustawić wartość nagłówka o nazwie **Channel** przy użyciu wartości **systemu** przychodzącej właściwości zdarzenia w danych zdarzenia, skonfiguruj subskrypcję zdarzenia w następujący sposób:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Właściwości dostarczania — dynamiczne":::

## <a name="examples"></a>Przykłady
Ta sekcja zawiera kilka przykładów użycia właściwości dostarczania.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Ustawianie nagłówka autoryzacji z tokenem okaziciela (przykładem innym niż normatywny)

Ustaw wartość na nagłówek autoryzacji, aby zidentyfikować żądanie z obsługą elementu webhook. Nagłówek autoryzacji można ustawić, jeśli nie [chronisz elementu webhook przy użyciu Azure Active Directory](secure-webhook-delivery.md).

| Nazwa nagłówka   | Typ nagłówka | Wartość nagłówka |
| :--           | :--         | :--            |
|`Authorization` | Static | `BEARER SlAV32hkKG...`|

Żądania wychodzące powinny teraz zawierać nagłówek ustawiony w subskrypcji zdarzenia:

```console
GET /home.html HTTP/1.1

Host: acme.com

User-Agent: <user-agent goes here>

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> Definiowanie nagłówków autoryzacji jest rozsądną opcją, gdy miejscem docelowym jest element webhook. Nie powinno być używane w przypadku [funkcji subskrybowanych przy użyciu identyfikatora zasobu](/rest/api/eventgrid/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs i połączenia hybrydowe jako te lokalizacje docelowe obsługują własne schematy uwierzytelniania w przypadku użycia z Event Grid.

### <a name="service-bus-example"></a>Przykład Service Bus
Azure Service Bus obsługuje użycie [nagłówka HTTP BrokerProperties](/rest/api/servicebus/message-headers-and-properties#message-headers) do definiowania właściwości komunikatów podczas wysyłania pojedynczych komunikatów. Wartość `BrokerProperties` nagłówka powinna być podana w formacie JSON. Na przykład, jeśli trzeba ustawić właściwości komunikatu podczas wysyłania pojedynczej wiadomości do Service Bus, należy ustawić nagłówek w następujący sposób:

| Nazwa nagłówka | Typ nagłówka | Wartość nagłówka |
| :-- | :-- | :-- |
|`BrokerProperties` | Static     | `BrokerProperties:  { "MessageId": "{701332E1-B37B-4D29-AA0A-E367906C206E}", "TimeToLive" : 90}` |


### <a name="event-hubs-example"></a>Przykład Event Hubs

Jeśli musisz opublikować zdarzenia na określonej partycji w centrum zdarzeń, zdefiniuj [nagłówek HTTP BrokerProperties](/rest/api/eventhub/event-hubs-runtime-rest#common-headers) w subskrypcji zdarzeń, aby określić klucz partycji identyfikujący docelową partycję centrum zdarzeń.

| Nazwa nagłówka | Typ nagłówka | Wartość nagłówka                                  |
| :-- | :-- | :-- |
|`BrokerProperties` | Static | `BrokerProperties: {"PartitionKey": "0000000000-0000-0000-0000-000000000000000"}`  |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Skonfiguruj czas wygaśnięcia zdarzeń wychodzących w kolejkach usługi Azure Storage
W przypadku lokalizacji docelowej kolejek usługi Azure Storage można skonfigurować tylko czas wygaśnięcia wiadomości wychodzącej, która będzie miała miejsce po dostarczeniu do kolejki usługi Azure Storage. Jeśli nie podano czasu, domyślny czas wygaśnięcia komunikatu wynosi 7 dni. Możesz również ustawić zdarzenie, aby nigdy nie wygasa.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Właściwości dostarczania — Kolejka magazynu":::

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat dostarczania zdarzeń, zobacz następujący artykuł:

- [Dostarczanie i ponawianie prób](delivery-and-retry.md)
- [Dostarczanie zdarzeń elementów webhook](webhook-event-delivery.md)
- [Filtrowanie zdarzeń](event-filtering.md)
