---
title: Używanie Azure Automation Runbook do zarządzania urządzeniami StorSimple
description: Dowiedz się, jak za pomocą programu Azure Automation Runbook zarządzać urządzeniem z serii StorSimple 8000 w programie Azure Portal.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 10/23/2017
ms.author: alkohli
ms.openlocfilehash: 8b74e23147e4dcc6f786c1c08b8a03bd07dd0b9a
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744036"
---
# <a name="use-azure-automation-runbooks-to-manage-storsimple-devices"></a>Używanie Azure Automation elementów Runbook do zarządzania urządzeniami StorSimple

W tym artykule opisano sposób, w jaki Azure Automation elementy Runbook są używane do zarządzania urządzeniem z serii StorSimple 8000 w programie Azure Portal. Przykładowy element Runbook zawiera instrukcje dotyczące konfigurowania środowiska do wykonywania tego elementu Runbook.


## <a name="configure-add-and-run-azure-runbook"></a>Konfigurowanie, Dodawanie i Uruchamianie elementu Runbook platformy Azure

W tej sekcji przedstawiono przykładowy skrypt programu Windows PowerShell dla programu StorSimple i szczegółowe informacje o różnych krokach wymaganych do zaimportowania skryptu do elementu Runbook, a następnie opublikowania i wykonania elementu Runbook.

### <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz:

* aktywna subskrypcja platformy Azure skojarzona z usługą StorSimple Menedżer urządzeń zarejestrowana na urządzeniu z serii StorSimple 8000.

* Program Windows PowerShell 5,0 zainstalowany na komputerze (lub na hoście z systemem Windows Server dla StorSimple, jeśli jest używany).

### <a name="create-automation-runbook-module-in-windows-powershell"></a>Tworzenie modułu elementu Runbook automatyzacji w programie Windows PowerShell

Aby utworzyć moduł automatyzacji dla zarządzania urządzeniami z serii StorSimple 8000, wykonaj następujące czynności:

1. Uruchom program Windows PowerShell. Utwórz nowy folder i zmień katalog na nowy.

    ```powershell
        mkdir C:\scripts\StorSimpleSDKTools
        cd C:\scripts\StorSimpleSDKTools
    ```

