---
title: Instalowanie Azure IoT Edge w systemie Linux | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge na urządzeniach z systemem Linux lub Ubuntu lub raspbian
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: kgremban
ms.openlocfilehash: 21fde76dc5791030a7afa280e00642119cbe464c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89660033"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Install the Azure IoT Edge runtime on Debian-based Linux systems (Instalowanie środowiska uruchomieniowego usługi IoT Edge w systemach Linux opartych na rozwiązaniu Debian)

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach jako niewielkich jako Raspberry Pi lub jako serwer przemysłowy. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

W tym artykule przedstawiono procedurę instalowania środowiska uruchomieniowego Azure IoT Edge na urządzeniu z systemem x64, ARM32 lub ARM64. Udostępniamy pakiety instalacyjne serwera Ubuntu Server 16,04, Ubuntu Server 18,04 i raspbian. Listę obsługiwanych systemów operacyjnych i architektur systemu Linux można znaleźć w [Azure IoT Edge obsługiwanych systemach](support.md#operating-systems) .

> [!NOTE]
> Pakiety w repozytoriach oprogramowania systemu Linux podlegają postanowieniom licencyjnym znajdującym się w każdym pakiecie (/usr/share/doc/*package-name*). Przeczytaj postanowienia licencyjne przed użyciem pakietu. Instalacja i użycie pakietu oznacza akceptację tych warunków. Jeśli nie akceptujesz postanowień licencyjnych, nie używaj pakietu.

## <a name="install-container-runtime-and-iot-edge"></a>Zainstaluj środowisko uruchomieniowe kontenera i IoT Edge

Skorzystaj z poniższych sekcji, aby zainstalować najnowszą wersję środowiska uruchomieniowego Azure IoT Edge na urządzeniu.

>[!NOTE]
>Obsługa urządzeń z ARM64 jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="register-microsoft-key-and-software-repository-feed"></a>Rejestrowanie kanału informacyjnego repozytorium i oprogramowania firmy Microsoft

Przygotuj urządzenie do instalacji środowiska uruchomieniowego IoT Edge.

Zainstaluj konfigurację repozytorium. Wybierz polecenie **16,04** lub **18,04** , które odpowiada systemowi operacyjnemu urządzenia:

* **Ubuntu Server 16,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18,04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Rozciągnij raspbian**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Skopiuj wygenerowaną listę.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Zainstaluj klucz publiczny programu Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>Instalowanie środowiska uruchomieniowego kontenera

Azure IoT Edge opiera się na [zgodnym ze sterownikiem](https://www.opencontainers.org/) środowiska uruchomieniowego kontenera. W przypadku scenariuszy produkcyjnych zaleca się użycie aparatu [opartego na Moby](https://mobyproject.org/) poniżej. Aparat Moby jest jedynym aparatem kontenera oficjalnie obsługiwanym przez Azure IoT Edge. Obrazy kontenerów platformy Docker CE/EE są zgodne ze środowiskiem uruchomieniowym Moby.

Aktualizowanie list pakietów na urządzeniu.

   ```bash
   sudo apt-get update
   ```

Zainstaluj aparat Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

Zainstaluj interfejs wiersza polecenia Moby (CLI). Interfejs wiersza polecenia jest przydatny do programowania, ale opcjonalny dla wdrożeń produkcyjnych.

   ```bash
   sudo apt-get install moby-cli
   ```

Jeśli występują błędy podczas instalowania środowiska uruchomieniowego kontenera Moby, wykonaj kroki opisane w dalszej części tego artykułu, aby [zweryfikować jądro systemu Linux pod kątem zgodności Moby](#verify-your-linux-kernel-for-moby-compatibility).

### <a name="install-the-azure-iot-edge-security-daemon"></a>Instalowanie demona zabezpieczeń Azure IoT Edge

**Demon security IoT Edge** zapewnia i utrzymuje standardy zabezpieczeń na urządzeniu IoT Edge. Demon jest uruchamiany przy każdym rozruchu i uruchamiania urządzenia przez uruchomienie pozostałej części środowiska uruchomieniowego IoT Edge.

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

Jeśli chcesz zainstalować określoną wersję demona zabezpieczeń, określ wersję z listy apt dane wyjściowe. Należy również określić tę samą wersję dla pakietu **libiothsm-STD** , który w przeciwnym razie zainstaluje jego najnowszą wersję. Na przykład następujące polecenie instaluje najnowszą wersję wersji 1.0.8:

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

Jeśli wersja, którą chcesz zainstalować, nie znajduje się na liście, wykonaj kroki opisane w temacie [Instalowanie środowiska uruchomieniowego przy użyciu zasobów wydania](#install-runtime-using-release-assets). W tej sekcji pokazano, jak określić docelową poprzednią wersję demona IoT Edge Security lub wersja Release Candidate.

Po pomyślnym zainstalowaniu programu IoT Edge w programie `/etc/iotedge/` wyjściowym zostanie wyświetlony komunikat z prośbą o zaktualizowanie pliku konfiguracji. Przejdź do następnej sekcji, aby zakończyć Inicjowanie obsługi urządzeń.

## <a name="configure-the-security-daemon"></a>Konfigurowanie demona zabezpieczeń

Skonfiguruj środowisko uruchomieniowe IoT Edge, aby połączyć urządzenie fizyczne z tożsamością urządzenia, która istnieje w usłudze Azure IoT Hub.

Demon można skonfigurować przy użyciu pliku konfiguracji w lokalizacji `/etc/iotedge/config.yaml` . Domyślnie plik jest chroniony przed zapisem, może być konieczne przepodwyższony poziom uprawnień, aby go edytować.

Pojedyncze urządzenie IoT Edge może być inicjowane ręcznie przy użyciu parametrów połączenia urządzenia dostarczonych przez IoT Hub. Można też użyć usługi Device Provisioning, aby automatycznie zainicjować obsługę administracyjną urządzeń, co jest przydatne, gdy masz wiele urządzeń do aprowizacji. W zależności od wybranej opcji aprowizacji wybierz odpowiedni skrypt instalacji.

### <a name="option-1-manual-provisioning"></a>Opcja 1: Ręczne inicjowanie obsługi

Aby ręcznie zainicjować obsługę administracyjną urządzenia, należy podać je za pomocą [parametrów połączenia urządzenia](how-to-register-device.md#register-in-the-azure-portal) , które można utworzyć, rejestrując nowe urządzenie w centrum IoT.

Otwórz plik konfiguracji.

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje aprowizacji pliku i Usuń komentarz z sekcji **Ręczna konfiguracja inicjowania obsługi** . Zaktualizuj wartość **device_connection_string** przy użyciu parametrów połączenia z urządzenia IoT Edge. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. Upewnij się, że funkcja **aprowizacji:** wiersz nie ma poprzedzającego odstępu i że elementy zagnieżdżone są wcięte o dwie spacje.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

Zapisz i zamknij plik.

   `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Opcja 2: automatyczne Inicjowanie obsługi

IoT Edge urządzeń można automatycznie zainicjować przy użyciu [usługi Azure IoT Hub Device Provisioning Service (DPS)](../iot-dps/index.yml). Obecnie IoT Edge obsługuje trzy mechanizmy zaświadczania przy korzystaniu z automatycznej aprowizacji, ale wymagania sprzętowe mogą wpływać na wybór. Na przykład urządzenia Raspberry Pi nie są domyślnie dołączone do układu moduł TPM (TPM). Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md)
* [Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu certyfikatów X. 509](how-to-auto-provision-x509-certs.md)
* [Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu zaświadczania klucza symetrycznego](how-to-auto-provision-symmetric-keys.md)

Te artykuły przeprowadzą Cię przez proces konfigurowania rejestracji w usłudze DPS i generowania odpowiednich certyfikatów lub kluczy na potrzeby zaświadczania. Niezależnie od tego, który mechanizm zaświadczania zostanie wybrany, informacje o aprowizacji są dodawane do pliku konfiguracji IoT Edge na urządzeniu IoT Edge.

Otwórz plik konfiguracji.

```bash
sudo nano /etc/iotedge/config.yaml
```

Znajdź konfiguracje aprowizacji pliku i usuń znaczniki komentarza odpowiednie dla mechanizmu zaświadczania. Upewnij się, że wszystkie inne sekcje aprowizacji zostały oznaczone jako komentarze. W wierszu **aprowizacji:** nie powinien znajdować się poprzednie odstępy, a zagnieżdżone elementy powinny mieć dwie spacje. Zaktualizuj wartość **scope_id** za pomocą wartości z wystąpienia IoT Hub Device Provisioning Service i podaj odpowiednie wartości pól zaświadczania.

Zaświadczanie modułu TPM:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

Zaświadczanie X. 509:

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
```

Zaświadczenie klucza symetrycznego:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

Aby wkleić zawartość schowka do systemu nano `Shift+Right Click` lub naciśnij klawisz `Shift+Insert` .

Zapisz i zamknij plik. `CTRL + X`, `Y`, `Enter`

Po wprowadzeniu informacji o aprowizacji w pliku konfiguracji należy ponownie uruchomić demon:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

W przypadku użycia czynności **konfiguracyjnych ręcznych** w poprzedniej sekcji środowisko uruchomieniowe IoT Edge powinno zostać pomyślnie zainicjowane i uruchomione na urządzeniu. W przypadku korzystania z kroków **automatycznej konfiguracji** należy wykonać kilka dodatkowych kroków, aby środowisko uruchomieniowe mógł zarejestrować urządzenie w Twoim imieniu w usłudze IoT Hub. Aby uzyskać kolejne kroki, zobacz [Tworzenie i udostępnianie symulowanego urządzenia TPM IoT Edge na maszynie wirtualnej z systemem Linux](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Możesz sprawdzić stan demona IoT Edge:

```bash
systemctl status iotedge
```

Sprawdzanie dzienników demona:

```bash
journalctl -u iotedge --no-pager --no-full
```

Uruchom [Narzędzie do rozwiązywania problemów](troubleshoot.md#run-the-check-command) , aby sprawdzić najbardziej typowe błędy konfiguracji i sieci:

```bash
sudo iotedge check
```

Do momentu wdrożenia pierwszego modułu do IoT Edge na urządzeniu moduł **$edgeHub** system nie zostanie wdrożony na urządzeniu. W związku z tym, automatyczne sprawdzanie zwróci błąd `Edge Hub can bind to ports on host` sprawdzania łączności. Ten błąd można zignorować, chyba że występuje po wdrożeniu modułu na urządzeniu.

Na koniec Wyświetl listę uruchomionych modułów:

```bash
sudo iotedge list
```

Po zainstalowaniu IoT Edge na urządzeniu jedynym modułem, który powinien zostać uruchomiony, jest **edgeAgent**. Po utworzeniu pierwszego wdrożenia na urządzeniu zostanie również uruchomiony inny moduł systemu **$edgeHub** . Aby uzyskać więcej informacji, zobacz [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md).

## <a name="tips-and-troubleshooting"></a>Porady i rozwiązywanie problemów

Uruchomienie poleceń `iotedge` wymaga podniesionych uprawnień. Po zainstalowaniu środowiska uruchomieniowego Wyloguj się z komputera i zaloguj się ponownie, aby automatycznie zaktualizować swoje uprawnienia. Do tego momentu należy użyć **sudo** przed wszelkimi `iotedge` poleceniami.

W przypadku urządzeń z ograniczonym zasobem zdecydowanie zaleca się ustawienie zmiennej środowiskowej *OptimizeForPerformance* na *wartość false* zgodnie z instrukcjami w [przewodniku rozwiązywania problemów](troubleshoot.md).

Jeśli urządzenie nie może nawiązać połączenia z usługą IoT Hub, a sieć ma serwer proxy, wykonaj kroki opisane w [sekcji Konfigurowanie urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Weryfikacja jądra systemu Linux pod kątem zgodności Moby

Wielu producentów urządzeń osadzonych dostarcza obrazy urządzeń, które zawierają niestandardowe jądra systemu Linux bez funkcji wymaganych do zapewnienia zgodności środowiska uruchomieniowego kontenera. Jeśli wystąpią problemy podczas instalowania zalecanego środowiska uruchomieniowego kontenera Moby, może być możliwe Rozwiązywanie problemów z konfiguracją jądra systemu Linux za pomocą skryptu [check-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) z oficjalnego [repozytorium usługi Moby](https://github.com/moby/moby)w witrynie GitHub. Aby sprawdzić konfigurację jądra, uruchom następujące polecenia na urządzeniu:

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

To polecenie zawiera szczegółowe dane wyjściowe zawierające stan funkcji jądra, które są używane przez środowisko uruchomieniowe Moby. Upewnij się, że wszystkie elementy w obszarze `Generally Necessary` i  `Network Drivers` są włączone, aby upewnić się, że jądro jest w pełni zgodne z Moby środowiska uruchomieniowego.  Jeśli zidentyfikowano jakiekolwiek brakujące funkcje, należy je włączyć przez ponowne skompilowanie jądra ze źródła i wybranie skojarzonych modułów do uwzględnienia w odpowiednim pliku kernel. config.  Podobnie, jeśli używasz generatora konfiguracji jądra, takiego jak `defconfig` lub `menuconfig` , Znajdź i Włącz odpowiednie funkcje i odpowiednio Skompiluj jądro.  Po wdrożeniu nowo zmodyfikowanego jądra ponownie uruchom skrypt Check-config, aby sprawdzić, czy wszystkie wymagane funkcje zostały pomyślnie włączone.

## <a name="install-runtime-using-release-assets"></a>Zainstaluj środowisko uruchomieniowe przy użyciu zasobów wydania

Wykonaj kroki opisane w tej sekcji, jeśli chcesz zainstalować konkretną wersję środowiska uruchomieniowego Azure IoT Edge, która nie jest dostępna w programie `apt-get install` . Lista pakietów firmy Microsoft zawiera tylko ograniczony zestaw najnowszych wersji i ich wersje podrzędne, dlatego te kroki są przeznaczone dla wszystkich osób, które chcą zainstalować starszą wersję lub wersję programu Release Candidate.

Korzystając z poleceń zwinięcie, można kierować pliki składników bezpośrednio z repozytorium IoT Edge GitHub. Wykonaj następujące kroki, aby zainstalować libiothsm i demona Security IoT Edge.

1. Przygotuj urządzenie przy użyciu aparatu kontenera zainstalowanego. Jeśli nie masz aparatu kontenera, postępuj zgodnie z instrukcjami, aby zarejestrować repozytorium firmy Microsoft i zainstalować Moby w sekcji [Install Container Runtime i IoT Edge](#install-container-runtime-and-iot-edge) w tym artykule.

2. Przejdź do [wydań Azure IoT Edge](https://github.com/Azure/azure-iotedge/releases)i Znajdź wersję wydania, która ma być docelowa.

3. Rozwiń sekcję **zasobów** dla tej wersji.

4. Każde wydanie powinno mieć nowe pliki dla IoT Edge Security daemon i hsmlib. Użyj następujących poleceń, aby zaktualizować te składniki.

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

Po pomyślnym zainstalowaniu programu IoT Edge w programie `/etc/iotedge` wyjściowym zostanie wyświetlony komunikat z prośbą o zaktualizowanie pliku konfiguracji. Wykonaj kroki opisane w sekcji [Konfigurowanie demona zabezpieczeń](#configure-the-security-daemon) , aby ukończyć Inicjowanie obsługi urządzeń.

## <a name="uninstall-iot-edge"></a>Odinstaluj IoT Edge

Jeśli chcesz usunąć instalację IoT Edge z urządzenia z systemem Linux, użyj następujących poleceń w wierszu polecenia.

Usuń środowisko uruchomieniowe usługi IoT Edge.

```bash
sudo apt-get remove --purge iotedge
```

Po usunięciu IoT Edge środowiska uruchomieniowego utworzone kontenery są zatrzymane, ale nadal istnieją na urządzeniu. Wyświetl wszystkie kontenery, aby zobaczyć, które pozostaną.

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

Teraz, gdy masz zainstalowaną IoT Edge urządzenie z zainstalowanym środowiskiem uruchomieniowym, możesz [wdrożyć moduły IoT Edge](how-to-deploy-modules-portal.md).

Jeśli masz problemy z instalacją środowiska uruchomieniowego IoT Edge prawidłowo, zapoznaj się ze stroną [rozwiązywania problemów](troubleshoot.md) .

Aby zaktualizować istniejącą instalację do najnowszej wersji IoT Edge, zobacz [Aktualizacja demona IoT Edge Security daemon i środowisko uruchomieniowe](how-to-update-iot-edge.md).
