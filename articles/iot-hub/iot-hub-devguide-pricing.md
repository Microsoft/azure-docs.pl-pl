---
title: Informacje o cenach usługi Azure IoT Hub | Microsoft Docs
description: Przewodnik dla deweloperów — informacje o tym, w jaki sposób pomiary i ceny współdziałają z IoT Hub uwzględnieniem przykładowych przykładów.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81729159"
---
# <a name="azure-iot-hub-pricing-information"></a>Informacje o cenach usługi Azure IoT Hub

[Cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) udostępnia ogólne informacje o różnych jednostkach SKU i cenach dla IoT Hub. W tym artykule znajdują się dodatkowe szczegółowe informacje o tym, jak różne funkcje IoT Hub są mierzone jako wiadomości przez IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Opłaty za operację

| Operacja | Informacje o rozliczeniach | 
| --------- | ------------------- |
| Operacje rejestru tożsamości <br/> (tworzenie, pobieranie, wyświetlanie, aktualizowanie, usuwanie) | Bez opłat. |
| Komunikaty z urządzenia do chmury | Pomyślnie wysłane komunikaty są rozliczone w postaci fragmentów 4 KB na ruch przychodzący do IoT Hub. Na przykład komunikat 6 KB jest rozliczany 2 komunikaty. |
| Komunikaty z chmury do urządzenia | Pomyślnie wysłane komunikaty są rozliczone w fragmentach o rozmiarach 4 KB, a na przykład komunikat 6 KB jest rozliczany 2 komunikaty. |
| Operacje przekazywania plików | Transfer plików do usługi Azure Storage nie jest naliczany przez IoT Hub. W przypadku komunikatów o inicjacji i zakończeniu transferu plików są naliczane opłaty za wiadomości w przyrostach o rozmiarze 4 KB. Na przykład transfer pliku o rozmiarze 10 MB jest naliczany jako dwa komunikaty oprócz kosztu usługi Azure Storage. |
| Metody bezpośrednie | Pomyślne żądania metod są rozliczone w fragmentach o rozmiarach 4 KB, a odpowiedzi są rozliczone w przypadku fragmentów o rozmiarach 4 KB jako dodatkowych komunikatów. Żądania dotyczące rozłączonych urządzeń są rozliczone jako komunikaty w fragmentach o rozmiarach 4 KB. Na przykład metoda o treści 4 KB, która skutkuje odpowiedzią bez treści urządzenia, jest naliczana za dwa komunikaty. Metoda z treścią 6 KB, która powoduje, że odpowiedź na 1 KB z urządzenia jest naliczana jako dwa komunikaty dla żądania oraz kolejny komunikat odpowiedzi. |
| Odczyty sznurka urządzenia i modułu | Dane z urządzenia lub modułu oraz z zaplecza rozwiązania są rozliczone jako komunikaty w przypadku segmentów 512-bajtowych. Na przykład odczytywanie sznurka 6 KB jest obciążane jako 12 komunikatów. |
| Aktualizacje urządzeń i modułów z przędzą (Tagi i właściwości) | Niezależne aktualizacje z urządzenia lub modułu oraz zaplecze rozwiązania są rozliczone jako komunikaty w przypadku segmentów 512-bajtowych. Na przykład odczytywanie sznurka 6 KB jest obciążane jako 12 komunikatów. |
| Zapytania o sznurki urządzenia i modułu | Zapytania są rozliczone jako komunikaty, w zależności od rozmiaru wyniku w fragmentach 512 bajtów. |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie, usuwanie) | Bez opłat. |
| Zadania dla poszczególnych urządzeń | Operacje na zadaniach (takie jak aktualizacje przędzy i metody) są rozliczone jako normalne. Na przykład zadanie w wyniku wywołania metody 1000 z 1 KB żądania i puste odpowiedzi są obciążane komunikat 1000. |
| Komunikaty Keep-Alive | W przypadku korzystania z protokołów AMQP lub MQTT komunikaty wymieniane w celu ustanowienia połączenia i komunikatów wymienianych w ramach negocjacji nie są naliczone. |

> [!NOTE]
> Wszystkie rozmiary są obliczane pod kątem rozmiaru ładunku w bajtach (ramka protokołu jest ignorowana). W przypadku komunikatów, które mają właściwości i treści, rozmiar jest obliczany w sposób niezależny od. Aby uzyskać więcej informacji, zobacz [format komunikatu IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Przykład #1

Urządzenie wysyła komunikat z urządzenia do chmury 1 1-KB na minutę do IoT Hub, który jest następnie odczytywany przez Azure Stream Analytics. Zaplecze rozwiązania wywołuje metodę (z 512-bajtowym ładunkiem) na urządzeniu co 10 minut, aby wyzwolić konkretną akcję. Urządzenie odpowiada na metodę z wynikiem 200 bajtów.

Urządzenie zużywa:

* Jeden komunikat * 60 minuty * 24 godziny = 1440 komunikatów dziennie dla komunikatów przesyłanych z urządzenia do chmury.
* Dwa żądania i odpowiedź * 6 razy na godzinę * 24 godziny = 288 komunikatów dla metod.

To obliczenie daje łączną liczbę 1728 komunikatów dziennie.

## <a name="example-2"></a>Przykład #2

Urządzenie wysyła komunikat z urządzenia do chmury 1 100-KB co godzinę. Aktualizuje ona również swój sznur urządzenia o 1 KB ładunków co cztery godziny. Zaplecze rozwiązania, raz dziennie odczytuje sznurki urządzenia 14 KB i aktualizuje je o 512-bajtowe ładunki, aby zmienić konfiguracje.

Urządzenie zużywa:

* 25 (100 KB/4 KB) komunikaty * 24 godziny dla komunikatów z urządzenia do chmury.
* Dwa komunikaty (1 KB/0,5 KB) * sześć razy dziennie w przypadku aktualizacji z przędzą na urządzeniu.

To obliczenie daje łączną liczbę 612 komunikatów dziennie.

Zaplecze rozwiązania zużywa 28 komunikatów (14 KB/0,5 KB) w celu odczytania sznurka urządzenia oraz jednej wiadomości, aby ją zaktualizować, w sumie 29 komunikatów.

Łącznie urządzenie i zaplecze rozwiązania zajmują 641 komunikatów dziennie.
