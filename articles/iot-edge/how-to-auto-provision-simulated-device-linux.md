---
title: Aprowizuj urządzenie za pomocą wirtualnego modułu TPM na maszynie wirtualnej z systemem Linux — Azure IoT Edge
description: Używanie symulowanego modułu TPM na maszynie wirtualnej z systemem Linux do testowania usługi Azure Device Provisioning service Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/09/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 79fe8acd06084c58b0cf9b47bf93e933c648510c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481994"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Tworzenie i aprowizować urządzenie IoT Edge z modułem TPM w systemie Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

W tym artykule pokazano, jak testować automatyczną aprowizowanie na urządzeniu z IoT Edge Linux przy użyciu Trusted Platform Module (TPM). Możesz automatycznie aprowiz Azure IoT Edge urządzeń za pomocą [usługi Device Provisioning Service.](../iot-dps/index.yml) Jeśli nie masz informacji o procesie automatycznego aprowizowania, [](../iot-dps/about-iot-dps.md#provisioning-process) przed kontynuowaniem zapoznaj się z omówieniem aprowacji.

Zadania są następujące:

1. Utwórz maszynę wirtualną z systemem Linux w funkcji Hyper-V przy użyciu symulowanego Trusted Platform Module (TPM) w celu zabezpieczenia sprzętu.
1. Utwórz wystąpienie usługi IoT Hub Device Provisioning Service (DPS).
1. Utwórz rejestrację indywidualną dla urządzenia.
1. Zainstaluj środowisko IoT Edge uruchomieniowe i połącz urządzenie z IoT Hub.

> [!TIP]
> W tym artykule opisano, jak przetestować aprowizowanie usługi DPS przy użyciu symulatora modułu TPM, ale większość z nich ma zastosowanie do fizycznego sprzętu TPM, takiego jak [Infineon &trade; TPM TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), certyfikowany przez platformę Azure dla urządzenia IoT.
>
> Jeśli używasz urządzenia fizycznego, możesz przejść od [](#retrieve-provisioning-information-from-a-physical-device) razu do sekcji Pobieranie informacji aprowizowania z urządzenia fizycznego w tym artykule.

## <a name="prerequisites"></a>Wymagania wstępne

* Maszyna dewelopera z systemem Windows z [włączoną obsługą funkcji Hyper-V.](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) W tym artykule Windows 10 maszyny wirtualnej z systemem Ubuntu Server.
* Aktywny IoT Hub.

> [!NOTE]
> Moduł TPM 2.0 jest wymagany w przypadku korzystania z zaświadczenia modułu TPM z usługą DPS i może służyć tylko do tworzenia rejestracji indywidualnych, a nie grupowych.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Tworzenie maszyny wirtualnej z systemem Linux przy użyciu wirtualnego modułu TPM

W tej sekcji utworzysz nową maszynę wirtualną z systemem Linux w funkcji Hyper-V. Tę maszynę wirtualną można skonfigurować za pomocą symulowanego modułu TPM w celu przetestowania działania automatycznej aprow IoT Edge.

### <a name="create-a-virtual-switch"></a>Tworzenie wirtualnego przełącznika

Przełącznik wirtualny umożliwia maszynie wirtualnej łączenie się z siecią fizyczną.

1. Otwórz Menedżera funkcji Hyper-V na maszynie z systemem Windows.

2. W menu **Akcje** wybierz pozycję **Menedżer przełącznika wirtualnego.**

3. Wybierz zewnętrzny **przełącznik** wirtualny, a następnie wybierz **pozycję Utwórz przełącznik wirtualny.**

4. Nadaj nazwę noweowi przełącznikowi wirtualnego, na przykład **EdgeSwitch**. Upewnij się, że typ połączenia jest ustawiony na Sieć **zewnętrzna,** a następnie wybierz przycisk **OK.**

5. Wyskakujące okienko ostrzega o tym, że łączność sieciowa może zostać zakłócona. Wybierz przycisk **Tak**, aby kontynuować.

Jeśli podczas tworzenia nowego przełącznika wirtualnego występują błędy, upewnij się, że żadne inne przełączniki nie używają adaptera Ethernet i że żadne inne przełączniki nie używają tej samej nazwy.

### <a name="create-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. Pobierz plik obrazu dysku do użycia dla maszyny wirtualnej i zapisz go lokalnie. Na przykład [Ubuntu Server 18.04.](http://releases.ubuntu.com/18.04/) Aby uzyskać informacje o obsługiwanych systemach operacyjnych dla IoT Edge, zobacz [Azure IoT Edge obsługiwanych systemów.](support.md)

2. W Menedżerze funkcji Hyper-V ponownie wybierz **pozycję** Akcja  >  **Nowa**  >  **maszyna wirtualna** w menu **Akcje.**

3. Wykonaj kroki **Kreatora nowej maszyny wirtualnej** z następującymi konfiguracjami:

   1. **Określ generację:** wybierz **pozycję Generacja 2.** Maszyny wirtualne generacji 2 mają włączoną wirtualizację zagnieżdżoną, która jest wymagana do IoT Edge na maszynie wirtualnej.
   2. **Konfigurowanie sieci:** ustaw wartość połączenia **z** przełącznikiem wirtualnym utworzonym w poprzedniej sekcji.
   3. **Opcje instalacji:** wybierz pozycję Zainstaluj system operacyjny z pliku obrazu **rozruchowego** i przejdź do zapisanego lokalnie pliku obrazu dysku.

4. Wybierz **pozycję** Zakończ w kreatorze, aby utworzyć maszynę wirtualną.

Tworzenie nowej maszyny wirtualnej może potrwać kilka minut.

### <a name="enable-virtual-tpm"></a>Włączanie wirtualnego modułu TPM

Po utworzeniu maszyny wirtualnej otwórz jej ustawienia, aby włączyć moduł TPM (Virtual Trusted Platform Module), który umożliwia automatyczną aprowizę urządzenia.

1. W Menedżerze funkcji Hyper-V kliknij prawym przyciskiem myszy maszynę wirtualną i wybierz pozycję **Ustawienia.**

2. Przejdź do obszaru **Security** (Zabezpieczenia).

3. Usuń zaznaczenie **pola wyboru Włącz bezpieczny rozruch.**

4. Zaznacz **pole wyboru Włącz Trusted Platform Module**.

5. Kliknij przycisk **OK**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Uruchamianie maszyny wirtualnej i zbieranie danych modułu TPM

Na maszynie wirtualnej skompilowaj narzędzie, które umożliwia  pobranie identyfikatora rejestracji i klucza **poręczenia urządzenia.**

1. W Menedżerze funkcji Hyper-V uruchom maszynę wirtualną i połącz się z nim.

1. Postępuj zgodnie z monitami na maszynie wirtualnej, aby zakończyć proces instalacji i ponownie uruchomić maszynę.

1. Zaloguj się do maszyny wirtualnej, a następnie postępuj zgodnie z instrukcjami z tematu Konfigurowanie środowiska projektowego systemu [Linux,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aby zainstalować i skompilować zestaw SDK urządzenia usługi Azure IoT dla języka C.

   >[!TIP]
   >W ramach tego artykułu skopiujesz i wklejesz na maszynie wirtualnej, co nie jest łatwe za pośrednictwem aplikacji połączenia Menedżera funkcji Hyper-V. Możesz połączyć się z maszyną wirtualną za pośrednictwem Menedżera funkcji Hyper-V raz, aby pobrać jej adres IP. Najpierw uruchom, `sudo apt install net-tools` a następnie `hostname -I` . Następnie możesz użyć adresu IP, aby nawiązać połączenie za pośrednictwem protokołu SSH: `ssh <username>@<ipaddress>` .

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizowania urządzenia z modułu TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. W oknie danych wyjściowych zostaną wyświetlone identyfikator **rejestracji urządzenia** i klucz **poręczenia**. Skopiuj te wartości do użycia później podczas tworzenia rejestracji indywidualnej dla urządzenia.

Gdy masz już identyfikator rejestracji i klucz poręczenia, przejdź do sekcji [Konfigurowanie IoT Hub Device Provisioning Service](#set-up-the-iot-hub-device-provisioning-service)

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>Pobieranie informacji aprowizowania z urządzenia fizycznego

Jeśli używasz urządzenia fizycznego IoT Edge a nie maszyny wirtualnej, skompilowaj narzędzie, za pomocą których możesz pobrać informacje o aprowizowania urządzenia.

1. Wykonaj kroki opisane w te tematu Konfigurowanie środowiska [projektowego systemu Linux,](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) aby zainstalować i skompilować zestaw SDK urządzenia usługi Azure IoT dla języka C.

1. Uruchom następujące polecenia, aby skompilować narzędzie zestawu SDK, które pobiera informacje o aprowizowania urządzenia z urządzenia TPM.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. Skopiuj wartości identyfikator **rejestracji i** **klucz poręczenia**. Za pomocą tych wartości można utworzyć rejestrację indywidualną dla urządzenia w uchucie DPS.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Konfigurowanie IoT Hub Device Provisioning Service

Utwórz nowe wystąpienie usługi IoT Hub Device Provisioning Service platformie Azure i połącz je z centrum IoT. Możesz wykonać instrukcje z [tematu Konfigurowanie IoT Hub DPS.](../iot-dps/quick-setup-auto-provision.md)

Po uruchomieniu usługi Device Provisioning Service skopiuj wartość **Zakres** identyfikatorów ze strony przeglądu. Ta wartość jest wartością używaną podczas konfigurowania IoT Edge uruchomieniowego.

## <a name="create-a-dps-enrollment"></a>Tworzenie rejestracji dps

Pobierz informacje o aprowizowaniach z maszyny wirtualnej i użyj ich do utworzenia rejestracji indywidualnej w usłudze Device Provisioning.

Podczas tworzenia rejestracji w uciece DPS masz możliwość zadeklarowania początkowego stanu bliźniaczej **reprezentacji urządzenia**. W bliźniaczej reprezentacji urządzenia można ustawić tagi, aby grupować urządzenia według dowolnej metryki potrzebnej w rozwiązaniu, na przykład regionu, środowiska, lokalizacji lub typu urządzenia. Te tagi są używane do tworzenia [wdrożeń automatycznych.](how-to-deploy-at-scale.md)

> [!TIP]
> W interfejsie wiersza polecenia [](/cli/azure/iot/dps/enrollment) platformy Azure możesz utworzyć rejestrację i użyć **flagi** z obsługą krawędzi, aby określić, że urządzenie jest urządzeniem IoT Edge brzegowym.

1. W [Azure Portal](https://portal.azure.com)przejdź do swojego wystąpienia IoT Hub Device Provisioning Service.

2. W **obszarze Ustawienia** wybierz pozycję Zarządzaj **rejestracjami.**

3. Wybierz **pozycję Dodaj rejestrację** indywidualną, a następnie wykonaj następujące kroki, aby skonfigurować rejestrację:  

   1. W **przypadku opcji Mechanizm** wybierz pozycję MODUŁ **TPM.**

   2. Podaj klucz **poręczenia i** **identyfikator rejestracji** skopiowane z maszyny wirtualnej.

      > [!TIP]
      > Jeśli używasz fizycznego urządzenia TPM, musisz określić klucz poręczenia **,** który jest unikatowy dla każdego mikroukładu modułu TPM i jest uzyskiwany od producenta mikroukładu modułu TPM skojarzonego z nim. Możesz uzyskać unikatowy **identyfikator** rejestracji dla urządzenia TPM, na przykład tworząc skrót SHA-256 klucza poręczenia.

   3. Jeśli chcesz, podaj identyfikator urządzenia. Jeśli nie poniesiesz identyfikatora urządzenia, zostanie użyty identyfikator rejestracji.

   4. Wybierz **pozycję Prawda,** aby zadeklarować, że ta maszyna wirtualna IoT Edge wirtualna.

   5. Wybierz połączone IoT Hub, z którym chcesz połączyć urządzenie, lub wybierz pozycję **Połącz z nowym IoT Hub**. Możesz wybrać wiele centrów, a urządzenie zostanie przypisane do jednego z nich zgodnie z wybranymi zasadami przypisywania.

   6. Jeśli chcesz, dodaj wartość tagu do **początkowego stanu bliźniaczej** reprezentacji urządzenia. Za pomocą tagów można kierować grupy urządzeń do wdrożenia modułu. Aby uzyskać więcej informacji, zobacz [Deploy IoT Edge modules at scale (Wdrażanie modułów IoT Edge na dużą skalę).](how-to-deploy-at-scale.md)

   7. Wybierz pozycję **Zapisz**.

Teraz, gdy dla tego urządzenia istnieje rejestracja, środowisko IoT Edge może automatycznie aprowizuje urządzenie podczas instalacji.

## <a name="install-the-iot-edge-runtime"></a>Instalowanie środowiska uruchomieniowego usługi IoT Edge

Środowisko uruchomieniowe usługi IoT Edge jest wdrażane na wszystkich urządzeniach usługi IoT Edge. Jego składniki działają w kontenerach i umożliwiają wdrażanie dodatkowych kontenerów na urządzeniu, dzięki czemu można uruchamiać kod na brzegu sieci. Zainstaluj środowisko IoT Edge uruchomieniowe na maszynie wirtualnej.

Wykonaj kroki opisane w [artykule Instalowanie Azure IoT Edge uruchomieniowego,](how-to-install-iot-edge.md)a następnie wróć do tego artykułu, aby aprowizować urządzenie.

## <a name="configure-the-device-with-provisioning-information"></a>Konfigurowanie urządzenia przy użyciu informacji aprowizowania

Po zainstalowaniu środowiska uruchomieniowego na urządzeniu skonfiguruj je przy użyciu informacji, których używa do nawiązywania połączenia z usługą Device Provisioning i IoT Hub.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Poznaj zakres identyfikatorów **dps** i identyfikator **rejestracji urządzeń** zebrane w poprzednich sekcjach.

1. Otwórz plik konfiguracji na IoT Edge urządzeniu.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Znajdź sekcję konfiguracji aprowizowania w pliku . Odkomentuj wiersze dla aprowowania modułu TPM i upewnij się, że wszystkie inne wiersze aprowności zostały zakomentowane.

   Wiersz `provisioning:` nie powinien mieć poprzedzających odstępów, a zagnieżdżone elementy powinny być wcięte przez dwie spacje.

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

1. Zaktualizuj wartości i przy `scope_id` `registration_id` użyciu usługi DPS i informacji o urządzeniu.

1. Opcjonalnie możesz użyć linii lub , aby skonfigurować zachowanie ponownego `always_reprovision_on_startup` `dynamic_reprovisioning` aprowizowania urządzenia. Jeśli ustawiono ponowne aprowizowanie urządzenia podczas uruchamiania, zawsze będzie ono najpierw próbowało aprowizować usługę DPS, a w razie niepowodzenia wrócić do tworzenia kopii zapasowej. Jeśli ustawiono dynamiczne ponowne aprowizowanie urządzenia, program IoT Edge ponowne uruchomienie i ponowne aprowizowanie w przypadku wykrycia zdarzenia ponownej aprowizowania. Aby uzyskać więcej informacji, [zobacz IoT Hub device reprovisioning concepts (Pojęcia dotyczące ponownego aprowizowania urządzeń).](../iot-dps/concepts-device-reprovision.md)

1. Zapisz i zamknij plik.

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Poznaj zakres identyfikatorów **dps** i identyfikator **rejestracji urządzeń** zebrane w poprzednich sekcjach.

1. Otwórz plik konfiguracji na IoT Edge urządzeniu.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Znajdź sekcję konfiguracji aprowizowania w pliku . Odkomentuj wiersze dla aprowowania modułu TPM i upewnij się, że wszystkie inne wiersze aprowności zostały zakomentowane.

   ```toml
   # DPS provisioning with TPM
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "<SCOPE_ID>"
   
   [provisioning.attestation]
   method = "tpm"
   registration_id = "<REGISTRATION_ID>"
   ```

1. Zaktualizuj wartości i przy `id_scope` `registration_id` użyciu usługi DPS i informacji o urządzeniu.

1. Opcjonalnie znajdź sekcję trybu automatycznego ponownego aprowizowania pliku. Użyj parametru , aby skonfigurować zachowanie ponownego aprowizowania urządzenia `auto_reprovisioning_mode` na wartość , lub `Dynamic` `AlwaysOnStartup` `OnErrorOnly` . Aby uzyskać więcej informacji, [zobacz IoT Hub device reprovisioning concepts (Pojęcia dotyczące ponownego aprowizowania urządzeń).](../iot-dps/concepts-device-reprovision.md)

1. Zapisz i zamknij plik.
:::moniker-end
<!-- end 1.2 -->

## <a name="give-iot-edge-access-to-the-tpm"></a>Nadaj IoT Edge dostęp do modułu TPM

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Środowisko IoT Edge musi uzyskać dostęp do modułu TPM, aby automatycznie aprowizować urządzenie.

Moduł TPM można udzielić dostępu do IoT Edge uruchomieniowego przez zastąpienie ustawień systemowych tak, aby usługa `iotedge` ma uprawnienia główne. Jeśli nie chcesz podnieść poziomu uprawnień usługi, możesz również wykonać następujące kroki, aby ręcznie zapewnić dostęp do modułu TPM.

1. Znajdź ścieżkę pliku do modułu sprzętowego TPM na urządzeniu i zapisz go jako zmienną lokalną.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Utwórz nową regułę, która zapewni środowisku uruchomieniowemu IoT Edge dostęp do modułu tpm0.

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

6. Wyzwolić system udev, aby ocenić nową regułę.

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

   Jeśli nie widzisz, że zastosowano poprawne uprawnienia, spróbuj ponownie uruchomić maszynę, aby odświeżyć interfejs użytkownika.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Środowisko IoT Edge opiera się na usłudze TPM, która ma dostęp brokera do modułu TPM urządzenia. Ta usługa musi uzyskać dostęp do modułu TPM, aby automatycznie aprowizować urządzenie.

Dostęp do modułu TPM można udzielić, przesłaniając ustawienia systemowe tak, aby usługa `aziottpm` ma uprawnienia główne. Jeśli nie chcesz podnieść poziomu uprawnień usługi, możesz również wykonać poniższe kroki, aby ręcznie zapewnić dostęp do modułu TPM.

1. Znajdź ścieżkę pliku do modułu sprzętowego TPM na urządzeniu i zapisz go jako zmienną lokalną.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Utwórz nową regułę, która zapewni środowisku uruchomieniowemu IoT Edge do modułu tpm0.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Otwórz plik reguł.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Skopiuj następujące informacje o dostępie do pliku reguł.

   ```input
   # allow aziottpm access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="aziottpm", MODE="0600"
   ```

5. Zapisz i zamknij plik.

6. Wyzwolić system udev, aby ocenić nową regułę.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Sprawdź, czy reguła została pomyślnie zastosowana.

   ```bash
   ls -l /dev/tpm0
   ```

   Pomyślne dane wyjściowe są wyświetlane w następujący sposób:

   ```output
   crw-rw---- 1 root aziottpm 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Jeśli nie widzisz, że zastosowano poprawne uprawnienia, spróbuj ponownie uruchomić maszynę, aby odświeżyć interfejs użytkownika.
:::moniker-end
<!-- end 1.2 -->

## <a name="restart-iot-edge-and-verify-successful-installation"></a>Uruchom ponownie IoT Edge i sprawdź, czy instalacja powiodła się

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Uruchom ponownie IoT Edge uruchomieniowe, aby przejmować wszystkie zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo systemctl restart iotedge
   ```

Sprawdź, czy środowisko IoT Edge uruchomieniowe.

   ```bash
   sudo systemctl status iotedge
   ```

Zbadaj dzienniki demona.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Jeśli występują błędy aprowowania, może się okazać, że zmiany konfiguracji nie zostały jeszcze wprowadzone. Spróbuj ponownie uruchomić demona IoT Edge demona.

   ```bash
   sudo systemctl daemon-reload
   ```

Możesz też spróbować ponownie uruchomić maszynę wirtualną, aby sprawdzić, czy zmiany zaczynają obowiązywać od nowa.
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
Zastosuj zmiany konfiguracji wprowadzone na urządzeniu.

   ```bash
   sudo iotedge config apply
   ```

Sprawdź, czy środowisko IoT Edge uruchomieniowe.

   ```bash
   sudo iotedge system status
   ```

Zbadaj dzienniki demona.

   ```cmd/sh
   sudo iotedge system logs
   ```

Jeśli występują błędy aprowowania, może się okazać, że zmiany konfiguracji nie zostały jeszcze wprowadzone. Spróbuj ponownie uruchomić IoT Edge demona.

   ```bash
   sudo systemctl daemon-reload
   ```

Możesz też spróbować ponownie uruchomić maszynę wirtualną, aby sprawdzić, czy zmiany zaczynają obowiązywać od nowa.
:::moniker-end
<!-- end 1.2 -->

Jeśli środowisko uruchomieniowe zostało uruchomione pomyślnie, możesz przejść do IoT Hub i zobaczyć, że nowe urządzenie zostało automatycznie aprowowane. Teraz urządzenie jest gotowe do uruchamiania IoT Edge modułów.

Lista uruchomionych modułów.

```cmd/sh
iotedge list
```

Możesz sprawdzić, czy została użyta rejestracja indywidualna utworzona w usłudze Device Provisioning Service. Przejdź do wystąpienia usługi Device Provisioning Service w Azure Portal. Otwórz szczegóły rejestracji indywidualnej utworzonej przez Ciebie. Zwróć uwagę, że stan rejestracji jest **przypisany i** jest na liście identyfikator urządzenia.

## <a name="next-steps"></a>Następne kroki

Proces rejestracji usługi DPS umożliwia ustawienie identyfikatora urządzenia i tagów bliźniaczej reprezentacji urządzenia w tym samym czasie, gdy aprowizuje się nowe urządzenie. Możesz użyć tych wartości, aby kierować je do poszczególnych urządzeń lub grup urządzeń przy użyciu automatycznego zarządzania urządzeniami. Dowiedz się, jak [wdrażać i monitorować moduły IoT Edge na](how-to-deploy-at-scale.md) dużą skalę przy użyciu interfejsu Azure Portal lub [interfejsu wiersza polecenia platformy Azure.](how-to-deploy-cli-at-scale.md)
