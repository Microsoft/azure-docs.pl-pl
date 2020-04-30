---
title: Udostępnianie urządzenia za pomocą wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux — Azure IoT Edge
description: Korzystanie z symulowanego modułu TPM na maszynie wirtualnej z systemem Linux w celu przetestowania usługi Azure Device Provisioning dla Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/2/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 82bdc71a123a263fffd842a04f4837b34aaa8685
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131064"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Tworzenie i Inicjowanie obsługi urządzenia IoT Edge przy użyciu wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux

Azure IoT Edge urządzeń można automatycznie zainicjować przy użyciu [usługi Device Provisioning](../iot-dps/index.yml). Jeśli nie znasz procesu samoobsługowego inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z [pojęciami autouzupełniania](../iot-dps/concepts-auto-provisioning.md) .

W tym artykule pokazano, jak przetestować funkcję autoaprowizacji na symulowanym urządzeniu IoT Edge, wykonując następujące czynności:

* Utwórz maszynę wirtualną z systemem Linux w funkcji Hyper-V z symulowanym moduł TPM (TPM) na potrzeby zabezpieczeń sprzętowych.
* Utwórz wystąpienie IoT Hub Device Provisioning Service (DPS).
* Utwórz rejestrację indywidualną dla urządzenia
* Zainstaluj środowisko uruchomieniowe IoT Edge i Połącz urządzenie z IoT Hub

