---
title: Informacje na temat niestandardowych punktów końcowych usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — używanie zapytań routingu do przesyłania komunikatów z urządzenia do chmury do niestandardowych punktów końcowych.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 8aebb5b6f6a3ac53bc49fd1d2f75de88667865fb
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147637"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Używanie tras komunikatów i niestandardowych punktów końcowych dla komunikatów z urządzenia do chmury

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub [Routing komunikatów](iot-hub-devguide-routing-query-syntax.md) umożliwia użytkownikom kierowanie komunikatów przesyłanych z urządzenia do chmury do punktów końcowych dostępnych dla usługi. Routing udostępnia również funkcję wykonywania zapytań, która umożliwia filtrowanie danych przed ich kierowaniem do punktów końcowych. Każde skonfigurowane zapytanie routingu ma następujące właściwości:

| Właściwość      | Opis |
| ------------- | ----------- |
| **Nazwa**      | Unikatowa nazwa identyfikująca zapytanie. |
| **Element źródłowy**    | Pochodzenie strumienia danych, z którym ma zostać podjęta próba. Na przykład dane telemetryczne urządzenia. |
| **Warunek** | Wyrażenie zapytania dla kwerendy routingu, która jest uruchamiana względem właściwości aplikacji komunikatów, właściwości systemu, treści wiadomości, tagów sznurów urządzenia i właściwości sznurów urządzenia, aby określić, czy jest to dopasowanie do punktu końcowego. Aby uzyskać więcej informacji na temat konstruowania zapytania, zobacz [Składnia zapytania dotyczącego routingu komunikatów](iot-hub-devguide-routing-query-syntax.md) |
| **Punkt końcowy**  | Nazwa punktu końcowego, w którym IoT Hub wysyła wiadomości pasujące do zapytania. Zalecamy wybranie punktu końcowego w tym samym regionie, w którym znajduje się centrum IoT. |

Pojedynczy komunikat może być zgodny z warunkiem na wielu zapytaniach routingu, A w takim przypadku IoT Hub dostarcza komunikat do punktu końcowego skojarzonego z każdym dopasowanym zapytaniem. IoT Hub również automatycznie deduplikowania dostarczania komunikatów, dlatego jeśli komunikat jest zgodny z wieloma zapytaniami, które mają takie same miejsce docelowe, jest zapisywana tylko raz w tym miejscu docelowym.

## <a name="endpoints-and-routing"></a>Punkty końcowe i Routing

Centrum IoT Hub ma domyślny [wbudowany punkt końcowy](iot-hub-devguide-messages-read-builtin.md). Można utworzyć niestandardowe punkty końcowe, do których będą kierowane komunikaty, łącząc inne usługi w ramach subskrypcji z centrum. IoT Hub obecnie obsługuje kontenery usługi Azure Storage, Event Hubs, kolejki Service Bus i tematy Service Bus jako niestandardowe punkty końcowe.

W przypadku używania routingu i niestandardowych punktów końcowych komunikaty są dostarczane tylko do wbudowanego punktu końcowego, jeśli nie pasują do żadnego zapytania. Aby dostarczać komunikaty do wbudowanego punktu końcowego oraz do niestandardowego punktu końcowego, Dodaj trasę, która wysyła komunikaty do wbudowanego punktu końcowego **zdarzeń** .

> [!NOTE]
> * IoT Hub obsługuje zapisywanie danych w kontenerach usługi Azure Storage jako obiekty blob.
> * Service Bus kolejki i tematy z włączonymi **sesjami** lub **wykrywaniem duplikatów** nie są obsługiwane jako niestandardowe punkty końcowe.

Aby uzyskać więcej informacji na temat tworzenia niestandardowych punktów końcowych w IoT Hub, zobacz [IoT Hub punktów końcowych](iot-hub-devguide-endpoints.md).

Aby uzyskać więcej informacji na temat odczytywania z niestandardowych punktów końcowych, zobacz:

* Odczytywanie z [kontenerów usługi Azure Storage](../storage/blobs/storage-blobs-introduction.md).

* Odczytywanie z [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

* Odczytywanie z [kolejek Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

* Odczytywanie z [Service Bus tematów](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji o IoT Hub punktach końcowych, zobacz [IoT Hub punktów końcowych](iot-hub-devguide-endpoints.md).

* Aby uzyskać więcej informacji o języku zapytań używanym do definiowania kwerend routingu, zobacz [Składnia zapytania dotyczącego routingu komunikatów](iot-hub-devguide-routing-query-syntax.md).

* [Proces IoT Hub komunikatów z urządzenia do chmury przy użyciu usługi Routes](tutorial-routing.md) pokazuje, jak używać zapytań routingu i niestandardowych punktów końcowych.