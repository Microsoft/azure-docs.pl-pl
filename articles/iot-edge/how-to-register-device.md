---
title: Rejestrowanie nowego urządzenia Azure IoT Edge | Microsoft Docs
description: Użyj rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure, aby zarejestrować nowe urządzenie IoT Edge i pobrać parametry połączenia
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/08/2020
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 85a5e2f6b28b9332e5ad3a38cdad3fb02bb3da87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450141"
---
# <a name="register-an-azure-iot-edge-device"></a>Rejestrowanie urządzenia Azure IoT Edge

Aby móc korzystać z urządzeń IoT w Azure IoT Edge, musisz zarejestrować je w usłudze IoT Hub. Po zarejestrowaniu urządzenia można pobrać parametry połączenia w celu skonfigurowania urządzenia na potrzeby obciążeń IoT Edge.

Użytkownik ma możliwość rejestracji przy użyciu jednego z następujących narzędzi:

* [Zarejestrowanie urządzenia w Azure Portal,](#register-in-the-azure-portal) Jeśli wolisz graficzny interfejs użytkownika do tworzenia i wyświetlania zasobów platformy Azure oraz zarządzania nimi.
* [Zarejestrowanie urządzenia w usłudze Visual Studio Code](#register-with-visual-studio-code) , jeśli wolisz zarządzać zasobami usługi Azure IoT w tym samym miejscu, w którym tworzysz rozwiązania IoT.
* [Zarejestrowanie urządzenia w interfejsie wiersza polecenia platformy Azure](#register-with-the-azure-cli) , jeśli wolisz używać narzędzi wiersza poleceń do zarządzania zasobami platformy Azure, lub zamierzaj zautomatyzować zadania.

## <a name="register-in-the-azure-portal"></a>Zarejestruj się w Azure Portal

Wszystkie zadania rejestracji można wykonać w Azure Portal.

### <a name="prerequisites-for-the-azure-portal"></a>Wymagania wstępne dotyczące Azure Portal

Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Utwórz urządzenie IoT Edge w Azure Portal

W IoT Hub w Azure Portal IoT Edge urządzenia są tworzone i zarządzane oddzielnie od urządzeń IOT, które nie są włączone.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.
2. W lewym okienku wybierz **IoT Edge** z menu.
3. Wybierz pozycję **Dodaj urządzenie IoT Edge**.
4. Podaj opisowy identyfikator urządzenia. Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i połączyć nowe urządzenie z centrum.
5. Wybierz pozycję **Zapisz**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Wyświetlanie IoT Edge urządzeń w Azure Portal

Wszystkie urządzenia z systemem Edge, które łączą się z Centrum IoT, są wyświetlane na stronie **IoT Edge** .

![Wyświetl wszystkie IoT Edge urządzenia w centrum IoT Hub](./media/how-to-register-device/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Pobierz parametry połączenia w Azure Portal

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT.

1. Na stronie **IoT Edge** w portalu kliknij identyfikator urządzenia na liście urządzeń IoT Edge.
2. Skopiuj wartość **podstawowych parametrów połączenia** lub **pomocniczych parametrów połączenia**.

## <a name="register-with-visual-studio-code"></a>Zarejestruj się w Visual Studio Code

Istnieje wiele sposobów wykonywania większości operacji w VS Code. W tym artykule jest używany Eksplorator, ale można również wykonać kroki przy użyciu palety poleceń.

### <a name="prerequisites-for-visual-studio-code"></a>Wymagania wstępne dotyczące Visual Studio Code

* [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

Możesz użyć rozszerzeń usługi Azure IoT, aby Visual Studio Code do wykonywania operacji przy użyciu IoT Hub. Aby te operacje działały, należy zalogować się do konta platformy Azure i wybrać IoT Hub.

1. W Visual Studio Code Otwórz widok **Eksploratora** .
1. W dolnej części Eksploratora rozwiń sekcję **IoT Hub platformy Azure** .

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kliknij pozycję **...** w nagłówku sekcji **IoT Hub platformy Azure** . Jeśli nie widzisz wielokropka, kliknij pozycję lub umieść wskaźnik myszy na nagłówku.
1. Wybierz **pozycję wybierz IoT Hub**.
1. Jeśli nie zalogowano się na koncie platformy Azure, postępuj zgodnie z monitami, aby to zrobić.
1. Wybierz swoją subskrypcję platformy Azure.
1. Wybierz Centrum IoT Hub.

### <a name="create-an-iot-edge-device-with-visual-studio-code"></a>Tworzenie urządzenia IoT Edge z Visual Studio Code

1. W Eksploratorze VS Code rozwiń sekcję **urządzenia IoT Hub platformy Azure** .
1. Kliknij pozycję **...** w nagłówku sekcji **urządzenia IoT Hub Azure** . Jeśli nie widzisz wielokropka, kliknij pozycję lub umieść wskaźnik myszy na nagłówku.
1. Wybierz pozycję **Utwórz urządzenie IoT Edge**.
1. W otwartym polu tekstowym nadaj urządzeniu identyfikator.

Na ekranie dane wyjściowe zostanie wyświetlony wynik polecenia. Informacje o urządzeniu są drukowane wraz z dostarczonym identyfikatorem **deviceId** oraz urządzeniem **ConnectionString** , które służy do łączenia urządzenia fizycznego z Centrum IoT.

Na ekranie dane wyjściowe zostanie wyświetlony wynik polecenia. Informacje o urządzeniu są drukowane wraz z dostarczonym identyfikatorem **deviceId** oraz urządzeniem **ConnectionString** , które służy do łączenia urządzenia fizycznego z Centrum IoT.

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Wyświetlanie IoT Edge urządzeń z Visual Studio Code

Wszystkie urządzenia, które łączą się z Centrum IoT, są wymienione w sekcji **IoT Hub platformy Azure** w Eksploratorze Visual Studio Code. Urządzenia IoT Edge są odróżniane od urządzeń innych niż brzegowe z inną ikoną, a fakt, że moduły **$edgeAgent** i **$edgeHub** są wdrażane na poszczególnych urządzeniach IoT Edge.

![Wyświetl wszystkie IoT Edge urządzenia w centrum IoT Hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Pobierz parametry połączenia z Visual Studio Code

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT.

1. Kliknij prawym przyciskiem myszy identyfikator urządzenia w sekcji **IoT Hub platformy Azure** .
1. Wybierz pozycję **Kopiuj parametry połączenia urządzenia**.

   Parametry połączenia są kopiowane do Schowka.

Możesz również wybrać pozycję **Pobierz informacje o urządzeniu** w menu rozwijanym prawym przyciskiem myszy, aby wyświetlić wszystkie informacje o urządzeniu, w tym parametry połączenia, w oknie danych wyjściowych.

## <a name="register-with-the-azure-cli"></a>Zarejestruj się w interfejsie wiersza polecenia platformy Azure

Interfejs wiersza polecenia [platformy Azure](/cli/azure) to narzędzie wielodostępnej do obsługi wielu platform i zarządzania zasobami platformy Azure, takimi jak IoT Edge. Umożliwia ona zarządzanie zasobami IoT Hub platformy Azure, wystąpieniami usługi Device Provisioning Service i połączonymi centrami spoza pola. Rozszerzenie IoT wzbogaca interfejs wiersza polecenia platformy Azure z funkcjami takimi jak zarządzanie urządzeniami i pełna IoT Edge.

### <a name="prerequisites-for-the-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Tworzenie urządzenia IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) , aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

To polecenie zawiera trzy parametry:

* **Identyfikator urządzenia**: Podaj opisową nazwę, która jest unikatowa dla Centrum IoT Hub.
* **nazwa centrum**: Podaj nazwę Centrum IoT Hub.
* z **włączoną krawędzią**: ten parametr deklaruje, że urządzenie jest używane z IoT Edge.

   ![AZ IoT Hub Device-Identity Create Output](./media/how-to-register-device/Create-edge-device.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Wyświetlanie IoT Edge urządzeń za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity list](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) , aby wyświetlić wszystkie urządzenia w centrum IoT Hub. Na przykład:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Każde urządzenie zarejestrowane jako urządzenie IoT Edge będzie miało **możliwość właściwości. iotEdge** ustawione na **wartość true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Pobieranie parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT. Użyj polecenia [AZ IoT Hub Device-Identity Connection-String show](/cli/azure/ext/azure-iot/iot/hub/device-identity/connection-string#ext_azure_iot_az_iot_hub_device_identity_connection_string_show) , aby zwrócić parametry połączenia dla pojedynczego urządzenia:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

W wartości `device-id` parametru jest rozróżniana wielkość liter. Nie Kopiuj znaków cudzysłowu wokół parametrów połączenia.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz już zarejestrowaną tożsamość urządzenia w usłudze IoT Hub, możesz zainstalować środowisko uruchomieniowe IoT Edge na swoich urządzeniach. Zainstaluj środowisko uruchomieniowe zgodnie z systemem operacyjnym urządzenia:

* [Zainstaluj Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md)
* [Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)
