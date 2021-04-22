---
title: Tworzenie pulpitu nawigacyjnego Azure Portal za pomocą interfejsu wiersza polecenia platformy Azure
description: 'Szybki start: dowiedz się, jak utworzyć pulpit nawigacyjny w witrynie Azure Portal interfejsie wiersza polecenia platformy Azure. Pulpit nawigacyjny to ukierunkowany i zorganizowany widok zasobów w chmurze.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: 581c8cc4c2da275467bc39c5c2008b29a5bc0e0e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875759"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Szybki start: tworzenie pulpitu nawigacyjnego Azure Portal pomocą interfejsu wiersza polecenia platformy Azure

Pulpit nawigacyjny w Azure Portal to ukierunkowany i zorganizowany widok zasobów w chmurze. Ten artykuł koncentruje się na procesie tworzenia pulpitu nawigacyjnego przy użyciu interfejsu wiersza polecenia platformy Azure.
Pulpit nawigacyjny przedstawia wydajność maszyny wirtualnej, a także niektóre statyczne informacje i linki.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w ramach której będą naliczane opłaty za zasoby.
Wybierz subskrypcję za pomocą [polecenia az account set:](/cli/azure/account#az_account_set)

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Utwórz [grupę zasobów platformy Azure za](../azure-resource-manager/management/overview.md) pomocą polecenia az group [create](/cli/azure/group#az_group_create) lub użyj istniejącej grupy zasobów:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą [polecenia az vm create:](/cli/azure/vm#az_vm_create)

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Hasło musi być złożone.
> Jest to nowa nazwa użytkownika i hasło.
> Nie jest to na przykład konto, za pomocą których loguje się się do platformy Azure.
> Aby uzyskać więcej informacji, zobacz [wymagania dotyczące nazwy użytkownika](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) i [hasła.](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)

Wdrażanie rozpoczyna się i zwykle trwa kilka minut.
Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="download-the-dashboard-template"></a>Pobieranie szablonu pulpitu nawigacyjnego

Ponieważ pulpity nawigacyjne platformy Azure są zasobami, mogą być reprezentowane jako JSON.
Aby uzyskać więcej informacji, [zobacz Struktura pulpitów nawigacyjnych platformy Azure.](./azure-portal-dashboards-structure.md)

Pobierz następujący plik: [portal-dashboard-template-testvm.jsna .](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json)

Dostosuj pobrany szablon, zmieniając następujące wartości na swoje wartości:

* `<subscriptionID>`: Twoja subskrypcja
* `<rgName>`: grupa zasobów, na przykład `myResourceGroup`
* `<vmName>`: nazwa maszyny wirtualnej, na przykład `SimpleWinVM`
* `<dashboardTitle>`: tytuł pulpitu nawigacyjnego, na przykład `Simple VM Dashboard`
* `<location>`: Region świadczenia usługi Azure, na przykład `centralus`

Aby uzyskać więcej informacji, zobacz microsoft portal dashboards template reference (Informacje o szablonie [pulpitów nawigacyjnych w witrynie Microsoft Portal).](/azure/templates/microsoft.portal/dashboards)

## <a name="deploy-the-dashboard-template"></a>Wdrażanie szablonu pulpitu nawigacyjnego

Teraz możesz wdrożyć szablon z poziomu interfejsu wiersza polecenia platformy Azure.

1. Uruchom polecenie [az portal dashboard create,](/cli/azure/portal/dashboard#az_portal_dashboard_create) aby wdrożyć szablon:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Sprawdź, czy pulpit nawigacyjny został pomyślnie utworzony, uruchamiając [polecenie az portal dashboard show:](/cli/azure/portal/dashboard#az_portal_dashboard_show)

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Aby wyświetlić wszystkie pulpity nawigacyjne dla bieżącej subskrypcji, użyj [az portal dashboard list](/cli/azure/portal/dashboard#az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Możesz również wyświetlić wszystkie pulpity nawigacyjne dla grupy zasobów:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Pulpit nawigacyjny można zaktualizować za pomocą [polecenia az portal dashboard update:](/cli/azure/portal/dashboard#az_portal_dashboard_update)

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć maszynę wirtualną i skojarzony pulpit nawigacyjny, usuń grupę zasobów, która je zawiera.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie zawarte w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, również zostaną usunięte.

```azurecli
az group delete --name myResourceGroup
```

Aby usunąć tylko pulpit nawigacyjny, użyj [polecenia az portal dashboard delete:](/cli/azure/portal/dashboard#az_portal_dashboard_delete)

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat obsługi pulpitów nawigacyjnych za pomocą interfejsu wiersza polecenia platformy Azure, zobacz [az portal dashboard](/cli/azure/portal/dashboard).
