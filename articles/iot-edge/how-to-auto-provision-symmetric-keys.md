---
title: Aprowizowanie urządzenia przy użyciu zaświadczenia klucza symetrycznego — Azure IoT Edge
description: Używanie zaświadczenia klucza symetrycznego do testowania automatycznej aprowacji urządzeń dla Azure IoT Edge przy użyciu usługi Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66e1e561c14b169d41028e151ac054888830b881
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481977"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Tworzenie i aprowizowanie urządzenia IoT Edge za pomocą zaświadczenia klucza symetrycznego

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge mogą być automatycznie aprowowane przy użyciu usługi [Device Provisioning,](../iot-dps/index.yml) podobnie jak urządzenia, które nie są włączone na urządzeniach brzegowych. Jeśli nie masz informacji na temat procesu automatycznego aprowizowania, przed kontynuowaniem zapoznaj się z omówieniem aprowacji. [](../iot-dps/about-iot-dps.md#provisioning-process)

W tym artykule pokazano, jak utworzyć rejestrację indywidualną lub grupową usługi Device Provisioning przy użyciu zaświadczenia klucza symetrycznego na urządzeniu IoT Edge, korzystając z następujących kroków:

* Utwórz wystąpienie usługi IoT Hub Device Provisioning Service (DPS).
* Utwórz rejestrację dla urządzenia.
* Zainstaluj środowisko IoT Edge uruchomieniowe i połącz się z IoT Hub.

Symetryczne zaświadczenia klucza to proste podejście do uwierzytelniania urządzenia za pomocą wystąpienia usługi Device Provisioning Service. Ta metoda zaświadczenia reprezentuje środowisko "Hello world" dla deweloperów, którzy są nowi w aprowiacji urządzeń lub nie mają rygorystycznych wymagań dotyczących zabezpieczeń. Zaświadczenie urządzenia przy użyciu certyfikatów [TPM](../iot-dps/concepts-tpm-attestation.md) lub [X.509](../iot-dps/concepts-x509-attestation.md) jest bezpieczniejsze i powinno być używane w przypadku bardziej rygorystycznych wymagań dotyczących zabezpieczeń.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywny IoT Hub
* Urządzenie fizyczne lub wirtualne

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie usługi IoT Hub Device Provisioning Service platformie Azure i połącz je z centrum IoT. Możesz wykonać instrukcje z [tematu Konfigurowanie IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

Po uruchomieniu usługi Device Provisioning Service skopiuj wartość **Zakres** identyfikatorów ze strony przeglądu. Ta wartość jest wartością używaną podczas konfigurowania IoT Edge uruchomieniowego.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Wybieranie unikatowego identyfikatora rejestracji dla urządzenia

Aby zidentyfikować każde urządzenie, należy zdefiniować unikatowy identyfikator rejestracji. Możesz użyć adresu MAC, numeru seryjnego lub dowolnych unikatowych informacji z urządzenia.

W tym przykładzie używamy kombinacji adresu MAC i numeru seryjnego tworzącej następujący ciąg dla identyfikatora rejestracji: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Utwórz unikatowy identyfikator rejestracji dla urządzenia. Prawidłowe znaki to małe litery alfanumeryczne i kreska ("-").

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji dps

Użyj identyfikatora rejestracji urządzenia, aby utworzyć rejestrację indywidualną w uciece DPS.

Podczas tworzenia rejestracji w uciekierach dps masz możliwość zadeklarowania początkowego stanu bliźniaczej **reprezentacji urządzenia.** W bliźniaczej reprezentacji urządzenia można ustawić tagi, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Te tagi są używane do tworzenia [wdrożeń automatycznych.](how-to-deploy-at-scale.md)

> [!TIP]
> Rejestracje grupowe są również możliwe w przypadku korzystania z zaświadczenia klucza symetrycznego i obejmują te same decyzje co rejestracje indywidualne.

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego wystąpienia IoT Hub Device Provisioning Service.

1. W **obszarze Ustawienia** wybierz pozycję Zarządzaj **rejestracjami.**

1. Wybierz **pozycję Dodaj rejestrację** indywidualną, a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   1. W **przypadku opcji Mechanizm** wybierz pozycję Klucz **symetryczny**.

   1. Zaznacz pole **wyboru Automatycznie generuj** klucze.

   1. Podaj **identyfikator rejestracji** utworzony dla urządzenia.

   1. Jeśli **IoT Hub identyfikator** urządzenia, podaj jego identyfikator. Identyfikatorów urządzeń można używać do kierowania poszczególnych urządzeń do wdrożenia modułu. Jeśli nie poniesiesz identyfikatora urządzenia, zostanie użyty identyfikator rejestracji.

   1. Wybierz **pozycję Prawda,** aby zadeklarować, że rejestracja dotyczy IoT Edge urządzenia. W przypadku rejestracji grupowej wszystkie urządzenia muszą być IoT Edge lub żadne z nich nie może być.

      > [!TIP]
      > W interfejsie wiersza polecenia [](/cli/azure/iot/dps/enrollment) platformy Azure [](/cli/azure/iot/dps/enrollment-group) możesz utworzyć rejestrację lub grupę rejestracji i użyć **flagi** z włączoną obsługą krawędzi, aby określić, że urządzenie lub grupa urządzeń jest urządzeniem IoT Edge brzegowym.

   1. Zaakceptuj wartość domyślną z zasad alokacji usługi Device Provisioning Service dla sposobu przypisywania urządzeń do **centrów** lub wybierz inną wartość specyficzną dla tej rejestracji.

   1. Wybierz połączone **IoT Hub,** z którym chcesz połączyć urządzenie. Możesz wybrać wiele centrów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   1. Wybierz **sposób obsługi danych urządzenia** podczas ponownego aprowizowania, gdy urządzenia żądają aprowizowania po raz pierwszy.

   1. Jeśli chcesz, dodaj wartość tagu do **początkowego stanu bliźniaczej** reprezentacji urządzenia. Tagów można używać do kierowania grup urządzeń do wdrożenia modułu. Na przykład:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Upewnij **się, że pozycja Włącz** jest ustawiona na wartość **Włącz.**

   1. Wybierz pozycję **Zapisz**.

Teraz, gdy dla tego urządzenia istnieje rejestracja, środowisko IoT Edge może automatycznie aprowizuje urządzenie podczas instalacji. Pamiętaj, aby skopiować  wartość klucza podstawowego rejestracji do użycia podczas instalowania środowiska uruchomieniowego programu IoT Edge lub tworzenia kluczy urządzeń do użycia z rejestracją grupową.

## <a name="derive-a-device-key"></a>Wyprowadzaj klucz urządzenia

> [!NOTE]
> Ta sekcja jest wymagana tylko w przypadku korzystania z rejestracji grupowej.

Każde urządzenie używa swojego pochodnego klucza urządzenia z unikatowym identyfikatorem rejestracji w celu przeprowadzenia zaświadczenia klucza symetrycznego podczas rejestracji podczas aprowizowania. Aby wygenerować klucz urządzenia, użyj klucza skopiowanego z rejestracji programu DPS, aby obliczyć [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji dla urządzenia i przekonwertować wynik na format Base64.

Nie uwzględniaj klucza podstawowego lub pomocniczego rejestracji w kodzie urządzenia.

### <a name="linux-workstations"></a>Stacje robocze z systemem Linux

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć narzędzia openssl do wygenerowania pochodnego klucza urządzenia, jak pokazano w poniższym przykładzie.

Zamień wartość **KEY** na **zanotiony** wcześniej klucz podstawowy.

Zastąp wartość **REG_ID** identyfikatorem rejestracji urządzenia.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Stacje robocze z systemem Windows

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell do wygenerowania pochodnego klucza urządzenia, jak pokazano w poniższym przykładzie.

Zamień wartość **KEY** na **zanotiony** wcześniej klucz podstawowy.

Zastąp wartość **REG_ID** identyfikatorem rejestracji urządzenia.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchamiać kod na brzegu sieci.

Wykonaj kroki opisane w [artykule Instalowanie Azure IoT Edge uruchomieniowego,](how-to-install-iot-edge.md)a następnie wróć do tego artykułu, aby aprowizować urządzenie.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia przy użyciu informacji aprowizowania

Po zainstalowaniu środowiska uruchomieniowego na urządzeniu skonfiguruj je przy użyciu informacji, których używa do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

Przygotuj następujące informacje:

* Wartość zakres **identyfikatorów DPS**
* Utworzony **identyfikator rejestracji** urządzenia
* Klucz **podstawowy skopiowany** z rejestracji w uciekierach dps

> [!TIP]
> W przypadku rejestracji grupowych potrzebny jest klucz pochodny każdego [urządzenia,](#derive-a-device-key) a nie klucz podstawowy rejestracji dps.

### <a name="linux-device"></a>Urządzenie z systemem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Otwórz plik konfiguracji na IoT Edge urządzeniu.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracji aprowizowania w pliku . Odkomentuj wiersze dotyczące aprowizowania kluczy symetrycznych usługi DPS i upewnij się, że wszystkie inne wiersze aprowności zostały zakomentowane.

   Wiersz `provisioning:` nie powinien mieć poprzedzającego odstępu, a zagnieżdżone elementy powinny być wcięte przez dwie spacje.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "symmetric_key"
       registration_id: "<REGISTRATION_ID>"
       symmetric_key: "<SYMMETRIC_KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Zaktualizuj wartości `scope_id` , `registration_id` i przy użyciu usługi `symmetric_key` DPS i informacji o urządzeniu.

1. Opcjonalnie możesz użyć wierszy lub , aby skonfigurować zachowanie `always_reprovision_on_startup` `dynamic_reprovisioning` ponownego aprowizowania urządzenia. Jeśli dla urządzenia ustawiono ponowne aprowizowanie przy uruchamianiu, zawsze będzie ono najpierw próbowało aprowizować usługę DPS, a następnie wrócić do tworzenia kopii zapasowej w przypadku niepowodzenia. Jeśli dla urządzenia ustawiono dynamiczne ponowne aprowizowanie, program IoT Edge ponowne uruchomienie i ponowne aprowizowanie w przypadku wykrycia zdarzenia ponownej aprowizowania. Aby uzyskać więcej informacji, [zobacz IoT Hub device reprovisioning concepts (Pojęcia dotyczące ponownego aprowizowania urządzeń).](../iot-dps/concepts-device-reprovision.md)

1. Uruchom ponownie IoT Edge uruchomieniu, aby przejmować wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Utwórz plik konfiguracji dla urządzenia na podstawie pliku szablonu dostarczonego w ramach IoT Edge instalacji.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Otwórz plik konfiguracji na IoT Edge urządzeniu.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Znajdź **sekcję Provisioning** (Aprowizowanie) w pliku . Odkomentuj wiersze aprowowania usługi DPS przy użyciu klucza symetrycznego i upewnij się, że wszystkie inne wiersze aprowności są komentarzami.

   ```toml
   # DPS provisioning with symmetric key
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "symmetric_key"
   registration_id = "<REGISTRATION_ID>"

   symmetric_key = "<PRIMARY_KEY OR DERIVED_KEY>"
   ```

1. Zaktualizuj wartości `id_scope` , `registration_id` i przy użyciu usługi `symmetric_key` DPS i informacji o urządzeniu.

   Parametr klucza symetrycznego może akceptować wartość klucza wbudowanego, URI pliku lub URI PKCS#11. Odkomentuj tylko jedną symetryczną linię klucza na podstawie formatu, którego używasz.

   Jeśli używasz jakichkolwiek interfejsów URIS PKCS#11, znajdź sekcję **PKCS#11** w pliku konfiguracji i podaj informacje o konfiguracji PKCS#11.

1. Zapisz i zamknij plik config.toml.

1. Zastosuj zmiany konfiguracji wprowadzone do IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Urządzenie z systemem Windows

1. Otwórz okno programu PowerShell w trybie administratora. Pamiętaj, aby użyć sesji AMD64 programu PowerShell podczas instalowania programu IoT Edge, a nie programu PowerShell (x86).

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko IoT Edge uruchomieniowe na maszynie. Domyślnie polecenie to ręczne inicjowanie obsługi administracyjnej przy użyciu kontenerów systemu Windows, dlatego użyj flagi , aby użyć automatycznej aprowiwizowania z `-DpsSymmetricKey` uwierzytelnianiem za pomocą klucza symetrycznego.

   Zastąp wartości symboli `{scope_id}` zastępczych `{registration_id}` dla , i `{symmetric_key}` danymi zebranymi wcześniej.

   Dodaj parametr `-ContainerOs Linux` , jeśli używasz kontenerów systemu Linux w systemie Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Weryfikowanie pomyślnej instalacji

Jeśli środowisko uruchomieniowe zostało uruchomione pomyślnie, możesz przejść do swojego IoT Hub i rozpocząć wdrażanie IoT Edge modułów na urządzeniu. Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i uruchomione.

### <a name="linux-device"></a>Urządzenie z systemem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Sprawdź stan usługi IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdź dzienniki usług.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Lista uruchomionych modułów.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Sprawdź stan usługi IoT Edge.

```cmd/sh
sudo iotedge system status
```

Sprawdź dzienniki usług.

```cmd/sh
sudo iotedge system logs
```

Lista uruchomionych modułów.

```cmd/sh
sudo iotedge list
```

:::moniker-end

### <a name="windows-device"></a>Urządzenie z systemem Windows

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdź dzienniki usług.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Lista uruchomionych modułów.

```powershell
iotedge list
```

Możesz sprawdzić, czy została użyta rejestracja indywidualna utworzona w usłudze Device Provisioning Service. Przejdź do wystąpienia usługi Device Provisioning Service w Azure Portal. Otwórz szczegóły rejestracji indywidualnej utworzonej przez Ciebie. Zwróć uwagę, że stan rejestracji jest **przypisany i** jest na liście identyfikator urządzenia.

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi Device Provisioning Service umożliwia ustawienie identyfikatora urządzenia i tagów bliźniaczej reprezentacji urządzenia w tym samym czasie, gdy aprowizuje się nowe urządzenie. Możesz użyć tych wartości, aby kierować je do poszczególnych urządzeń lub grup urządzeń przy użyciu automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na](how-to-deploy-at-scale.md) dużą skalę przy użyciu interfejsu Azure Portal lub [interfejsu wiersza polecenia platformy Azure.](how-to-deploy-cli-at-scale.md)
