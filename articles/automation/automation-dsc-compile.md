---
title: Kompiluj konfiguracje DSC w konfiguracji stanu Azure Automation
description: W tym artykule opisano, jak kompilować konfiguracje konfiguracji żądanego stanu (DSC) dla Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: 3bb42886c653afbdf8975b532bd2e1e1c3c63ce9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86186541"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompiluj konfiguracje DSC w konfiguracji stanu Azure Automation

Konfiguracje konfiguracji żądanego stanu (DSC) można kompilować w konfiguracji stanu Azure Automation w następujący sposób:

- Usługa kompilacji konfiguracji stanu platformy Azure
  - Metoda początkująca z interakcyjnym interfejsem użytkownika
   - Łatwe śledzenie stanu zadania

- Windows PowerShell
  - Wywołanie z programu Windows PowerShell na lokalnej stacji roboczej lub usłudze kompilacji
  - Integracja z potokiem testów programistycznych
  - Podaj złożone wartości parametrów
  - Pracuj z danymi węzła i niebędącymi węzłami w skali
  - Znaczne zwiększenie wydajności

Za Azure Resource Manager pomocą rozszerzenia konfiguracji żądanego stanu (DSC) platformy Azure można również używać szablonów do wypychania konfiguracji do maszyn wirtualnych platformy Azure. Rozszerzenie Azure DSC korzysta z platformy agenta maszyny wirtualnej platformy Azure w celu dostarczania, przyjęcia i raportowania konfiguracji DSC działających na maszynach wirtualnych platformy Azure. Aby uzyskać szczegółowe informacje dotyczące kompilacji przy użyciu szablonów Azure Resource Manager, zobacz [rozszerzenie konfiguracji żądanego stanu z szablonami Azure Resource Manager](../virtual-machines/extensions/dsc-template.md#details). 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Kompilowanie konfiguracji DSC w konfiguracji stanu platformy Azure

### <a name="portal"></a>Portal

1. Na koncie usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)**.
1. Kliknij kartę **konfiguracje** , a następnie kliknij nazwę konfiguracji do skompilowania.
1. Kliknij przycisk **Kompiluj**.
1. Jeśli konfiguracja nie ma parametrów, zostanie wyświetlony monit o potwierdzenie, czy chcesz go skompilować. Jeśli konfiguracja zawiera parametry, zostanie otwarty blok **kompilowania konfiguracji** , w którym można podać wartości parametrów.
1. Zostanie otwarta strona zadanie kompilacji, dzięki czemu można śledzić stan zadania kompilacji. Ta strona służy również do śledzenia konfiguracji węzłów (dokumentów konfiguracyjnych MOF) umieszczonych na serwerze ściągania konfiguracji stanu Azure Automation.

### <a name="azure-powershell"></a>Azure PowerShell

