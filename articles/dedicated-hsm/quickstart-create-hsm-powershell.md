---
title: 'Szybki start: tworzenie aplikacji Azure Dedicated HSM pomocą Azure PowerShell'
description: Tworzenie modułu Azure Dedicated HSM przy użyciu programu Azure PowerShell
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/13/2020
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: d5b6d0399ceb98caf9bdd7bbd725e6d92af0eaa3
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537801"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Szybki start: tworzenie aplikacji Azure Dedicated HSM pomocą Azure PowerShell

W tym artykule opisano, jak utworzyć Azure Dedicated HSM przy użyciu [modułu Az.DedicatedHsm programu](/powershell/module/az.dedicatedhsm) PowerShell.

## <a name="requirements"></a>Wymagania

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Gdy moduł **Az.DedicatedHsm** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, będzie częścią przyszłych wydań modułu Az programu PowerShell i będzie domyślnie dostępny z poziomu usługi Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której mają być naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) przy użyciu polecenia cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o określonej nazwie i w określonej lokalizacji.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Tworzenie dedykowanego modułu HSM

Aby utworzyć dedykowany moduł HSM, użyj polecenia cmdlet [New-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm) Poniższy przykład tworzy dedykowany moduł HSM w określonej subskrypcji.

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Uzyskiwanie dedykowanego modułu HSM

Aby pobrać informacje dotyczące istniejącego dedykowanego modułu HSM, użyj polecenia cmdlet [Get-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm) Poniższy przykład pobiera określony dedykowany moduł HSM.

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Aktualizowanie dedykowanego modułu HSM

Aby zaktualizować dedykowany moduł HSM, użyj polecenia cmdlet [Update-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm) Poniższy przykład aktualizuje dedykowany moduł HSM w określonej subskrypcji.

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, możesz je usunąć, uruchamiając poniższe przykłady.

### <a name="remove-a-dedicated-hsm"></a>Usuwanie dedykowanego modułu HSM

Aby usunąć dedykowany moduł HSM, użyj polecenia cmdlet [Remove-AzDedicatedHsm.](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm) Poniższy przykład usuwa określony dedykowany moduł HSM.

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie zawarte w niej zasoby.
> Jeśli zasoby poza zakresem tego artykułu istnieją w określonej grupie zasobów, również zostaną usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [Azure Dedicated HSM](overview.md).
