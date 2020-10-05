---
title: 'Szybki Start: Tworzenie usługi Azure front-drzwi przy użyciu szablonu Azure Resource Manager (szablon ARM)'
description: W tym przewodniku szybki start opisano sposób tworzenia usługi Azure front-drzwi przy użyciu szablonu Azure Resource Manager (szablon ARM).
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: 41ad240b7232895a83839777f9942011edfaf5f4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90529391"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Szybki Start: Tworzenie drzwi z przodu przy użyciu szablonu ARM

W tym przewodniku szybki start opisano, jak używać szablonu Azure Resource Manager (szablon ARM) do tworzenia czołowych drzwi w celu skonfigurowania wysokiej dostępności dla punktu końcowego sieci Web.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Adres IP lub nazwa FQDN witryny internetowej lub aplikacji sieci Web.

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

W tym przewodniku szybki start utworzysz konfigurację dla drzwi z pojedynczym zapleczem i jedną domyślną ścieżkę pasującą do "/*". 

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

* [**Microsoft. Network/usługi frontdoor**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz opcję **Wypróbuj** z następującego bloku kodu, aby otworzyć Azure Cloud Shell, a następnie postępuj zgodnie z instrukcjami, aby zalogować się do platformy Azure. 

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Poczekaj, aż zobaczysz monit z konsoli programu.

1. Wybierz pozycję **Kopiuj** z poprzedniego bloku kodu, aby skopiować skrypt programu PowerShell.

1. Kliknij prawym przyciskiem myszy okienko konsoli powłoki, a następnie wybierz polecenie **Wklej**.

1. Wprowadź wartości.

    Wdrożenie szablonu tworzy drzwi tylne z pojedynczym zapleczem. W tym przykładzie * <span>firma Microsoft.</span> Model com* jest używany jako **backendAddress**.

    Nazwa grupy zasobów jest nazwą projektu z dołączoną **RG** .

    > [!NOTE]
    > **frontDoorName** musi być globalnie unikatową nazwą, aby szablon został pomyślnie wdrożony. Jeśli wdrożenie nie powiedzie się, Zacznij od początku, korzystając z kroku 1.

    Wdrożenie szablonu trwa kilka minut. Po zakończeniu dane wyjściowe są podobne do:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="Dane wyjściowe wdrożenia programu PowerShell dla drzwi przednich Menedżer zasobów szablonu":::

Azure PowerShell służy do wdrożenia szablonu. Oprócz Azure PowerShell można również użyć Azure Portal, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Weryfikowanie wdrożenia

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

1. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślną nazwą grupy zasobów jest nazwa projektu z dołączoną **RG** .

1. Wybierz utworzone wcześniej drzwi i kliknij link **hosta frontonu** . Link spowoduje otwarcie przeglądarki sieci Web przekierowanie użytkownika do nazwy FQDN zaplecza zdefiniowanej podczas tworzenia.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="Dane wyjściowe wdrożenia programu PowerShell dla drzwi przednich Menedżer zasobów szablonu":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już usługi front-drzwi, Usuń grupę zasobów. Spowoduje to usunięcie drzwi z przodu i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:
* Front Door

Aby dowiedzieć się, jak dodać domenę niestandardową do swoich drzwi, przejdź do samouczków dotyczących drzwi.

> [!div class="nextstepaction"]
> [Samouczki dotyczące drzwi](front-door-custom-domain.md)
