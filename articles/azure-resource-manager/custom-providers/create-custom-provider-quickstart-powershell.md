---
title: Tworzenie niestandardowego dostawcy zasobów platformy Azure za pomocą Azure PowerShell
description: Opisuje sposób tworzenia niestandardowego dostawcy zasobów platformy Azure za pomocą Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c19eb41210b6fba1935a0d158c8240375f4f8f5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533917"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Szybki start: tworzenie niestandardowego dostawcy zasobów platformy Azure przy użyciu Azure PowerShell

Z tego przewodnika Szybki start dowiesz się, jak utworzyć własnego niestandardowego dostawcę zasobów platformy Azure przy użyciu [modułu Az.CustomProviders programu](/powershell/module/az.customproviders) PowerShell.

> [!CAUTION]
> Niestandardowi dostawcy platformy Azure są obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest ona zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł wymaga zainstalowania modułu Az programu PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Aby uzyskać więcej informacji na temat instalowania modułu Az programu PowerShell, zobacz [Instalowanie Azure PowerShell](/powershell/azure/install-az-ps). Jeśli zdecydujesz się używać Cloud Shell, zobacz [Omówienie Azure Cloud Shell,](../../cloud-shell/overview.md) aby uzyskać więcej informacji.

> [!IMPORTANT]
> Gdy moduł **Az.CustomProviders** programu PowerShell jest w wersji zapoznawczej, należy zainstalować go oddzielnie przy użyciu `Install-Module` polecenia cmdlet . Gdy ten moduł programu PowerShell stanie się ogólnie dostępny, stanie się częścią przyszłych wersji modułu Az programu PowerShell i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której mają być naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) przy użyciu polecenia cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o określonej nazwie i w określonej lokalizacji.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Tworzenie niestandardowego dostawcy zasobów

Aby utworzyć lub zaktualizować niestandardowego dostawcę zasobów, użyj polecenia cmdlet [New-AzCustomProvider,](/powershell/module/az.customproviders/new-azcustomprovider) jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Uzyskiwanie niestandardowego manifestu dostawcy zasobów

Aby pobrać informacje o manifeście niestandardowego dostawcy zasobów, użyj polecenia cmdlet [Get-AzCustomProvider,](/powershell/module/az.customproviders/get-azcustomprovider) jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Tworzenie skojarzenia

Aby utworzyć lub zaktualizować skojarzenie, użyj polecenia cmdlet [New-AzCustomProviderAssociation,](/powershell/module/az.customproviders/new-azcustomproviderassociation) jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Uzyskiwanie skojarzenia

Aby pobrać informacje dotyczące skojarzenia, użyj polecenia cmdlet [Get-AzCustomProviderAssociation,](/powershell/module/az.customproviders/get-azcustomproviderassociation) jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, możesz je usunąć, uruchamiając poniższe przykłady.

### <a name="delete-an-association"></a>Usuwanie skojarzenia

Aby usunąć skojarzenie, użyj polecenia cmdlet [Remove-AzCustomProviderAssociation.](/powershell/module/az.customproviders/remove-azcustomproviderassociation) Poniższy przykład usuwa skojarzenie.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Usuwanie niestandardowego dostawcy zasobów

Aby usunąć niestandardowego dostawcę zasobów, użyj polecenia cmdlet [Remove-AzCustomProvider.](/powershell/module/az.customproviders/remove-azcustomprovider) Poniższy przykład usuwa niestandardowego dostawcę zasobów.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie zawarte w niej zasoby.
> Jeśli zasoby poza zakresem tego artykułu istnieją w określonej grupie zasobów, również zostaną usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na [temat niestandardowych dostawców zasobów platformy Azure.](overview.md)
