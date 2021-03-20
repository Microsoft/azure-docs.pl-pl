---
title: Reagowanie na zdarzenia Azure Maps przy użyciu Event Grid
description: Dowiedz się, jak reagować na zdarzenia Azure Maps obejmujące geoogrodzenie. Zobacz, jak nasłuchiwać zdarzeń mapy i jak używać Event Grid do przekierowywania zdarzeń do programów obsługi zdarzeń.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/16/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 44b2cab814d4e51c4eb0c3ce3322b898f5b26414
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92889770"
---
# <a name="react-to-azure-maps-events-by-using-event-grid"></a>Reagowanie na zdarzenia Azure Maps przy użyciu Event Grid

Azure Maps integruje się z Azure Event Grid, dzięki czemu użytkownicy mogą wysyłać powiadomienia o zdarzeniach do innych usług i wyzwalać procesy podrzędne. Celem tego artykułu jest ułatwienie konfigurowania aplikacji firmowych do nasłuchiwania zdarzeń Azure Maps. Dzięki temu użytkownicy mogą reagować na krytyczne zdarzenia w niezawodny, skalowalny i bezpieczny sposób. Na przykład użytkownicy mogą utworzyć aplikację w celu zaktualizowania bazy danych, utworzenia biletu i dostarczenia powiadomienia e-mail za każdym razem, gdy urządzenie przejdzie do geofencingu.

Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania/subskrybowania. Event Grid ma wbudowaną obsługę usług platformy Azure, takich jak [Azure Functions](../azure-functions/functions-overview.md) i [Azure Logic Apps](../azure-functions/functions-overview.md). Może ona dostarczać alerty zdarzeń do usług innych niż Azure przy użyciu elementów webhook. Aby zapoznać się z pełną listą programów obsługi zdarzeń obsługiwanych przez Event Grid, zobacz [wprowadzenie do Azure Event Grid](../event-grid/overview.md).


![Azure Event Grid model funkcjonalny](./media/azure-maps-event-grid-integration/azure-event-grid-functional-model.png)


## <a name="azure-maps-events-types"></a>Typy zdarzeń Azure Maps

Funkcja Event Grid używa [subskrypcji zdarzeń](../event-grid/concepts.md#event-subscriptions) do kierowania komunikatów o zdarzeniach do subskrybentów. Konto Azure Maps emituje następujące typy zdarzeń: 

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. Maps. GeofenceEntered | Wywoływane, gdy odebrane współrzędne przeniesiono spoza danego elementu geołożonego do wewnątrz |
| Microsoft. Maps. GeofenceExited | Wywoływane, gdy odebrane współrzędne przeniesiono z wewnątrz danego elementu geołożonego do zewnątrz |
| Microsoft. Maps. GeofenceResult | Wywoływane za każdym razem, gdy zapytanie geofencingu zwraca wynik, niezależnie od stanu |

## <a name="event-schema"></a>Schemat zdarzeń

W poniższym przykładzie przedstawiono schemat dla GeofenceResult:

```JSON
{
    "id":"451675de-a67d-4929-876c-5c2bf0b2c000",
    "topic":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Maps/accounts/{accountName}",
    "subject":"/spatial/geofence/udid/{udid}/id/{eventId}",
    "data":{
        "geometries":[
            {
                "deviceId":"device_1",
                "udId":"1a13b444-4acf-32ab-ce4e-9ca4af20b169",
                "geometryId":"1",
                "distance":999.0,
                "nearestLat":47.609833,
                "nearestLon":-122.148274
            }
        ],
        "expiredGeofenceGeometryId":[
        ],
        "invalidPeriodGeofenceGeometryId":[
        ]
    },
    "eventType":"Microsoft.Maps.GeofenceResult",
    "eventTime":"2018-11-08T00:52:08.0954283Z",
    "metadataVersion":"1",
    "dataVersion":"1.0"
}

```

## <a name="tips-for-consuming-events"></a>Porady dotyczące korzystania ze zdarzeń

Aplikacje, które obsługują zdarzenia geofencingu Azure Maps powinny spełniać kilka zalecanych praktyk:

* Skonfiguruj wiele subskrypcji, aby kierować zdarzenia do tego samego programu obsługi zdarzeń. Ważne jest, aby nie założyć, że zdarzenia pochodzą z określonego źródła. Zawsze sprawdzaj temat wiadomości, aby upewnić się, że wiadomość pochodzi od oczekiwanego źródła.
* Użyj `X-Correlation-id` pola w nagłówku odpowiedzi, aby zrozumieć, czy informacje o obiektach są aktualne. Komunikaty mogą przychodzić w niewłaściwej kolejności lub z opóźnieniem.
* Gdy żądanie GET lub POST w interfejsie API geoogrodzenia jest wywoływane z parametrem Mode ustawionym na `EnterAndExit` , to zdarzenie Enter lub Exit jest generowane dla każdej geometrii w obszarze geoogrodzenia, dla którego stan zmienił się z poprzedniego wywołania interfejsu API z geoogrodzeniem.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o sposobach kontrolowania operacji w lokacji konstrukcja przy użyciu geofencingu, zobacz:

> [!div class="nextstepaction"] 
> [Konfigurowanie geofencingu przy użyciu usługi Azure Maps](tutorial-geofence.md)