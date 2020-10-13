---
title: Tworzenie niestandardowego dostawcy zasobów platformy Azure za pomocą Azure PowerShell
description: Opisuje sposób tworzenia niestandardowego dostawcy zasobów platformy Azure przy użyciu Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6912f7f6fdc88c5d611bfbfd78f15e5f7a949f70
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951846"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Szybki Start: Tworzenie niestandardowego dostawcy zasobów platformy Azure przy użyciu Azure PowerShell

W tym przewodniku szybki start dowiesz się, jak utworzyć własnego niestandardowego dostawcę zasobów platformy Azure przy użyciu modułu [AZ. CustomProviders](/powershell/module/az.customproviders) PowerShell.

> [!CAUTION]
> Dostawcy niestandardowi platformy Azure są obecnie w publicznej wersji zapoznawczej. Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług. Nie jest to zalecane w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Wymagania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

Jeśli zdecydujesz się używać programu PowerShell lokalnie, ten artykuł będzie wymagał instalacji modułu AZ PowerShell i nawiązania połączenia z kontem platformy Azure przy użyciu polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Aby uzyskać więcej informacji na temat instalowania modułu AZ PowerShell module, zobacz [Install Azure PowerShell](/powershell/azure/install-az-ps). Jeśli zdecydujesz się używać Cloud Shell, zobacz [omówienie Azure Cloud Shell](../../cloud-shell/overview.md) , aby uzyskać więcej informacji.

> [!IMPORTANT]
> Mimo że moduł **AZ. CustomProviders** PowerShell jest w wersji zapoznawczej, należy go zainstalować oddzielnie przy użyciu `Install-Module` polecenia cmdlet. Po ogólnym udostępnieniu tego modułu programu PowerShell jest on częścią przyszłej wersji modułu AZ PowerShell releases i będzie dostępny natywnie z poziomu Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której będą naliczane opłaty za zasoby. Wybierz określoną subskrypcję za pomocą polecenia cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów platformy Azure](../../azure-resource-manager/management/overview.md) za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

Poniższy przykład tworzy grupę zasobów o określonej nazwie i w określonej lokalizacji.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Tworzenie niestandardowego dostawcy zasobów

Aby utworzyć lub zaktualizować niestandardowego dostawcę zasobów, należy użyć polecenia cmdlet [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) , jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Pobierz manifest niestandardowego dostawcy zasobów

Aby pobrać informacje o niestandardowym manifeście dostawcy zasobów, należy użyć polecenia cmdlet [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) , jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Tworzenie skojarzenia

Aby utworzyć lub zaktualizować skojarzenie, należy użyć polecenia cmdlet [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) , jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Pobierz skojarzenie

Aby pobrać informacje o skojarzeniu, należy użyć polecenia cmdlet [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) , jak pokazano w poniższym przykładzie.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli zasoby utworzone w tym artykule nie są potrzebne, można je usunąć, uruchamiając następujące przykłady.

### <a name="delete-an-association"></a>Usuwanie skojarzenia

Aby usunąć skojarzenie, należy użyć polecenia cmdlet [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) . Poniższy przykład usuwa skojarzenie.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Usuwanie niestandardowego dostawcy zasobów

Aby usunąć niestandardowego dostawcę zasobów, należy użyć polecenia cmdlet [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) . Poniższy przykład usuwa niestandardowego dostawcę zasobów.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Usuwanie grupy zasobów

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [niestandardowych dostawcach zasobów platformy Azure](overview.md).