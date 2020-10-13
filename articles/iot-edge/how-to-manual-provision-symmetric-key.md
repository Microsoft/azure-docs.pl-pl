---
title: Udostępnianie kluczy symetrycznych — Azure IoT Edge | Microsoft Docs
description: Po instalacji należy zastanowić IoT Edge urządzenie z kluczami symetrycznymi przy użyciu jego parametrów połączenia i uwierzytelnić się na IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 9e288bcbebe4118bfc8cfa7cff46c79d7075555a
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979717"
---
# <a name="set-up-an-azure-iot-edge-device-with-symmetric-key-authentication"></a>Konfigurowanie urządzenia Azure IoT Edge z uwierzytelnianiem przy użyciu klucza symetrycznego

W tym artykule przedstawiono procedurę rejestrowania nowego urządzenia IoT Edge w usłudze IoT Hub i konfigurowania urządzenia do uwierzytelniania przy użyciu kluczy symetrycznych.

Kroki opisane w tym artykule przeprowadzą proces o nazwie Ręczne inicjowanie obsługi, gdzie można ręcznie podłączyć poszczególne urządzenia do centrum IoT Hub. Alternatywą jest automatyczne Inicjowanie obsługi przy użyciu IoT Hub Device Provisioning Service, co jest przydatne, gdy masz wiele urządzeń do aprowizacji.

<!--TODO: Add auto-provision info/links-->

W przypadku ręcznego inicjowania obsługi administracyjnej dostępne są dwie opcje uwierzytelniania urządzeń IoT Edgeych:

* **Klucz symetryczny**: podczas tworzenia nowej tożsamości urządzenia w IoT Hub Usługa tworzy dwa klucze. Należy umieścić jeden z kluczy na urządzeniu i przedstawiał klucz do IoT Hub podczas uwierzytelniania.

  Ta metoda uwierzytelniania jest szybsza, aby rozpocząć pracę, ale nie jako bezpieczna.

* **Z podpisem własnym x. 509**: tworzysz dwa certyfikaty tożsamości x. 509 i umieść je na urządzeniu. Podczas tworzenia nowej tożsamości urządzenia w IoT Hub można podawać odciski palców z obu certyfikatów. Gdy urządzenie zostanie uwierzytelnione do IoT Hub, przedstawia jego certyfikaty i IoT Hub może sprawdzić, czy pasują do odcisków palców.

  Ta metoda uwierzytelniania jest bezpieczniejsza i zalecana w scenariuszach produkcyjnych.

