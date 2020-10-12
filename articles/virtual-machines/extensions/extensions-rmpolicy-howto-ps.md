---
title: Używanie Azure Policy do ograniczania instalacji rozszerzenia maszyny wirtualnej (Windows)
description: Użyj Azure Policy, aby ograniczyć wdrożenia rozszerzeń.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: akjosh
ms.reviewer: cynthn
ms.openlocfilehash: e4959c9dca909afde4bf6d351d79ecca1e4022a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87069764"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Użyj Azure Policy, aby ograniczyć instalację rozszerzeń na maszynach wirtualnych z systemem Windows

Aby uniemożliwić używanie lub instalację niektórych rozszerzeń na maszynach wirtualnych z systemem Windows, można utworzyć definicję Azure Policy przy użyciu programu PowerShell, aby ograniczyć rozszerzenia dla maszyn wirtualnych w grupie zasobów. 

W tym samouczku są używane Azure PowerShell w Cloud Shell, które są stale aktualizowane do najnowszej wersji. 

 

## <a name="create-a-rules-file"></a>Utwórz plik reguł

Aby ograniczyć rozszerzenia, które można zainstalować, musisz mieć [regułę](../../governance/policy/concepts/definition-structure.md#policy-rule) umożliwiającą określenie logiki w celu zidentyfikowania rozszerzenia.

W tym przykładzie pokazano, jak odmówić rozszerzeń opublikowanych przez element "Microsoft. COMPUTE", tworząc plik reguł w Azure Cloud Shell, ale jeśli pracujesz lokalnie w programie PowerShell, możesz również utworzyć plik lokalny i zastąpić ścieżkę ($home/CloudDrive) ścieżką do pliku lokalnego na komputerze.

W [Cloud Shell](https://shell.azure.com/powershell)wpisz:

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

Skopiuj i wklej następujący plik JSON do pliku.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Gdy skończysz, naciśnij **klawisze CTRL + O** , a następnie **wprowadź** , aby zapisać plik. Naciśnij **klawisze Ctrl + X** , aby zamknąć plik i wyjść z programu.

## <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Potrzebny jest również plik [parametrów](../../governance/policy/concepts/definition-structure.md#parameters) , który tworzy strukturę do przekazywania na liście rozszerzeń do zablokowania. 

Ten przykład pokazuje, jak utworzyć plik parametrów dla maszyn wirtualnych w Cloud Shell, ale jeśli pracujesz w programie PowerShell lokalnie, możesz również utworzyć plik lokalny i zastąpić ścieżkę ($home/CloudDrive) ścieżką do pliku lokalnego na komputerze.

W [Cloud Shell](https://shell.azure.com/powershell)wpisz:

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

Skopiuj i wklej następujący plik JSON do pliku.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "displayName": "Denied extension"
        }
    }
}
```

Gdy skończysz, naciśnij **klawisze CTRL + O** , a następnie **wprowadź** , aby zapisać plik. Naciśnij **klawisze Ctrl + X** , aby zamknąć plik i wyjść z programu.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad to obiekt służący do przechowywania konfiguracji, której chcesz użyć. Definicja zasad używa plików reguł i parametrów do definiowania zasad. Utwórz definicję zasad przy użyciu polecenia cmdlet [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition) .

 Zasady i parametry zasad są plikami utworzonymi i przechowywanymi jako pliki JSON w usłudze Cloud Shell.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>Przypisywanie zasad

Ten przykład przypisuje zasady do grupy zasobów przy użyciu polecenia [New-AzPolicyAssignment](/powershell/module/az.resources/new-azpolicyassignment). W przypadku wszystkich maszyn wirtualnych utworzonych w grupie zasobów moja **zasobów** nie będzie można zainstalować agenta dostępu do maszyny wirtualnej ani rozszerzeń niestandardowego skryptu. 

Użyj [Get-AzSubscription | Polecenie cmdlet format-Table](/powershell/module/az.accounts/get-azsubscription) , aby użyć identyfikatora subskrypcji zamiast takiego w przykładzie.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>Testowanie zasad

Aby przetestować zasady, spróbuj użyć rozszerzenia dostępu do maszyny wirtualnej. Następujące zasady nie mogą kończyć się niepowodzeniem z komunikatem "Set-AzVMAccessExtension: Resource" myVMAccess ".

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

W portalu zmiana hasła powinna zakończyć się niepowodzeniem z powodu naruszenia zasad "wdrożenie szablonu nie powiodło się." .

## <a name="remove-the-assignment"></a>Usuń przypisanie

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>Usuń zasady

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz [Azure Policy](../../governance/policy/overview.md).
