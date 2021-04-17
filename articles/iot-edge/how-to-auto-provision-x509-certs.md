---
title: Automatycznie aprowizuj urządzenia za pomocą usługi DPS przy użyciu certyfikatów X.509 — Azure IoT Edge | Microsoft Docs
description: Używanie certyfikatów X.509 do testowania automatycznego aprowizowania urządzeń Azure IoT Edge za pomocą usługi Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: f3c783c57b49b45943882703aec6d735d12bf830
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481960"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Tworzenie i aprowiz IoT Edge urządzenia przy użyciu certyfikatów X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Usługa [Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml)umożliwia automatyczne aprowizowanie urządzeń IoT Edge przy użyciu certyfikatów X.509. Jeśli nie masz informacji o procesie automatycznej aprowacji, przed [](../iot-dps/about-iot-dps.md#provisioning-process) kontynuowaniem zapoznaj się z omówieniem aprowacji.

W tym artykule pokazano, jak utworzyć rejestrację w usłudze Device Provisioning Przy użyciu certyfikatów X.509 na urządzeniu IoT Edge w następujący sposób:

* Wygeneruj certyfikaty i klucze.
* Utwórz rejestrację indywidualną dla urządzenia lub rejestrację grupową dla zestawu urządzeń.
* Zainstaluj środowisko IoT Edge uruchomieniowe i zarejestruj urządzenie w IoT Hub.

