---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82204557"
---
W poniższej tabeli wymieniono limity związane z różnymi warstwami usług S1, S2, S3 i F1. Aby uzyskać informacje o kosztach poszczególnych *jednostek* w poszczególnych warstwach, zobacz [cennik usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Zasób | Standardowa S1 | Standardowa S2 | Standardowa S3 | Bezpłatna F1 |
| --- | --- | --- | --- | --- |
| Komunikaty/dzień |400 000 |6 000 000 |300 000 000 |8000 |
| Maksymalna liczba jednostek |200 |200 |10 |1 |

> [!NOTE]
> Jeśli przewidujesz używanie ponad 200 jednostek z koncentratorem warstwy S1 lub S2 lub 10 jednostkami z centrum warstwy S3, skontaktuj się z pomoc techniczna firmy Microsoft.
> 
> 

W poniższej tabeli wymieniono limity dotyczące zasobów IoT Hub.

| Zasób | Limit |
| --- | --- |
| Maksymalna liczba płatnych centrów IoT na subskrypcję platformy Azure |50 |
| Maksymalna liczba bezpłatnych centrów IoT na subskrypcję platformy Azure |1 |
| Maksymalna liczba znaków w IDENTYFIKATORze urządzenia | 128 |
| Maksymalna liczba tożsamości urządzeń<br/> zwróconych w pojedynczym wywołaniu |1000 |
| Maksymalny okres przechowywania komunikatów usługi IoT Hub dla komunikatów przesyłanych z urządzenia do chmury |7 dni |
| Maksymalny rozmiar komunikatu przesyłanego z urządzenia do chmury |256 KB |
| Maksymalny rozmiar partii przesyłanej z urządzenia do chmury |AMQP i HTTP: 256 KB dla całej partii <br/>MQTT: 256 KB dla każdego komunikatu |
| Maksymalna liczba komunikatów w partii przesyłanej z urządzenia do chmury |500 |
| Maksymalny rozmiar komunikatu przesyłanego z chmury do urządzenia |64 KB |
| Maksymalny czas wygaśnięcia komunikatu przesyłanego z chmury do urządzenia |2 dni |
| Maksymalna liczba dostarczonych komunikatów przesyłanych <br/> z chmury do urządzenia |100 |
| Maksymalna głębokość kolejki z chmury do urządzenia dla urządzenia |50 |
| Maksymalna liczba dostarczonych komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |100 |
| Maksymalny czas wygaśnięcia komunikatów zwrotnych <br/> w odpowiedzi na komunikaty przesłane z chmury do urządzenia |2 dni |
| [Maksymalny rozmiar bliźniaczej reprezentacji urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB dla sekcji tagów i 32 KB dla żądanych i raportowanych sekcji właściwości każdy |
| Maksymalna długość klucza ciągu sznurka urządzenia | 1 KB |
| Maksymalna długość ciągu sznurka urządzenia | 4 KB |
| [Maksymalna głębokość obiektu w bliźniaczej reprezentacji urządzenia](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maksymalny rozmiar ładunku metody bezpośredniej | 128 KB |
| Maksymalny okres przechowywania historii zadań | 30 dni |
| Maksymalna liczba równoczesnych zadań | 10 (warstwa S3), 5 (warstwa S2), 1 (warstwa S1) |
| Maksymalna liczba dodatkowych punktów końcowych | 10 (dla S1, S2 i S3) |
| Maksymalna liczba reguł rozsyłania komunikatów | 100 (dla S1, S2 i S3) |
| Maksymalna liczba strumieni urządzeń połączonych współbieżnie | 50 (tylko dla S1, S2, S3 i F1) |
| Maksymalny transfer danych strumienia urządzeń | 300 MB dziennie (tylko dla S1, S2, S3 i F1) |

> [!NOTE]
> Jeśli potrzebujesz więcej niż 50 płatnych centrów IoT w ramach subskrypcji platformy Azure, skontaktuj się z pomoc techniczna firmy Microsoft.

> [!NOTE]
> Obecnie całkowita liczba urządzeń i modułów, które mogą być zarejestrowane w pojedynczym Centrum IoT Hub, jest ograniczona do 1 000 000. Jeśli chcesz zwiększyć ten limit, skontaktuj się z [Pomoc techniczna firmy Microsoft](https://azure.microsoft.com/support/options/).

IoT Hub ogranicza żądania w przypadku przekroczenia następujących przydziałów.

| Ograniczenie | Wartość na centrum |
| --- | --- |
| Operacje rejestru tożsamości <br/> (tworzenie, pobieranie, wyświetlanie, aktualizowanie i usuwanie), <br/> import/eksport zbiorczy lub pojedynczy |83.33/sek/jednostkę (5000/min/jednostka) (dla S3). <br/> 1.67/sek/jednostkę (100/min/jednostka) (w przypadku S1 i S2). |
| Połączenia urządzenia |6000/sek/jednostkę (dla S3), 120/sek/jednostkę (dla S2), 12/sek/jednostkę (dla S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z urządzenia do chmury |6000/sek/jednostkę (dla S3), 120/sek/jednostkę (dla S2), 12/sek/jednostkę (dla S1). <br/>Minimum 100/sek. |
| Liczba elementów wysłanych z chmury do urządzenia | 83.33/sek/jednostkę (5000/min/jednostka) (dla S3), 1.67/sek/jednostkę (100/min/jednostka) (w przypadku S1 i S2). |
| Liczba odebranych elementów wysłanych z chmury do urządzenia |833.33/sek/jednostkę (50 000/min/jednostka) (dla S3), 16.67/sek/jednostkę (1000/min/jednostka) (w przypadku S1 i S2). |
| Operacje przekazywania plików |83,33 liczba inicjacji przekazywania plików na sekundę/jednostkę (5000/min/jednostka) (dla S3), 1,67/liczba inicjacji przekazywania plików na sekundę/jednostkę (100/min/jednostka) (w przypadku S1 i S2). <br/> Identyfikatory URI 10 000 SAS mogą być jednocześnie dla konta usługi Azure Storage.<br/> Jednocześnie może istnieć 10 identyfikatorów URI sygnatury dostępu współdzielonego. |
| Metody bezpośrednie | 24 MB/s/jednostkę (dla S3), 480 KB/s/jednostkę (dla S2), 160 KB/s/jednostkę (S1).<br/> W oparciu o rozmiar miernika dławienia 8 KB. |
| Liczba odczytów bliźniaczej reprezentacji urządzenia | 500/sek/jednostkę (dla S3), maksymalnie 100/sek lub 10/sek/jednostkę (dla S2), 100/SEK (dla S1) |
| Liczba aktualizacji bliźniaczej reprezentacji urządzenia | 250/sek/jednostkę (dla S3), maksymalnie 50/SEK lub 5/sek/jednostkę (dla S2), 50/s (dla S1) |
| Operacje zadań <br/> (tworzenie, aktualizowanie, wyświetlanie i usuwanie) | 83.33/sek/jednostkę (5000/min/jednostka) (dla S3), 1.67/sek/jednostkę (100/min/jednostka) (w przypadku S2), 1.67/sek/jednostkę (100/min/jednostka) (dla S1). |
| Przepływność operacji zadań poszczególnych urządzeń | 50/SEK/jednostkę (dla S3), maksymalnie 10/s lub 1/sek/jednostkę (dla S2), 10/s (dla S1). |
| Wskaźnik inicjacji strumienia urządzeń | 5 nowych strumieni/s (tylko dla S1, S2, S3 i F1). |
