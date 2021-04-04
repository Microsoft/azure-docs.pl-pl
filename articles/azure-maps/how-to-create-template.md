---
title: Utwórz konto usługi Azure Maps przy użyciu szablonu usługi ARM | Mapy Microsoft Azure
description: Dowiedz się, jak utworzyć konto Azure Maps przy użyciu szablonu Azure Resource Manager (ARM).
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92525185"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Tworzenie konta Azure Maps przy użyciu szablonu ARM

Konto Azure Maps można utworzyć przy użyciu szablonu Azure Resource Manager (ARM). Po utworzeniu konta można zaimplementować interfejsy API w witrynie sieci Web lub aplikacji mobilnej.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten artykuł:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

Zasób konta Azure Maps jest zdefiniowany w tym szablonie:

* [**Microsoft. Maps/konta**](/azure/templates/microsoft.maps/accounts): utwórz konto Azure Maps.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

1. Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy konto Azure Maps.

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Wybierz lub wprowadź następujące wartości.

    ![Portal wdrażania szablonów ARM](./media/how-to-create-template/create-account-using-template-portal.png)

    O ile nie zostanie on określony, użyj wartości domyślnej, aby utworzyć konto Azure Maps.

    * **Subskrypcja**: wybierz subskrypcję platformy Azure.
    * **Grupa zasobów**: wybierz pozycję **Utwórz nową**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**.
    * **Lokalizacja**: wybierz lokalizację. Na przykład **zachodnie stany USA 2**.
    * **Nazwa konta**: Wprowadź nazwę konta Azure Maps, która musi być globalnie unikatowa.
    * **Warstwa cenowa**: wybierz odpowiednią warstwę cenową, a wartość domyślna dla szablonu to S0.

3. Wybierz pozycję **Przejrzyj i utwórz**. 
4. Potwierdź ustawienia na stronie Recenzja, a następnie kliknij przycisk **Utwórz**. Po pomyślnym wdrożeniu Azure Maps otrzymujesz powiadomienie:

    ![Powiadomienie portalu wdrażania szablonu ARM](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal służy do wdrażania szablonu. Można również użyć Azure PowerShell, interfejsu wiersza polecenia platformy Azure i API REST. Aby poznać inne metody wdrażania, zobacz [wdrażanie szablonów](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Możesz użyć Azure Portal, aby sprawdzić konto Azure Maps i wyświetlić klucze. Możesz również użyć następującego skryptu interfejsu wiersza polecenia platformy Azure, aby wyświetlić listę kluczy konta.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów nie jest już konieczna, usuń ją, co spowoduje również usunięcie konta Azure Maps. Aby usunąć grupę zasobów przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Azure Maps i Azure Resource Manager, przejdź do artykułu poniżej.

- Tworzenie [aplikacji demonstracyjnej](quick-demo-map-app.md) Azure Maps
- Dowiedz się więcej o [szablonach ARM](../azure-resource-manager/templates/overview.md)