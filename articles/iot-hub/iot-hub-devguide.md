---
title: Przewodnik dla deweloperów usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów usługi Azure IoT Hub zawiera dyskusje na temat punktów końcowych, zabezpieczeń, rejestru tożsamości, zarządzania urządzeniami, metod bezpośrednich, bliźniaczych urządzeń, przekazywania plików, zadań, języka zapytań usługi IoT Hub i obsługi wiadomości.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 4e380fdcd72ab21f00272536b9f08145dcb09bc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728813"
---
# <a name="azure-iot-hub-developer-guide"></a>Przewodnik dla deweloperów usługi Azure IoT Hub

Usługa Azure IoT Hub to w pełni zarządzana usługa, która pomaga umożliwić niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń i zaplecza rozwiązania.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Usługa Azure IoT Hub zapewnia:

* Bezpieczna komunikacja przy użyciu poświadczeń zabezpieczeń na urządzeniu i kontroli dostępu.

* Wiele opcji komunikacji między urządzeniami a chmurą i chmurą na hiperskali.

* Queryable przechowywania informacji o stanie na urządzeniu i meta-danych.

* Łatwa łączność urządzenia z bibliotekami urządzeń dla najpopularniejszych języków i platform.

Ten przewodnik dla deweloperów usługi IoT Hub zawiera następujące artykuły:

* [Wskazówki dotyczące komunikacji między urządzeniami do chmury](iot-hub-devguide-d2c-guidance.md) ułatwiają wybór między komunikatami między urządzeniami a chmurami, właściwościami zgłaszanych bliźniaczej reprezentacji urządzenia i przekazywaniem plików.

* [Wskazówki dotyczące komunikacji między chmurą a urządzeniem](iot-hub-devguide-c2d-guidance.md) ułatwiają wybór między metodami bezpośrednimi, żądanymi właściwościami bliźniaczej reprezentacji urządzenia i komunikatami z chmury do urządzenia.

* [Obsługa wiadomości między urządzeniami i chmurą z chmury na urządzenia z usługą IoT Hub](iot-hub-devguide-messaging.md) opisuje funkcje obsługi wiadomości (między urządzeniami do chmury i z chmury do urządzenia), które udostępnia centrum IoT Hub.

  * [Wysyłanie wiadomości z urządzenia do chmury do centrum IoT Hub](iot-hub-devguide-messages-d2c.md).

  * [Odczytywanie komunikatów z urządzenia do chmury z wbudowanego punktu końcowego](iot-hub-devguide-messages-read-builtin.md).

  * [Użyj niestandardowych punktów końcowych i reguł routingu dla wiadomości z urządzenia do chmury](iot-hub-devguide-messages-read-custom.md).

  * [Wysyłanie komunikatów z chmury do urządzenia z usługi IoT Hub](iot-hub-devguide-messages-c2d.md).

  * [Tworzenie i odczytywanie komunikatów Centrum IoT](iot-hub-devguide-messages-construct.md).

* [Przekazywanie plików z urządzenia](iot-hub-devguide-file-upload.md) opisuje sposób przekazywania plików z urządzenia. Artykuł zawiera również informacje na tematy, takie jak powiadomienia, które proces przekazywania może wysyłać.

* [Zarządzanie tożsamościami urządzeń w Centrum IoT opisuje,](iot-hub-devguide-identity-registry.md)jakie informacje przechowuje rejestr tożsamości każdego centrum IoT Hub. W artykule opisano również, jak można uzyskać do niego dostęp i zmodyfikować go.

* [Kontrola dostępu do usługi IoT Hub](iot-hub-devguide-security.md) opisuje model zabezpieczeń używany do udzielania dostępu do funkcji Usługi IoT Hub dla urządzeń i składników chmury. Ten artykuł zawiera informacje dotyczące używania tokenów i certyfikatów X.509 oraz szczegóły dotyczące uprawnień, które można udzielić.

* [Użyj bliźniaczych reprezentacji urządzeń, aby zsynchronizować stan i konfiguracje](iot-hub-devguide-device-twins.md) opisuje koncepcję *bliźniaczej reprezentacji urządzenia.* W tym artykule opisano również funkcje bliźniaczych reprezentacji urządzenia uwidaczniać, takich jak synchronizowanie urządzenia z jego bliźniaczej reprezentacji urządzenia. Artykuł zawiera informacje o danych przechowywanych w bliźniaczej reprezentacji urządzenia.

* [Wywołać metodę bezpośrednią na urządzeniu](iot-hub-devguide-direct-methods.md) opisuje cykl życia metody bezpośredniej. W tym artykule opisano sposób wywoływania metod na urządzeniu z aplikacji zaplecza i obsługi metody bezpośredniej na urządzeniu.

* [Planowanie zadań na wielu urządzeniach](iot-hub-devguide-jobs.md) opisuje sposób planowania zadań na wielu urządzeniach. W tym artykule opisano sposób przesyłania zadań, które wykonują zadania jako wykonywanie metody bezpośredniej, aktualizowanie urządzenia przy użyciu bliźniaczej reprezentacji urządzenia. Opisano w nim również sposób wykonywania zapytań o stan zadania.

* [Odwołanie — wybierz protokół komunikacyjny](iot-hub-devguide-protocols.md) opisuje protokoły komunikacyjne, które obsługuje Centrum IoT dla komunikacji urządzenia i wyświetla listę portów, które powinny być otwarte.

* [Odwołanie — punkty końcowe usługi IoT Hub](iot-hub-devguide-endpoints.md) opisuje różne punkty końcowe, które każdy centrum IoT udostępnia dla środowiska uruchomieniowego i operacji zarządzania. W tym artykule opisano również, jak można utworzyć dodatkowe punkty końcowe w centrum IoT hub i jak używać bramy pola, aby włączyć łączność z punktami końcowymi usługi IoT Hub w niestandardowych scenariuszach.

* [Odwołanie — język zapytań usługi IoT Hub dla bliźniaczych urządzeń, zadań i routingu wiadomości](iot-hub-devguide-query-language.md) opisuje, że język zapytań Usługi IoT Hub, który umożliwia pobieranie informacji z koncentratora o bliźniaczych urządzeń i zadaniach.

* [Odwołanie — przydziały i ograniczanie podsumowuje](iot-hub-devguide-quotas-throttling.md) przydziały ustawione w usłudze Centrum IoT i ograniczanie przepustowości, które występuje po przekroczeniu przydziału.

* [Odwołanie — cennika](iot-hub-devguide-pricing.md) zawiera ogólne informacje na temat różnych jednostek SKU i cen dla Usługi IoT Hub i szczegółowe informacje o tym, jak różne funkcje Usługi IoT Hub są mierzone jako komunikaty przez Centrum IoT Hub.

* [Odwołanie — zestaw SDK urządzeń i usług](iot-hub-devguide-sdks.md) zawiera listę zestawów SDK usługi Azure IoT do tworzenia aplikacji urządzeń i usług, które współdziałają z centrum IoT hub. Ten artykuł zawiera łącza do dokumentacji interfejsu API online.

* [Odwołanie — obsługa MQTT usługi IoT Hub](iot-hub-mqtt-support.md) zawiera szczegółowe informacje o tym, jak IoT Hub obsługuje protokół MQTT. W tym artykule opisano obsługę protokołu MQTT wbudowanego w zestawY SDK usługi Azure IoT i zawiera informacje dotyczące bezpośredniego korzystania z protokołu MQTT.

* [Słowniczek](iot-hub-devguide-glossary.md) listę typowych terminów związanych z Centrum IoT.