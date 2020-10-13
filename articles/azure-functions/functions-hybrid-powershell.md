---
title: Zarządzanie zdalnymi zasobami lokalnymi przy użyciu funkcji programu PowerShell
description: Dowiedz się, jak skonfigurować Połączenia hybrydowe w Azure Relay, aby połączyć aplikację funkcji programu PowerShell z zasobami lokalnymi, które mogą być następnie używane do zdalnego zarządzania zasobem lokalnym.
author: eamono
ms.topic: conceptual
ms.date: 04/26/2020
ms.author: eamono
ms.openlocfilehash: 6034d1327d263eda49881af5eedf94ae06495128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83122276"
---
# <a name="managing-hybrid-environments-with-powershell-in-azure-functions-and-app-service-hybrid-connections"></a>Zarządzanie środowiskami hybrydowymi przy użyciu programu PowerShell w Azure Functions i App Service Połączenia hybrydowe

Funkcja Połączenia hybrydowe Azure App Service umożliwia dostęp do zasobów w innych sieciach. Więcej informacji na temat tej możliwości można znaleźć w dokumentacji [połączenia hybrydowe](../app-service/app-service-hybrid-connections.md) . W tym artykule opisano, jak używać tej funkcji do uruchamiania funkcji programu PowerShell przeznaczonych dla serwera lokalnego. Tego serwera można następnie użyć do zarządzania wszystkimi zasobami w środowisku lokalnym przy użyciu funkcji Azure PowerShell.


## <a name="configure-an-on-premises-server-for-powershell-remoting"></a>Konfigurowanie serwera lokalnego na potrzeby komunikacji zdalnej programu PowerShell

Poniższy skrypt włącza funkcję komunikacji zdalnej programu PowerShell i tworzy nową regułę zapory i odbiornik https usługi WinRM. Do celów testowych jest używany certyfikat z podpisem własnym. W środowisku produkcyjnym zalecamy korzystanie z podpisanego certyfikatu.

```powershell
# For configuration of WinRM, see
# https://docs.microsoft.com/windows/win32/winrm/installation-and-configuration-for-windows-remote-management.

# Enable PowerShell remoting.
Enable-PSRemoting -Force

# Create firewall rule for WinRM. The default HTTPS port is 5986.
New-NetFirewallRule -Name "WinRM HTTPS" `
                    -DisplayName "WinRM HTTPS" `
                    -Enabled True `
                    -Profile "Any" `
                    -Action "Allow" `
                    -Direction "Inbound" `
                    -LocalPort 5986 `
                    -Protocol "TCP"

# Create new self-signed-certificate to be used by WinRM.
$Thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME  -CertStoreLocation Cert:\LocalMachine\My).Thumbprint

