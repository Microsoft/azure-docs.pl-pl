---
title: Zarządzanie zasobami — interfejs wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure i Azure Resource Manager, aby zarządzać zasobami. Pokazuje, jak wdrażać i usuwać zasoby.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 077ebdb4dd33249923064a4f5ed20c5641a82e26
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/19/2020
ms.locfileid: "97695890"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak zarządzać zasobami platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure z [Azure Resource Manager](overview.md) . Aby zarządzać grupami zasobów, zobacz [Zarządzanie grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resource-groups-cli.md).

Inne artykuły dotyczące zarządzania zasobami:

- [Zarządzanie zasobami platformy Azure przy użyciu Azure Portal](manage-resources-portal.md)
- [Zarządzanie zasobami platformy Azure przy użyciu Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zasoby platformy Azure można wdrażać bezpośrednio przy użyciu interfejsu wiersza polecenia platformy Azure lub wdrożyć szablon Menedżer zasobów w celu utworzenia zasobów platformy Azure.

### <a name="deploy-a-resource"></a>Wdrażanie zasobu

Poniższy skrypt tworzy konto magazynu.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Wdrażanie szablonu

Poniższy skrypt tworzy szablon szybkiego startu, aby utworzyć konto magazynu. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie szablonów ARM przy użyciu Visual Studio Code](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów Menedżer zasobów i interfejsu wiersza polecenia platformy Azure](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](../templates/deploy-to-subscription.md#resource-groups).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Wdrażanie zasobów w wielu subskrypcjach lub grupach zasobów

Zwykle wszystkie zasoby w szablonie są wdrażane w jednej grupie zasobów. Istnieją jednak scenariusze, w których należy wspólnie wdrożyć zestaw zasobów, ale umieścić je w różnych grupach zasobów lub subskrypcjach. Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów platformy Azure w wielu subskrypcjach lub grupach zasobów](../templates/deploy-to-resource-group.md).

## <a name="delete-resources"></a>Usuwanie zasobów

Poniższy skrypt pokazuje, jak usunąć konto magazynu.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](delete-resource-group.md).

## <a name="move-resources"></a>Przenoszenie zasobów

Poniższy skrypt pokazuje, jak usunąć konto magazynu z jednej grupy zasobów do innej grupy zasobów.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](move-resource-group-and-subscription.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji).

## <a name="lock-resources"></a>Blokowanie zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób. 

Poniższy skrypt blokuje konto magazynu, aby nie można było usunąć konta.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Następujący skrypt pobiera wszystkie blokady dla konta magazynu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Następujący skrypt usuwa blokadę konta magazynu:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resources"></a>Tagowanie zasobów

Tagowanie pomaga w logicznym organizowaniu grupy zasobów i zasobów. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md#azure-cli).

## <a name="manage-access-to-resources"></a>Zarządzanie dostępem do zasobów

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](../templates/template-syntax.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](../index.yml).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).