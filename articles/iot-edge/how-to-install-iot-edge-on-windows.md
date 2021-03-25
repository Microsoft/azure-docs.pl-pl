---
title: Instalowanie Azure IoT Edge dla systemu Linux w systemie Windows | Microsoft Docs
description: Instrukcje dotyczące instalacji Azure IoT Edge na urządzeniach z systemem Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: c291bb701aca385a11631504f1a312b25cced633
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045675"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Instalowanie i aprowizacja usługi IoT Edge dla systemu Linux w systemie Windows (wersja zapoznawcza)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Środowisko uruchomieniowe Azure IoT Edge to włączenie urządzenia do urządzenia IoT Edge. Środowisko uruchomieniowe można wdrożyć na urządzeniach z klasy komputerów na serwerach przemysłowych. Po skonfigurowaniu urządzenia przy użyciu środowiska uruchomieniowego usługi IoT Edge można rozpocząć wdrażanie na nim logiki biznesowej z chmury. Aby dowiedzieć się więcej, zobacz [Omówienie środowiska uruchomieniowego Azure IoT Edge i jego architektury](iot-edge-runtime.md).

Azure IoT Edge dla systemu Linux w systemie Windows umożliwia korzystanie z Azure IoT Edge na urządzeniach z systemem Windows przy użyciu maszyn wirtualnych z systemem Linux. Wersja systemu Linux Azure IoT Edge i wszystkie moduły systemu Linux wdrożone z tym działaniem na maszynie wirtualnej. Z tego miejsca aplikacje i kod systemu Windows oraz środowisko uruchomieniowe IoT Edge i moduły mogą swobodnie współdziałać ze sobą.

W tym artykule przedstawiono procedurę konfigurowania IoT Edge na urządzeniu z systemem Windows. Te kroki umożliwiają wdrożenie maszyny wirtualnej z systemem Linux zawierającej środowisko uruchomieniowe IoT Edge do uruchomienia na urządzeniu z systemem Windows, a następnie zainicjowanie urządzenia przy użyciu jego tożsamości urządzenia IoT Hub.

