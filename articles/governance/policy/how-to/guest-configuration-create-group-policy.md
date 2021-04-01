---
title: Jak utworzyć definicje zasad konfiguracji gościa na podstawie zasady grupy linii bazowej dla systemu Windows
description: Dowiedz się, jak skonwertować zasady grupy z linii bazowej zabezpieczeń systemu Windows Server 2019 do definicji zasad.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 7f7e2af70efa6771d94d7ceaa14d1408175b1d12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348648"
---
# <a name="how-to-create-guest-configuration-policy-definitions-from-group-policy-baseline-for-windows"></a>Jak utworzyć definicje zasad konfiguracji gościa na podstawie zasady grupy linii bazowej dla systemu Windows

Przed utworzeniem niestandardowych definicji zasad warto przeczytać informacje dotyczące omówienia koncepcji w [Azure Policy konfiguracji gościa](../concepts/guest-configuration.md). Aby dowiedzieć się więcej o tworzeniu niestandardowych definicji zasad konfiguracji gościa dla systemu Linux, zobacz [jak utworzyć zasady konfiguracji gościa dla systemu Linux](./guest-configuration-create-linux.md). Aby dowiedzieć się więcej na temat tworzenia niestandardowych definicji zasad konfiguracji gościa dla systemu Windows, zobacz [jak utworzyć zasady konfiguracji gościa dla systemu Windows](./guest-configuration-create.md).

Podczas przeprowadzania inspekcji systemu Windows konfiguracja gościa używa modułu zasobów platformy [Desired State Configuration](/powershell/scripting/dsc/overview/overview) (DSC) do utworzenia pliku konfiguracji. Konfiguracja platformy DSC definiuje stan, w jakim powinna być maszyna. Jeśli Ocena konfiguracji nie jest **zgodna**, zostanie wyzwolony efekt zasad *auditIfNotExists* .
[Azure Policy konfiguracja gościa](../concepts/guest-configuration.md) tylko inspekcji ustawień wewnątrz maszyn.

> [!IMPORTANT]
> Do przeprowadzania inspekcji na maszynach wirtualnych platformy Azure jest wymagane rozszerzenie konfiguracji gościa. Aby wdrożyć rozszerzenie na dużą skalę na wszystkich maszynach z systemem Windows, przypisz następujące definicje zasad:
> - [Wdróż wymagania wstępne, aby włączyć zasady konfiguracji gościa na maszynach wirtualnych z systemem Windows.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ecd903d-91e7-4726-83d3-a229d7f2e293)
> 
> Nie używaj wpisów tajnych ani informacji poufnych w niestandardowych pakietach zawartości.

