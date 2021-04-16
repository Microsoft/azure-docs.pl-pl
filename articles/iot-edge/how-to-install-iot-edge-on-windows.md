---
title: Instalowanie Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Azure IoT Edge instalacji na urządzeniach z systemem Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2799e25dbd84ff07b375c6fa1b103789aae82b49
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538426"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Instalowanie i aprowizacja usługi IoT Edge dla systemu Linux w systemie Windows (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Środowisko Azure IoT Edge uruchomieniowe zmienia urządzenie w IoT Edge uruchomieniowe. Środowisko uruchomieniowe można wdrożyć na urządzeniach od klasy komputera po serwery przemysłowe. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury. Aby dowiedzieć się więcej, zobacz Understand the Azure IoT Edge runtime and its architecture (Informacje o [środowisku uruchomieniowym Azure IoT Edge i jego architekturze).](iot-edge-runtime.md)

Azure IoT Edge dla systemu Linux w systemie Windows umożliwia korzystanie z usługi Azure IoT Edge na urządzeniach z systemem Windows przy użyciu maszyn wirtualnych z systemem Linux. Wersja systemu Linux usługi Azure IoT Edge i wszystkie moduły systemu Linux wdrożone wraz z nim są uruchamiane na maszynie wirtualnej. Z tego miejsca aplikacje i kod systemu Windows oraz IoT Edge uruchomieniowe i moduły mogą swobodnie wchodzić ze sobą w interakcje.

W tym artykule przedstawiono procedurę IoT Edge na urządzeniu z systemem Windows. Te kroki obejmują wdrożenie maszyny wirtualnej z systemem Linux zawierającej środowisko uruchomieniowe IoT Edge do uruchomienia na urządzeniu z systemem Windows, a następnie aprowizowania urządzenia przy użyciu jego IoT Hub tożsamości urządzenia.

