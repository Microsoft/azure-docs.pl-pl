---
title: 'Szybki Start: Tworzenie serwera usługi Azure Route przy użyciu szablonu Azure Resource Manager (szablon ARM)'
description: W tym przewodniku szybki start pokazano, jak utworzyć serwer usługi Azure Route przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 6f56b9fb1f6a1f5a1fe0811617fb20412c52fd72
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106452203"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Szybki Start: Tworzenie serwera usługi Azure Route przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do wdrażania serwera usługi Azure Route w nowej lub istniejącej sieci wirtualnej.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-route-server).

W tym przewodniku szybki start wdrożono serwer usługi Azure Route w nowej lub istniejącej sieci wirtualnej. `RouteServerSubnet`Do hostowania serwera tras zostanie utworzona dedykowana podsieć o nazwie. Serwer tras zostanie również skonfigurowany z równorzędnym numerem ASN i równorzędnym adresem IP w celu ustanowienia komunikacji równorzędnej BGP.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft. Network/virtualNetworks/podsieci**](/azure/templates/microsoft.network/virtualNetworks/subnets) (dwie podsieci, jedna nazwa `routeserversubnet` )
* [**Microsoft. Network/virtualHubs**](/azure.templates/microsoft.network/virtualhubs) (wdrożenie serwera tras)
* [**Microsoft. Network/virtualHubs/Ipconfiguration**](/azure.templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft. Network/virtualHubs/bgpConnections**](/azure.templates/microsoft.network/virtualhubs/bgpConnections) (Konfiguracja elementów równorzędnych ASN i peer IP)


Aby znaleźć więcej szablonów związanych z usługą ExpressRoute, zobacz [Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

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

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Serwer tras Menedżer zasobów dane wyjściowe wdrożenia programu PowerShell.":::

Azure PowerShell służy do wdrożenia szablonu. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Grupa zasobów powinna zawierać tylko sieć wirtualną:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Grupa zasobów wdrażania serwera tras z siecią wirtualną.":::

1. Przejdź do witryny https://aka.ms/routeserver.

1. Wybierz serwer tras o nazwie **routeserver** , aby sprawdzić, czy wdrożenie zakończyło się pomyślnie.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd serwera tras.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu serwera tras, Usuń grupę zasobów. Spowoduje to usunięcie serwera tras i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:

* Serwer tras
* Virtual Network
* Podsieć

Po utworzeniu serwera usługi Azure Route należy dalej dowiedzieć się, jak usługa Azure Route Server współdziała z ExpressRoute i bramami sieci VPN: 

> [!div class="nextstepaction"]
> [Azure ExpressRoute i obsługa sieci VPN platformy Azure](expressroute-vpn-support.md)
