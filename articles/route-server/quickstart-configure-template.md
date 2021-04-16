---
title: 'Szybki start: tworzenie serwera usługi Azure Route Server przy użyciu szablonu usługi Azure Resource Manager (szablon usługi ARM)'
description: W tym przewodniku Szybki start pokazano, jak utworzyć serwer usługi Azure Route Server przy użyciu Azure Resource Manager (szablonu usługi ARM).
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/05/2021
ms.author: duau
ms.openlocfilehash: 3476e5fa2c274f0fc2c180711480375b0ebefaf2
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388044"
---
# <a name="quickstart-create-an-azure-route-server-using-an-arm-template"></a>Szybki start: tworzenie serwera usługi Azure Route Server przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano sposób wdrażania serwera usługi Azure Route Server w nowej lub istniejącej sieci wirtualnej Azure Resource Manager szablonu usługi Arm.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-route-server%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-route-server).

W tym przewodniku Szybki start wdrożysz serwer usługi Azure Route Server w nowej lub istniejącej sieci wirtualnej. Zostanie utworzona dedykowana `RouteServerSubnet` podsieć o nazwie , która będzie hostować serwer tras. Serwer tras zostanie również skonfigurowany przy użyciu równorzędnego adresu ASN i adresu IP elementu równorzędnego w celu ustanowienia komunikacji równorzędnej BGP.

:::code language="json" source="~/quickstart-templates/101-route-server/azuredeploy.json" range="001-145" highlight="105-142":::

W szablonie zdefiniowano wiele zasobów platformy Azure:

* [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualNetworks)
* [**Microsoft.Network/virtualNetworks/subnets**](/azure/templates/microsoft.network/virtualNetworks/subnets) (dwie podsieci, jedna o nazwie `routeserversubnet` )
* [**Microsoft.Network/virtualHubs**](/azure/templates/microsoft.network/virtualhubs) (wdrożenie serwera tras)
* [**Microsoft.Network/virtualHubs/ipConfigurations**](/azure/templates/microsoft.network/virtualhubs/ipConfigurations)
* [**Microsoft.Network/virtualHubs/bgpConnections**](/azure/templates/microsoft.network/virtualhubs/bgpconnections) (Konfiguracja równorzędnego adresu ASN i adresu IP elementu równorzędnego)


Aby znaleźć więcej szablonów związanych z usługą ExpressRoute, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz **pozycję Wypróbuj z** poniższego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-route-server/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Zaczekaj, aż zostanie wyświetlony monit z konsoli programu .

1. Wybierz **pozycję Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz pozycję **Wklej**.

1. Wprowadź wartości.

    Nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

    Wdrożenie szablonu trwa około 20 minut. Po zakończeniu dane wyjściowe będą podobne do:

    :::image type="content" source="./media/quickstart-configure-template/powershell-output.png" alt-text="Serwer tras Resource Manager danych wyjściowych wdrożenia programu PowerShell.":::

Azure PowerShell służy do wdrażania szablonu. Oprócz interfejsu Azure PowerShell można również używać interfejsu wiersza polecenia platformy Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Deploy templates (Wdrażanie szablonów).](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Grupy zasobów** w okienku po lewej stronie.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

1. Grupa zasobów powinna zawierać tylko sieć wirtualną:

     :::image type="content" source="./media/quickstart-configure-template/resource-group.png" alt-text="Przekieruj grupę zasobów wdrożenia serwera z siecią wirtualną.":::

1. Przejdź do witryny https://aka.ms/routeserver.

1. Wybierz serwer tras o nazwie **routeserver,** aby sprawdzić, czy wdrożenie powiodło się.

    :::image type="content" source="./media/quickstart-configure-template/deployment.png" alt-text="Zrzut ekranu przedstawiający stronę przeglądu serwera tras.":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą serwera tras, usuń grupę zasobów. Spowoduje to usunięcie serwera tras i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono:

* Serwer tras
* Virtual Network
* Podsieć

Po utworzeniu serwera usługi Azure Route Server dowiedz się więcej o tym, jak usługa Azure Route Server współdziała z usługą ExpressRoute i bramami sieci VPN: 

> [!div class="nextstepaction"]
> [obsługa Azure ExpressRoute sieci VPN na platformie Azure](expressroute-vpn-support.md)