# Create WinRM HTTPS listener.
$Cmd = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:COMPUTERNAME ""; CertificateThumbprint=""$Thumbprint""}"
cmd.exe /C $Cmd
```

## <a name="create-a-powershell-function-app-in-the-portal"></a>Tworzenie aplikacji funkcji programu PowerShell w portalu

Funkcja Połączenia hybrydowe App Service jest dostępna tylko w planach cen w warstwach Podstawowa, standardowa i izolowana. Podczas tworzenia aplikacji funkcji przy użyciu programu PowerShell Utwórz lub wybierz jeden z tych planów.

1. W menu witryny Azure Portal lub na **stronie głównej** wybierz pozycję **Utwórz zasób**.

1. Na stronie **Nowy** wybierz pozycję **obliczeniowe**  >  **aplikacja funkcji**.

1. Na stronie **podstawowe** Użyj ustawień aplikacji funkcji, zgodnie z opisem w poniższej tabeli.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **[Grupa zasobów](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. |
    | **Nazwa aplikacji funkcji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z` (bez uwzględniania wielkości liter), `0-9`i `-`.  |
    |**Publikowanie**| Kod | Opcja publikowania plików kodu lub kontenera Docker. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz pozycję PowerShell Core. |
    |**Wersja**| Numer wersji | Wybierz wersję zainstalowanego środowiska uruchomieniowego.  |
    |**Region**| Preferowany region | Wybierz [region](https://azure.microsoft.com/regions/) znajdujący się w sąsiedztwie lub w najbliższej usłudze dostęp do funkcji. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-basics.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wybierz pozycję **Dalej: hosting**. Na stronie **hosting** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Konto magazynu](../storage/common/storage-account-create.md)** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz również użyć istniejącego konta, które musi spełniać [wymagania dotyczące konta magazynu](../azure-functions/functions-scale.md#storage-account-requirements). |
    |**System operacyjny**| Preferowany system operacyjny | System operacyjny jest wstępnie wybrany na podstawie wybranego stosu środowiska uruchomieniowego, ale w razie potrzeby można zmienić to ustawienie. |
    | **[Typ planu](../azure-functions/functions-scale.md)** | **Plan usługi App Service** | Wybierz **plan usługi App Service**. W przypadku uruchomienia w ramach planu usługi App Service musisz zarządzać [skalowaniem aplikacji funkcji](../azure-functions/functions-scale.md).  |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-hosting.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wybierz kolejno pozycje **Dalej: monitorowanie**. Na stronie **monitorowanie** wprowadź następujące ustawienia.

    | Ustawienie      | Sugerowana wartość  | Opis |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | Domyślne | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie lub wybierając pozycję **Utwórz nowe**, możesz zmienić nazwę Application Insights lub wybrać inny region w obszarze [geograficznym platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w którym chcesz przechowywać dane. |

    :::image type="content" source="./media/functions-hybrid-powershell/function-app-create-monitoring.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć wybrane opcje konfiguracji aplikacji.

1. Na stronie **Recenzja i tworzenie** przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz** , aby udostępnić i wdrożyć aplikację funkcji.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i obejrzyj komunikat **wdrażanie zakończyło się pomyślnie** .

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Tworzenie połączenia hybrydowego dla aplikacji funkcji

Połączenia hybrydowe są konfigurowane z poziomu sekcji sieci aplikacji funkcji:

1. W obszarze **Ustawienia** w aplikacji funkcji, która została właśnie utworzona, wybierz pozycję **Sieć**. 
1. Wybierz pozycję **Konfiguruj punkty końcowe połączeń hybrydowych**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/configure-hybrid-connection-endpoint.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wybierz pozycję **Dodaj połączenie hybrydowe**.
   
    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-overview.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wprowadź informacje o połączeniu hybrydowym, jak pokazano bezpośrednio po poniższym zrzucie ekranu. Istnieje możliwość ustawienia **hosta punktu końcowego** zgodnego z nazwą hosta serwera lokalnego w celu łatwiejszego zapamiętania serwera podczas wykonywania poleceń zdalnych. Port jest zgodny z domyślnym portem usługi zdalnego zarządzania systemu Windows, który został zdefiniowany na serwerze wcześniej.
  
      :::image type="content" source="./media/functions-hybrid-powershell/add-hybrid-connection.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

    | Ustawienie      | Sugerowana wartość  |
    | ------------ | ---------------- |
    | **Nazwa połączenia hybrydowego** | ContosoHybridOnPremisesServer |
    | **Host punktu końcowego** | finance1 |
    | **Port punktu końcowego** | 5986 |
    | **ServiceBus przestrzeń nazw** | Kliknięcie przycisku Nowe |
    | **Lokalizacja** | Wybierz dostępną lokalizację |
    | **Nazwa** | contosopowershellhybrid | 

1. Wybierz **przycisk OK** , aby utworzyć połączenie hybrydowe.

## <a name="download-and-install-the-hybrid-connection"></a>Pobierz i zainstaluj połączenie hybrydowe

1. Wybierz pozycję **Pobierz Menedżera połączeń** , aby zapisać plik *MSI* lokalnie na komputerze.

    :::image type="content" source="./media/functions-hybrid-powershell/download-hybrid-connection-installer.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Skopiuj plik *MSI* z komputera lokalnego na serwer lokalny.
1. Uruchom Instalatora Menedżer połączeń hybrydowych, aby zainstalować usługę na serwerze lokalnym.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-installation.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. W portalu Otwórz połączenie hybrydowe, a następnie skopiuj parametry połączenia bramy do Schowka.

    :::image type="content" source="./media/functions-hybrid-powershell/copy-hybrid-connection.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Otwórz interfejs użytkownika Menedżer połączeń hybrydowych na serwerze lokalnym.

    :::image type="content" source="./media/functions-hybrid-powershell/hybrid-connection-ui.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wybierz pozycję **wprowadź ręcznie** i wklej parametry połączenia ze schowka.

    :::image type="content" source="./media/functions-hybrid-powershell/enter-manual-connection.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Uruchom ponownie Menedżer połączeń hybrydowych z programu PowerShell, jeśli nie jest wyświetlany jako połączony.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Utwórz ustawienie aplikacji dla hasła konta administratora

1. W obszarze **Ustawienia** aplikacji funkcji wybierz pozycję **Konfiguracja**. 
1. Wybierz pozycję **+ nowe ustawienie aplikacji**.

    :::image type="content" source="./media/functions-hybrid-powershell/select-configuration.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Nazwij ustawienie **ContosoUserPassword**, a następnie wprowadź hasło. Kliknij przycisk **OK**.
1. Wybierz pozycję **Zapisz** , aby zapisać hasło w aplikacji funkcji.

    :::image type="content" source="./media/functions-hybrid-powershell/save-administrator-password.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

## <a name="create-a-function-http-trigger"></a>Tworzenie wyzwalacza HTTP funkcji

1. W aplikacji funkcji wybierz pozycję **funkcje**, a następnie wybierz pozycję **+ Dodaj**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-http-trigger-function.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Wybierz szablon **wyzwalacza http** .

    :::image type="content" source="./media/functions-hybrid-powershell/select-http-trigger-template.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

1. Nazwij nową funkcję i wybierz pozycję **Utwórz funkcję**.

    :::image type="content" source="./media/functions-hybrid-powershell/create-new-http-function.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

## <a name="test-the-function"></a>Testowanie funkcji

1. W nowej funkcji wybierz pozycję **Code + test**. Zastąp kod programu PowerShell z szablonu następującym kodem:

    ```powershell
    # Input bindings are passed in via param block.
    param($Request, $TriggerMetadata)
    
    # Write to the Azure Functions log stream.
    Write-Output "PowerShell HTTP trigger function processed a request."
    
    # Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
    $UserName = "ContosoUser"
    $securedPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
    $Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)
    
    # This is the name of the hybrid connection Endpoint.
    $HybridEndpoint = "finance1"
    
    $Script = {
        Param(
            [Parameter(Mandatory=$True)]
            [String] $Service
        )
        Get-Service $Service
    }
    
    Write-Output "Scenario 1: Running command via Invoke-Command"
    Invoke-Command -ComputerName $HybridEndpoint `
                   -Credential $Credential `
                   -Port 5986 `
                   -UseSSL `
                   -ScriptBlock $Script `
                   -ArgumentList "*" `
                   -SessionOption (New-PSSessionOption -SkipCACheck)
    ```

1. Wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/functions-hybrid-powershell/save-http-function.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

 1. Wybierz pozycję **test**, a następnie wybierz pozycję **Uruchom** , aby przetestować funkcję. Przejrzyj dzienniki, aby sprawdzić, czy test zakończył się pomyślnie.

     :::image type="content" source="./media/functions-hybrid-powershell/test-function-hybrid.png" alt-text="Tworzenie aplikacji funkcji — podstawy." border="true":::

## <a name="managing-other-systems-on-premises"></a>Zarządzanie innymi systemami lokalnymi

Połączonego serwera lokalnego można użyć do nawiązania połączenia z innymi serwerami i systemami zarządzania w środowisku lokalnym. Pozwala to na zarządzanie operacjami centrum danych z platformy Azure przy użyciu funkcji programu PowerShell. Poniższy skrypt rejestruje sesję konfiguracji programu PowerShell, która jest uruchamiana w ramach podanych poświadczeń. Te poświadczenia muszą mieć uprawnienia administratora na serwerach zdalnych. Tej konfiguracji można następnie użyć do uzyskiwania dostępu do innych punktów końcowych na serwerze lokalnym lub centrum danych.

```powershell
# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Note that ContosoUserPassword is a function app setting, so I can access it as $env:ContosoUserPassword.
$UserName = "ContosoUser"
$SecuredPassword = ConvertTo-SecureString  $Env:ContosoUserPassword -AsPlainText -Force
$Credential = [System.management.automation.pscredential]::new($UserName, $SecuredPassword)

# This is the name of the hybrid connection Endpoint.
$HybridEndpoint = "finance1"

# The remote server that will be connected to run remote PowerShell commands on
$RemoteServer = "finance2".

Write-Output "Use hybrid connection server as a jump box to connect to a remote machine"

# We are registering an endpoint that runs under credentials ($Credential) that has access to the remote server.
$SessionName = "HybridSession"
$ScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $SessionName)

    if (-not (Get-PSSessionConfiguration -Name $SessionName -ErrorAction SilentlyContinue))
    {
        Register-PSSessionConfiguration -Name $SessionName -RunAsCredential $Using:Credential
    }
}

Write-Output "Registering session on hybrid connection jumpbox"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $ScriptCommand `
               -ArgumentList $SessionName `
               -SessionOption (New-PSSessionOption -SkipCACheck)

# Script to run on the jump box to run against the second machine.
$RemoteScriptCommand = {
    param (
        [Parameter(Mandatory=$True)]
        $ComputerName)
        # Write out the hostname of the hybrid connection server.
        hostname
        # Write out the hostname of the remote server.
        Invoke-Command -ComputerName $ComputerName -Credential $Using:Credential -ScriptBlock {hostname} `
                        -UseSSL -Port 5986 -SessionOption (New-PSSessionOption -SkipCACheck)
}

Write-Output "Running command against remote machine via jumpbox by connecting to the PowerShell configuration session"
Invoke-Command -ComputerName $HybridEndpoint `
               -Credential $Credential `
               -Port 5986 `
               -UseSSL `
               -ScriptBlock $RemoteScriptCommand `
               -ArgumentList $RemoteServer `
               -SessionOption (New-PSSessionOption -SkipCACheck) `
               -ConfigurationName $SessionName
```

Zastąp następujące zmienne w tym skrypcie odpowiednimi wartościami z Twojego środowiska:
* $HybridEndpoint
* $RemoteServer

W dwóch powyższych scenariuszach można połączyć środowiska lokalne i zarządzać nimi za pomocą programu PowerShell w Azure Functions i Połączenia hybrydowe. Zachęcamy do dowiesz się więcej na temat [połączenia hybrydowe](../app-service/app-service-hybrid-connections.md) i [programu PowerShell w funkcjach](./functions-reference-powershell.md).

Za pomocą usługi Azure [Virtual Networks](./functions-create-vnet.md) można także połączyć się ze środowiskiem lokalnym za pomocą Azure Functions.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"] 
> [Dowiedz się więcej o pracy z funkcjami programu PowerShell](functions-reference-powershell.md)