Społeczność DSC opublikowała [moduł BaselineManagement](https://github.com/microsoft/BaselineManagement) w celu przekonwertowania wyeksportowanych szablonów zasady grupy w formacie DSC. Wraz z poleceniem cmdlet GuestConfiguration moduł BaselineManagement tworzy Azure Policy pakiet konfiguracji gościa dla systemu Windows z zawartości zasady grupy. Aby uzyskać szczegółowe informacje na temat korzystania z modułu BaselineManagement, zobacz artykuł [Szybki Start: konwertowanie zasady grupy na DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).

W tym przewodniku omówiono proces tworzenia Azure Policy pakietu konfiguracji Gościa z obiektu zasady grupy (GPO). Podczas gdy przewodnik przedstawia konwersję linii bazowej zabezpieczeń systemu Windows Server 2019, ten sam proces można zastosować do innych obiektów zasad grupy.  

## <a name="download-windows-server-2019-security-baseline-and-install-related-powershell-modules"></a>Pobierz linię bazową zabezpieczeń systemu Windows Server 2019 i zainstaluj powiązane moduły programu PowerShell

Aby zainstalować **DSC**, **GuestConfiguration**, **Zarządzanie punktami odniesienia** i powiązane moduły platformy Azure w programie PowerShell:

1. W wierszu polecenia programu PowerShell uruchom następujące polecenie:

   ```azurepowershell-interactive
   # Install the BaselineManagement module, Guest Configuration DSC resource module, and relevant Azure modules from PowerShell Gallery
   Install-Module az.resources, az.policyinsights, az.storage, guestconfiguration, gpregistrypolicyparser, securitypolicydsc, auditpolicydsc, baselinemanagement -scope currentuser -Repository psgallery -AllowClobber
   ```

1. Utwórz katalog dla i Pobierz linię bazową zabezpieczeń systemu Windows Server 2019 z zestawu narzędzi zgodności zabezpieczeń systemu Windows.

   ```azurepowershell-interactive
   # Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
   New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
   Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
   ```

1. Odblokuj i rozwiń pobraną linię bazową serwera 2019.

   ```azurepowershell-interactive
   Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
   Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
   ```

1. Sprawdź poprawność zawartości punktu odniesienia serwera 2019 przy użyciu **MapGuidsToGpoNames.ps1**.

   ```azurepowershell-interactive
   # Show content details of downloaded GPOs
   C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
   ```

## <a name="convert-from-group-policy-to-azure-policy-guest-configuration"></a>Konwertuj z zasady grupy na Azure Policy konfigurację gościa

Następnie Konwertujemy pobraną linię bazową serwera 2019 na pakiet konfiguracyjny gościa za pomocą modułów konfiguracji gościa i zarządzania punktami odniesienia.

1. Przekonwertuj zasady grupy na konfigurację żądanego stanu przy użyciu modułu bazowego zarządzania.

   ```azurepowershell-interactive
   ConvertFrom-GPO -Path 'C:\git\policyfiles\downloads\GPOs\{3657C7A2-3FF3-4C21-9439-8FDF549F1D68}\' -OutputPath 'C:\git\policyfiles\' -OutputConfigurationScript -Verbose
   ```

1. Przed utworzeniem pakietu zawartości zasad Zmień nazwę, sformatuj i uruchom przekonwertowane skrypty.

   ```azurepowershell-interactive
   Rename-Item -Path C:\git\policyfiles\DSCFromGPO.ps1 -NewName C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('DSCFromGPO', 'Server2019Baseline') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   (Get-Content -Path C:\git\policyfiles\Server2019Baseline.ps1).Replace('PSDesiredStateConfiguration', 'PSDscResources') | Set-Content -Path C:\git\policyfiles\Server2019Baseline.ps1
   C:\git\policyfiles\Server2019Baseline.ps1
   ```

1. Utwórz pakiet zawartości konfiguracji gościa Azure Policy.

   ```azurepowershell-interactive
   New-GuestConfigurationPackage -Name Server2019Baseline -Configuration c:\git\policyfiles\localhost.mof -Verbose
   ```

## <a name="create-azure-policy-guest-configuration"></a>Utwórz konfigurację Azure Policy gościa

1. Następnym krokiem jest opublikowanie pliku na platformie Azure Blob Storage. Polecenie `Publish-GuestConfigurationPackage` wymaga `Az.Storage` modułu.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName  myResourceGroupName -StorageAccountName myStorageAccountName
   ```

1. Po utworzeniu i przekazaniu niestandardowego pakietu zasad konfiguracji gościa Utwórz definicję zasad konfiguracji gościa. Użyj `New-GuestConfigurationPolicy` polecenia cmdlet, aby utworzyć konfigurację gościa.

   ```azurepowershell-interactive
    $NewGuestConfigurationPolicySplat = @{
        ContentUri = $Uri 
        DisplayName = 'Server 2019 Configuration Baseline' 
        Description 'Validation of using a completely custom baseline configuration for Windows VMs' 
        Path = 'C:\git\policyfiles\policy'  
        Platform = Windows 
        }
   New-GuestConfigurationPolicy @NewGuestConfigurationPolicySplat
   ```
    
1. Opublikuj definicje zasad przy użyciu `Publish-GuestConfigurationPolicy` polecenia cmdlet. Polecenie cmdlet ma tylko parametr **Path** wskazujący lokalizację plików JSON utworzonych przez `New-GuestConfigurationPolicy` . Aby uruchomić polecenie publikowania, musisz mieć dostęp do tworzenia definicji zasad na platformie Azure. Wymagania dotyczące autoryzacji są udokumentowane na stronie [przegląd Azure Policy](../overview.md#getting-started) . Najlepsza wbudowana rola to **współautor zasad zasobów**.

   ```azurepowershell-interactive
   Publish-GuestConfigurationPolicy -Path C:\git\policyfiles\policy\ -Verbose
   ```

## <a name="assign-guest-configuration-policy-definition"></a>Przypisywanie definicji zasad konfiguracji gościa

Ostatnim krokiem w przypadku zasad utworzonych na platformie Azure jest przypisanie inicjatywy. Zobacz, jak przypisać inicjatywę przy użyciu [portalu](../assign-policy-portal.md), [interfejsu wiersza polecenia platformy Azure](../assign-policy-azurecli.md)i [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Definicje zasad konfiguracji gościa **zawsze** muszą być przypisane przy użyciu inicjatywy, która łączy zasady _AuditIfNotExists_ i _DeployIfNotExists_ . Jeśli tylko zasady _AuditIfNotExists_ są przypisane, wymagania wstępne nie są wdrażane, a zasady zawsze pokazują, że serwery "0" są zgodne.

Przypisanie definicji zasad z efektem _DeployIfNotExists_ wymaga dodatkowego poziomu dostępu. Aby udzielić najmniejszego poziomu uprawnień, można utworzyć niestandardową definicję roli, która rozszerza **współautor zasad zasobów**. Poniższy przykład tworzy rolę o nazwie **współautor zasad zasobów Dine** z dodatkowymi uprawnieniami _Microsoft. Authorization/roleAssignments/Write_.

   ```azurepowershell-interactive
   $subscriptionid = '00000000-0000-0000-0000-000000000000'
   $role = Get-AzRoleDefinition "Resource Policy Contributor"
   $role.Id = $null
   $role.Name = "Resource Policy Contributor DINE"
   $role.Description = "Can assign Policies that require remediation."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/$subscriptionid")
   New-AzRoleDefinition -Role $role
   ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat inspekcji maszyn wirtualnych z [konfiguracją gościa](../concepts/guest-configuration.md).
- Dowiedz się, jak [programowo utworzyć zasady](./programmatically-create.md).
- Dowiedz się, jak [uzyskać dane zgodności](./get-compliance-data.md).
