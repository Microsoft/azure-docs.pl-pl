---
title: Rejestrowanie nowego urządzenia — Azure IoT Edge | Microsoft Docs
description: Rejestrowanie pojedynczego urządzenia IoT Edge na platformie IoT Hub ręcznej aprowiwizowania przy użyciu kluczy symetrycznych lub certyfikatów X.509
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: kgremban
ms.openlocfilehash: b5d761cfa947b3fd4e5f718e603219c650e8dd72
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481875"
---
# <a name="register-an-iot-edge-device-in-iot-hub"></a>Rejestrowanie urządzenia IoT Edge w IoT Hub

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Ten artykuł zawiera instrukcje rejestrowania nowego urządzenia IoT Edge w IoT Hub.

Każde urządzenie, które łączy się z centrum IoT Hub, ma identyfikator urządzenia używany do śledzenia komunikacji między chmurą a urządzeniem lub urządzeniem do chmury. Urządzenie można skonfigurować przy użyciu informacji o połączeniu, w tym nazwy hosta centrum IoT, identyfikatora urządzenia i informacji, których urządzenie używa do uwierzytelniania IoT Hub.

Kroki opisane w tym artykule są opisane w procesie o nazwie aprowizowanie ręczne, w którym łączysz jedno urządzenie z jego centrum IoT. W przypadku aprowizowania ręcznego dostępne są dwie opcje uwierzytelniania IoT Edge urządzeń:

* **Klucz symetryczny:** podczas tworzenia nowej tożsamości urządzenia w usłudze IoT Hub usługa tworzy dwa klucze. Umieszczasz jeden z kluczy na urządzeniu i przedstawia on klucz do IoT Hub podczas uwierzytelniania.

  Ta metoda uwierzytelniania jest szybsza, aby rozpocząć pracę, ale nie jest tak bezpieczna.

* **X.509 z** podpisem własnym: tworzysz dwa certyfikaty tożsamości X.509 i umieszczasz je na urządzeniu. Podczas tworzenia nowej tożsamości urządzenia w programie IoT Hub odciski palca z obu certyfikatów. Gdy urządzenie uwierzytelnia się w IoT Hub, przedstawia jeden certyfikat i IoT Hub sprawdza, czy certyfikat pasuje do jego odcisku palca.

  Ta metoda uwierzytelniania jest bezpieczniejsza i zalecana w scenariuszach produkcyjnych.

W tym artykule o mowa w obu metodach uwierzytelniania.

Jeśli masz wiele urządzeń do skonfigurowania i nie chcesz ręcznie aprowizować każdego z nich, skorzystaj z jednego z następujących artykułów, aby dowiedzieć się, jak IoT Edge współpracuje z IoT Hub Device Provisioning Service:

* [Tworzenie i aprowiz IoT Edge urządzeń przy użyciu certyfikatów X.509](how-to-auto-provision-x509-certs.md)
* [Tworzenie i aprowiz IoT Edge urządzeń z modułem TPM](how-to-auto-provision-simulated-device-linux.md)
* [Tworzenie i aprowiz IoT Edge urządzeń przy użyciu kluczy symetrycznych](how-to-auto-provision-symmetric-keys.md)

## <a name="prerequisites"></a>Wymagania wstępne

# <a name="portal"></a>[Portal](#tab/azure-portal)

