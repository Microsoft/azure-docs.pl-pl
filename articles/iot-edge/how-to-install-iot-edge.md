---
title: Zainstaluj Azure IoT Edge | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge na urządzeniach z systemem Windows lub Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: kgremban
ms.openlocfilehash: 6a64bb2801830440dc49e72786c9c00a6e4796b3
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201621"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge na urządzeniach z systemem Linux.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zarejestrowany identyfikator urządzenia](how-to-register-device.md)

  Jeśli urządzenie zostało zarejestrowane z uwierzytelnianiem przy użyciu klucza symetrycznego, przygotuj parametry połączenia urządzenia.

  Jeśli urządzenie zostało zarejestrowane z uwierzytelnianiem za pomocą certyfikatu X. 509 z podpisem własnym, mają co najmniej jeden certyfikat tożsamości, który został użyty do zarejestrowania urządzenia i jego zgodnego klucza prywatnego dostępnego na urządzeniu.

* Urządzenie z systemem Linux

  Posiadanie urządzenia z systemem Linux lub x64, ARM32 lub ARM64. Firma Microsoft udostępnia pakiety instalacyjne systemów operacyjnych Ubuntu Server 18,04 i Raspberry Pi w systemie operacyjnym.

  Najnowsze informacje o tym, które systemy operacyjne są obecnie obsługiwane w scenariuszach produkcyjnych, można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md#operating-systems)

  >[!NOTE]
  >Obsługa urządzeń z ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* Przygotuj urządzenie w celu uzyskania dostępu do pakietów instalacyjnych firmy Microsoft.

  Zainstaluj konfigurację repozytorium zgodną z systemem operacyjnym urządzenia.

  * **Ubuntu Server 18,04**:

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Rozciąganie systemu operacyjnego Raspberry Pi**:

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  Skopiuj wytworzoną listę do katalogu sources. list. d.

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  Zainstaluj klucz publiczny programu Microsoft GPG.

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge pakiety oprogramowania podlegają postanowieniom licencyjnym znajdującym się w każdym pakiecie ( `usr/share/doc/{package-name}` lub `LICENSE` katalogu). Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj tego pakietu.

## <a name="install-a-container-engine"></a>Instalowanie aparatu kontenera

Azure IoT Edge opiera się na zgodnym ze STEROWNIKIem środowiska uruchomieniowego kontenera. W przypadku scenariuszy produkcyjnych zaleca się użycie aparatu Moby. Aparat Moby jest jedynym aparatem kontenera oficjalnie obsługiwanym przez Azure IoT Edge. Obrazy kontenerów platformy Docker CE/EE są zgodne ze środowiskiem uruchomieniowym Moby.

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zainstaluj aparat Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

