---
title: Za pomocą tego szablonu Azure Resource Manager będzie można wdrożyć prywatny punkt końcowy dla aplikacji internetowej.
description: Dowiedz się, jak wdrożyć prywatny punkt końcowy dla aplikacji sieci Web przy użyciu szablonu usługi ARM
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ff2d59ed569037f34e24a69ffafa0df237a3de34
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86535710"
---
# <a name="create-an-app-service-app-and-deploy-private-endpoint-using-azure-resource-manager-template"></a>Tworzenie aplikacji App Service i wdrażanie prywatnego punktu końcowego przy użyciu szablonu Azure Resource Manager

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager, aby utworzyć aplikację sieci Web i uwidocznić ją za pomocą prywatnego punktu końcowego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Wymaganie wstępne

Potrzebujesz konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Ten szablon służy do tworzenia prywatnego punktu końcowego dla aplikacji internetowej platformy Azure.

### <a name="review-the-template"></a>Przegląd szablonu

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Poniżej przedstawiono sposób wdrażania szablonu Azure Resource Manager na platformie Azure:

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz pozycję **Wdróż na platformie Azure**. Szablon tworzy sieć wirtualną, aplikację sieci Web, prywatny punkt końcowy i prywatną strefę DNS.

   [Wdróż na platformie Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json)

2. Wybierz lub Utwórz grupę zasobów.
3. Wpisz nazwę aplikacji sieci Web, App Service plan, prywatny punkt końcowy.
5. Zapoznaj się z instrukcjami dotyczącymi warunków i postanowień. Jeśli zgadzasz się, wybierz opcję Akceptuję warunki i postanowienia podane powyżej > zakupie. Wdrożenie może potrwać kilka minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu prywatnego punktu końcowego, Usuń grupę zasobów. Spowoduje to usunięcie prywatnego punktu końcowego i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

- Dodatkowe szablony Azure Resource Manager dla Azure App Service Web Apps można znaleźć w [przykładach szablonów usługi ARM](../samples-resource-manager-templates.md).
