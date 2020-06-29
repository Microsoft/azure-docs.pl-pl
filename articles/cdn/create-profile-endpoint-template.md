---
title: 'Szybki Start: Tworzenie profilu i szablonu Menedżer zasobów punktu końcowego'
titleSuffix: Azure Content Delivery Network
description: Dowiedz się, jak utworzyć profil usługi Azure Content Delivery Network i punkt końcowy szablonu Menedżer zasobów
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/25/2020
ms.author: allensu
ms.openlocfilehash: bdb30fed4dadef84fe012c11893661b8d9d70325
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483044"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint---resource-manager-template"></a>Szybki Start: Tworzenie profilu Azure CDN i szablonu punktu końcowego Menedżer zasobów

Rozpocznij pracę z Azure CDN przy użyciu szablonu Azure Resource Manager.  Ten szablon służy do wdrażania profilu i punktu końcowego.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-cdn-profile-and-endpoint"></a>Tworzenie profilu i punktu końcowego usługi CDN

Ten szablon jest skonfigurowany do tworzenia:

* Profil
* Endpoint

### <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów szybkiego startu platformy Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cdn-with-custom-origin/azuredeploy.json" range="1-125" highlight="46-117":::


Jeden zasób platformy Azure jest zdefiniowany w szablonie:

**Microsoft. CDN**

* **[Microsoft. CDN/profile](https://docs.microsoft.com/azure/templates/microsoft.cdn/profiles)**

### <a name="deploy-the-template"></a>Wdrożenie szablonu

**Interfejs wiersza polecenia platformy Azure**

```azurecli-interactive
read -p "Enter the location (i.e. eastus): " location
resourceGroupName="myResourceGroupCDN"
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json" 

az group create \
--name $resourceGroupName \
--location $location

az group deployment create \
--resource-group $resourceGroupName \
--template-uri  $templateUri
```

**Azure PowerShell**

```azurepowershell-interactive
$location = Read-Host -Prompt "Enter the location (i.e. eastus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cdn-with-custom-origin/azuredeploy.json"

$resourceGroupName = "myResourceGroupCDN"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri
```

**Azure Portal**

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cdn-with-custom-origin%2Fazuredeploy.json)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. W okienku po lewej stronie wybierz pozycję **grupy zasobów** .

3. Wybierz grupę zasobów utworzoną w poprzedniej sekcji. Domyślna nazwa grupy zasobów to **myResourceGroupCDN**

4. Sprawdź, czy w grupie zasobów zostały utworzone następujące zasoby:

    :::image type="content" source="media/create-profile-endpoint-template/cdn-profile-template-rg.png" alt-text="Grupa zasobów Azure CDN" border="true":::

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

**Interfejs wiersza polecenia platformy Azure**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group#az-group-delete) .

```azurecli-interactive 
  az group delete \
    --name myResourceGroupCDN
```

**Azure PowerShell**

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=latest) .

```azurepowershell-interactive 
Remove-AzResourceGroup -Name myResourceGroupCDN
```

**Azure Portal**

Gdy grupa zasobów, profil usługi CDN i wszystkie powiązane zasoby nie będą już potrzebne, usuń je. Wybierz grupę zasobów **myResourceGroupCDN** , która zawiera profil i punkt końcowy usługi CDN, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono:

* Profil CDN
* Endpoint

Aby dowiedzieć się więcej na temat Azure CDN i Azure Resource Manager, przejdź do artykułów poniżej.

* Zapoznaj się [z omówieniem Azure CDN](cdn-overview.md)
* Dowiedz się więcej o usłudze [Azure Resource Manager](../azure-resource-manager/management/overview.md)