---
title: Zarządzanie certyfikatami urządzeń — Azure IoT Edge | Microsoft Docs
description: Utwórz certyfikaty testowe, zainstaluj je i zarządzaj nimi na urządzeniu Azure IoT Edge, aby przygotować się do wdrożenia produkcyjnego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6a4cade6a740bffc33695c40663609df38ba6e7a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044905"
---
# <a name="manage-certificates-on-an-iot-edge-device"></a>Zarządzanie certyfikatami na urządzeniu IoT Edge

Wszystkie urządzenia IoT Edge używają certyfikatów do tworzenia bezpiecznych połączeń między środowiskiem uruchomieniowym a dowolnymi modułami uruchomionymi na urządzeniu. IoT Edge urządzenia działające jako bramy używają tych samych certyfikatów do łączenia się z urządzeniami podrzędnymi.

## <a name="install-production-certificates"></a>Instalowanie certyfikatów produkcyjnych

Podczas pierwszej instalacji IoT Edge i aprowizacji urządzenia urządzenie zostanie skonfigurowane z certyfikatami tymczasowymi, aby umożliwić przetestowanie usługi.
Te certyfikaty tymczasowe wygasną przez 90 dni lub można je zresetować przez ponowne uruchomienie komputera.
Gdy przejdziesz do scenariusza produkcyjnego lub chcesz utworzyć urządzenie bramy, musisz podać własne certyfikaty.
W tym artykule przedstawiono procedurę instalowania certyfikatów na IoT Edge urządzeniach.

Aby dowiedzieć się więcej o różnych typach certyfikatów i ich rolach, zobacz [Opis sposobu używania certyfikatów przez Azure IoT Edge](iot-edge-certs.md).

>[!NOTE]
>Termin "główny urząd certyfikacji" używany w tym artykule odnosi się do certyfikatu publicznego urzędu łańcucha certyfikatów dla rozwiązania IoT. Nie trzeba używać certyfikatu głównego urzędu certyfikacji z certyfikatem lub administratorem urzędu certyfikacji w organizacji. W wielu przypadkach jest to pośredni certyfikat publiczny urzędu certyfikacji.

### <a name="prerequisites"></a>Wymagania wstępne

* Urządzenie IoT Edge.

  Jeśli nie masz skonfigurowanego urządzenia IoT Edge, możesz je utworzyć na maszynie wirtualnej platformy Azure. Wykonaj kroki opisane w jednym z artykułów z przewodnikiem Szybki Start, aby [utworzyć wirtualne urządzenie](quickstart-linux.md) z systemem Linux lub [utworzyć wirtualne urządzenie systemu Windows](quickstart.md).