W tym artykule omówiono proces rejestracji i aprowizacji z uwierzytelnianiem przy użyciu klucza symetrycznego. Jeśli chcesz dowiedzieć się, jak skonfigurować urządzenie za pomocą certyfikatów X. 509, zobacz [Konfigurowanie urządzenia Azure IoT Edge przy użyciu uwierzytelniania certyfikatu x. 509](how-to-manual-provision-x509.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule należy mieć urządzenie z zainstalowanym środowiskiem uruchomieniowym IoT Edge. W przeciwnym razie wykonaj czynności opisane w temacie [Instalowanie lub Odinstalowywanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md).

## <a name="register-a-new-device"></a>Rejestrowanie nowego urządzenia

Każde urządzenie, które nawiązuje połączenie z IoT Hub ma identyfikator urządzenia, który służy do śledzenia komunikacji między chmurą i urządzeniem a chmurą. Można skonfigurować urządzenie ze swoimi informacjami o połączeniu, które obejmują nazwę hosta usługi IoT Hub, identyfikator urządzenia i informacje, które są wykorzystywane przez urządzenie do uwierzytelniania w IoT Hub.

W przypadku uwierzytelniania przy użyciu klucza symetrycznego te informacje są zbierane w *ciągu połączenia* , który można pobrać z IoT Hub a następnie umieścić na urządzeniu IoT Edge.

Za pomocą kilku narzędzi można zarejestrować nowe urządzenie IoT Edge w usłudze IoT Hub i pobrać jego parametry połączenia, w zależności od preferencji.

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Wymagania wstępne dotyczące Azure Portal

Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Utwórz urządzenie IoT Edge w Azure Portal

W IoT Hub w Azure Portal IoT Edge urządzenia są tworzone i zarządzane oddzielnie od urządzeń IOT, które nie są włączone.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W lewym okienku wybierz pozycję **IoT Edge** z menu, a następnie wybierz pozycję **Dodaj urządzenie IoT Edge**.

   ![Dodaj urządzenie IoT Edge z Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na stronie **Tworzenie urządzenia** podaj następujące informacje:

   * Utwórz opisowy identyfikator urządzenia.
   * Wybierz **klucz symetryczny** jako typ uwierzytelniania.
   * Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i połączyć nowe urządzenie z centrum.

1. Wybierz pozycję **Zapisz**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Wyświetlanie IoT Edge urządzeń w Azure Portal

Wszystkie urządzenia z systemem Edge, które łączą się z Centrum IoT, są wyświetlane na stronie **IoT Edge** .

![Użyj Azure Portal, aby wyświetlić wszystkie urządzenia IoT Edge w centrum IoT Hub](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

### <a name="retrieve-the-connection-string-in-the-azure-portal"></a>Pobierz parametry połączenia w Azure Portal

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT.

1. Na stronie **IoT Edge** w portalu kliknij identyfikator urządzenia na liście urządzeń IoT Edge.
2. Skopiuj wartość **podstawowych parametrów połączenia** lub **pomocniczych parametrów połączenia**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="prerequisites-for-visual-studio-code"></a>Wymagania wstępne dotyczące Visual Studio Code

* Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla Visual Studio Code

### <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

Możesz użyć rozszerzeń usługi Azure IoT, aby Visual Studio Code do wykonywania operacji przy użyciu IoT Hub. Aby te operacje działały, należy zalogować się do konta platformy Azure i wybrać IoT Hub.

1. W Visual Studio Code Otwórz widok **Eksploratora** .
1. W dolnej części Eksploratora rozwiń sekcję **IoT Hub platformy Azure** .

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-manual-provision-symmetric-key/azure-iot-hub-devices.png)

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

![Używanie VS Code do wyświetlania wszystkich urządzeń IoT Edge w centrum IoT Hub](./media/how-to-manual-provision-symmetric-key/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Pobierz parametry połączenia z Visual Studio Code

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT.

1. Kliknij prawym przyciskiem myszy identyfikator urządzenia w sekcji **IoT Hub platformy Azure** .
1. Wybierz pozycję **Kopiuj parametry połączenia urządzenia**.

   Parametry połączenia są kopiowane do Schowka.

Możesz również wybrać pozycję **Pobierz informacje o urządzeniu** w menu rozwijanym prawym przyciskiem myszy, aby wyświetlić wszystkie informacje o urządzeniu, w tym parametry połączenia, w oknie danych wyjściowych.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Tworzenie urządzenia IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) , aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

To polecenie zawiera trzy parametry:

* `--device-id` lub `-d` : Podaj opisową nazwę, która jest unikatowa dla Centrum IoT Hub.
* `hub-name` lub `-n` : Podaj nazwę Centrum IoT.
* `--edge-enabled` lub `--ee` : Zadeklaruj, że urządzenie jest urządzeniem IoT Edge.

   ![AZ IoT Hub Device-Identity Create Output](./media/how-to-manual-provision-symmetric-key/create-edge-device-cli.png)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Wyświetlanie IoT Edge urządzeń za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) , aby wyświetlić wszystkie urządzenia w centrum IoT Hub. Na przykład:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Każde urządzenie zarejestrowane jako urządzenie IoT Edge będzie miało **możliwość właściwości. iotEdge** ustawione na **wartość true**.

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Pobieranie parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT. Użyj polecenia [AZ IoT Hub Device-Identity show-Connection-String](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) , aby zwrócić parametry połączenia dla pojedynczego urządzenia:

   ```azurecli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name]
   ```

W wartości `device-id` parametru jest rozróżniana wielkość liter. Nie Kopiuj znaków cudzysłowu wokół parametrów połączenia.

---

## <a name="provision-an-iot-edge-device"></a>Aprowizowanie urządzenia usługi IoT Edge

Gdy urządzenie IoT Edge ma tożsamość w IoT Hub i parametry połączenia, które mogą być używane do uwierzytelniania, należy udostępnić urządzeniu informacje.

Na urządzeniu z systemem Linux parametry połączenia można podawać, edytując plik config. YAML. Na urządzeniu z systemem Windows podano parametry połączenia, uruchamiając skrypt programu PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje aprowizacji pliku i usuń znaczniki **ręcznej konfiguracji aprowizacji za pomocą sekcji parametrów połączenia** . 

Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

```yml
# Manual provisioning configuration using a connection string
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  dynamic_reprovisioning: false
```

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

```bash
sudo systemctl restart iotedge
```

# <a name="windows"></a>[Windows](#tab/windows)

1. Na urządzeniu IoT Edge Uruchom program PowerShell jako administrator.

2. Użyj polecenia [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) , aby skonfigurować środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualConnectionString -ContainerOs Windows
   ```

   * Jeśli używasz kontenerów systemu Linux, Dodaj `-ContainerOs` parametr do flagi. Być spójne z opcją kontenera wybraną z `Deploy-IoTEdge` poleceniem, które zostało wcześniej uruchomione.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ContainerOs Linux
      ```

   * Jeśli pobrano skrypt IoTEdgeSecurityDaemon.ps1 na urządzenie w celu instalacji w trybie offline lub określonej wersji, należy się upewnić, aby odwołać się do lokalnej kopii skryptu.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po wyświetleniu monitu podaj parametry połączenia urządzenia pobrane w poprzedniej sekcji. Parametry połączenia urządzenia kojarzą urządzenie fizyczne z IDENTYFIKATORem urządzenia w IoT Hub i udostępniają informacje o uwierzytelnianiu.

   Parametry połączenia urządzenia mają następujący format i nie powinny zawierać znaków cudzysłowu: `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

W przypadku ręcznego aprowizacji urządzenia można użyć dodatkowych parametrów, aby zmodyfikować proces, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Zadeklaruj określony obraz kontenera edgeAgent i podaj poświadczenia, jeśli znajduje się on w rejestrze prywatnym

Aby uzyskać więcej informacji o tych dodatkowych parametrach, zobacz [skrypty programu PowerShell dla IoT Edge w systemie Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md) , aby dowiedzieć się, jak wdrażać moduły na urządzeniu.
