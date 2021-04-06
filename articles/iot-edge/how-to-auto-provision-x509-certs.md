---
title: Automatyczne Inicjowanie obsługi administracyjnej urządzeń za pomocą usługi DPS przy użyciu certyfikatów X. 509 — Azure IoT Edge | Microsoft Docs
description: Używanie certyfikatów X. 509 do testowania automatycznej aprowizacji urządzeń dla Azure IoT Edge przy użyciu usługi Device Provisioning
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 44ea6546eb2099165071fd493ec8f890820c0688
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103199836"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu certyfikatów X. 509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Za pomocą [usługi Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)można automatycznie zainicjować obsługę administracyjną urządzeń IoT Edge za pomocą certyfikatów X. 509. Jeśli nie znasz procesu inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z omówieniem [aprowizacji](../iot-dps/about-iot-dps.md#provisioning-process) .

W tym artykule pokazano, jak utworzyć rejestrację usługi Device Provisioning za pomocą certyfikatów X. 509 na urządzeniu IoT Edge, wykonując następujące czynności:

* Generowanie certyfikatów i kluczy.
* Utwórz rejestrację indywidualną dla urządzenia lub rejestrację grupy dla zestawu urządzeń.
* Zainstaluj środowisko uruchomieniowe IoT Edge i Zarejestruj urządzenie przy użyciu IoT Hub.