* Posiadanie certyfikatu głównego urzędu certyfikacji (CA) z podpisem własnym lub zakupionego od zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, VeriSign, DigiCert lub GlobalSign.

  Jeśli nie masz jeszcze głównego urzędu certyfikacji, ale chcesz wypróbować IoT Edge funkcje wymagające certyfikatów produkcyjnych (na przykład scenariusze bramy), możesz [utworzyć certyfikaty demonstracyjne do testowania funkcji IoT Edge urządzeń](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Tworzenie certyfikatów produkcyjnych

Aby utworzyć następujące pliki, należy użyć własnego urzędu certyfikacji:

* Główny urząd certyfikacji
* Certyfikat urzędu certyfikacji urządzenia
* Klucz prywatny urzędu certyfikacji urządzenia

Ten artykuł zawiera informacje o tym, jako że *główny urząd certyfikacji* nie jest najwyższym urzędem certyfikatu dla organizacji. Jest to najwyższy urząd certyfikacji dla scenariusza IoT Edge, do którego moduł IoT Edge Hub, moduły użytkownika i wszystkie urządzenia podrzędne używają do ustanawiania relacji zaufania między sobą.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

:::moniker-end

Aby zapoznać się z przykładem tych certyfikatów, przejrzyj skrypty, które tworzą certyfikaty demonstracyjne w [zarządzaniu testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalowanie certyfikatów na urządzeniu

Zainstaluj łańcuch certyfikatów na urządzeniu IoT Edge i skonfiguruj środowisko uruchomieniowe IoT Edge, aby odwoływać się do nowych certyfikatów.

Skopiuj trzy pliki certyfikatów i kluczy na urządzenie IoT Edge. Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](../key-vault/index.yml) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .  Jeśli certyfikaty zostały wygenerowane na urządzeniu IoT Edge, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

Na przykład jeśli użyto przykładowych skryptów do [tworzenia certyfikatów demonstracyjnych](how-to-create-test-certificates.md), Skopiuj następujące pliki na urządzenie IoT-Edge:

* Certyfikat urzędu certyfikacji urządzenia: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Klucz prywatny urzędu certyfikacji urządzenia: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Główny urząd certyfikacji: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Otwórz plik konfiguracji demona Security IoT Edge.

   * Windows: `C:\ProgramData\iotedge\config.yaml`
   * Linux: `/etc/iotedge/config.yaml`

1. Ustaw właściwości **certyfikatu** w pliku config. YAML na ścieżkę identyfikatora URI, aby uzyskać certyfikat i pliki kluczy na urządzeniu IoT Edge. Usuń `#` znak przed właściwościami certyfikatu, aby usunąć komentarz z czterech wierszy. Upewnij się, że w wierszu **Certyfikaty:** nie ma powyższego odstępu, a elementy zagnieżdżone są wcięte o dwie spacje. Na przykład:

   * W systemie Windows:

      ```yaml
      certificates:
        device_ca_cert: "file:///C:/<path>/<device CA cert>"
        device_ca_pk: "file:///C:/<path>/<device CA key>"
        trusted_ca_certs: "file:///C:/<path>/<root CA cert>"
      ```

   * W systemie Linux:

      ```yaml
      certificates:
        device_ca_cert: "file:///<path>/<device CA cert>"
        device_ca_pk: "file:///<path>/<device CA key>"
        trusted_ca_certs: "file:///<path>/<root CA cert>"
      ```

1. Na urządzeniach z systemem Linux upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu w katalogu zawierającym certyfikaty.

1. Jeśli przed uruchomieniem lub ponownym IoT Edge uruchomieniem usługi IoT Edge na urządzeniu zostały wcześniej użyte inne certyfikaty, usuń je z następujących dwóch katalogów:

   * Windows: `C:\ProgramData\iotedge\hsm\certs` i `C:\ProgramData\iotedge\hsm\cert_keys`

   * Linux: `/var/lib/iotedge/hsm/certs` i `/var/lib/iotedge/hsm/cert_keys`
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Otwórz plik konfiguracji demona zabezpieczeń IoT Edge: `/etc/aziot/config.toml`

1. Znajdź `trust_bundle_cert` parametr na początku pliku. Usuń komentarz z tego wiersza i podaj identyfikator URI pliku dla certyfikatu głównego urzędu certyfikacji na urządzeniu.

   ```toml
   trust_bundle_cert = "file:///<path>/<root CA cert>"
   ```

1. Znajdź `[edge_ca]` sekcję w pliku config. toml. Usuń znaczniki komentarza z wierszy w tej sekcji i podaj ścieżki URI pliku dla certyfikatów i plików kluczy na urządzeniu IoT Edge.

   ```toml
   [edge_ca]
   cert = "file:///<path>/<device CA cert>"
   pk = "file:///<path>/<device CA key>"
   ```

1. Upewnij się, że użytkownik **iotedge** ma uprawnienia do odczytu w katalogu zawierającym certyfikaty.

1. Jeśli przed uruchomieniem lub ponownym IoT Edge uruchomieniem usługi IoT Edge na urządzeniu zostały wcześniej użyte inne certyfikaty, usuń je z następujących dwóch katalogów:

   * `/var/lib/aziot/certd/certs`
   * `/var/lib/aziot/keyd/keys`

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1. -->
<!-- Temporarily, customizable certificate lifetime not available in 1.2. Update before GA. -->
:::moniker range="iotedge-2018-06"

## <a name="customize-certificate-lifetime"></a>Dostosuj okres istnienia certyfikatu

IoT Edge automatycznie generuje certyfikaty na urządzeniu w kilka przypadków, w tym:

* Jeśli nie podano własnych certyfikatów produkcyjnych podczas instalowania i inicjowania obsługi administracyjnej IoT Edge, program IoT Edge Security Manager automatycznie wygeneruje **certyfikat urzędu certyfikacji urządzenia**. Ten certyfikat z podpisem własnym jest przeznaczony wyłącznie do celów deweloperskich i testowych, a nie do produkcji. Ten certyfikat wygasa po 90 dniach.
* IoT Edge Security Manager generuje również **certyfikat urzędu certyfikacji obciążenia** podpisany przez certyfikat urzędu certyfikacji urządzenia

Aby uzyskać więcej informacji na temat funkcji różnych certyfikatów na urządzeniu IoT Edge, zobacz [Opis sposobu Azure IoT Edge korzystania z certyfikatów](iot-edge-certs.md).

Dla tych dwóch automatycznie generowanych certyfikatów istnieje możliwość ustawienia flagi **auto_generated_ca_lifetime_days** w pliku konfiguracji w celu skonfigurowania liczby dni okresu istnienia certyfikatów.

>[!NOTE]
>Istnieje trzeci certyfikat wygenerowany automatycznie, który zostanie utworzony przez program IoT Edge Security Manager, **certyfikat serwera centrum IoT Edge**. Ten certyfikat zawsze ma 90 dzień okresu istnienia, ale jest automatycznie odnawiany przed wygaśnięciem. Wartość **auto_generated_ca_lifetime_days** nie ma wpływu na ten certyfikat.

Po upływie określonej liczby dni IoT Edge musi zostać ponownie uruchomiona w celu ponownego wygenerowania certyfikatu urzędu certyfikacji urządzenia. Certyfikat urzędu certyfikacji urządzenia nie zostanie odnowiony automatycznie.

1. Aby skonfigurować wygaśnięcie certyfikatu do wartości innej niż domyślna 90 dni, należy dodać wartość w dniach do sekcji **Certyfikaty** w pliku konfiguracji.

   ```yaml
   certificates:
     device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
     device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
     trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
     auto_generated_ca_lifetime_days: <value>
   ```

   > [!NOTE]
   > Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

1. Usuń zawartość `hsm` folderu, aby usunąć wszystkie wcześniej wygenerowane certyfikaty.

   Windows: `C:\ProgramData\iotedge\hsm\certs` i `C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs` i `/var/lib/iotedge/hsm/cert_keys`

1. Uruchom ponownie usługę IoT Edge.

   W systemie Windows:

   ```powershell
   Restart-Service iotedge
   ```

   W systemie Linux:

   ```bash
   sudo systemctl restart iotedge
   ```

1. Potwierdź ustawienie okresu istnienia.

   W systemie Windows:

   ```powershell
   iotedge check --verbose
   ```

   W systemie Linux:

   ```bash
   sudo iotedge check --verbose
   ```

   Sprawdź dane wyjściowe **gotowości produkcyjnej: sprawdzanie certyfikatów** , które wyświetla liczbę dni, po upływie których automatycznie Wygenerowano certyfikaty urzędu certyfikacji.

:::moniker-end
<!-- end 1.1 -->

<!-- 
<!-- 1.2 --
:::moniker range=">=iotedge-2020-11"

1. To configure the certificate expiration to something other than the default 90 days, add the value in days to the **certificates** section of the config file.

   ```toml
   [certificates]
   device_ca_cert = "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
   device_ca_pk = "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
   trusted_ca_certs = "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
   auto_generated_ca_lifetime_days = <value>
   ```

1. Delete the contents of the `certd` and `keyd` folders to remove any previously generated certificates: `/var/lib/aziot/certd/certs` `/var/lib/aziot/keyd/keys`

1. Restart IoT Edge.

   ```bash
   sudo iotedge system restart
   ```

1. Confirm the new lifetime setting.

   ```bash
   sudo iotedge check --verbose
   ```

   Check the output of the **production readiness: certificates** check, which lists the number of days until the automatically generated device CA certificates expire.
:::moniker-end
<!-- end 1.2 --
-->

## <a name="next-steps"></a>Następne kroki

Zainstalowanie certyfikatów na urządzeniu IoT Edge jest czynnością wymaganą przed wdrożeniem rozwiązania w środowisku produkcyjnym. Dowiedz się więcej o [tym, jak przygotować się do wdrożenia rozwiązania IoT Edge w środowisku produkcyjnym](production-checklist.md).
