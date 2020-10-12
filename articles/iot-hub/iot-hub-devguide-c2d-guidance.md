---
title: Azure IoT Hub opcje chmury-to-Device | Microsoft Docs
description: Przewodnik dla deweloperów — wskazówki dotyczące sytuacji, w których należy używać metod bezpośrednich, odpowiednich właściwości sznurów urządzeń lub komunikatów z chmury do urządzenia w celu komunikacji między chmurą i urządzeniem.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 10206aced4f38f4d157f46703aac2d28ec863274
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319154"
---
# <a name="cloud-to-device-communications-guidance"></a>Wskazówki dotyczące komunikacji między chmurą i urządzeniem

IoT Hub udostępnia trzy opcje dla aplikacji urządzeń, które umożliwiają udostępnienie funkcjonalności aplikacji zaplecza:

* [Metody bezpośrednie](iot-hub-devguide-direct-methods.md) do komunikacji, które wymagają natychmiastowego potwierdzenia wyniku. Metody bezpośrednie są często używane do interaktywnej kontroli nad urządzeniami, takimi jak Włączanie wentylatorów.

* [Odpowiednie właściwości sznurka](iot-hub-devguide-device-twins.md) dla długotrwałych poleceń przeznaczonych do przełączenia urządzenia do określonego stanu. Na przykład ustaw interwał wysyłania danych telemetrycznych na 30 minut.

* [Komunikaty z chmury do urządzenia](iot-hub-devguide-messages-c2d.md) dla powiadomień jednokierunkowych do aplikacji urządzenia.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Poniżej przedstawiono szczegółowe porównanie różnych opcji komunikacji między chmurą i urządzeniem.

| Kategorie | Metody bezpośrednie | Żądane właściwości sznurka | Komunikaty z chmury do urządzenia |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Scenariusz | Polecenia, które wymagają natychmiastowego potwierdzenia, na przykład włączenie wentylatoru. | Długotrwałe polecenia przeznaczone do przełączenia urządzenia do określonego stanu. Na przykład ustaw interwał wysyłania danych telemetrycznych na 30 minut. | Powiadomienia jednokierunkowe do aplikacji urządzenia. |
| Przepływ danych | Dwukierunkowo. Aplikacja urządzenia może od razu odpowiedzieć na metodę. Zaplecze rozwiązania odbiera wynik kontekstowy do żądania. | Jednokierunkowe. Aplikacja urządzenia odbiera powiadomienie ze zmianą właściwości. | Jednokierunkowe. Aplikacja urządzenia otrzymuje komunikat
| Trwałość | Nie nawiązano połączenia z odłączonymi urządzeniami. Zaplecze rozwiązania zostaje powiadomione o tym, że urządzenie nie jest połączone. | Wartości właściwości są zachowywane w postaci dwuosiowej urządzenia. Urządzenie odczyta je przy następnym ponownym połączeniu. Wartości właściwości są możliwy do pobierania przy użyciu [języka zapytań IoT Hub](iot-hub-devguide-query-language.md). | Komunikaty mogą być przechowywane przez IoT Hub przez maksymalnie 48 godzin. |
| Targets (Obiekty docelowe) | Pojedyncze urządzenie używające **deviceId**lub wiele urządzeń korzystających z [zadań](iot-hub-devguide-jobs.md). | Pojedyncze urządzenie używające **deviceId**lub wiele urządzeń korzystających z [zadań](iot-hub-devguide-jobs.md). | Pojedyncze **urządzenie według identyfikator**urządzenia. |
| Rozmiar | Maksymalny rozmiar ładunku metody bezpośredniej to 128 KB. | Maksymalny rozmiar żądanych właściwości to 32 KB. | Do 64 KB komunikatów. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [limity IoT Hub](iot-hub-devguide-quotas-throttling.md). | Średnia. Aby uzyskać więcej informacji, zobacz [limity IoT Hub](iot-hub-devguide-quotas-throttling.md). | Niski. Aby uzyskać więcej informacji, zobacz [limity IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protokół | Dostępne za pomocą MQTT lub AMQP. | Dostępne za pomocą MQTT lub AMQP. | Dostępne we wszystkich protokołach. Urządzenie musi sondować przy użyciu protokołu HTTPS. |

Dowiedz się, jak korzystać z metod bezpośrednich, wymaganych właściwości i komunikatów z chmury do urządzeń w następujących samouczkach:

* [Korzystanie z metod bezpośrednich](quickstart-control-device-node.md)
* [Konfigurowanie urządzeń przy użyciu żądanych właściwości](tutorial-device-twins.md) 
* [Wysyłanie komunikatów z chmury do urządzeń](iot-hub-node-node-c2d.md)
