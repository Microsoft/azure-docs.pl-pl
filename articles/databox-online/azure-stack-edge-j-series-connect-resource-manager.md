---
title: Nawiązywanie połączenia z Azure Resource Manager na urządzeniu z systemem Azure Stack EDGE Pro GPU
description: Zawiera opis sposobu nawiązywania połączenia z Azure Resource Manager uruchomionego na Azure Stackj funkcji brzegowej procesora GPU przy użyciu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 5cf406dc0577f477858dd8a6570f7975747112e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90891241"
---
# <a name="connect-to-azure-resource-manager-on-your-azure-stack-edge-pro-device"></a>Nawiązywanie połączenia z usługą Azure Resource Manager na urządzeniu z systemem Azure Stack EDGE Pro

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Azure Resource Manager udostępnia warstwę zarządzania, która umożliwia tworzenie, aktualizowanie i usuwanie zasobów w ramach subskrypcji platformy Azure. Urządzenie Azure Stack EDGE Pro obsługuje te same interfejsy API Azure Resource Manager do tworzenia, aktualizowania i usuwania maszyn wirtualnych w ramach subskrypcji lokalnej. Ta obsługa pozwala zarządzać urządzeniem w sposób zgodny z chmurą. 

W tym samouczku opisano sposób nawiązywania połączenia z lokalnymi interfejsami API na urządzeniu Azure Stack EDGE Pro za pośrednictwem Azure Resource Manager przy użyciu Azure PowerShell.

## <a name="about-azure-resource-manager"></a>Informacje o usłudze Azure Resource Manager

Azure Resource Manager zapewnia spójną warstwę zarządzania do wywoływania interfejsu API urządzeń z systemem Azure Stack EDGE Pro i wykonywania operacji, takich jak tworzenie, aktualizowanie i usuwanie maszyn wirtualnych. Architektura Azure Resource Manager szczegółowo przedstawiono na poniższym diagramie.

![Diagram dla Azure Resource Manager](media/azure-stack-edge-j-series-connect-resource-manager/edge-device-flow.svg)


## <a name="endpoints-on-azure-stack-edge-pro-device"></a>Punkty końcowe na urządzeniu Azure Stack EDGE Pro

W poniższej tabeli zestawiono różne punkty końcowe uwidocznione na urządzeniu, obsługiwane protokoły i porty umożliwiające dostęp do tych punktów końcowych. W całym artykule znajdują się odwołania do tych punktów końcowych.

| # | Punkt końcowy | Obsługiwane protokoły | Używany port | Sposób użycia |
| --- | --- | --- | --- | --- |
| 1. | Azure Resource Manager | https | 443 | Aby nawiązać połączenie z usługą Azure Resource Manager na potrzeby automatyzacji |
| 2. | Usługa tokenu zabezpieczającego | https | 443 | Aby uwierzytelnić się za pomocą tokenów dostępu i odświeżania |
| 3. | Obiekt blob | https | 443 | Aby nawiązać połączenie z usługą BLOB Storage za pośrednictwem interfejsu REST |


## <a name="connecting-to-azure-resource-manager-workflow"></a>Łączenie z przepływem pracy Azure Resource Manager

Proces łączenia się z lokalnymi interfejsami API urządzenia przy użyciu Azure Resource Manager wymaga wykonania następujących czynności:

