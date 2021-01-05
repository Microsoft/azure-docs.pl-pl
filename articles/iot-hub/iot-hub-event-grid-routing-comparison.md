---
title: Porównaj Event Grid, routing dla IoT Hub | Microsoft Docs
description: IoT Hub oferuje własną usługę routingu komunikatów, ale integruje się także z Event Grid na potrzeby publikowania zdarzeń. Porównaj te dwie funkcje.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 37b411792ea1a3e21e0f26df0c7905eb8d46310e
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97897709"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Porównanie routingu komunikatów i Event Grid dla IoT Hub

Usługa Azure IoT Hub oferuje możliwość przesyłania strumieniowego danych z połączonych urządzeń i integrowania tych danych z aplikacjami biznesowymi. IoT Hub oferuje dwie metody integracji zdarzeń IoT z innymi usługami platformy Azure lub aplikacjami biznesowymi. W tym artykule omówiono dwie funkcje zapewniające tę możliwość, dzięki czemu można wybrać, która opcja jest Najlepsza dla danego scenariusza.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub Routing komunikatów](iot-hub-devguide-messages-d2c.md)**: ta funkcja IoT Hub umożliwia użytkownikom kierowanie komunikatów przesyłanych z urządzenia do chmury do punktów końcowych usługi, takich jak kontenery Azure Storage, Event Hubs, Service Bus Queues i Service Bus. Routing udostępnia również funkcję wykonywania zapytań, która umożliwia filtrowanie danych przed ich kierowaniem do punktów końcowych. Oprócz danych telemetrycznych urządzenia można także wysyłać [zdarzenia telemetrii](iot-hub-devguide-messages-d2c.md#non-telemetry-events) , które mogą być używane do wyzwalania akcji. 

**IoT Hub integrację z Event Grid**: Azure Event Grid to w pełni zarządzana usługa routingu zdarzeń, która używa modelu publikowania/subskrybowania. IoT Hub i Event Grid współpracują ze sobą, aby [zintegrować IoT Hub zdarzeń z platformą Azure i usługami](iot-hub-event-grid.md)nienależącymi do platformy Azure w czasie niemal rzeczywistym. IoT Hub publikuje zarówno [zdarzenia dotyczące urządzeń](iot-hub-event-grid.md#event-types) , jak i zdarzenia telemetryczne.

## <a name="differences"></a>Różnice

Chociaż zarówno Routing komunikatów, jak i Event Grid włączają konfigurację alertów, istnieją pewne kluczowe różnice między nimi. Szczegółowe informacje znajdują się w poniższej tabeli:

| Cechy | IoT Hub Routing komunikatów | IoT Hub integrację z usługą Event Grid |
| ------- | --------------- | ---------- |
| **Komunikaty i zdarzenia urządzenia** | Tak, routing komunikatów może być używany na potrzeby danych telemetrii, zmian dotyczących sznurów urządzeń, zdarzeń cyklu życia urządzenia i cyfrowych sznurów zmiany. | Tak, Event Grid mogą być używane na potrzeby danych telemetrycznych i zdarzeń urządzeń, takich jak urządzenie utworzone/usunięte/połączone/rozłączone. Nie można jednak używać usługi Event Grid na potrzeby zdarzeń zmiany sznurka urządzenia i zdarzeń zmiany cyfrowych sznurów. |
| **Zamawianie** | Tak, porządkowanie zdarzeń jest zachowywane.  | Nie, kolejność zdarzeń nie jest gwarantowana. | 
| **Filtrowanie** | Zaawansowane filtrowanie właściwości aplikacji komunikatów, właściwości systemu komunikatów, treści wiadomości, znaczniki sznurka urządzenia i właściwości sznurów urządzeń. Filtrowanie nie jest stosowane do wieloosiowych zdarzeń zmiany. Aby zapoznać się z przykładami, zobacz [Składnia zapytania dotyczącego routingu komunikatów](iot-hub-devguide-routing-query-syntax.md). | Filtrowanie na podstawie typu zdarzenia, typu podmiotu i atrybutów w każdym zdarzeniu. Aby zapoznać się z przykładami, zobacz [Opis filtrowania zdarzeń w Event Grid subskrypcje](../event-grid/event-filtering.md). Podczas subskrybowania zdarzeń telemetrii można zastosować dodatkowe filtry do danych w celu filtrowania właściwości komunikatów, treści wiadomości i sznurów urządzeń w IoT Hub przed opublikowaniem w Event Grid. Zobacz [Jak filtrować zdarzenia](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Punkty końcowe** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Kolejka usługi Service Bus</li> <li>Tematy usługi Service Bus</li></ul><br>Płatne IoT Hub jednostki SKU (S1, S2 i S3) są ograniczone do 10 niestandardowych punktów końcowych. 100 tras można utworzyć na IoT Hub. | <ul><li>Azure Functions</li> <li>Usługa Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Tematy niestandardowe</li> <li>Queue Storage</li> <li>Power Automate</li> <li>Usługi innych firm za poorednictwem elementów webhook</li></ul><br>500 punktów końcowych na IoT Hub są obsługiwane. Aby uzyskać najbardziej aktualną listę punktów końcowych, zobacz [Event Grid obsługi zdarzeń](../event-grid/overview.md#event-handlers). |
| **Koszty** | Nie ma oddzielnej opłaty za Routing komunikatów. Opłata jest naliczana tylko za transfer danych telemetrycznych do IoT Hub. Na przykład jeśli masz komunikat kierowany do trzech różnych punktów końcowych, opłaty są naliczane tylko za jeden komunikat. | Nie jest naliczana opłata od IoT Hub. Event Grid oferuje bezpłatnie pierwszych 100 000 operacji miesięcznie, a następnie $0,60 za milion operacji. |

## <a name="similarities"></a>Podobieństwa

IoT Hub Routing komunikatów i Event Grid mają podobne podobieństwa, ale niektóre z nich opisano szczegółowo w poniższej tabeli:

| Cechy | IoT Hub Routing komunikatów | IoT Hub integrację z usługą Event Grid |
| ------- | --------------- | ---------- |
| **Maksymalny rozmiar komunikatu** | 256 KB, urządzenie-chmura | 256 KB, urządzenie-chmura |
| **Niezawodność** | Wysoka: dostarcza każdy komunikat do punktu końcowego co najmniej raz dla każdej trasy. Wygaśnie wszystkie komunikaty, które nie zostały dostarczone w ciągu godziny. | Wysoka: dostarcza każdy komunikat do elementu webhook co najmniej raz dla każdej subskrypcji. Wygaśnie wszystkie zdarzenia, które nie zostały dostarczone w ciągu 24 godzin. | 
| **Skalowalność** | Wysoka: zoptymalizowane pod kątem obsługi milionów komunikatów jednocześnie połączonych urządzeń wysyłających miliardy wiadomości. | Wysoka: możliwość routingu zdarzeń 10 000 000 na sekundę na region. |
| **Opóźnienie** | Niski: czas niemal w czasie rzeczywistym. | Niski: czas niemal w czasie rzeczywistym. |
| **Wyślij do wielu punktów końcowych** | Tak, Wyślij pojedynczą wiadomość do wielu punktów końcowych. | Tak, Wyślij pojedynczą wiadomość do wielu punktów końcowych.  
| **Bezpieczeństwo** | Centrum IoT oferuje tożsamość poszczególnych urządzeń i kontrolę dostępu revocable. Aby uzyskać więcej informacji, zobacz [IoT Hub kontroli dostępu](iot-hub-devguide-security.md). | Event Grid zapewnia weryfikację w trzech punktach: subskrypcje zdarzeń, publikowanie zdarzeń i dostarczanie zdarzeń elementu webhook. Aby uzyskać więcej informacji, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Jak wybrać

IoT Hub Routing komunikatów i IoT Hub integrację z Event Grid wykonywać różne akcje w celu uzyskania podobnych wyników. Obie te osoby przyjmują informacje z rozwiązania IoT Hub i przekazują je, tak aby inne usługi mogły reagować. Aby określić, który z nich ma być używany? Weź pod uwagę następujące pytania, aby pomóc Ci w podejmowaniu decyzji: 

* **Jakiego rodzaju dane są wysyłane do punktów końcowych?**

   Użyj IoT Hub Routing komunikatów, gdy musisz wysyłać dane telemetryczne do innych usług. Routing komunikatów umożliwia również wykonywanie zapytań dotyczących aplikacji i właściwości systemu, treści wiadomości, znaczników sznurów urządzeń oraz właściwości z sznurów urządzeń.

   Integracja IoT Hub z Event Grid współpracuje ze zdarzeniami występującymi w usłudze IoT Hub. Te zdarzenia IoT Hub obejmują dane telemetryczne, utworzone, usunięte, połączone i rozłączone. Podczas subskrybowania zdarzeń telemetrii można zastosować dodatkowe filtry do danych w celu filtrowania właściwości komunikatów, treści wiadomości i sznurów urządzeń w IoT Hub przed opublikowaniem w Event Grid. Zobacz [Jak filtrować zdarzenia](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Jakie punkty końcowe muszą otrzymać te informacje?**

   IoT Hub Routing komunikatów obsługuje ograniczoną liczbę unikatowych punktów końcowych i typów punktów końcowych, ale można tworzyć łączniki w celu przekierowania danych i zdarzeń do dodatkowych punktów końcowych. Aby uzyskać pełną listę obsługiwanych punktów końcowych, zapoznaj się z tabelą w poprzedniej sekcji. 

   Integracja IoT Hub z Event Grid obsługuje 500 punktów końcowych na IoT Hub oraz większą różnorodność typów punktów końcowych. Integruje się ona natywnie z kolejkami Azure Functions, Logic Apps, Storage i Service Bus, a także współpracuje z elementami webhook, aby zwiększyć wysyłanie danych poza ekosystemem usługi platformy Azure oraz do aplikacji firmowych innych firm.

* **Czy dane są dostarczane w kolejności?**

   IoT Hub Routing komunikatów zachowuje kolejność, w jakiej wysyłane są komunikaty, dzięki czemu docierają w ten sam sposób.

   Event Grid nie gwarantuje, że punkty końcowe będą odbierać zdarzenia w takiej samej kolejności, w jakiej się znajdowały. W przypadku, gdy bezwzględne kolejność komunikatów jest istotna i/lub jeśli odbiorca potrzebuje wiarygodnego identyfikatora unikatowego dla komunikatów, zalecamy użycie routingu komunikatów. 

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [IoT Hub kierowaniu komunikatów](iot-hub-devguide-messages-d2c.md) i [IoT Hub punktów końcowych](iot-hub-devguide-endpoints.md).
* Dowiedz się więcej na temat usługi [Azure Event Grid](../event-grid/overview.md).
* Aby dowiedzieć się, jak tworzyć trasy komunikatów, zobacz temat [proces IoT Hub komunikatów z urządzenia do chmury przy użyciu usługi Routes](../iot-hub/tutorial-routing.md) .
* Wypróbuj integrację Event Grid, [wysyłając powiadomienia e-mail o zdarzeniach IoT Hub platformy Azure przy użyciu Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).
