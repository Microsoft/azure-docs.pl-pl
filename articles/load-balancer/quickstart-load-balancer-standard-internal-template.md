---
title: 'Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia przy użyciu szablonu'
description: Ten przewodnik Szybki Start przedstawia sposób tworzenia wewnętrznego modułu równoważenia obciążenia platformy Azure przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: allensu
ms.date: 09/14/2020
ms.openlocfilehash: a029ee79019c958eaf240a4656c96c6e1848f04d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700328"
---
# <a name="quickstart-create-an-internal-load-balancer-to-load-balance-vms-by-using-an-arm-template"></a>Szybki Start: Tworzenie wewnętrznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia wewnętrznego modułu równoważenia obciążenia platformy Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-2-vms-internal-load-balancer%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/201-2-vms-internal-load-balancer).

:::code language="json" source="~/quickstart-templates/201-2-vms-internal-load-balancer/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts): konta magazynu maszyny wirtualnej do diagnostyki rozruchu.
- [**Microsoft. COMPUTE/availabilitySets**](/azure/templates/microsoft.compute/availabilitySets): zestaw dostępności dla maszyn wirtualnych.
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks): Sieć wirtualna dla modułu równoważenia obciążenia i maszyn wirtualnych.
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkInterfaces): interfejsy sieciowe dla maszyn wirtualnych.
- [**Microsoft. Network/loadBalancers**](/azure/templates/microsoft.network/loadBalancers): wewnętrzny moduł równoważenia obciążenia.
- [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualMachines): maszyny wirtualne.

Aby znaleźć więcej szablonów związanych z Azure Load Balancer, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

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

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupLB**

1. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

:::image type="content" source="media/quickstart-load-balancer-standard-internal-template/verify-creation.png" alt-text="Użytkownik Azure Portal do weryfikowania tworzenia zasobów." border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name myResourceGroupLB
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia szablonu, zobacz:

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie i wdrażanie pierwszego szablonu ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)