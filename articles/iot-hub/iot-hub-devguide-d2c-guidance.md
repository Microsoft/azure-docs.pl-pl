---
title: Opcje usługi Azure IoT Hub między urządzeniami do chmury | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — wskazówki dotyczące używania komunikatów z urządzenia do chmury, zgłoszonych właściwości lub przekazywania plików do komunikacji z chmury do urządzenia.
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
ms.openlocfilehash: b984ee7ed662bf089e7bcb6fc7e948fb61ed1209
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733214"
---
# <a name="device-to-cloud-communications-guidance"></a>Wskazówki dotyczące komunikacji między urządzeniami a chmurą

Podczas wysyłania informacji z aplikacji urządzenia do zaplecza rozwiązania Usługa IoT Hub udostępnia trzy opcje:

* [Komunikaty między urządzeniami do chmury](iot-hub-devguide-messages-d2c.md) dla danych telemetrycznych szeregów czasowych i alertów.

* [Bliźniaczej reprezentacji urządzenia zgłaszane właściwości](iot-hub-devguide-device-twins.md) do raportowania informacji o stanie urządzenia, takich jak dostępne możliwości, warunki lub stan długotrwałych przepływów pracy. Na przykład aktualizacje konfiguracji i oprogramowania.

* [Przekazywanie plików](iot-hub-devguide-file-upload.md) dla plików multimedialnych i dużych partii telemetrycznych przesyłanych przez sporadycznie podłączone urządzenia lub skompresowanych w celu zaoszczędzenia przepustowości.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Oto szczegółowe porównanie różnych opcji komunikacji między urządzeniami a chmurą.

|  | Komunikaty z urządzenia do chmury | Zgłaszane właściwości bliźniaczej reprezentacji urządzenia | Operacje przekazywania plików |
| ---- | ------- | ---------- | ---- |
| Scenariusz | Szeregi czasowe telemetrii i alerty. Na przykład 256 KB partii danych czujnika wysyłane co 5 minut. | Dostępne możliwości i warunki. Na przykład bieżący tryb łączności urządzenia, taki jak sieć komórkowa lub Wi-Fi. Synchronizowanie długotrwałych przepływów pracy, takich jak aktualizacje konfiguracji i oprogramowania. | Pliki multimedialne. Duże (zazwyczaj skompresowane) partie telemetrii. |
| Przechowywanie i pobieranie | Tymczasowo przechowywane przez Centrum IoT, do 7 dni. Tylko odczyt sekwencyjny. | Przechowywane przez Centrum IoT w bliźniaczej reprezentacji urządzenia. Można go pobrać przy użyciu [języka zapytań Centrum IoT](iot-hub-devguide-query-language.md). | Przechowywane na koncie usługi Azure Storage dostarczonym przez użytkownika. |
| Rozmiar | Do 256 KB wiadomości. | Maksymalny rozmiar zgłoszonych właściwości to 32 KB. | Maksymalny rozmiar pliku obsługiwany przez usługę Azure Blob Storage. |
| Częstotliwość | Wysoka. Aby uzyskać więcej informacji, zobacz [Limity usługi IoT Hub](iot-hub-devguide-quotas-throttling.md). | Średnia. Aby uzyskać więcej informacji, zobacz [Limity usługi IoT Hub](iot-hub-devguide-quotas-throttling.md). | Niski. Aby uzyskać więcej informacji, zobacz [Limity usługi IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protocol (Protokół) | Dostępne we wszystkich protokołach. | Dostępne przy użyciu MQTT lub AMQP. | Dostępne w przypadku korzystania z dowolnego protokołu, ale wymaga https na urządzeniu. |

Aplikacja może być konieczne wysłanie informacji zarówno jako szeregi czasowe telemetrii lub alertu i udostępnić je w bliźniaczej reprezentacji urządzenia. W tym scenariuszu można wybrać jedną z następujących opcji:

* Aplikacja urządzenia wysyła komunikat urządzenia do chmury i zgłasza zmianę właściwości.
* Zaplecze rozwiązania można przechowywać informacje w tagach bliźniaczej reprezentacji urządzenia po odebraniu wiadomości.

Ponieważ komunikaty między urządzeniami do chmury umożliwiają znacznie wyższą przepływność niż aktualizacje bliźniaczej reprezentacji urządzenia, czasami pożądane jest unikanie aktualizowania bliźniaczej reprezentacji urządzenia dla każdego komunikatu urządzenia do chmury.
