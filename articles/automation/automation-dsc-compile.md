---
title: Kompilowanie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure
description: W tym artykule opisano sposób kompilowania konfiguracji konfiguracji żądanego stanu (DSC) dla usługi Azure Automation.
services: automation
ms.subservice: dsc
ms.date: 04/06/2020
ms.topic: conceptual
ms.openlocfilehash: f7558745442ac26fc33a063ff66fe170d08487ac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392084"
---
# <a name="compile-dsc-configurations-in-azure-automation-state-configuration"></a>Kompilowanie konfiguracji DSC w konfiguracji stanu automatyzacji platformy Azure

Konfiguracje konfiguracji żądanego stanu (DSC) można skompilować w konfiguracji stanu automatyzacji platformy Azure w następujący sposób:

- Usługa kompilacji konfiguracji stanu platformy Azure
  - Metoda dla początkujących z interaktywnym interfejsem użytkownika
   - Łatwe śledzenie stanu zadania

- Windows PowerShell
  - Wywołanie z programu Windows PowerShell na lokalnej stacji roboczej lub usłudze kompilacji
  - Integracja z potokiem testowym rozwoju
  - Podaj złożone wartości parametrów
  - Praca z danymi węzłów i niewędników na dużą skalę
  - Znaczna poprawa wydajności

Można również użyć szablonów usługi Azure Resource Manager z rozszerzeniem konfiguracji żądanego stanu platformy Azure (DSC) do wypychania konfiguracji do maszyn wirtualnych platformy Azure. Rozszerzenie usługi Azure DSC używa struktury agenta maszyny wirtualnej platformy Azure do dostarczania, uchwalania i raportowania konfiguracji DSC uruchomionych na maszynach wirtualnych platformy Azure. Aby uzyskać szczegółowe informacje o kompilacji przy użyciu szablonów usługi Azure Resource Manager, zobacz [Rozszerzenie konfiguracji żądanego stanu z szablonami usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template#details). 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="compiling-a-dsc-configuration-in-azure-state-configuration"></a>Kompilowanie konfiguracji DSC w konfiguracji stanu platformy Azure

### <a name="portal"></a>Portal

1. Na koncie automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)**.
1. Kliknij kartę **Konfiguracje,** a następnie kliknij nazwę konfiguracji do skompilowania.
1. Kliknij pozycję **Skompiluj**.
1. Jeśli konfiguracja nie ma parametrów, zostanie wyświetlony monit o potwierdzenie, czy chcesz ją skompilować. Jeśli konfiguracja ma parametry, otwiera się blok **Konfiguracja kompilacji,** dzięki czemu można podać wartości parametrów.
1. Strona Zadanie kompilacji jest otwierana, dzięki czemu można śledzić stan zadania kompilacji. Można również użyć tej strony do śledzenia konfiguracji węzłów (dokumenty konfiguracji MOF) umieszczone na serwerze ściągania konfiguracji usługi Azure Automation State.

### <a name="azure-powershell"></a>Azure PowerShell

Aby rozpocząć kompilację z programem Windows PowerShell, można użyć [funkcji Start-AzAutomationDscCompilationJob.](/powershell/module/az.automation/start-azautomationdsccompilationjob) Poniższy przykładowy kod rozpoczyna kompilację konfiguracji DSC o nazwie **SampleConfig**.

```powershell
Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'SampleConfig'
```

`Start-AzAutomationDscCompilationJob`zwraca obiekt zadania kompilacji, którego można użyć do śledzenia stanu zadania. Następnie można użyć tego obiektu zadania kompilacji z [Get-AzAutomationDscCompilationJob](/powershell/module/az.automation/get-azautomationdsccompilationjob) do określenia stanu zadania kompilacji i [Get-AzAutomationDscCompilationJobOutput,](/powershell/module/az.automation/get-azautomationdscconfiguration) aby wyświetlić jego strumienie (dane wyjściowe). Poniższy przykład rozpoczyna kompilację konfiguracji SampleConfig, czeka, aż zostanie ukończona, a następnie wyświetla jego strumienie.

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

