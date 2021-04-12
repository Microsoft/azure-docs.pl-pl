---
title: Udostępnianie urządzenia przy użyciu zaświadczania klucza symetrycznego — Azure IoT Edge
description: Korzystanie z zaświadczania klucza symetrycznego do testowania automatycznej aprowizacji urządzeń dla Azure IoT Edge przy użyciu usługi Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: eb5cbc2f2db0ba9f92a637c7e9a905d2f746880a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200836"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu zaświadczania klucza symetrycznego

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Urządzenia Azure IoT Edge mogą być obsługiwane przy użyciu [usługi Device Provisioning](../iot-dps/index.yml) , podobnie jak dla urządzeń, które nie są włączone. Jeśli nie znasz procesu inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z omówieniem [aprowizacji](../iot-dps/about-iot-dps.md#provisioning-process) .

W tym artykule opisano sposób tworzenia rejestracji indywidualnej lub grupowej usługi Device Provisioning przy użyciu zaświadczania klucza symetrycznego na urządzeniu IoT Edge, wykonując następujące czynności:

* Utwórz wystąpienie IoT Hub Device Provisioning Service (DPS).
* Utwórz rejestrację dla urządzenia.
* Zainstaluj środowisko uruchomieniowe IoT Edge i Połącz się z IoT Hub.

Zaświadczenie klucza symetrycznego to proste podejście do uwierzytelniania urządzenia za pomocą wystąpienia usługi Device Provisioning. Ta metoda zaświadczania reprezentuje środowisko "Hello World" dla deweloperów, którzy są nowym sposobem aprowizacji urządzeń lub nie mają rygorystycznych wymagań dotyczących zabezpieczeń. Zaświadczenie urządzenia przy użyciu [modułu TPM](../iot-dps/concepts-tpm-attestation.md) lub [certyfikatu X. 509](../iot-dps/concepts-x509-attestation.md) jest bezpieczniejsze i powinno być używane do bardziej rygorystycznych wymagań w zakresie bezpieczeństwa.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywna IoT Hub
* Urządzenie fizyczne lub wirtualne

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Skonfiguruj IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie IoT Hub Device Provisioning Service na platformie Azure i połącz je z Centrum IoT. Można postępować zgodnie z instrukcjami podanymi w temacie [konfigurowanie IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po uruchomieniu usługi Device Provisioning należy skopiować wartość **zakres identyfikatora** ze strony przegląd. Ta wartość jest używana podczas konfigurowania środowiska uruchomieniowego IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Wybierz unikatowy identyfikator rejestracji dla urządzenia

Unikatowy identyfikator rejestracji musi być zdefiniowany, aby można było zidentyfikować każde urządzenie. Możesz użyć adresu MAC, numeru seryjnego lub wszelkich unikatowych informacji z urządzenia.

W tym przykładzie używamy kombinacji adresu MAC i numeru seryjnego, tworząc następujący ciąg dla identyfikatora rejestracji: `sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6` .

Utwórz unikatowy identyfikator rejestracji dla urządzenia. Prawidłowe znaki to małe litery alfanumeryczne i myślnik ("-").

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji w usłudze DPS

Użyj identyfikatora rejestracji urządzenia, aby utworzyć rejestrację indywidualną w usłudze DPS.

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W ramach sznurka urządzenia można ustawić Tagi do grupowania urządzeń według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-at-scale.md).

> [!TIP]
> Rejestracje grup są również możliwe podczas korzystania z zaświadczania klucza symetrycznego i obejmują te same decyzje co rejestracje indywidualne.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

1. W obszarze **Ustawienia** wybierz pozycję **Zarządzaj rejestracjami**.

1. Wybierz pozycję **Dodaj rejestrację indywidualną** , a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   1. W obszarze **mechanizm** wybierz opcję **klucz symetryczny**.

   1. Zaznacz pole wyboru **automatycznie Generuj klucze** .

   1. Podaj **Identyfikator rejestracji** , który został utworzony dla Twojego urządzenia.

   1. Podaj **Identyfikator urządzenia IoT Hub** , jeśli chcesz. Identyfikatory urządzeń umożliwiają kierowanie poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podano identyfikatora urządzenia, zostanie użyty Identyfikator rejestracji.

   1. Wybierz **wartość true** , aby zadeklarować, że rejestracja dotyczy urządzenia IoT Edge. W przypadku rejestracji grupy wszystkie urządzenia muszą być IoT Edge urządzeń lub żadna z nich nie może być.

      > [!TIP]
      > W interfejsie wiersza polecenia platformy Azure można utworzyć [rejestrację](/cli/azure/ext/azure-iot/iot/dps/enrollment) lub [grupę rejestracji](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , a następnie użyć flagi z **włączoną krawędzią** , aby określić, że urządzenie lub grupa urządzeń jest urządzeniem IoT Edge.

   1. Zaakceptuj wartość domyślną z zasad alokacji usługi Device Provisioning, aby określić **sposób przypisywania urządzeń do centrów** lub wybrać inną wartość specyficzną dla tej rejestracji.

   1. Wybierz połączone **IoT Hub** , do których chcesz połączyć urządzenie. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji.

   1. Wybierz **, w jaki sposób dane urządzenia mają być obsługiwane na potrzeby ponownej aprowizacji,** gdy urządzenia zażądają aprowizacji po raz pierwszy.

   1. Jeśli chcesz, Dodaj wartość tagu do **początkowego stanu dwuosiowego urządzenia** . Możesz użyć tagów do grup docelowych urządzeń do wdrożenia modułu. Na przykład:

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

   1. Upewnij się, że **wpis Enable** ma wartość **enable**.

   1. Wybierz pozycję **Zapisz**.

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji. Skopiuj wartość **klucza podstawowego** rejestracji, która ma być używana podczas instalowania środowiska uruchomieniowego IoT Edge, lub jeśli chcesz utworzyć klucze urządzeń do użycia z rejestracją grupy.

## <a name="derive-a-device-key"></a>Utwórz klucz urządzenia

> [!NOTE]
> Ta sekcja jest wymagana tylko w przypadku korzystania z rejestracji grupowej.

Każde urządzenie używa pochodnego klucza urządzenia z unikatowym IDENTYFIKATORem rejestracji w celu przeprowadzenia zaświadczania klucza symetrycznego z rejestracją podczas aprowizacji. Aby wygenerować klucz urządzenia, użyj klucza skopiowanego z rejestracji usługi DPS w celu obliczenia [algorytmu HMAC-SHA256](https://wikipedia.org/wiki/HMAC) unikatowego identyfikatora rejestracji dla urządzenia i Przekształć wynik w formacie base64.

Nie uwzględniaj klucza podstawowego lub pomocniczego rejestracji w kodzie urządzenia.

### <a name="linux-workstations"></a>Stacje robocze systemu Linux

Jeśli używasz stacji roboczej z systemem Linux, możesz użyć OpenSSL, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

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

Jeśli używasz stacji roboczej z systemem Windows, możesz użyć programu PowerShell, aby wygenerować pochodny klucz urządzenia, jak pokazano w poniższym przykładzie.

Zastąp wartość **klucza kluczem** **podstawowym** zanotowanym wcześniej.

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

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrożenie dodatkowych kontenerów na urządzeniu, aby można było uruchomić kod na krawędzi.

Wykonaj kroki opisane w artykule [Instalowanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md), a następnie wróć do tego artykułu, aby udostępnić urządzenie.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia przy użyciu informacji o aprowizacji

Po zainstalowaniu na urządzeniu środowiska uruchomieniowego skonfiguruj je za pomocą informacji, które są używane do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

Przygotuj następujące informacje:

* Wartość **zakresu identyfikatora** DPS
* Utworzony **Identyfikator rejestracji** urządzenia
* **Klucz podstawowy** skopiowany z rejestracji usługi DPS

> [!TIP]
> W przypadku rejestracji grup należy dysponować [kluczem pochodnym](#derive-a-device-key) każdego urządzenia, a nie kluczem podstawowym rejestracji usługi DPS.

### <a name="linux-device"></a>Urządzenie z systemem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
1. Otwórz plik konfiguracji na urządzeniu IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracje aprowizacji pliku. Usuń znaczniki komentarza z wierszy usługi DPS symetrycznej aprowizacji kluczy i upewnij się, że wszystkie inne wiersze aprowizacji są oznaczone jako komentarze.

   `provisioning:`Wiersz nie powinien zawierać poprzedzających odstępów, a zagnieżdżone elementy powinny mieć dwie spacje.

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

1. Zaktualizuj wartości `scope_id` , `registration_id` i `symmetric_key` wraz z informacjami o usłudze DPS i urządzeniu.

1. Opcjonalnie możesz użyć `always_reprovision_on_startup` linii lub, `dynamic_reprovisioning` Aby skonfigurować zachowanie ponownego inicjowania obsługi administracyjnej urządzenia. Jeśli urządzenie jest ustawione na ponowne Inicjowanie obsługi administracyjnej, będzie zawsze próbowało najpierw zainicjować obsługę administracyjną przy użyciu punktu dystrybucji, a następnie wrócić do tworzenia kopii zapasowej, jeśli to się nie powiedzie. Jeśli urządzenie jest ustawione na dynamiczną ponowną obsługę administracyjną, IoT Edge zostanie ponownie uruchomione i Zainicjuj obsługę administracyjną w przypadku wykrycia zdarzenia ponownego aprowizacji. Aby uzyskać więcej informacji, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](../iot-dps/concepts-device-reprovision.md).

1. Uruchom ponownie środowisko uruchomieniowe IoT Edge, aby wyszukać wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Utwórz plik konfiguracji dla urządzenia na podstawie pliku szablonu, który jest dostarczany jako część instalacji IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Otwórz plik konfiguracji na urządzeniu IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Znajdź sekcję **aprowizacji** pliku. Usuń znaczniki komentarza z wierszy dla aprowizacji usługi DPS przy użyciu klucza symetrycznego i upewnij się, że wszystkie inne wiersze aprowizacji są oznaczone jako komentarze.

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

1. Zaktualizuj wartości `id_scope` , `registration_id` i `symmetric_key` wraz z informacjami o usłudze DPS i urządzeniu.

   Parametr klucza symetrycznego może przyjmować wartość klucza wbudowanego, identyfikatora URI pliku lub identyfikatora URI PKCS # 11. Usuń komentarz tylko z jednej linii symetrycznej, w oparciu o używany format.

   Jeśli używasz dowolnego identyfikatora URI PKCS # 11, Znajdź sekcję **PKCS # 11** w pliku konfiguracji i podaj informacje o konfiguracji PKCS # 11.

1. Zapisz i zamknij plik config. toml.

1. Zastosuj zmiany konfiguracji wprowadzone w IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Urządzenie z systemem Windows

1. Otwórz okno programu PowerShell w trybie administratora. Należy pamiętać, aby podczas instalowania IoT Edge używać sesji AMD64 programu PowerShell, a nie programu PowerShell (x86).

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows, więc Użyj `-DpsSymmetricKey` flagi, aby użyć automatycznej aprowizacji z uwierzytelnianiem przy użyciu klucza symetrycznego.

   Zastąp wartości symboli zastępczych dla `{scope_id}` , `{registration_id}` i `{symmetric_key}` z danymi, które zostały zebrane wcześniej.

   Dodaj `-ContainerOs Linux` parametr, jeśli używasz kontenerów systemu Linux w systemie Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsSymmetricKey -ScopeId {scope ID} -RegistrationId {registration ID} -SymmetricKey {symmetric key}
   ```

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

Jeśli środowisko uruchomieniowe zostało pomyślnie uruchomione, możesz przejść do IoT Hub i rozpocząć wdrażanie modułów IoT Edge na urządzeniu. Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zostało prawidłowo zainstalowane i uruchomione.

### <a name="linux-device"></a>Urządzenie z systemem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Sprawdź stan usługi IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdzanie dzienników usług.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Wyświetl listę uruchomionych modułów.

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

Sprawdzanie dzienników usług.

```cmd/sh
sudo iotedge system logs
```

Wyświetl listę uruchomionych modułów.

```cmd/sh
sudo iotedge list
```

:::moniker-end

### <a name="windows-device"></a>Urządzenie z systemem Windows

Sprawdź stan usługi IoT Edge.

```powershell
Get-Service iotedge
```

Sprawdzanie dzienników usług.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Wyświetl listę uruchomionych modułów.

```powershell
iotedge list
```

Możesz sprawdzić, czy została użyta Rejestracja indywidualna utworzona w usłudze Device Provisioning. Przejdź do wystąpienia usługi Device Provisioning w Azure Portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Zwróć uwagę, że jest **przypisany** stan rejestracji i identyfikator urządzenia jest wyświetlany.

## <a name="next-steps"></a>Następne kroki

Proces rejestracji w usłudze Device Provisioning Service umożliwia ustawienie tagów urządzeń i urządzeń w tym samym czasie, w których jest inicjowane nowe urządzenie. Tych wartości można użyć do kierowania poszczególnych urządzeń lub grup urządzeń przy użyciu funkcji automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-at-scale.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-cli-at-scale.md).
