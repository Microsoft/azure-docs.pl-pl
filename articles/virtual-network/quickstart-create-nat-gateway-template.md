---
title: Tworzenie Menedżer zasobów szablonu bramy translatora adresów sieciowych
titleSuffix: Azure Virtual Network NAT
description: W tym przewodniku szybki start pokazano, jak utworzyć bramę NAT przy użyciu szablonu Azure Resource Manager (szablon ARM).
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
ms.date: 10/27/2020
ms.author: allensu
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 95856db9288e5860dfab47dce506d1e7d6de1ffc
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913334"
---
# <a name="quickstart-create-a-nat-gateway---arm-template"></a>Szybki Start: Tworzenie bramy translatora adresów sieciowych — szablon ARM

Rozpocznij pracę z Virtual Network translatora adresów sieciowych przy użyciu szablonu Azure Resource Manager (szablon ARM). Ten szablon służy do wdrażania sieci wirtualnej, zasobu bramy translatora adresów sieciowych i Ubuntu maszyny wirtualnej. Maszyna wirtualna Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy translatora adresów sieciowych.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure** . Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-nat-gateway-1-vm).

Ten szablon jest skonfigurowany do tworzenia:

* Sieć wirtualna
* Zasób bramy translatora adresów sieciowych
* Ubuntu maszynę wirtualną

Maszyna wirtualna Ubuntu jest wdrażana w podsieci skojarzonej z zasobem bramy translatora adresów sieciowych.

:::code language="json" source="~/quickstart-templates/101-nat-gateway-1-vm/azuredeploy.json":::

Dziewięć zasobów platformy Azure są zdefiniowane w szablonie:

* **[Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)** : tworzy sieciową grupę zabezpieczeń.
* **[Microsoft. Network/networkSecurityGroups/securityRules](/azure/templates/microsoft.network/networksecuritygroups/securityrules)** : tworzy regułę zabezpieczeń.
* **[Microsoft. Network/adresów publicipaddress](/azure/templates/microsoft.network/publicipaddresses)** : tworzy publiczny adres IP.
* **[Microsoft. Network/publicIPPrefixes](/azure/templates/microsoft.network/publicipprefixes)** : tworzy prefiks publicznego adresu IP.
* **[Microsoft. COMPUTE/virtualMachines](/azure/templates/Microsoft.Compute/virtualMachines)** : tworzy maszynę wirtualną.
* **[Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)** : tworzy sieć wirtualną.
* **[Microsoft. Network/natGateways](/azure/templates/microsoft.network/natgateways)** : tworzy zasób bramy NAT.
* **[Microsoft. Network/virtualNetworks/podsieci](/azure/templates/microsoft.network/virtualnetworks/subnets)** : tworzy podsieć sieci wirtualnej.
* **[Microsoft. Network/NetworkInterfaces](/azure/templates/microsoft.network/networkinterfaces)** : tworzy interfejs sieciowy.

## <a name="deploy-the-template"></a>Wdrażanie szablonu

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

**Witryna Azure Portal**

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-nat-gateway-1-vm%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupNAT**

1. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

    ![Grupa zasobów Virtual Network translator adresów sieciowych](./media/quick-create-template/nat-gateway-template-rg.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

**Interfejs wiersza polecenia platformy Azure**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive
  az group delete \
    --name myResourceGroupNAT
```

**Azure PowerShell**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupNAT
```

**Witryna Azure Portal**

Gdy grupa zasobów, Brama translatora adresów sieciowych i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupNAT** , która zawiera bramę translatora adresów sieciowych, a następnie wybierz pozycję **Usuń** .

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
