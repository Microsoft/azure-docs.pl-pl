---
title: Zarządzanie zdalnymi zasobami lokalnymi przy użyciu funkcji programu PowerShell
description: Dowiedz się, jak skonfigurować Połączenia hybrydowe w Azure Relay, aby połączyć aplikację funkcji programu PowerShell z zasobami lokalnymi, które mogą być następnie używane do zdalnego zarządzania zasobem lokalnym.
author: eamono
ms.topic: conceptual
ms.date: 9/5/2019
ms.author: eamono
ms.openlocfilehash: 36fc4c873dccfe9fa814bddccd829ed04207f095
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74226934"
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

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **+ Utwórz zasób** w menu po lewej stronie, a następnie wybierz pozycję **aplikacja funkcji**.

1. W obszarze **Plan hostingu**wybierz pozycję **Plan App Service**, a następnie wybierz pozycję **App Service plan/lokalizacja**.

1. Wybierz **pozycję Utwórz nowy**, wpisz **nazwę planu App Service** , wybierz **lokalizację** w [regionie](https://azure.microsoft.com/regions/) blisko siebie lub w niemal innych usługach, do których ma dostęp funkcja, a następnie wybierz pozycję **warstwa cenowa**.

1. Wybierz plan Standard S1, a następnie wybierz pozycję **Zastosuj**.

1. Wybierz **przycisk OK** , aby utworzyć plan, a następnie skonfiguruj pozostałe ustawienia **aplikacja funkcji** zgodnie z opisem w tabeli bezpośrednio po poniższym zrzucie ekranu:

    ![Aplikacja funkcji podstawowych programu PowerShell](./media/functions-hybrid-powershell/create-function-powershell-app.png)  

    | Ustawienie      | Sugerowana wartość  | Opis                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nazwa aplikacji** | Nazwa unikatowa w skali globalnej | Nazwa identyfikująca nową aplikację funkcji. Prawidłowe znaki to `a-z`, `0-9` i `-`.  | 
    | **Subskrypcja** | Twoja subskrypcja | Subskrypcja, w ramach której jest tworzona ta nowa aplikacja funkcji. |
    | **Grupa zasobów** |  myResourceGroup | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja funkcji. Możesz również użyć sugerowanej wartości. |
    | **System operacyjny** | Preferowany system operacyjny | Wybierz pozycję Windows. |
    | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz pozycję PowerShell Core. |
    | **Storage** |  Nazwa unikatowa w skali globalnej |  Utwórz konto magazynu używane przez aplikację funkcji. Nazwy kont magazynu muszą mieć długość od 3 do 24 znaków i mogą zawierać tylko cyfry i małe litery. Możesz także użyć istniejącego konta.
    | **Application Insights** | Domyślny | Tworzy zasób Application Insights o tej samej *nazwie aplikacji* w najbliższym obsługiwanym regionie. Rozszerzając to ustawienie, można zmienić **nazwę nowego zasobu** lub wybrać inną **lokalizację** w regionie [geograficznym platformy Azure](https://azure.microsoft.com/global-infrastructure/geographies/) , w którym mają być przechowywane dane. |

1. Po sprawdzeniu poprawności ustawień wybierz pozycję **Utwórz**.

1. Wybierz ikonę **powiadomienia** w prawym górnym rogu portalu i poczekaj na komunikat "wdrożenie powiodło się".

1. Wybierz pozycję **Przejdź do zasobu**, aby wyświetlić nową aplikację funkcji. Możesz również wybrać pozycję **Przypnij do pulpitu nawigacyjnego**. Przypinanie ułatwia powrót do tego zasobu aplikacji funkcji z pulpitu nawigacyjnego.

## <a name="create-a-hybrid-connection-for-the-function-app"></a>Tworzenie połączenia hybrydowego dla aplikacji funkcji

Połączenia hybrydowe są konfigurowane z poziomu sekcji sieci aplikacji funkcji:

1. Wybierz kartę **funkcje platformy** w aplikacji funkcji, a następnie wybierz pozycję **Sieć**. 
   ![Przegląd aplikacji dla sieci platformy](./media/functions-hybrid-powershell/app-overview-platform-networking.png)  
1. Wybierz pozycję **Konfiguruj punkty końcowe połączeń hybrydowych**.
   ![Sieć](./media/functions-hybrid-powershell/select-network-feature.png)  
1. Wybierz pozycję **Dodaj połączenie hybrydowe**.
   ![Połączenie hybrydowe](./media/functions-hybrid-powershell/hybrid-connection-overview.png)  
1. Wprowadź informacje o połączeniu hybrydowym, jak pokazano bezpośrednio po poniższym zrzucie ekranu. Istnieje możliwość ustawienia **hosta punktu końcowego** zgodnego z nazwą hosta serwera lokalnego w celu łatwiejszego zapamiętania serwera podczas wykonywania poleceń zdalnych. Port jest zgodny z domyślnym portem usługi zdalnego zarządzania systemu Windows, który został zdefiniowany na serwerze wcześniej.
  ![Dodaj połączenie hybrydowe](./media/functions-hybrid-powershell/add-hybrid-connection.png)  

    **Nazwa połączenia hybrydowego**: ContosoHybridOnPremisesServer
    
    **Host punktu końcowego**: finance1
    
    **Port punktu końcowego**: 5986
    
    **ServiceBus — przestrzeń nazw**: Utwórz nową
    
    **Lokalizacja**: Wybierz dostępną lokalizację
    
    **Nazwa**: contosopowershellhybrid

5. Wybierz **przycisk OK** , aby utworzyć połączenie hybrydowe.

## <a name="download-and-install-the-hybrid-connection"></a>Pobierz i zainstaluj połączenie hybrydowe

1. Wybierz pozycję **Pobierz Menedżera połączeń** , aby zapisać plik msi lokalnie na komputerze.
![Pobierz instalatora](./media/functions-hybrid-powershell/download-hybrid-connection-installer.png)  
1. Skopiuj plik msi z komputera lokalnego na serwer lokalny.
1. Uruchom Instalatora Menedżer połączeń hybrydowych, aby zainstalować usługę na serwerze lokalnym.
![Zainstaluj połączenie hybrydowe](./media/functions-hybrid-powershell/hybrid-installation.png)  
1. W portalu Otwórz połączenie hybrydowe, a następnie skopiuj parametry połączenia bramy do Schowka.
![Kopiuj parametry połączenia hybrydowego](./media/functions-hybrid-powershell/copy-hybrid-connection.png)  
1. Otwórz interfejs użytkownika Menedżer połączeń hybrydowych na serwerze lokalnym.
![Otwórz interfejs użytkownika połączenia hybrydowego](./media/functions-hybrid-powershell/hybrid-connection-ui.png)  
1. Wybierz przycisk **wprowadź ręcznie** i wklej parametry połączenia ze schowka.
![Wklej połączenie](./media/functions-hybrid-powershell/enter-manual-connection.png)  
1. Uruchom ponownie Menedżer połączeń hybrydowych z programu PowerShell, jeśli nie jest wyświetlany jako połączony.
    ```powershell
    Restart-Service HybridConnectionManager
    ```

## <a name="create-an-app-setting-for-the-password-of-an-administrator-account"></a>Utwórz ustawienie aplikacji dla hasła konta administratora

1. Wybierz kartę **funkcje platformy** w aplikacji funkcji.
1. W obszarze **Ustawienia ogólne**wybierz pozycję **Konfiguracja**.
![Wybierz konfigurację platformy](./media/functions-hybrid-powershell/select-configuration.png)  
1. Rozwiń pozycję **nowe ustawienie aplikacji** , aby utworzyć nowe ustawienie dla hasła.
1. Nazwij ustawienie _ContosoUserPassword_, a następnie wprowadź hasło.
1. Wybierz przycisk **OK** , a następnie Zapisz, aby zapisać hasło w aplikacji funkcji.
![Dodaj ustawienie aplikacji dla hasła](./media/functions-hybrid-powershell/add-appsetting-password.png)  

## <a name="create-a-function-http-trigger-to-test"></a>Tworzenie wyzwalacza http funkcji do testowania

1. Utwórz nową funkcję wyzwalacza HTTP z aplikacji funkcji.
![Utwórz nowy wyzwalacz HTTP](./media/functions-hybrid-powershell/create-http-trigger-function.png)  
1. Zastąp kod programu PowerShell z szablonu następującym kodem:

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

3. Wybierz pozycję **Zapisz** i **Uruchom** , aby przetestować funkcję.
![Testowanie aplikacji funkcji](./media/functions-hybrid-powershell/test-function-hybrid.png)  

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
