---
title: 'Szybki Start: Tworzenie obwodu usługi ExpressRoute przy użyciu szablonu Azure Resource Manager (szablon ARM)'
description: W tym przewodniku szybki start przedstawiono sposób tworzenia obwodu usługi ExpressRoute przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: expressroute
author: duongau
mnager: kumud
ms.service: expressroute
ms.topic: quickstart
ms.custom: subject-armsq
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 540d9d5f7ef3815743909a399076c55018f0df40
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961855"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Szybki Start: Tworzenie obwodu usługi ExpressRoute z prywatną usługą komunikacji równorzędnej przy użyciu szablonu usługi ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia obwodu ExpressRoute za pomocą prywatnej komunikacji równorzędnej.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

W tym przewodniku szybki start utworzysz obwód usługi ExpressRoute z *Equinix* jako usługodawcą. Obwód będzie używać *jednostki SKU w warstwie Premium*z przepustowością *50 MB/s*i lokalizacją komunikacji równorzędnej w *Waszyngton*. Prywatna Komunikacja równorzędna zostanie włączona z podstawową i pomocniczą podsiecią odpowiednio *192.168.10.16/30* i *192.168.10.20/30* . Sieć wirtualna zostanie również utworzona wraz z *bramą ExpressRoute HighPerformance*.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json" range="001-351" highlight="183-219":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [**Microsoft. Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft. Network/expressRouteCircuits/Komunikacja równorzędna**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (używana do włączenia prywatnej komunikacji równorzędnej w ramach obwodu)
* [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (sieciowa Grupa zabezpieczeń jest stosowana do podsieci w sieci wirtualnej)
* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks) 
* [**Microsoft. Network/adresów publicipaddress**](/azure/templates/microsoft.network/publicIPAddresses) (publiczny adres IP jest używany przez bramę ExpressRoute)
* [**Microsoft. Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (Brama ExpressRoute służy do łączenia sieci wirtualnej z obwodem)

Aby znaleźć więcej szablonów związanych z usługą ExpressRoute, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrażanie szablonu

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Poczekaj, aż zobaczysz monit z konsoli programu.

1. Wybierz pozycję **Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

    Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** .

    Wdrożenie szablonu trwa około 20 minut. Po zakończeniu dane wyjściowe są podobne do:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="ExpressRoute Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell szablonu":::

Azure PowerShell służy do wdrożenia szablonu. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Grupa zasobów powinna zawierać następujące zasoby:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="ExpressRoute Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell szablonu":::

1. Wybierz obwód ExpressRoute **er-ck01** , aby sprawdzić, czy stan obwodu **jest włączony**, stan dostawcy **nie jest zainicjowany** , a prywatna Komunikacja równorzędna ma stan **aprowizacji**.

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="ExpressRoute Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell szablonu":::

> [!NOTE]
> Aby można było połączyć sieć wirtualną z obwodem, należy skontaktować się z dostawcą w celu ukończenia procesu aprowizacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych za pomocą obwodu ExpressRoute, Usuń grupę zasobów. Spowoduje to usunięcie obwodu ExpressRoute i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:
* Obwód usługi ExpressRoute
* Virtual Network
* VPN Gateway
* Publiczny adres IP
* sieciowe grupy zabezpieczeń

Aby dowiedzieć się, jak połączyć sieć wirtualną z obwodem, przejdź do samouczków ExpressRoute.

> [!div class="nextstepaction"]
> [Samouczki ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
