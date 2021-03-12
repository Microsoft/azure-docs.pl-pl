---
title: Zarejestruj nowe urządzenie — Azure IoT Edge | Microsoft Docs
description: Zarejestruj pojedyncze urządzenie IoT Edge w usłudze IoT Hub, aby ręcznie zainicjować obsługę administracyjną przy użyciu kluczy symetrycznych lub certyfikatów X. 509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: d75f184a324a9d418b0af2e3cf5790205af0fa42
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200718"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Rejestrowanie urządzenia IoT Edge w usłudze IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

W tym artykule przedstawiono procedurę rejestrowania nowego urządzenia IoT Edge w programie IoT Hub.

Każde urządzenie, które nawiązuje połączenie z usługą IoT Hub, ma identyfikator urządzenia, który służy do śledzenia komunikacji między chmurą i urządzeniem a chmurą. Można skonfigurować urządzenie ze swoimi informacjami o połączeniu, które obejmują nazwę hosta usługi IoT Hub, identyfikator urządzenia i informacje, które są wykorzystywane przez urządzenie do uwierzytelniania w IoT Hub.

Kroki opisane w tym artykule przeprowadzą proces o nazwie Ręczne inicjowanie obsługi, w którym można podłączyć pojedyncze urządzenie do centrum IoT Hub. W przypadku ręcznego inicjowania obsługi administracyjnej dostępne są dwie opcje uwierzytelniania urządzeń IoT Edgeych:

* **Klucz symetryczny**: podczas tworzenia nowej tożsamości urządzenia w IoT Hub Usługa tworzy dwa klucze. Należy umieścić jeden z kluczy na urządzeniu i przedstawiał klucz do IoT Hub podczas uwierzytelniania.

  Ta metoda uwierzytelniania jest szybsza, aby rozpocząć pracę, ale nie jako bezpieczna.

* **Z podpisem własnym x. 509**: tworzysz dwa certyfikaty tożsamości x. 509 i umieść je na urządzeniu. Podczas tworzenia nowej tożsamości urządzenia w IoT Hub można podawać odciski palców z obu certyfikatów. Gdy urządzenie zostanie uwierzytelnione do IoT Hub, przedstawia jeden certyfikat i IoT Hub sprawdza, czy certyfikat pasuje do jego odcisku palca.

  Ta metoda uwierzytelniania jest bezpieczniejsza i zalecana w scenariuszach produkcyjnych.

W tym artykule opisano metody uwierzytelniania.

Jeśli masz wiele urządzeń do skonfigurowania i nie chcesz, aby ręcznie zainicjować obsługę administracyjną każdego z nich, użyj jednego z poniższych artykułów, aby dowiedzieć się, jak IoT Edge współpracuje z IoT Hub Device Provisioning Service:

