---
title: 'Szybki start: tworzenie aplikacji Traffic Manager użyciu Azure Resource Manager szablonu (szablonu usługi ARM)'
description: W tym artykule Szybki start opisano sposób tworzenia profilu Azure Traffic Manager przy użyciu Azure Resource Manager szablonu (szablonu usługi ARM).
services: traffic-manager
author: duongau
ms.author: duau
ms.date: 09/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 03342d33731ed29b60908044cd29d529aaa5677b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531256"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Szybki start: tworzenie profilu Traffic Manager przy użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak za pomocą szablonu usługi Azure Resource Manager (arm) utworzyć profil usługi Traffic Manager z zewnętrznymi punktami końcowymi przy użyciu metody routingu wydajności.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Aby znaleźć więcej szablonów powiązanych z Azure Traffic Manager, zobacz [Szablony szybkiego startu platformy Azure.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz **pozycję Wypróbuj w** poniższym bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Zaczekaj, aż zostanie wyświetlony monit z konsoli.

1. Wybierz **pozycję Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz pozycję **Wklej**.

1. Wprowadź wartości.

    Wdrożenie szablonu tworzy profil z dwoma zewnętrznymi punktami końcowymi. **Punkt końcowy Endpoint1** używa docelowego punktu `www.microsoft.com` końcowego z lokalizacją w Europie **Północnej.** **Punkt końcowy 2** używa docelowego punktu `docs.microsoft.com` końcowego usługi z lokalizacją w **południowo-środkowych usa.**

    Nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

    > [!NOTE]
    > **UniqueDNSname** musi być globalnie unikatową nazwą, aby szablon został pomyślnie wdrożony. Jeśli wdrożenie zakończy się niepowodzeniem, zacznij od początku od kroku 1.

    Wdrożenie szablonu może potrwać kilka minut. Po zakończeniu dane wyjściowe będą podobne do:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager Resource Manager wdrożenia szablonu programu PowerShell":::

Azure PowerShell służy do wdrażania szablonu. Oprócz interfejsu Azure PowerShell można również używać interfejsu wiersza polecenia platformy Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Deploy templates (Wdrażanie szablonów).](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Określ nazwę DNS profilu Traffic Manager za pomocą [get-AzTrafficManagerProfile.](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Skopiuj **wartość RelativeDnsName.** Nazwa DNS profilu Traffic Manager to `<relativednsname>.trafficmanager.net` .

1. Z lokalnego programu PowerShell uruchom następujące polecenie, zastępując zmienną **{relativeDNSname}** zmienną `<relativednsname>.trafficmanager.net` .

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    W zależności od regionu, który znajduje się bliżej Ciebie, należy uzyskać host NameHost o nazwie lub `www.microsoft.com` `docs.microsoft.com` .

1. Aby sprawdzić, czy można rozpoznać inny punkt końcowy, wyłącz punkt końcowy dla obiektu docelowego, który został w ostatnim kroku. Zastąp element **{endpointName}** elementem **endpoint1** lub **endpoint2,** aby wyłączyć element docelowy `www.microsoft.com` odpowiednio dla elementu lub `docs.microsoft.com` .

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Ponownie uruchom polecenie z kroku 2 w lokalnym programie PowerShell. Tym razem należy pobrać drugi host NameHost dla drugiego punktu końcowego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już profilu Traffic Manager, usuń grupę zasobów. Spowoduje to usunięcie Traffic Manager i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono profil usługi Traffic Manager.

Aby dowiedzieć się więcej o kierowaniu ruchu, przejdź do samouczków usługi Traffic Manager.

> [!div class="nextstepaction"]
> [Samouczki usługi Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