> [!TIP]
> W tym artykule opisano sposób testowania aprowizacji usługi DPS przy użyciu symulatora modułu TPM, ale większość z nich dotyczy fizycznego sprzętu modułu TPM, takiego jak [moduł&trade; TPM Infineon OPTIGA](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), urządzenie z certyfikatem platformy Azure dla IoT.
>
> Jeśli używasz urządzenia fizycznego, możesz przejść do sekcji [pobieranie informacji o aprowizacji z urządzenia fizycznego](#retrieve-provisioning-information-from-a-physical-device) w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna deweloperskia systemu Windows z [włączoną funkcją Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). W tym artykule jest używany system Windows 10 z uruchomioną maszyną wirtualną serwera Ubuntu.
* Aktywna IoT Hub.
* W przypadku korzystania z symulowanego modułu TPM, [programu Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 lub nowszego z włączonym obciążeniem ["Programowanie aplikacji klasycznych w języku C++"](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) .

> [!NOTE]
> Moduł TPM 2,0 jest wymagany w przypadku korzystania z zaświadczania TPM z usługą DPS i może być używany tylko do tworzenia pojedynczych, nie grup, rejestracji.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Tworzenie maszyny wirtualnej z systemem Linux przy użyciu wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną z systemem Linux w ramach funkcji Hyper-V. Ta maszyna wirtualna została skonfigurowana przy użyciu symulowanego modułu TPM, aby można było użyć jej do testowania, w jaki sposób Automatyczna obsługa administracyjna współpracuje z IoT Edge.

### <a name="create-a-virtual-switch"></a>Tworzenie wirtualnego przełącznika

Przełącznik wirtualny umożliwia maszynie wirtualnej łączenie się z siecią fizyczną.

1. Otwórz Menedżera funkcji Hyper-V na komputerze z systemem Windows.

2. W menu **Akcje** wybierz pozycję **Menedżer przełącznika wirtualnego**.

3. Wybierz **zewnętrzny** przełącznik wirtualny, a następnie wybierz pozycję **Utwórz przełącznik wirtualny**.

4. Nadaj nowemu przełącznikowi wirtualnemu nazwę, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia to **Sieć zewnętrzna**, a następnie wybierz przycisk **OK**.

5. Wyskakujące okienko ostrzega o tym, że łączność sieciowa może ulec zakłóceniu. Wybierz pozycję **tak** , aby kontynuować.

Jeśli podczas tworzenia nowego przełącznika wirtualnego pojawią się błędy, upewnij się, że żadne inne przełączniki nie używają adaptera Ethernet i że żadne inne przełączniki nie mają tej samej nazwy.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia dla maszyny wirtualnej i Zapisz go lokalnie. Na przykład [serwer Ubuntu](https://www.ubuntu.com/download/server).

2. W Menedżerze funkcji Hyper-V ponownie wybierz pozycję **Nowa** > **maszyna wirtualna** w menu **Akcje** .

3. Ukończ pracę **Kreatora nowej maszyny wirtualnej** z następującymi określonymi konfiguracjami:

   1. **Określ generację**: Wybierz **generację 2**. Maszyny wirtualne generacji 2 mają włączoną funkcję wirtualizacji zagnieżdżonej, która jest wymagana do uruchamiania IoT Edge na maszynie wirtualnej.
   2. **Konfigurowanie sieci**: Ustaw wartość ustawienia **połączenie** z przełącznikiem wirtualnym, który został utworzony w poprzedniej sekcji.
   3. **Opcje instalacji**: wybierz opcję **Zainstaluj system operacyjny z pliku obrazu rozruchowego** i przejdź do pliku obrazu dysku zapisanego lokalnie.

4. Wybierz pozycję **Zakończ** w kreatorze, aby utworzyć maszynę wirtualną.

Utworzenie nowej maszyny wirtualnej może potrwać kilka minut.

### <a name="enable-virtual-tpm"></a>Włącz wirtualny moduł TPM

Po utworzeniu maszyny wirtualnej Otwórz jej ustawienia, aby włączyć moduł TPM (Trusted Platform Module), który umożliwia samoobsługowe Inicjowanie obsługi urządzenia.

1. Wybierz maszynę wirtualną, a następnie otwórz jej **Ustawienia**.

2. Przejdź do obszaru **Security** (Zabezpieczenia).

3. Usuń zaznaczenie pola wyboru **Włącz bezpieczny rozruch**.

4. Zaznacz **opcję włącz moduł TPM**.

5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchom maszynę wirtualną i Zbierz dane modułu TPM

Na maszynie wirtualnej Utwórz narzędzie, za pomocą którego można pobrać **Identyfikator rejestracji** i **Klucz poręczenia**urządzenia.

1. Uruchom maszynę wirtualną i nawiąż z nią połączenie.

1. Postępuj zgodnie z monitami na maszynie wirtualnej, aby zakończyć proces instalacji i ponownie uruchomić maszynę.

1. Zaloguj się do maszyny wirtualnej, a następnie wykonaj kroki opisane w temacie [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu zainstalowania i SKOMPILOWANIA zestawu SDK urządzeń Azure IoT dla języka C.

   >[!TIP]
   >W tym artykule opisano kopiowanie do i wklejanie z maszyny wirtualnej, co nie jest łatwe w aplikacji połączenia Menedżera funkcji Hyper-V. Aby pobrać swój adres IP, możesz połączyć się z maszyną wirtualną za pomocą Menedżera funkcji Hyper-V: `ifconfig`. Następnie można użyć adresu IP, aby nawiązać połączenie za pośrednictwem `ssh <username>@<ipaddress>`protokołu SSH:.

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizacji urządzenia z symulatora modułu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. W oknie wiersza polecenia przejdź do `azure-iot-sdk-c` katalogu i uruchom symulator modułu TPM. Nasłuchuje on przez gniazdo na portach 2321 i 2322. Nie zamykaj tego okna polecenia; należy zachować ten symulator uruchomiony.

   W `azure-iot-sdk-c` katalogu Uruchom następujące polecenie, aby uruchomić symulator:

   ```bash
   ./provisioning_client/deps/utpm/tools/tpm_simulator/Simulator.exe
   ```

1. Korzystając z programu Visual Studio, Otwórz rozwiązanie wygenerowane `cmake` w katalogu `azure_iot_sdks.sln`o nazwie i skompiluj je za pomocą polecenia **Kompiluj rozwiązanie** w menu **kompilacja** .

1. W okienku **Eksplorator rozwiązań** w programie Visual Studio przejdź do folderu **Provision\_Tools**. Kliknij prawym przyciskiem myszy projekt **tpm_device_provision** i wybierz pozycję **Ustaw jako projekt startowy**.

1. Uruchom rozwiązanie przy użyciu dowolnego polecenia **Uruchom** z menu **debugowanie** . W oknie danych wyjściowych zostanie wyświetlony **Identyfikator rejestracji** symulatora modułu TPM i **Klucz poręczenia**, który należy skopiować do użycia później podczas tworzenia rejestracji indywidualnej dla urządzenia w tym oknie (z identyfikatorem rejestracji i kluczem poręczenia), ale pozostawić okno symulatora modułu TPM uruchomione.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Pobieranie informacji o aprowizacji z urządzenia fizycznego

Na urządzeniu Utwórz narzędzie, za pomocą którego można pobrać informacje o aprowizacji urządzenia.

1. Wykonaj kroki opisane w temacie [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) , aby zainstalować i skompilować zestaw SDK urządzeń Azure IoT dla języka C.

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizacji urządzenia z urządzenia TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Skopiuj wartości **Identyfikator rejestracji** i **Klucz poręczenia**. Te wartości służą do tworzenia rejestracji indywidualnej dla urządzenia w usłudze DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Skonfiguruj IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie IoT Hub Device Provisioning Service na platformie Azure i połącz je z Centrum IoT. Można postępować zgodnie z instrukcjami podanymi w temacie [konfigurowanie IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Po uruchomieniu usługi Device Provisioning należy skopiować wartość **zakres identyfikatora** ze strony przegląd. Ta wartość jest używana podczas konfigurowania środowiska uruchomieniowego IoT Edge.

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji w usłudze DPS

Pobierz informacje o aprowizacji z maszyny wirtualnej i Użyj tej funkcji, aby utworzyć rejestrację indywidualną w usłudze Device Provisioning.

Po utworzeniu rejestracji w usłudze DPS można zadeklarować **początkowy stan dwuosiowy urządzenia**. W ramach sznurka urządzenia można ustawić Tagi do grupowania urządzeń według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Tagi te służą do tworzenia [wdrożeń automatycznych](how-to-deploy-at-scale.md).

> [!TIP]
> W interfejsie wiersza polecenia platformy Azure można utworzyć [rejestrację](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) lub [grupę rejestracji](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment-group) , a następnie użyć flagi z **włączoną krawędzią** , aby określić, że urządzenie lub grupa urządzeń jest urządzeniem IoT Edge.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

2. W obszarze **Ustawienia**wybierz pozycję **Zarządzaj rejestracjami**.

3. Wybierz pozycję **Dodaj rejestrację indywidualną** , a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   1. W obszarze **mechanizm**wybierz pozycję **TPM**.

   2. Podaj **Klucz poręczenia** i **Identyfikator rejestracji** skopiowane z maszyny wirtualnej.

      > [!TIP]
      > W przypadku korzystania z fizycznego urządzenia TPM należy określić **Klucz poręczenia**, który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany z przypisanego do niego producenta mikroukładu modułu TPM. Można utworzyć unikatowy **Identyfikator rejestracji** dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.

   3. Wybierz **wartość true** , aby zadeklarować, że ta maszyna wirtualna jest urządzeniem IoT Edge.

   4. Wybierz połączone **IoT Hub** , do których chcesz połączyć urządzenie. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami alokacji.

   5. Podaj identyfikator urządzenia, jeśli chcesz. Identyfikatory urządzeń umożliwiają kierowanie poszczególnych urządzeń do wdrożenia modułu. Jeśli nie podano identyfikatora urządzenia, zostanie użyty Identyfikator rejestracji.

   6. Jeśli chcesz, Dodaj wartość tagu do **początkowego stanu dwuosiowego urządzenia** . Możesz użyć tagów do grup docelowych urządzeń do wdrożenia modułu. Przykład:

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

   7. Wybierz pozycję **Zapisz**.

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji.

## <a name="install-the-iot-edge-runtime"></a>Zainstaluj środowisko uruchomieniowe IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrożone na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrożenie dodatkowych kontenerów na urządzeniu, aby można było uruchomić kod na krawędzi. Zainstaluj środowisko uruchomieniowe IoT Edge na maszynie wirtualnej.

Przed rozpoczęciem artykułu, który jest zgodny z typem urządzenia, należy znać **zakres identyfikatorów** DPS i **Identyfikator rejestracji** urządzenia. Jeśli zainstalowano przykładowy serwer Ubuntu, należy użyć instrukcji **x64** . Pamiętaj, aby skonfigurować środowisko uruchomieniowe IoT Edge automatyczne, a nie ręczne, Inicjowanie obsługi administracyjnej.

[Zainstaluj środowisko uruchomieniowe Azure IoT Edge w systemie Linux](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Nadaj IoT Edge dostęp do modułu TPM

Aby środowisko uruchomieniowe IoT Edge automatycznie inicjować obsługę administracyjną urządzenia, musi mieć dostęp do modułu TPM.

Aby zapewnić dostęp do IoT Edge środowiska uruchomieniowego TPM, można zastępowanie ustawień systemowych, aby `iotedge` usługa miała uprawnienia główne. Jeśli nie chcesz podwyższyć poziomu uprawnień usługi, możesz również użyć poniższych kroków, aby ręcznie zapewnić dostęp do modułu TPM.

1. Znajdź ścieżkę pliku do modułu sprzętowego modułu TPM na urządzeniu i Zapisz ją jako zmienną lokalną.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Utwórz nową regułę, która zapewni IoT Edge dostęp do środowiska uruchomieniowego do tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otwórz plik reguł.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Skopiuj następujące informacje o dostępie do pliku reguł.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Zapisz i zamknij plik.

6. Wyzwól system udev, aby oszacować nową regułę.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Sprawdź, czy reguła została pomyślnie zastosowana.

   ```bash
   ls -l /dev/tpm0
   ```

   Pomyślne dane wyjściowe wyglądają następująco:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Jeśli nie widzisz odpowiednich uprawnień, spróbuj ponownie uruchomić maszynę, aby odświeżyć udev.

## <a name="restart-the-iot-edge-runtime"></a>Uruchom ponownie środowisko uruchomieniowe IoT Edge

Uruchom ponownie środowisko uruchomieniowe IoT Edge, aby wyszukać wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

Sprawdź, czy środowisko uruchomieniowe IoT Edge jest uruchomione.

   ```bash
   sudo systemctl status iotedge
   ```

Jeśli widzisz błędy aprowizacji, może to spowodować, że zmiany konfiguracji nie zostały jeszcze zastosowane. Spróbuj ponownie uruchomić demo IoT Edge.

   ```bash
   sudo systemctl daemon-reload
   ```

Lub spróbuj ponownie uruchomić maszynę wirtualną, aby sprawdzić, czy zmiany zaczną obowiązywać po rozpoczęciu pracy.

## <a name="verify-successful-installation"></a>Weryfikuj pomyślną instalację

Jeśli środowisko uruchomieniowe zostało pomyślnie uruchomione, możesz przejść do IoT Hub i zobaczyć, że nowe urządzenie zostało automatycznie zainicjowane. Teraz urządzenie jest gotowe do uruchamiania modułów IoT Edge.

Sprawdź stan demona IoT Edge.

```cmd/sh
systemctl status iotedge
```

Sprawdzanie dzienników demona.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Wyświetl listę uruchomionych modułów.

```cmd/sh
iotedge list
```

Możesz sprawdzić, czy została użyta Rejestracja indywidualna utworzona w usłudze Device Provisioning. Przejdź do wystąpienia usługi Device Provisioning w Azure Portal. Otwórz szczegóły rejestracji dla utworzonej rejestracji indywidualnej. Zwróć uwagę, że jest **przypisany** stan rejestracji i identyfikator urządzenia jest wyświetlany.

## <a name="next-steps"></a>Następne kroki

Proces rejestracji w usłudze Device Provisioning Service umożliwia ustawienie tagów urządzeń i urządzeń w tym samym czasie, w których jest inicjowane nowe urządzenie. Tych wartości można użyć do kierowania poszczególnych urządzeń lub grup urządzeń przy użyciu funkcji automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-at-scale.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-cli-at-scale.md).
