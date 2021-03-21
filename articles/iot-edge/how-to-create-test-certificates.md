---
title: Tworzenie certyfikatów testowych — Azure IoT Edge | Microsoft Docs
description: Utwórz certyfikaty testowe i Dowiedz się, jak zainstalować je na urządzeniu Azure IoT Edge, aby przygotować się do wdrożenia produkcyjnego.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d8cf3dbe9d1dc2ad329a0b5ab8fa9554c85ae55c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199100"
---
# <a name="create-demo-certificates-to-test-iot-edge-device-features"></a>Tworzenie certyfikatów demonstracyjnych do testowania funkcji urządzenia usługi IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Urządzenia IoT Edge wymagają certyfikatów do bezpiecznej komunikacji między środowiskiem uruchomieniowym, modułami i wszystkimi urządzeniami podrzędnymi.
Jeśli nie masz urzędu certyfikacji, aby utworzyć wymagane certyfikaty, możesz użyć opcji certyfikaty demonstracyjne, aby wypróbować IoT Edge funkcje w środowisku testowym.
W tym artykule opisano funkcje skryptów generowania certyfikatów, które IoT Edge zapewnia do testowania.

Te certyfikaty wygasną za 30 dni i nie powinny być używane w żadnym scenariuszu produkcyjnym.

Możesz tworzyć certyfikaty na dowolnym komputerze, a następnie kopiować je na urządzenie IoT Edge.
Łatwiej jest używać komputera podstawowego do tworzenia certyfikatów zamiast generować je na urządzeniu IoT Edge.
Korzystając z komputera podstawowego, można skonfigurować skrypty jeden raz, a następnie użyć ich do tworzenia certyfikatów dla wielu urządzeń.

Wykonaj następujące kroki, aby utworzyć certyfikaty demonstracyjne na potrzeby testowania IoT Edge scenariuszu:

