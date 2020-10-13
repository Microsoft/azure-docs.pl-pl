---
title: Inicjowanie obsługi administracyjnej za pomocą certyfikatów X. 509 — Azure IoT Edge | Microsoft Docs
description: Po instalacji Zainicjuj obsługę urządzenia IoT Edge przy użyciu certyfikatów tożsamości urządzenia i Uwierzytelnij się do IoT Hub
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: 8cfb7c5a0821bd030252a105b98b1c138b9ef820
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979729"
---
# <a name="set-up-an-azure-iot-edge-device-with-x509-certificate-authentication"></a>Konfigurowanie urządzenia Azure IoT Edge przy użyciu uwierzytelniania certyfikatu X. 509

W tym artykule przedstawiono procedurę rejestrowania nowego urządzenia IoT Edge w usłudze IoT Hub i konfigurowania urządzenia do uwierzytelniania za pomocą certyfikatów X. 509.

Kroki opisane w tym artykule przeprowadzą proces o nazwie Ręczne inicjowanie obsługi, gdzie można ręcznie podłączyć poszczególne urządzenia do centrum IoT Hub. Alternatywą jest automatyczne Inicjowanie obsługi przy użyciu IoT Hub Device Provisioning Service, co jest przydatne, gdy masz wiele urządzeń do aprowizacji.

<!--TODO: Add auto-provision info/links-->

W przypadku ręcznego inicjowania obsługi administracyjnej dostępne są dwie opcje uwierzytelniania urządzeń IoT Edgeych:

* **Klucz symetryczny**: podczas tworzenia nowej tożsamości urządzenia w IoT Hub Usługa tworzy dwa klucze. Należy umieścić jeden z kluczy na urządzeniu i przedstawiał klucz do IoT Hub podczas uwierzytelniania.

  Ta metoda uwierzytelniania jest szybsza, aby rozpocząć pracę, ale nie jako bezpieczna.

* **Z podpisem własnym x. 509**: tworzysz dwa certyfikaty tożsamości x. 509 i umieść je na urządzeniu. Podczas tworzenia nowej tożsamości urządzenia w IoT Hub można podawać odciski palców z obu certyfikatów. Gdy urządzenie zostanie uwierzytelnione do IoT Hub, przedstawia jego certyfikaty i IoT Hub może sprawdzić, czy pasują do odcisków palców.

  Ta metoda uwierzytelniania jest bezpieczniejsza i zalecana w scenariuszach produkcyjnych.

