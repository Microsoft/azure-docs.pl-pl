---
title: 'Szybki Start: tworzenie sieci wirtualnej przy użyciu szablonu Menedżer zasobów'
titleSuffix: Azure Virtual Network
description: Dowiedz się, jak utworzyć sieć wirtualną platformy Azure przy użyciu szablonu Menedżer zasobów.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: c9e9ea2e59537a1c0c8e372a766fba3aa9a1b9a0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88122184"
---
# <a name="quickstart-create-a-virtual-network---resource-manager-template"></a>Szybki Start: Tworzenie szablonu Menedżer zasobów sieci wirtualnej

W tym przewodniku szybki start dowiesz się, jak utworzyć sieć wirtualną z dwiema podsieciami przy użyciu szablonu Azure Resource Manager. Sieć wirtualna jest podstawowym blokiem konstrukcyjnym sieci prywatnej na platformie Azure. Pozwala ona na bezpieczne komunikowanie się ze sobą i z Internetem za pomocą zasobów platformy Azure, takich jak maszyny wirtualne.


[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ten przewodnik Szybki Start można także wykonać przy użyciu [Azure Portal](quick-create-portal.md), [Azure PowerShell](quick-create-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md).

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-vnet-two-subnets/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-vnet-two-subnets/azuredeploy.json" range="001-96" highlight="56-92":::

Następujące zasoby platformy Azure zostały zdefiniowane w szablonie:
- [**Microsoft. Network/virtualNetworks**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks): tworzenie sieci wirtualnej platformy Azure.
-  [**Microsoft. Network/virtualNetworks/podsieci**](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks/subnets) — Utwórz podsieć.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon Menedżer zasobów na platformie Azure:

1. Wybierz pozycję **Wdróż na platformie Azure** , aby zalogować się do platformy Azure i otworzyć szablon. Szablon tworzy sieć wirtualną z dwiema podsieciami.

   [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-virtual-network-2vms-create%2Fazuredeploy.json)

2. W portalu na stronie **tworzenie Virtual Network z dwiema podsieciami** wpisz lub wybierz następujące wartości:
   - **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wpisz nazwę grupy zasobów, a następnie wybierz **przycisk OK**.
   - **Nazwa Virtual Network**: wpisz nazwę nowej sieci wirtualnej.
3. Wybierz pozycję **Przejrzyj i utwórz**, a następnie wybierz pozycję **Utwórz**.

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Eksploruj zasoby, które zostały utworzone za pomocą sieci wirtualnej.

Aby dowiedzieć się więcej o składni i właściwościach JSON dla sieci wirtualnej w szablonie, zobacz [Microsoft. Network/virtualNetworks](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne zasoby utworzone w sieci wirtualnej, Usuń grupę zasobów. Spowoduje to usunięcie sieci wirtualnej i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki
W tym przewodniku szybki start wdrożono sieć wirtualną platformy Azure z dwiema podsieciami. Aby dowiedzieć się więcej na temat sieci wirtualnych platformy Azure, przejdź do samouczka dotyczącego sieci wirtualnych.

> [!div class="nextstepaction"]
> [Filtrowanie ruchu sieciowego](tutorial-filter-network-traffic.md)
