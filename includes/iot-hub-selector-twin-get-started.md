---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050392"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Bliźniacze reprezentacje urządzeń to dokumenty JSON, które przechowują informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. IoT Hub utrzymuje sznurki urządzenia dla każdego urządzenia, które nawiązuje z nim połączenie.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Użyj bliźniaczych reprezentacji urządzenia, aby:

* Przechowuj metadane urządzenia z zaplecza rozwiązania.

* Zgłoś bieżące informacje o stanie, takie jak dostępne możliwości i warunki, na przykład używaną metodę łączności, z aplikacji urządzenia.

* Zsynchronizuj stan długotrwałych przepływów pracy, takich jak oprogramowanie układowe i aktualizacje konfiguracji, między aplikacją urządzenia a aplikacją zaplecza.

* Wykonaj zapytanie dotyczące metadanych, konfiguracji lub stanu urządzenia.

Urządzenia bliźniaczych reprezentacji są przeznaczone do synchronizacji oraz do wykonywania zapytań dotyczących konfiguracji i warunków urządzeń. Więcej informacji o tym, kiedy używać bliźniaczych reprezentacji urządzeń, można znaleźć w temacie [Omówienie urządzenia bliźniaczych reprezentacji](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Urządzenia bliźniaczych reprezentacji są przechowywane w centrum IoT Hub i zawierają następujące elementy:

* **Tagi**. Metadane urządzenia dostępne tylko dla zaplecza rozwiązania.

* **Żądane właściwości**. Obiekty JSON modyfikowane przez zaplecze rozwiązania i zauważalne przez aplikację urządzenia.

* **Raportowane właściwości**. Obiekty JSON modyfikowane przez aplikację urządzenia i odczytywane przez zaplecze rozwiązania.

Tagi i właściwości nie mogą zawierać tablic, ale obiekty mogą być zagnieżdżane.

Na poniższej ilustracji przedstawiono organizację wieloosiową urządzeń:

![Obraz przedstawiający sznurki urządzenia z możliwością](./media/iot-hub-selector-twin-get-started/twin.png)

Zaplecze rozwiązania może dodatkowo wykonywać zapytania dotyczące bliźniaczych reprezentacji urządzenia w oparciu o wszystkie powyższe dane.
Aby uzyskać więcej informacji na temat bliźniaczych reprezentacji urządzeń, zobacz [Omówienie urządzenia bliźniaczych reprezentacji](../articles/iot-hub/iot-hub-devguide-device-twins.md). Aby uzyskać więcej informacji na temat wykonywania zapytań, zobacz [IoT Hub języka zapytań](../articles/iot-hub/iot-hub-devguide-query-language.md).


Ten samouczek przedstawia sposób wykonania następujących czynności:

* Utwórz aplikację zaplecza, która dodaje Tagi do sznurka urządzenia, oraz aplikację symulowanego urządzenia, która zgłasza swój kanał łączności jako raportowaną właściwość na sznurze urządzeń.

* Twórz zapytania dotyczące urządzeń z aplikacji zaplecza przy użyciu filtrów dla utworzonych wcześniej tagów i właściwości.
