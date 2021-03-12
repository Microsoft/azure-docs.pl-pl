---
title: Przewodnik dla deweloperów dla platformy Azure IoT Hub | Microsoft Docs
description: Przewodnik dewelopera platformy Azure IoT Hub obejmuje dyskusje na temat punktów końcowych, zabezpieczeń, rejestru tożsamości, zarządzania urządzeniami, metod bezpośrednich, bliźniaczych reprezentacji urządzeń, przekazywania plików, zadań, języka zapytań IoT Hub i obsługi komunikatów.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 1fcf560ba1fef137a28ab1872635cc2182cd07e1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009202"
---
# <a name="azure-iot-hub-developer-guide"></a>Przewodnik dla deweloperów IoT Hub platformy Azure

Azure IoT Hub to w pełni zarządzana usługa, która pomaga zapewnić niezawodne i niezawodną komunikację dwukierunkową między milionami urządzeń i zapleczem rozwiązania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Usługa Azure IoT Hub oferuje następujące informacje:

* Bezpieczna komunikacja przy użyciu poświadczeń zabezpieczeń i kontroli dostępu dla poszczególnych urządzeń.

* Wiele opcji komunikacji między urządzeniami a chmurą i chmurą z urządzeniem.

* Queryable magazynu informacji o stanie urządzenia i metadanych.

* Łatwe łączenie urządzeń z bibliotekami urządzeń dla najpopularniejszych języków i platform.

Ten przewodnik dla deweloperów IoT Hub obejmuje następujące artykuły:

* [Wskazówki dotyczące komunikacji między urządzeniami i chmurą](iot-hub-devguide-d2c-guidance.md) ułatwiają wybór między komunikatami z urządzenia do chmury, zgłaszanymi właściwościami z przędzki urządzeń i przekazywaniem plików.

* [Wskazówki dotyczące komunikacji między chmurą i urządzeniem](iot-hub-devguide-c2d-guidance.md) ułatwiają wybór między metodami bezpośrednimi, pożądanymi właściwościami sznurów urządzeń i komunikatami z chmury do urządzenia.

* [Obsługa komunikatów przesyłanych z urządzeń do chmury i z chmury do urządzeń w IoT Hub](iot-hub-devguide-messaging.md) opisuje funkcje obsługi komunikatów (urządzenia do chmury i chmury do urządzeń), które IoT Hub uwidaczniają.

  * [Wysyłaj komunikaty z urządzenia do chmury do IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego](iot-hub-devguide-messages-read-builtin.md).

  * [Używanie niestandardowych punktów końcowych i reguł routingu na potrzeby komunikatów przesyłanych z urządzeń do chmury](iot-hub-devguide-messages-read-custom.md).

  * [Wysyłanie komunikatów z chmury do urządzeń z IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Tworzenie i odczytywanie wiadomości IoT Hub](iot-hub-devguide-messages-construct.md).

* [Przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md) zawiera opis sposobu przekazywania plików z urządzenia. Artykuł zawiera również informacje dotyczące tematów, takich jak powiadomienia, które mogą być wysyłane przez proces przekazywania.

* [Zarządzanie tożsamościami urządzeń w IoT Hub](iot-hub-devguide-identity-registry.md) opisuje, jakie informacje są przechowywane w rejestrze tożsamości Centrum IoT Hub. W tym artykule opisano również, jak można uzyskać dostęp i zmodyfikować go.

* [Kontrola dostępu do IoT Hub](iot-hub-devguide-security.md) opisuje model zabezpieczeń używany do udzielania dostępu do IoT Hub funkcji dla urządzeń i składników w chmurze. Artykuł zawiera informacje na temat używania tokenów i certyfikatów X. 509 oraz szczegóły uprawnień, które można udzielić.

* [Aby synchronizować stan i konfiguracje, należy użyć bliźniaczych reprezentacji urządzeń](iot-hub-devguide-device-twins.md) .  W tym artykule opisano również funkcje udostępniane przez bliźniaczych reprezentacji urządzenia, takie jak Synchronizowanie urządzenia z jego sznurem. Artykuł zawiera informacje o danych przechowywanych w ramach sznurka urządzenia.

* [Wywoływanie metody bezpośredniej na urządzeniu](iot-hub-devguide-direct-methods.md) opisuje cykl życia metody bezpośredniej. W tym artykule opisano sposób wywoływania metod na urządzeniu z poziomu aplikacji zaplecza i obsługi metody bezpośredniej na urządzeniu.

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md) opisuje sposób planowania zadań na wielu urządzeniach. W tym artykule opisano sposób przesyłania zadań wykonujących zadania jako metody bezpośredniej, aktualizowania urządzenia przy użyciu sznurka urządzenia. Opisano w nim również sposób wykonywania zapytań dotyczących stanu zadania.

* [Odwołanie — wybór protokołu komunikacyjnego](iot-hub-devguide-protocols.md) zawiera opis protokołów komunikacyjnych, które IoT Hub obsługują komunikację z urządzeniem i wyświetla listę portów, które powinny być otwarte.

* [Punkty końcowe IoT Hub referencyjnych](iot-hub-devguide-endpoints.md) opisują różne punkty końcowe, które są uwidaczniane przez poszczególne usługi IoT Hub na potrzeby operacji środowiska uruchomieniowego i zarządzania. W tym artykule opisano również, jak można tworzyć dodatkowe punkty końcowe w usłudze IoT Hub oraz jak korzystać z bramy pól w celu umożliwienia łączności z punktami końcowymi IoT Hub w scenariuszach niestandardowych.

* Informacje dotyczące [języka zapytań dla bliźniaczych reprezentacji urządzeń, zadań i routingu komunikatów w programie Reference IoT Hub —](iot-hub-devguide-query-language.md) opis języka zapytań IoT Hub, który umożliwia pobieranie z centrum informacji o bliźniaczych reprezentacji i zadaniach związanych z urządzeniem.

* [Przydziały i ograniczanie przepustowości](iot-hub-devguide-quotas-throttling.md) zawiera podsumowanie przydziałów ustawionych w ramach usługi IoT Hub i ograniczania przepustowości w przypadku przekroczenia limitu przydziału.

* Informacje o [cenach referencyjnych](iot-hub-devguide-pricing.md) są ogólne dla różnych jednostek SKU i cen dla IoT Hub i szczegółowe informacje o tym, jak różne IoT Hub funkcje są mierzone jako komunikaty IoT Hub.

* [Zestawy SDK dla urządzeń i usług zawierają](iot-hub-devguide-sdks.md) listę zestawów SDK usługi Azure IoT do tworzenia aplikacji dla urządzeń i usług, które współpracują z Centrum IoT. Artykuł zawiera linki do dokumentacji interfejsu API online.

* [Dokumentacja-IoT Hub obsługa MQTT](iot-hub-mqtt-support.md) zawiera szczegółowe informacje dotyczące sposobu, w jaki IoT Hub obsługuje protokół MQTT. W tym artykule opisano obsługę protokołu MQTT wbudowanego w zestawach SDK usługi Azure IoT i podano informacje na temat bezpośredniego używania protokołu MQTT.
