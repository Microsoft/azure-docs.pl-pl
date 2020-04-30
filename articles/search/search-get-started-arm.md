---
title: Wdrażanie usługi przy użyciu szablonu Azure Resource Manager
titleSuffix: Azure Cognitive Search
description: Możesz szybko wdrożyć wystąpienie usługi Azure Wyszukiwanie poznawcze przy użyciu szablonu Azure Resource Manager.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/20/2020
ms.openlocfilehash: 6da2c324872a86c2c0ce6f55801bc7b0ee8d713e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80607624"
---
# <a name="quickstart-deploy-cognitive-search-using-a-resource-manager-template"></a>Szybki Start: wdrażanie Wyszukiwanie poznawcze przy użyciu szablonu Menedżer zasobów

W tym artykule omówiono proces wdrażania zasobu Wyszukiwanie poznawcze platformy Azure w Azure Portal przy użyciu szablonu Menedżer zasobów.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="create-a-cognitive-search-service"></a>Tworzenie usługi Wyszukiwanie poznawcze

### <a name="review-the-template"></a>Zapoznaj się z szablonem

Szablon używany w tym przewodniku szybki start pochodzi z [szablonów platformy Azure](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchservices).

:::code language="json"source="~/quickstart-templates/101-azure-search-create/azuredeploy.json" range="1-86" highlight="4-50":::

Zasób platformy Azure zdefiniowany w tym szablonie:

- [Microsoft. Search/searchServices](https://docs.microsoft.com/azure/templates/Microsoft.Search/2015-08-19/searchServices): Tworzenie usługi Azure wyszukiwanie poznawcze

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wybierz poniższy obraz, aby zalogować się na platformie Azure i otworzyć szablon. Szablon tworzy zasób Wyszukiwanie poznawcze platformy Azure.

[![Wdrażanie na platformie Azure](./media/search-get-started-arm/arm-deploybuttona.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-search-create%2Fazuredeploy.json)

W portalu jest wyświetlany formularz, który umożliwia łatwe udostępnianie wartości parametrów. Niektóre parametry są wstępnie wypełnione wartościami domyślnymi z szablonu. Musisz podać swoją subskrypcję, grupę zasobów, lokalizację i nazwę usługi. Jeśli chcesz użyć Cognitive Services w potoku [wzbogacenia AI](https://docs.microsoft.com/azure/search/cognitive-search-concept-intro) , na przykład w celu przeanalizowania plików obrazów binarnych dla tekstu, wybierz lokalizację, w której są dostępne zarówno wyszukiwanie poznawcze, jak i Cognitive Services. Obie usługi muszą znajdować się w tym samym regionie dla obciążeń wzbogacania AI. Po zakończeniu formularza należy wyrazić zgodę na warunki i postanowienia, a następnie wybrać przycisk Kup, aby zakończyć wdrożenie.

> [!div class="mx-imgBorder"]
> ![Azure Portal wyświetlania szablonu](./media/search-get-started-arm/arm-portalscrnsht.png)

## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

Po zakończeniu wdrażania możesz uzyskać dostęp do nowej grupy zasobów i nowej usługi wyszukiwania w portalu.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Inne Wyszukiwanie poznawcze Przewodniki Szybki Start i samouczki zostały skompilowane na tym przewodniku Szybki Start. Jeśli planujesz kontynuować pracę z kolejnymi przewodnikami Szybki Start i samouczkami, możesz pozostawić ten zasób na miejscu. Gdy nie jest już potrzebne, można usunąć grupę zasobów, która spowoduje usunięcie usługi Wyszukiwanie poznawcze i powiązanych zasobów.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono usługę Wyszukiwanie poznawcze przy użyciu szablonu Azure Resource Manager i zweryfikowano wdrożenie. Aby dowiedzieć się więcej na temat Wyszukiwanie poznawcze i Azure Resource Manager, przejdź do artykułu poniżej.

 - Zapoznaj się [z omówieniem wyszukiwanie poznawcze platformy Azure](https://docs.microsoft.com/azure/search/search-what-is-azure-search)
 - [Tworzenie indeksu](https://docs.microsoft.com/azure/search/search-get-started-portal) dla usługi wyszukiwania
 - [Tworzenie aplikacji wyszukiwania](https://docs.microsoft.com/azure/search/search-create-app-portal) przy użyciu kreatora portalu
 - [Utwórz zestawu umiejętności,](https://docs.microsoft.com/azure/search/cognitive-search-quickstart-blob) aby wyodrębnić informacje z danych


