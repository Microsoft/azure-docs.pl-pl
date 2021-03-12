---
title: Udostępnianie urządzenia za pomocą wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux — Azure IoT Edge
description: Korzystanie z symulowanego modułu TPM na maszynie wirtualnej z systemem Linux w celu przetestowania usługi Azure Device Provisioning dla Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9356da9516ea17243be8e0e5519ec4b19e06db8a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200872"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Tworzenie i Inicjowanie obsługi administracyjnej urządzenia IoT Edge przy użyciu modułu TPM w systemie Linux

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

W tym artykule pokazano, jak przetestować funkcję autoaprowizacji na urządzeniu z systemem Linux IoT Edge przy użyciu Trusted Platform Module (TPM). Można automatycznie zainicjować obsługę administracyjną urządzeń Azure IoT Edge przy użyciu [usługi Device Provisioning](../iot-dps/index.yml). Jeśli nie znasz procesu inicjowania obsługi administracyjnej, przed kontynuowaniem zapoznaj się z omówieniem [aprowizacji](../iot-dps/about-iot-dps.md#provisioning-process) .

:::moniker range=">=iotedge-2020-11"
> [!NOTE]
> Obecnie funkcja automatycznej aprowizacji przy użyciu uwierzytelniania modułu TPM nie jest obsługiwana w IoT Edge w wersji 1,2.
:::moniker-end

Zadania są następujące:

1. Utwórz maszynę wirtualną z systemem Linux w funkcji Hyper-V z symulowanym Trusted Platform Module (TPM) na potrzeby zabezpieczeń sprzętowych.
1. Utwórz wystąpienie IoT Hub Device Provisioning Service (DPS).
1. Utwórz rejestrację indywidualną dla urządzenia.
1. Zainstaluj środowisko uruchomieniowe IoT Edge i Połącz urządzenie z IoT Hub.

> [!TIP]
> W tym artykule opisano sposób testowania aprowizacji usługi DPS przy użyciu symulatora modułu TPM, ale większość z nich dotyczy fizycznego sprzętu modułu TPM, takiego jak [ &trade; moduł TPM Infineon OPTIGA](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), urządzenie z certyfikatem platformy Azure dla IoT.
>
> Jeśli używasz urządzenia fizycznego, możesz przejść do sekcji [pobieranie informacji o aprowizacji z urządzenia fizycznego](#retrieve-provisioning-information-from-a-physical-device) w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna deweloperskia systemu Windows z [włączoną funkcją Hyper-V](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). W tym artykule jest używany system Windows 10 z uruchomioną maszyną wirtualną serwera Ubuntu.
* Aktywna IoT Hub.

> [!NOTE]
> Moduł TPM 2,0 jest wymagany w przypadku korzystania z zaświadczania TPM z usługą DPS i może być używany tylko do tworzenia pojedynczych, nie grup, rejestracji.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Tworzenie maszyny wirtualnej z systemem Linux przy użyciu wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną z systemem Linux w ramach funkcji Hyper-V. Ta maszyna wirtualna jest konfigurowana z symulowanym modułem TPM do testowania, w jaki sposób Automatyczna obsługa administracyjna współdziała z IoT Edge.

### <a name="create-a-virtual-switch"></a>Tworzenie wirtualnego przełącznika

Przełącznik wirtualny umożliwia maszynie wirtualnej łączenie się z siecią fizyczną.

1. Otwórz Menedżera funkcji Hyper-V na komputerze z systemem Windows.

2. W menu **Akcje** wybierz pozycję **Menedżer przełącznika wirtualnego**.

3. Wybierz **zewnętrzny** przełącznik wirtualny, a następnie wybierz pozycję **Utwórz przełącznik wirtualny**.

4. Nadaj nowemu przełącznikowi wirtualnemu nazwę, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia to **Sieć zewnętrzna**, a następnie wybierz przycisk **OK**.

5. Wyskakujące okienko ostrzega o tym, że łączność sieciowa może ulec zakłóceniu. Wybierz przycisk **Tak**, aby kontynuować.

Jeśli podczas tworzenia nowego przełącznika wirtualnego pojawią się błędy, upewnij się, że żadne inne przełączniki nie używają adaptera Ethernet i że żadne inne przełączniki nie mają tej samej nazwy.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia dla maszyny wirtualnej i Zapisz go lokalnie. Na przykład [Ubuntu server 18,04](http://releases.ubuntu.com/18.04/). Informacje o obsługiwanych systemach operacyjnych dla urządzeń IoT Edge można znaleźć w temacie [Azure IoT Edge obsługiwane systemy](support.md).

2. W Menedżerze funkcji Hyper-V ponownie wybierz pozycję **Akcja**  >  **Nowa**  >  **maszyna wirtualna** w menu **Akcje** .

3. Ukończ pracę **Kreatora nowej maszyny wirtualnej** z następującymi określonymi konfiguracjami:

   1. **Określ generację**: Wybierz **generację 2**. Maszyny wirtualne generacji 2 mają włączoną funkcję wirtualizacji zagnieżdżonej, która jest wymagana do uruchamiania IoT Edge na maszynie wirtualnej.
   2. **Konfigurowanie sieci**: Ustaw wartość ustawienia **połączenie** z przełącznikiem wirtualnym, który został utworzony w poprzedniej sekcji.
   3. **Opcje instalacji**: wybierz opcję **Zainstaluj system operacyjny z pliku obrazu rozruchowego** i przejdź do pliku obrazu dysku zapisanego lokalnie.

4. Wybierz pozycję **Zakończ** w kreatorze, aby utworzyć maszynę wirtualną.

Utworzenie nowej maszyny wirtualnej może potrwać kilka minut.

### <a name="enable-virtual-tpm"></a>Włącz wirtualny moduł TPM

Po utworzeniu maszyny wirtualnej Otwórz jej ustawienia, aby włączyć moduł TPM (Trusted Platform Module), który umożliwia samoobsługowe Inicjowanie obsługi urządzenia.

1. W Menedżerze funkcji Hyper-V kliknij prawym przyciskiem myszy maszynę wirtualną, a następnie wybierz pozycję **Ustawienia**.

2. Przejdź do obszaru **Security** (Zabezpieczenia).

3. Usuń zaznaczenie pola wyboru **Włącz bezpieczny rozruch**.

4. Zaznacz **opcję włącz Trusted Platform Module**.

5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchom maszynę wirtualną i Zbierz dane modułu TPM

Na maszynie wirtualnej Utwórz narzędzie, za pomocą którego można pobrać **Identyfikator rejestracji** i **Klucz poręczenia** urządzenia.

1. W Menedżerze funkcji Hyper-V Uruchom maszynę wirtualną i nawiąż z nią połączenie.

1. Postępuj zgodnie z monitami na maszynie wirtualnej, aby zakończyć proces instalacji i ponownie uruchomić maszynę.

1. Zaloguj się do maszyny wirtualnej, a następnie wykonaj kroki opisane w temacie [Konfigurowanie środowiska deweloperskiego systemu Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) w celu zainstalowania i SKOMPILOWANIA zestawu SDK urządzeń Azure IoT dla języka C.

   >[!TIP]
   >W ramach tego artykułu skopiujesz i wkleisz ją na maszynie wirtualnej, co nie jest łatwe w aplikacji połączenia Menedżera funkcji Hyper-V. Aby pobrać swój adres IP, możesz połączyć się z maszyną wirtualną za pomocą Menedżera funkcji Hyper-V. Najpierw uruchom `sudo apt install net-tools` , a następnie `hostname -I` . Następnie można użyć adresu IP, aby nawiązać połączenie za pośrednictwem protokołu SSH: `ssh <username>@<ipaddress>` .

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizacji urządzenia z modułu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. W oknie danych wyjściowych zostanie wyświetlony **Identyfikator rejestracji** urządzenia i **Klucz poręczenia**. Skopiuj te wartości do użycia później podczas tworzenia rejestracji indywidualnej dla urządzenia.

Gdy masz Identyfikator rejestracji i klucz poręczenia, przejdź do sekcji [konfigurowanie IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Pobieranie informacji o aprowizacji z urządzenia fizycznego

Jeśli używasz urządzenia fizycznego IoT Edge zamiast maszyny wirtualnej, Utwórz narzędzie, za pomocą którego można pobrać informacje o aprowizacji urządzenia.

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
> W interfejsie wiersza polecenia platformy Azure można utworzyć [rejestrację](/cli/azure/ext/azure-iot/iot/dps/enrollment) i użyć flagi z **włączoną krawędzią** , aby określić, że urządzenie jest urządzeniem IoT Edge.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia IoT Hub Device Provisioning Service.

2. W obszarze **Ustawienia** wybierz pozycję **Zarządzaj rejestracjami**.

3. Wybierz pozycję **Dodaj rejestrację indywidualną** , a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   1. W obszarze **mechanizm** wybierz pozycję **TPM**.

   2. Podaj **Klucz poręczenia** i **Identyfikator rejestracji** skopiowane z maszyny wirtualnej.

      > [!TIP]
      > W przypadku korzystania z fizycznego urządzenia TPM należy określić **Klucz poręczenia**, który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany z przypisanego do niego producenta mikroukładu modułu TPM. Można utworzyć unikatowy **Identyfikator rejestracji** dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.

   3. Podaj identyfikator urządzenia, jeśli chcesz. Jeśli nie podano identyfikatora urządzenia, zostanie użyty Identyfikator rejestracji.

   4. Wybierz **wartość true** , aby zadeklarować, że ta maszyna wirtualna jest urządzeniem IoT Edge.

   5. Wybierz połączone IoT Hub, do których chcesz połączyć urządzenie, lub wybierz pozycję **Połącz z nowym IoT Hub**. Można wybrać wiele centrów, a urządzenie zostanie przypisane do jednej z nich zgodnie z wybranymi zasadami przypisywania.

   6. Jeśli chcesz, Dodaj wartość tagu do **początkowego stanu dwuosiowego urządzenia** . Możesz użyć tagów do grup docelowych urządzeń do wdrożenia modułu. Aby uzyskać więcej informacji, zobacz [wdrażanie modułów IoT Edge w odpowiedniej skali](how-to-deploy-at-scale.md).

   7. Wybierz pozycję **Zapisz**.

Teraz, gdy istnieje Rejestracja dla tego urządzenia, środowisko uruchomieniowe IoT Edge może automatycznie zainicjować obsługę administracyjną urządzenia podczas instalacji.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrożenie dodatkowych kontenerów na urządzeniu, aby można było uruchomić kod na krawędzi. Zainstaluj środowisko uruchomieniowe IoT Edge na maszynie wirtualnej.

Wykonaj kroki opisane w artykule [Instalowanie środowiska uruchomieniowego Azure IoT Edge](how-to-install-iot-edge.md), a następnie wróć do tego artykułu, aby udostępnić urządzenie.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia przy użyciu informacji o aprowizacji

Po zainstalowaniu na urządzeniu środowiska uruchomieniowego skonfiguruj je za pomocą informacji, które są używane do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

1. Należy znać **zakres identyfikatorów** DPS i **Identyfikator rejestracji** urządzenia, które zostały zebrane w poprzednich sekcjach.

1. Otwórz plik konfiguracji na urządzeniu IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracje aprowizacji pliku. Usuń znaczniki komentarza z wierszy, aby zainicjować obsługę administracyjną modułu TPM, i upewnij się, że wszystkie inne wiersze aprowizacji zostały oznaczone jako komentarze.

   `provisioning:`Wiersz nie powinien zawierać poprzedzających odstępów, a zagnieżdżone elementy powinny mieć dwie spacje.

   ```yml
   # DPS TPM provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "<SCOPE_ID>"
     attestation:
       method: "tpm"
       registration_id: "<REGISTRATION_ID>"
   # always_reprovision_on_startup: true
   # dynamic_reprovisioning: false
   ```

   Opcjonalnie możesz użyć `always_reprovision_on_startup` linii lub, `dynamic_reprovisioning` Aby skonfigurować zachowanie ponownego inicjowania obsługi administracyjnej urządzenia. Jeśli urządzenie jest ustawione na ponowne Inicjowanie obsługi administracyjnej, będzie zawsze próbowało najpierw zainicjować obsługę administracyjną przy użyciu punktu dystrybucji, a następnie wrócić do tworzenia kopii zapasowej, jeśli to się nie powiedzie. Jeśli urządzenie jest ustawione na dynamiczną ponowną obsługę administracyjną, IoT Edge zostanie ponownie uruchomione i Zainicjuj obsługę administracyjną w przypadku wykrycia zdarzenia ponownego aprowizacji. Aby uzyskać więcej informacji, zobacz temat [IoT Hub ponowne Inicjowanie obsługi administracyjnej urządzeń](../iot-dps/concepts-device-reprovision.md).

1. Zaktualizuj wartości `scope_id` i `registration_id` przy użyciu informacji o usłudze DPS i urządzeniu.

## <a name="give-iot-edge-access-to-the-tpm"></a>Nadaj IoT Edge dostęp do modułu TPM

Środowisko uruchomieniowe IoT Edge musi uzyskać dostęp do modułu TPM w celu automatycznego aprowizacji urządzenia.

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
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
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

   Pomyślne dane wyjściowe są wyświetlane w następujący sposób:

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

Proces rejestracji w usłudze DPS pozwala ustawić znaczniki identyfikatora urządzenia i sznurka urządzenia w tym samym czasie, w którym jest inicjowane nowe urządzenie. Tych wartości można użyć do kierowania poszczególnych urządzeń lub grup urządzeń przy użyciu funkcji automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na dużą skalę przy użyciu Azure Portal](how-to-deploy-at-scale.md) lub [przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-deploy-cli-at-scale.md).
