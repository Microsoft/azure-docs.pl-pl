---
title: Tworzenie certyfikatów testowych — usługa Azure IoT Edge | Dokumenty firmy Microsoft
description: Tworzenie certyfikatów testowych i dowiedz się, jak zainstalować je na urządzeniu usługi Azure IoT Edge, aby przygotować się do wdrożenia produkcyjnego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 364846f6cef196f6cefa7872af48f262b387db4f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393818"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Tworzenie certyfikatów demonstracyjnych w celu testowania funkcji urządzenia IoT Edge

Urządzenia usługi IoT Edge wymagają certyfikatów do bezpiecznej komunikacji między środowiska wykonawczego, modułów i wszystkich urządzeń podrzędnych.
Jeśli nie masz urzędu certyfikacji do tworzenia wymaganych certyfikatów, możesz użyć certyfikatów demonstracyjnych, aby wypróbować funkcje IoT Edge w środowisku testowym.
W tym artykule opisano funkcje skryptów generowania certyfikatów, które usługa IoT Edge zapewnia do testowania.

Certyfikaty te wygasają w ciągu 30 dni i nie powinny być używane w żadnym scenariuszu produkcji.

Certyfikaty można utworzyć na dowolnym komputerze, a następnie skopiować je na urządzenie Usługi IoT Edge.
Łatwiej jest używać komputera podstawowego do tworzenia certyfikatów, zamiast generować je na samym urządzeniu IoT Edge.
Korzystając z komputera podstawowego, można skonfigurować skrypty raz, a następnie powtórzyć proces tworzenia certyfikatów dla wielu urządzeń.

Wykonaj następujące kroki, aby utworzyć certyfikaty demonstracyjne do testowania scenariusza usługi IoT Edge:

