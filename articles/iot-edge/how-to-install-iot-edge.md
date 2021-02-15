---
title: Zainstaluj Azure IoT Edge | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge na urządzeniach z systemem Windows lub Linux
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: kgremban
ms.openlocfilehash: efbae71162bdd0c126287191f7ad35cf903db138
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378081"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>Instalowanie lub odinstalowywanie Azure IoT Edge dla systemu Linux

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

## <a name="install-the-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń IoT Edge

Demon Security IoT Edge zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany przy każdym rozruchu i uruchamiania urządzenia przez uruchomienie pozostałej części środowiska uruchomieniowego IoT Edge.

Kroki opisane w tej sekcji przedstawiają typowy proces instalacji najnowszej wersji na urządzeniu z połączeniem internetowym. Jeśli musisz zainstalować określoną wersję, na przykład wersję wstępną lub zainstalować ją w trybie offline, wykonaj kroki [instalacji w trybie offline lub określonej wersji](#offline-or-specific-version-installation-optional) w następnej sekcji.

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

## <a name="provision-the-device-with-its-cloud-identity"></a>Inicjowanie obsługi administracyjnej urządzenia przy użyciu tożsamości w chmurze

Teraz, gdy aparat kontenera i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przejść do następnego kroku, czyli skonfigurować urządzenie przy użyciu tożsamości i informacji o uwierzytelnianiu w chmurze.

Wybierz następną sekcję w zależności od typu uwierzytelniania, którego chcesz użyć:

* [Opcja 1. uwierzytelnianie przy użyciu kluczy symetrycznych](#option-1-authenticate-with-symmetric-keys)
* [Opcja 2: uwierzytelnianie za pomocą certyfikatów X. 509](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>Opcja 1. uwierzytelnianie przy użyciu kluczy symetrycznych

Na tym etapie środowisko uruchomieniowe IoT Edge jest instalowane na urządzeniu z systemem Linux i należy udostępnić je swoje informacje o tożsamości i uwierzytelnianiu w chmurze.

W tej sekcji omówiono procedurę aprowizacji urządzenia z uwierzytelnianiem przy użyciu klucza symetrycznego. Urządzenie powinno zostać zarejestrowane w IoT Hub i pobrane parametry połączenia z informacji o urządzeniu. W przeciwnym razie wykonaj czynności opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Znajdź konfiguracje aprowizacji pliku i usuń znaczniki **ręcznej konfiguracji aprowizacji za pomocą sekcji parametrów połączenia** .

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

   ```bash
   sudo systemctl restart iotedge
   ```

### <a name="option-2-authenticate-with-x509-certificates"></a>Opcja 2: uwierzytelnianie za pomocą certyfikatów X. 509

Na tym etapie środowisko uruchomieniowe IoT Edge jest instalowane na urządzeniu z systemem Linux i należy udostępnić je swoje informacje o tożsamości i uwierzytelnianiu w chmurze.

W tej sekcji omówiono procedurę aprowizacji urządzenia za pomocą uwierzytelniania certyfikatu X. 509. Urządzenie powinno zostać zarejestrowane w IoT Hub, co zapewnia odciski palców zgodne z certyfikatem i kluczem prywatnym znajdującym się na urządzeniu IoT Edge. W przeciwnym razie wykonaj czynności opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).

Na urządzeniu IoT Edge Otwórz plik konfiguracji.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Znajdź sekcję konfiguracje aprowizacji pliku i usuń znaczniki **ręcznej konfiguracji inicjowania obsługi przy użyciu certyfikatu tożsamości X. 509** . Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
     dynamic_reprovisioning: false
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

## <a name="verify-successful-configuration"></a>Weryfikuj pomyślną konfigurację

Sprawdź, czy środowisko uruchomieniowe zostało pomyślnie zainstalowane i skonfigurowane na urządzeniu usługi IoT Edge.

1. Sprawdź, czy demon IoT Edge Security jest uruchomiony jako usługa systemowa.

   ```bash
   sudo systemctl status iotedge
   ```

   >[!TIP]
   >Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po wylogowaniu się z komputera i ponownym zalogowaniu się do niego po raz pierwszy od zainstalowania środowiska uruchomieniowego usługi IoT Edge Twoje uprawnienia zostaną automatycznie zaktualizowane. Do tego momentu należy użyć `sudo` przed poleceniami.

2. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

   ```bash
   journalctl -u iotedge
   ```

3. Użyj `check` Narzędzia, aby zweryfikować konfigurację i stan połączenia urządzenia.

   ```bash
   sudo iotedge check
   ```

   >[!TIP]
   >Zawsze używaj `sudo` , aby uruchomić narzędzie sprawdzania, nawet po zaktualizowaniu uprawnień. Narzędzie wymaga podniesionych uprawnień, aby uzyskać dostęp do pliku **config. YAML** w celu sprawdzenia stanu konfiguracji.

4. Wyświetl wszystkie moduły uruchomione na urządzeniu usługi IoT Edge. Gdy usługa jest uruchamiana po raz pierwszy, powinien być widoczny tylko uruchomiony moduł **edgeAgent** . Moduł edgeAgent jest domyślnie uruchamiany i ułatwia Instalowanie i uruchamianie wszelkich dodatkowych modułów wdrażanych na urządzeniu.

   ```bash
   sudo iotedge list
   ```

## <a name="offline-or-specific-version-installation-optional"></a>Instalacja w trybie offline lub określona wersja (opcjonalnie)

Kroki opisane w tej sekcji dotyczą scenariuszy nieuwzględnionych w standardowych krokach instalacji. Może to obejmować:

* Zainstaluj IoT Edge w trybie offline
* Zainstaluj wersję Release Candidate

Wykonaj kroki opisane w tej sekcji, jeśli chcesz zainstalować konkretną wersję środowiska uruchomieniowego Azure IoT Edge, która nie jest dostępna w programie `apt-get install` . Lista pakietów firmy Microsoft zawiera tylko ograniczony zestaw najnowszych wersji i ich wersje podrzędne, dlatego te kroki są przeznaczone dla wszystkich osób, które chcą zainstalować starszą wersję lub wersję programu Release Candidate.

Korzystając z poleceń zwinięcie, można kierować pliki składników bezpośrednio z repozytorium IoT Edge GitHub.

1. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa.

2. Rozwiń sekcję **zasobów** dla tej wersji.

3. Każde wydanie powinno mieć nowe pliki dla IoT Edge Security daemon i hsmlib. Użyj następujących poleceń, aby zaktualizować te składniki.

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

Teraz, gdy aparat kontenerów i środowisko uruchomieniowe IoT Edge są zainstalowane na urządzeniu, możesz przystąpić do następnego kroku, który polega na [udostępnieniu urządzenia w jego tożsamości w chmurze](#provision-the-device-with-its-cloud-identity).

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia, użyj następujących poleceń.

Usuń środowisko uruchomieniowe usługi IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

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
