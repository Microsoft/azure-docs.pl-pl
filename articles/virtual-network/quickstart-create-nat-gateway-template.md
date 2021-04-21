---
title: Tworzenie bramy nat — szablon Resource Manager sieci
titleSuffix: Azure Virtual Network NAT
description: W tym przewodniku Szybki start pokazano, jak utworzyć bramę nat przy użyciu szablonu Azure Resource Manager usługi ARM.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: ea214cb98e1f75daccf783333a67c6d1b0eacfeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776610"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Szybki start: tworzenie bramy nat — szablon usługi ARM

Rozpoczynanie pracy z Translator adresów sieciowych usługi Virtual Network przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM). Ten szablon wdraża sieć wirtualną, zasób bramy nat i maszynę wirtualną z systemem Ubuntu. Maszyna wirtualna z systemem Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy nat.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Ten szablon jest skonfigurowany do tworzenia:

* Sieć wirtualna
* Zasób bramy translatora adresów sieciowych
* Maszyna wirtualna z systemem Ubuntu

Maszyna wirtualna z systemem Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy nat.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

W szablonie zdefiniowano dziewięć zasobów platformy Azure:

* **[Microsoft.Network/networkSecurityGroups:](/azure/templates/microsoft.network/networksecuritygroups)** tworzy sieciową grupę zabezpieczeń.
* **[Microsoft.Network/networkSecurityGroups/securityRules:](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** tworzy regułę zabezpieczeń.
* **[Microsoft.Network/publicIPAddresses:](/azure/templates/microsoft.network/publicipaddresses)** tworzy publiczny adres IP.
* **[Microsoft.Network/publicIPPrefixes:](/azure/templates/microsoft.network/publicipprefixes)** tworzy prefiks publicznego adresu IP.
* **[Microsoft.Compute/virtualMachines:](/azure/templates/Microsoft.Compute/virtualMachines)** tworzy maszynę wirtualną.
* **[Microsoft.Network/virtualNetworks:](/azure/templates/microsoft.network/virtualnetworks)** tworzy sieć wirtualną.
* **[Microsoft.Network/natGateways:](/azure/templates/microsoft.network/natgateways)** tworzy zasób bramy nat.
* **[Microsoft.Network/virtualNetworks/subnets:](/azure/templates/microsoft.network/virtualnetworks/subnets)** tworzy podsieć sieci wirtualnej.
* **[Microsoft.Network/networkinterfaces:](/azure/templates/microsoft.network/networkinterfaces)** tworzy interfejs sieciowy.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

**Interfejs wiersza polecenia platformy Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. westcentralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json"

$resourceGroupName = "myResourceGroupNAT"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Witryna Azure Portal**

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Grupy zasobów** w okienku po lewej stronie.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupNAT**

1. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

    ![Translator adresów sieciowych usługi Virtual Network grupy zasobów](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

**Interfejs wiersza polecenia platformy Azure**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group](/cli/azure/group#az_group_delete) delete.

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup.](/powershell/module/az.resources/remove-azresourcegroup)

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Witryna Azure Portal**

Gdy grupa zasobów, brama nat i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupNAT** zawierającą bramę nat, a następnie wybierz pozycję **Usuń.**

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:

* Zasób bramy translatora adresów sieciowych
* Sieć wirtualna
* Maszyna wirtualna z systemem Ubuntu

Maszyna wirtualna jest wdrażana w podsieci sieci wirtualnej skojarzonej z bramą nat.

Aby dowiedzieć się więcej na Translator adresów sieciowych usługi Virtual Network i Azure Resource Manager, przejdź do poniższych artykułów.

* Przeczytaj omówienie [Translator adresów sieciowych usługi Virtual Network](nat-overview.md)
* Przeczytaj o [zasobie NAT Gateway zasobów](nat-gateway-resource.md)
* Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)
