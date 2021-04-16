---
title: Opis Azure IoT Hub niestandardowych punktów końcowych | Microsoft Docs
description: Przewodnik dla deweloperów — kierowanie komunikatów z urządzenia do chmury do niestandardowych punktów końcowych przy użyciu zapytań routingu.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 4ad57473e0950f031fbeadee2302f85557ed526f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388265"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Używanie tras komunikatów i niestandardowych punktów końcowych dla komunikatów wysyłanych z urządzenia do chmury

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [komunikatów umożliwia](iot-hub-devguide-routing-query-syntax.md) użytkownikom kierowanie komunikatów z urządzenia do chmury do punktów końcowych umożliwiających korzystanie z usługi. Routing zapewnia również możliwość wykonywania zapytań w celu filtrowania danych przed ich kierowaniem do punktów końcowych. Każde skonfigurowane zapytanie routingu ma następujące właściwości:

| Właściwość      | Opis |
| ------------- | ----------- |
| **Nazwa**      | Unikatowa nazwa identyfikująca zapytanie. |
| **Element źródłowy**    | Źródło strumienia danych, na który mają być przetwarzane działania. Na przykład telemetria urządzenia. |
| **Warunek** | Wyrażenie zapytania routingu, które jest uruchamiane względem właściwości aplikacji komunikatów, właściwości systemu, treści komunikatu, tagów bliźniaczej reprezentacji urządzenia i właściwości bliźniaczej reprezentacji urządzenia w celu określenia, czy jest to dopasowanie dla punktu końcowego. Aby uzyskać więcej informacji na temat tworzenia zapytania, zobacz składnię zapytania [routingu komunikatów](iot-hub-devguide-routing-query-syntax.md) |
| **Punkt końcowy**  | Nazwa punktu końcowego, do którego IoT Hub komunikaty zgodne z zapytaniem. Zalecamy wybranie punktu końcowego w tym samym regionie co centrum IoT. |

Pojedynczy komunikat może odpowiadać warunku w wielu zapytaniach routingu. W takim przypadku IoT Hub komunikat do punktu końcowego skojarzonego z każdym dopasowane zapytaniem. IoT Hub automatycznie deduplikuje dostarczanie komunikatów, więc jeśli komunikat pasuje do wielu zapytań o tym samym miejscu docelowym, jest zapisywany tylko raz w tym miejscu docelowym.

## <a name="endpoints-and-routing"></a>Punkty końcowe i routing

Centrum IoT ma domyślny [wbudowany punkt końcowy](iot-hub-devguide-messages-read-builtin.md). Możesz utworzyć niestandardowe punkty końcowe, do których będą kierowane komunikaty, łącząc inne usługi w subskrypcjach, których jesteś właścicielem, z centrum. IoT Hub obecnie obsługuje kontenery usługi Azure Storage, Event Hubs, Service Bus kolejki i Service Bus jako niestandardowe punkty końcowe.

W przypadku korzystania z routingu i niestandardowych punktów końcowych komunikaty są dostarczane do wbudowanego punktu końcowego tylko wtedy, gdy nie są zgodne z żadnym zapytaniem. Aby dostarczać komunikaty do wbudowanego punktu końcowego, a także do niestandardowego punktu końcowego, dodaj trasę, która wysyła komunikaty do wbudowanego punktu **końcowego** zdarzeń.

> [!NOTE]
> * IoT Hub tylko zapisywanie danych w kontenerach usługi Azure Storage jako obiektów blob.
> * Service Bus i tematy z **włączonymi**  sesjami lub wykrywaniem duplikatów nie są obsługiwane jako niestandardowe punkty końcowe.
> * W Azure Portal można tworzyć niestandardowe punkty końcowe routingu tylko do zasobów platformy Azure, które znajdują się w tej samej subskrypcji co centrum. Możesz tworzyć niestandardowe punkty końcowe dla zasobów w innych subskrypcjach, których jesteś właścicielem, ale niestandardowe punkty końcowe muszą być skonfigurowane przy użyciu innej metody niż Azure Portal.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych punktów końcowych w IoT Hub, zobacz [IoT Hub endpoints](iot-hub-devguide-endpoints.md).

Aby uzyskać więcej informacji na temat odczytywania z niestandardowych punktów końcowych, zobacz:

* Odczytywanie z [kontenerów usługi Azure Storage.](../storage/blobs/storage-blobs-introduction.md)

* Odczytywanie z [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Odczytywanie [z Service Bus kolejki.](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Odczytywanie [z Service Bus tematach.](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat IoT Hub końcowych, zobacz [IoT Hub końcowych](iot-hub-devguide-endpoints.md).

* Aby uzyskać więcej informacji o języku zapytań używanym do definiowania zapytań routingu, zobacz Składnia zapytań [routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

* W [samouczku Process IoT Hub device-to-cloud messages using routes (Przetwarzanie](tutorial-routing.md) komunikatów z urządzenia do chmury przy użyciu tras) pokazano, jak używać zapytań routingu i niestandardowych punktów końcowych.
