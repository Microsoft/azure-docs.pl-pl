---
title: Kompilowanie konfiguracji DSC w Azure Automation State Configuration
description: W tym artykule opisano sposób kompilowania Desired State Configuration konfiguracji konfiguracji (DSC) dla Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e0a8bc3057bc098855eb7ff65a8feeb83b7c746
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834826"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilowanie konfiguracji DSC w Azure Automation State Configuration

Konfiguracje konfiguracji Desired State Configuration (DSC) można skompilować Azure Automation State Configuration w następujący sposób:

- Usługa kompilacji State Configuration Azure
  - Metoda dla początkujących z interaktywnym interfejsem użytkownika
   - Łatwe śledzenie stanu zadania

- Windows PowerShell
  - Wywołanie z Windows PowerShell na lokalnej stacji roboczej lub w usłudze kompilacji
  - Integracja z potokiem testowania projektowego
  - Podaj złożone wartości parametrów
  - Praca z danymi węzła i niewęzłowego na dużą skalę
  - Znaczna poprawa wydajności

Możesz również użyć szablonów Azure Resource Manager z rozszerzeniem Azure Desired State Configuration (DSC), aby wypychać konfiguracje do maszyn wirtualnych platformy Azure. Rozszerzenie Azure DSC używa struktury agenta maszyny wirtualnej platformy Azure do dostarczania, realizacji i tworzenia raportów dotyczących konfiguracji DSC uruchomionych na maszynach wirtualnych platformy Azure. Aby uzyskać szczegółowe informacje dotyczące kompilacji Azure Resource Manager szablonów, zobacz Desired State Configuration extension with Azure Resource Manager templates (Rozszerzenie kompilacji [przy użyciu Azure Resource Manager szablonów).](../virtual-machines/extensions/dsc-template.md#details) 

## <a name="compile-a-dsc-configuration-in-azure-state-configuration"></a>Kompilowanie konfiguracji DSC w usłudze Azure State Configuration

### <a name="portal"></a>Portal

1. Na koncie usługi Automation kliknij pozycję **Konfiguracja stanu (DSC).**
1. Kliknij kartę **Konfiguracje,** a następnie kliknij nazwę konfiguracji do skompilowania.
1. Kliknij pozycję **Kompiluj.**
1. Jeśli konfiguracja nie ma parametrów, zostanie wyświetlony monit o potwierdzenie, czy chcesz ją skompilować. Jeśli konfiguracja ma parametry, zostanie otwarty blok **Konfiguracja kompilacji,** aby można było podać wartości parametrów.
1. Zostanie otwarta strona Zadanie kompilacji, aby można było śledzić stan zadania kompilacji. Ta strona umożliwia również śledzenie konfiguracji węzłów (dokumentów konfiguracji MOF) umieszczonych na Azure Automation State Configuration ściągania.

### <a name="azure-powershell"></a>Azure PowerShell

Możesz użyć [start-AzAutomationDscCompilationJob,](/powershell/module/az.automation/start-azautomationdsccompilationjob) aby rozpocząć kompilowanie przy użyciu Windows PowerShell. Poniższy przykładowy kod rozpoczyna kompilację konfiguracji DSC o nazwie **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob` Zwraca obiekt zadania kompilacji, który umożliwia śledzenie stanu zadania. Następnie można użyć tego obiektu zadania kompilacji z [get-AzAutomationDscCompilationJob,](/powershell/module/az.automation/get-azautomationdsccompilationjob) aby określić stan zadania kompilacji, i [get-AzAutomationDscCompilationJobOutput,](/powershell/module/az.automation/get-azautomationdscconfiguration) aby wyświetlić jego strumienie (dane wyjściowe). Poniższy przykład rozpoczyna kompilację konfiguracji SampleConfig, czeka na jej ukończenie, a następnie wyświetla jego strumienie.

```powershell
$CompilationJob = Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'

while($null -eq $CompilationJob.EndTime -and $null -eq $CompilationJob.Exception)
{
    $CompilationJob = $CompilationJob | Get-AzAutomationDscCompilationJob
    Start-Sleep -Seconds 3
}

$CompilationJob | Get-AzAutomationDscCompilationJobOutput –Stream Any
```

### <a name="declare-basic-parameters"></a>Deklarowanie podstawowych parametrów

Deklaracja parametrów w konfiguracjach DSC, w tym typy parametrów i właściwości, działa tak samo jak w Azure Automation runbook. Aby dowiedzieć się więcej [o parametrach, zobacz Uruchamianie](./start-runbooks.md) Azure Automation runbook w programie .

W poniższym przykładzie użyto parametrów i w celu określenia wartości właściwości w konfiguracji węzła `FeatureName` `IsPresent` **ParametersExample.sample** wygenerowanej podczas kompilacji.

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

Konfiguracje DSC, które używają podstawowych parametrów, można skompilować w Azure Automation State Configuration portal lub za pomocą Azure PowerShell.

#### <a name="portal"></a>Portal

W portalu możesz wprowadzić wartości parametrów po kliknięciu pozycji Kompiluj .

![Parametry kompilacji konfiguracji](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Program PowerShell wymaga parametrów w [tablicy skrótów](/powershell/module/microsoft.powershell.core/about/about_hash_tables), gdzie klucz odpowiada nazwie parametru, a wartość jest równa wartości parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Aby uzyskać informacje o `PSCredential` przekazywaniu obiektów jako parametrów, zobacz [Zasoby poświadczeń](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilowanie konfiguracji zawierających zasoby złożone w Azure Automation

Funkcja **Zasoby złożone** umożliwia używanie konfiguracji DSC jako zasobów zagnieżdżonych w konfiguracji. Ta funkcja umożliwia zastosowanie wielu konfiguracji do pojedynczego zasobu. Aby dowiedzieć się więcej o zasobach złożonych, zobacz Zasoby złożone: Używanie konfiguracji [DSC](/powershell/scripting/dsc/resources/authoringresourcecomposite) jako zasobu.

> [!NOTE]
> Aby konfiguracje zawierające zasoby złożone zostały poprawnie skompilowane, należy najpierw zaimportować je do Azure Automation DSC, na których bazują te elementy. Dodawanie zasobu złożonego DSC nie różni się od dodawania modułu programu PowerShell do Azure Automation. Ten proces został udokumentowany w te [tematach Manage Modules (Zarządzanie modułami) Azure Automation](./shared-resources/modules.md).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Zarządzanie danymi konfiguracji podczas kompilowania konfiguracji w programie Azure Automation

`ConfigurationData` jest wbudowanym parametrem DSC, który pozwala oddzielić konfigurację strukturalną od dowolnej konfiguracji specyficznej dla środowiska podczas korzystania z konfiguracji DSC programu PowerShell. Aby uzyskać więcej informacji, zobacz [Oddzielanie "What" from "Where" in PowerShell DSC (Oddzielanie "co" od "gdzie" w konfiguracji DSC programu PowerShell).](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/)

> [!NOTE]
> Podczas kompilowania w Azure Automation State Configuration, można użyć w `ConfigurationData` Azure PowerShell, ale nie w Azure Portal.

W poniższym przykładzie konfiguracja DSC używa `ConfigurationData` za pośrednictwem `$ConfigurationData` słów `$AllNodes` kluczowych i . W tym przykładzie potrzebny jest również [moduł xWebAdministration.](https://www.powershellgallery.com/packages/xWebAdministration/)

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

Poprzednią konfigurację DSC można skompilować za pomocą Windows PowerShell. Poniższy skrypt dodaje dwie konfiguracje węzłów do usługi ściągania Azure Automation State Configuration: **ConfigurationDataSample.MyVM1** i **ConfigurationDataSample.MyVM3.**

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Praca z zasobami w Azure Automation podczas kompilacji

Odwołania do zasobów są takie same w Azure Automation State Configuration i runbook. Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Certyfikaty](./shared-resources/certificates.md)
- [Połączenia](automation-connections.md)
- [Poświadczenia](./shared-resources/credentials.md)
- [Zmienne](./shared-resources/variables.md)

#### <a name="credential-assets"></a>Zasoby poświadczeń

Konfiguracje DSC w Azure Automation mogą odwoływać się do zasobów poświadczeń usługi Automation przy użyciu `Get-AutomationPSCredential` polecenia cmdlet . Jeśli konfiguracja ma parametr określający obiekt , użyj polecenia , przekazując nazwę ciągu zasobu poświadczeń Azure Automation do polecenia cmdlet w celu `PSCredential` `Get-AutomationPSCredential` pobrania poświadczeń. Następnie użyj tego obiektu dla parametru wymagającego `PSCredential` obiektu . W tle zasób Azure Automation poświadczenia o tej nazwie jest pobierany i przekazywany do konfiguracji. W poniższym przykładzie pokazano ten scenariusz w działaniu.

Zabezpieczanie poświadczeń w konfiguracjach węzłów (dokumenty konfiguracji MOF) wymaga szyfrowania poświadczeń w pliku MOF konfiguracji węzła. Obecnie podczas generowania pliku MOF konfiguracji węzła musisz przyznać uprawnienia DSC programu PowerShell do poświadczeń wyjściowych w postaci zwykłego tekstu. Rozszerzenie DSC programu PowerShell nie wie, Azure Automation szyfruje cały plik MOF po jego generowaniu za pośrednictwem zadania kompilacji.

Możesz poinformować usługę DSC programu PowerShell, że dane wyjściowe poświadczeń są wygenerowane w postaci zwykłego tekstu w plikach MOF konfiguracji wygenerowanego węzła przy użyciu danych konfiguracji. Należy przekazać za `PSDscAllowPlainTextPassword = $true` `ConfigurationData` pośrednictwem dla każdej nazwy bloku węzła, która pojawia się w konfiguracji DSC i używa poświadczeń.

W poniższym przykładzie pokazano konfigurację DSC, która używa zasobu poświadczeń usługi Automation.

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

Poprzednią konfigurację DSC można skompilować przy użyciu programu PowerShell. Poniższy kod programu PowerShell dodaje dwie konfiguracje węzłów do serwera ściągania Azure Automation State Configuration: **CredentialSample.MyVM1** i **CredentialSample.MyVM2.**

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
> Po zakończeniu kompilacji może zostać wyświetlony komunikat o błędzie `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` Można bezpiecznie zignorować ten komunikat.

## <a name="compile-your-dsc-configuration-in-windows-powershell"></a>Kompilowanie konfiguracji DSC w Windows PowerShell

Proces kompilowania konfiguracji DSC w programie Windows PowerShell znajduje się w dokumentacji dsc programu PowerShell: Pisanie, kompilowanie [i stosowanie konfiguracji.](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration)
Ten proces można wykonać na stacji roboczej dewelopera lub w usłudze kompilacji, takiej [jak Azure DevOps](https://dev.azure.com). Następnie możesz zaimportować pliki MOF kompilowane w usłudze Azure State Configuration Service.

Kompilowanie w Windows PowerShell udostępnia również opcję podpisywania zawartości konfiguracji. Agent DSC weryfikuje lokalnie konfigurację podpisanego węzła w węźle zarządzanym. Weryfikacja zapewnia, że konfiguracja zastosowana do węzła pochodzi z autoryzowanego źródła.

Możesz również zaimportować konfiguracje węzłów (pliki MOF), które zostały skompilowane poza platformą Azure. Import obejmuje kompilację ze stacji roboczej dewelopera lub usługi takiej jak [Azure DevOps](https://dev.azure.com). Takie podejście ma wiele zalet, w tym wydajność i niezawodność.

> [!NOTE]
> Plik konfiguracji węzła nie może być większy niż 1 MB, aby Azure Automation go zaimportować.

Aby uzyskać więcej informacji na temat podpisywania konfiguracji węzłów, zobacz Ulepszenia w programie [WMF 5.1 — jak podpisać konfigurację i moduł](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importowanie konfiguracji węzła w Azure Portal

1. Na koncie usługi Automation kliknij pozycję **Konfiguracja stanu (DSC) w obszarze** Zarządzanie **konfiguracją.**
1. Na stronie Konfiguracja stanu (DSC) kliknij **kartę Konfiguracje,** a następnie kliknij pozycję **Dodaj**.
1. Na stronie Importowanie kliknij ikonę  folderu obok pola Plik konfiguracji węzła, aby przejść do pliku MOF konfiguracji węzła na komputerze lokalnym.

   ![Wyszukiwanie pliku lokalnego](./media/automation-dsc-compile/import-browse.png)

1. Wprowadź nazwę w **polu Nazwa** konfiguracji. Ta nazwa musi odpowiadać nazwie konfiguracji, z której została skompilowana konfiguracja węzła.
1. Kliknij przycisk **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importowanie konfiguracji węzła za pomocą Azure PowerShell

Za pomocą polecenia cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) można zaimportować konfigurację węzła na konto usługi Automation.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Następne kroki

- Aby rozpocząć pracę, zobacz [Wprowadzenie do Azure Automation State Configuration](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej na temat kompilowania konfiguracji DSC w celu przypisania ich do węzłów docelowych, zobacz Compile [DSC configurations in Azure Automation State Configuration](automation-dsc-compile.md)(Kompilowanie konfiguracji DSC w systemie Azure Automation State Configuration ).
- Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
- Aby uzyskać informacje o cenach, [zobacz Azure Automation State Configuration cennik.](https://azure.microsoft.com/pricing/details/automation/)
- Aby uzyskać przykład użycia narzędzia State Configuration w potoku ciągłego wdrażania, zobacz Set up continuous deployment with Chocolatey (Konfigurowanie ciągłego wdrażania [za pomocą narzędzia Chocolatey).](automation-dsc-cd-chocolatey.md)
