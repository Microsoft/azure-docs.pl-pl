---
title: 'Szablon ExpressRoute platformy Azure: Tworzenie obwodu usługi ExpressRoute'
description: Dowiedz się, jak utworzyć obwód usługi Azure ExpressRoute, wdrażając szablon Azure Resource Manager przy użyciu Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/13/2019
ms.author: duau
ms.openlocfilehash: 2e9b6ddc9da4467590946af12a47f1473a4ea494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92202059"
---
# <a name="create-an-expressroute-circuit-by-using-azure-resource-manager-template"></a>Tworzenie obwodu usługi ExpressRoute przy użyciu szablonu Azure Resource Manager

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-circuit-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-circuit-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-circuit-cli.md)
> * [Szablon usługi Azure Resource Manager](expressroute-howto-circuit-resource-manager-template.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-circuit-classic.md)
>

Dowiedz się, jak utworzyć obwód usługi ExpressRoute, wdrażając szablon Azure Resource Manager przy użyciu Azure PowerShell. Aby uzyskać więcej informacji na temat opracowywania szablonów Menedżer zasobów, zobacz [dokumentację Menedżer zasobów i dokumentacja](../azure-resource-manager/index.yml) [szablonu](/azure/templates/microsoft.network/expressroutecircuits).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Przed rozpoczęciem konfiguracji Przejrzyj [wymagania wstępne](expressroute-prerequisites.md) i [przepływy pracy](expressroute-workflows.md) .
* Upewnij się, że masz uprawnienia do tworzenia nowych zasobów sieciowych. Jeśli nie masz odpowiednich uprawnień, skontaktuj się z administratorem konta.
* [Film wideo można wyświetlić](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć te czynności.

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Tworzenie i Inicjowanie obsługi obwodu ExpressRoute

[Szablony szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/) mają dobrą kolekcję Menedżer zasobów szablonu. Użyj jednego z [istniejących szablonów](https://azure.microsoft.com/resources/templates/101-expressroute-circuit-create/) do utworzenia obwodu ExpressRoute.

[!code-json[create-azure-expressroute-circuit](~/quickstart-templates/101-expressroute-circuit-create/azuredeploy.json)]

Aby wyświetlić więcej powiązanych szablonów, wybierz [tutaj](https://azure.microsoft.com/resources/templates/?term=expressroute).

Aby utworzyć obwód ExpressRoute przez wdrożenie szablonu:

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do usługi Azure Cloud Shell.

    ```azurepowershell-interactive
    $circuitName = Read-Host -Prompt "Enter a circuit name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${circuitName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-expressroute-circuit-create/azuredeploy.json"

    $serviceProviderName = "Equinix"
    $peeringLocation = "Silicon Valley"
    $bandwidthInMbps = 500
    $sku_tier = "Premium"
    $sku_family = "MeteredData"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -circuitName $circuitName -serviceProviderName $serviceProviderName -peeringLocation $peeringLocation -bandwidthInMbps $bandwidthInMbps -sku_tier $sku_tier -sku_family $sku_family

    Write-Host "Press [ENTER] to continue ..."
    ```

   * **Warstwa SKU** określa, czy obwód ExpressRoute jest [lokalny](expressroute-faqs.md#expressroute-local), standardowy, czy [Premium](expressroute-faqs.md#expressroute-premium). Możesz określić *Local*, * Standard lub *Premium*. Nie można zmienić jednostki SKU z warstwy *standardowa/Premium* na *lokalną*.
   * **Rodzina SKU** określa typ rozliczeń. Możesz określić *Metereddata* dla mierzonego planu taryfowego i *Unlimiteddata* dla nieograniczonego planu taryfowego. Typ rozliczeń można zmienić z *Metereddata* na *Unlimiteddata*, ale nie można zmienić typu z *Unlimiteddata* na *Metereddata*. Obwód *lokalny* jest tylko *Unlimiteddata* .
   * **Lokalizacja komunikacji równorzędnej** to lokalizacja fizyczna, w której Komunikacja równorzędna jest przeprowadzana z firmą Microsoft.

     > [!IMPORTANT]
     > Lokalizacja komunikacji równorzędnej wskazuje [lokalizację fizyczną](expressroute-locations.md) , w której jest przeprowadzana Komunikacja równorzędna z firmą Microsoft. **Nie** jest to połączenie z właściwością "Location", która odnosi się do lokalizacji geograficznej, w której znajduje się dostawca zasobów sieciowych platformy Azure. Chociaż nie są one powiązane, dobrym sposobem jest wybranie dostawcy zasobów sieciowych geograficznie blisko lokalizacji komunikacji równorzędnej obwodu.

    Nazwa grupy zasobów to nazwa przestrzeni nazw usługi Service Bus z dołączoną **RG** .

2. Wybierz przycisk **Kopiuj**, aby skopiować skrypt programu PowerShell.
3. Kliknij prawym przyciskiem myszy konsolę powłoki, a następnie wybierz polecenie **Wklej**.

Utworzenie centrum zdarzeń trwa kilka chwil.

Azure PowerShell służy do wdrażania szablonu w tym samouczku. Aby poznać inne metody wdrażania szablonów, zobacz:

* Za [pomocą Azure Portal](../azure-resource-manager/templates/deploy-portal.md).
* Za [pomocą interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md).
* Za [pomocą interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a><a name="delete"></a>Anulowanie aprowizacji i usuwanie obwodu usługi ExpressRoute

Obwód ExpressRoute można usunąć, wybierając ikonę **Usuń** . Zanotuj następujące informacje:

* Musisz odłączyć wszystkie sieci wirtualne od obwodu usługi ExpressRoute. Jeśli ta operacja nie powiedzie się, sprawdź, czy wszystkie sieci wirtualne są połączone z obwodem.
* Jeśli stan aprowizacji dostawcy usługi obwodu ExpressRoute jest inicjowany lub **Zainicjowano obsługę administracyjną** , należy skontaktować się z dostawcą usług w celu **anulowania aprowizacji** obwodu po stronie. Nadal rezerwujemy zasoby i obciążamy Cię, dopóki dostawca usług nie ukończy anulowania aprowizacji obwodu i powiadamia nas.
* Jeśli dostawca usług anulował obsługę administracyjną obwodu (stan aprowizacji dostawcy usług jest ustawiony na **nieinicjowany**), można usunąć obwód. Spowoduje to zatrzymanie naliczania opłat za obwód.

Obwód ExpressRoute można usunąć, uruchamiając następujące polecenie programu PowerShell:

```azurepowershell-interactive
$circuitName = Read-Host -Prompt "Enter the same circuit name that you used earlier"
$resourceGroupName = "${circuitName}rg"

Remove-AzExpressRouteCircuit -ResourceGroupName $resourceGroupName -Name $circuitName
```

## <a name="next-steps"></a>Następne kroki

Po utworzeniu obwodu wykonaj następujące kolejne czynności:

* [Tworzenie i modyfikowanie routingu dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Łączenie sieci wirtualnej z obwodem ExpressRoute](expressroute-howto-linkvnet-arm.md)