>[!NOTE]
>IoT Edge dla systemu Linux w systemie Windows jest w publicznej [wersji zapoznawczej.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)
>
>Chociaż IoT Edge dla systemu Linux w systemie Windows jest zalecanym środowiskiem korzystania z usługi Azure IoT Edge w środowisku systemu Windows, kontenery systemu Windows są nadal dostępne. Jeśli wolisz używać kontenerów systemu Windows, zobacz przewodnik z instalowania kontenerów systemu Windows i zarządzania [Azure IoT Edge nimi.](how-to-install-iot-edge-windows-on-windows.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z ważną subskrypcją. Jeśli nie masz subskrypcji platformy [Azure,](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

* Warstwa Bezpłatna lub Standardowa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) platformie Azure.

* Urządzenie z systemem Windows z następującymi minimalnymi wymaganiami systemowymi:

  * Windows 10 wersji 1809 lub nowszej; kompilacja 17763 lub nowszy
  * Wersje Professional, Enterprise lub Server
  * Minimalna ilość wolnej pamięci: 1 GB
  * Minimalna ilość wolnego miejsca na dysku: 10 GB
  * Jeśli tworzysz nowe wdrożenie przy użyciu Windows 10, upewnij się, że włączyć funkcję Hyper-V. Aby uzyskać więcej informacji, zobacz jak [zainstalować funkcji Hyper-V na Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).
  * Jeśli tworzysz nowe wdrożenie przy użyciu systemu Windows Server, upewnij się, że instalujesz rolę funkcji Hyper-V. Aby uzyskać więcej informacji, zobacz jak [zainstalować rolę funkcji Hyper-V w systemie Windows Server.](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  * Jeśli tworzysz nowe wdrożenie przy użyciu maszyny wirtualnej, upewnij się, że wirtualizacja zagnieżdżona jest poprawnie skonfigurowana. Aby uzyskać więcej informacji, zobacz przewodnik [wirtualizacji zagnieżdżonych.](nested-virtualization.md)

* Dostęp do Windows Admin Center z zainstalowanym rozszerzeniem Azure IoT Edge dla Windows Admin Center:

   1. Pobierz [instalatora Windows Admin Center programu](https://aka.ms/wacdownload).

   1. Uruchom pobranego instalatora i postępuj zgodnie z monitami kreatora instalacji, aby zainstalować Windows Admin Center. 

   1. Po zainstalowaniu użyj obsługiwanej przeglądarki, aby otworzyć Windows Admin Center. Obsługiwane przeglądarki obejmują Microsoft Edge (Windows 10, wersja 1709 lub nowsza), Google Chrome i Microsoft Edge Insider.

   1. Przy pierwszym użyciu Windows Admin Center zostanie wyświetlony monit o wybranie certyfikatu do użycia. Wybierz **Windows Admin Center Klienta** jako certyfikat.

   1. Na tym czas zainstaluj rozszerzenie Azure IoT Edge . Wybierz ikonę koła zębatego w prawym górnym rogu pulpitu Windows Admin Center nawigacyjnego.

      ![Wybierz ikonę koła zębatego w prawym górnym rogu pulpitu nawigacyjnego, aby uzyskać dostęp do ustawień.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. W menu **Ustawienia** w obszarze **Brama** wybierz **pozycję Rozszerzenia.**

   1. Na **karcie Dostępne rozszerzenia** znajdź **Azure IoT Edge** na liście rozszerzeń. Wybierz go, a **następnie** wybierz wiersz instalacji nad listą rozszerzeń.

   1. Po zakończeniu instalacji na liście zainstalowanych rozszerzeń na Azure IoT Edge rozszerzeniach powinna być wyświetlona lista **zainstalowanych** rozszerzeń.

## <a name="choose-your-provisioning-method"></a>Wybieranie metody aprowizowania

Azure IoT Edge dla systemu Linux w systemie Windows obsługuje następujące metody aprowizowania:

* Aprowizowanie ręczne przy IoT Edge parametrów połączenia urządzenia. Aby użyć tej metody, zarejestruj urządzenie i pobierz ciąg połączenia, korzystając z procedury opisanej IoT Edge rejestracji urządzenia [w IoT Hub](how-to-register-device.md).
  * Wybierz opcję uwierzytelniania za pomocą klucza symetrycznego, ponieważ certyfikaty Z podpisem własnym X.509 nie są obecnie obsługiwane przez program IoT Edge dla systemu Linux w systemie Windows.
* Automatyczna aprowria przy użyciu usługi Device Provisioning Service (DPS) i kluczy symetrycznych. Dowiedz się więcej [na temat tworzenia i aprowizowania urządzenia IoT Edge przy użyciu usługi DPS i kluczy symetrycznych.](how-to-auto-provision-symmetric-keys.md)
* Automatyczne aprowizowanie przy użyciu certyfikatów DPS i X.509. Dowiedz się więcej na temat tworzenia i aprowizowania urządzenia IoT Edge przy użyciu certyfikatów DPS i [X.509.](how-to-auto-provision-x509-certs.md)

Ręczne aprowizowanie jest łatwiejsze do rozpoczęcia pracy z kilkoma urządzeniami. Usługa Device Provisioning Service jest przydatna do aprowizowania wielu urządzeń.

Jeśli planujesz aprowizować urządzenie lub urządzenia przy użyciu jednej z metod usługi DPS, wykonaj kroki opisane w odpowiednim artykule połączonym powyżej, aby utworzyć wystąpienie usługi DPS, połączyć wystąpienie usługi DPS z usługą IoT Hub i utworzyć rejestrację usługi DPS. Możesz utworzyć *rejestrację indywidualną* dla pojedynczego urządzenia lub *rejestrację grupową* dla grupy urządzeń. Aby uzyskać więcej informacji na temat typów rejestracji, odwiedź [stronę pojęcia Azure IoT Hub Device Provisioning Service.](../iot-dps/concepts-service.md#enrollment)

## <a name="create-a-new-deployment"></a>Tworzenie nowego wdrożenia

Utwórz wdrożenie aplikacji dla Azure IoT Edge Linux w systemie Windows na urządzeniu docelowym.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Na Windows Admin Center startowej pod listą połączeń zobaczysz połączenie hosta lokalnego reprezentujące komputer, na którym jest uruchomiony Windows Admin Center. W tym miejscu będą również wyświetlane dodatkowe serwery, komputery lub klastry, które można zarządzać.

Za pomocą usługi Windows Admin Center można instalować aplikacje i zarządzać nimi Azure IoT Edge systemem Linux w systemie Windows na urządzeniu lokalnym lub urządzeniach zarządzanych zdalnie. W tym przewodniku połączenie hosta lokalnego będzie służyć jako urządzenie docelowe do wdrażania usługi Azure IoT Edge dla systemu Linux w systemie Windows.

Jeśli chcesz wdrożyć na zdalnym urządzeniu docelowym zamiast na urządzeniu lokalnym i nie widzisz żądanego urządzenia docelowego na liście, postępuj zgodnie z instrukcjami, aby [dodać urządzenie.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters).

   ![Początkowy Windows Admin Center nawigacyjny z urządzeniem docelowym na liście](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Wybierz pozycję **Dodaj**.

1. W **okienku Dodawanie lub tworzenie zasobów** znajdź **kafelek Azure IoT Edge** zasobów. Wybierz **pozycję Utwórz nową,** aby zainstalować nowe Azure IoT Edge dla systemu Linux w systemie Windows na urządzeniu.

   Jeśli masz już system IoT Edge Linux w systemie Windows uruchomionym na urządzeniu, możesz wybrać pozycję **Dodaj,** aby nawiązać połączenie z istniejącym urządzeniem IoT Edge i zarządzać nim za pomocą Windows Admin Center.

   ![Wybierz pozycję Utwórz nowy na Azure IoT Edge kafelku w Windows Admin Center](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Zostanie **otwarte okienko Azure IoT Edge dla systemu Linux w systemie Windows.** Na **stronie 1. Wprowadzenie** sprawdź, czy urządzenie docelowe spełnia minimalne wymagania, a następnie wybierz pozycję **Dalej.**

1. Przejrzyj postanowienia licencyjne, zaznacz pozycję **Akceptuję** i wybierz pozycję **Dalej.**

1. W zależności od **preferencji** można włączyć lub wyłączyć opcjonalne dane diagnostyczne.

1. Wybierz **pozycję Dalej: Wd wdrażaj**.

   ![Wybierz przycisk Dalej: Wd wdrażaj po przegłoceniu opcjonalnych danych diagnostycznych do swoich preferencji](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Na **stronie 2. Na** karcie Wdrażanie w **obszarze Wybierz urządzenie docelowe** kliknij urządzenie na liście, aby sprawdzić, czy spełnia ono minimalne wymagania. Po potwierdzeniu, że stan jest obsługiwany, wybierz pozycję **Dalej.**

   ![Wybierz urządzenie, aby sprawdzić, czy jest obsługiwane](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Na karcie **Ustawienia 2.2** przejrzyj ustawienia konfiguracji wdrożenia. Gdy ustawienia będą zadowalać, wybierz pozycję **Dalej.**

   ![Przejrzyj ustawienia konfiguracji wdrożenia](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Jeśli używasz maszyny wirtualnej z systemem Windows, zaleca się użycie przełącznika domyślnego, a nie przełącznika zewnętrznego, aby upewnić się, że maszyna wirtualna z systemem Linux utworzona we wdrożeniu może uzyskać adres IP.
   >
   >Użycie przełącznika domyślnego przypisuje maszynie wirtualnej z systemem Linux wewnętrzny adres IP. Nie można uzyskać dostępu do tego wewnętrznego adresu IP spoza maszyny wirtualnej z systemem Windows, ale można go połączyć lokalnie po zalogowaniu się na maszynie wirtualnej z systemem Windows.
   >
   >Jeśli używasz systemu Windows Server, pamiętaj, że program Azure IoT Edge dla systemu Linux w systemie Windows nie obsługuje automatycznie przełącznika domyślnego. W przypadku lokalnej maszyny wirtualnej z systemem Windows Server upewnij się, że maszyna wirtualna z systemem Linux może uzyskać adres IP za pośrednictwem przełącznika zewnętrznego. W przypadku maszyny wirtualnej z systemem Windows Server na platformie Azure skonfiguruj przełącznik wewnętrzny przed wdrożeniem IoT Edge dla systemu Linux w systemie Windows.

1. Na karcie **2.3 Deployment** (Wdrożenie) możesz obserwować postęp wdrażania. Pełny proces obejmuje pobranie pakietu usługi Azure IoT Edge dla systemu Linux w systemie Windows, zainstalowanie pakietu, skonfigurowanie urządzenia hosta i skonfigurowanie maszyny wirtualnej z systemem Linux. Ten proces może potrwać kilka minut. Poniżej przedstawiono pomyślne wdrożenie.

   ![Po pomyślnym wdrożeniu każdy krok zostanie oznaczony zielonym znakiem wyboru i etykietą "Complete" (Ukończono).](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Po zakończeniu wdrażania możesz rozpocząć aprowizować urządzenie. Wybierz **pozycję Dalej: Połącz,** aby przejść do **3. Karta** Połącz, która obsługuje Azure IoT Edge aprowizowania urządzeń.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Zainstaluj IoT Edge dla systemu Linux w systemie Windows na urządzeniu docelowym, jeśli nie zostało to jeszcze zainstalowane.

> [!NOTE]
> W poniższym procesie programu PowerShell opisano sposób tworzenia lokalnego wdrożenia hosta usługi Azure IoT Edge dla systemu Linux w systemie Windows. Aby utworzyć wdrożenie na zdalnym urządzeniu docelowym przy użyciu programu PowerShell, można użyć programu Remote [PowerShell,](/powershell/module/microsoft.powershell.core/about/about_remote) aby nawiązać połączenie z urządzeniem zdalnym i zdalnie uruchomić te polecenia na tym urządzeniu.

1. W sesji programu PowerShell z podwyższonym poziomem uprawnień uruchom każde z następujących poleceń, aby pobrać IoT Edge dla systemu Linux w systemie Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Zainstaluj IoT Edge dla systemu Linux w systemie Windows na urządzeniu.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Można określić niestandardowe IoT Edge dla systemu Linux w systemie Windows instalacji i katalogów VHDX, dodając INSTALLDIR ="<FULLY_QUALIFIED_PATH>" i VHDXDIR ="<FULLY_QUALIFIED_PATH>" parametry do polecenia instalacji powyżej.

1. Aby wdrożenie zostało pomyślnie uruchomione, należy ustawić zasady wykonywania na urządzeniu docelowym na wartość `AllSigned` , jeśli jeszcze nie zostało ono uruchomione. Bieżące zasady wykonywania można sprawdzić w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień, używając polecenia:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Jeśli zasady wykonywania programu `local machine` nie mają ustawienia , możesz ustawić zasady wykonywania przy `AllSigned` użyciu:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Utwórz nową IoT Edge dla systemu Linux w systemie Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > To polecenie można uruchomić bez parametrów lub opcjonalnie dostosować wdrożenie za pomocą parametrów. Aby zapoznać się z znaczeniami parametrów i wartościami domyślnymi, można IoT Edge w skrypcie systemu [Linux](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) w skrypcie systemu Windows PowerShell linux.

1. Wprowadź wartość "Y", aby zaakceptować postanowienia licencyjne.

1. Wprowadź wartość "O" lub "R", aby włączyć lub wyłączyć opcjonalne dane diagnostyczne w zależności od preferencji.  Poniżej przedstawiono pomyślne wdrożenie.

   ![Pomyślne wdrożenie będzie kończyć się komunikatem "Wdrożenie zakończyło się pomyślnie" na końcu komunikatów](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Po zakończeniu wdrażania możesz rozpocząć aprowizować urządzenie.

---

Aby aprowizować urządzenie, możesz użyć poniższych linków, aby przejść do sekcji wybranej metody aprowacji:

* [Opcja 1. Ręczne aprowizowanie przy użyciu IoT Edge parametrów połączenia urządzenia](#option-1-provisioning-manually-using-the-connection-string)
* [Opcja 2. Automatyczne aprowizowanie przy użyciu usługi Device Provisioning Service (DPS) i kluczy symetrycznych](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Opcja 3. Automatyczne aprowizowanie przy użyciu certyfikatów DPS i X.509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Aprowizować urządzenie

Wybierz metodę aprowizowania urządzenia i postępuj zgodnie z instrukcjami w odpowiedniej sekcji. Aby aprowizować urządzenia Windows Admin Center można użyć sesji programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Opcja 1. Ręczne aprowizowanie przy użyciu parametrów połączenia

Ta sekcja obejmuje ręczne aprowizowanie urządzenia przy użyciu Azure IoT Edge parametrów połączenia urządzenia.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. W **okienku Azure IoT Edge aprowizowania urządzeń** wybierz pozycję **Connection String (Manual) (Ciąg połączenia (ręcznie)** z listy rozwijanej metody inicjowania obsługi.

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do **IoT Edge** karty IoT Hub.

1. Kliknij identyfikator urządzenia. Skopiuj pole **Podstawowe ciągi** połączenia.

1. Wklej go w polu parametrów połączenia urządzenia w Windows Admin Center. Następnie wybierz pozycję **Aprowizowanie przy użyciu wybranej metody**.

   ![Wybierz aprowizowanie przy użyciu wybranej metody po wklejeniu parametrów połączenia urządzenia](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Po zakończeniu aprowizowania wybierz pozycję **Zakończ.** Wrócisz do głównego pulpitu nawigacyjnego. Teraz na liście powinno zostać wyświetlone nowe urządzenie, którego typ to `IoT Edge Devices` . Możesz wybrać urządzenie IoT Edge, aby się z nim połączyć. Na stronie **Przegląd** można wyświetlić listę modułów **IoT Edge** i **IoT Edge stan** urządzenia.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do **IoT Edge** karty IoT Hub.

1. Kliknij identyfikator urządzenia. Skopiuj pole **Podstawowe ciągi** połączenia.

1. Wklej tekst zastępczy w poniższym poleceniu i uruchom go w sesji programu PowerShell z podwyższonym poziomem uprawnień na urządzeniu docelowym.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Opcja 2. Aprowizowanie za pośrednictwem usługi DPS przy użyciu kluczy symetrycznych

Ta sekcja obejmuje automatyczną aprowizowanie urządzenia przy użyciu usługi DPS i kluczy symetrycznych.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. W **okienku Azure IoT Edge aprowizowania urządzeń** wybierz pozycję **Klucz symetryczny (DPS)** z listy rozwijanej metody aprowacji.

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia usługi DPS.

1. Na karcie **Przegląd** skopiuj wartość **Zakres** identyfikatorów. Wklej go w polu identyfikator zakresu w Windows Admin Center.

1. Na karcie **Zarządzanie rejestracjami** w Azure Portal wybierz utworzoną rejestrację. Skopiuj wartość **Klucz podstawowy** w szczegółach rejestracji. Wklej go w polu klucza symetrycznego w Windows Admin Center.

1. Podaj identyfikator rejestracji urządzenia w polu Identyfikator rejestracji w Windows Admin Center.

1. Wybierz **pozycję Aprowizowanie przy użyciu wybranej metody**.

   ![Wybierz aprowizowanie przy użyciu wybranej metody po wypełnieniu pól wymaganych do aprowizowania kluczy symetrycznych](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Po zakończeniu aprowizowania wybierz pozycję **Zakończ.** Powrócisz do głównego pulpitu nawigacyjnego. Teraz powinno zostać wyświetlone nowe urządzenie o typie `IoT Edge Devices` . Możesz wybrać urządzenie IoT Edge, aby się z nim połączyć. Na stronie **Przegląd** można wyświetlić  listę modułów IoT Edge i **IoT Edge stan** urządzenia.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Skopiuj następujące polecenie do edytora tekstów. Zastąp tekst zastępczy informacjami zgodnie ze szczegółowymi informacjami.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia usługi DPS.

1. Na karcie **Przegląd** skopiuj wartość **Zakres** identyfikatorów. Wklej go do odpowiedniego tekstu zastępczego w poleceniu .

1. Na karcie **Zarządzanie rejestracjami** w Azure Portal wybierz utworzoną rejestrację. Skopiuj wartość **Klucz podstawowy** w szczegółach rejestracji. Wklej go do odpowiedniego tekstu zastępczego w poleceniu .

1. Podaj identyfikator rejestracji urządzenia, aby zastąpić odpowiedni tekst zastępczy w poleceniu.

1. Uruchom polecenie w sesji programu PowerShell z podwyższonym poziomem uprawnień na urządzeniu docelowym.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Opcja 3. Aprowizowanie za pośrednictwem usługi DPS przy użyciu certyfikatów X.509

Ta sekcja obejmuje automatyczną aprowizowanie urządzenia przy użyciu certyfikatów DPS i X.509.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. W **okienku Azure IoT Edge** aprowizowania urządzeń wybierz pozycję **X.509 Certificate (DPS) (Certyfikat X.509 (DPS) z** listy rozwijanej metody inicjowania obsługi.

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia usługi DPS.

1. Na karcie **Przegląd** skopiuj wartość **Zakres** identyfikatorów. Wklej go w polu identyfikator zakresu w Windows Admin Center.

1. Podaj identyfikator rejestracji urządzenia w polu identyfikator rejestracji w Windows Admin Center.

1. Przekaż plik certyfikatu i klucza prywatnego.

1. Wybierz **pozycję Aprowizowanie przy użyciu wybranej metody**.

   ![Wybierz aprowizowanie przy użyciu wybranej metody po wypełnieniu pól wymaganych do aprowowania certyfikatów X.509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Po zakończeniu aprowizowania wybierz pozycję **Zakończ.** Wrócisz do głównego pulpitu nawigacyjnego. Teraz na liście powinno zostać wyświetlone nowe urządzenie, którego typ to `IoT Edge Devices` . Możesz wybrać urządzenie IoT Edge, aby się z nim połączyć. Na stronie **Przegląd** możesz wyświetlić  listę modułów IoT Edge i **IoT Edge stan** urządzenia.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Skopiuj następujące polecenie do edytora tekstów. Zastąp tekst zastępczy informacjami zgodnie ze szczegółowymi informacjami.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia usługi DPS.

1. Na karcie **Przegląd** skopiuj wartość **Zakres** identyfikatorów. Wklej go do odpowiedniego tekstu zastępczego w poleceniu .

1. Podaj identyfikator rejestracji urządzenia, aby zastąpić odpowiedni tekst zastępczy w poleceniu.

1. Zastąp odpowiedni tekst zastępczy bezwzględną ścieżką źródłową do pliku certyfikatu.

1. Zastąp odpowiedni tekst zastępczy bezwzględną ścieżką źródłową do pliku klucza prywatnego.

1. Uruchom polecenie w sesji programu PowerShell z podwyższonym poziomem uprawnień na urządzeniu docelowym.

---

## <a name="verify-successful-configuration"></a>Weryfikowanie pomyślnej konfiguracji

Sprawdź, IoT Edge systemu Linux w systemie Windows został pomyślnie zainstalowany i skonfigurowany na IoT Edge urządzeniu.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Wybierz urządzenie IoT Edge z listy połączonych urządzeń na Windows Admin Center, aby się z nim połączyć.

1. Na stronie przeglądu urządzenia są wyświetlane pewne informacje o urządzeniu:

    1. Sekcja **IoT Edge Lista modułów zawiera** uruchomione moduły na urządzeniu. Po pierwszym uruchomieniu IoT Edge edgeAgent powinien zostać wyświetlony tylko uruchomiony moduł **edgeAgent.** Moduł edgeAgent jest uruchamiany domyślnie i pomaga zainstalować i uruchomić wszelkie dodatkowe moduły wdrażane na urządzeniu.
    1. W **IoT Edge stan usługi** powinien być aktywny **(uruchomiony).**

1. Jeśli musisz rozwiązać problemy z usługą IoT Edge, użyj narzędzia **powłoki** poleceń na stronie urządzenia, aby na maszynie wirtualnej użyć programu ssh (secure shell) i uruchomić polecenia systemu Linux.

    1. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

       ```bash
       journalctl -u iotedge
       ```

    2. Użyj narzędzia `check` , aby zweryfikować konfigurację i stan połączenia urządzenia.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Zaloguj się do maszyny IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows przy użyciu następującego polecenia w sesji programu PowerShell:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >Jedynym kontem, które może natworzyć z maszyną wirtualną za pomocą SSH, jest użytkownik, który ją utworzył.

1. Po zalogowaniu się możesz sprawdzić listę uruchomionych modułów IoT Edge pomocą następującego polecenia systemu Linux:

   ```bash
   iotedge list
   ```

1. Jeśli musisz rozwiązać problemy z usługą IoT Edge, użyj następujących poleceń systemu Linux.

    1. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

       ```bash
       journalctl -u iotedge
       ```

    2. Użyj narzędzia `check` , aby zweryfikować konfigurację i stan połączenia urządzenia.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Następne kroki

* Kontynuuj wdrażanie [IoT Edge,](how-to-deploy-modules-portal.md) aby dowiedzieć się, jak wdrażać moduły na urządzeniu.
* Dowiedz się, [jak zarządzać](how-to-manage-device-certificates.md) certyfikatami na maszynie IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows i przesyłać pliki z systemu operacyjnego hosta na maszynę wirtualną z systemem Linux.
* Dowiedz się, [jak skonfigurować urządzenia IoT Edge do komunikowania się za pośrednictwem serwera proxy.](how-to-configure-proxy-support.md)