1. [Skonfiguruj skrypty](#set-up-scripts) na potrzeby generowania certyfikatów na urządzeniu.
2. [Utwórz certyfikat głównego urzędu certyfikacji](#create-root-ca-certificate) , za pomocą którego chcesz podpisać wszystkie inne certyfikaty w danym scenariuszu.
3. Wygeneruj certyfikaty, które są potrzebne dla scenariusza, który chcesz przetestować:
   * [Utwórz Certyfikaty tożsamości urządzeń IoT Edge](#create-iot-edge-device-identity-certificates) na potrzeby aprowizacji urządzeń przy użyciu uwierzytelniania certyfikatu X. 509 ręcznie lub przy użyciu IoT Hub Device Provisioning Service.
   * [Utwórz IoT Edge certyfikaty urzędu certyfikacji](#create-iot-edge-device-ca-certificates) dla urządzeń IoT Edge w scenariuszach bramy.
   * [Tworzenie certyfikatów urządzeń podrzędnych](#create-downstream-device-certificates) na potrzeby uwierzytelniania urządzeń podrzędnych w scenariuszu bramy.

## <a name="prerequisites"></a>Wymagania wstępne

Komputer deweloperski z zainstalowaną usługą git.

## <a name="set-up-scripts"></a>Konfigurowanie skryptów

Repozytorium IoT Edge w witrynie GitHub obejmuje skrypty generowania certyfikatów, których można użyć do tworzenia certyfikatów demonstracyjnych.
Ta sekcja zawiera instrukcje dotyczące przygotowania skryptów do uruchamiania na komputerze w systemie Windows lub Linux.
Jeśli pracujesz na komputerze z systemem Linux, przejdź do wcześniejszej [konfiguracji systemu Linux](#set-up-on-linux).

### <a name="set-up-on-windows"></a>Konfiguracja w systemie Windows

Aby utworzyć certyfikaty demonstracyjne na urządzeniu z systemem Windows, należy zainstalować OpenSSL, a następnie sklonować skrypty generacji i skonfigurować je do uruchamiania lokalnie w programie PowerShell.

#### <a name="install-openssl"></a>Zainstaluj OpenSSL

Zainstaluj program OpenSSL dla systemu Windows na komputerze, którego używasz do generowania certyfikatów.
Jeśli na urządzeniu z systemem Windows jest już zainstalowany program OpenSSL, upewnij się, że openssl.exe jest dostępny w zmiennej środowiskowej PATH.

Istnieje kilka sposobów instalacji programu OpenSSL, w tym następujące opcje:

* **Łatwiejsze:** Pobierz i zainstaluj wszystkie [pliki binarne OpenSSL innych](https://wiki.openssl.org/index.php/Binaries)firm, na przykład z [OpenSSL na sourceforge](https://sourceforge.net/projects/openssl/). Dodaj pełną ścieżkę do openssl.exe do zmiennej środowiskowej PATH.

* **Zalecane:** Pobierz kod źródłowy OpenSSL i Utwórz pliki binarne na komputerze przez siebie lub za pośrednictwem [vcpkg](https://github.com/Microsoft/vcpkg). Poniższe instrukcje wykorzystują vcpkg do pobierania kodu źródłowego, kompilowania i instalowania OpenSSL na komputerze z systemem Windows z prostymi krokami.

   1. Przejdź do katalogu, w którym chcesz zainstalować vcpkg. Postępuj zgodnie z instrukcjami, aby pobrać i zainstalować [vcpkg](https://github.com/Microsoft/vcpkg).

   2. Po zainstalowaniu vcpkg Uruchom następujące polecenie w wierszu polecenia programu PowerShell, aby zainstalować pakiet OpenSSL dla systemu Windows x64. Instalacja zazwyczaj trwa około 5 minut.

      ```powershell
      .\vcpkg install openssl:x64-windows
      ```

   3. Dodaj `<vcpkg path>\installed\x64-windows\tools\openssl` do zmiennej środowiskowej PATH, aby plik openssl.exe był dostępny do wywołania.

#### <a name="prepare-scripts-in-powershell"></a>Przygotowywanie skryptów w programie PowerShell

Repozytorium Azure IoT Edge git zawiera skrypty, których można użyć do generowania certyfikatów testowych.
W tej sekcji można sklonować repozytorium IoT Edge i wykonać skrypty.

1. Otwórz okno programu PowerShell w trybie administratora.

2. Sklonuj repozytorium IoT Edge git, które zawiera skrypty do generowania certyfikatów demonstracyjnych. Użyj `git clone` polecenia lub [Pobierz plik zip](https://github.com/Azure/iotedge/archive/master.zip).

   ```powershell
   git clone https://github.com/Azure/iotedge.git
   ```

3. Przejdź do katalogu, w którym chcesz korzystać. W tym artykule wywołamy ten katalog *\<WRKDIR>* . Wszystkie certyfikaty i klucze zostaną utworzone w tym katalogu roboczym.

4. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium do katalogu roboczego.

   ```powershell
   copy <path>\iotedge\tools\CACertificates\*.cnf .
   copy <path>\iotedge\tools\CACertificates\ca-certs.ps1 .
   ```

   Jeśli repozytorium zostało pobrane jako plik ZIP, nazwa folderu to, `iotedge-master` a reszta ścieżki jest taka sama.

5. Włącz program PowerShell, aby uruchamiał skrypty.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
   ```

6. Przenieś funkcje używane przez skrypty do globalnej przestrzeni nazw programu PowerShell.

   ```powershell
   . .\ca-certs.ps1
   ```

   W oknie programu PowerShell zostanie wyświetlone ostrzeżenie informujące o tym, że certyfikaty wygenerowane przez ten skrypt są przeznaczone tylko do celów testowych i nie powinny być używane w scenariuszach produkcyjnych.

7. Sprawdź, czy program OpenSSL został prawidłowo zainstalowany i upewnij się, że nie nastąpiły kolizje nazw z istniejącymi certyfikatami. Jeśli występują problemy, w danych wyjściowych skryptu powinna być opisany sposób naprawy w systemie.

   ```powershell
   Test-CACertsPrerequisites
   ```

### <a name="set-up-on-linux"></a>Konfiguracja w systemie Linux

Aby można było tworzyć certyfikaty demonstracyjne na urządzeniu z systemem Windows, należy sklonować skrypty generacji i skonfigurować je tak, aby uruchamiały się lokalnie w bash.

1. Sklonuj repozytorium IoT Edge git, które zawiera skrypty do generowania certyfikatów demonstracyjnych.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

2. Przejdź do katalogu, w którym chcesz korzystać. Odwołujemy się do tego katalogu w artykule jako *\<WRKDIR>* . Wszystkie pliki certyfikatów i kluczy zostaną utworzone w tym katalogu.
  
3. Skopiuj pliki konfiguracji i skryptów ze sklonowanego repozytorium IoT Edge do katalogu roboczego.

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

## <a name="create-root-ca-certificate"></a>Utwórz certyfikat głównego urzędu certyfikacji

Certyfikat głównego urzędu certyfikacji służy do wprowadzania wszystkich innych certyfikatów demonstracyjnych na potrzeby testowania IoT Edge scenariusz.
Możesz nadal używać tego samego certyfikatu głównego urzędu certyfikacji, aby wypróbować certyfikaty dla wielu IoT Edge lub urządzeń podrzędnych.

Jeśli masz już certyfikat głównego urzędu certyfikacji w folderze roboczym, nie twórz nowego.
Nowy certyfikat głównego urzędu certyfikacji nadpisze stary, a wszystkie certyfikaty podrzędne wykonane z starego, przestaną działać.
Jeśli chcesz mieć wiele certyfikatów głównego urzędu certyfikacji, pamiętaj, aby zarządzać nimi w oddzielnych folderach.

Przed przejściem do procedury opisanej w tej sekcji wykonaj kroki opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) , aby przygotować katalog roboczy z przykładowymi skryptami generowania certyfikatów.

### <a name="windows"></a>Windows

1. Przejdź do katalogu roboczego, w którym zostały umieszczone skrypty generowania certyfikatu.

1. Utwórz certyfikat głównego urzędu certyfikacji i podpisz jeden certyfikat pośredni. Wszystkie certyfikaty są umieszczane w katalogu roboczym.

   ```powershell
   New-CACertsCertChain rsa
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale gdy artykuły poproszą o **certyfikat głównego urzędu certyfikacji**, należy użyć następującego pliku:

   * `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem`

### <a name="linux"></a>Linux

1. Przejdź do katalogu roboczego, w którym zostały umieszczone skrypty generowania certyfikatu.

1. Utwórz certyfikat głównego urzędu certyfikacji i jeden certyfikat pośredni.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   To polecenie skryptu tworzy kilka plików certyfikatów i kluczy, ale gdy artykuły poproszą o **certyfikat głównego urzędu certyfikacji**, należy użyć następującego pliku:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

## <a name="create-iot-edge-device-identity-certificates"></a>Tworzenie certyfikatów tożsamości urządzeń IoT Edge

Certyfikaty tożsamości urządzeń służą do aprowizacji IoT Edge urządzeń, jeśli zdecydujesz się użyć uwierzytelniania certyfikatu X. 509. Te certyfikaty działają niezależnie od tego, czy jest używana ręczna obsługa administracyjna, czy Automatyczna obsługa administracyjna za pomocą usługi Azure IoT Hub Device Provisioning Service (DPS).

Certyfikaty tożsamości urządzeń przejdź do sekcji **Inicjowanie obsługi** pliku konfiguracyjnego na urządzeniu IoT Edge.

Przed przejściem do procedury opisanej w tej sekcji wykonaj kroki opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) i [Tworzenie certyfikatu głównego urzędu certyfikacji](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

Utwórz certyfikat tożsamości urządzenia IoT Edge i klucz prywatny przy użyciu następującego polecenia:

```powershell
New-CACertsEdgeDeviceIdentity "<name>"
```

Nazwa przekazana do tego polecenia będzie IDENTYFIKATORem urządzenia dla urządzenia IoT Edge w IoT Hub.

Nowe polecenie nowej tożsamości urządzenia tworzy kilka certyfikatów i plików kluczy, w tym trzy, które będą używane podczas tworzenia rejestracji indywidualnej w usłudze DPS i instalowania IoT Edge środowiska uruchomieniowego:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>\private\iot-edge-device-identity-<name>.key.pem`

### <a name="linux"></a>Linux

Utwórz certyfikat tożsamości urządzenia IoT Edge i klucz prywatny przy użyciu następującego polecenia:

```bash
./certGen.sh create_edge_device_identity_certificate "<name>"
```

Nazwa przekazana do tego polecenia będzie IDENTYFIKATORem urządzenia dla urządzenia IoT Edge w IoT Hub.

Skrypt tworzy kilka plików certyfikatów i kluczy, w tym trzy, które będą używane podczas tworzenia rejestracji indywidualnej w usłudze DPS i instalowania IoT Edge środowiska uruchomieniowego:

* `<WRKDIR>\certs\iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/certs/iot-edge-device-identity-<name>.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

## <a name="create-iot-edge-device-ca-certificates"></a>Tworzenie certyfikatów urzędu certyfikacji urządzenia IoT Edge

Każde urządzenie IoT Edge przechodzące do środowiska produkcyjnego wymaga certyfikatu urzędu certyfikacji urządzenia, do którego odwołuje się plik konfiguracyjny.
Certyfikat urzędu certyfikacji urządzenia jest odpowiedzialny za tworzenie certyfikatów dla modułów uruchomionych na urządzeniu.
Jest to również konieczne w scenariuszach związanych z bramą, ponieważ certyfikat urzędu certyfikacji urządzenia określa, jak urządzenie IoT Edge weryfikuje swoją tożsamość na urządzeniach podrzędnych.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Certyfikaty urzędu certyfikacji urządzenia przejdź do sekcji **certyfikat** pliku config. YAML na urządzeniu IoT Edge.
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Certyfikaty urzędu certyfikacji urządzenia przejdź do sekcji **graniczny urząd certyfikacji** pliku config. toml na urządzeniu IoT Edge.
:::moniker-end

Przed przejściem do procedury opisanej w tej sekcji wykonaj kroki opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) i [Tworzenie certyfikatu głównego urzędu certyfikacji](#create-root-ca-certificate) .

### <a name="windows"></a>Windows

1. Przejdź do katalogu roboczego, który ma skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz certyfikat i klucz prywatny urzędu certyfikacji urządzenia IoT Edge przy użyciu następującego polecenia. Podaj nazwę certyfikatu urzędu certyfikacji.

   ```powershell
   New-CACertsEdgeDevice "<CA cert name>"
   ```

   To polecenie umożliwia utworzenie kilku certyfikatów i plików kluczy. Poniższego certyfikatu i pary kluczy muszą zostać skopiowane do urządzenia IoT Edge, do którego odwołuje się plik konfiguracyjny:

   * `<WRKDIR>\certs\iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>\private\iot-edge-device-<CA cert name>.key.pem`

Nazwa przeniesiona do polecenia **New-CACertsEdgeDevice** nie powinna być taka sama jak parametr nazwy hosta w pliku konfiguracyjnym lub identyfikator urządzenia w IoT Hub.

### <a name="linux"></a>Linux

1. Przejdź do katalogu roboczego, który ma skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz certyfikat i klucz prywatny urzędu certyfikacji urządzenia IoT Edge przy użyciu następującego polecenia. Podaj nazwę certyfikatu urzędu certyfikacji.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "<CA cert name>"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy. Poniższego certyfikatu i pary kluczy muszą zostać skopiowane do urządzenia IoT Edge, do którego odwołuje się plik konfiguracyjny:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Nazwa przenoszona do polecenia **create_edge_device_ca_certificate** nie powinna być taka sama jak parametr nazwy hosta w pliku konfiguracyjnym lub identyfikator urządzenia w IoT Hub.

## <a name="create-downstream-device-certificates"></a>Tworzenie certyfikatów urządzeń podrzędnych

W przypadku konfigurowania podrzędnego urządzenia IoT dla scenariusza bramy i używania uwierzytelniania X. 509 można generować certyfikaty demonstracyjne dla urządzenia podrzędnego.
Jeśli chcesz użyć uwierzytelniania przy użyciu klucza symetrycznego, nie musisz tworzyć dodatkowych certyfikatów dla urządzenia podrzędnego.
Istnieją dwa sposoby uwierzytelniania urządzenia IoT przy użyciu certyfikatów X. 509: przy użyciu certyfikatów z podpisem własnym lub certyfikatów podpisanych przez urząd certyfikacji.
W przypadku uwierzytelniania z podpisem własnym X. 509, czasami określanego jako Uwierzytelnianie odciskiem palca, należy utworzyć nowe certyfikaty do umieszczenia na urządzeniu IoT.
Te certyfikaty mają odcisk palca, które udostępniają IoT Hub na potrzeby uwierzytelniania.
W przypadku uwierzytelniania podpisanego za pomocą certyfikatu X. 509 urząd certyfikacji wymagany jest certyfikat głównego urzędu certyfikacji zarejestrowany w IoT Hub używany do podpisywania certyfikatów dla urządzenia IoT.
Uwierzytelnianie może zostać wykonane przy użyciu dowolnego urządzenia, które zostało wystawione przez certyfikat głównego urzędu certyfikacji lub dowolny z jego certyfikatów pośrednich.

Skrypty generowania certyfikatów mogą pomóc w wprowadzeniu certyfikatów demonstracyjnych do testowania jednego z tych scenariuszy uwierzytelniania.

Przed przejściem do procedury opisanej w tej sekcji wykonaj kroki opisane w sekcji [Konfigurowanie skryptów](#set-up-scripts) i [Tworzenie certyfikatu głównego urzędu certyfikacji](#create-root-ca-certificate) .

### <a name="self-signed-certificates"></a>Certyfikaty z podpisem własnym

Podczas uwierzytelniania urządzenia IoT przy użyciu certyfikatów z podpisem własnym należy utworzyć certyfikaty urządzeń na podstawie certyfikatu głównego urzędu certyfikacji dla rozwiązania.
Następnie można pobrać szesnastkowe "odcisk palca" z certyfikatów, aby zapewnić IoT Hub.
Urządzenie IoT wymaga również kopii certyfikatów urządzeń, aby można było uwierzytelnić się za pomocą IoT Hub.

#### <a name="windows"></a>Windows

1. Przejdź do katalogu roboczego, który ma skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz dwa certyfikaty (podstawowe i pomocnicze) dla urządzenia podrzędnego. Łatwą konwencją nazewnictwa jest utworzenie certyfikatów o nazwie urządzenia IoT, a następnie podstawowej lub pomocniczej etykiety. Na przykład:

   ```PowerShell
   New-CACertsDevice "<device name>-primary"
   New-CACertsDevice "<device name>-secondary"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i przywoływane w aplikacjach łączących się z IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>\private\iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>\private\iot-device-<device name>-secondary.key.pem`

3. Pobierz odcisk palca SHA1 (nazywany odciskiem palca w kontekście IoT Hub) z każdego certyfikatu. Odcisk palca to 40 ciąg znaków szesnastkowych. Użyj następującego polecenia OpenSSL, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```PowerShell
   openssl x509 -in <WRKDIR>\certs\iot-device-<device name>-primary.cert.pem -text -fingerprint
   ```

   Uruchom to polecenie dwukrotnie, raz dla certyfikatu podstawowego i jeden raz dla certyfikatu pomocniczego. Podczas rejestrowania nowego urządzenia IoT za pomocą certyfikatów X. 509 z podpisem własnym można podawać odciski palców dla obu certyfikatów.

#### <a name="linux"></a>Linux

1. Przejdź do katalogu roboczego, który ma skrypty generowania certyfikatów i certyfikat głównego urzędu certyfikacji.

2. Utwórz dwa certyfikaty (podstawowe i pomocnicze) dla urządzenia podrzędnego. Łatwą konwencją nazewnictwa jest utworzenie certyfikatów o nazwie urządzenia IoT, a następnie podstawowej lub pomocniczej etykiety. Na przykład:

   ```bash
   ./certGen.sh create_device_certificate "<device name>-primary"
   ./certGen.sh create_device_certificate "<device name>-secondary"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i przywoływane w aplikacjach łączących się z IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device name>-primary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary-full-chain.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device name>-primary.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device name>-secondary.cert.pfx`
   * `<WRKDIR>/private/iot-device-<device name>-primary.key.pem`
   * `<WRKDIR>/private/iot-device-<device name>-secondary.key.pem`

3. Pobierz odcisk palca SHA1 (nazywany odciskiem palca w kontekście IoT Hub) z każdego certyfikatu. Odcisk palca to 40 ciąg znaków szesnastkowych. Użyj następującego polecenia OpenSSL, aby wyświetlić certyfikat i znaleźć odcisk palca:

   ```bash
   openssl x509 -in <WRKDIR>/certs/iot-device-<device name>-primary.cert.pem -text -fingerprint | sed 's/[:]//g'
   ```

   Podczas rejestrowania nowego urządzenia IoT za pomocą certyfikatów X. 509 z podpisem własnym należy podać zarówno podstawowy, jak i pomocniczy odcisk palca.

### <a name="ca-signed-certificates"></a>Certyfikaty podpisane przez urząd certyfikacji

Podczas uwierzytelniania urządzenia IoT przy użyciu certyfikatów z podpisem własnym należy przekazać certyfikat głównego urzędu certyfikacji dla rozwiązania do IoT Hub.
Następnie należy przeprowadzić weryfikację, aby potwierdzić, IoT Hub posiadania certyfikatu głównego urzędu certyfikacji.
Na koniec Użyj tego samego certyfikatu głównego urzędu certyfikacji, aby utworzyć certyfikaty urządzenia do umieszczenia na urządzeniu IoT, aby można było uwierzytelnić się za pomocą IoT Hub.

Certyfikaty w tej sekcji dotyczą kroków [konfigurowania zabezpieczeń X. 509 w usłudze Azure IoT Hub](../iot-hub/iot-hub-security-x509-get-started.md).

#### <a name="windows"></a>Windows

1. Przekaż plik certyfikatu głównego urzędu certyfikacji z katalogu roboczego `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` do centrum IoT.

2. Użyj kodu podanego w Azure Portal, aby sprawdzić, czy jesteś własnym certyfikatem głównego urzędu certyfikacji.

   ```PowerShell
   New-CACertsVerificationCert "<verification code>"
   ```

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego identyfikatora urządzenia, w ramach którego zarejestrowano urządzenie w IoT Hub.

   ```PowerShell
   New-CACertsDevice "<device id>"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i przywoływane w aplikacjach łączących się z IoT Hub:

   * `<WRKDIR>\certs\iot-device-<device id>.cert.pem`
   * `<WRKDIR>\certs\iot-device-<device id>.cert.pfx`
   * `<WRKDIR>\certs\iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>\private\iot-device-<device id>.key.pem`

#### <a name="linux"></a>Linux

1. Przekaż plik certyfikatu głównego urzędu certyfikacji z katalogu roboczego `<WRKDIR>\certs\azure-iot-test-only.root.ca.cert.pem` do centrum IoT.

2. Użyj kodu podanego w Azure Portal, aby sprawdzić, czy jesteś własnym certyfikatem głównego urzędu certyfikacji.

   ```bash
   ./certGen.sh create_verification_certificate "<verification code>"
   ```

3. Utwórz łańcuch certyfikatów dla urządzenia podrzędnego. Użyj tego samego identyfikatora urządzenia, w ramach którego zarejestrowano urządzenie w IoT Hub.

   ```bash
   ./certGen.sh create_device_certificate "<device id>"
   ```

   To polecenie skryptu tworzy kilka certyfikatów i plików kluczy. Następujące pary certyfikatów i kluczy muszą zostać skopiowane do podrzędnego urządzenia IoT i przywoływane w aplikacjach łączących się z IoT Hub:

   * `<WRKDIR>/certs/iot-device-<device id>.cert.pem`
   * `<WRKDIR>/certs/iot-device-<device id>.cert.pfx`
   * `<WRKDIR>/certs/iot-device-<device id>-full-chain.cert.pem`  
   * `<WRKDIR>/private/iot-device-<device id>.key.pem`
