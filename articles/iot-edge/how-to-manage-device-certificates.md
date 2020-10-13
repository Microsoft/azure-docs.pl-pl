---
title: Zarządzanie certyfikatami urządzeń — Azure IoT Edge | Microsoft Docs
description: Utwórz certyfikaty testowe, zainstaluj je i zarządzaj nimi na urządzeniu Azure IoT Edge, aby przygotować się do wdrożenia produkcyjnego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9e3925d2c14d51785ed4fe00a508ea353490e1cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669032"
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

* Urządzenie IoT Edge uruchomione w [systemie Windows](how-to-install-iot-edge-windows.md) lub [Linux](how-to-install-iot-edge-linux.md).
* Posiadanie certyfikatu głównego urzędu certyfikacji (CA) z podpisem własnym lub zakupionego od zaufanego komercyjnego urzędu certyfikacji, takiego jak Baltimore, VeriSign, DigiCert lub GlobalSign.

Jeśli nie masz jeszcze głównego urzędu certyfikacji, ale chcesz wypróbować IoT Edge funkcje wymagające certyfikatów produkcyjnych (na przykład scenariusze bramy), możesz [utworzyć certyfikaty demonstracyjne do testowania funkcji IoT Edge urządzeń](how-to-create-test-certificates.md).

### <a name="create-production-certificates"></a>Tworzenie certyfikatów produkcyjnych

Aby utworzyć następujące pliki, należy użyć własnego urzędu certyfikacji:

* Główny urząd certyfikacji
* Certyfikat urzędu certyfikacji urządzenia
* Klucz prywatny urzędu certyfikacji urządzenia

Ten artykuł zawiera informacje o tym, jako że *główny urząd certyfikacji* nie jest najwyższym urzędem certyfikatu dla organizacji. Jest to najwyższy urząd certyfikacji dla scenariusza IoT Edge, do którego moduł IoT Edge Hub, moduły użytkownika i wszystkie urządzenia podrzędne używają do ustanawiania relacji zaufania między sobą.

> [!NOTE]
> Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

Aby zapoznać się z przykładem tych certyfikatów, przejrzyj skrypty, które tworzą certyfikaty demonstracyjne w [zarządzaniu testowymi certyfikatami urzędu certyfikacji dla przykładów i samouczków](https://github.com/Azure/iotedge/tree/master/tools/CACertificates).

### <a name="install-certificates-on-the-device"></a>Instalowanie certyfikatów na urządzeniu

Zainstaluj łańcuch certyfikatów na urządzeniu IoT Edge i skonfiguruj środowisko uruchomieniowe IoT Edge, aby odwoływać się do nowych certyfikatów.

Na przykład jeśli użyto przykładowych skryptów do [tworzenia certyfikatów demonstracyjnych](how-to-create-test-certificates.md), Skopiuj następujące pliki na urządzenie IoT-Edge:

* Certyfikat urzędu certyfikacji urządzenia: `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
* Klucz prywatny urzędu certyfikacji urządzenia: `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`
* Główny urząd certyfikacji: `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

1. Skopiuj trzy pliki certyfikatów i kluczy na urządzenie IoT Edge.

   Do przenoszenia plików certyfikatów można użyć usługi, takiej jak [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) lub funkcja, taka jak [Secure Copy Protocol](https://www.ssh.com/ssh/scp/) .  Jeśli certyfikaty zostały wygenerowane na urządzeniu IoT Edge, możesz pominąć ten krok i użyć ścieżki do katalogu roboczego.

1. Otwórz plik konfiguracji demona Security IoT Edge.

   * Systemy `C:\ProgramData\iotedge\config.yaml`
   * System `/etc/iotedge/config.yaml`

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

## <a name="customize-certificate-lifetime"></a>Dostosuj okres istnienia certyfikatu

IoT Edge automatycznie generuje certyfikaty na urządzeniu w kilka przypadków, w tym:

* Jeśli nie podano własnych certyfikatów produkcyjnych podczas instalowania i inicjowania obsługi administracyjnej IoT Edge, program IoT Edge Security Manager automatycznie wygeneruje **certyfikat urzędu certyfikacji urządzenia**. Ten certyfikat z podpisem własnym jest przeznaczony wyłącznie do celów deweloperskich i testowych, a nie do produkcji. Ten certyfikat wygasa po 90 dniach.
* IoT Edge Security Manager generuje również **certyfikat urzędu certyfikacji obciążenia** podpisany przez certyfikat urzędu certyfikacji urządzenia

Aby uzyskać więcej informacji na temat funkcji różnych certyfikatów na urządzeniu IoT Edge, zobacz [Opis sposobu Azure IoT Edge korzystania z certyfikatów](iot-edge-certs.md).

Dla tych dwóch automatycznie generowanych certyfikatów istnieje możliwość ustawienia flagi **auto_generated_ca_lifetime_days** w pliku config. YAML w celu skonfigurowania liczby dni okresu istnienia certyfikatów.

>[!NOTE]
>Istnieje trzeci certyfikat wygenerowany automatycznie, który zostanie utworzony przez program IoT Edge Security Manager, **certyfikat serwera centrum IoT Edge**. Ten certyfikat zawsze ma 90 dzień okresu istnienia, ale jest automatycznie odnawiany przed wygaśnięciem. Wartość **auto_generated_ca_lifetime_days** nie ma wpływu na ten certyfikat.

Aby skonfigurować wygaśnięcie certyfikatu do wartości innej niż domyślna 90 dni, Dodaj wartość w dniach do sekcji **Certyfikaty** w pliku **config. YAML** .

Po upływie określonej liczby dni należy ponownie uruchomić demona zabezpieczeń IoT Edge, aby ponownie wygenerować certyfikat urzędu certyfikacji urządzenia, nie zostanie on automatycznie odnowiony.

```yaml
certificates:
  device_ca_cert: "<ADD URI TO DEVICE CA CERTIFICATE HERE>"
  device_ca_pk: "<ADD URI TO DEVICE CA PRIVATE KEY HERE>"
  trusted_ca_certs: "<ADD URI TO TRUSTED CA CERTIFICATES HERE>"
  auto_generated_ca_lifetime_days: <value>
```

> [!NOTE]
> Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

Po określeniu wartości w pliku config. YAML wykonaj następujące czynności:

1. Usuń zawartość `hsm` folderu.

   System Windows: `C:\ProgramData\iotedge\hsm\certs and C:\ProgramData\iotedge\hsm\cert_keys` Linux: `/var/lib/iotedge/hsm/certs and /var/lib/iotedge/hsm/cert_keys`

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

## <a name="next-steps"></a>Następne kroki

Zainstalowanie certyfikatów na urządzeniu IoT Edge jest czynnością wymaganą przed wdrożeniem rozwiązania w środowisku produkcyjnym. Dowiedz się więcej o [tym, jak przygotować się do wdrożenia rozwiązania IoT Edge w środowisku produkcyjnym](production-checklist.md).
