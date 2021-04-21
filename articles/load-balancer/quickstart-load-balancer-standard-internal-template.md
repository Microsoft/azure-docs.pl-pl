---
title: 'Szybki start: tworzenie wewnętrznego równoważenia obciążenia przy użyciu szablonu'
description: W tym przewodniku Szybki start pokazano, jak utworzyć wewnętrzny usługę Azure Load Balancer przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: 00126dde55ffe584be611ddf268bb759e127d7a1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788749"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Szybki start: tworzenie wewnętrznego równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak utworzyć wewnętrzny Azure Resource Manager azure load balancer za pomocą szablonu usługi Azure Load Balancer.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start pochodzi z szablonów [szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer)

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft.Storage/storageAccounts:**](/azure/templates/microsoft.storage/storageaccounts)konta magazynu maszyny wirtualnej na potrzeby diagnostyki rozruchu.
- [**Microsoft.Compute/availabilitySets:**](/azure/templates/microsoft.compute/availabilitySets)zestaw dostępności dla maszyn wirtualnych.
- [**Microsoft.Network/virtualNetworks:**](/azure/templates/microsoft.network/virtualNetworks)Sieć wirtualna dla usługi równoważenia obciążenia i maszyn wirtualnych.
- [**Microsoft.Network/networkInterfaces:**](/azure/templates/microsoft.network/networkInterfaces)interfejsy sieciowe dla maszyn wirtualnych.
- [**Microsoft.Network/loadBalancers:**](/azure/templates/microsoft.network/loadBalancers)wewnętrzny równoważenie obciążenia.
- [**Microsoft.Compute/virtualMachines:**](/azure/templates/microsoft.compute/virtualMachines)Maszyny wirtualne.

Aby znaleźć więcej szablonów powiązanych z Azure Load Balancer, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

**Interfejs wiersza polecenia platformy Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupLB"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Grupy zasobów** w okienku po lewej stronie.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupLB**

1. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Użytkownik Azure Portal, aby zweryfikować tworzenie zasobów." border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, możesz je usunąć za pomocą polecenia [az group delete.](/cli/azure/group#az_group_delete)

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: tworzenie i wdrażanie pierwszego szablonu usługi ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