2. [Pobierz interfejs wiersza polecenia NuGet](https://www.nuget.org/downloads) w folderze utworzonym w poprzednim kroku. Istnieją różne wersje _nuget.exe_. Wybierz wersję odpowiadającą zestawowi SDK. Każde łącze pobierania wskazuje bezpośrednio plik _. exe_ . Upewnij się, że po kliknięciu prawym przyciskiem myszy i Zapisz plik na komputerze zamiast uruchamiania go z przeglądarki.

    Możesz również uruchomić następujące polecenie, aby pobrać i zapisać skrypt w tym samym folderze, który został utworzony wcześniej.

    ```
        wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

3. Pobierz zależny zestaw SDK.

    ```
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
        C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

4. Pobierz skrypt z przykładowego projektu GitHub.

    ```
        wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    ```

5. Utwórz moduł Azure Automation Runbook dla zarządzania urządzeniami z serii StorSimple 8000. W oknie programu Windows PowerShell wpisz następujące polecenia:

    ```powershell
        # set path variables
        $downloadDir = "C:\scripts\StorSimpleSDKTools"
        $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"

        #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
        mkdir "$moduleDir"
        Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
        Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
        Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir

        #Don't change the name of the Archive
        compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

6. Sprawdź, czy plik zip modułu automatyzacji został utworzony w `C:\scripts\StorSimpleSDKTools` .

    ![Zrzut ekranu przedstawia folder StorSimpleSDKTools z plikiem ZIP modułu automatyzacji](./media/storsimple-8000-automation-azurerm-runbook/verify-automation-module.png)

7. Poniższe dane wyjściowe są prezentowane podczas tworzenia modułu automatyzacji za pośrednictwem programu Windows PowerShell.

    ```powershell
    mkdir C:\scripts\StorSimpleSDKTools
    ```

    ```Output
        Directory: C:\scripts

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:43 AM                StorSimpleSDKTools
    ```

    ```powershell
    wget https://dist.nuget.org/win-x86-commandline/latest/nuget.exe -Out C:\scripts\StorSimpleSDKTools\nuget.exe
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Azure.Management.Storsimple8000series
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Azure.Management.Storsimple8000series 1.0.0' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 1.77 sec
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.28.3
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.IdentityModel.Clients.ActiveDirectory 2.28.3' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 927.64 ms
    ```

    ```powershell
    C:\scripts\StorSimpleSDKTools\nuget.exe install Microsoft.Rest.ClientRuntime.Azure.Authentication -Version 2.2.9-preview
    ```

    ```Output
    -------------------------------------------
    CUT              CUT
    -------------------------------------------
    Successfully installed 'Microsoft.Rest.ClientRuntime.Azure.Authentication 2.2.9-preview' to C:\scripts\StorSimpleSDKTools
    Executing nuget actions took 717.48 ms
    ```

    ```powershell
    wget https://raw.githubusercontent.com/anoobbacker/storsimpledevicemgmttools/master/Monitor-Backups.ps1 -Out Monitor-Backups.ps1
    # set path variables
    $downloadDir = "C:\scripts\StorSimpleSDKTools"
    $moduleDir = "$downloadDir\AutomationModule\Microsoft.Azure.Management.StorSimple8000Series"
    #don't change the folder name "Microsoft.Azure.Management.StorSimple8000Series"
    mkdir "$moduleDir"
    ```

    ```Output
        Directory: C:\scripts\StorSimpleSDKTools\AutomationModule

    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----       10/18/2017   8:48 AM                Microsoft.Azure.Management.StorSimple8000Series
    ```

    ```powershell
    Copy-Item "$downloadDir\Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\Microsoft.IdentityModel.Clients.ActiveDirectory*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\Microsoft.Rest.ClientRuntime.Azure*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.2.3.8\lib\net452\Microsoft.Rest.ClientRuntime*.dll" $moduleDir
    Copy-Item "$downloadDir\Newtonsoft.Json.6.0.8\lib\net45\Newtonsoft.Json*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Rest.ClientRuntime.Azure.Authentication.2.2.9-preview\lib\net45\Microsoft.Rest.ClientRuntime.Azure.Authentication*.dll" $moduleDir
    Copy-Item "$downloadDir\Microsoft.Azure.Management.Storsimple8000series.1.0.0\lib\net452\Microsoft.Azure.Management.Storsimple8000series*.dll" $moduleDir
    #Don't change the name of the Archive
    compress-Archive -Path "$moduleDir" -DestinationPath Microsoft.Azure.Management.StorSimple8000Series.zip
    ```

### <a name="import-publish-and-run-automation-runbook"></a>Importowanie, publikowanie i Uruchamianie elementu Runbook usługi Automation

1. Utwórz konto usługi Automatyzacja Azure Uruchom jako w Azure Portal. Aby to zrobić, przejdź do **witryny Azure Marketplace, > wszystko** , a następnie wyszukaj **automatyzację**. Wybierz pozycję **konta usługi Automation**.

    ![Wyszukiwanie — Automatyzacja](./media/storsimple-8000-automation-azurerm-runbook/automation1.png)

2. W bloku **Dodawanie konta usługi Automation** :

   1. Podaj **nazwę** konta usługi Automation.
   2. Wybierz **subskrypcję** połączoną z usługą StorSimple Menedżer urządzeń.
   3. Utwórz nową grupę zasobów lub wybierz ją z istniejącej grupy zasobów.
   4. Wybierz **lokalizację** (jeśli jest to możliwe tak samo jak w przypadku działania usługi).
   5. Pozostaw wybraną opcję Domyślne **Utwórz konto Uruchom jako** .
   6. Opcjonalnie zaznacz pole wyboru **Przypnij do pulpitu nawigacyjnego**. Kliknij pozycję **Utwórz**.

       ![Zrzut ekranu przedstawia okienko Dodawanie konta usługi Automation z wartościami dla subskrypcji, tworzenie konta Uruchom jako platformy Azure i tworzenie 2](./media/storsimple-8000-automation-azurerm-runbook/create-automation-account.png)

      Po pomyślnym utworzeniu konta usługi Automation zostanie wyświetlone powiadomienie. Aby uzyskać więcej informacji na temat tworzenia konta usługi Automation, przejdź do obszaru [Tworzenie konta Uruchom jako](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

3. Aby upewnić się, że utworzone konto usługi Automation może uzyskać dostęp do usługi StorSimple Menedżer urządzeń, musisz przypisać odpowiednie uprawnienia do konta usługi Automation. Przejdź do **kontroli dostępu** w usłudze StorSimple Menedżer urządzeń. Kliknij pozycję **+ Dodaj** i podaj nazwę konta Azure Automation. **Zapisz** ustawienia.

    ![Dodawanie-uprawnienia-Automatyzacja](./media/storsimple-8000-automation-azurerm-runbook/goto-add-roles.png)

4. W nowo utworzonym koncie przejdź do obszaru **zasoby udostępnione > moduły** i kliknij pozycję **+ Dodaj moduł**.

5. W bloku **Dodaj moduł** przejdź do lokalizacji spakowanego modułu, a następnie wybierz i otwórz moduł. Kliknij przycisk **OK**.

    ![Zrzut ekranu przedstawia okienko Dodawanie modułu z wartością Przekaż plik i OK](./media/storsimple-8000-automation-azurerm-runbook/add-module.png)

6. Przejdź do **usługi Process Automation > elementów Runbook, a następnie kliknij pozycję + Dodaj element Runbook**. W bloku **Dodawanie elementu Runbook** kliknij pozycję **Importuj istniejący element Runbook**. Wskaż plik skryptu programu Windows PowerShell dla **pliku elementu Runbook**. Typ elementu Runbook jest wybierany automatycznie. Podaj nazwę i opcjonalny opis elementu Runbook. Kliknij pozycję **Utwórz**.

    ![Dodaj moduł](./media/storsimple-8000-automation-azurerm-runbook/import-runbook.png)

7. Element Runbook zostanie dodany do listy elementów Runbook. Wybierz i kliknij ten element Runbook.

    ![Kliknij pozycję-New-Runbook](./media/storsimple-8000-automation-azurerm-runbook/verify-runbook-created.png)

8. Edytuj element Runbook, a następnie kliknij pozycję **okienko testowania**. Podaj parametry, takie jak nazwa usługi StorSimple Menedżer urządzeń, nazwę urządzenia StorSimple i subskrypcję. **Rozpocznij** test. Raport jest generowany po zakończeniu przebiegu. Aby uzyskać więcej informacji, przejdź do procedury [testowania elementu Runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Zrzut ekranu przedstawia przesunięcie testu, w którym można wprowadzać wartości parametrów i uruchamiać testy](./media/storsimple-8000-automation-azurerm-runbook/test-runbook.png)

9. Sprawdź dane wyjściowe z elementu Runbook w okienku testów. Jeśli jest spełniony, zamknij okienko. Kliknij przycisk **Publikuj** i po wyświetleniu monitu o potwierdzenie, Potwierdź i Opublikuj element Runbook.

    ![Zrzut ekranu przedstawia okienko Edycja elementu Runbook programu PowerShell, które wyświetla komunikat z prośbą o dalszą publikację elementu Runbook](./media/storsimple-8000-automation-azurerm-runbook/publish-runbook.png)

## <a name="next-steps"></a>Następne kroki

[Zarządzanie urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).
