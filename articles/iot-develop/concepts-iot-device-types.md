---
title: Przegląd typów urządzeń usługi Azure IoT
description: Poznaj różne typy urządzeń obsługiwane przez usługę Azure IoT i dostępne narzędzia.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: aa99594fe3de98635e37d15beebf015f15dc4f64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100654371"
---
# <a name="overview-of-azure-iot-device-types"></a>Przegląd typów urządzeń usługi Azure IoT
Urządzenia IoT istnieją w szerokim zakresie platform sprzętowych. W przypadku małych procesorów x86, które znajdują się na komputerze stacjonarnym, istnieją małe MCUs 8-bitowe. Wiele zmiennych jest czynnikiem do decyzji, dla którego sprzętu należy wybrać dla urządzenia IoT, a w tym artykule omówiono niektóre kluczowe różnice.

## <a name="key-hardware-differentiators"></a>Najważniejsze odróżnia sprzętowe
Niektóre ważne czynniki, które należy wziąć pod uwagę w przypadku wybrania sprzętu, są koszty, zużycie mocy, Sieć i dostępne dane wejściowe i wyjściowe.

* **Koszt:** Mniejsze tańsze urządzenia są zwykle używane podczas masowego tworzenia produktu końcowego. Jednak ten rodzaj handlu polega na tym, że programowanie urządzenia może być droższe dla urządzenia z wysoce ograniczonymi ograniczeniami. Koszt programistyczny może być rozłożony na wszystkie wytwarzane urządzenia, dzięki czemu koszt na rozwój jednostki będzie niski.

* **Moc:** Ilość zużywanej przez urządzenie jest ważna, jeśli urządzenie będzie używać baterii i nie połączyło się z siatką zasilania. MCUs są często zaprojektowane dla małych scenariuszy zasilania i mogą być lepszym wyborem w przypadku wydłużenia czasu pracy baterii.

* **Dostęp do sieci:** Istnieje wiele sposobów podłączenia urządzenia do usługi w chmurze. Ethernet, Wi-Fi i sieć komórkowa oraz niektóre z dostępnych opcji. Wybrany typ połączenia będzie zależeć od lokalizacji wdrożenia urządzenia i sposobu jego użycia. Na przykład sieć komórkowa może być atrakcyjną opcją mającą wysokie pokrycie, jednak w przypadku urządzeń o dużym natężeniu ruchu może być kosztowna. Hardwired Ethernet zapewnia tańsze koszty danych, ale z minusemem mniej przenośnego.

* **Wejście i wyjście:** Dane wejściowe i wyjściowe dostępne na urządzeniu bezpośrednio wpływają na możliwości operacyjne urządzeń. Mikrokontroler zwykle ma wiele funkcji we/wy wbudowanych bezpośrednio do mikroukładu i zapewnia szeroki wybór czujników do bezpośredniego łączenia.

## <a name="microcontrollers-vs-microprocessors"></a>Mikrokontrolerzy mikroprocesorów
Urządzenia IoT można rozdzielić na dwie szerokie Kategorie: mikrokontrolery (MCUs) i mikroprocesorów (MPUs).

**MCUs** są tańsze i prostsze do działania niż MPUs. MIKROKONTROLERY będzie zawierać wiele funkcji, takich jak pamięć, interfejsy i we/wy w samym układzie. MPU narysuje tę funkcję ze składników obsługujących mikroukłady. MIKROKONTROLERY często będzie używać systemu operacyjnego w czasie rzeczywistym (RTO) lub systemu operacyjnego bez systemu operacyjnego, a także zapewnia odpowiedź w czasie rzeczywistym i wysoce deterministyczne reakcje na zdarzenia zewnętrzne.

**MPUs** zazwyczaj uruchamia system operacyjny ogólnego przeznaczenia, taki jak Windows, Linux lub MacOSX, który zapewnia niedeterministyczną odpowiedź w czasie rzeczywistym. Zazwyczaj nie ma gwarancji, że w przypadku zadania zostanie ukończone. 

:::image type="content" border="false" source="media/concepts-iot-device-types/mcu-vs-mpu.png" alt-text="MIKROKONTROLERY vs MPU":::

Poniżej znajduje się tabela przedstawiająca niektóre z definiowania różnic między MIKROKONTROLERY i systemem opartym na MPU:

||Mikrokontroler (MIKROKONTROLERY)|Mikroprocesor (MPU)|
|-|-|-|
|**Procesor CPU**| Less | Więcej |
|**NIEGO**| Less | Więcej |
|**Flash**| Less | Więcej |
|**System operacyjny**| Nie lub RTO | Ogólnego przeznaczenia |
|**Trudności programistyczne**| Utrudnia |  Czytelnooci |
|**Zużycie mocy**| Niższa | Wyższa |
|**Koszty**| Niższa | Wyższa |
|**Deterministyczne**| Tak | Nie — z wyjątkami|
|**Rozmiar urządzenia**| Niższa | Krocz |
