---
title: 'Szybki start: tworzenie obwodu usługi ExpressRoute przy użyciu szablonu Azure Resource Manager (szablonu usługi ARM)'
description: W tym przewodniku Szybki start pokazano, jak utworzyć obwód usługi ExpressRoute przy użyciu Azure Resource Manager szablonu (szablonu USŁUGI ARM).
services: expressroute
author: duongau
ms.author: duau
manager: kumud
ms.date: 10/12/2020
ms.topic: quickstart
ms.service: expressroute
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 3dc1d5f5ec3dfb004468deb2bec80927c7ec189d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529893"
---
# <a name="quickstart-create-an-expressroute-circuit-with-private-peering-using-an-arm-template"></a>Szybki start: tworzenie obwodu usługi ExpressRoute z prywatną komunikacja równorzędną przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak utworzyć obwód usługi ExpressRoute z prywatną Azure Resource Manager równorzędną za pomocą szablonu usługi Azure Resource Manager (ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-expressroute-private-peering-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-expressroute-private-peering-vnet).

W tym przewodniku Szybki start utworzysz obwód usługi ExpressRoute z usługą *Equinix* jako dostawcą usług. Obwód będzie używać wersji *Premium SKU* o przepustowości *50 Mb/s* i lokalizacji komunikacji równorzędnej *Waszyngton.* Prywatna komunikacja równorzędna zostanie włączona z podstawową i pomocniczą podsiecią *odpowiednio 192.168.10.16/30* i *192.168.10.20/30.* Zostanie również utworzona sieć wirtualna wraz z bramą *usługi ExpressRoute o* wysokiej wydajności.

:::code language="json" source="~/quickstart-templates/101-expressroute-private-peering-vnet/azuredeploy.json":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [**Microsoft.Network/expressRouteCircuits**](/azure/templates/microsoft.network/expressRouteCircuits)
* [**Microsoft.Network/expressRouteCircuits/peerings**](/azure/templates/microsoft.network/expressRouteCircuits/peerings) (Służy do obsługi prywatnej komunikacji równorzędnej w obwodzie)
* [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networkSecurityGroups) (sieciowa grupa zabezpieczeń jest stosowana do podsieci w sieci wirtualnej)
* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicIPAddresses) (publiczny adres IP jest używany przez bramę usługi ExpressRoute)
* [**Microsoft.Network/virtualNetworkGateways**](/azure/templates/microsoft.network/virtualNetworkGateways) (brama usługi ExpressRoute służy do łączenia sieci wirtualnej z obwodem)

Aby znaleźć więcej szablonów związanych z usługą ExpressRoute, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz **pozycję Wypróbuj w** poniższym bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-private-peering-vnet/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Zaczekaj, aż zostanie wyświetlony monit z konsoli.

1. Wybierz **pozycję Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz pozycję **Wklej**.

1. Wprowadź wartości.

    Nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

    Wdrożenie szablonu trwa około 20 minut. Po zakończeniu dane wyjściowe będą podobne do:

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-powershell-output.png" alt-text="Dane wyjściowe Resource Manager wdrożenia programu PowerShell w szablonie usługi ExpressRoute":::

Azure PowerShell służy do wdrażania szablonu. Oprócz interfejsu Azure PowerShell można również używać interfejsu wiersza polecenia platformy Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Deploy templates (Wdrażanie szablonów).](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Grupy zasobów** w okienku po lewej stronie.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

1. Grupa zasobów powinna zawierać następujące zasoby widoczne tutaj:

     :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-resource-group.png" alt-text="Grupa zasobów wdrożenia usługi ExpressRoute":::

1. Wybierz obwód usługi ExpressRoute **er-ck01,** aby sprawdzić, czy  stan obwodu to **Włączone,** stan dostawcy to Nie aprowizowana, a prywatna komunikacja równorzędna ma stan **Aprowizowana.**

    :::image type="content" source="./media/quickstart-create-expressroute-vnet/expressroute-circuit.png" alt-text="Obwód wdrażania usługi ExpressRoute":::

> [!NOTE]
> Aby można było połączyć sieć wirtualną z obwodem, należy wywołać dostawcę w celu ukończenia procesu aprowacji.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą obwodu expressRoute, usuń grupę zasobów. Spowoduje to usunięcie obwodu usługi ExpressRoute i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:

* Obwód usługi ExpressRoute
* Virtual Network
* VPN Gateway
* Publiczny adres IP
* sieciowe grupy zabezpieczeń

Aby dowiedzieć się, jak połączyć sieć wirtualną z obwodem, przejdź do samouczków usługi ExpressRoute.

> [!div class="nextstepaction"]
> [Samouczki dotyczące expressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
