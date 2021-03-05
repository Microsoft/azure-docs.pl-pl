---
title: Zarządzanie grupami zasobów — interfejs wiersza polecenia platformy Azure
description: Użyj interfejsu wiersza polecenia platformy Azure, aby zarządzać grupami zasobów za pomocą Azure Resource Manager. Pokazuje, jak tworzyć, wyświetlać i usuwać grupy zasobów.
author: mumian
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: e28b66844eaa0b73c2654175dea2e31d3cd75f5d
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172100"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Zarządzanie grupami zasobów Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure

Dowiedz się, jak zarządzać grupami zasobów platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure z [Azure Resource Manager](overview.md) . Aby zarządzać zasobami platformy Azure, zobacz [Zarządzanie zasobami platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](manage-resources-cli.md).

Inne artykuły dotyczące zarządzania grupami zasobów:

- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure Portal](manage-resources-portal.md)
- [Zarządzanie grupami zasobów platformy Azure przy użyciu Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Co to jest Grupa zasobów

Grupa zasobów to kontener zawierający powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji. Ogólnie rzecz biorąc, Dodaj zasoby, które mają ten sam cykl życia do tej samej grupy zasobów, dzięki czemu możesz łatwo wdrażać, aktualizować i usuwać je jako grupę.

Grupa zasobów przechowuje metadane dotyczące zasobów. Z tego powodu określając lokalizację dla grupy zasobów, określasz miejsce, w którym przechowywane są metadane. W celu zapewnienia zgodności może być konieczne upewnienie się, że dane są przechowywane w konkretnym regionie.

Grupa zasobów przechowuje metadane dotyczące zasobów. W przypadku określania lokalizacji grupy zasobów należy określić miejsce przechowywania metadanych.

## <a name="create-resource-groups"></a>Tworzenie grup zasobów

Poniższe polecenie interfejsu wiersza polecenia tworzy grupę zasobów.

```azurecli-interactive
az group create --name demoResourceGroup --location westus
```

## <a name="list-resource-groups"></a>Wyświetl listę grup zasobów

Poniższy skrypt interfejsu wiersza polecenia zawiera listę grup zasobów w ramach subskrypcji.

```azurecli-interactive
az group list
```

Aby uzyskać jedną grupę zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Usuwanie grup zasobów

Następujący skrypt interfejsu wiersza polecenia usuwa grupę zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Aby uzyskać więcej informacji na temat sposobu, w jaki Azure Resource Manager kolejności usuwania zasobów, zobacz [Azure Resource Manager Usuwanie grupy zasobów](delete-resource-group.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Wdrażanie zasobów w istniejącej grupie zasobów

Zobacz [wdrażanie zasobów w istniejącej grupie zasobów](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Wdrażanie grupy zasobów i zasobów

Można utworzyć grupę zasobów i wdrożyć zasoby w grupie przy użyciu szablonu Menedżer zasobów. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów i wdrażanie zasobów](../templates/deploy-to-subscription.md#resource-groups).

## <a name="redeploy-when-deployment-fails"></a>Wdróż ponownie w przypadku niepowodzenia wdrożenia

Ta funkcja jest również znana jako *Rollback w przypadku błędu*. Aby uzyskać więcej informacji, zobacz [ponowne wdrażanie w przypadku niepowodzenia wdrożenia](../templates/rollback-on-error.md).

## <a name="move-to-another-resource-group-or-subscription"></a>Przenieś do innej grupy zasobów lub subskrypcji

Zasoby w grupie można przenieść do innej grupy zasobów. Aby uzyskać więcej informacji, zobacz [przenoszenie zasobów](manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Zablokuj grupy zasobów

Blokowanie uniemożliwia innym użytkownikom w organizacji przypadkowe usuwanie lub modyfikowanie krytycznych zasobów, takich jak subskrypcja platformy Azure, Grupa zasobów lub zasób.

Poniższy skrypt blokuje grupę zasobów, aby nie można było usunąć grupy zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName
```

Następujący skrypt pobiera wszystkie blokady dla grupy zasobów:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName
```

Następujący skrypt usuwa blokadę:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

## <a name="tag-resource-groups"></a>Dodawanie tagów do grup zasobów

Możesz zastosować znaczniki do grup zasobów i zasobów, aby logicznie organizować zasoby. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Eksportowanie grup zasobów do szablonów

Po pomyślnym skonfigurowaniu grupy zasobów warto wyświetlić szablon Menedżer zasobów dla grupy zasobów. Eksportowanie szablonu oferuje dwie korzyści:

- Automatyzuj przyszłe wdrożenia rozwiązania, ponieważ szablon zawiera całą kompletną infrastrukturę.
- Poznaj składnię szablonu, przeglądając JavaScript Object Notation (JSON), który reprezentuje Twoje rozwiązanie.

Aby wyeksportować wszystkie zasoby w grupie zasobów, użyj polecenie [AZ Group Export](/cli/azure/group#az_group_export) i podaj nazwę grupy zasobów.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName
```

Skrypt wyświetla szablon w konsoli programu. Skopiuj kod JSON i Zapisz go jako plik.

Zamiast eksportować wszystkie zasoby w grupie zasobów, możesz wybrać zasoby do wyeksportowania.

Aby wyeksportować jeden zasób, Przekaż ten identyfikator zasobu.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $resourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az group export --resource-group $resourceGroupName --resource-ids $storageAccount
```

Aby wyeksportować więcej niż jeden zasób, należy przekazać identyfikatory zasobów oddzielone spacją. Aby wyeksportować wszystkie zasoby, nie należy określać tego argumentu ani dostawy "*".

```azurecli-interactive
az group export --resource-group <resource-group-name> --resource-ids $storageAccount1 $storageAccount2
```

Podczas eksportowania szablonu można określić, czy parametry są używane w szablonie. Domyślnie parametry nazw zasobów są uwzględniane, ale nie mają wartości domyślnej. Wartość parametru należy przekazać podczas wdrażania.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
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

Jeśli używasz `--include-parameter-default-value` parametru podczas eksportowania szablonu, parametr szablonu zawiera wartość domyślną ustawioną na bieżącą wartość. Możesz użyć tej wartości domyślnej lub zastąpić wartość domyślną, przekazując inną wartość.

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

Jeśli używasz `--skip-resource-name-params` parametru podczas eksportowania szablonu, parametry nazw zasobów nie są uwzględnione w szablonie. Zamiast tego nazwa zasobu jest ustawiana bezpośrednio w ramach zasobu na jego bieżącą wartość. Nie można dostosować nazwy podczas wdrażania.

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

[Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md) umożliwia zarządzanie dostępem do zasobów na platformie Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się Azure Resource Manager, zobacz [Omówienie usługi Azure Resource Manager](overview.md).
- Aby poznać składnię szablonu Menedżer zasobów, zobacz [Opis struktury i składni Azure Resource Manager szablonów](../templates/template-syntax.md).
- Aby dowiedzieć się, jak opracowywać szablony, zobacz [Samouczki krok po kroku](../index.yml).
- Aby wyświetlić Azure Resource Manager Schematy szablonów, zobacz [Dokumentacja szablonu](/azure/templates/).