Deklaracja parametrów w konfiguracjach DSC, w tym typy parametrów i właściwości, działa tak samo jak w projektach runbook usługi Azure Automation. Zobacz [Uruchamianie uruchomieniu w usłudze Azure Automation,](automation-starting-a-runbook.md) aby dowiedzieć się więcej o parametrach zestawu.

Poniższy `FeatureName` przykład używa `IsPresent` i parametrów do określenia wartości właściwości w **parametersexample.sample** konfiguracji węzła, generowane podczas kompilacji.

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

Można skompilować konfiguracje DSC, które używają podstawowych parametrów w portalu konfiguracji stanu automatyzacji platformy Azure lub za pomocą programu Azure PowerShell.

#### <a name="portal"></a>Portal

W portalu można wprowadzić wartości parametrów po kliknięciu przycisku **Compile**.

![Parametry kompilacji konfiguracji](./media/automation-dsc-compile/DSC_compiling_1.png)

#### <a name="azure-powershell"></a>Azure PowerShell

Program PowerShell wymaga parametrów w [tabeli mieszania,](/powershell/module/microsoft.powershell.core/about/about_hash_tables)gdzie klucz pasuje do nazwy parametru, a wartość jest równa wartości parametru.

```powershell
$Parameters = @{
    'FeatureName' = 'Web-Server'
    'IsPresent' = $False
}

Start-AzAutomationDscCompilationJob -ResourceGroupName 'MyResourceGroup' -AutomationAccountName 'MyAutomationAccount' -ConfigurationName 'ParametersExample' -Parameters $Parameters
```