Używanie certyfikatów X. 509 jako mechanizmu zaświadczania jest doskonałym sposobem na skalowanie produkcji i uproszczenie aprowizacji urządzeń. Zazwyczaj certyfikaty X. 509 są uporządkowane w łańcuchu certyfikatów zaufania. Począwszy od certyfikatu z podpisem własnym lub z zaufanym certyfikatem głównym, każdy certyfikat w łańcuchu podpisuje następny niższy certyfikat. Ten wzorzec tworzy delegowany łańcuch zaufania z certyfikatu głównego w dół przez każdy certyfikat pośredni do końcowego certyfikatu "liścia" zainstalowanego na urządzeniu.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywna IoT Hub.
* Urządzenie fizyczne lub wirtualne będące urządzeniem IoT Edge.
* Najnowsza wersja usługi [git](https://git-scm.com/download/) jest zainstalowana.
* Wystąpienie IoT Hub Device Provisioning Service na platformie Azure połączone z Centrum IoT Hub.
  * Jeśli nie masz wystąpienia usługi Device Provisioning, postępuj zgodnie z instrukcjami podanymi w temacie [konfigurowanie IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).
  * Po uruchomieniu usługi Device Provisioning należy skopiować wartość **zakres identyfikatora** ze strony przegląd. Ta wartość jest używana podczas konfigurowania środowiska uruchomieniowego IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generuj Certyfikaty tożsamości urządzeń

Certyfikat tożsamości urządzenia to certyfikat liścia, który nawiązuje połączenie za pomocą łańcucha certyfikatów zaufania z certyfikatem głównego urzędu certyfikacji X. 509. Certyfikat tożsamości urządzenia musi mieć ustawioną nazwę pospolitą (CN) odpowiadającą IDENTYFIKATORowi urządzenia, które ma mieć urządzenie w centrum IoT.

Certyfikaty tożsamości urządzeń są używane tylko do aprowizacji urządzenia IoT Edge i uwierzytelniania urządzenia za pomocą usługi Azure IoT Hub. Nie podpisywania certyfikatów, w przeciwieństwie do certyfikatów urzędu certyfikacji, które urządzenie IoT Edge prezentuje na moduły lub urządzenia liściowe w celu weryfikacji. Aby uzyskać więcej informacji, zobacz [Azure IoT Edge szczegóły użycia certyfikatu](iot-edge-certs.md).

Po utworzeniu certyfikatu tożsamości urządzenia powinny znajdować się dwa pliki: plik cer lub PEM zawierający publiczną część certyfikatu oraz plik. cer lub PEM z kluczem prywatnym certyfikatu. Jeśli planujesz korzystanie z rejestracji grupowej w usłudze DPS, potrzebujesz także publicznej części certyfikatu pośredniego lub głównego urzędu certyfikacji w tym samym łańcuchu certyfikatów zaufania.

Aby skonfigurować automatyczną Inicjowanie obsługi przy użyciu pliku X. 509, potrzebne są następujące pliki:

* Certyfikat tożsamości urządzenia i jego certyfikat klucza prywatnego. Certyfikat tożsamości urządzenia zostanie przekazany do funkcji DPS w przypadku utworzenia rejestracji indywidualnej. Klucz prywatny jest przesyłany do środowiska uruchomieniowego IoT Edge.
* Pełny certyfikat łańcucha, który powinien mieć co najmniej tożsamość urządzenia i certyfikaty pośrednie. Pełny certyfikat łańcucha jest przesyłany do środowiska uruchomieniowego IoT Edge.
* Certyfikat pośredniego lub głównego urzędu certyfikacji z łańcucha certyfikatów zaufania. Ten certyfikat jest przekazywany do programu DPS w przypadku utworzenia rejestracji grupy.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Obecnie ograniczenie w libiothsm uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Użyj certyfikatów testowych (opcjonalnie)

Jeśli nie masz dostępnego urzędu certyfikacji, aby utworzyć nowe certyfikaty tożsamości i chcesz wypróbować ten scenariusz, Azure IoT Edge repozytorium git zawiera skrypty, których można użyć do generowania certyfikatów testowych. Te certyfikaty są przeznaczone wyłącznie do testowania deweloperskiego i nie mogą być używane w środowisku produkcyjnym.

Aby utworzyć certyfikaty testowe, wykonaj kroki opisane w temacie [Tworzenie certyfikatów demonstracyjnych w celu przetestowania IoT Edge funkcji urządzeń](how-to-create-test-certificates.md). Wykonaj dwie wymagane sekcje, aby skonfigurować skrypty generowania certyfikatów i utworzyć certyfikat głównego urzędu certyfikacji. Następnie postępuj zgodnie z instrukcjami, aby utworzyć certyfikat tożsamości urządzenia. Po zakończeniu należy mieć następujący łańcuch certyfikatów i parę kluczy:

W systemie Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

W systemie Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Te certyfikaty są wymagane na urządzeniu IoT Edge. Jeśli zamierzasz użyć rejestracji indywidualnej w usłudze DPS, przekażesz plik. CERT. pem. W przypadku korzystania z funkcji rejestracji grupowej w usłudze DPS należy również uzyskać certyfikat pośredniego lub głównego urzędu certyfikacji w ramach tego samego łańcucha certyfikatów, który ma zostać przekazany. Jeśli używasz certyfikatów demonstracyjnych, użyj `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` certyfikatu do rejestracji grupowej.

## <a name="create-a-dps-individual-enrollment"></a>Tworzenie rejestracji indywidualnej w usłudze DPS

Użyj wygenerowanych certyfikatów i kluczy, aby utworzyć rejestrację indywidualną w usłudze DPS dla jednego IoT Edge urządzenia. Indywidualne rejestracje pobierają publiczną część certyfikatu tożsamości urządzenia i pasują do certyfikatu na urządzeniu.

Jeśli chcesz zainicjować obsługę wielu urządzeń IoT Edge, wykonaj kroki opisane w następnej sekcji, aby [utworzyć rejestrację grupy w usłudze DPS](#create-a-dps-group-enrollment).

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W ramach sznurka urządzenia można ustawić Tagi do grupowania urządzeń według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-at-scale.md).

Aby uzyskać więcej informacji na temat rejestracji w usłudze Device Provisioning, zobacz [jak zarządzać rejestracjami urządzeń](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > W interfejsie wiersza polecenia platformy Azure można utworzyć [rejestrację](/cli/azure/ext/azure-iot/iot/dps/enrollment) lub [grupę rejestracji](/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , a następnie użyć flagi z **włączoną krawędzią** , aby określić, że urządzenie lub grupa urządzeń jest urządzeniem IoT Edge.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

1. W obszarze **Ustawienia** wybierz pozycję **Zarządzaj rejestracjami**.

1. Wybierz pozycję **Dodaj rejestrację indywidualną** , a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   * **Mechanizm**: Wybierz **X. 509**.

   * **Plik PEM lub CER certyfikatu podstawowego**: Przekaż plik publiczny z certyfikatu tożsamości urządzenia. Jeśli użyto skryptów do wygenerowania certyfikatu testowego, wybierz następujący plik:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **Identyfikator urządzenia IoT Hub**: podaj identyfikator urządzenia, jeśli chcesz. Identyfikatory urządzeń umożliwiają kierowanie poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podano identyfikatora urządzenia, używana jest nazwa pospolita (CN) w certyfikacie X. 509.

   * **IoT Edge urządzenie**: Wybierz **wartość true** , aby zadeklarować, że rejestracja dotyczy urządzenia IoT Edge.

   * **Wybierz centra IoT, do których można przypisać to urządzenie**: Wybierz połączone centrum IoT Hub, z którym chcesz połączyć urządzenie. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji.

   * **Początkowy stan dwuosiowy urządzenia**: Dodaj wartość tagu, która ma zostać dodana do sznurka urządzenia, jeśli chcesz. Można używać tagów do grup docelowych urządzeń na potrzeby automatycznego wdrażania. Na przykład:

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

1. Wybierz pozycję **Zapisz**.

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji. Przejdź do sekcji [instalowanie IoT Edge środowiska uruchomieniowego](#install-the-iot-edge-runtime) , aby skonfigurować urządzenie IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Utwórz rejestrację grupy DPS

Użyj wygenerowanych certyfikatów i kluczy, aby utworzyć rejestrację grupy w usłudze DPS dla wielu IoT Edge urządzeń. Rejestracje grup używają pośredniego lub głównego certyfikatu urzędu certyfikacji z łańcucha certyfikatów zaufania używanego do generowania certyfikatów tożsamości poszczególnych urządzeń.

Jeśli chcesz zamiast tego zainicjować obsługę jednego urządzenia IoT Edge, wykonaj kroki opisane w poprzedniej sekcji, aby [utworzyć rejestrację indywidualną](#create-a-dps-individual-enrollment)w usłudze DPS.

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W ramach sznurka urządzenia można ustawić Tagi do grupowania urządzeń według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-at-scale.md).

### <a name="verify-your-root-certificate"></a>Weryfikowanie certyfikatu głównego

Podczas tworzenia grupy rejestracji można skorzystać z zweryfikowanego certyfikatu. Certyfikat można sprawdzić za pomocą programu DPS, wskazując, że masz własność certyfikatu głównego. Aby uzyskać więcej informacji, zobacz [Jak przeprowadzić potwierdzenie posiadania dla certyfikatów urzędu certyfikacji X. 509](../iot-dps/how-to-verify-certificates.md).

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

1. Z menu po lewej stronie wybierz pozycję **Certyfikaty** .

1. Wybierz pozycję **Dodaj** , aby dodać nowy certyfikat.

1. Wprowadź przyjazną nazwę certyfikatu, a następnie przejdź do pliku cer lub PEM, który reprezentuje publiczną część certyfikatu X. 509.

   Jeśli używasz certyfikatów demonstracyjnych, Przekaż `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certyfikat.

1. Wybierz pozycję **Zapisz**.

1. Certyfikat powinien teraz znajdować się na liście na stronie **Certyfikaty** . Wybierz go, aby otworzyć Szczegóły certyfikatu.

1. Wybierz pozycję **Generuj kod weryfikacyjny** , a następnie skopiuj wygenerowany kod.

1. Niezależnie od tego, czy masz własny certyfikat urzędu certyfikacji, czy korzystasz z certyfikatów demonstracyjnych, możesz użyć narzędzia weryfikacyjnego dostępnego w repozytorium IoT Edge, aby sprawdzić dowód posiadania. Narzędzie weryfikacji używa certyfikatu urzędu certyfikacji w celu podpisania nowego certyfikatu, który ma podany kod weryfikacyjny jako nazwę podmiotu.

   * W systemie Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * W systemie Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na tej samej stronie Szczegóły certyfikatu w Azure Portal Przekaż nowo wygenerowany certyfikat weryfikacyjny.

1. Wybierz pozycję **Weryfikuj**.

### <a name="create-enrollment-group"></a>Utwórz grupę rejestracji

Aby uzyskać więcej informacji na temat rejestracji w usłudze Device Provisioning, zobacz [jak zarządzać rejestracjami urządzeń](../iot-dps/how-to-manage-enrollments.md).

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

1. W obszarze **Ustawienia** wybierz pozycję **Zarządzaj rejestracjami**.

1. Wybierz pozycję **Dodaj grupę rejestracji** , a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:

   * **Nazwa grupy**: Podaj nazwę do zapamiętania dla tej rejestracji grupy.

   * **Typ zaświadczania**: wybierz pozycję **certyfikat**.

   * **IoT Edge urządzenie**: Wybierz **wartość PRAWDA**. W przypadku rejestracji grupy wszystkie urządzenia muszą być IoT Edge urządzeń lub żadna z nich nie może być.

   * **Typ certyfikatu**: wybierz opcję **certyfikat urzędu certyfikacji** , jeśli certyfikat urzędu certyfikacji jest przechowywany przy użyciu usługi DPS lub **certyfikatu pośredniego** , jeśli chcesz przekazać nowy plik tylko dla tej rejestracji.

   * **Certyfikat podstawowy**: w przypadku wybrania w ostatniej sekcji certyfikatu urzędu certyfikacji wybierz certyfikat z listy rozwijanej. W przypadku wybrania certyfikatu pośredniego należy przekazać plik publiczny z certyfikatu urzędu certyfikacji w łańcuchu certyfikatów zaufania, który został użyty do wygenerowania certyfikatów tożsamości urządzeń.

   * **Wybierz centra IoT, do których można przypisać to urządzenie**: Wybierz połączone centrum IoT Hub, z którym chcesz połączyć urządzenie. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji.

   * **Początkowy stan dwuosiowy urządzenia**: Dodaj wartość tagu, która ma zostać dodana do sznurka urządzenia, jeśli chcesz. Można używać tagów do grup docelowych urządzeń na potrzeby automatycznego wdrażania. Na przykład:

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

1. Wybierz pozycję **Zapisz**.

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji. Przejdź do następnej sekcji, aby skonfigurować urządzenie IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrożenie dodatkowych kontenerów na urządzeniu, aby można było uruchomić kod na krawędzi.

Wykonaj kroki opisane w artykule [Instalowanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md), a następnie wróć do tego artykułu, aby udostępnić urządzenie.

Obsługa administracyjna X. 509 z usługą DPS jest obsługiwana tylko w IoT Edge w wersji 1.0.9 lub nowszej.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia przy użyciu informacji o aprowizacji

Po zainstalowaniu na urządzeniu środowiska uruchomieniowego skonfiguruj je za pomocą informacji, które są używane do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

Przygotuj następujące informacje:

* Wartość **identyfikatora** DPS. Tę wartość można pobrać ze strony Przegląd wystąpienia usługi DPS w Azure Portal.
* Plik łańcucha certyfikatu tożsamości urządzenia na urządzeniu.
* Plik klucza tożsamości urządzenia na urządzeniu.
* Opcjonalny identyfikator rejestracji. Jeśli nie zostanie podany, identyfikator zostanie pobrany z nazwy pospolitej w certyfikacie tożsamości urządzenia.

### <a name="linux-device"></a>Urządzenie z systemem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Otwórz plik konfiguracji na urządzeniu IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracje aprowizacji pliku. Usuń znaczniki komentarza z wierszy dla udostępniania certyfikatu X. 509 usługi DPS i upewnij się, że wszystkie inne wiersze aprowizacji zostały oznaczone jako komentarze.

   `provisioning:`Wiersz nie powinien zawierać poprzedzających odstępów, a zagnieżdżone elementy powinny mieć dwie spacje.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "x509"
   #   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Zaktualizuj wartości `scope_id` , `identity_cert` i `identity_pk` wraz z informacjami o usłudze DPS i urządzeniu.

   Po dodaniu informacji o certyfikacie X. 509 i kluczu do pliku config. YAML ścieżki powinny być podawane jako identyfikatory URI plików. Na przykład:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Opcjonalnie podaj `registration_id` dla urządzenia. W przeciwnym razie pozostaw ten wiersz komentarzem, aby zarejestrować urządzenie przy użyciu nazwy POSPOLITej certyfikatu tożsamości.

1. Opcjonalnie możesz użyć `always_reprovision_on_startup` linii lub, `dynamic_reprovisioning` Aby skonfigurować zachowanie ponownego inicjowania obsługi administracyjnej urządzenia. Jeśli urządzenie jest ustawione na ponowne Inicjowanie obsługi administracyjnej, będzie zawsze próbowało najpierw zainicjować obsługę administracyjną przy użyciu punktu dystrybucji, a następnie wrócić do tworzenia kopii zapasowej, jeśli to się nie powiedzie. Jeśli urządzenie jest ustawione na dynamiczną ponowną obsługę administracyjną, IoT Edge zostanie ponownie uruchomione i Zainicjuj obsługę administracyjną w przypadku wykrycia zdarzenia ponownego aprowizacji. Aby uzyskać więcej informacji, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](../iot-dps/concepts-device-reprovision.md).

1. Zapisz i zamknij plik config. YAML.

1. Uruchom ponownie środowisko uruchomieniowe IoT Edge, aby wyszukać wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. Znajdź sekcję **aprowizacji** pliku. Usuń znaczniki komentarza z wierszy dla aprowizacji usługi DPS przy użyciu certyfikatu X. 509 i upewnij się, że wszystkie inne wiersze aprowizacji zostały oznaczone jako komentarze.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "x509"
   # registration_id = "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"

   identity_cert = "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

1. Zaktualizuj wartości `id_scope` , `identity_cert` i `identity_pk` wraz z informacjami o usłudze DPS i urządzeniu.

   Wartość certyfikatu tożsamości można podać jako identyfikator URI pliku lub można ją dynamicznie wystawić przy użyciu narzędzia EST lub lokalnego urzędu certyfikacji. Usuń komentarz tylko z jednego wiersza na podstawie formatu wybranego do użycia.

   Wartość klucza prywatnego tożsamości można podać jako identyfikator URI pliku lub identyfikator URI PKCS # 11. Usuń komentarz tylko z jednego wiersza na podstawie formatu wybranego do użycia.

   Jeśli używasz dowolnego identyfikatora URI PKCS # 11, Znajdź sekcję **PKCS # 11** w pliku konfiguracji i podaj informacje o konfiguracji PKCS # 11.

1. Opcjonalnie podaj `registration_id` dla urządzenia. W przeciwnym razie pozostaw ten wiersz komentarzem, aby zarejestrować urządzenie przy użyciu nazwy pospolitej certyfikatu tożsamości.

1. Zapisz i zamknij plik.

1. Zastosuj zmiany konfiguracji wprowadzone w IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Urządzenie z systemem Windows

1. Otwórz okno programu PowerShell w trybie administratora. Należy pamiętać, aby podczas instalowania IoT Edge używać sesji AMD64 programu PowerShell, a nie programu PowerShell (x86).

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko uruchomieniowe IoT Edge na komputerze. Polecenie domyślnie umożliwia ręczne Inicjowanie obsługi przy użyciu kontenerów systemu Windows, więc Użyj `-DpsX509` flagi, aby użyć automatycznej aprowizacji z uwierzytelnianiem certyfikatu X. 509.

   Zastąp wartości symboli zastępczych dla `{scope_id}` , `{identity cert chain path}` i `{identity key path}` z odpowiednimi wartościami z wystąpienia usługi DPS i ścieżkami plików na urządzeniu.

   Dodaj, `-RegistrationId {registration_id}` Jeśli chcesz ustawić identyfikator urządzenia jako inny niż nazwa pospolita certyfikatu tożsamości.

   Dodaj `-ContainerOs Linux` parametr, jeśli używasz kontenerów systemu Linux w systemie Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Plik konfiguracji przechowuje certyfikat i informacje o kluczu jako identyfikatory URI plików. Jednak polecenie Initialize-IoTEdge obsługuje ten krok formatowania, więc można podać ścieżkę bezwzględną do certyfikatu i plików kluczy na urządzeniu.

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

Jeśli środowisko uruchomieniowe zostało pomyślnie uruchomione, możesz przejść do IoT Hub i rozpocząć wdrażanie modułów IoT Edge na urządzeniu.

Możesz sprawdzić, czy została użyta Rejestracja indywidualna utworzona w usłudze Device Provisioning. Przejdź do wystąpienia usługi Device Provisioning w Azure Portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Zwróć uwagę, że jest **przypisany** stan rejestracji i identyfikator urządzenia jest wyświetlany.

Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zostało prawidłowo zainstalowane i uruchomione.

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

## <a name="next-steps"></a>Następne kroki

Proces rejestracji w usłudze Device Provisioning Service umożliwia ustawienie tagów urządzeń i urządzeń w tym samym czasie, w których jest inicjowane nowe urządzenie. Tych wartości można użyć do kierowania poszczególnych urządzeń lub grup urządzeń przy użyciu funkcji automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-at-scale.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-cli-at-scale.md).