W przypadku wystąpienia błędów podczas instalowania aparatu kontenera Moby należy zweryfikować jądro systemu Linux pod kątem zgodności Moby. Niektórzy producenci urządzeń osadzonych dostarczają obrazy urządzeń, które zawierają niestandardowe jądra systemu Linux bez funkcji wymaganych do zapewnienia zgodności aparatu kontenera. Uruchom następujące polecenie, które używa [skryptu Check-config](https://github.com/moby/moby/blob/master/contrib/check-config.sh) dostarczonego przez Moby, aby sprawdzić konfigurację jądra:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

W danych wyjściowych skryptu Sprawdź, czy wszystkie elementy w obszarze `Generally Necessary` i `Network Drivers` są włączone. Jeśli nie masz żadnych funkcji, włącz je przez przebudowywanie jądra ze źródła i wybranie skojarzonych modułów do uwzględnienia w odpowiednim pliku kernel. config. Podobnie, jeśli używasz generatora konfiguracji jądra, takiego jak `defconfig` lub `menuconfig` , Znajdź i Włącz odpowiednie funkcje i odpowiednio Skompiluj jądro. Po wdrożeniu nowo zmodyfikowanego jądra ponownie uruchom skrypt Check-config, aby sprawdzić, czy wszystkie wymagane funkcje zostały pomyślnie włączone.

## <a name="install-iot-edge"></a>Zainstaluj IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Demon Security IoT Edge zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany przy każdym rozruchu i uruchamiania urządzenia przez uruchomienie pozostałej części środowiska uruchomieniowego IoT Edge.

Kroki opisane w tej sekcji przedstawiają typowy proces instalacji najnowszej wersji na urządzeniu z połączeniem internetowym. Jeśli musisz zainstalować określoną wersję, na przykład wersję wstępną lub zainstalować ją w trybie offline, wykonaj kroki [instalacji w trybie offline lub określonej wersji](#offline-or-specific-version-installation-optional) w dalszej części tego artykułu.

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zaznacz, aby zobaczyć, które wersje IoT Edge są dostępne.

   ```bash
   apt list -a iotedge
   ```

Jeśli chcesz zainstalować najnowszą wersję demona zabezpieczeń, użyj następującego polecenia, które powoduje także zainstalowanie najnowszej wersji pakietu **libiothsm-STD** :

   ```bash
   sudo apt-get install iotedge
   ```

Lub, jeśli chcesz zainstalować określoną wersję demona zabezpieczeń, określ wersję z listy apt danych wyjściowych. Należy również określić tę samą wersję dla pakietu **libiothsm-STD** , który w przeciwnym razie zainstaluje jego najnowszą wersję. Na przykład następujące polecenie instaluje najnowszą wersję wersji 1.0.10:

   ```bash
   sudo apt-get install iotedge=1.0.10* libiothsm-std=1.0.10*
   ```

Jeśli wersja, którą chcesz zainstalować, nie znajduje się na liście, postępuj zgodnie z krokami [instalacji w trybie offline lub określonym wersją](#offline-or-specific-version-installation-optional) w dalszej części tego artykułu. W tej sekcji pokazano, jak określić docelową poprzednią wersję demona IoT Edge Security lub wersja Release Candidate.

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Usługa IoT Edge zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Usługa jest uruchamiana przy każdym rozruchu i uruchamiania na urządzeniu przez uruchomienie pozostałej części środowiska uruchomieniowego IoT Edge.

Usługa tożsamość IoT została wprowadzona wraz z wersją 1,2 IoT Edge. Ta usługa obsługuje obsługę administracyjną i zarządzanie tożsamościami dla IoT Edge i dla innych składników urządzenia, które muszą komunikować się z IoT Hub.

Kroki opisane w tej sekcji przedstawiają typowy proces instalacji najnowszej wersji na urządzeniu z połączeniem internetowym. Jeśli musisz zainstalować określoną wersję, na przykład wersję wstępną lub zainstalować ją w trybie offline, wykonaj kroki [instalacji w trybie offline lub określonej wersji](#offline-or-specific-version-installation-optional) w dalszej części tego artykułu.

>[!NOTE]
>Kroki opisane w tej sekcji pokazują, jak zainstalować program IoT Edge w wersji 1,2, która jest obecnie dostępna w publicznej wersji zapoznawczej. Jeśli szukasz kroków instalacji najnowszej ogólnie dostępnej wersji IoT Edge, zobacz wersję [1,1 (LTS)](?view=iotedge-2018-06&preserve-view=true) tego artykułu.
>
>Jeśli masz już urządzenie IoT Edge działające w starszej wersji i chcesz przeprowadzić uaktualnienie do wersji 1,2, wykonaj kroki opisane w temacie [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md). Wersja 1,2 jest wystarczająco różna od poprzednich wersji IoT Edge, które są niezbędne do uaktualnienia.

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zaznacz, aby zobaczyć, które wersje IoT Edge są dostępne.

   ```bash
   apt list -a aziot-edge
   ```

Jeśli chcesz zainstalować najnowszą wersję IoT Edge, użyj następującego polecenia, które powoduje także zainstalowanie najnowszej wersji pakietu usługi tożsamości:

   ```bash
   sudo apt-get install aziot-edge
   ```

<!-- commenting out for public preview. reintroduce at GA

Or, if you want to install a specific version of IoT Edge and the identity service, specify the versions from the apt list output. Specify the same versions for both services.. For example, the following command installs the most recent version of the 1.2 release:

   ```bash
   sudo apt-get install aziot-edge=1.2* aziot-identity-service=1.2*
   ```

-->

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>Inicjowanie obsługi administracyjnej urządzenia przy użyciu tożsamości w chmurze

Teraz, gdy aparat kontenera i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przejść do następnego kroku, czyli skonfigurować urządzenie przy użyciu tożsamości i informacji o uwierzytelnianiu w chmurze.

Wybierz następną sekcję w zależności od typu uwierzytelniania, którego chcesz użyć:

* [Opcja 1. uwierzytelnianie przy użyciu kluczy symetrycznych](#option-1-authenticate-with-symmetric-keys)
* [Opcja 2: uwierzytelnianie za pomocą certyfikatów X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opcja 1. uwierzytelnianie przy użyciu kluczy symetrycznych

Na tym etapie środowisko uruchomieniowe IoT Edge jest instalowane na urządzeniu z systemem Linux i należy udostępnić je swoje informacje o tożsamości i uwierzytelnianiu w chmurze.

W tej sekcji omówiono procedurę aprowizacji urządzenia z uwierzytelnianiem przy użyciu klucza symetrycznego. Urządzenie powinno zostać zarejestrowane w IoT Hub i pobrane parametry połączenia z informacji o urządzeniu. W przeciwnym razie wykonaj czynności opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Znajdź konfiguracje aprowizacji pliku i usuń znaczniki **ręcznej konfiguracji inicjowania obsługi przy użyciu sekcji parametrów połączenia** , jeśli nie została jeszcze odkomentowana.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Utwórz plik konfiguracji dla urządzenia na podstawie pliku szablonu, który jest dostarczany jako część instalacji IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Znajdź sekcję **aprowizacji** pliku i Usuń komentarz z ręcznej aprowizacji przy użyciu wierszy parametrów połączenia.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

Zaktualizuj wartość **connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge.

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji Zastosuj zmiany:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>Opcja 2: uwierzytelnianie za pomocą certyfikatów X. 509

Na tym etapie środowisko uruchomieniowe IoT Edge jest instalowane na urządzeniu z systemem Linux i należy udostępnić je swoje informacje o tożsamości i uwierzytelnianiu w chmurze.

W tej sekcji omówiono procedurę aprowizacji urządzenia za pomocą uwierzytelniania certyfikatu X. 509. Urządzenie powinno zostać zarejestrowane w IoT Hub, co zapewnia odciski palców zgodne z certyfikatem i kluczem prywatnym znajdującym się na urządzeniu IoT Edge. W przeciwnym razie wykonaj czynności opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Znajdź sekcję konfiguracje aprowizacji pliku i usuń znaczniki **ręcznej konfiguracji inicjowania obsługi przy użyciu certyfikatu tożsamości X. 509** . Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Zaktualizuj następujące pola:

* **iothub_hostname**: Nazwa hosta Centrum IoT, z którym zostanie nawiązane połączenie. Na przykład `{IoT hub name}.azure-devices.net`.
* **device_ID**: identyfikator podany podczas rejestrowania urządzenia.
* **identity_cert**: identyfikator URI do certyfikatu tożsamości na urządzeniu. Na przykład `file:///path/identity_certificate.pem`.
* **identity_pk**: identyfikator URI pliku klucza prywatnego dla podanego certyfikatu tożsamości. Na przykład `file:///path/identity_key.pem`.

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

Utwórz plik konfiguracji dla urządzenia na podstawie pliku szablonu, który jest dostarczany jako część instalacji IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

Znajdź sekcję **aprowizacji** pliku i usuń znaczniki komentarza z wierszy do ręcznego inicjowania obsługi przy użyciu certyfikatu tożsamości X. 509. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze.

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

Zaktualizuj następujące pola:

* **iothub_hostname**: Nazwa hosta Centrum IoT, z którym zostanie nawiązane połączenie. Na przykład `{IoT hub name}.azure-devices.net`.
* **device_ID**: identyfikator podany podczas rejestrowania urządzenia.
* **identity_cert**: identyfikator URI do certyfikatu tożsamości na urządzeniu, na przykład: `file:///path/identity_certificate.pem` . Lub dynamicznie wystawić certyfikat przy użyciu narzędzia EST lub lokalnego urzędu certyfikacji.
* **identity_pk**: identyfikator URI pliku klucza prywatnego dla podanego certyfikatu tożsamości, na przykład: `file:///path/identity_key.pem` . Lub podaj identyfikator URI PKCS # 11, a następnie podaj informacje konfiguracyjne w sekcji **PKCS # 11** później w pliku konfiguracyjnym.

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji Zastosuj zmiany:

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>Weryfikuj pomyślną konfigurację

Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane na urządzeniu usługi IoT Edge.

>[!TIP]
>Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Do tego momentu należy użyć `sudo` przed poleceniami.

Sprawdź, czy usługa systemowa IoT Edge jest uruchomiona.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

::: moniker-end

Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

Użyj `check` Narzędzia, aby zweryfikować konfigurację i stan połączenia urządzenia.

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>Zawsze używaj `sudo` , aby uruchomić narzędzie sprawdzania, nawet po zaktualizowaniu uprawnień. Narzędzie wymaga podniesionych uprawnień, aby uzyskać dostęp do pliku konfiguracji w celu sprawdzenia stanu konfiguracji.

Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Gdy usługa jest uruchamiana po raz pierwszy, powinien być widoczny tylko uruchomiony moduł **edgeAgent** . Moduł edgeAgent jest domyślnie uruchamiany i ułatwia Instalowanie i uruchamianie wszelkich dodatkowych modułów wdrażanych na urządzeniu.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Instalacja w trybie offline lub określona wersja (opcjonalnie)

Kroki opisane w tej sekcji dotyczą scenariuszy nieuwzględnionych w standardowych krokach instalacji. Może to obejmować:

* Zainstaluj IoT Edge w trybie offline
* Zainstaluj wersję Release Candidate

Wykonaj kroki opisane w tej sekcji, jeśli chcesz zainstalować konkretną wersję środowiska uruchomieniowego Azure IoT Edge, która nie jest dostępna w programie `apt-get install` . Lista pakietów firmy Microsoft zawiera tylko ograniczony zestaw najnowszych wersji i ich wersje podrzędne, dlatego te kroki są przeznaczone dla wszystkich osób, które chcą zainstalować starszą wersję lub wersję programu Release Candidate.

Korzystając z poleceń zwinięcie, można kierować pliki składników bezpośrednio z repozytorium IoT Edge GitHub.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa.

2. Rozwiń sekcję **zasobów** dla tej wersji.

3. Każde wydanie powinno mieć nowe pliki dla IoT Edge Security daemon i hsmlib. Jeśli zamierzasz zainstalować IoT Edge na urządzeniu w trybie offline, Pobierz te pliki przed czasem. W przeciwnym razie użyj następujących poleceń, aby zaktualizować te składniki.

   1. Znajdź plik **libiothsm-STD** zgodny z architekturą urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   2. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. Znajdź plik **iotedge** , który jest zgodny z architekturą urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   4. Aby zainstalować tę wersję demona zabezpieczeń IoT Edge, użyj skopiowanego linku w poniższym poleceniu.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>Jeśli urządzenie jest aktualnie uruchomione IoT Edge wersji 1,1 lub starszej, Odinstaluj pakiety **iotedge** i **libiothsm-STD** przed wykonaniem kroków opisanych w tej sekcji. Aby uzyskać więcej informacji, zobacz [Aktualizacja z 1,0 lub 1,1 do 1,2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12).

1. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa.

2. Rozwiń sekcję **zasobów** dla tej wersji.

3. Każde wydanie powinno mieć nowe pliki dla IoT Edge i usługi tożsamości. Jeśli zamierzasz zainstalować IoT Edge na urządzeniu w trybie offline, Pobierz te pliki przed czasem. W przeciwnym razie użyj następujących poleceń, aby zaktualizować te składniki.

   1. Znajdź plik **aziot-Identity-Service** pasujący do architektury urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   2. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję usługi tożsamości:

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo dpkg -i ./aziot-identity-service.deb
      ```

   3. Znajdź plik **aziot-Edge** pasujący do architektury urządzenia IoT Edge. Kliknij prawym przyciskiem myszy link do pliku i skopiuj adres łącza.

   4. Użyj skopiowanego linku w poniższym poleceniu, aby zainstalować tę wersję programu IoT Edge.

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo dpkg -i ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

Teraz, gdy aparat kontenerów i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przystąpić do następnego kroku, który polega na [udostępnieniu urządzenia w jego tożsamości w chmurze](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia, użyj następujących poleceń.

Usuń środowisko uruchomieniowe usługi IoT Edge.

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

Użyj `--purge` flagi, aby usunąć wszystkie pliki skojarzone z IoT Edge, w tym pliki konfiguracji. Pozostaw tę flagę, jeśli chcesz ponownie zainstalować IoT Edge i korzystać z tych samych informacji konfiguracyjnych w przyszłości.

Po usunięciu środowiska uruchomieniowego IoT Edge wszystkie utworzone kontenery zostaną zatrzymane, ale nadal istnieją na urządzeniu. Wyświetl wszystkie kontenery, aby zobaczyć, które pozostaną.

```bash
sudo docker ps -a
```

Usuń kontenery z urządzenia, w tym dwa kontenery środowiska uruchomieniowego.

```bash
sudo docker rm -f <container name>
```

Na koniec Usuń środowisko uruchomieniowe kontenera z urządzenia.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Następne kroki

Kontynuuj [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md) , aby dowiedzieć się, jak wdrażać moduły na urządzeniu.
