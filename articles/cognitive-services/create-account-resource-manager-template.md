---
title: Tworzenie zasobu Cognitive Services platformy Azure przy użyciu szablonów usługi ARM | Microsoft Docs
description: Utwórz zasób usługi poznawczej platformy Azure przy użyciu szablonu ARM.
keywords: usługi poznawcze, rozwiązania poznawcze, analiza poznawcze i nieposztuczna inteligencja
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 3/22/2021
ms.author: aahi
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 161c5779926acad8814ec057f24e36f371738483
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864367"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Szybki Start: Tworzenie zasobu Cognitive Services przy użyciu szablonu ARM

W tym przewodniku szybki start opisano sposób tworzenia Cognitive Services przy użyciu szablonu Azure Resource Manager (szablon ARM).

Usługa Azure Cognitive Services to usługi w chmurze z interfejsami API REST, a także dostępne zestawy SDK dla deweloperów, które ułatwiają deweloperom tworzenie analiz poznawczych w aplikacjach, bez konieczności bezpośredniego sztucznej analizy danych lub wiedzy. Dzięki platformie Azure Cognitive Services deweloperzy mogą łatwo dodawać funkcje poznawcze do swoich aplikacji przy użyciu rozwiązań poznawczych, które widzą, słyszą, mówią, wiedzą, a nawet z przyczyn.

Utwórz zasób przy użyciu szablonu Azure Resource Manager (szablon ARM). Ten zasób usługi wiele usług umożliwia:

* Dostęp do wielu Cognitive Services platformy Azure za pomocą jednego klucza i punktu końcowego.
* Konsolidowanie rozliczeń od używanych usług.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Jeśli Twoje środowisko spełnia wymagania wstępne i masz doświadczenie w korzystaniu z szablonów ARM, wybierz przycisk **Wdróż na platformie Azure**. Szablon zostanie otwarty w witrynie Azure Portal.

[![Wdrażanie usługi poznawczej na platformie Azure](../media/template-deployments/deploy-to-azure.svg "Wdrażanie usługi poznawczej na platformie Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Przegląd szablonu

Szablon używany w tym przewodniku Szybki start jest jednym z [szablonów szybkiego startu platformy Azure](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

Jeden zasób platformy Azure jest zdefiniowany w szablonie:
* [Microsoft. CognitiveServices/accounts](/azure/templates/microsoft.cognitiveservices/accounts): tworzy zasób Cognitive Services.

## <a name="deploy-the-template"></a>Wdrożenie szablonu

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. Kliknij przycisk **Wdróż na platformie Azure** .

    [![Wdrażanie na platformie Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Wprowadź następujące wartości.

    |Wartość  |Opis  |
    |---------|---------|
    | **Subskrypcja** | Wybierz subskrypcję platformy Azure. |
    | **Grupa zasobów** | Wybierz pozycję **Utwórz nowy**, wprowadź unikatową nazwę grupy zasobów, a następnie kliknij przycisk **OK**. |
    | **Region** | Wybierz region.  Na przykład **Wschodnie stany USA** |
    | **Nazwa usługi poznawczej** | Zamień na unikatową nazwę zasobu. Podczas sprawdzania poprawności wdrożenia będzie potrzebna nazwa w następnej sekcji. |
    | **Lokalizacja** | Zamień na region używany powyżej. |
    | **Magazyn** | [Warstwa cenowa](https://azure.microsoft.com/pricing/details/cognitive-services/) zasobu. |

    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Ekran tworzenia zasobów.":::

3. Wybierz pozycję **Przeglądanie + tworzenie**, a następnie pozycję **Utwórz**. Po pomyślnym zakończeniu wdrażania zasobu zostanie wyróżniony przycisk **Przejdź do zasobu** .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/CLI)

> [!NOTE]
> `az deployment group` Tworzenie wymaga interfejsu wiersza polecenia platformy Azure w wersji 2,6 lub nowszej. Aby wyświetlić typ wersji `az --version` . Więcej informacji zawiera strona [dokumentacji](/cli/azure/deployment/group).

Uruchom następujący skrypt przy użyciu interfejsu wiersza polecenia platformy Azure [na komputerze lokalnym](/cli/azure/install-azure-cli)lub w przeglądarce z przyciskiem **Wypróbuj** . Wprowadź nazwę i lokalizację (na przykład `centralus` ) dla nowej grupy zasobów, a szablon ARM zostanie użyty do wdrożenia w nim zasobu Cognitive Services. Zapamiętaj nazwę, której używasz. Będzie on używany później do weryfikacji wdrożenia.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="review-deployed-resources"></a>Przejrzyj wdrożone zasoby

# <a name="portal"></a>[Portal](#tab/portal)

Po zakończeniu wdrożenia będzie można kliknąć przycisk **Przejdź do zasobu** , aby wyświetlić nowy zasób. Grupę zasobów można również znaleźć według:

1. Wybieranie **grup zasobów** z menu nawigacji po lewej stronie.
2. Wybieranie nazwy grupy zasobów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/CLI)

Korzystając z interfejsu wiersza polecenia platformy Azure, uruchom następujący skrypt i wprowadź nazwę utworzonej wcześniej grupy zasobów.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję Cognitive Services, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów znajdujących się w grupie.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję **Grupy zasobów**, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów zawierającą zasób do usunięcia
3. Kliknij prawym przyciskiem myszy na liście grup zasobów. Wybierz pozycję **Usuń grupę zasobów** i potwierdź.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/CLI)

Korzystając z interfejsu wiersza polecenia platformy Azure, uruchom następujący skrypt i wprowadź nazwę utworzonej wcześniej grupy zasobów.

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="see-also"></a>Zobacz też

* Zobacz temat **[uwierzytelnianie żądań na platformie Azure Cognitive Services](authentication.md)** , jak bezpiecznie współpracować z Cognitive Services.
* Zobacz, **[co to jest usługa Azure Cognitive Services?](./what-are-cognitive-services.md)** , aby uzyskać listę różnych kategorii w programie Cognitive Services.
* Zobacz **[obsługę języka naturalnego](language-support.md)** , aby wyświetlić listę języków naturalnych obsługiwanych przez Cognitive Services.
* Zobacz **[używanie Cognitive Services jako kontenerów,](cognitive-services-container-support.md)** aby zrozumieć, jak używać Cognitive Services w Premium.
* Zobacz **[Planowanie i zarządzanie kosztami Cognitive Services](plan-manage-costs.md)** , aby oszacować koszt korzystania z Cognitive Services.
