---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 239ea2eb4d5bc8d326d5ca503a18b149252dc1be
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "69558737"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-device-management-get-started.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-device-management-get-started.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-device-management-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-python-device-management-get-started.md)

Aplikacje zaplecza mogą korzystać z usługi Azure IoT Hub prymitywów, takich jak [sznurki urządzenia][lnk-devtwin] i [metody bezpośrednie][lnk-c2dmethod], aby zdalnie uruchamiać i monitorować akcje zarządzania urządzeniami na urządzeniach. Ten samouczek pokazuje, w jaki sposób aplikacja zaplecza i aplikacja urządzenia mogą współdziałać, aby inicjować i monitorować ponowne uruchomienie urządzenia zdalnego przy użyciu IoT Hub.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Użyj metody bezpośredniej, aby zainicjować akcje zarządzania urządzeniami (takie jak ponowne uruchamianie, Resetowanie do ustawień fabrycznych i aktualizacja oprogramowania układowego) z aplikacji zaplecza w chmurze. Urządzenie jest odpowiedzialne za:

* Obsługa żądania metody wysyłanego z IoT Hub.

* Inicjowanie odpowiedniej akcji specyficznej dla urządzenia na urządzeniu.

* Dostarczanie aktualizacji stanu za poorednictwem *raportowanych właściwości* do IoT Hub.

Możesz użyć aplikacji zaplecza w chmurze, aby uruchamiać zapytania o pojedynczej urządzeniu w celu raportowania postępu akcji związanych z zarządzaniem urządzeniami.

[lnk-devtwin]: ../articles/iot-hub/iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