1. [Skonfiguruj skrypty](#set-up-scripts) do generowania certyfikatów na urządzeniu.
2. [Utwórz certyfikat głównego urzędu certyfikacji](#create-root-ca-certificate) używany do podpisywania wszystkich innych certyfikatów dla scenariusza.
3. Wygeneruj certyfikaty potrzebne dla scenariusza, który chcesz przetestować:
   * [Utwórz certyfikaty tożsamości urządzenia usługi IoT Edge,](#create-iot-edge-device-identity-certificates) aby przetestować automatyczne inicjowanie obsługi administracyjnej za pomocą usługi inicjowania obsługi administracyjnej urządzeń usługi IoT Hub.
   * [Tworzenie certyfikatów urzędu certyfikacji urządzeń usługi IoT Edge](#create-iot-edge-device-ca-certificates) w celu przetestowania scenariuszy produkcji lub scenariuszy bramy.
   * [Tworzenie certyfikatów urządzeń podrzędnych](#create-downstream-device-certificates) w celu przetestowania uwierzytelniania urządzeń podrzędnych w centrum IoT Hub w scenariuszu bramy.

## <a name="prerequisites"></a>Wymagania wstępne

Maszyna deweloperska z zainstalowanym Git.

## <a name="set-up-scripts"></a>Konfigurowanie skryptów

Repozytorium usługi IoT Edge w usłudze GitHub zawiera skrypty generowania certyfikatów, których można użyć do tworzenia certyfikatów demonstracyjnych.
Ta sekcja zawiera instrukcje dotyczące przygotowywania skryptów do uruchomienia na komputerze w systemie Windows lub Linux.
Jeśli korzystasz z komputera z systemem Linux, przejdź do [konfiguracji w systemie Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Konfigurowanie w systemie Windows

Aby utworzyć certyfikaty demonstracyjne na urządzeniu z systemem Windows, należy zainstalować openssl, a następnie sklonować skrypty generacji i skonfigurować je do lokalnego uruchamiania w programie PowerShell.

#### <a name="install-openssl"></a>Instalowanie openssl

Zainstaluj OpenSSL dla systemu Windows na komputerze, którego używasz do generowania certyfikatów.
Jeśli na urządzeniu z systemem Windows zainstalowano już openssl, możesz pominąć ten krok, ale upewnij się, że openssl.exe jest dostępny w zmiennej środowiskowej PATH.

OpenSSL można zainstalować na kilka sposobów, w tym następujące opcje:

* **Łatwiejsze:** Pobierz i zainstaluj dowolne [pliki binarne OpenSSL innych firm,](https://wiki.openssl.org/index.php/Binaries)na przykład z [OpenSSL na SourceForge](https://sourceforge.net/projects/openssl/). Dodaj pełną ścieżkę do openssl.exe do zmiennej środowiskowej PATH.

* **Zalecane:** Pobierz kod źródłowy OpenSSL i zbuduj pliki binarne na swoim komputerze samodzielnie lub za pośrednictwem [vcpkg](https://github.com/Microsoft/vcpkg). Poniższe instrukcje używają vcpkg do pobierania kodu źródłowego, kompilowania i instalowania openssl na komputerze z systemem Windows w prostych krokach.

   1. Przejdź do katalogu, w którym chcesz zainstalować vcpkg. Postępuj zgodnie z instrukcjami, aby pobrać i zainstalować [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Po zainstalowaniu vcpkg uruchom następujące polecenie z monitu programu PowerShell, aby zainstalować pakiet OpenSSL dla systemu Windows x64. Instalacja trwa zwykle około 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Dodaj `<vcpkg path>\installed\x64-windows\tools\openssl` do zmiennej środowiskowej PATH, aby plik openssl.exe był dostępny do wywołania.

#### <a name="prepare-scripts-in-powershell"></a>Przygotowywanie skryptów w programie PowerShell

Repozytorium git usługi Azure IoT Edge zawiera skrypty, których można użyć do generowania certyfikatów testowych.
W tej sekcji sklonujesz repozytorium IoT Edge i wykonasz skrypty.

1. Otwórz okno programu PowerShell w trybie administratora.

2. Sklonuj repozytorium git usługi IoT Edge, które zawiera skrypty do generowania certyfikatów demonstracyjnych. Użyj `git clone` polecenia lub [pobierz zip](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Przejdź do katalogu, w którym chcesz pracować. W tym artykule będziemy nazywać ten katalog * \<WRKDIR>*. Wszystkie certyfikaty i klucze zostaną utworzone w tym katalogu roboczym.

4. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium do katalogu roboczego.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Jeśli pobrano repozytorium jako zip, nazwa `iotedge-master` folderu jest i reszta ścieżki jest taka sama.

5. Włącz program PowerShell, aby uruchomić skrypty.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Przenieś funkcje używane przez skrypty do globalnej przestrzeni nazw programu PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   W oknie programu PowerShell zostanie wyświetlone ostrzeżenie, że certyfikaty generowane przez ten skrypt są tylko do celów testowych i nie powinny być używane w scenariuszach produkcyjnych.

7. Sprawdź, czy openssl został poprawnie zainstalowany i upewnij się, że nie będzie kolizji nazw z istniejącymi certyfikatami. Jeśli występują problemy, dane wyjściowe skryptu należy opisać, jak je naprawić w systemie.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Konfiguracja w systemie Linux

Aby utworzyć certyfikaty demonstracyjne na urządzeniu z systemem Windows, należy sklonować skrypty generowania i skonfigurować je do uruchamiania lokalnie w bash.

1. Sklonuj repozytorium git usługi IoT Edge, które zawiera skrypty do generowania certyfikatów demonstracyjnych.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Przejdź do katalogu, w którym chcesz pracować. Będziemy odnosić się do tego katalogu w całym artykule jako * \<WRKDIR>*. Wszystkie pliki certyfikatów i kluczy zostaną utworzone w tym katalogu.
  
3. Skopiuj pliki konfiguracyjne i skrypty ze sklonowanego repozytorium usługi IoT Edge do katalogu roboczego.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

<!--
4. Configure OpenSSL to generate certificates using the provided script. 

   ```bash
   chmod 700 certGen.sh 
   ```
-->

## <a name="create-root-ca-certificate"></a>Tworzenie głównego certyfikatu urzędu certyfikacji

Główny certyfikat urzędu certyfikacji jest używany do tworzenia wszystkich innych certyfikatów demonstracyjnych do testowania scenariusza IoT Edge.
Możesz nadal używać tego samego głównego certyfikatu urzędu certyfikacji, aby tworzyć certyfikaty demonstracyjne dla wielu urządzeń usługi IoT Edge lub niższego rzędu.

Jeśli w folderze roboczym znajduje się już jeden główny certyfikat urzędu certyfikacji, nie należy tworzyć nowego certyfikatu.
Nowy certyfikat głównego urzędu certyfikacji zastąpi stary, a wszystkie certyfikaty podrzędne wykonane ze starego przestaną działać.
Jeśli chcesz mieć wiele głównych certyfikatów urzędu certyfikacji, należy zarządzać nimi w oddzielnych folderach.

Przed przystąpieniem do czynności określonych w tej sekcji wykonaj czynności opisane w sekcji [Konfigurowanie skryptów,](#set-up-scripts) aby przygotować katalog roboczy ze skryptami generowania certyfikatów demonstracyjnych.

### <a name="windows"></a>Windows

1. Przejdź do katalogu roboczego, w którym zostały umieszczone skrypty generowania certyfikatów.

1. Utwórz główny certyfikat urzędu certyfikacji i załóż podpisanie jednego certyfikatu pośredniego. Wszystkie certyfikaty są umieszczane w katalogu roboczym.

   ```powershell
   New-CACertsCertChain rsa
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale gdy artykuły proszą o **certyfikat głównego urzędu certyfikacji,** użyj następującego pliku:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Przejdź do katalogu roboczego, w którym zostały umieszczone skrypty generowania certyfikatów.

1. Utwórz główny certyfikat urzędu certyfikacji i jeden certyfikat pośredni.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale gdy artykuły proszą o **certyfikat głównego urzędu certyfikacji,** użyj następującego pliku:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-ca-certificates"></a>Tworzenie certyfikatów urzędu certyfikacji urządzeń IoT Edge

Każde urządzenie usługi IoT Edge przechodzące do produkcji potrzebuje certyfikatu urzędu certyfikacji urządzenia, do którego odwołuje się plik config.yaml.
Certyfikat urzędu certyfikacji urządzenia jest odpowiedzialny za tworzenie certyfikatów dla modułów uruchomionych na urządzeniu.
Jest to również sposób, w jaki urządzenie Usługi IoT Edge weryfikuje swoją tożsamość podczas łączenia się z urządzeniami podrzędnymi.

Certyfikaty urzędu certyfikacji urządzenia są w sekcji **Certyfikat** pliku config.yaml na urządzeniu Usługi IoT Edge.

Przed przystąpieniem do czynności określonych w tej sekcji wykonaj czynności opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) i [Tworzenie certyfikatów głównego urzędu certyfikacji.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

1. Przejdź do katalogu roboczego zawierającego skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz certyfikat urzędu certyfikacji urządzenia usługi IoT Edge i klucz prywatny za pomocą następującego polecenia. Podaj nazwę certyfikatu urzędu certyfikacji, na przykład **MyEdgeDeviceCA**, który jest używany do nadawanie nazw plikom wyjściowym.

   ```powershell
   New-CACertsEdgeDevice "MyEdgeDeviceCA"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy. Następujący certyfikat i para kluczy muszą zostać skopiowane na urządzenie usługi IoT Edge i odwołuje się do pliku config.yaml:

   * `<WRKDIR>\certs\iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-MyEdgeDeviceCA.key.pem`

Nazwa urządzenia bramy przekazywana do tych skryptów nie powinna być taka sama jak parametr "nazwa hosta" w pliku config.yaml lub identyfikator urządzenia w centrum IoT Hub.
Skrypty pomagają uniknąć problemów, dołączając ciąg ".ca" do nazwy urządzenia bramy, aby zapobiec kolizji nazw w przypadku, gdy użytkownik konfiguruje usługę IoT Edge przy użyciu tej samej nazwy w obu miejscach.
Jednak jest dobrą praktyką, aby uniknąć używania tej samej nazwy.

### <a name="linux"></a>Linux

1. Przejdź do katalogu roboczego zawierającego skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz certyfikat urzędu certyfikacji urządzenia usługi IoT Edge i klucz prywatny za pomocą następującego polecenia. Podaj nazwę certyfikatu urzędu certyfikacji, na przykład **MyEdgeDeviceCA**, który jest używany do nadawanie nazw plikom wyjściowym.

   ```bash
   ./certGen.sh create_edge_device_certificate "MyEdgeDeviceCA"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy. Następujący certyfikat i para kluczy muszą zostać skopiowane na urządzenie usługi IoT Edge i odwołuje się do pliku config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-MyEdgeDeviceCA-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-MyEdgeDeviceCA.key.pem`

Nazwa urządzenia bramy przekazywana do tych skryptów nie powinna być taka sama jak parametr "nazwa hosta" w pliku config.yaml lub identyfikator urządzenia w centrum IoT Hub.
Skrypty pomagają uniknąć problemów, dołączając ciąg ".ca" do nazwy urządzenia bramy, aby zapobiec kolizji nazw w przypadku, gdy użytkownik konfiguruje usługę IoT Edge przy użyciu tej samej nazwy w obu miejscach.
Jednak jest dobrą praktyką, aby uniknąć używania tej samej nazwy.

## <a name="create-iot-edge-device-identity-certificates"></a>Tworzenie certyfikatów tożsamości urządzenia usługi IoT Edge

Certyfikaty tożsamości urządzeń są używane do aprowizowania urządzeń usługi Usługi [inicjowania obsługi administracyjnej usługi](../iot-dps/index.yml)usługi dps usługi usługi azure ioT Hub .

Certyfikaty tożsamości urządzenia można przejść w sekcji **Inicjowanie obsługi administracyjnej** pliku config.yaml na urządzeniu usługi IoT Edge.

Przed przystąpieniem do czynności określonych w tej sekcji wykonaj czynności opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) i [Tworzenie certyfikatów głównego urzędu certyfikacji.](#create-root-ca-certificate)

### <a name="windows"></a>Windows

Utwórz certyfikat tożsamości urządzenia usługi IoT Edge i klucz prywatny za pomocą następującego polecenia:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Nazwa przekazywalna do tego polecenia będzie identyfikatorem urządzenia usługi IoT Edge w centrum IoT Hub.

Nowe polecenie tożsamości urządzenia tworzy kilka plików certyfikatów i kluczy, w tym trzy, które będą używane podczas tworzenia indywidualnej rejestracji w dps i instalowania środowiska wykonawczego usługi IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Utwórz certyfikat tożsamości urządzenia usługi IoT Edge i klucz prywatny za pomocą następującego polecenia:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Nazwa przekazywalna do tego polecenia będzie identyfikatorem urządzenia usługi IoT Edge w centrum IoT Hub.

Skrypt tworzy kilka plików certyfikatów i kluczy, w tym trzy, które będą używane podczas tworzenia indywidualnej rejestracji w dps i instalowania środowiska wykonawczego IoT Edge:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-downstream-device-certificates"></a>Tworzenie certyfikatów urządzeń podrzędnych

Jeśli konfigurujesz podrzędne urządzenie IoT dla scenariusza bramy, możesz wygenerować certyfikaty demonstracyjne dla uwierzytelniania X.509.
Istnieją dwa sposoby uwierzytelniania urządzenia IoT przy użyciu certyfikatów X.509: przy użyciu certyfikatów z podpisem z podpisem z podpisem lub przy użyciu certyfikatów podpisanych przez urząd certyfikacji.There are two ways to authenticate an IoT device using X.509 certificates: using self-signed certs or using certificatets (Certificatets) signed certs.
W przypadku uwierzytelniania z podpisem własnym X.509, czasami nazywanego uwierzytelnianiem odcisków palców, należy utworzyć nowe certyfikaty do umieszczenia na urządzeniu IoT.
Te certyfikaty mają odcisk palca w nich, które można udostępnić w Centrum IoT do uwierzytelniania.
W przypadku uwierzytelniania podpisanego przez urząd certyfikacji X.509 (CA) potrzebny jest certyfikat głównego urzędu certyfikacji zarejestrowany w Centrum IoT Hub, który służy do podpisywania certyfikatów dla urządzenia IoT.
Każde urządzenie używające certyfikatu wystawionego przez główny certyfikat urzędu certyfikacji lub którykolwiek z jego certyfikatów pośrednich będzie mógł się uwierzytelnić.

Skrypty generowania certyfikatów mogą pomóc w testowaniu certyfikatów demonstracyjnych w celu przetestowania jednego z tych scenariuszy uwierzytelniania.

Przed przystąpieniem do czynności określonych w tej sekcji wykonaj czynności opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) i [Tworzenie certyfikatów głównego urzędu certyfikacji.](#create-root-ca-certificate)

### <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

Podczas uwierzytelniania urządzenia IoT z certyfikatami z podpisem własnym należy utworzyć certyfikaty urządzeń na podstawie certyfikatu głównego urzędu certyfikacji dla rozwiązania.
Następnie można pobrać szesnastkowy "odcisk palca" z certyfikatów, aby zapewnić usługi IoT Hub.
Urządzenie IoT wymaga również kopii certyfikatów urządzeń, aby można było uwierzytelnić się za pomocą usługi IoT Hub.

#### <a name="windows"></a>Windows

1. Przejdź do katalogu roboczego zawierającego skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz dwa certyfikaty (podstawowe i pomocnicze) dla urządzenia podrzędnego. Łatwa konwencja nazewnictwa służy do tworzenia certyfikatów z nazwą urządzenia IoT, a następnie etykiety podstawowej lub pomocniczej. Przykład:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i odwołuje się do aplikacji łączących się z Centrum IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Pobierz odcisk palca SHA1 (nazywany odcisk palca w kontekstach usługi IoT Hub) z każdego certyfikatu. Odcisk palca jest 40 szesnastkowym ciągiem znaków. Użyj następującego polecenia openssl, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Uruchom to polecenie dwa razy, raz dla certyfikatu podstawowego i raz dla certyfikatu pomocniczego. Odciski palców dla obu certyfikatów są poświęcene podczas rejestrowania nowego urządzenia IoT przy użyciu certyfikatów X.509 z podpisem własnym.

#### <a name="linux"></a>Linux

1. Przejdź do katalogu roboczego zawierającego skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz dwa certyfikaty (podstawowe i pomocnicze) dla urządzenia podrzędnego. Łatwa konwencja nazewnictwa służy do tworzenia certyfikatów z nazwą urządzenia IoT, a następnie etykiety podstawowej lub pomocniczej. Przykład:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i odwołuje się do aplikacji łączących się z Centrum IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Pobierz odcisk palca SHA1 (nazywany odcisk palca w kontekstach usługi IoT Hub) z każdego certyfikatu. Odcisk palca jest 40 szesnastkowym ciągiem znaków. Użyj następującego polecenia openssl, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Podczas rejestrowania nowego urządzenia IoT przy użyciu certyfikatów X.509 z podpisem podstawowym i pomocniczym można podać zarówno podstawowy, jak i pomocniczy odcisk palca.

### <a name="ca-signed-certificates"></a>Certyfikaty podpisane przez urząd certyfikacji

Podczas uwierzytelniania urządzenia IoT z certyfikatami z podpisem własnym należy przekazać certyfikat głównego urzędu certyfikacji dla rozwiązania do usługi IoT Hub.
Następnie należy przeprowadzić weryfikację, aby udowodnić centrum IoT Hub, że jesteś właścicielem certyfikatu głównego urzędu certyfikacji.
Na koniec używasz tego samego głównego certyfikatu urzędu certyfikacji do tworzenia certyfikatów urządzeń do umieszczenia na urządzeniu IoT, aby można było uwierzytelnić się za pomocą usługi IoT Hub.

Certyfikaty w tej sekcji są dla kroków w [Konfigurowanie zabezpieczeń X.509 w centrum Azure IoT hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Przekaż główny plik certyfikatu urzędu certyfikacji z katalogu `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`roboczego do centrum IoT hub.

2. Użyj kodu dostarczonego w witrynie Azure portal, aby sprawdzić, czy jesteś właścicielem tego głównego certyfikatu urzędu certyfikacji.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego identyfikatora urządzenia, w którego urządzenie jest zarejestrowane w U. IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i odwołuje się do aplikacji łączących się z Centrum IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Przekaż główny plik certyfikatu urzędu certyfikacji z katalogu `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`roboczego do centrum IoT hub.

2. Użyj kodu dostarczonego w witrynie Azure portal, aby sprawdzić, czy jesteś właścicielem tego głównego certyfikatu urzędu certyfikacji.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego identyfikatora urządzenia, w którego urządzenie jest zarejestrowane w U. IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i odwołuje się do aplikacji łączących się z Centrum IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