Aby rozpocząć Kompilowanie za pomocą programu Windows PowerShell, można użyć polecenia [Start-AzAutomationDscCompilationJob](/powershell/module/az.automation/start-azautomationdsccompilationjob) . Następujący przykładowy kod rozpoczyna kompilację konfiguracji DSC o nazwie **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` zwraca obiekt zadania kompilacji, którego można użyć do śledzenia stanu zadania. Następnie można użyć tego obiektu zadania kompilacji za pomocą polecenia [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) , aby określić stan zadania kompilacji, i polecenie [Get-AzAutomationDscCompilationJobOutput](/powershell/module/az.automation/get-azautomationdscconfiguration) , aby wyświetlić jego strumienie (dane wyjściowe). Poniższy przykład uruchamia kompilację konfiguracji SampleConfig, czeka, dopóki nie zostanie ukończona, a następnie wyświetla jej strumienie.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Zadeklaruj podstawowe parametry

Deklaracja parametru w konfiguracjach DSC, w tym typy parametrów i właściwości, działa tak samo jak w Azure Automation elementach Runbook. Zobacz [Uruchamianie elementu Runbook w Azure Automation](./start-runbooks.md) , aby dowiedzieć się więcej na temat parametrów elementu Runbook.

Poniższy przykład używa `FeatureName` i `IsPresent` parametrów do określenia wartości właściwości w konfiguracji węzła **ParametersExample. sampled** , generowanej podczas kompilacji.

```powershell
Configuration ParametersExample
{
    param(
        [Parameter(Mandatory=$true)]
        [string] $FeatureName,

        [Parameter(Mandatory=$true)]
        [boolean] $IsPresent
    )

    $EnsureString = 'Present'
    if($IsPresent -eq $false)
    {
        $EnsureString = 'Absent'
    }

    Node 'sample'
    {
        WindowsFeature ($FeatureName + 'Feature')
        {
            Ensure = $EnsureString
            Name   = $FeatureName
        }
    }
}
```

Konfiguracje DSC wykorzystujące podstawowe parametry można skompilować w portalu konfiguracji stanu Azure Automation lub z Azure PowerShell.

#### <a name="portal"></a>Portal

W portalu możesz wprowadzić wartości parametrów po kliknięciu przycisku **Kompiluj**.

![Parametry kompilacji konfiguracji](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Program PowerShell wymaga parametrów w elemencie [Hashtable](/powershell/module/microsoft.powershell.core/about/about_hash_tables), gdzie klucz jest zgodny z nazwą parametru, a wartość jest równa wartości parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Aby uzyskać informacje o przekazywaniu `PSCredential` obiektów jako parametrów, zobacz [zasoby poświadczeń](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompiluj konfiguracje zawierające zasoby złożone w Azure Automation

Funkcja **zasobów złożonych** umożliwia korzystanie z konfiguracji DSC jako zasobów zagnieżdżonych w konfiguracji. Ta funkcja umożliwia stosowanie wielu konfiguracji do pojedynczego zasobu. Zobacz [zasoby złożone: użycie konfiguracji DSC jako zasobu,](/powershell/scripting/dsc/resources/authoringresourcecomposite) aby dowiedzieć się więcej na temat zasobów złożonych.

> [!NOTE]
> Aby konfiguracje zawierające zasoby złożone zostały skompilowane prawidłowo, należy najpierw zaimportować do Azure Automation wszystkich zasobów DSC, na których bazują złożone. Dodawanie zasobu złożonego DSC nie różni się od dodawania żadnego modułu programu PowerShell do Azure Automation. Ten proces jest opisany w temacie [Zarządzanie modułami w Azure Automation](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Zarządzanie ConfigurationData podczas kompilowania konfiguracji w Azure Automation

`ConfigurationData` jest wbudowanym parametrem DSC, który umożliwia oddzielenie konfiguracji strukturalnej od wszelkich konfiguracji specyficznych dla środowiska przy użyciu DSC programu PowerShell. Aby uzyskać więcej informacji, zobacz [rozdzielenie "co" od "Where" w programie POWERSHELL DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Podczas kompilowania w konfiguracji stanu Azure Automation można użyć `ConfigurationData` programu w Azure PowerShell, ale nie w Azure Portal.

Poniższa przykładowa Konfiguracja DSC używa `ConfigurationData` za pośrednictwem `$ConfigurationData` `$AllNodes` słów kluczowych i. Wymagany jest również [moduł xWebAdministration](https://www.powershellgallery.com/packages/xWebAdministration/) w tym przykładzie.

```powershell
Configuration ConfigurationDataSample
{
    Import-DscResource -ModuleName xWebAdministration -Name MSFT_xWebsite

    Write-Verbose $ConfigurationData.NonNodeData.SomeMessage

    Node $AllNodes.Where{$_.Role -eq 'WebServer'}.NodeName
    {
        xWebsite Site
        {
            Name         = $Node.SiteName
            PhysicalPath = $Node.SiteContents
            Ensure       = 'Present'
        }
    }
}
```

Poprzednią konfigurację DSC można skompilować za pomocą programu Windows PowerShell. Poniższy skrypt dodaje dwa konfiguracje węzłów do usługi ściągania konfiguracji stanu Azure Automation: **ConfigurationDataSample. MyVM1** i **ConfigurationDataSample. MyVM3**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = 'MyVM1'
            Role = 'WebServer'
        },
        @{
            NodeName = 'MyVM2'
            Role = 'SQLServer'
        },
        @{
            NodeName = 'MyVM3'
            Role = 'WebServer'
        }
    )

    NonNodeData = @{
        SomeMessage = 'I love Azure Automation State Configuration and DSC!'
    }
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ConfigurationDataSample' -ConfigurationData $ConfigData
```

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Pracuj z zasobami w Azure Automation podczas kompilacji

