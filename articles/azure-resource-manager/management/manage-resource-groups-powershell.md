---
title: Zarządzanie grupami zasobów — Azure PowerShell
description: Użyj Azure PowerShell do zarządzania grupami zasobów przy użyciu Azure Resource Manager. Pokazuje, jak tworzyć, wyświetlać i usuwać grupy zasobów.
author: mumian
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: jgao
ms.openlocfilehash: fb8cd45842e2f9b64048aa939f7b6adc9b0e6e0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98703686"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Zarządzanie grupami zasobów Azure Resource Manager przy użyciu Azure PowerShell

Dowiedz się, jak używać Azure PowerShell z [Azure Resource Manager](overview.md) do zarządzania grupami zasobów platformy Azure. Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu Azure PowerShell](manage-resources-powershell.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure Portal](manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Co to jest Grupa zasobów

Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc, Dodaj zasoby, które mają ten sam cykl życia do tej samej grupy zasobów, dzięki czemu możesz łatwo wdrażać, aktualizować i usuwać je jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. W celu zapewnienia zgodności może być konieczne upewnienie się, że dane są przechowywane w konkretnym regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. W przypadku określania lokalizacji grupy zasobów należy określić miejsce przechowywania metadanych.

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Poniższy skrypt programu PowerShell tworzy grupę zasobów.

```azurepowershell-interactive
New-AzResourceGroup -Name demoResourceGroup -Location westus
```

## <a name="list-resource-groups"></a>Wyświetl listę grup zasobów

Poniższy skrypt programu PowerShell zawiera listę grup zasobów w ramach subskrypcji.

```azurepowershell-interactive
Get-AzResourceGroup
```

Aby uzyskać jedną grupę zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Usuwanie grup zasobów

Następujący skrypt programu PowerShell usuwa grupę zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zobacz [wdrażanie zasobów w istniejącej grupie zasobów](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Aby sprawdzić poprawność wdrożenia grupy zasobów, zobacz [test-AzResourceGroupDeployment](/powershell/module/Az.Resources/Test-AzResourceGroupDeployment).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie przy użyciu szablonu Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie w przypadku niepowodzenia wdrożenia

Ta funkcja jest również znana jako *Rollback w przypadku błędu*. Aby uzyskać więcej informacji, zobacz [ponowne wdrażanie w przypadku niepowodzenia wdrożenia](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resource-groups"></a>Zablokuj grupy zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób. 

Poniższy skrypt blokuje grupę zasobów, aby nie można było usunąć grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Następujący skrypt pobiera wszystkie blokady dla grupy zasobów:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Dodawanie tagów do grup zasobów

Możesz zastosować znaczniki do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Po skonfigurowaniu grupy zasobów można wyświetlić szablon Menedżer zasobów dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Automatyzuj przyszłe wdrożenia rozwiązania, ponieważ szablon zawiera kompletną infrastrukturę.
- Poznaj składnię szablonu, przeglądając JavaScript Object Notation (JSON), który reprezentuje Twoje rozwiązanie.

Aby wyeksportować wszystkie zasoby w grupie zasobów, należy użyć polecenia cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) i podać nazwę grupy zasobów.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Zapisuje szablon jako plik lokalny.

Zamiast eksportować wszystkie zasoby w grupie zasobów, możesz wybrać zasoby do wyeksportowania.

Aby wyeksportować jeden zasób, Przekaż ten identyfikator zasobu.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Aby wyeksportować więcej niż jeden zasób, Przekaż identyfikatory zasobów w tablicy.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Podczas eksportowania szablonu można określić, czy parametry są używane w szablonie. Domyślnie parametry nazw zasobów są uwzględniane, ale nie mają wartości domyślnej. Wartość parametru należy przekazać podczas wdrażania.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

W zasobie parametr jest używany jako nazwa.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Jeśli używasz `-IncludeParameterDefaultValue` parametru podczas eksportowania szablonu, parametr szablonu zawiera wartość domyślną ustawioną na bieżącą wartość. Możesz użyć tej wartości domyślnej lub zastąpić wartość domyślną, przekazując inną wartość.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Jeśli używasz `-SkipResourceNameParameterization` parametru podczas eksportowania szablonu, parametry nazw zasobów nie są uwzględnione w szablonie. Zamiast tego nazwa zasobu jest ustawiana bezpośrednio w ramach zasobu na jego bieżącą wartość. Nie można dostosować nazwy podczas wdrażania.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Funkcja eksportowania szablonu nie obsługuje eksportowania zasobów Azure Data Factory. Aby dowiedzieć się, jak wyeksportować zasoby Data Factory, zobacz [kopiowanie lub klonowanie fabryki danych w Azure Data Factory](../../data-factory/copy-clone-data-factory.md).

Aby wyeksportować zasoby utworzone za pomocą klasycznego modelu wdrażania, należy [je zmigrować do modelu wdrażania Menedżer zasobów](../../virtual-machines/migration-classic-resource-manager-overview.md).

Aby uzyskać więcej informacji, zobacz [Eksportowanie jednego i wielu zasobów do szablonu w Azure Portal](../templates/export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Zarządzanie dostępem do grup zasobów

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](../templates/template-syntax.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](../index.yml).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).