>[!NOTE]
>IoT Edge dla systemu Linux w systemie Windows jest w [publicznej wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Podczas gdy IoT Edge dla systemu Linux w systemie Windows to zalecane środowisko korzystania z Azure IoT Edge w środowisku systemu Windows, kontenery systemu Windows są nadal dostępne. Jeśli wolisz korzystać z kontenerów systemu Windows, zapoznaj się z tematem Przewodnik po [instalowaniu i zarządzaniu Azure IoT Edge dla systemu Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z prawidłową subskrypcją. Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/) .

* Warstwa Bezpłatna lub standardowa [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) na platformie Azure.

* Urządzenie z systemem Windows z następującymi minimalnymi wymaganiami systemowymi:

  * Windows 10 w wersji 1809 lub nowszej; Kompilacja 17763 lub nowsza
  * Wersje Professional, Enterprise i Server
  * Minimalna pamięć RAM: 4 GB (zalecane 8 GB)
  * Minimalny magazyn: 10 GB
  * Jeśli tworzysz nowe wdrożenie przy użyciu systemu Windows 10, upewnij się, że włączona jest funkcja Hyper-V. Aby uzyskać więcej informacji, zobacz jak [zainstalować funkcję Hyper-V w systemie Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v.md).
  * Jeśli tworzysz nowe wdrożenie przy użyciu systemu Windows Server, upewnij się, że zainstalowano rolę funkcji Hyper-V. Aby uzyskać więcej informacji, zobacz jak [zainstalować rolę funkcji Hyper-V w systemie Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server.md).
  * Jeśli tworzysz nowe wdrożenie przy użyciu maszyny wirtualnej, upewnij się, że zawarto prawidłowo skonfigurować wirtualizację zagnieżdżoną. Aby uzyskać więcej informacji, zobacz [zagnieżdżony Przewodnik wirtualizacji](nested-virtualization.md) .

* Dostęp do centrum administracyjnego systemu Windows z zainstalowanym rozszerzeniem Azure IoT Edge dla centrum administracyjnego systemu Windows:

   1. Pobierz [Instalatora centrum administracyjnego systemu Windows](https://aka.ms/wacdownload).

   1. Uruchom pobrany Instalator i postępuj zgodnie z monitami Kreatora instalacji, aby zainstalować centrum administracyjne systemu Windows. 

   1. Po zainstalowaniu programu Użyj obsługiwanej przeglądarki, aby otworzyć Centrum administracyjne systemu Windows. Obsługiwane przeglądarki to Microsoft Edge (system Windows 10, wersja 1709 lub nowsza), Google Chrome i Microsoft Edge.

   1. Przy pierwszym użyciu Centrum administracyjnego systemu Windows zostanie wyświetlony monit o wybranie certyfikatu do użycia. Wybierz pozycję **Klient centrum administracyjnego systemu Windows** jako certyfikat.

   1. Jest to czas na zainstalowanie rozszerzenia Azure IoT Edge. Wybierz ikonę koła zębatego w prawym górnym rogu pulpitu nawigacyjnego Centrum administracyjnego systemu Windows.

      ![Wybierz ikonę koła zębatego w prawym górnym rogu pulpitu nawigacyjnego, aby uzyskać dostęp do ustawień.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. W menu **Ustawienia** w obszarze **brama** wybierz pozycję **rozszerzenia**.

   1. Na karcie **dostępne rozszerzenia** Znajdź **Azure IoT Edge** na liście rozszerzeń. Wybierz go, a następnie wybierz polecenie **Zainstaluj** wiersz powyżej listy rozszerzeń.

   1. Po zakończeniu instalacji na karcie **zainstalowane rozszerzenia** powinna zostać wyświetlona Azure IoT Edge na liście zainstalowanych rozszerzeń.

## <a name="choose-your-provisioning-method"></a>Wybierz metodę aprowizacji

Azure IoT Edge dla systemu Linux w systemie Windows obsługuje następujące metody aprowizacji:

* Ręczne inicjowanie obsługi przy użyciu parametrów połączenia urządzenia IoT Edge. Aby użyć tej metody, należy zarejestrować urządzenie i pobrać parametry połączenia, wykonując kroki opisane w temacie [Rejestrowanie urządzenia IoT Edge w IoT Hub](how-to-register-device.md).
  * Wybierz opcję uwierzytelniania przy użyciu klucza symetrycznego, ponieważ certyfikaty z podpisem własnym X. 509 nie są obecnie obsługiwane przez IoT Edge dla systemu Linux w systemie Windows.
* Automatyczna obsługa administracyjna przy użyciu usługi Device Provisioning Service (DPS) i kluczy symetrycznych. Dowiedz się więcej o [tworzeniu i aprowizacji urządzenia IoT Edge przy użyciu funkcji DPS i kluczy symetrycznych](how-to-auto-provision-symmetric-keys.md).
* Automatyczne Inicjowanie obsługi przy użyciu certyfikatów usługi DPS i X. 509. Dowiedz się więcej o [tworzeniu i aprowizacji urządzenia IoT Edge przy użyciu certyfikatów DPS i X. 509](how-to-auto-provision-x509-certs.md).

Ręczne inicjowanie obsługi jest łatwiejsze, aby rozpocząć pracę z kilkoma urządzeniami. Usługa Device Provisioning jest przydatna do aprowizacji wielu urządzeń.

Jeśli planujesz użycie jednej z metod usługi DPS do aprowizacji urządzenia lub urządzeń, wykonaj kroki opisane w odpowiednim artykule powiązanym powyżej, aby utworzyć wystąpienie punktu dystrybucji, Połącz wystąpienie usługi DPS z IoT Hub i Utwórz rejestrację usługi DPS. Można utworzyć *rejestrację indywidualną* dla jednego urządzenia lub *rejestracji grupy* dla grupy urządzeń. Aby uzyskać więcej informacji na temat typów rejestracji, odwiedź [pojęcia dotyczące usługi Azure IoT Hub Device Provisioning Service](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Utwórz nowe wdrożenie

Utwórz wdrożenie Azure IoT Edge dla systemu Linux w systemie Windows na urządzeniu docelowym.

# <a name="windows-admin-center"></a>[Centrum administracyjne systemu Windows](#tab/windowsadmincenter)

Na stronie Start Center centrum administracyjnego systemu Windows w obszarze Lista połączeń zostanie wyświetlone połączenie hosta lokalnego reprezentujące komputer z uruchomionym centrum administracyjnym systemu Windows. Wszystkie dodatkowe serwery, komputery lub klastry, którymi zarządzasz, również zostaną wyświetlone tutaj.

Centrum administracyjne systemu Windows umożliwia instalację i zarządzanie Azure IoT Edge dla systemu Linux w systemie Windows na urządzeniu lokalnym lub urządzeniach zarządzanych zdalnie. W tym przewodniku połączenie hosta lokalnego będzie działać jako urządzenie docelowe wdrożenia Azure IoT Edge dla systemu Linux w systemie Windows.

Jeśli chcesz wdrożyć na zdalnym urządzeniu docelowym zamiast na urządzeniu lokalnym, a na liście nie widzisz żądanego urządzenia docelowego, postępuj zgodnie z [instrukcjami, aby dodać urządzenie.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![Początkowy pulpit nawigacyjny Centrum administracyjnego systemu Windows z urządzeniem docelowym na liście](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Wybierz pozycję **Dodaj**.

1. W okienku **Dodawanie lub tworzenie zasobów** znajdź kafelek **Azure IoT Edge** . Wybierz pozycję **Utwórz nowy** , aby zainstalować nowe wystąpienie Azure IoT Edge dla systemu Linux w systemie Windows na urządzeniu.

   Jeśli masz już IoT Edge dla systemu Linux w systemie Windows uruchomionym na urządzeniu, możesz wybrać opcję **Dodaj** , aby nawiązać połączenie z istniejącym urządzeniem IoT Edge i zarządzać nim za pomocą Centrum administracyjnego systemu Windows.

   ![Wybierz pozycję Utwórz nowe na kafelku Azure IoT Edge w centrum administracyjnym systemu Windows](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. Zostanie otwarte okienko **utwórz Azure IoT Edge dla systemu Linux w systemie Windows** . Na **1.** Upewnij się, że na karcie wprowadzenie znajduje się urządzenie docelowe spełniające minimalne wymagania, a następnie wybierz przycisk **dalej**.

1. Zapoznaj się z postanowieniami licencyjnymi, zaznacz pole wyboru **Akceptuję** i wybierz pozycję **dalej**.

1. Możesz włączyć lub wyłączyć **opcjonalne dane diagnostyczne** , w zależności od preferencji.

1. Wybierz pozycję **Dalej: Wdróż**.

   ![Wybierz przycisk Dalej: Wdróż po przełączeniu opcjonalnych danych diagnostycznych na preferencję](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. Na **2.** Na karcie wdrażanie w obszarze **Wybierz urządzenie docelowe** kliknij urządzenie na liście, aby sprawdzić, czy spełnia ono wymagania minimalne. Po potwierdzeniu jego stanu jako obsługiwanego wybierz pozycję **dalej**.

   ![Wybierz urządzenie, aby sprawdzić, czy jest ono obsługiwane](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Zaakceptuj ustawienia domyślne na karcie **ustawienia 2,2** .

1. Na karcie **wdrożenie 2,3** można obejrzeć postęp wdrażania. Pełny proces obejmuje pobranie Azure IoT Edge dla systemu Linux w pakiecie Windows, zainstalowanie pakietu, skonfigurowanie urządzenia hosta i skonfigurowanie maszyny wirtualnej z systemem Linux. Ten proces może potrwać kilka minut. Pomyślne wdrożenie zostało przedstawione poniżej.

   ![Pomyślne wdrożenie będzie wyświetlać każdy krok z zielonym znacznikiem wyboru i etykietą "Complete"](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Po zakończeniu wdrażania możesz przystąpić do aprowizacji Twojego urządzenia. Wybierz pozycję **Dalej: Połącz** , aby przejoć do **3. Karta łączenie** , która obsługuje Azure IoT Edge aprowizacji urządzeń.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Zainstaluj IoT Edge dla systemu Linux w systemie Windows na urządzeniu docelowym, jeśli jeszcze tego nie zrobiono.

> [!NOTE]
> Poniższy proces programu PowerShell przedstawia sposób tworzenia wdrożenia hosta lokalnego Azure IoT Edge dla systemu Linux w systemie Windows. Aby utworzyć wdrożenie na zdalnym urządzeniu docelowym przy użyciu programu PowerShell, można użyć [zdalnego programu PowerShell](/powershell/module/microsoft.powershell.core/about/about_remote) w celu nawiązania połączenia z urządzeniem zdalnym i uruchamiania tych poleceń zdalnie na tym urządzeniu.

1. W sesji programu PowerShell z podwyższonym poziomem uprawnień uruchom następujące polecenia, aby pobrać IoT Edge dla systemu Linux w systemie Windows.

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
   > Możesz określić niestandardowe IoT Edge dla systemu Linux w katalogach instalacyjnych i dysków VHDX, dodając parametry INSTALLDIR = "<FULLY_QUALIFIED_PATH>" i VHDXDIR = "<FULLY_QUALIFIED_PATH>" w powyższym poleceniu instalacji.

1. Aby wdrożenie zostało pomyślnie uruchomione, należy ustawić zasady wykonywania na urządzeniu docelowym na, `AllSigned` Jeśli nie zostało jeszcze skonfigurowane. Bieżące zasady wykonywania można sprawdzić w wierszu polecenia programu PowerShell z podwyższonym poziomem uprawnień przy użyciu:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Jeśli zasady wykonywania `local machine` nie są `AllSigned` , można ustawić zasady wykonywania przy użyciu:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Utwórz IoT Edge dla systemu Linux w systemie Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Można uruchomić to polecenie bez parametrów lub opcjonalnie dostosować wdrożenie z parametrami. Aby zobaczyć ich znaczenie, można zapoznać się z informacjami o [skrypcie IoT Edge dla systemu Linux w programie Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) .

1. Wprowadź wartość "Y", aby zaakceptować postanowienia licencyjne.

1. Wprowadź "O" lub "R", aby włączyć lub wyłączyć **opcjonalne dane diagnostyczne** , w zależności od preferencji. Pomyślne wdrożenie zostało przedstawione poniżej.

   ![Pomyślne wdrożenie powiedzie się "wdrożenie pomyślne" na końcu komunikatów](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Po zakończeniu wdrażania możesz przystąpić do aprowizacji Twojego urządzenia.

---

Aby udostępnić urządzenie, możesz skorzystać z poniższych linków, aby przejść do sekcji dotyczącej wybranej metody aprowizacji:

* [Opcja 1: Ręczne inicjowanie obsługi przy użyciu parametrów połączenia urządzenia IoT Edge](#option-1-provisioning-manually-using-the-connection-string)
* [Opcja 2: Automatyczna obsługa administracyjna przy użyciu usługi Device Provisioning Service (DPS) i kluczy symetrycznych](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Opcja 3: automatyczne Inicjowanie obsługi przy użyciu certyfikatów usługi DPS i X. 509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Inicjowanie obsługi administracyjnej urządzenia

Wybierz metodę aprowizacji urządzenia i postępuj zgodnie z instrukcjami w odpowiedniej sekcji. Do aprowizacji urządzeń można użyć Centrum administracyjnego systemu Windows lub sesji programu PowerShell z podwyższonym poziomem uprawnień.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Opcja 1: Ręczne inicjowanie obsługi przy użyciu parametrów połączenia

W tej części omówiono Inicjowanie obsługi urządzenia ręcznie przy użyciu parametrów połączenia urządzenia Azure IoT Edge.

# <a name="windows-admin-center"></a>[Centrum administracyjne systemu Windows](#tab/windowsadmincenter)

1. W okienku **Azure IoT Edge Device Provisioning** wybierz pozycję **Parametry połączenia (ręczne)** z listy rozwijanej Metoda aprowizacji.

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do karty **IoT Edge** IoT Hub.

1. Kliknij identyfikator urządzenia na urządzeniu. Skopiuj **podstawowe pole parametrów połączenia** .

1. Wklej ją do pola parametry połączenia urządzenia w centrum administracyjnym systemu Windows. Następnie wybierz opcję **aprowizacji z wybraną metodą**.

   ![Wybierz opcję aprowizacji z wybraną metodą po wklejeniu parametrów połączenia urządzenia](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Po zakończeniu aprowizacji wybierz pozycję **Zakończ**. Nastąpi powrót do głównego pulpitu nawigacyjnego. Teraz powinno pojawić się nowe urządzenie na liście, którego typem jest `IoT Edge Devices` . Możesz wybrać urządzenie IoT Edge, aby nawiązać z nim połączenie. Na stronie **Przegląd** można wyświetlić **listę IoT Edge modułów** i **IoT Edge stan** urządzenia.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do karty **IoT Edge** IoT Hub.

1. Kliknij identyfikator urządzenia na urządzeniu. Skopiuj **podstawowe pole parametrów połączenia** .

1. Wklej tekst zastępczy w poniższym poleceniu i uruchom go w sesji programu PowerShell z podwyższonym poziomem uprawnień na urządzeniu docelowym.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Opcja 2: Inicjowanie obsługi za pośrednictwem usługi DPS przy użyciu kluczy symetrycznych

Ta sekcja obejmuje automatyczne Inicjowanie obsługi urządzenia przy użyciu usługi DPS i kluczy symetrycznych.

# <a name="windows-admin-center"></a>[Centrum administracyjne systemu Windows](#tab/windowsadmincenter)

1. W okienku **Azure IoT Edge Device Provisioning** wybierz pozycję **klucz symetryczny (DPS)** z listy rozwijanej Metoda aprowizacji.

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia DPS.

1. Na karcie **Przegląd** skopiuj wartość **Identyfikator zakresu** . Wklej ją do pola Identyfikator zakresu w centrum administracyjnym systemu Windows.

1. Na karcie **Zarządzanie rejestracjami** w Azure Portal Wybierz utworzoną rejestrację. Skopiuj wartość **klucza podstawowego** ze szczegółowych informacji o rejestracji. Wklej ją do pola klucz symetryczny w centrum administracyjnym systemu Windows.

1. Podaj identyfikator rejestracji urządzenia w polu Identyfikator rejestracji w centrum administracyjnym systemu Windows.

1. Wybierz opcję **aprowizacji z wybraną metodą**.

   ![Wybierz opcję aprowizacji z wybraną metodą po wypełnieniu pól wymaganych do aprowizacji kluczy symetrycznych](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Po zakończeniu aprowizacji wybierz pozycję **Zakończ**. Nastąpi powrót do głównego pulpitu nawigacyjnego. Teraz powinno pojawić się nowe urządzenie na liście, którego typem jest `IoT Edge Devices` . Możesz wybrać urządzenie IoT Edge, aby nawiązać z nim połączenie. Na stronie **Przegląd** można wyświetlić **listę IoT Edge modułów** i **IoT Edge stan** urządzenia.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Skopiuj następujące polecenie do edytora tekstów. Zastąp tekst symbolem zastępczym informacjami w sposób szczegółowy.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia DPS.

1. Na karcie **Przegląd** skopiuj wartość **Identyfikator zakresu** . Wklej ją nad odpowiednim tekstem zastępczym w poleceniu.

1. Na karcie **Zarządzanie rejestracjami** w Azure Portal Wybierz utworzoną rejestrację. Skopiuj wartość **klucza podstawowego** ze szczegółowych informacji o rejestracji. Wklej ją nad odpowiednim tekstem zastępczym w poleceniu.

1. Podaj identyfikator rejestracji urządzenia, aby zastąpić odpowiedni tekst zastępczy w poleceniu.

1. Uruchom polecenie w sesji programu PowerShell z podwyższonym poziomem uprawnień na urządzeniu docelowym.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Opcja 3: Inicjowanie obsługi za pośrednictwem usługi DPS przy użyciu certyfikatów X. 509

Ta sekcja obejmuje automatyczne Inicjowanie obsługi administracyjnej urządzenia przy użyciu certyfikatów usługi DPS i X. 509.

# <a name="windows-admin-center"></a>[Centrum administracyjne systemu Windows](#tab/windowsadmincenter)

1. W okienku **Azure IoT Edge Device Provisioning** wybierz pozycję **certyfikat X. 509 (DPS)** z listy rozwijanej Metoda aprowizacji.

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia DPS.

1. Na karcie **Przegląd** skopiuj wartość **Identyfikator zakresu** . Wklej ją do pola Identyfikator zakresu w centrum administracyjnym systemu Windows.

1. Podaj identyfikator rejestracji urządzenia w polu Identyfikator rejestracji w centrum administracyjnym systemu Windows.

1. Przekaż certyfikat i pliki kluczy prywatnych.

1. Wybierz opcję **aprowizacji z wybraną metodą**.

   ![Wybierz Inicjowanie obsługi przy użyciu wybranej metody po wypełnieniu wymaganych pól dla aprowizacji certyfikatu X. 509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Po zakończeniu aprowizacji wybierz pozycję **Zakończ**. Nastąpi powrót do głównego pulpitu nawigacyjnego. Teraz powinno pojawić się nowe urządzenie na liście, którego typem jest `IoT Edge Devices` . Możesz wybrać urządzenie IoT Edge, aby nawiązać z nim połączenie. Na stronie **Przegląd** można wyświetlić **listę IoT Edge modułów** i **IoT Edge stan** urządzenia.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Skopiuj następujące polecenie do edytora tekstów. Zastąp tekst symbolem zastępczym informacjami w sposób szczegółowy.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. W [Azure Portal](https://ms.portal.azure.com/)przejdź do wystąpienia DPS.

1. Na karcie **Przegląd** skopiuj wartość **Identyfikator zakresu** . Wklej ją nad odpowiednim tekstem zastępczym w poleceniu.

1. Podaj identyfikator rejestracji urządzenia, aby zastąpić odpowiedni tekst zastępczy w poleceniu.

1. Zastąp odpowiedni tekst zastępczy absolutną ścieżką źródłową do pliku certyfikatu.

1. Zastąp odpowiedni tekst zastępczy absolutną ścieżką źródłową do pliku klucza prywatnego.

1. Uruchom polecenie w sesji programu PowerShell z podwyższonym poziomem uprawnień na urządzeniu docelowym.

---

## <a name="verify-successful-configuration"></a>Weryfikuj pomyślną konfigurację

Sprawdź, czy IoT Edge dla systemu Linux w systemie Windows zostały pomyślnie zainstalowane i skonfigurowane na urządzeniu IoT Edge.

# <a name="windows-admin-center"></a>[Centrum administracyjne systemu Windows](#tab/windowsadmincenter)

1. Wybierz urządzenie IoT Edge z listy podłączonych urządzeń w centrum administracyjnym systemu Windows, aby nawiązać z nim połączenie.

1. Na stronie Przegląd urządzenia są wyświetlane informacje o urządzeniu:

    1. W sekcji **listy modułów IoT Edge** są wyświetlane uruchomione moduły na urządzeniu. Gdy usługa IoT Edge jest uruchamiana po raz pierwszy, będzie widoczny tylko uruchomiony moduł **edgeAgent** . Moduł edgeAgent jest domyślnie uruchamiany i ułatwia Instalowanie i uruchamianie wszelkich dodatkowych modułów wdrażanych na urządzeniu.
    1. Sekcja **stan IoT Edge** zawiera stan usługi i powinien być raportowany jako **aktywny (uruchomiony)**.

1. Jeśli musisz rozwiązać problem z usługą IoT Edge, użyj narzędzia **powłoki poleceń** na stronie urządzenia, aby SSH (Secure Shell) na maszynie wirtualnej i uruchomić polecenia systemu Linux.

    1. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

       ```bash
       journalctl -u iotedge
       ```

    2. Użyj `check` Narzędzia, aby zweryfikować konfigurację i stan połączenia urządzenia.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

1. Zaloguj się do IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows przy użyciu następującego polecenia w sesji programu PowerShell:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >Jedynym kontem dozwolonym dla protokołu SSH do maszyny wirtualnej jest użytkownik, który go utworzył.

1. Po zalogowaniu możesz sprawdzić listę uruchomionych modułów IoT Edge za pomocą następującego polecenia systemu Linux:

   ```bash
   iotedge list
   ```

1. Jeśli musisz rozwiązać problem z usługą IoT Edge, użyj następujących poleceń systemu Linux.

    1. Jeśli potrzebujesz rozwiązać problem z usługą, pobierz jej dzienniki.

       ```bash
       journalctl -u iotedge
       ```

    2. Użyj `check` Narzędzia, aby zweryfikować konfigurację i stan połączenia urządzenia.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Następne kroki

* Kontynuuj [wdrażanie modułów IoT Edge](how-to-deploy-modules-portal.md) , aby dowiedzieć się, jak wdrażać moduły na urządzeniu.
* Dowiedz się, jak [zarządzać certyfikatami na IoT Edge dla systemu Linux na maszynie wirtualnej z systemem Windows](how-to-manage-device-certificates.md) i przetransferować pliki z systemu operacyjnego hosta na maszynę wirtualną z systemem Linux.
* Dowiedz się, jak [skonfigurować urządzenia IoT Edge do komunikowania się za pomocą serwera proxy](how-to-configure-proxy-support.md).
