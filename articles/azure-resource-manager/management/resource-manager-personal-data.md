---
title: Dane osobowe
description: Dowiedz się, jak zarządzać danymi osobowy skojarzonymi z operacjami Azure Resource Manager danych.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 9087d3e46f38aab3de7774ea341ebd9cbc2d7d1f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785959"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Zarządzanie danymi osobowy skojarzonymi z Azure Resource Manager

Aby uniknąć ujawniania poufnych informacji, usuń wszelkie informacje osobiste podane we wdrożeniach, grupach zasobów lub tagach. Azure Resource Manager udostępnia operacje, które umożliwiają zarządzanie danymi osobowymi, które być może podano we wdrożeniach, grupach zasobów lub tagach.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Usuwanie danych osobowych w historii wdrażania

W przypadku wdrożeń Resource Manager wartości parametrów i komunikaty o stanie w historii wdrożenia. Te wartości są utrwalane do momentu usunięcia wdrożenia z historii. Aby sprawdzić, czy w tych wartościach podano dane osobowe, wyświetl listę wdrożeń. Jeśli znajdziesz dane osobowe, usuń wdrożenia z historii.

Aby wyświetlić **listę wdrożeń** w historii, użyj:

* [Lista według grupy zasobów](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [az deployment group list](/cli/azure/deployment/group#az_deployment_group_list)

Aby usunąć **wdrożenia** z historii, użyj:

* [Usuwanie](/rest/api/resources/deployments/delete)
* [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [az deployment group delete](/cli/azure/deployment/group#az_deployment_group_delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Usuwanie danych osobowych w nazwach grup zasobów

Nazwa grupy zasobów jest zachowywana do momentu usunięcia grupy zasobów. Aby sprawdzić, czy w nazwach podano dane osobowe, wyświetl listę grup zasobów. Jeśli znajdziesz dane osobowe, [przenieś zasoby](move-resource-group-and-subscription.md) do nowej grupy zasobów i usuń grupę zasobów z danymi osobistymi w nazwie.

Aby wyświetlić **listę grup zasobów,** użyj:

* [Lista](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az group list](/cli/azure/group#az_group_list)

Aby usunąć **grupy zasobów,** użyj:

* [Usuwanie](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az_group_delete)

## <a name="delete-personal-data-in-tags"></a>Usuwanie danych osobowych w tagach

Nazwy i wartości tagów są zachowywane do momentu usunięcia lub zmodyfikowania tagu. Aby sprawdzić, czy w tagach podano dane osobowe, wyświetl listę tagów. Jeśli znajdziesz dane osobowe, usuń tagi.

Aby wyświetlić **listę tagów,** użyj:

* [Lista](/rest/api/resources/tags/list)
* [Get-AzTag](/powershell/module/az.resources/Get-AzTag)
* [az tag list](/cli/azure/tag#az_tag_list)

Aby usunąć **tagi,** użyj:

* [Usuwanie](/rest/api/resources/tags/delete)
* [Remove-AzTag](/powershell/module/az.resources/Remove-AzTag)
* [az tag delete](/cli/azure/tag#az_tag_delete)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać omówienie Azure Resource Manager, zobacz Co to jest [Resource Manager?](overview.md)