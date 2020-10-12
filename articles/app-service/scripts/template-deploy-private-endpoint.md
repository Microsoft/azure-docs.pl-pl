---
title: Użyj szablonu Azure Resource Manager, aby wdrożyć prywatny punkt końcowy dla aplikacji sieci Web
description: Dowiedz się, jak wdrożyć prywatny punkt końcowy dla aplikacji sieci Web przy użyciu szablonu usługi ARM.
author: ericgre
ms.assetid: 49e460d0-7759-4ceb-b5a4-f1357e4fde56
ms.topic: sample
ms.date: 07/08/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: acf7198477ce0c55f9321fd3f8e57294a60c0961
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652029"
---
# <a name="create-an-app-service-app-and-deploy-a-private-endpoint-by-using-an-azure-resource-manager-template"></a>Tworzenie aplikacji App Service i wdrażanie prywatnego punktu końcowego przy użyciu szablonu Azure Resource Manager

W tym przewodniku szybki start użyjesz szablonu Azure Resource Manager (ARM), aby utworzyć aplikację sieci Web i uwidocznić ją za pomocą prywatnego punktu końcowego.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisite"></a>Wymaganie wstępne

Potrzebujesz konta platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-private-endpoint"></a>Tworzenie prywatnego punktu końcowego

Ten szablon służy do tworzenia prywatnego punktu końcowego dla aplikacji internetowej platformy Azure.

### <a name="review-the-template"></a>Przegląd szablonu

:::code language="json" source="~/quickstart-templates/101-private-endpoint-webapp/azuredeploy.json" :::

### <a name="deploy-the-template"></a>Wdrażanie szablonu

Poniżej przedstawiono sposób wdrażania szablonu Azure Resource Manager na platformie Azure:

1. Aby zalogować się do platformy Azure i otworzyć szablon, wybierz ten link:  [Wdróż na platformie Azure](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-private-endpoint-webapp%2Fazuredeploy.json). Szablon tworzy sieć wirtualną, aplikację sieci Web, prywatny punkt końcowy i prywatną strefę DNS.
2. Wybierz lub Utwórz grupę zasobów.
3. Wprowadź nazwę aplikacji sieci Web, plan Azure App Service i prywatny punkt końcowy.
5. Zapoznaj się z instrukcją dotyczącą warunków i postanowień. Jeśli zgadzasz się, wybierz opcję **Akceptuję warunki i postanowienia podane powyżej**  >  **Purchase**. Wdrożenie może potrwać kilka minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie potrzebujesz już zasobów utworzonych przy użyciu prywatnego punktu końcowego, Usuń grupę zasobów. Spowoduje to usunięcie prywatnego punktu końcowego i wszystkich powiązanych zasobów.

Aby usunąć grupę zasobów, wywołaj `Remove-AzResourceGroup` polecenie cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Następne kroki

- Więcej szablonów Azure Resource Manager dla Azure App Service aplikacji sieci Web można znaleźć w [przykładach szablonów usługi ARM](../samples-resource-manager-templates.md).