| Czynności # | Wykonaj ten krok... | .. w tej lokalizacji. |
| --- | --- | --- |
| 1. | [Konfigurowanie urządzenia Azure Stack EDGE Pro](#step-1-configure-azure-stack-edge-pro-device) | Lokalny interfejs użytkownika sieci Web |
| 2. | [Tworzenie i instalowanie certyfikatów](#step-2-create-and-install-certificates) | Klient systemu Windows/lokalny interfejs użytkownika sieci Web |
| 3. | [Przejrzyj i skonfiguruj wymagania wstępne](#step-3-install-powershell-on-the-client) | Klient systemu Windows |
| 4. | [Konfigurowanie Azure PowerShell na kliencie](#step-4-set-up-azure-powershell-on-the-client) | Klient systemu Windows |
| 5. | [Modyfikuj plik hosta dla rozpoznawania nazw punktów końcowych](#step-5-modify-host-file-for-endpoint-name-resolution) | Klient systemu Windows lub serwer DNS |
| 6. | [Sprawdź, czy nazwa punktu końcowego została rozpoznana](#step-6-verify-endpoint-name-resolution-on-the-client) | Klient systemu Windows |
| 7. | [Użyj poleceń cmdlet Azure PowerShell, aby zweryfikować połączenie z Azure Resource Manager](#step-7-set-azure-resource-manager-environment) | Klient systemu Windows |

W poniższych sekcjach szczegółowo opisano poszczególne kroki opisane w temacie łączenie z Azure Resource Manager.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że klient używany do nawiązywania połączenia z urządzeniem za pośrednictwem Azure Resource Manager korzysta z protokołu TLS 1,2. Aby uzyskać więcej informacji, przejdź do pozycji [Konfigurowanie protokołu TLS 1,2 na kliencie systemu Windows, do którego jest uzyskiwany dostęp Azure Stack Edge](azure-stack-edge-j-series-configure-tls-settings.md).

## <a name="step-1-configure-azure-stack-edge-pro-device"></a>Krok 1. Konfigurowanie Azure Stack Edge — urządzenie 

Wykonaj następujące kroki w lokalnym interfejsie użytkownika sieci Web urządzenia z Azure Stack Edge.

1. Wypełnij ustawienia sieci dla urządzenia z Azure Stack Edge. 

    ![Strona ustawień sieciowych lokalnego interfejsu użytkownika sieci Web](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png)


    Zanotuj adres IP urządzenia. Ten adres IP będzie używany później.

2. Skonfiguruj nazwę urządzenia i domenę DNS na stronie **urządzenia** . Zanotuj nazwę urządzenia i domenę DNS, które będą używane później.

    ![Strona lokalnego interfejsu użytkownika sieci Web "urządzenie"](./media/azure-stack-edge-gpu-deploy-set-up-device-update-time/device-2.png)

    > [!IMPORTANT]
    > Nazwa urządzenia, domena DNS, zostanie użyta do utworzenia uwidocznionych punktów końcowych.
    > Użyj Azure Resource Manager i punktów końcowych obiektów BLOB ze strony **urządzenia** w lokalnym interfejsie użytkownika sieci Web.


## <a name="step-2-create-and-install-certificates"></a>Krok 2. Tworzenie i instalowanie certyfikatów

Certyfikaty zapewniają, że komunikacja jest zaufana. Na urządzeniu z systemem Azure Stack brzeg Pro automatycznie generowane są urządzenia z podpisem własnym, obiekty blob i Azure Resource Manager certyfikatów. Opcjonalnie możesz również przenieść swój własny podpisany obiekt BLOB i Azure Resource Manager certyfikaty.

Po dołączeniu własnego certyfikatu do własnych potrzeb potrzebny jest również odpowiedni łańcuch podpisywania certyfikatu. W przypadku łańcucha podpisywania, Azure Resource Manager i certyfikatów obiektów BLOB na urządzeniu wymagane są odpowiednie certyfikaty na komputerze klienckim, co umożliwia również uwierzytelnianie i komunikację z urządzeniem.

Aby nawiązać połączenie z Azure Resource Manager, musisz utworzyć lub pobrać łańcuch podpisywania oraz certyfikaty punktów końcowych, zaimportować te certyfikaty do klienta systemu Windows i na koniec przekazać te certyfikaty na urządzeniu.

### <a name="create-certificates-optional"></a>Tworzenie certyfikatów (opcjonalnie)

Tylko do celów testowych i programistycznych można użyć programu Windows PowerShell do utworzenia certyfikatów w systemie lokalnym. Podczas tworzenia certyfikatów dla klienta postępuj zgodnie z następującymi wskazówkami:

1. Najpierw musisz utworzyć certyfikat główny dla łańcucha podpisywania. Aby uzyskać więcej informacji, zobacz temat kroki [tworzenia certyfikatów łańcucha podpisywania](azure-stack-edge-j-series-manage-certificates.md#create-signing-chain-certificate).

2. Można następnie utworzyć certyfikaty punktów końcowych dla obiektu BLOB i Azure Resource Manager. Te punkty końcowe można uzyskać ze strony **urządzenia** w lokalnym interfejsie użytkownika sieci Web. Zapoznaj się z instrukcjami [tworzenia certyfikatów punktów końcowych](azure-stack-edge-j-series-manage-certificates.md#create-signed-endpoint-certificates).

3. W przypadku wszystkich tych certyfikatów upewnij się, że nazwa podmiotu i Alternatywna nazwa podmiotu są zgodne z następującymi wskazówkami:

    |Type |Nazwa podmiotu (SN)  |Alternatywna nazwa podmiotu (SAN)  |Przykład nazwy podmiotu |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Pojedynczy certyfikat sieci SAN dla obu punktów końcowych|`<Device name>.<dnsdomain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |

Aby uzyskać więcej informacji na temat certyfikatów, przejdź do sposobu [zarządzania certyfikatami](azure-stack-edge-j-series-manage-certificates.md).

### <a name="upload-certificates-on-the-device"></a>Przekazywanie certyfikatów na urządzeniu

Certyfikaty utworzone w poprzednim kroku będą znajdować się w magazynie osobistym klienta. Te certyfikaty należy wyeksportować na kliencie do odpowiednich plików formatu, które można następnie przekazać do urządzenia.

1. Certyfikat główny należy wyeksportować jako plik w formacie DER przy użyciu rozszerzenia pliku *. cer* . Aby uzyskać szczegółowe instrukcje, zobacz [Eksportowanie certyfikatów jako pliku w formacie. cer](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-der-format).

2. Certyfikaty punktów końcowych muszą być eksportowane jako pliki *PFX* z kluczami prywatnymi. Aby uzyskać szczegółowe instrukcje, zobacz [Eksportowanie certyfikatów jako plik PFX z kluczami prywatnymi](azure-stack-edge-j-series-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

3. Certyfikaty główne i końcowe są następnie przekazywane na urządzeniu przy użyciu opcji **+ Dodaj certyfikat** na stronie **Certyfikaty** w lokalnym interfejsie użytkownika sieci Web. Aby przekazać certyfikaty, wykonaj kroki opisane w sekcji [przekazywanie certyfikatów](azure-stack-edge-j-series-manage-certificates.md#upload-certificates).


### <a name="import-certificates-on-the-client-running-azure-powershell"></a>Importowanie certyfikatów na kliencie z uruchomionym Azure PowerShell

Klient systemu Windows, który będzie wywoływał Azure Resource Manager interfejsy API musi ustanowić relację zaufania z urządzeniem. W tym celu certyfikaty utworzone w poprzednim kroku muszą zostać zaimportowane na klienta systemu Windows do odpowiedniego magazynu certyfikatów.

1. Certyfikat główny wyeksportowany jako format DER z rozszerzeniem *CER* powinien zostać teraz zaimportowany do zaufanych głównych urzędów certyfikacji w systemie klienta. Aby uzyskać szczegółowe instrukcje, zobacz [Importowanie certyfikatów do magazynu zaufanych głównych urzędów certyfikacji.](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format)

2. Certyfikaty punktów końcowych, które zostały wyeksportowane jako plik *PFX* , muszą zostać wyeksportowane jako plik *CER*. Następnie *CER* zostanie zaimportowany do **osobistego** magazynu certyfikatów w systemie. Aby uzyskać szczegółowe instrukcje, zobacz [Importowanie certyfikatów do magazynu osobistego](azure-stack-edge-j-series-manage-certificates.md#import-certificates-as-der-format).

## <a name="step-3-install-powershell-on-the-client"></a>Krok 3. Instalowanie programu PowerShell na kliencie 

Klient systemu Windows musi spełniać następujące wymagania wstępne:

1. Uruchom program PowerShell w wersji 5,0. Wymagany jest program PowerShell w wersji 5,0 lub nowszej. Aby sprawdzić wersję programu PowerShell w systemie, uruchom następujące polecenie cmdlet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Porównaj wersję **główną** i upewnij się, że jest ona 5,0 lub nowsza.

    Jeśli masz nieaktualną wersję, zobacz [Upgrading existing Windows PowerShell (Uaktualnianie istniejącego programu Windows PowerShell)](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

    Jeśli nie \' masz programu PowerShell 5,0, postępuj zgodnie z tematem [Instalowanie programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6).

    Poniżej pokazano przykładowe dane wyjściowe.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved. 
    Try the new cross-platform PowerShell https://aka.ms/pscore6
    PS C:\windows\system32> $PSVersionTable.PSVersion
    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      18362  145
    ```
    
2. Możesz uzyskać dostęp do Galeria programu PowerShell.

    Uruchom program PowerShell jako administrator. Sprawdź, czy PSGallery jest zarejestrowany jako repozytorium.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop
    Get-PSRepository -Name "PSGallery"
    ```
    
    Poniżej pokazano przykładowe dane wyjściowe.
    
    ```powershell
    PS C:\windows\system32> Import-Module -Name PowerShellGet -ErrorAction Stop
    PS C:\windows\system32> Import-Module -Name PackageManagement -ErrorAction Stop
    PS C:\windows\system32> Get-PSRepository -Name "PSGallery"
    Name                      InstallationPolicy   SourceLocation
    ----                      ------------------   --------------
    PSGallery                 Trusted              https://www.powershellgallery.com/api/v2
    ```
    
Jeśli repozytorium nie jest zaufane lub potrzebujesz więcej informacji, zobacz [Sprawdzanie dostępności Galeria programu PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?view=azs-1908#2-validate-the-powershell-gallery-accessibility).

## <a name="step-4-set-up-azure-powershell-on-the-client"></a>Krok 4. Konfigurowanie Azure PowerShell na kliencie 

<!--1. Verify the API profile of the client and identify which version of the Azure PowerShell modules and libraries to include on your client. In this example, the client system will be running Azure Stack 1904 or later. For more information, see [Azure Resource Manager API profiles](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1908#azure-resource-manager-api-profiles).-->

1. Na kliencie zostaną zainstalowane moduły Azure PowerShell, które będą współpracować z Twoim urządzeniem.

    a. Uruchom program PowerShell jako administrator. Wymagany jest dostęp do galerii programu PowerShell. 


    b. Aby zainstalować wymagane moduły Azure PowerShell z Galeria programu PowerShell, uruchom następujące polecenie:

    ```powershell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    
    Install-Module -Name AzureRM.BootStrapper
    
   # Install and import the API Version Profile into the current PowerShell session.
    
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    
    # Confirm the installation of PowerShell
    Get-Module -Name "Azure*" -ListAvailable
    ```
    
    Upewnij się, że masz uruchomioną usługę Azure-RM 2.5.0 w wersji na końcu instalacji. 
    Jeśli masz istniejącą wersję modułu Azure-RM, która nie jest zgodna z wymaganą wersją, Odinstaluj przy użyciu następującego polecenia:

    `Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose`

    Teraz musisz ponownie zainstalować wymaganą wersję.
   

Poniżej przedstawiono przykładowe dane wyjściowe wskazujące, że moduły AzureRM w wersji 2.5.0 zostały pomyślnie zainstalowane.

```powershell
PS C:\windows\system32> Install-Module -Name AzureRM.BootStrapper
PS C:\windows\system32> Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Loading Profile 2019-03-01-hybrid
PS C:\windows\system32> Get-Module -Name "Azure*" -ListAvailable
 
    Directory: C:\Program Files\WindowsPowerShell\Modules
 
ModuleType Version    Name                          ExportedCommands
---------- -------    ----                          ----------------
Script     4.5.0      Azure.Storage                       {Get-AzureStorageTable, New-AzureStorageTableSASToken, New...
Script     2.5.0      AzureRM
Script     0.5.0      AzureRM.BootStrapper                {Update-AzureRmProfile, Uninstall-AzureRmProfile, Install-...
Script     4.6.1      AzureRM.Compute                     {Remove-AzureRmAvailabilitySet, Get-AzureRmAvailabilitySet...
Script     3.5.1      AzureRM.Dns                         {Get-AzureRmDnsRecordSet, New-AzureRmDnsRecordConfig, Remo...
Script     5.1.5      AzureRM.Insights                    {Get-AzureRmMetricDefinition, Get-AzureRmMetric, Remove-Az...
Script     4.2.0      AzureRM.KeyVault                    {Add-AzureKeyVaultCertificate, Set-AzureKeyVaultCertificat...
Script     5.0.1      AzureRM.Network                     {Add-AzureRmApplicationGatewayAuthenticationCertificate, G...
Script     5.8.3      AzureRM.profile                     {Disable-AzureRmDataCollection, Disable-AzureRmContextAuto...
Script     6.4.3      AzureRM.Resources                   {Get-AzureRmProviderOperation, Remove-AzureRmRoleAssignmen...
Script     5.0.4      AzureRM.Storage                     {Get-AzureRmStorageAccount, Get-AzureRmStorageAccountKey, ...
Script     4.0.2      AzureRM.Tags                        {Remove-AzureRmTag, Get-AzureRmTag, New-AzureRmTag}
Script     4.0.3      AzureRM.UsageAggregates             Get-UsageAggregates
Script     5.0.1      AzureRM.Websites                    {Get-AzureRmAppServicePlan, Set-AzureRmAppServicePlan, New...

 
    Directory: C:\Program Files (x86)\Microsoft Azure Information Protection\Powershell
 
ModuleType Version    Name                            ExportedCommands
---------- -------    ----                           ----------------
Binary     1.48.204.0 AzureInformationProtection          {Clear-RMSAuthentication, Get-RMSFileStatus, Get-RMSServer...
```


## <a name="step-5-modify-host-file-for-endpoint-name-resolution"></a>Krok 5. Modyfikowanie pliku hosta na potrzeby rozpoznawania nazw punktów końcowych 

Teraz zostanie dodany spójny adres VIP platformy Azure zdefiniowany w lokalnym interfejsie użytkownika sieci Web urządzenia do:

- Plik hosta na kliencie lub,
- Konfiguracja serwera DNS

> [!IMPORTANT]
> Zalecamy modyfikację konfiguracji serwera DNS na potrzeby rozpoznawania nazw punktów końcowych.

Na kliencie systemu Windows używanym do łączenia się z urządzeniem wykonaj następujące czynności:

1. Uruchom **Notatnik** jako administrator, a następnie otwórz plik **hosts** znajdujący się w lokalizacji C:\Windows\System32\Drivers\etc.

    ![Eksplorator Windows — plik hosts](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)

2. Dodaj następujące wpisy do pliku **hosts** zastępujące odpowiednie wartości dla urządzenia: 

    ```
    <Device IP> login.<appliance name>.<DNS domain>
    <Device IP> management.<appliance name>.<DNS domain>
    <Device IP> <storage name>.blob.<appliance name>.<DNS domain>
    ```

    > [!IMPORTANT]
    > Wpis w pliku hosts powinien dokładnie pasować do podanego połączenia z Azure Resource Manager w późniejszym kroku. Upewnij się, że wpis domeny DNS znajduje się w tym miejscu.

    Adres IP urządzenia został zapisany z lokalnego interfejsu użytkownika sieci Web we wcześniejszym kroku.

    Logowanie. \<appliance name\> ..\<DNS domain\> wpis jest punktem końcowym usługi tokenu zabezpieczającego (STS). Usługa STS jest odpowiedzialna za tworzenie, sprawdzanie poprawności, odnawianie i anulowanie tokenów zabezpieczających. Usługa tokenu zabezpieczającego służy do tworzenia tokenu dostępu i tokenu odświeżania, które są używane do ciągłej komunikacji między urządzeniem a klientem.

3. Powinno to wyglądać podobnie jak na poniższej ilustracji. Zapisz plik **hosts**.

    ![plik hosts w Notatniku](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file-notepad.png)

## <a name="step-6-verify-endpoint-name-resolution-on-the-client"></a>Krok 6. Weryfikacja rozpoznawania nazw punktów końcowych na kliencie

Sprawdź, czy nazwa punktu końcowego jest rozpoznawana na kliencie używanym do nawiązywania połączenia z spójnym adresem VIP platformy Azure.

1. Aby sprawdzić, czy nazwa punktu końcowego została rozpoznana, można użyć narzędzia wiersza polecenia ping.exe. Po otrzymaniu adresu IP polecenie ping zwróci nazwę hosta TCP/IP komputera, który należy \' ponownie śledzenie.

    Dodaj `-a` przełącznik do wiersza polecenia, jak pokazano w poniższym przykładzie. Jeśli nazwa hosta jest zwracana, zwróci również te potencjalnie cenne informacje w odpowiedzi.

    ![Polecenie ping w wierszu polecenia](media/azure-stack-edge-j-series-connect-resource-manager/ping-command-prompt.png)



## <a name="step-7-set-azure-resource-manager-environment"></a>Krok 7. Ustawianie środowiska Azure Resource Manager

Ustaw środowisko Azure Resource Manager i sprawdź, czy urządzenie do komunikacji z klientem za pośrednictwem Azure Resource Manager działa prawidłowo. Wykonaj następujące czynności w ramach tej weryfikacji:


1. Użyj `Add-AzureRmEnvironment` polecenia cmdlet, aby zapewnić, że komunikacja za pośrednictwem Azure Resource Manager działa prawidłowo, a wywołania interfejsu API przechodzą przez port dedykowany dla Azure Resource Manager-443.

    `Add-AzureRmEnvironment`Polecenie cmdlet dodaje punkty końcowe i metadane umożliwiające Azure Resource Manager polecenia cmdlet do nawiązywania połączenia z nowym wystąpieniem Azure Resource Manager. 


    > [!IMPORTANT]
    > Adres URL Azure Resource Manager punktu końcowego, który jest udostępniany w poniższym poleceniu cmdlet, uwzględnia wielkość liter. Upewnij się, że adres URL punktu końcowego jest cały i jest zgodny z informacjami podanymi w pliku Hosts. Jeśli przypadek nie jest zgodny, zostanie wyświetlony komunikat o błędzie.

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>/"
    ```

    Poniżej przedstawiono przykładowe dane wyjściowe:
    
    ```powershell
    PS C:\windows\system32> Add-AzureRmEnvironment -Name AzDBE -ARMEndpoint https://management.dbe-n6hugc2ra.microsoftdatabox.com/
    
    Name  Resource Manager Url                    ActiveDirectory Authority
    ----  --------------------                   -------------------------
    AzDBE https://management.dbe-n6hugc2ra.microsoftdatabox.com https://login.dbe-n6hugc2ra.microsoftdatabox.com/adfs/
    ```

2. Ustaw środowisko jako Azure Stack brzeg Pro i port, który ma być używany do Azure Resource Manager wywołań jako 443. Środowisko definiuje się na dwa sposoby:

    - Ustaw środowisko. Wpisz następujące polecenie:

    ```powershell
    Set-AzureRMEnvironment -Name <Environment Name>
    ```
    
    Aby uzyskać więcej informacji, przejdź do pozycji [Set-AzureRMEnvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/set-azurermenvironment?view=azurermps-6.13.0).

    - Zdefiniuj środowisko w tekście dla każdego wykonywanego polecenia cmdlet. Dzięki temu wszystkie wywołania interfejsu API przechodzą przez poprawne środowisko. Domyślnie wywołania przechodzą przez publiczną platformę Azure, ale chcesz, aby przechodzą przez środowisko, które zostało ustawione dla Azure Stack Edge na urządzeniu.

    - Zobacz więcej informacji na temat [sposobu przełączania środowisk AzureRM](#switch-environments).

2. Wywołaj interfejsy API urządzenia lokalnego, aby uwierzytelnić połączenia z Azure Resource Manager. 

    1. Te poświadczenia są przeznaczone dla konta komputera lokalnego i są używane wyłącznie na potrzeby dostępu do interfejsu API.

    2. Możesz połączyć się za pośrednictwem `login-AzureRMAccount` polecenia lub za pośrednictwem `Connect-AzureRMAccount` . 

        1. Aby się zalogować, wpisz następujące polecenie. Identyfikator dzierżawy w tym wystąpieniu jest zakodowany jako c0257de7-538f-415c-993a-1b87a031879d. Użyj następującej nazwy użytkownika i hasła.

            - **Nazwa użytkownika**  -  *EdgeArmUser*

            - **Hasło**  -  [Ustaw hasło dla Azure Resource Manager](azure-stack-edge-j-series-set-azure-resource-manager-password.md) i Użyj tego hasła do logowania. 

            ```powershell
            PS C:\windows\system32> $pass = ConvertTo-SecureString "<Your password>" -AsPlainText -Force;
            PS C:\windows\system32> $cred = New-Object System.Management.Automation.PSCredential("EdgeArmUser", $pass)
            PS C:\windows\system32> Connect-AzureRmAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d -credential $cred
            
            Account       SubscriptionName   TenantId            Environment
            -------       ----------------   --------            -----------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            
            PS C:\windows\system32>
            ```
                   
        
            Alternatywnym sposobem logowania jest użycie `login-AzureRmAccount` polecenia cmdlet. 
            
            `login-AzureRMAccount -EnvironmentName <Environment Name>` -TenantId c0257de7-538f-415c-993a-1b87a031879d 

            Oto przykładowe dane wyjściowe polecenia. 
         
            ```powershell
            PS C:\Users\Administrator> login-AzureRMAccount -EnvironmentName AzDBE -TenantId c0257de7-538f-415c-993a-1b87a031879d
            
            Account         SubscriptionName  TenantId              Environment
            -------         ----------------  --------              -------
            EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE
            PS C:\Users\Administrator>
            ```



> [!IMPORTANT]
> Połączenie do Azure Resource Manager wygasa co 1,5 godzin lub w przypadku ponownego uruchomienia urządzenia Azure Stack EDGE Pro. W takim przypadku wszystkie polecenia cmdlet, które wykonujesz, będą zwracać komunikaty o błędach do skutku, że już nie masz połączenia z platformą Azure. Konieczne będzie ponowne zalogowanie.

## <a name="switch-environments"></a>Przełączanie środowisk

Uruchom `Disconnect-AzureRmAccount` polecenie, aby przełączyć się do innego `AzureRmEnvironment` . 

W przypadku korzystania z programu `Set-AzureRmEnvironment` i `Login-AzureRmAccount` bez `Disconnect-AzureRmAccount` niego środowisko nie jest faktycznie przełączane.  

W poniższych przykładach pokazano, jak przełączać się między dwoma środowiskami `AzDBE1` i `AzDBE2` .

Najpierw należy wyświetlić listę wszystkich istniejących środowisk na komputerze klienckim.


```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmEnvironment
Name    Resource Manager Url     ActiveDirectory Authority
----    --------------------      -------------------------
AzureChinaCloud   https://management.chinacloudapi.cn/                 https://login.chinacloudapi.cn/
AzureCloud        https://management.azure.com/                        https://login.microsoftonline.com/
AzureGermanCloud  https://management.microsoftazure.de/                https://login.microsoftonline.de/
AzDBE1            https://management.HVTG1T2-Test.microsoftdatabox.com https://login.hvtg1t2-test.microsoftdatabox.com/adfs/
AzureUSGovernment https://management.usgovcloudapi.net/                https://login.microsoftonline.us/
AzDBE2            https://management.CVV4PX2-Test.microsoftdatabox.com https://login.cvv4px2-test.microsoftdatabox.com/adfs/
```

Następnie należy uzyskać dostęp do środowiska, z którym aktualnie nawiązano połączenie za pośrednictwem Azure Resource Manager.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE2
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```

Przed przełączeniem do innego środowiska należy teraz odłączyć od bieżącego środowiska.


```azurepowershell
PS C:\WINDOWS\system32> Disconnect-AzureRmAccount

Id                    : EdgeArmUser@localhost
Type                  : User
Tenants               : {c0257de7-538f-415c-993a-1b87a031879d}
AccessToken           :
Credential            :
TenantMap             : {}
CertificateThumbprint :
ExtendedProperties    : {[Subscriptions, A4257FDE-B946-4E01-ADE7-674760B8D1A3], [Tenants, c0257de7-538f-415c-993a-1b87a031879d]}
```

Zaloguj się do innego środowiska. Poniżej pokazano przykładowe dane wyjściowe.

```azurepowershell
PS C:\WINDOWS\system32> Login-AzureRmAccount -Environment "AzDBE1" -TenantId $ArmTenantId

Account     SubscriptionName   TenantId        Environment
-------     ----------------   --------        -----------
EdgeArmUser@localhost Default Provider Subscription c0257de7-538f-415c-993a-1b87a031879d AzDBE1
```

Uruchom to polecenie cmdlet, aby potwierdzić środowisko, z którym nawiązano połączenie.

```azurepowershell
PS C:\WINDOWS\system32> Get-AzureRmContext |fl *

Name               : Default Provider Subscription (A4257FDE-B946-4E01-ADE7-674760B8D1A3) - EdgeArmUser@localhost
Account            : EdgeArmUser@localhost
Environment        : AzDBE1
Subscription       : a4257fde-b946-4e01-ade7-674760b8d1a3
Tenant             : c0257de7-538f-415c-993a-1b87a031879d
TokenCache         : Microsoft.Azure.Commands.Common.Authentication.ProtectedFileTokenCache
VersionProfile     :
ExtendedProperties : {}
```
Przełączono teraz do zamierzonego środowiska.

## <a name="next-steps"></a>Następne kroki

[Wdróż maszyny wirtualne na urządzeniu z systemem Azure Stack brzeg Pro](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md).
