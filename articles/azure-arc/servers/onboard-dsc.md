---
title: Instalowanie agenta połączonej maszyny przy użyciu programu Windows PowerShell DSC
description: W tym artykule dowiesz się, jak połączyć maszyny z platformą Azure przy użyciu usługi Azure ARC dla serwerów (wersja zapoznawcza) przy użyciu programu Windows PowerShell DSC.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 02661ff3c21fa27367cc9f2c399341ed2b86db3f
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/05/2020
ms.locfileid: "87809616"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Jak zainstalować agenta połączonej maszyny przy użyciu programu Windows PowerShell DSC

Przy użyciu [konfiguracji żądanego stanu (DSC) programu Windows PowerShell](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) można zautomatyzować instalację oprogramowania i konfigurację dla komputera z systemem Windows. W tym artykule opisano sposób instalowania usługi Azure ARC dla serwerów połączonych z agentem maszyn na hybrydowych komputerach z systemem Windows.

## <a name="requirements"></a>Wymagania

- Windows PowerShell w wersji 4,0 lub nowszej

- Moduł [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC

- Nazwa główna usługi do łączenia maszyn z usługą Azure Arc w przypadku serwerów nieinteraktywnie. Wykonaj kroki opisane w sekcji [Tworzenie jednostki usługi na potrzeby](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) dołączania w odpowiedniej skali, jeśli nie utworzono jednostki usługi dla łuku dla serwerów.

## <a name="install-the-connectedmachine-dsc-module"></a>Instalowanie modułu ConnectedMachine DSC

1. Aby ręcznie zainstalować moduł, należy pobrać kod źródłowy i rozpakować zawartość katalogu projektu do programu `$env:ProgramFiles\WindowsPowerShell\Modules folder` . Lub uruchom następujące polecenie, aby zainstalować program z galerii programu PowerShell przy użyciu programu PowerShellGet (w programie PowerShell 5,0):

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Aby potwierdzić instalację, uruchom następujące polecenie i upewnij się, że dostępne są zasoby DSC połączonej maszyny z platformą Azure.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   W danych wyjściowych powinien zostać wyświetlony komunikat podobny do następującego:

   ![Potwierdzenie przykładu instalacji modułu połączonej maszyny DSC](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Instalowanie agenta i nawiązywanie połączenia z platformą Azure

Zasoby w tym module są przeznaczone do zarządzania konfiguracją agenta połączonej maszyny platformy Azure. Uwzględniono również skrypt programu PowerShell, który znajduje się `AzureConnectedMachineAgent.ps1` w `AzureConnectedMachineDsc\examples` folderze. Używa zasobów społeczności do automatyzowania pobierania i instalacji oraz nawiązywania połączenia z usługą Azure Arc. Ten skrypt wykonuje podobne kroki opisane w artykule [łączenie maszyn hybrydowych z platformą Azure z artykułu Azure Portal](onboard-portal.md) .

Jeśli komputer musi komunikować się z usługą za pomocą serwera proxy, po zainstalowaniu agenta należy uruchomić polecenie, które jest opisane w [tym miejscu](onboard-portal.md#configure-the-agent-proxy-setting). Powoduje to ustawienie zmiennej środowiskowej serwera proxy `https_proxy` . Zamiast ręcznego uruchamiania polecenia można wykonać ten krok za pomocą usługi DSC przy użyciu modułu [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) .

>[!NOTE]
>Aby można było uruchomić DSC, system Windows musi być skonfigurowany do odbierania poleceń zdalnych programu PowerShell nawet w przypadku uruchamiania konfiguracji hosta lokalnego. Aby prawidłowo skonfigurować środowisko, po prostu uruchom `Set-WsManQuickConfig -Force` w terminalu programu PowerShell z podwyższonym poziomem uprawnień.
>

Dokumenty konfiguracyjne (pliki MOF) mogą być stosowane do maszyny przy użyciu `Start-DscConfiguration` polecenia cmdlet.

Poniżej przedstawiono parametry, które są przekazywane do skryptu programu PowerShell do użycia.

- `TenantId`: Unikatowy identyfikator (GUID) reprezentujący dedykowane wystąpienie usługi Azure AD.

- `SubscriptionId`: Identyfikator subskrypcji (GUID) subskrypcji platformy Azure, w której mają być używane maszyny.

- `ResourceGroup`: Nazwa grupy zasobów, do której mają należeć połączone maszyny.

- `Location`: Zobacz [Obsługiwane regiony platformy Azure](overview.md#supported-regions). Ta lokalizacja może być taka sama lub inna, jak lokalizacja grupy zasobów.

- `Tags`: Tablica ciągów znaczników, które powinny być stosowane do zasobu połączonej maszyny.

- `Credential`: Obiekt poświadczeń programu PowerShell z identyfikatorem **aplikacji** i **hasłem** używanym do rejestrowania maszyn na dużą skalę przy użyciu [nazwy głównej usługi](onboard-service-principal.md). 

1. W konsoli programu PowerShell przejdź do folderu, w którym zapisano `.ps1` plik.

2. Uruchom następujące polecenia programu PowerShell, aby skompilować dokument MOF (Aby uzyskać informacje na temat kompilowania konfiguracji DSC, zobacz [konfiguracje DSC](/powershell/scripting/dsc/configurations/configurations?view=powershell-7):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Spowoduje to utworzenie `localhost.mof file` nowego folderu o nazwie `C:\dsc` .

Po zainstalowaniu agenta programu i skonfigurowaniu go w celu nawiązania połączenia z usługą Azure ARC dla serwerów (wersja zapoznawcza) przejdź do Azure Portal, aby sprawdzić, czy serwer został pomyślnie połączony. Wyświetl maszyny w witrynie [Azure Portal](https://aka.ms/hybridmachineportal).

## <a name="adding-to-existing-configurations"></a>Dodawanie do istniejących konfiguracji

Ten zasób można dodać do istniejących konfiguracji DSC, aby reprezentować konfigurację kompleksową dla maszyny. Na przykład możesz chcieć dodać ten zasób do konfiguracji, która ustawia bezpieczne ustawienia systemu operacyjnego.

Moduł [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) z Galeria programu PowerShell może służyć do tworzenia [zasobów złożonych](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7) przykładowej konfiguracji, aby dodatkowo uprościć łączenie konfiguracji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak zarządzać maszyną za pomocą [Azure Policy](../../governance/policy/overview.md), na przykład w [konfiguracji gościa](../../governance/policy/concepts/guest-configuration.md)maszyny wirtualnej, sprawdzając, czy komputer jest raportowany do oczekiwanego log Analytics obszaru roboczego, włącz monitorowanie za pomocą [Azure monitor z maszynami wirtualnymi](../../azure-monitor/insights/vminsights-enable-policy.md)i wiele więcej.

- Dowiedz się więcej o [agencie log Analytics](../../azure-monitor/platform/log-analytics-agent.md). Agent Log Analytics dla systemów Windows i Linux jest wymagany, gdy użytkownik chce aktywnie monitorować system operacyjny i obciążenia uruchomione na komputerze, zarządzać nim za pomocą elementów Runbook usługi Automation lub rozwiązań, takich jak Update Management, lub używać innych usług platformy Azure, takich jak [Azure Security Center](../../security-center/security-center-intro.md).