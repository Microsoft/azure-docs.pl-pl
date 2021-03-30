---
title: 'Szybki Start: nowe przypisanie zasad przy użyciu programu PowerShell'
description: W tym przewodniku szybki start użyjesz Azure PowerShell, aby utworzyć przypisanie Azure Policy w celu zidentyfikowania niezgodnych zasobów.
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: e941b74101308af703f243197fb4043f8f32d233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88548416"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu Azure PowerShell

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów. W tym przewodniku Szybki start utworzysz przypisanie zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych. Po zakończeniu zidentyfikujesz maszyny wirtualne, które nie są _zgodne_.

Moduł Azure PowerShell służy do zarządzania zasobami platformy Azure z poziomu wiersza polecenia lub skryptów.
W tym przewodniku wyjaśniono, jak używać AZ module do tworzenia przypisania zasad.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

- Przed rozpoczęciem upewnij się, że masz zainstalowaną najnowszą wersję programu Azure PowerShell. Zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps), aby uzyskać szczegółowe informacje.

- Zarejestruj dostawcę zasobów usługi Azure Policy Insights przy użyciu Azure PowerShell. Dzięki zarejestrowaniu dostawcy zasobów masz pewność, że subskrypcja będzie z nim współpracować. Aby zarejestrować dostawcę zasobów, należy mieć uprawnienia do wykonywania operacji rejestrowania dostawcy zasobów. Ta operacja jest uwzględniona w rolach Współautor i Właściciel. Uruchom następujące polecenie, aby zarejestrować dostawcę zasobów:

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  Aby uzyskać więcej informacji na temat rejestrowania i przeglądania dostawców zasobów, zobacz [dostawcy zasobów i ich typy](../../azure-resource-manager/management/resource-providers-and-types.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>Tworzenie przypisania zasad

W tym przewodniku szybki start utworzysz przypisanie zasad dla _maszyn wirtualnych inspekcji bez definicji dysków zarządzanych_ . Ta definicja zasad identyfikuje maszyny wirtualne, które nie korzystają z dysków zarządzanych.

Uruchom następujące polecenia, aby utworzyć nowe przypisanie zasad:

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

Poprzednie polecenia korzystają z następujących informacji:

- **Nazwa** —rzeczywista nazwa przypisania. W tym przykładzie użyto nazwy _audit-vm-manageddisks_.
- **DisplayName** — nazwa wyświetlana przypisania zasad. W takim przypadku używasz _maszyn wirtualnych inspekcji bez przypisania dysków zarządzanych_.
- **Definicja** — identyfikator definicji zasad, na podstawie której tworzysz przypisanie. W takim przypadku jest IDENTYFIKATORem _maszyn wirtualnych inspekcji definicji zasad, które nie korzystają z dysków zarządzanych_.
- **Zakres** — zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może obejmować zarówno subskrypcje, jak i grupy zasobów. Pamiętaj, aby zastąpić fragment &lt;zakres&gt; nazwą grupy zasobów.

Teraz możesz zidentyfikować niezgodne zasoby, aby zrozumieć stan zgodności środowiska.

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Skorzystaj z poniższych informacji w celu zidentyfikowania zasobów niezgodnych z utworzonym przypisaniem zasad. Uruchom następujące polecenia:

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

Aby uzyskać więcej informacji na temat uzyskiwania stanu zasad, zobacz [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState).

Wyniki powinny wyglądać podobnie do następujących:

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

Wyniki są zgodne z informacjami wyświetlanymi na karcie **zgodność zasobów** w przypisaniu zasad w widoku Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, wykonaj następujące polecenie:

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)