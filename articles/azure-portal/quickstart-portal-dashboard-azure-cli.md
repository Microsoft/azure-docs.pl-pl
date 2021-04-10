---
title: Tworzenie pulpitu nawigacyjnego Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure
description: 'Szybki Start: informacje na temat tworzenia pulpitu nawigacyjnego w Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure. Pulpit nawigacyjny to skoncentrowany i zorganizowany widok zasobów w chmurze.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ddfee1932c6887c6ca7593ca7a28c03e68e09899
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104613215"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Szybki Start: tworzenie pulpitu nawigacyjnego Azure Portal przy użyciu interfejsu wiersza polecenia platformy Azure

Pulpit nawigacyjny w Azure Portal to skoncentrowany i zorganizowany widok zasobów w chmurze. Ten artykuł koncentruje się na procesie tworzenia pulpitu nawigacyjnego przy użyciu interfejsu wiersza polecenia platformy Azure.
Pulpit nawigacyjny pokazuje wydajność maszyny wirtualnej, a także pewne informacje i linki statyczne.


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Jeśli masz wiele subskrypcji platformy Azure, wybierz odpowiednią subskrypcję, w której chcesz rozliczać zasoby.
Wybierz subskrypcję za pomocą polecenia [AZ Account Set](/cli/azure/account#az_account_set) :

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Utwórz [grupę zasobów platformy Azure](../azure-resource-manager/management/overview.md) za pomocą polecenia [AZ Group Create](/cli/azure/group#az_group_create) lub Użyj istniejącej grupy zasobów:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi w formie grupy.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [AZ VM Create](/cli/azure/vm#az_vm_create) :

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Hasło musi być złożone.
> To jest nowa nazwa użytkownika i hasło.
> Nie jest to na przykład konto używane do logowania się na platformie Azure.
> Aby uzyskać więcej informacji, zobacz Wymagania dotyczące [nazwy użytkownika](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) i [hasła](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

Wdrożenie rozpocznie się i zazwyczaj trwa kilka minut.
Po zakończeniu wdrażania przejdź do następnej sekcji.

## <a name="download-the-dashboard-template"></a>Pobierz szablon pulpitu nawigacyjnego

Ponieważ pulpity nawigacyjne platformy Azure to zasoby, mogą być reprezentowane jako dane JSON.
Aby uzyskać więcej informacji, zobacz [strukturę pulpitów nawigacyjnych platformy Azure](./azure-portal-dashboards-structure.md).

Pobierz następujący plik: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Dostosuj pobrany szablon, zmieniając następujące wartości na wartości:

* `<subscriptionID>`: Twoja subskrypcja
* `<rgName>`: Grupa zasobów, na przykład `myResourceGroup`
* `<vmName>`: Nazwa maszyny wirtualnej, na przykład `SimpleWinVM`
* `<dashboardTitle>`: Tytuł pulpitu nawigacyjnego, na przykład `Simple VM Dashboard`
* `<location>`: Twój region świadczenia usługi Azure, na przykład `centralus`

Aby uzyskać więcej informacji, zobacz [Dokumentacja szablonu pulpitów nawigacyjnych w portalu Microsoft](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Wdrażanie szablonu pulpitu nawigacyjnego

Teraz można wdrożyć szablon z poziomu interfejsu wiersza polecenia platformy Azure.

1. Uruchom polecenie [AZ Portal Dashboard Create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) , aby wdrożyć szablon:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Sprawdź, czy pulpit nawigacyjny został utworzony pomyślnie, uruchamiając polecenie [AZ Portal Dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) :

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Aby wyświetlić wszystkie pulpity nawigacyjne dla bieżącej subskrypcji, użyj polecenie [AZ Portal Dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

Możesz również wyświetlić wszystkie pulpity nawigacyjne dla grupy zasobów:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

Pulpit nawigacyjny można zaktualizować za pomocą polecenia [AZ Portal pulpitu nawigacyjnego Update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) :

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć maszynę wirtualną i skojarzony z nią pulpit nawigacyjny, Usuń grupę zasobów, która ją zawiera.

> [!CAUTION]
> Poniższy przykład usuwa określoną grupę zasobów i wszystkie znajdujące się w niej zasoby.
> Jeśli zasoby spoza zakresu tego artykułu istnieją w określonej grupie zasobów, zostaną również usunięte.

```azurecli
az group delete --name myResourceGroup
```

Aby usunąć tylko pulpit nawigacyjny, użyj polecenia [AZ Portal Dashboard Delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) :

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o obsłudze interfejsu wiersza polecenia platformy Azure dla pulpitów nawigacyjnych, zobacz [AZ Portal Dashboard](/cli/azure/ext/portal/portal/dashboard).
