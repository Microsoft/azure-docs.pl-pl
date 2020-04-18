---
title: 'Szybki start: tworzenie bramy NAT — szablon Menedżera zasobów'
titleSuffix: Azure Virtual Network NAT
description: Ten przewodnik Szybki start pokazuje, jak utworzyć bramę NAT przy użyciu szablonu Usługi Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: 3850f3f22e730e46f6d278b6cef0e17d357b126d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81618053"
---
# <a name="quickstart-create-a-nat-gateway---resource-manager-template"></a>Szybki start: tworzenie bramy NAT — szablon Menedżera zasobów

Wprowadzenie do translatora adresów sieci wirtualnych przy użyciu szablonu usługi Azure Resource Manager.  Ten szablon wdraża sieć wirtualną, zasób bramy NAT i maszynę wirtualną Ubuntu. Maszyna wirtualna Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy NAT.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Tworzenie bramy NAT i zasobów pomocniczych

Ten szablon jest skonfigurowany do tworzenia 

* Sieć wirtualna 
* Zasób bramy TRANSLATOR
* Maszyna wirtualna Ubuntu

Maszyna wirtualna Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy NAT.

### <a name="review-the-template"></a>Przejrzyj szablon

Szablon używany w tym przewodniku Szybki start pochodzi z [szablonów szybki start platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

W szablonie zdefiniowano dziewięć zasobów platformy Azure:

**Microsoft.Network**

* **[Microsoft.Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: Tworzy zasób bramy NAT.

* **[Microsoft.Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: Tworzy grupę zabezpieczeń sieci.

    * **[Microsoft.Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: Tworzy regułę zabezpieczeń.

* **[Microsoft.Network/publicIPAddresses](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: Tworzy publiczny adres IP.

* **[Microsoft.Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: Tworzy publiczny prefiks IP.

* **[Microsoft.Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: Tworzy sieć wirtualną.

    * **[Microsoft.Network/virtualNetworks/subnets](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: Tworzy podsieć sieci wirtualnej.

* **[Microsoft.Network/networkinterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: Tworzy interfejs sieciowy.

**Microsoft.Compute**

* **[Microsoft.Compute/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: Tworzy maszynę wirtualną.

### <a name="deploy-the-template"></a>Wdrożenie szablonu

**Interfejs wiersza polecenia platformy Azure**

```azurecli-interactive
read -p "Enter the location (i.e. westcentralus): " location
resourceGroupName="myResourceGroupNAT"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
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

**Azure Portal**

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przeglądanie wdrożonych zasobów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Wybierz **grupę zasobów** z lewego okienka.

3. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupNAT**

4. Sprawdź, czy w grupie zasobów utworzono następujące zasoby:

    ![Grupa zasobów NAT sieci wirtualnej](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

**Interfejs wiersza polecenia platformy Azure**

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group#az-group-delete) aby usunąć grupę zasobów i wszystkie zasoby zawarte w.

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Gdy nie jest już potrzebne, można użyć [polecenia Usuń-AzResourceGroup,](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) aby usunąć grupę zasobów i wszystkie zasoby zawarte w.

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Gdy nie jest już potrzebna, usuń grupę zasobów, bramę NAT i wszystkie powiązane zasoby. Wybierz grupę zasobów **myResourceGroupNAT** zawierającą bramę NAT, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:

* Zasób bramy TRANSLATOR
* Sieć wirtualna
* Maszyna wirtualna Ubuntu

Maszyna wirtualna jest wdrażana w podsieci sieci wirtualnej skojarzonej z bramą NAT. 

Aby dowiedzieć się więcej o translatora adresów sieci wirtualnej i usłudze Azure Resource Manager, przejdź do poniższych artykułów.

* Przeczytaj [przegląd translatora adresów sieci wirtualnych](nat-overview.md)
* Przeczytaj o [zasobie bramy NAT](nat-gateway-resource.md)
* Dowiedz się więcej o [usłudze Azure Resource Manager](../azure-resource-manager/management/overview.md)
