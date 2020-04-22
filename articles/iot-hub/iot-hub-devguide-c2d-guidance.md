---
title: Opcje usługi Azure IoT Hub z chmury do urządzenia | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — wskazówki dotyczące używania metod bezpośrednich, żądanych właściwości bliźniaczej reprezentacji urządzenia lub komunikatów z chmury do urządzenia do komunikacji z chmury do urządzenia.
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
ms.openlocfilehash: b5682334bd3fb23fbbebed5fc8ece6d55e9c5652
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733240"
---
# <a name="cloud-to-device-communications-guidance"></a>Wskazówki dotyczące komunikacji z chmury do urządzenia

Centrum IoT hub udostępnia trzy opcje dla aplikacji urządzenia, aby udostępnić funkcje aplikacji zaplecza:

* [Bezpośrednie metody](iot-hub-devguide-direct-methods.md) komunikacji, które wymagają natychmiastowego potwierdzenia wyniku. Metody bezpośrednie są często używane do interaktywnej kontroli urządzeń, takich jak włączanie wentylatora.

* [Bliźniaczej żądane właściwości](iot-hub-devguide-device-twins.md) dla długotrwałych poleceń przeznaczonych do wprowadzenia urządzenia w pewien pożądany stan. Na przykład ustaw interwał wysyłania danych telemetrycznych na 30 minut.

* [Komunikaty między chmurami dla powiadomień](iot-hub-devguide-messages-c2d.md) jednokierunkowych do aplikacji urządzenia.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Oto szczegółowe porównanie różnych opcji komunikacji między chmurą a urządzeniem.

|  | Metody bezpośrednie | Pożądane właściwości bliźniaczki | Komunikaty z chmury do urządzenia |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Polecenia wymagające natychmiastowego potwierdzenia, takie jak włączenie wentylatora. | Długotrwałe polecenia mające na celu umieszczenie urządzenia w określonym pożądanym stanie. Na przykład ustaw interwał wysyłania danych telemetrycznych na 30 minut. | Jednokierunkowe powiadomienia do aplikacji urządzenia. |
| Przepływ danych | Dwukierunkowe. Aplikacja urządzenia może od razu odpowiedzieć na metodę. Zaplecza rozwiązania odbiera wynik kontekstowo do żądania. | Jednokierunkowe. Aplikacja urządzenia otrzymuje powiadomienie ze zmianą właściwości. | Jednokierunkowe. Aplikacja urządzenia odbiera komunikat
| Trwałość | Nie można skontaktować się z urządzeniami rozłączonych. Zaplecze rozwiązania jest powiadamiany, że urządzenie nie jest podłączony. | Wartości właściwości są zachowywane w bliźniaczej reprezentacji urządzenia. Urządzenie odczyta go przy następnym ponownym połączeniu. Wartości właściwości można pobrać za pomocą [języka zapytań usługi IoT Hub](iot-hub-devguide-query-language.md). | Wiadomości mogą być przechowywane przez Centrum IoT przez maksymalnie 48 godzin. |
| Obiekty docelowe | Pojedyncze urządzenie **używające identyfikatora deviceId**lub wielu urządzeń korzystających z [zadań](iot-hub-devguide-jobs.md). | Pojedyncze urządzenie **używające identyfikatora deviceId**lub wielu urządzeń korzystających z [zadań](iot-hub-devguide-jobs.md). | Pojedyncze urządzenie według **identyfikatora urządzenia**. |
| Rozmiar | Maksymalny rozmiar ładunku metody bezpośredniej wynosi 128 KB. | Maksymalny rozmiar żądanych właściwości to 32 KB. | Do 64 KB wiadomości. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [Limity usługi IoT Hub](iot-hub-devguide-quotas-throttling.md). | Średnia. Aby uzyskać więcej informacji, zobacz [Limity usługi IoT Hub](iot-hub-devguide-quotas-throttling.md). | Niski. Aby uzyskać więcej informacji, zobacz [Limity usługi IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protocol (Protokół) | Dostępne przy użyciu MQTT lub AMQP. | Dostępne przy użyciu MQTT lub AMQP. | Dostępne we wszystkich protokołach. Urządzenie musi sondować podczas korzystania z protokołu HTTPS. |

Dowiedz się, jak używać metod bezpośrednich, żądanych właściwości i komunikatów z chmury do urządzenia w następujących samouczkach:

* [Korzystanie z metod bezpośrednich](quickstart-control-device-node.md)
* [Konfigurowanie urządzeń za pomocą żądanych właściwości](tutorial-device-twins.md) 
* [Wysyłanie komunikatów z chmury do urządzeń](iot-hub-node-node-c2d.md)