Aby uzyskać `PSCredential` informacje o przekazywaniu obiektów jako parametrów, zobacz [Zasoby poświadczeń](#credential-assets).

### <a name="compile-configurations-containing-composite-resources-in-azure-automation"></a>Kompilowanie konfiguracji zawierających zasoby złożone w usłudze Azure Automation

Funkcja **Zasoby złożone** umożliwia używanie konfiguracji DSC jako zagnieżdżonych zasobów wewnątrz konfiguracji. Ta funkcja umożliwia stosowanie wielu konfiguracji do jednego zasobu. Zobacz [Zasoby złożone: Korzystanie z konfiguracji DSC jako zasobu,](/powershell/scripting/dsc/resources/authoringresourcecomposite) aby dowiedzieć się więcej o zasobach złożonych.

> [!NOTE]
> Aby konfiguracje zawierające zasoby złożone kompilować poprawnie, należy najpierw zaimportować do usługi Azure Automation wszystkie zasoby DSC, które kompozyty polegać. Dodawanie zasobu złożonego DSC nie różni się od dodawania dowolnego modułu programu PowerShell do usługi Azure Automation. Ten proces jest udokumentowany w [zarządzania modułami w usłudze Azure Automation](/azure/automation/shared-resources/modules).

### <a name="manage-configurationdata-when-compiling-configurations-in-azure-automation"></a>Zarządzanie danymi ConfigurationData podczas kompilowania konfiguracji w usłudze Azure Automation

`ConfigurationData`jest wbudowanym parametrem DSC, który umożliwia oddzielenie konfiguracji strukturalnej od dowolnej konfiguracji specyficznej dla środowiska podczas korzystania z programu PowerShell DSC. Aby uzyskać więcej informacji, zobacz [Oddzielanie "Co" od "Gdzie" w programie PowerShell DSC](https://devblogs.microsoft.com/powershell/separating-what-from-where-in-powershell-dsc/).

> [!NOTE]
> Podczas kompilowania w konfiguracji stanu `ConfigurationData` automatyzacji platformy Azure można używać w programie Azure PowerShell, ale nie w witrynie Azure portal.

Poniższa przykładowa konfiguracja `ConfigurationData` DSC używa za pośrednictwem `$ConfigurationData` i `$AllNodes` słów kluczowych. W tym przykładzie potrzebny jest również [moduł administrowa xWebAdministration.](https://www.powershellgallery.com/packages/xWebAdministration/)

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

Poprzednią konfigurację DSC można skompilować za pomocą programu Windows PowerShell. Poniższy skrypt dodaje dwie konfiguracje węzłów do usługi ściągania konfiguracji stanu usługi Azure Automation: **ConfigurationDataSample.MyVM1** i **ConfigurationDataSample.MyVM3**.

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

### <a name="work-with-assets-in-azure-automation-during-compilation"></a>Praca z zasobami w usłudze Azure Automation podczas kompilacji

Odwołania do zasobów są takie same w konfiguracji stanu automatyzacji platformy Azure i uruchomieniu. platformy Azure. Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Certyfikaty](automation-certificates.md)
- [Połączenia](automation-connections.md)
- [Poświadczenia](automation-credentials.md)
- [Zmienne](automation-variables.md)

#### <a name="credential-assets"></a>Zasoby poświadczeń

Konfiguracje DSC w usłudze Azure Automation `Get-AutomationPSCredential` mogą odwoływać się do zasobów poświadczeń automatyzacji przy użyciu polecenia cmdlet. Jeśli konfiguracja ma parametr, `PSCredential` który określa `Get-AutomationPSCredential` obiekt, należy użyć przez przekazanie nazwy ciągu zasobu poświadczeń usługi Azure Automation do polecenia cmdlet, aby pobrać poświadczenia. Następnie użyj tego obiektu dla parametru `PSCredential` wymagającego obiektu. W tle zasobów poświadczeń usługi Azure Automation o tej nazwie jest pobierany i przekazywany do konfiguracji. Poniższy przykład przedstawia ten scenariusz w akcji.

Utrzymywanie bezpieczeństwa poświadczeń w konfiguracjach węzłów (dokumenty konfiguracyjne MOF) wymaga szyfrowania poświadczeń w pliku MOF konfiguracji węzła. Obecnie należy przyznać uprawnienie DSC programu PowerShell do danych wyjściowych poświadczeń w postaci zwykłego tekstu podczas generowania MOF konfiguracji węzła. Program PowerShell DSC nie jest świadomy, że usługa Azure Automation szyfruje cały plik MOF po jego generowaniu za pomocą zadania kompilacji.

Można powiedzieć, że program PowerShell DSC, że jest w porządku dla poświadczeń, które mają być wyprowadzane w postaci zwykłego tekstu w konfiguracji generowanego węzła MOF przy użyciu danych konfiguracyjnych. Należy przekazać `PSDscAllowPlainTextPassword = $true` `ConfigurationData` za pośrednictwem dla każdej nazwy bloku węzła, który pojawia się w konfiguracji DSC i używa poświadczeń.

W poniższym przykładzie przedstawiono konfigurację DSC, która używa zasobu poświadczeń automatyzacji.

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

Poprzednią konfigurację DSC można skompilować za pomocą programu PowerShell. Następujący kod programu PowerShell dodaje dwie konfiguracje węzłów do serwera ściągania konfiguracji stanu automatyzacji platformy Azure: **CredentialSample.MyVM1** i **CredentialSample.MyVM2**.

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
> Po zakończeniu kompilacji może pojawić się `The 'Microsoft.PowerShell.Management' module was not imported because the 'Microsoft.PowerShell.Management' snap-in was already imported.` komunikat o błędzie Można bezpiecznie zignorować ten komunikat.

## <a name="compiling-your-dsc-configuration-in-windows-powershell"></a>Kompilowanie konfiguracji DSC w programie Windows PowerShell

Można również importować konfiguracje węzłów (plików MOF), które zostały skompilowane poza platformą Azure. Import obejmuje kompilację ze stacji roboczej dewelopera lub w usłudze, takiej jak [Azure DevOps.](https://dev.azure.com) Takie podejście ma wiele zalet, w tym wydajność i niezawodność.

Kompilacja w programie Windows PowerShell umożliwia również podpisywanie zawartości konfiguracji. Agent DSC weryfikuje konfigurację podpisanego węzła lokalnie w węźle zarządzanym. Weryfikacja zapewnia, że konfiguracja zastosowana do węzła pochodzi z autoryzowanego źródła.

> [!NOTE]
> Plik konfiguracji węzła nie może być większy niż 1 MB, aby umożliwić usługę Azure Automation zaimportowanie go.

Aby uzyskać więcej informacji na temat podpisywania konfiguracji węzłów, zobacz [Ulepszenia w WMF 5.1 - Jak podpisać konfigurację i moduł](/powershell/scripting/wmf/whats-new/dsc-improvements#dsc-module-and-configuration-signing-validations).

### <a name="compile-the-dsc-configuration"></a>Kompilowanie konfiguracji DSC

Proces kompilowania konfiguracji DSC w programie Windows PowerShell jest zawarty w dokumentacji programu PowerShell DSC [Write, Compile i Apply a Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration#compile-the-configuration).
Ten proces można wykonać z dewelopera stacji roboczej lub w ramach usługi kompilacji, takich jak [Azure DevOps](https://dev.azure.com). Następnie można zaimportować pliki MOF wyprodukowane przez kompilowanie konfiguracji do usługi Azure State Configuration.

### <a name="import-a-node-configuration-in-the-azure-portal"></a>Importowanie konfiguracji węzła w witrynie Azure portal

1. Na koncie automatyzacji kliknij pozycję **Konfiguracja stanu (DSC)** w obszarze **Zarządzanie konfiguracją**.
1. Na stronie Konfiguracja stanu (DSC) kliknij kartę **Konfiguracje,** a następnie kliknij pozycję **Dodaj**.
1. Na stronie Importowanie kliknij ikonę folderu obok pola **Plik konfiguracji węzła,** aby wyszukać plik MOF konfiguracji węzła na komputerze lokalnym.

   ![Przeglądanie pliku lokalnego](./media/automation-dsc-compile/import-browse.png)

1. Wprowadź nazwę w polu **Nazwa konfiguracji.** Ta nazwa musi być zgodna z nazwą konfiguracji, z której skompilowano konfigurację węzła.
1. Kliknij przycisk **OK**.

### <a name="import-a-node-configuration-with-azure-powershell"></a>Importowanie konfiguracji węzła za pomocą programu Azure PowerShell

Polecenia cmdlet [Import-AzAutomationDscNodeConfiguration](/powershell/module/az.automation/import-azautomationdscnodeconfiguration) służy do importowania konfiguracji węzła do konta automatyzacji.

```powershell
Import-AzAutomationDscNodeConfiguration -AutomationAccountName 'MyAutomationAccount' -ResourceGroupName 'MyResourceGroup' -ConfigurationName 'MyNodeConfiguration' -Path 'C:\MyConfigurations\TestVM1.mof'
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje o tym, zobacz [Wprowadzenie do konfiguracji stanu automatyzacji platformy Azure](automation-dsc-getting-started.md).
- Aby dowiedzieć się więcej o kompilowaniu konfiguracji DSC, aby można je było przypisać do węzłów docelowych, zobacz [Kompilowanie konfiguracji w konfiguracji stanu automatyzacji platformy Azure](automation-dsc-compile.md).
- Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Aby uzyskać informacje o cenach, zobacz [Cennik konfiguracji stanu usługi Azure Automation .](https://azure.microsoft.com/pricing/details/automation/)
- Aby zobaczyć przykład użycia konfiguracji stanu automatyzacji platformy Azure w potoku ciągłego wdrażania, zobacz [Ciągłe wdrażanie na maszynach wirtualnych przy użyciu konfiguracji stanu automatyzacji platformy Azure i chocolatey.](automation-dsc-cd-chocolatey.md)
