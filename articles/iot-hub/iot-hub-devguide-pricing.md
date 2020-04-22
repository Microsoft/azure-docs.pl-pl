---
title: Opis cen usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Przewodnik dla deweloperów — informacje o tym, jak pomiary i ceny działają z Centrum IoT, w tym przepracowane przykłady.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729159"
---
# <a name="azure-iot-hub-pricing-information"></a>Informacje o cenach usługi Azure IoT Hub

[Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) zawiera ogólne informacje na temat różnych jednostek SKU i cen dla usługi IoT Hub. Ten artykuł zawiera dodatkowe szczegóły dotyczące sposobu, w jaki różne funkcje usługi IoT Hub są mierzone jako komunikaty przez centrum IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Opłaty za operację

| Operacja | Informacje rozliczeniowe | 
| --------- | ------------------- |
| Operacje rejestru tożsamości <br/> (tworzenie, pobieranie, listy, aktualizacji, usuwania) | Nie naładowana. |
| Komunikaty z urządzenia do chmury | Pomyślnie wysłane wiadomości są naliczane w 4 KB fragmentów przy przychodzących do Usługi IoT Hub. Na przykład wiadomość o 6 KB jest naliczana na 2 wiadomości. |
| Komunikaty z chmury do urządzenia | Pomyślnie wysłane wiadomości są naliczane w fragmentach 4 KB, na przykład wiadomość o masie 6 KB jest naliczana 2 wiadomości. |
| Operacje przekazywania plików | Transfer plików do usługi Azure Storage nie jest mierzony przez centrum IoT Hub. Komunikaty inicjacji i zakończenia transferu plików są naliczane jako messaged mierzone w przyrostach 4 KB. Na przykład przeniesienie pliku o rozmiarze 10 MB jest naliczane jako dwie wiadomości oprócz kosztu usługi Azure Storage. |
| Metody bezpośrednie | Pomyślne żądania metody są naliczane w fragmentach 4 KB, a odpowiedzi są naliczane w fragmentach 4 KB jako dodatkowe wiadomości. Żądania do rozłączonych urządzeń są naliczane jako wiadomości we fragmentach 4 KB. Na przykład metoda z treścią 4 KB, która powoduje odpowiedź bez treści z urządzenia, jest ładowana jako dwie wiadomości. Metoda z treścią 6 KB, która powoduje odpowiedź 1 KB z urządzenia, jest naliczana jako dwie wiadomości dla żądania plus inny komunikat dla odpowiedzi. |
| Odczyty bliźniaczej funkcji urządzenia i modułu | Bliźniacze odczyty z urządzenia lub modułu i z zaplecza rozwiązania są ładowane jako wiadomości w 512-bajtowych fragmentach. Na przykład odczytu bliźniaczej reprezentacji 6 KB jest naliczana jako 12 wiadomości. |
| Aktualizacje bliźniaczej reprezentacji urządzeń i modułów (znaczniki i właściwości) | Bliźniacze aktualizacje z urządzenia lub modułu i z zaplecza rozwiązania są ładowane jako wiadomości w 512-bajtowych fragmentach. Na przykład odczytu bliźniaczej reprezentacji 6 KB jest naliczana jako 12 wiadomości. |
| Zapytania bliźniacze dwie urządzenia i modułu | Zapytania są naliczane jako wiadomości w zależności od rozmiaru wyniku w 512-bajtowych fragmentach. |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | Nie naładowana. |
| Zadania na urządzenie | Operacje zadań (takie jak bliźniacze aktualizacje i metody) są naliczane normalnie. Na przykład zadanie powodujące 1000 wywołań metody z żądaniami 1 KB i odpowiedziami pustymi treściami jest obciążone 1000 wiadomości. |
| Wiadomości o utrzymaniu przy życiu | W przypadku korzystania z protokołów AMQP lub MQTT wiadomości wymieniane w celu nawiązania połączenia i wiadomości wymieniane w negocjacjach nie są naliczane. |

> [!NOTE]
> Wszystkie rozmiary są obliczane z uwzględnieniem rozmiaru ładunku w bajtach (ramki protokołu są ignorowane). Dla wiadomości, które mają właściwości i treść, rozmiar jest obliczany w sposób niezależny od protokołu. Aby uzyskać więcej informacji, zobacz [Format wiadomości Usługi IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Przykład #1

Urządzenie wysyła jeden komunikat z urządzeniami do chmury o wartości 1 KB na minutę do usługi IoT Hub, który jest następnie odczytywany przez usługę Azure Stream Analytics. Zaplecze rozwiązania wywołuje metodę (z ładunkiem 512 bajtów) na urządzeniu co 10 minut, aby wyzwolić określoną akcję. Urządzenie reaguje na metodę z wynikiem 200 bajtów.

Urządzenie zużywa:

* Jedna wiadomość * 60 minut * 24 godziny = 1440 wiadomości dziennie dla wiadomości z urządzenia do chmury.
* Dwa żądania plus odpowiedź * 6 razy na godzinę * 24 godziny = 288 wiadomości dla metod.

To obliczenie daje łącznie 1728 wiadomości dziennie.

## <a name="example-2"></a>Przykład #2

Urządzenie wysyła jedną wiadomość o wartości 100 KB między urządzeniami do chmury co godzinę. Aktualizuje również bliźniaczej reprezentacji urządzenia ładunki 1 KB co cztery godziny. Zaplecze rozwiązania, raz dziennie, odczytuje bliźniaczej reprezentacji urządzenia 14 KB i aktualizuje go za pomocą ładunków 512-bajtowych, aby zmienić konfiguracje.

Urządzenie zużywa:

* 25 (100 KB / 4 KB) wiadomości * 24 godziny dla wiadomości z urządzenia do chmury.
* Dwa komunikaty (1 KB / 0.5 KB) * sześć razy dziennie dla aktualizacji bliźniaczych urządzeń.

To obliczenie daje łącznie 612 komunikatów dziennie.

Zaplecze rozwiązania zużywa 28 komunikatów (14 KB / 0,5 KB), aby odczytać bliźniaczej reprezentacji urządzenia, a także jedną wiadomość, aby go zaktualizować, w sumie 29 wiadomości.

W sumie urządzenie i zaplecze rozwiązania zużywają 641 komunikatów dziennie.
