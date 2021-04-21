---
title: Wdrażanie prywatnego punktu końcowego dla aplikacji internetowej przy użyciu szablonu usługi Azure Resource Manager aplikacji internetowej
description: Dowiedz się, jak używać szablonu usługi ARM do wdrażania prywatnego punktu końcowego dla aplikacji internetowej.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: df71c1a92840ae0e7fa263e2ababcf5b3e059789
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832540"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Tworzenie aplikacji App Service i wdrażanie prywatnego punktu końcowego przy użyciu szablonu Azure Resource Manager końcowego

W tym przewodniku Szybki start użyjemy szablonu usługi Azure Resource Manager (ARM), aby utworzyć aplikację internetową i uwidocznić ją za pomocą prywatnego punktu końcowego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Wymaganie wstępne

Potrzebujesz konta platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Ten szablon tworzy prywatny punkt końcowy dla aplikacji internetowej platformy Azure.

### <a name="review-the-template"></a>Przegląd szablonu

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Oto jak wdrożyć szablon Azure Resource Manager na platformie Azure:

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz ten link: Deploy to Azure (Wdrażanie [na platformie Azure).](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json) Szablon tworzy sieć wirtualną, aplikację internetową, prywatny punkt końcowy i prywatną strefę DNS.
2. Wybierz lub utwórz grupę zasobów.
3. Wprowadź nazwę aplikacji internetowej, plan Azure App Service i prywatny punkt końcowy.
5. Przeczytaj oświadczenie dotyczące warunków i postanowień. Jeśli się zgadzasz, wybierz pozycję **Wyrażam zgodę na warunki i postanowienia określone powyżej**  >  **zakup.** Wdrożenie może potrwać kilka minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych za pomocą prywatnego punktu końcowego, usuń grupę zasobów. Spowoduje to usunięcie prywatnego punktu końcowego i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet :

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

- Więcej szablonów szablonów Azure Resource Manager dla Azure App Service internetowych można znaleźć w przykładach [szablonów usługi ARM.](../samples-resource-manager-templates.md)
