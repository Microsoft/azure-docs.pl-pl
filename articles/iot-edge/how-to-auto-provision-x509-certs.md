---
title: Automatyczne aprowizyjuj urządzenia z DPS przy użyciu certyfikatów X.509 — Usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Użyj certyfikatów X.509, aby przetestować automatyczne inicjowanie obsługi administracyjnej urządzeń dla usługi Azure IoT Edge z usługą inicjowania obsługi administracyjnej urządzeń
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: kevindaw
ms.date: 04/09/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d5e968e578428a16a0005149a409986015a1fc5c
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393753"
---
# <a name="create-and-provision-an-iot-edge-device-using-x509-certificates"></a>Tworzenie i aprowizyja na urządzeniu Usługi IoT Edge przy użyciu certyfikatów X.509

Dzięki [usłudze inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub (DPS)](../iot-dps/index.yml)można automatycznie aprowizować urządzenia usługi IoT Edge przy użyciu certyfikatów X.509. Jeśli nie znasz procesu automatycznego inicjowania obsługi administracyjnej, przejrzyj [pojęcia automatycznego inicjowania obsługi administracyjnej](../iot-dps/concepts-auto-provisioning.md) przed kontynuowaniem.

W tym artykule pokazano, jak utworzyć rejestrację usługi inicjowania obsługi administracyjnej urządzeń przy użyciu certyfikatów X.509 na urządzeniu usługi IoT Edge, wykonując następujące czynności:

* Generowanie certyfikatów i kluczy.
* Utwórz rejestrację indywidualną dla urządzenia lub rejestrację grupową dla zestawu urządzeń.
* Zainstaluj środowisko uruchomieniowe usługi IoT Edge i zarejestruj urządzenie za pomocą usługi IoT Hub.

Używanie certyfikatów X.509 jako mechanizmu zaświadczania jest doskonałym sposobem skalowania produkcji i upraszczania inicjowania obsługi administracyjnej urządzeń. Zazwyczaj certyfikaty X.509 są rozmieszczone w łańcuchu zaufania certyfikatów. Począwszy od certyfikatu głównego z podpisem własnym lub zaufanym, każdy certyfikat w łańcuchu podpisuje następny niższy certyfikat. Ten wzorzec tworzy delegowany łańcuch zaufania od certyfikatu głównego w dół za pośrednictwem każdego certyfikatu pośredniego do końcowego certyfikatu "liścia" zainstalowanego na urządzeniu.

## <a name="prerequisites"></a>Wymagania wstępne