Odwołania do elementów zawartości są takie same w konfiguracji stanu Azure Automation i elementów Runbook. Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Certyfikaty](./shared-resources/certificates.md)
- [Połączenia](automation-connections.md)
- [Poświadczenia](./shared-resources/credentials.md)
- [Zmienne](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Zasoby poświadczeń

Konfiguracje DSC w Azure Automation mogą odwoływać się do zasobów poświadczeń usługi Automation przy użyciu `Get-AutomationPSCredential` polecenia cmdlet. Jeśli konfiguracja ma parametr, który określa `PSCredential` obiekt, użyj `Get-AutomationPSCredential` przez przekazanie nazwy ciągu Azure Automation zasobu poświadczenia do polecenia cmdlet w celu pobrania poświadczenia. Następnie użyj tego obiektu dla parametru wymagającego `PSCredential` obiektu. W tle zasób poświadczeń Azure Automation o tej nazwie zostanie pobrany i przesłany do konfiguracji. W poniższym przykładzie przedstawiono ten scenariusz w akcji.

Utrzymywanie bezpiecznych poświadczeń w konfiguracjach węzłów (dokumenty konfiguracji MOF) wymaga szyfrowania poświadczeń w pliku MOF konfiguracji węzła. Obecnie należy nadać uprawnienia DSC programu PowerShell w przypadku poświadczeń wyjściowych w postaci zwykłego tekstu podczas generowania pliku MOF konfiguracji węzła. Program PowerShell DSC nie wie, że Azure Automation szyfruje cały plik MOF po jego generowaniu za pomocą zadania kompilacji.

Możesz poinformować, że program PowerShell DSC ma być w stanie wypełniania poświadczeń w postaci zwykłego tekstu w wygenerowanej konfiguracji węzła pliki MOF przy użyciu danych konfiguracyjnych. Należy przekazać `PSDscAllowPlainTextPassword = $true` za pośrednictwem `ConfigurationData` dla każdej nazwy bloku węzła, która pojawia się w konfiguracji DSC i używa poświadczeń.

W poniższym przykładzie przedstawiono konfigurację DSC korzystającą z zasobu poświadczenia usługi Automation.

```powershell
Configuration CredentialSample
{
    Import-DscResource -ModuleName PSDesiredStateConfiguration
    $Cred = Get-AutomationPSCredential 'SomeCredentialAsset'

    Node $AllNodes.NodeName
    {
        File ExampleFile
        {
            SourcePath      = '\\Server\share\path\file.ext'
            DestinationPath = 'C:\destinationPath'
            Credential      = $Cred
        }
    }
}
```

Poprzednią konfigurację DSC można skompilować za pomocą programu PowerShell. Poniższy kod programu PowerShell dodaje dwa konfiguracje węzłów do serwera ściągania konfiguracji stanu Azure Automation: **CredentialSample. MyVM1** i **CredentialSample. MyVM2**.

```powershell
$ConfigData = @{
    AllNodes = @(
        @{
            NodeName = '*'
            PSDscAllowPlainTextPassword = $True
        },
        @{
            NodeName = 'MyVM1'
        },
        @{
            NodeName = 'MyVM2'
        }
    )
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'CredentialSample' -ConfigurationData $ConfigData
```

> [!NOTE]
> Po zakończeniu kompilacji może zostać wyświetlony komunikat o błędzie, `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` Aby bezpiecznie zignorować ten komunikat.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Kompilowanie konfiguracji DSC w programie Windows PowerShell

Proces kompilowania konfiguracji DSC w programie Windows PowerShell jest dostępny w dokumentacji DSC programu PowerShell [pisanie, kompilowanie i stosowanie konfiguracji](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Ten proces można wykonać z poziomu stacji roboczej dewelopera lub w ramach usługi kompilacji, takiej jak [Azure DevOps](https://dev.azure.com). Następnie można zaimportować pliki MOF utworzone przez skompilowanie konfiguracji do usługi Azure State Configuration.

Kompilacja w programie Windows PowerShell udostępnia również opcję podpisywania zawartości konfiguracji. Agent DSC weryfikuje konfigurację podpisanego węzła lokalnie w zarządzanym węźle. Weryfikacja zapewnia, że konfiguracja zastosowana do węzła pochodzi z autoryzowanego źródła.

Można również zaimportować konfiguracje węzłów (pliki MOF), które zostały skompilowane poza platformą Azure. Import obejmuje kompilację z poziomu stacji roboczej dewelopera lub usługi, takiej jak [Azure DevOps](https://dev.azure.com). Takie podejście ma wiele korzyści, w tym wydajność i niezawodność.

> [!NOTE]
> Plik konfiguracji węzła nie może być większy niż 1 MB, aby można było go zaimportować Azure Automation.

Więcej informacji o podpisywaniu konfiguracji węzłów znajduje się [w temacie ulepszenia w programie WMF 5,1 — Jak podpisać konfigurację i moduł](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Zaimportuj konfigurację węzła w Azure Portal

1. Na koncie usługi Automation kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **konfiguracje** , a następnie kliknij przycisk **Dodaj**.
1. Na stronie Importowanie kliknij ikonę folderu obok pola **plik konfiguracji węzła** , aby wyszukać plik MOF konfiguracji węzła na komputerze lokalnym.

   ![Przeglądaj w poszukiwaniu pliku lokalnego](./media/automation-dsc-compile/import-browse.png)

1. Wprowadź nazwę w polu **nazwa konfiguracji** . Ta nazwa musi być zgodna z nazwą konfiguracji, z której została skompilowana konfiguracja węzła.
1. Kliknij przycisk **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importowanie konfiguracji węzła przy użyciu Azure PowerShell

Możesz użyć polecenia cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) , aby zaimportować konfigurację węzła do konta usługi Automation.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć, zobacz Wprowadzenie do [konfiguracji stanu Azure Automation](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC, aby można było przypisać je do węzłów docelowych, zobacz [Kompilowanie konfiguracji DSC w konfiguracji stanu Azure Automation](automation-dsc-compile.md).
- Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Aby zapoznać się z przykładem użycia konfiguracji stanu w potoku ciągłego wdrażania, zobacz [Konfigurowanie ciągłego wdrażania z czekoladą](automation-dsc-cd-chocolatey.md).
