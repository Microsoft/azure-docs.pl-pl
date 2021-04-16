---
title: 'Szybki start: tworzenie Azure Front Door Service przy użyciu szablonu Azure Resource Manager (szablonu USŁUGI ARM)'
description: W tym przewodniku Szybki start opisano, jak utworzyć Azure Front Door Service przy użyciu Azure Resource Manager szablonu usługi ARM.
services: front-door
documentationcenter: ''
author: duongau
ms.author: duau
editor: ''
ms.date: 09/14/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: de8a592f6eecbb43b58a044096e8ba2e0f9b5973
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538993"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Szybki start: tworzenie aplikacji Front Door użyciu szablonu usługi ARM

W tym przewodniku Szybki start opisano, jak używać szablonu usługi Azure Resource Manager (arm) w celu utworzenia Front Door w celu skonfigurowania wysokiej dostępności dla internetowego punktu końcowego.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Adres IP lub WQDN witryny internetowej lub aplikacji internetowej.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

W tym przewodniku Szybki start utworzysz konfigurację usługi Front Door z pojedynczymi zapleczami i jedną ścieżką domyślną pasującą do . `/*`

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz **pozycję Wypróbuj w** poniższym bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Zaczekaj, aż zostanie wyświetlony monit z konsoli.

1. Wybierz **pozycję Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz pozycję **Wklej**.

1. Wprowadź wartości.

    Wdrożenie szablonu tworzy Front Door z jednym zaplecza. W tym `microsoft.com` przykładzie jest używany jako **backendAddress**.

    Nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

    > [!NOTE]
    > **FrontDoorName** musi być globalnie unikatową nazwą, aby szablon został pomyślnie wdrożony. Jeśli wdrożenie zakończy się niepowodzeniem, zacznij od początku od kroku 1.

    Wdrożenie szablonu może potrwać kilka minut. Po zakończeniu dane wyjściowe będą podobne do:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Front Door Resource Manager danych wyjściowych wdrożenia programu PowerShell dla szablonu":::

Azure PowerShell służy do wdrażania szablonu. Oprócz interfejsu Azure PowerShell można również używać interfejsu wiersza polecenia platformy Azure Portal, interfejsu wiersza polecenia platformy Azure i interfejsu API REST. Aby poznać inne metody wdrażania, zobacz [Deploy templates (Wdrażanie szablonów).](../azure-resource-manager/templates/deploy-portal.md)

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Grupy zasobów** w okienku po lewej stronie.

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to nazwa projektu z dołączonym **rg.**

1. Wybierz wcześniej Front Door i kliknij link **Host frontonie.** Link spowoduje otwarcie przeglądarki internetowej przekierowuje Cię do twojej domeny FQDN zaplecza zdefiniowanej podczas tworzenia.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Front Door omówienie portalu":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już usługi Front Door, usuń grupę zasobów. Spowoduje to usunięcie Front Door i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono Front Door.

Aby dowiedzieć się, jak dodać domenę niestandardową do Front Door, przejdź do Front Door samouczków.

> [!div class="nextstepaction"]
> [Front Door samouczków](front-door-custom-domain.md)