* Aktywne Centrum IoT.
* Urządzenie fizyczne lub wirtualne, które ma być urządzeniem usługi IoT Edge.
* Zainstalowana najnowsza wersja [Gita.](https://git-scm.com/download/)
* Wystąpienie usługi inicjowania obsługi administracyjnej urządzeń usługi Usługi obsługi urządzeń usługi IoT hub na platformie Azure, połączone z centrum IoT hub.
  * Jeśli nie masz wystąpienia usługi inicjowania obsługi urządzeń, postępuj zgodnie z instrukcjami w [aplikacji Konfigurowanie dps usługi Dps w centrum IoT](../iot-dps/quick-setup-auto-provision.md).
  * Po uruchomieniu usługi inicjowania obsługi administracyjnej urządzeń skopiuj wartość **zakresu identyfikatora** ze strony przeglądowej. Ta wartość jest używana podczas konfigurowania środowiska wykonawczego usługi IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generowanie certyfikatów tożsamości urządzenia

Certyfikat tożsamości urządzenia to certyfikat typu liść łączący się za pośrednictwem łańcucha zaufania certyfikatów z najwyższym certyfikatem urzędu certyfikacji X.509(CA). Certyfikat tożsamości urządzenia musi mieć ustawioną nazwę pospolitą (CN) na identyfikator urządzenia, który ma mieć urządzenie w centrum IoT hub.

Certyfikaty tożsamości urządzenia są używane tylko do inicjowania obsługi administracyjnej urządzenia usługi IoT Edge i uwierzytelniania urządzenia za pomocą usługi Azure IoT Hub. Nie podpisują certyfikatów, w przeciwieństwie do certyfikatów urzędu certyfikacji, które urządzenie IoT Edge przedstawia modułom lub urządzeniom typu liść do weryfikacji. Aby uzyskać więcej informacji, zobacz [szczegóły użycia certyfikatu usługi Azure IoT Edge](iot-edge-certs.md).

Po utworzeniu certyfikatu tożsamości urządzenia powinny być dostępne dwa pliki: plik cer lub pem zawierający publiczną część certyfikatu oraz plik cer lub pem z kluczem prywatnym certyfikatu. Jeśli planujesz używać rejestracji grup w dps, potrzebujesz również publicznej części pośredniego lub głównego certyfikatu urzędu certyfikacji w tym samym łańcuchu zaufania certyfikatów.

Do skonfigurowania automatycznego inicjowania obsługi administracyjnej za pomocą x.509 potrzebne są następujące pliki:

* Certyfikat tożsamości urządzenia i jego certyfikat klucza prywatnego. Certyfikat tożsamości urządzenia jest przekazyany do dps, jeśli tworzysz rejestrację indywidualną. Klucz prywatny jest przekazywany do środowiska wykonawczego usługi IoT Edge.
* Pełny certyfikat łańcucha, który powinien mieć co najmniej tożsamość urządzenia i certyfikaty pośrednie w nim. Certyfikat pełnego łańcucha jest przekazywany do środowiska wykonawczego IoT Edge.
* Pośredni lub główny certyfikat urzędu certyfikacji z łańcucha zaufania certyfikatów. Ten certyfikat jest przekazyany do dps po utworzeniu rejestracji grupy.

### <a name="use-test-certificates"></a>Używanie certyfikatów testowych

Jeśli nie masz urzędu certyfikacji dostępne do tworzenia nowych certyfikatów tożsamości i chcesz wypróbować ten scenariusz, repozytorium git usługi Azure IoT Edge zawiera skrypty, których można użyć do generowania certyfikatów testowych. Certyfikaty te są przeznaczone wyłącznie do badań rozwojowych i nie mogą być używane w produkcji.

Aby utworzyć certyfikaty testowe, wykonaj czynności opisane w [artykule Tworzenie certyfikatów demonstracyjnych w celu przetestowania funkcji urządzenia IoT Edge](how-to-create-test-certificates.md). Wykonaj dwie wymagane sekcje, aby skonfigurować skrypty generowania certyfikatów i utworzyć główny certyfikat urzędu certyfikacji. Następnie wykonaj kroki, aby utworzyć certyfikat tożsamości urządzenia. Po zakończeniu powinieneś mieć następujący łańcuch certyfikatów i parę kluczy:

W systemie Linux:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

W systemie Windows:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

Oba certyfikaty są potrzebne na urządzeniu Usługi IoT Edge. Jeśli zamierzasz korzystać z rejestracji indywidualnej w dps, przekażesz plik .cert.pem. Jeśli zamierzasz używać rejestracji grup w dps, potrzebujesz również pośredniego lub głównego certyfikatu urzędu certyfikacji w tym samym łańcuchu zaufania certyfikatów do przekazania. Jeśli używasz certyfikatów demonstracyjnych, użyj certyfikatu `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` do rejestracji grupowej.

## <a name="create-a-dps-individual-enrollment"></a>Tworzenie indywidualnej rejestracji DPS

Wygenerowane certyfikaty i klucze służy do tworzenia indywidualnej rejestracji w dps dla jednego urządzenia IoT Edge. Rejestracje indywidualne przyjmują publiczną część certyfikatu tożsamości urządzenia i dopasowywać ją do certyfikatu na urządzeniu.

Jeśli chcesz aprowizować wiele urządzeń IoT Edge, wykonaj kroki opisane w następnej sekcji [Tworzenie rejestracji grupy DPS](#create-a-dps-group-enrollment).

Podczas tworzenia rejestracji w dps, masz możliwość zadeklarowania **stanu bliźniaczej reprezentacji urządzenia początkowego**. W bliźniaczej reprezentacji urządzenia można ustawić znaczniki, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, takiej jak region, środowisko, lokalizacja lub typ urządzenia. Tagi te są używane do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

Aby uzyskać więcej informacji na temat rejestracji w usłudze inicjowania obsługi administracyjnej urządzeń, zobacz [Jak zarządzać rejestracjami urządzeń](../iot-dps/how-to-manage-enrollments.md).

   > [!TIP]
   > W wierszu polecenia platformy Azure można utworzyć [rejestrację](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) lub [grupę rejestracji](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) i użyć flagi **obsługującej krawędź,** aby określić, że urządzenie lub grupa urządzeń jest urządzeniem usługi IoT Edge.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.

1. W obszarze **Ustawienia**wybierz pozycję **Zarządzaj rejestracjami**.

1. Wybierz **pozycję Dodaj rejestrację indywidualną,** a następnie wykonaj następujące czynności, aby skonfigurować rejestrację:  

   * **Mechanizm**: Wybierz **X.509**.

   * **Podstawowy certyfikat .pem lub plik cer**: Prześlij plik publiczny z certyfikatu tożsamości urządzenia. Jeśli skrypty zostały użyte do wygenerowania certyfikatu testowego, wybierz następujący plik:

      `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`

   * **Identyfikator urządzenia usługi IoT Hub:** podaj identyfikator urządzenia, jeśli chcesz. Identyfikatory urządzeń można użyć do kierowania poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podasz identyfikatora urządzenia, używana jest nazwa pospolita (CN) w certyfikacie X.509.

   * **Urządzenie IoT Edge**: Wybierz **true,** aby zadeklarować, że rejestracja jest dla urządzenia IoT Edge.

   * **Wybierz koncentratory IoT, do**których można przypisać to urządzenie: Wybierz połączony węzeł IoT, do którego chcesz podłączyć urządzenie. Można wybrać wiele koncentratorów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   * **Początkowy stan bliźniaczej**reprezentacji urządzenia: Dodaj wartość znacznika, która ma zostać dodana do bliźniaczej reprezentacji urządzenia, jeśli chcesz. Tagów można używać do grup docelowych urządzeń do automatycznego wdrażania. Przykład:

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

Teraz, gdy istnieje rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie aprowizować urządzenie podczas instalacji. Przejdź do sekcji [Zainstaluj środowisko wykonawcze usługi IoT Edge,](#install-the-iot-edge-runtime) aby skonfigurować urządzenie Usługi IoT Edge.

## <a name="create-a-dps-group-enrollment"></a>Tworzenie rejestracji grupy DPS

Użyj wygenerowanych certyfikatów i kluczy, aby utworzyć rejestrację grupową w dps dla wielu urządzeń IoT Edge. Rejestracje grup używają pośredniego lub głównego certyfikatu urzędu certyfikacji z łańcucha zaufania certyfikatów używanego do generowania certyfikatów tożsamości poszczególnych urządzeń.

Jeśli zamiast tego chcesz aprowizować pojedyncze urządzenie IoT Edge, wykonaj kroki opisane w poprzedniej sekcji [Utwórz rejestrację indywidualną DPS](#create-a-dps-individual-enrollment).

Podczas tworzenia rejestracji w dps, masz możliwość zadeklarowania **stanu bliźniaczej reprezentacji urządzenia początkowego**. W bliźniaczej reprezentacji urządzenia można ustawić znaczniki, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, takiej jak region, środowisko, lokalizacja lub typ urządzenia. Tagi te są używane do tworzenia [wdrożeń automatycznych](how-to-deploy-monitor.md).

### <a name="verify-your-root-certificate"></a>Weryfikowanie certyfikatu głównego

Podczas tworzenia grupy rejestracji można użyć zweryfikowanego certyfikatu. Certyfikat za pomocą dps można zweryfikować, potwierdzając, że masz własność certyfikatu głównego. Aby uzyskać więcej informacji, zobacz [Jak wykonać dowód posiadania certyfikatów urzędu certyfikacji X.509](../iot-dps/how-to-verify-certificates.md).

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.

1. Wybierz **pozycję Certyfikaty** z menu po lewej stronie.

1. Wybierz **pozycję Dodaj,** aby dodać nowy certyfikat.

1. Wprowadź przyjazną nazwę certyfikatu, a następnie przejdź do pliku .cer lub pem reprezentującego publiczną część certyfikatu X.509.

   Jeśli używasz certyfikatów demonstracyjnych, przekaż `<wrkdir>/certs/azure-iot-test-only.root.ca.cert.pem` certyfikat.

1. Wybierz pozycję **Zapisz**.

1. Certyfikat powinien być teraz wyświetlany na stronie **Certyfikaty.** Wybierz go, aby otworzyć szczegóły certyfikatu.

1. Wybierz **pozycję Generuj kod weryfikacyjny,** a następnie skopiuj wygenerowany kod.

1. Niezależnie od tego, czy został przywieziony własny certyfikat urzędu certyfikacji, czy certyfikaty demonstracyjne, możesz użyć narzędzia weryfikacji dostępnego w repozytorium usługi IoT Edge w celu zweryfikowania dowodu posiadania. Narzędzie weryfikacyjne używa certyfikatu urzędu certyfikacji do podpisania nowego certyfikatu, który ma podany kod weryfikacyjny jako nazwę podmiotu.

   * W systemie Windows:

     ```powershell
     New-CACertsVerificationCert "<verification code>"
     ```

   * W systemie Linux:

     ```bash
     ./certGen.sh create_verification_certificate <verification code>
     ```

1. Na tej samej stronie szczegółów certyfikatu w witrynie Azure portal przekaż nowo wygenerowany certyfikat weryfikacji.

1. Wybierz pozycję **Verify** (Weryfikuj).

### <a name="create-enrollment-group"></a>Tworzenie grupy rejestracji

Aby uzyskać więcej informacji na temat rejestracji w usłudze inicjowania obsługi administracyjnej urządzeń, zobacz [Jak zarządzać rejestracjami urządzeń](../iot-dps/how-to-manage-enrollments.md).

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.

1. W obszarze **Ustawienia**wybierz pozycję **Zarządzaj rejestracjami**.

1. Wybierz **pozycję Dodaj grupę rejestracji,** a następnie wykonaj następujące czynności, aby skonfigurować rejestrację:

   * **Nazwa grupy**: Podaj zapadającą w pamięć nazwę dla tej rejestracji grupy.

   * **Typ zaświadczenia**: Wybierz **certyfikat**.

   * **Urządzenie IoT Edge**: Wybierz **true**. W przypadku rejestracji w grupie wszystkie urządzenia muszą być urządzeniami IoT Edge lub żadne z nich nie może być.

   * **Typ certyfikatu:** Wybierz **certyfikat urzędu certyfikacji,** jeśli certyfikat zweryfikowanego urzędu certyfikacji jest przechowywany w systemie DPS lub **certyfikat pośredni,** jeśli chcesz przekazać nowy plik tylko dla tej rejestracji.

   * **Certyfikat podstawowy:** Jeśli w ostatniej sekcji wybrano certyfikat urzędu certyfikacji, wybierz certyfikat z listy rozwijanej. Jeśli wybrano certyfikat pośredni, przekaż plik publiczny z certyfikatu urzędu certyfikacji w łańcuchu zaufania certyfikatów, który został użyty do wygenerowania certyfikatów tożsamości urządzenia.

   * **Wybierz koncentratory IoT, do**których można przypisać to urządzenie: Wybierz połączony węzeł IoT, do którego chcesz podłączyć urządzenie. Można wybrać wiele koncentratorów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami alokacji.

   * **Początkowy stan bliźniaczej**reprezentacji urządzenia: Dodaj wartość znacznika, która ma zostać dodana do bliźniaczej reprezentacji urządzenia, jeśli chcesz. Tagów można używać do grup docelowych urządzeń do automatycznego wdrażania. Przykład:

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

Teraz, gdy istnieje rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie aprowizować urządzenie podczas instalacji. Przejdź do następnej sekcji, aby skonfigurować urządzenie IoT Edge.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska wykonawczego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Jego składniki są uruchamiane w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchomić kod na krawędzi.

Aprowizacji X.509 z DPS jest obsługiwany tylko w Uodochy IoT Edge w wersji 1.0.9 lub nowszej.

Podczas inicjowania obsługi administracyjnej urządzenia potrzebne są następujące informacje:

* Wartość **zakresu identyfikatora** DPS. Tę wartość można pobrać ze strony przeglądu wystąpienia DPS w witrynie Azure portal.
* Plik łańcucha certyfikatu tożsamości urządzenia na urządzeniu.
* Plik klucza tożsamości urządzenia na urządzeniu.
* Opcjonalny identyfikator rejestracji (pobrany z nazwy pospolitej w certyfikacie tożsamości urządzenia, jeśli nie jest podany).

### <a name="linux-device"></a>Urządzenie z systemem Linux

Użyj następującego łącza, aby zainstalować środowisko uruchomieniowe usługi Azure IoT Edge na urządzeniu przy użyciu poleceń odpowiednich dla architektury urządzenia. Po dojście do sekcji dotyczącej konfigurowania demona zabezpieczeń należy skonfigurować środowisko uruchomieniowe IoT Edge dla automatycznego, a nie ręcznego inicjowania obsługi administracyjnej urządzenia XoT Edge. Po zakończeniu poprzednich sekcji tego artykułu należy mieć wszystkie potrzebne informacje i pliki certyfikatów.

[Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

Po dodaniu certyfikatu X.509 i informacji o kluczu do pliku config.yaml ścieżki powinny być dostarczane jako identyfikatory URI plików. Przykład:

* `file:///<path>/identity_certificate_chain.pem`
* `file:///<path>/identity_key.pem`

Sekcja w pliku konfiguracyjnym automatycznego inicjowania obsługi administracyjnej X.509 wygląda następująco:

```yaml
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
```

Zastąp wartości `scope_id` `identity_cert`zastępcze dla , `identity_pk` z identyfikatorem zakresu z wystąpienia DPS i identyfikatorami URI do łańcucha certyfikatów i lokalizacji plików kluczy na urządzeniu. Podaj `registration_id` urządzenie, jeśli chcesz, lub pozostaw ten wiersz, aby zarejestrować urządzenie z nazwą CN certyfikatu tożsamości.

Zawsze ponownie uruchamiaj demona zabezpieczeń po zaktualizowaniu pliku config.yaml.

```bash
sudo systemctl restart iotedge
```

### <a name="windows-device"></a>Urządzenie z systemem Windows

Zainstaluj środowisko uruchomieniowe usługi IoT Edge na urządzeniu, dla którego wygenerowano łańcuch certyfikatów tożsamości i klucz tożsamości. Środowiska wykonawczego IoT Edge można skonfigurować do automatycznego, a nie ręcznego inicjowania obsługi administracyjnej.

Aby uzyskać bardziej szczegółowe informacje dotyczące instalowania usługi IoT Edge w systemie Windows, w tym wymagania wstępne i instrukcje dotyczące zadań, takich jak zarządzanie kontenerami i aktualizowanie usługi IoT Edge, zobacz [Instalowanie środowiska wykonawczego usługi Azure IoT Edge w systemie Windows](how-to-install-iot-edge-windows.md).

1. Otwórz okno programu PowerShell w trybie administratora. Pamiętaj, aby podczas instalowania aplikacji IoT Edge, a nie programu PowerShell (x86), należy używać sesji programu AMD64 programu PowerShell.

1. Polecenie **Deploy-IoTEdge** sprawdza, czy komputer z systemem Windows jest w obsługiwanej wersji, włącza funkcję kontenerów, a następnie pobiera środowisko uruchomieniowe moby i środowisko wykonawcze usługi IoT Edge. Polecenie domyślnie używa kontenerów systemu Windows.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

1. W tym momencie urządzenia IoT Core mogą zostać automatycznie ponownie uruchomione. Inne urządzenia z systemem Windows 10 lub Windows Server mogą monitować o ponowne uruchomienie. Jeśli tak, uruchom ponownie urządzenie teraz. Gdy urządzenie będzie gotowe, uruchom program PowerShell jako administrator ponownie.

1. Polecenie **Initialize-IoTEdge** konfiguruje środowisko wykonawcze IoT Edge na komputerze. Polecenie domyślnie ręczne inicjowanie obsługi administracyjnej, chyba że flaga `-Dps` jest używana do używania automatycznego inicjowania obsługi administracyjnej.

   Zastąp wartości `{scope_id}` `{identity cert chain path}`zastępcze `{identity key path}` dla programu , oraz odpowiednie wartości z wystąpienia DPS i ścieżki plików na urządzeniu. Jeśli chcesz określić identyfikator rejestracji, `-RegistrationId {registration_id}` dołącz również, zastępując symbol zastępczy, stosownie do przypadku.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge -Dps -ScopeId {scope ID} -X509IdentityCertificate {identity cert chain path} -X509IdentityPrivateKey {identity key path}
   ```

   >[!TIP]
   >Plik config.yaml przechowuje certyfikat i informacje o kluczu jako identyfikatory URI plików. Jednak polecenie Initialize-IoTEdge obsługuje ten krok formatowania, dzięki czemu można podać bezwzględną ścieżkę do certyfikatu i plików kluczy na urządzeniu.

## <a name="verify-successful-installation"></a>Weryfikacja pomyślnej instalacji

Jeśli środowisko wykonawcze rozpoczęło się pomyślnie, możesz przejść do centrum IoT Hub i rozpocząć wdrażanie modułów usługi IoT Edge na urządzeniu.

Można sprawdzić, czy została użyta indywidualna rejestracja utworzona w usłudze inicjowania obsługi administracyjnej urządzeń. Przejdź do wystąpienia usługi inicjowania obsługi administracyjnej urządzeń w witrynie Azure portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Należy zauważyć, że stan rejestracji jest **przypisany** i identyfikator urządzenia jest wyświetlany.

Użyj następujących poleceń na urządzeniu, aby sprawdzić, czy środowisko wykonawcze zostało zainstalowane i uruchomione pomyślnie.

### <a name="linux-device"></a>Urządzenie z systemem Linux

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

Proces rejestracji usługi inicjowania obsługi administracyjnej urządzeń umożliwia ustawienie identyfikatora urządzenia i bliźniaczych tagów urządzenia w tym samym czasie, co aprowizowanie nowego urządzenia. Za pomocą tych wartości można kierować reklamy na poszczególne urządzenia lub grupy urządzeń za pomocą automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły usługi IoT Edge na dużą skalę przy użyciu witryny Azure portal](how-to-deploy-monitor.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure.](how-to-deploy-monitor-cli.md)
