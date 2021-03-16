---
title: Tłumaczenie protokołów Modbus na bramy — Azure IoT Edge | Microsoft Docs
description: Zezwalanie urządzeniom korzystających z protokołu Modbus TCP na komunikację z usługą Azure IoT Hub poprzez utworzenie urządzenia bramy usługi IoT Edge
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 0388520903e208b3225375d5cee81e8321740a1b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103487738"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>Łączenie urządzeń Modbus TCP za pośrednictwem bramy urządzenia IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Jeśli chcesz połączyć urządzenia IoT korzystające z protokołów Modbus TCP lub RTU z usługą Azure IoT Hub, możesz użyć urządzenia IoT Edge jako bramy. Urządzenie bramy odczytuje dane z urządzeń korzystających z protokołu Modbus, a następnie przekazuje te dane do chmury przy użyciu obsługiwanego protokołu.

![Urządzenia Modbus łączą się z IoT Hub za poorednictwem bramy IoT Edge](./media/deploy-modbus-gateway/diagram.png)

W tym artykule opisano sposób tworzenia własnego obrazu kontenera dla modułu Modbus (można też użyć wstępnie utworzonej próbki), a następnie wdrożenia go do urządzenia usługi IoT Edge, które będzie pełnić rolę bramy.

W tym artykule założono, że użytkownik korzysta z protokołu Modbus TCP. Więcej informacji o sposobie konfigurowania modułu do obsługi Modbus RTU można znaleźć w projekcie [modułu Azure IoT Edge Modbus](https://github.com/Azure/iot-edge-modbus) w witrynie GitHub.

## <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie usługi Azure IoT Edge. Aby zapoznać się z przewodnikiem dotyczącym konfigurowania jednego z nich, zobacz [wdrażanie Azure IoT Edge w systemie Windows](quickstart.md) lub [Linux](quickstart-linux.md).
* Parametry połączenia klucza podstawowego dla urządzenia usługi IoT Edge.
* Fizyczne lub symulowane urządzenie Modbus obsługujące protokół Modbus TCP. Musisz znać swój adres IPv4.

## <a name="prepare-a-modbus-container"></a>Przygotowanie kontenera Modbus

Jeśli chcesz przetestować funkcje bramy Modbus, firma Microsoft przygotowała przykładowy moduł, którego możesz użyć. Możesz uzyskać dostęp do modułu z witryny Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)lub z identyfikatorem URI obrazu `mcr.microsoft.com/azureiotedge/modbus:1.0` .

Jeśli chcesz utworzyć własny moduł i dostosować go do swojego środowiska, istnieje [Azure IoT Edge projektu modułu](https://github.com/Azure/iot-edge-modbus) "open source" w witrynie GitHub. Wykonaj instrukcje zawarte w tym projekcie, aby utworzyć własny obraz kontenera. Aby utworzyć obraz kontenera, zapoznaj się z tematem [Tworzenie modułów C# w programie Visual Studio](./how-to-visual-studio-develop-module.md) lub [opracowywanie modułów w Visual Studio Code](how-to-vs-code-develop-module.md). Te artykuły zawierają instrukcje dotyczące tworzenia nowych modułów i publikowania obrazów kontenerów w rejestrze.

## <a name="try-the-solution"></a>Wypróbuj rozwiązanie

Ta sekcja zawiera instrukcje wdrażania przykładowego modułu Modbus firmy Microsoft na urządzeniu IoT Edge.

1. W witrynie [Azure Portal](https://portal.azure.com/) przejdź do centrum IoT Hub.

2. Przejdź do **IoT Edge** i kliknij urządzenie IoT Edge.

3. Wybierz pozycję **Ustaw moduły**.

4. W sekcji **IoT Edge modułów** Dodaj moduł Modbus:

   1. Kliknij przycisk **Dodaj** listę rozwijaną i wybierz **moduł Marketplace**.
   2. Wyszukaj `Modbus` i wybierz **moduł Modbus TCP** firmy Microsoft.
   3. Moduł jest automatycznie konfigurowany dla IoT Hub i pojawia się na liście modułów IoT Edge. Trasy są również automatycznie konfigurowane. Wybierz pozycję **Przejrzyj i utwórz**.
   4. Przejrzyj manifest wdrożenia i wybierz pozycję **Utwórz**.

5. Wybierz moduł Modbus, `ModbusTCPModule` na liście i wybierz kartę **Ustawienia sznurka modułu** . Wymagany kod JSON dla potrzebnych właściwości sznurka modułu jest wypełniany automatycznie.

6. Wyszukaj Właściwość **SlaveConnection** w formacie JSON i ustaw jej wartość na adres IPv4 urządzenia Modbus.

7. Wybierz pozycję **Aktualizuj**.

8. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj wdrożenie, a następnie wybierz pozycję **Utwórz**.

9. Wróć do strony szczegółów urządzenia i wybierz opcję **Odśwież**. Powinien zostać wyświetlony nowy `ModbusTCPModule` moduł wraz z IoT Edge środowiska uruchomieniowego.

## <a name="view-data"></a>Wyświetlanie danych

Wyświetlanie danych przechodzących przez moduł Modbus:

```cmd/sh
iotedge logs modbus
```

Możesz również wyświetlić dane telemetryczne wysyłane przez urządzenie przy użyciu [rozszerzenia IoT Hub platformy Azure dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dawniej rozszerzenie usługi Azure IoT Toolkit).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o tym, jak urządzenia IoT Edge mogą działać jako bramy, zobacz [tworzenie IoT Edge urządzenia, które działa jako niejawna Brama](./how-to-create-transparent-gateway.md).
* Aby uzyskać więcej informacji na temat sposobu działania modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](iot-edge-modules.md).