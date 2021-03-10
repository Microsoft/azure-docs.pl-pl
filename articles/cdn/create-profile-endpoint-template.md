---
title: 'Szybki Start: Tworzenie profilu i szablonu Menedżer zasobów punktu końcowego'
titleSuffix: Azure Content Delivery Network
description: W tym przewodniku szybki start dowiesz się, jak utworzyć profil usługi Azure Content Delivery Network i punkt końcowy szablonu Menedżer zasobów
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: a36484bea3a2bc9caadc5cf4482aae3bea84eafa
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560467"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---arm-template"></a>Szybki Start: Tworzenie profilu Azure CDN i szablonu punktu końcowego

Rozpocznij pracę z usługą Azure Content Delivery Network (CDN) przy użyciu szablonu Azure Resource Manager (szablon ARM). Szablon wdraża profil i punkt końcowy.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cdn-with-custom-origin/).

Ten szablon jest skonfigurowany do tworzenia:

* Profil
* Punkt końcowy

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:

* **[Microsoft. CDN/profile](/azure/templates/microsoft.cdn/profiles)**

## <a name="deploy-the-template"></a>Wdrożenie szablonu

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az deployment group create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

### <a name="portal"></a>Portal

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

3. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupCDN**

4. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Grupa zasobów Azure CDN" border="true":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

### <a name="powershell"></a>PowerShell

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

### <a name="portal"></a>Portal

Gdy grupa zasobów, profil usługi CDN i wszystkie powiązane zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupCDN** , która zawiera profil i punkt końcowy usługi CDN, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:

* Profil CDN
* Punkt końcowy

Aby dowiedzieć się więcej na temat Azure CDN i Azure Resource Manager, przejdź do artykułów poniżej.

> [!div class="nextstepaction"]
> [Samouczek: używanie usługi CDN do obsługi zawartości statycznej z poziomu aplikacji sieci Web](cdn-add-to-web-app.md)