Używanie certyfikatów X.509 jako mechanizmu zaświadczenia to doskonały sposób skalowania produkcji i upraszczania aprowacji urządzeń. Zazwyczaj certyfikaty X.509 są uporządkowane w łańcuchu zaufania certyfikatów. Począwszy od certyfikatu z podpisem własnym lub zaufanego certyfikatu głównego, każdy certyfikat w łańcuchu podpisuje następny niższy certyfikat. Ten wzorzec tworzy delegowany łańcuch zaufania z certyfikatu głównego za pośrednictwem każdego certyfikatu pośredniego do końcowego certyfikatu "liścia" zainstalowanego na urządzeniu.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywny IoT Hub.
* Urządzenie fizyczne lub wirtualne, które ma być IoT Edge urządzeniem.
* Zainstalowana najnowsza [wersja usługi Git.](https://git-scm.com/download/)
* Wystąpienie usługi IoT Hub Device Provisioning Service na platformie Azure połączone z centrum IoT.
  * Jeśli nie masz wystąpienia usługi Device Provisioning Service, postępuj zgodnie z instrukcjami w te IoT Hub [DPS.](../iot-dps/quick-setup-auto-provision.md)
  * Po uruchomieniu usługi Device Provisioning Service skopiuj wartość **Zakres** identyfikatorów ze strony przeglądu. Ta wartość jest wartością używaną podczas konfigurowania IoT Edge uruchomieniowego.

## <a name="generate-device-identity-certificates"></a>Generowanie certyfikatów tożsamości urządzeń

Certyfikat tożsamości urządzenia jest certyfikatem liścia, który łączy się za pośrednictwem łańcucha certyfikatów z najwyższym certyfikatem urzędu certyfikacji X.509. Certyfikat tożsamości urządzenia musi mieć nazwę pospolitą (CN) ustawioną na identyfikator urządzenia, który ma znajdować się w centrum IoT.

Certyfikaty tożsamości urządzeń są używane tylko do aprowizowania urządzenia IoT Edge i uwierzytelniania urządzenia za pomocą Azure IoT Hub. W przeciwieństwie do certyfikatów urzędu certyfikacji, które urządzenie IoT Edge do weryfikacji, nie podpisują one certyfikatów. Aby uzyskać więcej informacji, [zobacz Azure IoT Edge szczegóły użycia certyfikatu.](iot-edge-certs.md)

Po utworzeniu certyfikatu tożsamości urządzenia powinny być dostępne dwa pliki: plik cer lub pem zawierający publiczną część certyfikatu oraz plik cer lub pem z kluczem prywatnym certyfikatu. Jeśli planujesz użycie rejestracji grupowej w u usługi DPS, musisz również użyć publicznej części pośredniego lub głównego certyfikatu urzędu certyfikacji w tym samym łańcuchu zaufania certyfikatów.

Aby skonfigurować automatyczną aprowizowanie za pomocą X.509, potrzebne są następujące pliki:

* Certyfikat tożsamości urządzenia i jego certyfikat klucza prywatnego. Certyfikat tożsamości urządzenia jest przekazywany do usługi DPS w przypadku utworzenia rejestracji indywidualnej. Klucz prywatny jest przekazywany do IoT Edge uruchomieniowego.
* Certyfikat pełnego łańcucha, który powinien mieć co najmniej tożsamość urządzenia i certyfikaty pośrednie. Certyfikat pełnego łańcucha jest przekazywany do IoT Edge uruchomieniowego.
* Pośredni lub główny certyfikat urzędu certyfikacji z łańcucha certyfikatów zaufania. Ten certyfikat jest przekazywany do usługi DPS w przypadku utworzenia rejestracji grupowej.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
> [!NOTE]
> Obecnie ograniczenie w programie uniemożliwia korzystanie z certyfikatów, które wygasną od 1 stycznia 2038 r. lub później.

:::moniker-end

### <a name="use-test-certificates-optional"></a>Używanie certyfikatów testowych (opcjonalnie)

Jeśli nie masz dostępnego urzędu certyfikacji do tworzenia nowych certyfikatów tożsamości i chcesz wypróbować ten scenariusz, repozytorium git usługi Azure IoT Edge zawiera skrypty, których można użyć do wygenerowania certyfikatów testowych. Te certyfikaty są przeznaczone tylko do testowania programowego i nie mogą być używane w środowisku produkcyjnym.

Aby utworzyć certyfikaty testowe, wykonaj kroki opisane w teście Tworzenie [certyfikatów demonstracyjnych, aby przetestować IoT Edge urządzenia.](how-to-create-test-certificates.md) Wykonaj dwie wymagane sekcje, aby skonfigurować skrypty generowania certyfikatów i utworzyć certyfikat głównego urzędu certyfikacji. Następnie wykonaj kroki, aby utworzyć certyfikat tożsamości urządzenia. Po zakończeniu powinien być następujący łańcuch certyfikatów i para kluczy:

W systemie Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

W systemie Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Oba te certyfikaty są potrzebne na IoT Edge urządzenia. Jeśli zamierzasz używać rejestracji indywidualnej w uciece DPS, przekaż plik .cert.pem. Jeśli zamierzasz używać rejestracji grupowej w u usługach DPS, do przekazania potrzebny jest również certyfikat pośredniego lub głównego urzędu certyfikacji w tym samym łańcuchu zaufania certyfikatów. Jeśli używasz certyfikatów demonstracyjnych, użyj certyfikatu `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` do rejestracji grupowej.

## <a name="create-a-dps-individual-enrollment"></a>Tworzenie rejestracji indywidualnej w u programie DPS

Użyj wygenerowanych certyfikatów i kluczy, aby utworzyć rejestrację indywidualną w u usługi DPS dla jednego IoT Edge urządzenia. Rejestracje indywidualne przejmą publiczną część certyfikatu tożsamości urządzenia i są zgodne z certyfikatem na urządzeniu.

Jeśli chcesz aprowizować wiele urządzeń IoT Edge, wykonaj kroki opisane w następnej sekcji Tworzenie [rejestracji grupowej usługi DPS.](#create-a-dps-group-enrollment)

Podczas tworzenia rejestracji w u programie DPS masz możliwość zadeklarowania początkowego stanu bliźniaczej **reprezentacji urządzenia.** W bliźniaczej reprezentacji urządzenia można ustawić tagi tak, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Te tagi są używane do tworzenia [wdrożeń automatycznych.](how-to-deploy-at-scale.md)

Aby uzyskać więcej informacji na temat rejestracji w usłudze Device Provisioning Service, zobacz [Jak zarządzać rejestracjami urządzeń.](../iot-dps/how-to-manage-enrollments.md)

   > [!TIP]
   > W interfejsie wiersza polecenia [](/cli/azure/iot/dps/enrollment) platformy Azure [](/cli/azure/iot/dps/enrollment-group) możesz utworzyć rejestrację lub grupę rejestracji i użyć **flagi** z włączoną obsługą krawędzi, aby określić, że urządzenie lub grupa urządzeń jest urządzeniem IoT Edge brzegowym.

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego wystąpienia IoT Hub Device Provisioning Service.

1. W **obszarze Ustawienia** wybierz pozycję Zarządzaj **rejestracjami.**

1. Wybierz **pozycję Dodaj rejestrację** indywidualną, a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   * **Mechanizm:** wybierz **pozycję X.509.**

   * **Plik PEM lub CER** certyfikatu podstawowego: przekaż plik publiczny z certyfikatu tożsamości urządzenia. Jeśli skrypty są używane do generowania certyfikatu testowego, wybierz następujący plik:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub identyfikator urządzenia:** podaj identyfikator urządzenia, jeśli chcesz. Identyfikatorów urządzeń można używać do kierowania poszczególnych urządzeń do wdrożenia modułu. Jeśli nie poniesiesz identyfikatora urządzenia, zostanie użyta nazwa pospolita (CN) w certyfikacie X.509.

   * **IoT Edge urządzenia:** wybierz pozycję **Prawda,** aby zadeklarować, że rejestracja dotyczy IoT Edge urządzenia.

   * **Wybierz centra IoT,** do których można przypisać to urządzenie: wybierz połączone centrum IoT Hub, z którym chcesz połączyć urządzenie. Możesz wybrać wiele centrów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   * **Początkowy stan bliźniaczej reprezentacji** urządzenia: jeśli chcesz, dodaj wartość tagu do dodania do bliźniaczej reprezentacji urządzenia. Za pomocą tagów można kierować grupy urządzeń do automatycznego wdrażania. Na przykład:

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

Teraz, gdy dla tego urządzenia istnieje rejestracja, środowisko IoT Edge może automatycznie aprowizuje urządzenie podczas instalacji. Przejdź do sekcji [Instalowanie IoT Edge uruchomieniowego,](#install-the-iot-edge-runtime) aby skonfigurować IoT Edge uruchomieniowe.

## <a name="create-a-dps-group-enrollment"></a>Tworzenie rejestracji grupy dps

Użyj wygenerowanych certyfikatów i kluczy, aby utworzyć rejestrację grupową w u usługi DPS dla wielu IoT Edge urządzeń. Rejestracje grupowe używają pośredniego lub głównego certyfikatu urzędu certyfikacji z łańcucha certyfikatów zaufania używanego do generowania certyfikatów tożsamości poszczególnych urządzeń.

Jeśli zamiast tego chcesz aprowizować pojedyncze urządzenie IoT Edge, wykonaj kroki opisane w poprzedniej sekcji Tworzenie rejestracji indywidualnej [usługi DPS.](#create-a-dps-individual-enrollment)

Podczas tworzenia rejestracji w uciece DPS masz możliwość zadeklarowania początkowego stanu bliźniaczej **reprezentacji urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi tak, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Te tagi są używane do tworzenia [wdrożeń automatycznych.](how-to-deploy-at-scale.md)

### <a name="verify-your-root-certificate"></a>Weryfikowanie certyfikatu głównego

Podczas tworzenia grupy rejestracji możesz użyć zweryfikowanego certyfikatu. Certyfikat można zweryfikować za pomocą usługi DPS, udowadniając, że masz własność certyfikatu głównego. Aby uzyskać więcej informacji, [zobacz How to do proof-of-possession for X.509 CA certificates](../iot-dps/how-to-verify-certificates.md)(Jak wykonać dowód posiadania certyfikatów X.509 urzędu certyfikacji).

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego wystąpienia IoT Hub Device Provisioning Service.

1. Wybierz **pozycję Certyfikaty** z menu po lewej stronie.

1. Wybierz **pozycję Dodaj,** aby dodać nowy certyfikat.

1. Wprowadź przyjazną nazwę certyfikatu, a następnie przejdź do pliku cer lub pem reprezentującego publiczną część certyfikatu X.509.

   Jeśli używasz certyfikatów demonstracyjnych, przekaż `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certyfikat.

1. Wybierz pozycję **Zapisz**.

1. Certyfikat powinien być teraz wymieniony na **stronie** Certyfikaty. Wybierz go, aby otworzyć szczegóły certyfikatu.

1. Wybierz **pozycję Generuj** kod weryfikacyjny, a następnie skopiuj wygenerowany kod.

1. Niezależnie od tego, czy masz własny certyfikat urzędu certyfikacji, czy korzystasz z certyfikatów demonstracyjnych, możesz użyć narzędzia weryfikacji dostępnego w repozytorium IoT Edge, aby zweryfikować dowód posiadania. Narzędzie weryfikacji używa certyfikatu urzędu certyfikacji do podpisania nowego certyfikatu, który ma podany kod weryfikacyjny jako nazwę podmiotu.

   * W systemie Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * W systemie Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na tej samej stronie szczegółów certyfikatu w Azure Portal przekaż nowo wygenerowany certyfikat weryfikacji.

1. Wybierz pozycję **Weryfikuj**.

### <a name="create-enrollment-group"></a>Tworzenie grupy rejestracji

Aby uzyskać więcej informacji na temat rejestracji w usłudze Device Provisioning Service, zobacz [Jak zarządzać rejestracjami urządzeń.](../iot-dps/how-to-manage-enrollments.md)

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego wystąpienia IoT Hub Device Provisioning Service.

1. W **obszarze Ustawienia** wybierz pozycję Zarządzaj **rejestracjami.**

1. Wybierz **pozycję Dodaj grupę rejestracji,** a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:

   * **Nazwa grupy:** podaj pamiętną nazwę dla tej rejestracji grupowej.

   * **Typ zaświadczenia:** wybierz **pozycję Certyfikat.**

   * **IoT Edge urządzenia:** wybierz pozycję **Prawda.** W przypadku rejestracji grupowej wszystkie urządzenia muszą być IoT Edge lub żadne z nich nie może być.

   * **Typ certyfikatu:** wybierz pozycję **Certyfikat** urzędu certyfikacji, jeśli masz  zweryfikowany certyfikat urzędu certyfikacji przechowywany z usługą DPS, lub Certyfikat pośredni, jeśli chcesz przekazać nowy plik tylko dla tej rejestracji.

   * **Certyfikat podstawowy:** jeśli w ostatniej sekcji wybrano certyfikat urzędu certyfikacji, wybierz certyfikat z listy rozwijanej. W przypadku wybrania certyfikatu pośredniego przekaż plik publiczny z certyfikatu urzędu certyfikacji w łańcuchu zaufania certyfikatów, który został użyty do wygenerowania certyfikatów tożsamości urządzeń.

   * **Wybierz centra IoT,** do których można przypisać to urządzenie: wybierz połączone centrum IoT Hub, z którym chcesz połączyć urządzenie. Możesz wybrać wiele centrów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   * **Początkowy stan bliźniaczej reprezentacji** urządzenia: jeśli chcesz, dodaj wartość tagu do dodania do bliźniaczej reprezentacji urządzenia. Za pomocą tagów można kierować grupy urządzeń do automatycznego wdrażania. Na przykład:

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

Teraz, gdy dla tego urządzenia istnieje rejestracja, środowisko IoT Edge może automatycznie aprowizuje urządzenie podczas instalacji. Przejdź do następnej sekcji, aby skonfigurować IoT Edge urządzenia.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchamiać kod na brzegu sieci.

Wykonaj kroki opisane w [artykule Instalowanie Azure IoT Edge uruchomieniowego,](how-to-install-iot-edge.md)a następnie wróć do tego artykułu, aby aprowizować urządzenie.

Aprowizowanie X.509 za pomocą usługi DPS jest obsługiwane IoT Edge wersji 1.0.9 lub nowszej.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia przy użyciu informacji aprowizowania

Po zainstalowaniu środowiska uruchomieniowego na urządzeniu skonfiguruj je przy użyciu informacji, których używa do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

Przygotuj następujące informacje:

* Wartość Zakres **identyfikatorów dps.** Tę wartość można pobrać ze strony przeglądu wystąpienia usługi DPS w Azure Portal.
* Plik łańcucha certyfikatów tożsamości urządzenia na urządzeniu.
* Plik klucza tożsamości urządzenia na urządzeniu.
* Opcjonalny identyfikator rejestracji. Jeśli nie zostanie podany, identyfikator jest ściągany z nazwy pospolitej w certyfikacie tożsamości urządzenia.

### <a name="linux-device"></a>Urządzenie z systemem Linux

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Otwórz plik konfiguracji na IoT Edge urządzeniu.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracji aprowizowania w pliku . Odkomentuj wiersze dotyczące aprowizowania certyfikatów X.509 usługi DPS i upewnij się, że wszystkie inne wiersze aprowizowania są komentarzami.

   Wiersz `provisioning:` nie powinien mieć poprzedzającego odstępu, a zagnieżdżone elementy powinny być wcięte przez dwie spacje.

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

1. Zaktualizuj wartości `scope_id` , `identity_cert` i przy użyciu usługi `identity_pk` DPS i informacji o urządzeniu.

   Po dodaniu certyfikatu X.509 i informacji o kluczu do pliku config.yaml ścieżki powinny być podane jako adresy URI plików. Na przykład:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. Opcjonalnie podaj `registration_id` parametr dla urządzenia. W przeciwnym razie pozostaw ten wiersz w komentarzu, aby zarejestrować urządzenie przy użyciu nazwy cn certyfikatu tożsamości.

1. Opcjonalnie możesz użyć linii lub , aby skonfigurować zachowanie `always_reprovision_on_startup` `dynamic_reprovisioning` ponownego aprowizowania urządzenia. Jeśli dla urządzenia ustawiono ponowne aprowizowanie przy uruchamianiu, zawsze będzie ono najpierw próbowało aprowizować usługę DPS, a następnie wrócić do tworzenia kopii zapasowej w przypadku niepowodzenia. Jeśli dla urządzenia ustawiono dynamiczne ponowne aprowizowanie, program IoT Edge ponowne uruchomienie i ponowne aprowizowanie w przypadku wykrycia zdarzenia ponownej aprowizowania. Aby uzyskać więcej informacji, [zobacz IoT Hub device reprovisioning concepts (Pojęcia dotyczące ponownego aprowizowania urządzeń).](../iot-dps/concepts-device-reprovision.md)

1. Zapisz i zamknij plik config.yaml.

1. Uruchom ponownie IoT Edge uruchomieniowe, aby przejmować wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

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

1. Znajdź **sekcję Provisioning** (Aprowizowanie) w pliku . Odkomentuj wiersze dotyczące aprowizowania usługi DPS przy użyciu certyfikatu X.509 i upewnij się, że wszystkie inne wiersze aprowności zostały zakomentowane.

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

1. Zaktualizuj wartości `id_scope` , `identity_cert` i przy użyciu usługi `identity_pk` DPS i informacji o urządzeniu.

   Wartość certyfikatu tożsamości może być podany jako plik URI lub może być wystawiony dynamicznie przy użyciu est lub lokalnego urzędu certyfikacji. Odkomentuj tylko jeden wiersz na podstawie formatu, który ma być używany.

   Wartość klucza prywatnego tożsamości może zostać podany jako plik URI lub PKCS#11 URI. Odkomentuj tylko jeden wiersz na podstawie formatu, który ma być używany.

   Jeśli używasz jakichkolwiek URI PKCS#11, znajdź sekcję **PKCS#11** w pliku konfiguracji i podaj informacje o konfiguracji PKCS#11.

1. Opcjonalnie podaj `registration_id` parametr dla urządzenia. W przeciwnym razie pozostaw ten wiersz jako komentarz, aby zarejestrować urządzenie przy użyciu nazwy pospolitej certyfikatu tożsamości.

1. Zapisz i zamknij plik.

1. Zastosuj zmiany konfiguracji wprowadzone do IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

### <a name="windows-device"></a>Urządzenie z systemem Windows

1. Otwórz okno programu PowerShell w trybie administratora. Pamiętaj, aby użyć sesji AMD64 programu PowerShell podczas instalowania programu IoT Edge, a nie programu PowerShell (x86).

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko IoT Edge uruchomieniowe na maszynie. Domyślnie polecenie to ręczne inicjowanie obsługi administracyjnej przy użyciu kontenerów systemu Windows, dlatego użyj flagi , aby użyć automatycznej aprowingu z uwierzytelnianiem certyfikatów `-DpsX509` X.509.

   Zastąp wartości symboli zastępczych , i odpowiednimi wartościami z wystąpienia usługi DPS i ścieżek `{scope_id}` `{identity cert chain path}` plików na `{identity key path}` urządzeniu.

   Dodaj wartość , jeśli chcesz ustawić identyfikator urządzenia jako inny niż nazwa cn `-RegistrationId {registration_id}` certyfikatu tożsamości.

   Dodaj parametr `-ContainerOs Linux` , jeśli używasz kontenerów systemu Linux w systemie Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -DpsX509 -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Plik konfiguracji przechowuje informacje o certyfikacie i kluczu jako adresy URI plików. Jednak to Initialize-IoTEdge obsługuje ten krok formatowania, więc możesz podać ścieżkę bezwzględną do certyfikatu i plików kluczy na urządzeniu.

## <a name="verify-successful-installation"></a>Weryfikowanie pomyślnej instalacji

Jeśli środowisko uruchomieniowe zostało uruchomione pomyślnie, możesz przejść do swojego IoT Hub i rozpocząć wdrażanie IoT Edge modułów na urządzeniu.

Możesz sprawdzić, czy została użyta rejestracja indywidualna utworzona w usłudze Device Provisioning Service. Przejdź do wystąpienia usługi Device Provisioning Service w Azure Portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Zwróć uwagę, że stan rejestracji jest **przypisany,** a identyfikator urządzenia jest na liście.

Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i uruchomione.

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

## <a name="next-steps"></a>Następne kroki

Proces rejestracji w usłudze Device Provisioning Service umożliwia ustawienie identyfikatora urządzenia i tagów bliźniaczej reprezentacji urządzenia w tym samym czasie, gdy aprowizuje się nowe urządzenie. Możesz użyć tych wartości, aby kierować je do poszczególnych urządzeń lub grup urządzeń przy użyciu automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na](how-to-deploy-at-scale.md) dużą skalę przy użyciu interfejsu Azure Portal lub [interfejsu wiersza polecenia platformy Azure.](how-to-deploy-cli-at-scale.md)
