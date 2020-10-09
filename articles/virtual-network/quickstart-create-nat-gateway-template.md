---
title: Tworzenie Menedżer zasobów szablonu bramy translatora adresów sieciowych
titleSuffix: Azure Virtual Network NAT
description: W tym przewodniku szybki start pokazano, jak utworzyć bramę NAT przy użyciu szablonu Azure Resource Manager.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway by using an Azure Resource Manager template so that I can provide outbound connectivity for my virtual machines.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/09/2020
ms.author: allensu
ms.custom: subject-armqs
ms.openlocfilehash: fc4804070e0fa4ca6e9e54dcf6e04aafcc17f91a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88053902"
---
# <a name="create-a-nat-gateway---resource-manager-template"></a>Tworzenie Menedżer zasobów szablonu bramy translatora adresów sieciowych

Rozpocznij pracę z Virtual Network translatora adresów sieciowych przy użyciu szablonu Azure Resource Manager.  Ten szablon służy do wdrażania sieci wirtualnej, zasobu bramy translatora adresów sieciowych i Ubuntu maszyny wirtualnej. Maszyna wirtualna Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy translatora adresów sieciowych.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-nat-gateway-and-supporting-resources"></a>Tworzenie bramy translatora adresów sieciowych i obsługa zasobów

Ten szablon jest skonfigurowany do tworzenia 

* Sieć wirtualna 
* Zasób bramy translatora adresów sieciowych
* Ubuntu maszynę wirtualną

Maszyna wirtualna Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy translatora adresów sieciowych.

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-nat-gateway-1-vm/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json" range="1-335" highlight="256-282":::

Dziewięć zasobów platformy Azure są zdefiniowane w szablonie:

**Microsoft.Network**

* **[Microsoft. Network/natGateways](https://docs.microsoft.com/azure/templates/microsoft.network/natgateways)**: tworzy zasób bramy NAT.

* **[Microsoft. Network/networkSecurityGroups](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)**: tworzy sieciową grupę zabezpieczeń.

    * **[Microsoft. Network/networkSecurityGroups/securityRules](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups/securityrules)**: tworzy regułę zabezpieczeń.

* **[Microsoft. Network/adresów publicipaddress](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)**: tworzy publiczny adres IP.

* **[Microsoft. Network/publicIPPrefixes](https://docs.microsoft.com/azure/templates/microsoft.network/publicipprefixes)**: tworzy prefiks publicznego adresu IP.

* **[Microsoft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)**: tworzy sieć wirtualną.

    * **[Microsoft. Network/virtualNetworks/podsieci](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets)**: tworzy podsieć sieci wirtualnej.

* **[Microsoft. Network/NetworkInterfaces](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)**: tworzy interfejs sieciowy.

**Microsoft.Compute**

* **[Microsoft. COMPUTE/virtualMachines](https://docs.microsoft.com/azure/templates/Microsoft.Compute/virtualMachines)**: tworzy maszynę wirtualną.

### <a name="deploy-the-template"></a>Wdrażanie szablonu

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

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

3. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupNAT**

4. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

    ![Grupa zasobów Virtual Network translator adresów sieciowych](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

**Interfejs wiersza polecenia platformy Azure**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Azure Portal**

Gdy grupa zasobów, Brama translatora adresów sieciowych i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupNAT** , która zawiera bramę translatora adresów sieciowych, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:

* Zasób bramy translatora adresów sieciowych
* Sieć wirtualna
* Ubuntu maszynę wirtualną

Maszyna wirtualna jest wdrażana w podsieci sieci wirtualnej skojarzonej z bramą translatora adresów sieciowych. 

Aby dowiedzieć się więcej na temat Virtual Network translatora adresów sieciowych i Azure Resource Manager, przejdź do artykułów poniżej.

* Zapoznaj się [z omówieniem Virtual Network translatora adresów sieciowych](nat-overview.md)
* Przeczytaj informacje o [zasobie bramy translatora adresów sieciowych](nat-gateway-resource.md)
* Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)