W tym artykule omówiono proces rejestracji i aprowizacji przy użyciu uwierzytelniania certyfikatu X. 509. Jeśli chcesz dowiedzieć się, jak skonfigurować urządzenie przy użyciu kluczy symetrycznych, zobacz [Konfigurowanie urządzenia Azure IoT Edge z uwierzytelnianiem przy użyciu klucza symetrycznego](how-to-manual-provision-symmetric-key.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed wykonaniem kroków opisanych w tym artykule należy mieć urządzenie z zainstalowanym środowiskiem uruchomieniowym IoT Edge. W przeciwnym razie wykonaj czynności opisane w temacie [Instalowanie lub Odinstalowywanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md).

Ręczne inicjowanie obsługi z certyfikatami X. 509 wymaga IoT Edge w wersji 1.0.10 lub nowszej.

## <a name="create-certificates-and-thumbprints"></a>Tworzenie certyfikatów i odcisków palców



<!-- TODO -->

## <a name="register-a-new-device"></a>Rejestrowanie nowego urządzenia

Każde urządzenie, które nawiązuje połączenie z IoT Hub ma identyfikator urządzenia, który służy do śledzenia komunikacji między chmurą i urządzeniem a chmurą. Można skonfigurować urządzenie ze swoimi informacjami o połączeniu, które obejmują nazwę hosta usługi IoT Hub, identyfikator urządzenia i informacje, które są wykorzystywane przez urządzenie do uwierzytelniania w IoT Hub.

W przypadku uwierzytelniania za pomocą certyfikatu X. 509 te informacje są podawane w formie *odcisków palców* z certyfikatów tożsamości urządzeń. Te odciski palców są nadawane IoT Hub w momencie rejestracji urządzenia, aby usługa mogła rozpoznać urządzenie po nawiązaniu połączenia.

Aby zarejestrować nowe urządzenie IoT Edge w usłudze IoT Hub i przekazać odciski palców certyfikatów, można użyć kilku narzędzi. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="prerequisites-for-the-azure-portal"></a>Wymagania wstępne dotyczące Azure Portal

Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.

### <a name="create-an-iot-edge-device-in-the-azure-portal"></a>Utwórz urządzenie IoT Edge w Azure Portal

W IoT Hub w Azure Portal IoT Edge urządzenia są tworzone i zarządzane oddzielnie od urządzeń IOT, które nie są włączone.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W lewym okienku wybierz pozycję **IoT Edge** z menu, a następnie wybierz pozycję **Dodaj urządzenie IoT Edge**.

   ![Dodaj urządzenie IoT Edge z Azure Portal](./media/how-to-manual-provision-symmetric-key/portal-add-iot-edge-device.png)

1. Na stronie **Tworzenie urządzenia** podaj następujące informacje:

   * Utwórz opisowy identyfikator urządzenia. Zanotuj ten identyfikator urządzenia, ponieważ będzie on używany w następnej sekcji.
   * Wybierz pozycję **X. 509 z podpisem własnym** jako typ uwierzytelniania.
   * Podaj odciski palców certyfikatu tożsamości podstawowej i pomocniczej. Wartości odcisków palca to 40 — znaki szesnastkowe dla skrótów SHA-1 i znaków 64-szesnastkowych dla skrótów SHA-256.

1. Wybierz pozycję **Zapisz**.

### <a name="view-iot-edge-devices-in-the-azure-portal"></a>Wyświetlanie IoT Edge urządzeń w Azure Portal

Wszystkie urządzenia z systemem Edge, które łączą się z Centrum IoT, są wyświetlane na stronie **IoT Edge** .

![Wyświetl wszystkie IoT Edge urządzenia w centrum IoT Hub](./media/how-to-manual-provision-symmetric-key/portal-view-devices.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="prerequisites-for-the-azure-cli"></a>Wymagania wstępne dotyczące interfejsu wiersza polecenia platformy Azure

* [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.
* [Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

### <a name="create-an-iot-edge-device-with-the-azure-cli"></a>Tworzenie urządzenia IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity Create](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) , aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

To polecenie zawiera kilka parametrów:

* `--device-id` lub `-d` : Podaj opisową nazwę, która jest unikatowa dla Centrum IoT Hub. Zanotuj ten identyfikator urządzenia, ponieważ będzie on używany w następnej sekcji.
* `hub-name` lub `-n` : Podaj nazwę Centrum IoT.
* `--edge-enabled` lub `--ee` : Zadeklaruj, że urządzenie jest urządzeniem IoT Edge.
* `--auth-method` lub `--am` : Zadeklaruj typ autoryzacji, który będzie używany przez urządzenie. W tym przypadku używamy odcisków palca certyfikatu X. 509.
* `--primary-thumbprint` lub `--ptp` : Podaj odcisk palca certyfikatu X. 509, który ma być używany jako klucz podstawowy.
* `--secondary-thumbprint` lub `--stp` : Podaj odcisk palca certyfikatu X. 509 do użycia jako klucz pomocniczy.

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Wyświetlanie IoT Edge urządzeń za pomocą interfejsu wiersza polecenia platformy Azure

Użyj polecenia [AZ IoT Hub Device-Identity list](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-list) , aby wyświetlić wszystkie urządzenia w centrum IoT Hub. Na przykład:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Dodaj flagę `--edge-enabled` lub `--ee` Aby wyświetlić listę tylko IoT Edge urządzeń w centrum IoT Hub.

Każde urządzenie zarejestrowane jako urządzenie IoT Edge będzie miało **możliwość właściwości. iotEdge** ustawione na **wartość true**.

--- 

## <a name="configure-an-iot-edge-device"></a>Konfigurowanie urządzenia usługi IoT Edge

Gdy urządzenie IoT Edge ma tożsamość w IoT Hub, należy skonfigurować urządzenie przy użyciu jego tożsamości w chmurze oraz certyfikatów tożsamości.

Na urządzeniu z systemem Linux podaj te informacje, edytując plik config. YAML. Na urządzeniu z systemem Windows podaj te informacje, uruchamiając skrypt programu PowerShell.

# <a name="linux"></a>[Linux](#tab/linux)

1. Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracje aprowizacji pliku. 

1. Dodaj komentarz do **ręcznej konfiguracji inicjowania obsługi przy użyciu sekcji parametrów połączenia** .

1. Usuń komentarz **ręcznej konfiguracji inicjowania obsługi przy użyciu sekcji certyfikatu tożsamości X. 509** . Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
   ```

1. Zaktualizuj następujące pola:

   * **iothub_hostname**: Nazwa hosta Centrum IoT, z którym zostanie nawiązane połączenie. Na przykład `{IoT hub name}.azure-devices.net`.
   * **device_ID**: identyfikator podany podczas rejestrowania urządzenia.
   * **identity_cert**: identyfikator URI do certyfikatu tożsamości na urządzeniu. Na przykład `file:///path/identity_certificate.pem`.
   * **identity_pk**: identyfikator URI pliku klucza prywatnego dla podanego certyfikatu tożsamości. Na przykład `file:///path/identity_key.pem`.

1. Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

1. Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

   ```bash
   sudo systemctl restart iotedge
   ```

# <a name="windows"></a>[Windows](#tab/windows)

1. Na urządzeniu IoT Edge Uruchom program PowerShell jako administrator.

2. Użyj polecenia [Initialize-IoTEdge](reference-windows-scripts.md#initialize-iotedge) , aby skonfigurować środowisko uruchomieniowe IoT Edge na komputerze.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -ManualX509
   ```

   * Jeśli używasz kontenerów systemu Linux, Dodaj `-ContainerOs` parametr do flagi. Być spójne z opcją kontenera wybraną z `Deploy-IoTEdge` poleceniem, które zostało wcześniej uruchomione.

      ```powershell
      . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
      Initialize-IoTEdge -ManualX509 -ContainerOs Linux
      ```

   * Jeśli pobrano skrypt IoTEdgeSecurityDaemon.ps1 na urządzenie w celu instalacji w trybie offline lub określonej wersji, należy się upewnić, aby odwołać się do lokalnej kopii skryptu.

      ```powershell
      . <path>/IoTEdgeSecurityDaemon.ps1
      Initialize-IoTEdge -ManualX509
      ```

3. Po wyświetleniu monitu podaj następujące informacje:

   * **IotHubHostName**: Nazwa hosta Centrum IoT, z którym zostanie nawiązane połączenie. Na przykład `{IoT hub name}.azure-devices.net`.
   * **DeviceID**: identyfikator podany podczas rejestrowania urządzenia.
   * **X509IdentityCertificate**: ścieżka bezwzględna do certyfikatu tożsamości na urządzeniu. Na przykład `C:\path\identity_certificate.pem`.
   * **X509IdentityPrivateKey**: ścieżka bezwzględna do pliku klucza prywatnego dla podanego certyfikatu tożsamości. Na przykład `C:\path\identity_key.pem`.

W przypadku ręcznego aprowizacji urządzenia można użyć dodatkowych parametrów, aby zmodyfikować proces, w tym:

* Bezpośredni ruch do przechodzenia przez serwer proxy
* Zadeklaruj określony obraz kontenera edgeAgent i podaj poświadczenia, jeśli znajduje się on w rejestrze prywatnym

Aby uzyskać więcej informacji o tych dodatkowych parametrach, zobacz [skrypty programu PowerShell dla IoT Edge w systemie Windows](reference-windows-scripts.md).

---

[!INCLUDE [Verify and troubleshoot installation](../../includes/iot-edge-verify-troubleshoot-install.md)]

## <a name="next-steps"></a>Następne kroki

Kontynuuj [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md) , aby dowiedzieć się, jak wdrażać moduły na urządzeniu.