Bezpłatne lub standardowe centrum [IoT w](../iot-hub/iot-hub-create-through-portal.md) ramach subskrypcji platformy Azure.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* Bezpłatne lub standardowe centrum [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) w ramach subskrypcji platformy Azure
* [Visual Studio Code](https://code.visualstudio.com/)
* [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) dla Visual Studio Code

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

* Bezpłatne lub standardowe [centrum IoT Hub](../iot-hub/iot-hub-create-using-cli.md) w ramach subskrypcji platformy Azure.
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w Twoim środowisku. Interfejs wiersza polecenia platformy Azure musi mieć co najmniej wersję 2.0.70 lub nowszą. Użyj polecenia `az --version` w celu przeprowadzenia weryfikacji. Ta wersja obsługuje polecenia rozszerzenia az i wprowadza platformę poleceń Knack.

---

## <a name="option-1-register-with-symmetric-keys"></a>Opcja 1. Rejestrowanie przy użyciu kluczy symetrycznych

Możesz użyć kilku narzędzi, aby zarejestrować nowe urządzenie IoT Edge w programie IoT Hub i pobrać jego parametrów połączenia, w zależności od preferencji.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W centrum IoT hub w Azure Portal urządzenia IoT Edge są tworzone i zarządzane oddzielnie od urządzeń IoT, które nie są włączone na urządzeniach brzegowych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do centrum IoT.

1. W okienku po lewej stronie wybierz **IoT Edge** menu, a następnie wybierz pozycję **Dodaj IoT Edge urządzenie.**

   ![Dodawanie IoT Edge urządzenia z Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na **stronie Tworzenie urządzenia** podaj następujące informacje:

   * Utwórz opisowy identyfikator urządzenia.
   * Wybierz **pozycję Klucz symetryczny** jako typ uwierzytelniania.
   * Użyj ustawień domyślnych, aby automatycznie wygenerować klucze uwierzytelniania i połączyć nowe urządzenie z centrum.

1. Wybierz pozycję **Zapisz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="sign-in-to-access-your-iot-hub"></a>Zaloguj się, aby uzyskać dostęp do centrum IoT

Rozszerzenia usługi Azure IoT mogą być Visual Studio Code do wykonywania operacji z centrum IoT. Aby te operacje działały, musisz zalogować się do konta platformy Azure i wybrać centrum.

1. W Visual Studio Code otwórz widok **Eksploratora.**
1. W dolnej części eksploratora rozwiń **sekcję Azure IoT Hub** eksploratora.

   ![Rozwiń Azure IoT Hub Urządzenia](./media/how-to-register-device/azure-iot-hub-devices.png)

1. Kliknij pozycję **... w** **nagłówku Azure IoT Hub** sekcji. Jeśli nie widzisz wielokropka, kliknij nagłówek lub umieść na nim wskaźnik myszy.
1. Wybierz **pozycję Wybierz IoT Hub**.
1. Jeśli nie zalogowano się do konta platformy Azure, postępuj zgodnie z monitami, aby to zrobić.
1. Wybierz swoją subskrypcję platformy Azure.
1. Wybierz centrum IoT Hub.

### <a name="register-a-new-device-with-visual-studio-code"></a>Rejestrowanie nowego urządzenia w u Visual Studio Code

1. W eksploratorze Visual Studio Code rozwiń **sekcję Azure IoT Hub.**
1. Kliknij pozycję **... w** **nagłówku Azure IoT Hub** sekcji. Jeśli nie widzisz wielokropka, kliknij nagłówek lub umieść na nim wskaźnik myszy.
1. Wybierz **pozycję IoT Edge urządzenie.**
1. W polu tekstowym, które zostanie otwarte, nadaj urządzeniu identyfikator.

Na ekranie danych wyjściowych zostanie wyświetlony wynik polecenia . Informacje o urządzeniu są drukowane, w tym podany przez Ciebie **deviceId** i **connectionString,** których można użyć do połączenia urządzenia fizycznego z centrum IoT.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [az iot hub device-identity create,](/cli/azure/iot/hub/device-identity) aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

To polecenie zawiera trzy parametry:

* `--device-id` lub `-d` : podaj opisową nazwę, która jest unikatowa w obrębie centrum IoT.
* `--hub-name` lub `-n` : podaj nazwę centrum IoT Hub.
* `--edge-enabled` lub `--ee` : zadeklaruj, że urządzenie jest IoT Edge urządzeniem.

   ![az iot hub device-identity create output](./media/how-to-register-device/create-edge-device-cli.png)

---

Teraz, gdy urządzenie jest zarejestrowane w IoT Hub, pobierz ciągi połączenia, których używasz do ukończenia instalacji i aprowizowania środowiska IoT Edge uruchomieniowego. Wykonaj kroki opisane w dalszej części tego artykułu, aby [wyświetlić zarejestrowane urządzenia i pobrać parametry połączenia.](#view-registered-devices-and-retrieve-connection-strings)

## <a name="option-2-register-with-x509-certificates"></a>Opcja 2. Rejestrowanie za pomocą certyfikatów X.509

Ręczne aprowizowanie za pomocą certyfikatów X.509 wymaga IoT Edge wersji 1.0.10 lub nowszej.

W przypadku uwierzytelniania certyfikatu X.509 informacje o uwierzytelnianiu  poszczególnych urządzeń są udostępniane w postaci odcisków palca z certyfikatów tożsamości urządzenia. Te odciski palca są IoT Hub podczas rejestracji urządzenia, dzięki czemu usługa może rozpoznać urządzenie podczas jego połączenia.

### <a name="create-certificates-and-thumbprints"></a>Tworzenie certyfikatów i odcisków palca

Podczas aprowiz IoT Edge urządzenia z certyfikatami X.509 używa się tak zwanego certyfikatu *tożsamości urządzenia.* Ten certyfikat służy tylko do aprowizowania urządzenia IoT Edge i uwierzytelniania urządzenia za pomocą Azure IoT Hub. Jest to certyfikat liścia, który nie podpisuje innych certyfikatów. Certyfikat tożsamości urządzenia jest oddzielony od certyfikatów urzędu certyfikacji (CA), które urządzenie IoT Edge prezentuje modułom lub urządzeniom nadrzędnym w celu weryfikacji. Aby uzyskać więcej informacji na temat sposobu, w jaki certyfikaty urzędu certyfikacji są używane na urządzeniach IoT Edge, zobacz [Understand how Azure IoT Edge uses certificates](iot-edge-certs.md)(Opis sposobu Azure IoT Edge certyfikatów).

Do ręcznego aprowizowania za pomocą X.509 potrzebne są następujące pliki:

* Dwa certyfikaty tożsamości urządzeń z pasującymi certyfikatami kluczy prywatnych w formatach cer lub pem.

  Jeden zestaw plików certyfikatów/kluczy jest dostarczany do IoT Edge uruchomieniowego. Podczas tworzenia certyfikatów tożsamości urządzenia ustaw nazwę pospolitą certyfikatu (CN) na identyfikator urządzenia, który ma mieć urządzenie w centrum IoT.

* Odciski palca wykonane z obu certyfikatów tożsamości urządzenia.

  Wartości odcisku palca to 40-hex znaków dla skrótów SHA-1 lub 64-hex dla skrótów SHA-256. Oba odciski palca są udostępniane IoT Hub podczas rejestracji urządzenia.

Jeśli nie masz dostępnych certyfikatów, możesz utworzyć certyfikaty demonstracyjne, aby przetestować IoT Edge [urządzenia.](how-to-create-test-certificates.md) Postępuj zgodnie z instrukcjami w tym artykule, aby skonfigurować skrypty tworzenia certyfikatów, utworzyć certyfikat głównego urzędu certyfikacji, a następnie utworzyć dwa certyfikaty IoT Edge tożsamości urządzenia.

Jednym ze sposobów pobrania odcisku palca z certyfikatu jest następujące polecenie openssl:

```cmd
openssl x509 -in <certificate filename>.pem -text -fingerprint
```

### <a name="register-a-new-device"></a>Rejestrowanie nowego urządzenia

Możesz użyć kilku narzędzi, aby zarejestrować nowe urządzenie IoT Edge w programie IoT Hub i przekazać jego odciski palca certyfikatu.

# <a name="portal"></a>[Portal](#tab/azure-portal)

W centrum IoT w Azure Portal urządzenia IoT Edge są tworzone i zarządzane oddzielnie od urządzeń IoT, które nie są włączone na urządzeniach brzegowych.

1. Zaloguj się do [centrum Azure Portal](https://portal.azure.com) i przejdź do centrum IoT.

1. W okienku po lewej **stronie wybierz IoT Edge** z menu, a następnie wybierz pozycję Dodaj IoT Edge **urządzenie.**

   ![Dodawanie IoT Edge z Azure Portal](./media/how-to-register-device/portal-add-iot-edge-device.png)

1. Na **stronie Tworzenie urządzenia** podaj następujące informacje:

   * Utwórz opisowy identyfikator urządzenia. Zanotuj ten identyfikator urządzenia, ponieważ użyjesz go w następnej sekcji.
   * Wybierz **typ uwierzytelniania X.509 z** podpisem własnym.
   * Podaj odciski palca certyfikatu tożsamości podstawowej i pomocniczej. Wartości odcisku palca to 40-hex znaków skrótów SHA-1 lub 64-hex dla skrótów SHA-256.

1. Wybierz pozycję **Zapisz**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

Obecnie rozszerzenie usługi Azure IoT dla Visual Studio Code nie obsługuje rejestracji urządzeń przy użyciu certyfikatów X.509.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj polecenia [az iot hub device-identity create,](/cli/azure/iot/hub/device-identity) aby utworzyć nową tożsamość urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled --auth-method x509_thumbprint --primary-thumbprint [SHA thumbprint] --secondary-thumbprint [SHA thumbprint]
   ```

To polecenie zawiera kilka parametrów:

* `--device-id` lub `-d` : podaj opisową nazwę, która jest unikatowa dla Centrum IoT. Zanotuj ten identyfikator urządzenia, ponieważ użyjesz go w następnej sekcji.
* `hub-name` lub `-n` : podaj nazwę centrum IoT Hub.
* `--edge-enabled` lub `--ee` : Zadeklaruj, że urządzenie jest IoT Edge urządzeniem.
* `--auth-method` lub `--am` : zadeklaruj typ autoryzacji, z których urządzenie będzie korzystać. W tym przypadku używamy odcisków palca certyfikatu X.509.
* `--primary-thumbprint` lub `--ptp` : podaj odcisk palca certyfikatu X.509 do użycia jako klucz podstawowy.
* `--secondary-thumbprint` lub `--stp` : podaj odcisk palca certyfikatu X.509 do użycia jako klucz pomocniczy.

---

Teraz, gdy urządzenie jest zarejestrowane w IoT Hub, możesz zainstalować i aprowizować środowisko uruchomieniowe IoT Edge na urządzeniu. IoT Edge, które uwierzytelniają się za pomocą certyfikatów X.509, nie używają ciągów połączenia, więc możesz przejść do następnego kroku:

* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux](how-to-install-iot-edge.md)
* [Instalowanie lub odinstalowywanie Azure IoT Edge systemu Windows](how-to-install-iot-edge-windows-on-windows.md)

## <a name="view-registered-devices-and-retrieve-connection-strings"></a>Wyświetlanie zarejestrowanych urządzeń i pobieranie ciągów połączenia

Urządzenia, które korzystają z uwierzytelniania za pomocą klucza symetrycznego, muszą mieć parametry połączenia, aby ukończyć instalację i aprowizowanie IoT Edge uruchomieniowego.

Urządzenia, które korzystają z uwierzytelniania certyfikatu X.509, nie potrzebują ciągów połączenia. Zamiast tego te urządzenia potrzebują nazwy centrum IoT Hub, nazwy urządzenia i plików certyfikatów, aby ukończyć instalację i aprowizowanie IoT Edge uruchomieniowego.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wszystkie urządzenia z obsługą brzegową, które łączą się z centrum IoT Hub, są wyświetlane na **IoT Edge** internetowej.

![Użyj Azure Portal, aby wyświetlić wszystkie IoT Edge urządzeń w centrum IoT](./media/how-to-register-device/portal-view-devices.png)

Gdy wszystko będzie gotowe do skonfigurowania urządzenia, potrzebujesz parametrów połączenia, które będą łączyły urządzenie fizyczne z jego tożsamością w centrum IoT.

Urządzenia uwierzytelniane za pomocą kluczy symetrycznych mają parametry połączenia dostępne do skopiowania w portalu.

1. Na **IoT Edge** portalu kliknij identyfikator urządzenia z listy urządzeń IoT Edge urządzenia.
2. Skopiuj wartość podstawowych **parametrów połączenia lub** pomocniczych parametrów **połączenia**.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

### <a name="view-iot-edge-devices-with-visual-studio-code"></a>Wyświetlanie IoT Edge urządzeń z Visual Studio Code

Wszystkie urządzenia, które łączą się z centrum IoT Hub, są wymienione w **sekcji** Azure IoT Hub w oknie Visual Studio Code Explorer. IoT Edge urządzenia są odróżnialne od urządzeń innych niż urządzenia brzegowe za pomocą innej ikony oraz faktu, że moduły **$edgeAgent** **i $edgeHub** są wdrażane na każdym urządzeniu IoT Edge brzegowym.

![Użyj VS Code, aby wyświetlić wszystkie IoT Edge urządzeń w centrum IoT](./media/how-to-register-device/view-devices.png)

### <a name="retrieve-the-connection-string-with-visual-studio-code"></a>Pobieranie parametrów połączenia za pomocą Visual Studio Code

Gdy wszystko będzie gotowe do skonfigurowania urządzenia, potrzebujesz parametrów połączenia, które będą łączyły urządzenie fizyczne z jego tożsamością w centrum IoT.

1. Kliknij prawym przyciskiem myszy identyfikator urządzenia w sekcji **Azure IoT Hub** aplikacji.
1. Wybierz **pozycję Kopiuj ciąg połączenia urządzenia.**

   Te ciągi połączenia są kopiowane do schowka.

Możesz również wybrać pozycję **Pobierz informacje o urządzeniu** z menu dostępnego po kliknięciu prawym przyciskiem myszy, aby wyświetlić wszystkie informacje o urządzeniu, w tym ciąg połączenia, w oknie danych wyjściowych.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

### <a name="view-iot-edge-devices-with-the-azure-cli"></a>Wyświetlanie IoT Edge przy użyciu interfejsu wiersza polecenia platformy Azure

Użyj polecenia [az iot hub device-identity list,](/cli/azure/iot/hub/device-identity) aby wyświetlić wszystkie urządzenia w centrum IoT. Na przykład:

   ```azurecli
   az iot hub device-identity list --hub-name [hub name]
   ```

Każde urządzenie zarejestrowane jako urządzenie IoT Edge będzie mieć właściwość **capabilities.iotEdge** ustawioną na **wartość true.**

### <a name="retrieve-the-connection-string-with-the-azure-cli"></a>Pobieranie parametrów połączenia za pomocą interfejsu wiersza polecenia platformy Azure

Gdy wszystko będzie gotowe do skonfigurowania urządzenia, potrzebujesz parametrów połączenia, które będą łączyły urządzenie fizyczne z jego tożsamością w centrum IoT. Użyj polecenia [az iot hub device-identity connection-string show,](/cli/azure/iot/hub/device-identity/connection-string) aby zwrócić ciąg połączenia dla pojedynczego urządzenia:

   ```azurecli
   az iot hub device-identity connection-string show --device-id [device id] --hub-name [hub name]
   ```

>[!TIP]
>Polecenie zostało wprowadzone w wersji `connection-string show` 0.9.8 rozszerzenia usługi Azure IoT, zastępując przestarzałe `show-connection-string` polecenie. Jeśli wystąpi błąd podczas uruchamiania tego polecenia, upewnij się, że wersja rozszerzenia została zaktualizowana do wersji 0.9.8 lub nowszej. Aby uzyskać więcej informacji i najnowsze aktualizacje, zobacz [Microsoft Azure IoT extension for Azure CLI](https://github.com/Azure/azure-iot-cli-extension)(Rozszerzenie IoT dla interfejsu wiersza polecenia platformy Azure).

W wartości `device-id` parametru jest zróżnicowana wielkość liter.

Podczas kopiowania parametrów połączenia do użycia na urządzeniu nie należy uwzględniać znaków cudzysłowu wokół parametrów połączenia.

---

## <a name="next-steps"></a>Następne kroki

Teraz, gdy urządzenie jest zarejestrowane w IoT Hub, możesz zainstalować i aprowizować środowisko uruchomieniowe IoT Edge na urządzeniu.

* [Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux](how-to-install-iot-edge.md)
* [Instalowanie lub odinstalowywanie Azure IoT Edge systemu Windows](how-to-install-iot-edge-windows-on-windows.md)