* [Tworzenie i Inicjowanie obsługi IoT Edge urządzeń za pomocą certyfikatów X. 509](how-to-auto-provision-x509-certs.md)
* [Tworzenie i udostępnianie urządzeń IoT Edge przy użyciu modułu TPM](how-to-auto-provision-simulated-device-linux.md)
* [Tworzenie i udostępnianie urządzeń IoT Edge przy użyciu kluczy symetrycznych](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Narzędzia usługi Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla Visual Studio Code

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

* Bezpłatne lub standardowe [Centrum IoT](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Minimalna wersja interfejsu wiersza polecenia platformy Azure musi być 2.0.70 lub nowsza. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Opcja 1: rejestrowanie przy użyciu kluczy symetrycznych

Za pomocą kilku narzędzi można zarejestrować nowe urządzenie IoT Edge w usłudze IoT Hub i pobrać jego parametry połączenia, w zależności od preferencji.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W centrum IoT w Azure Portal IoT Edge urządzenia są tworzone i zarządzane oddzielnie od urządzeń IoT, które nie są włączone.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W lewym okienku wybierz pozycję **IoT Edge** z menu, a następnie wybierz pozycję **Dodaj urządzenie IoT Edge**.

   ![Dodaj urządzenie IoT Edge z Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na stronie **Tworzenie urządzenia** podaj następujące informacje:

   * Utwórz opisowy identyfikator urządzenia.
   * Wybierz **klucz symetryczny** jako typ uwierzytelniania.
   * Użyj ustawień domyślnych, aby automatycznie generować klucze uwierzytelniania i połączyć nowe urządzenie z centrum.

1. Wybierz pozycję **Zapisz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT Hub

Za pomocą rozszerzeń usługi Azure IoT Visual Studio Code można wykonywać operacje w usłudze IoT Hub. Aby te operacje działały, należy zalogować się do konta platformy Azure i wybrać centrum.

1. W Visual Studio Code Otwórz widok **Eksploratora** .
1. W dolnej części Eksploratora rozwiń sekcję **IoT Hub platformy Azure** .

   ![Rozwiń sekcję usługi Azure IoT Hub Devices](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kliknij pozycję **...** w nagłówku sekcji **IoT Hub platformy Azure** . Jeśli nie widzisz wielokropka, kliknij pozycję lub umieść wskaźnik myszy na nagłówku.
1. Wybierz **pozycję wybierz IoT Hub**.
1. Jeśli nie zalogowano się na koncie platformy Azure, postępuj zgodnie z monitami, aby to zrobić.
1. Wybierz swoją subskrypcję platformy Azure.
1. Wybierz Centrum IoT Hub.

### <a name="register-a-new-device-with-visual-studio-code"></a>Zarejestruj nowe urządzenie w Visual Studio Code

1. W Eksploratorze Visual Studio Code rozwiń sekcję **IoT Hub platformy Azure** .
1. Kliknij pozycję **...** w nagłówku sekcji **IoT Hub platformy Azure** . Jeśli nie widzisz wielokropka, kliknij pozycję lub umieść wskaźnik myszy na nagłówku.
1. Wybierz pozycję **Utwórz urządzenie IoT Edge**.
1. W otwartym polu tekstowym nadaj urządzeniu identyfikator.

Na ekranie dane wyjściowe zostanie wyświetlony wynik polecenia. Informacje o urządzeniu są drukowane wraz z dostarczonym identyfikatorem **deviceId** oraz urządzeniem **ConnectionString** , które służy do łączenia urządzenia fizycznego z Centrum IoT.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) , aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

To polecenie zawiera trzy parametry:

* `--device-id` lub `-d` : Podaj opisową nazwę, która jest unikatowa w ramach Centrum IoT Hub.
* `--hub-name` lub `-n` : Podaj nazwę Centrum IoT.
* `--edge-enabled` lub `--ee` : Zadeklaruj, że urządzenie jest urządzeniem IoT Edge.

   ![AZ IoT Hub Device-Identity Create Output](./media/how-to-register-device/create-edge-device-cli.png)

---

Teraz, gdy masz urządzenie zarejestrowane w IoT Hub, Pobierz parametry połączenia używane do kończenia instalacji i aprowizacji środowiska uruchomieniowego IoT Edge. Wykonaj kroki opisane w dalszej części tego artykułu, aby [wyświetlić zarejestrowane urządzenia i pobrać parametry połączenia](#view-registered-devices-and-retrieve-connection-strings).

## <a name="option-2-register-with-x509-certificates"></a>Opcja 2: rejestrowanie z certyfikatami X. 509

Ręczne inicjowanie obsługi certyfikatów X. 509 wymaga IoT Edge w wersji 1.0.10 lub nowszej.

W przypadku uwierzytelniania za pomocą certyfikatu X. 509 informacje o uwierzytelnianiu poszczególnych urządzeń są podawane w postaci *odcisków palców* pobranych z certyfikatów tożsamości urządzenia. Te odciski palców są nadawane IoT Hub w momencie rejestracji urządzenia, aby usługa mogła rozpoznać urządzenie po nawiązaniu połączenia.

### <a name="create-certificates-and-thumbprints"></a>Tworzenie certyfikatów i odcisków palców

W przypadku aprowizacji urządzenia IoT Edge z certyfikatami X. 509 należy użyć co nosi nazwę *certyfikatu tożsamości urządzenia*. Ten certyfikat jest używany tylko na potrzeby aprowizacji urządzenia IoT Edge i uwierzytelniania urządzenia za pomocą usługi Azure IoT Hub. Jest to certyfikat liścia, który nie podpisuje innych certyfikatów. Certyfikat tożsamości urządzenia jest oddzielony od certyfikatów urzędu certyfikacji (CA), które urządzenie IoT Edge prezentuje do modułów lub urządzeń podrzędnych w celu weryfikacji. Aby uzyskać więcej informacji na temat sposobu używania certyfikatów urzędu certyfikacji w IoT Edge urządzeniach, zobacz [Opis sposobu, w jaki Azure IoT Edge używa certyfikatów](iot-edge-certs.md).

Aby ręcznie zainicjować obsługę administracyjną w programie X. 509, potrzebne są następujące pliki:

* Dwa certyfikaty tożsamości urządzeń z ich zgodnymi certyfikatami klucza prywatnego w formatach cer lub PEM.

  Jeden zestaw plików certyfikatu/klucza jest dostarczany do środowiska uruchomieniowego IoT Edge. Podczas tworzenia certyfikatów tożsamości urządzeń Ustaw nazwę pospolitą certyfikatu (CN) na identyfikator urządzenia, które ma mieć urządzenie w centrum IoT.

* Odciski palców z obu certyfikatów tożsamości urządzeń.

  Wartości odcisków palca to 40 — znaki szesnastkowe dla skrótów SHA-1 i znaków 64-szesnastkowych dla skrótów SHA-256. Oba odciski palców są dostarczane do IoT Hub w momencie rejestracji urządzeń.

Jeśli nie masz dostępnych certyfikatów, możesz [utworzyć certyfikaty demonstracyjne w celu przetestowania funkcji urządzenia IoT Edge](how-to-create-test-certificates.md). Postępuj zgodnie z instrukcjami w tym artykule, aby skonfigurować skrypty tworzenia certyfikatów, utworzyć certyfikat głównego urzędu certyfikacji, a następnie utworzyć dwa IoT Edge Certyfikaty tożsamości urządzeń.

Jednym ze sposobów pobrania odcisku palca z certyfikatu jest następujące polecenie OpenSSL:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Rejestrowanie nowego urządzenia

Aby zarejestrować nowe urządzenie IoT Edge w usłudze IoT Hub i przekazać odciski palców certyfikatów, można użyć kilku narzędzi.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W centrum IoT w Azure Portal IoT Edge urządzenia są tworzone i zarządzane oddzielnie od urządzeń IoT, które nie są włączone.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT Hub.

1. W lewym okienku wybierz pozycję **IoT Edge** z menu, a następnie wybierz pozycję **Dodaj urządzenie IoT Edge**.

   ![Dodaj urządzenie IoT Edge z Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na stronie **Tworzenie urządzenia** podaj następujące informacje:

   * Utwórz opisowy identyfikator urządzenia. Zanotuj ten identyfikator urządzenia, ponieważ będzie on używany w następnej sekcji.
   * Wybierz pozycję **X. 509 z podpisem własnym** jako typ uwierzytelniania.
   * Podaj odciski palców certyfikatu tożsamości podstawowej i pomocniczej. Wartości odcisków palca to 40 — znaki szesnastkowe dla skrótów SHA-1 i znaków 64-szesnastkowych dla skrótów SHA-256.

1. Wybierz pozycję **Zapisz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Obecnie rozszerzenie Azure IoT dla Visual Studio Code nie obsługuje rejestracji urządzeń w certyfikatach X. 509.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [AZ IoT Hub Device-Identity Create](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-create) , aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

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

---

Teraz, gdy masz urządzenie zarejestrowane w usłudze IoT Hub, możesz przystąpić do instalacji i aprowizacji IoT Edge środowiska uruchomieniowego na urządzeniu. IoT Edge urządzeń uwierzytelnianych za pomocą certyfikatów X. 509 nie należy używać parametrów połączenia, dzięki czemu można przejść do następnego kroku:

* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux](how-to-install-iot-edge.md)
* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Wyświetlanie zarejestrowanych urządzeń i pobieranie parametrów połączenia

W przypadku urządzeń korzystających z uwierzytelniania przy użyciu klucza symetrycznego potrzebne są parametry połączenia w celu ukończenia instalacji i aprowizacji środowiska uruchomieniowego IoT Edge.

Urządzenia korzystające z uwierzytelniania certyfikatu X. 509 nie potrzebują parametrów połączenia. Zamiast tego urządzenia te muszą mieć swoją nazwę Centrum IoT, nazwę urządzenia i ich pliki certyfikatów, aby ukończyć instalację i Inicjowanie obsługi środowiska uruchomieniowego IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wszystkie urządzenia z systemem Edge, które łączą się z Centrum IoT, są wyświetlane na stronie **IoT Edge** .

![Użyj Azure Portal, aby wyświetlić wszystkie urządzenia IoT Edge w centrum IoT Hub](./media/how-to-register-device/portal-view-devices.png)

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT.

Urządzenia, które uwierzytelniają się przy użyciu kluczy symetrycznych, mają swoje parametry połączenia dostępne do skopiowania w portalu.

1. Na stronie **IoT Edge** w portalu kliknij identyfikator urządzenia na liście urządzeń IoT Edge.
2. Skopiuj wartość **podstawowych parametrów połączenia** lub **pomocniczych parametrów połączenia**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Wyświetlanie IoT Edge urządzeń z Visual Studio Code

Wszystkie urządzenia, które łączą się z Centrum IoT, są wymienione w sekcji **IoT Hub platformy Azure** w Eksploratorze Visual Studio Code. Urządzenia IoT Edge są odróżniane od urządzeń innych niż brzegowe z inną ikoną, a fakt, że moduły **$edgeAgent** i **$edgeHub** są wdrażane na poszczególnych urządzeniach IoT Edge.

![Używanie VS Code do wyświetlania wszystkich urządzeń IoT Edge w centrum IoT Hub](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Pobierz parametry połączenia z Visual Studio Code

Gdy wszystko jest gotowe do skonfigurowania urządzenia, potrzebne są parametry połączenia łączące urządzenie fizyczne z jego tożsamością w centrum IoT.

1. Kliknij prawym przyciskiem myszy identyfikator urządzenia w sekcji **IoT Hub platformy Azure** .
1. Wybierz pozycję **Kopiuj parametry połączenia urządzenia**.

   Parametry połączenia są kopiowane do Schowka.

Możesz również wybrać pozycję **Pobierz informacje o urządzeniu** w menu rozwijanym prawym przyciskiem myszy, aby wyświetlić wszystkie informacje o urządzeniu, w tym parametry połączenia, w oknie danych wyjściowych.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

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

>[!TIP]
>`connection-string show`Polecenie zostało wprowadzone w wersji 0.9.8 rozszerzenia Azure IoT, zastępując przestarzałe `show-connection-string` polecenie. Jeśli wystąpi błąd uruchamiania tego polecenia, upewnij się, że wersja rozszerzenia została zaktualizowana do 0.9.8 lub nowszego. Aby uzyskać więcej informacji i najnowszych aktualizacji, zobacz [Microsoft Azure IoT Extension dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension).

W wartości `device-id` parametru jest rozróżniana wielkość liter.

Podczas kopiowania parametrów połączenia do użycia na urządzeniu nie należy uwzględniać znaków cudzysłowu otaczających parametry połączenia.

---

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz urządzenie zarejestrowane w usłudze IoT Hub, możesz przystąpić do instalacji i aprowizacji IoT Edge środowiska uruchomieniowego na urządzeniu.

* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux](how-to-install-iot-edge.md)
* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Windows](how-to-install-iot-edge-windows-on-windows.md)