---
title: Odnajdź właściwości zasobów
description: Opisuje sposób wyszukiwania właściwości zasobów.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327752"
---
# <a name="discover-resource-properties"></a>Odnajdź właściwości zasobów

Przed utworzeniem Menedżer zasobów szablonów należy zrozumieć, jakie typy zasobów są dostępne i jakie wartości należy użyć w szablonie. W tym artykule pokazano, jak można znaleźć właściwości do uwzględnienia w szablonie.

## <a name="find-resource-provider-namespaces"></a>Znajdowanie przestrzeni nazw dostawcy zasobów

Zasoby w szablonie ARM są zdefiniowane za pomocą przestrzeni nazw dostawcy zasobów i typu zasobu. Na przykład, Microsoft. Storage/storageAccounts to pełna nazwa typu zasobu konta magazynu. Microsoft. Storage jest przestrzenią nazw. Jeśli nie znasz jeszcze przestrzeni nazw dla typów zasobów, których chcesz użyć, zobacz [dostawcy zasobów dla usług platformy Azure](../management/azure-services-resource-providers.md).

![Mapowanie przestrzeni nazw dostawcy zasobów Menedżer zasobów](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Eksportowanie szablonów

Najprostszym sposobem uzyskania właściwości szablonu dla istniejących zasobów jest wyeksportowanie szablonu. Aby uzyskać więcej informacji, zobacz [eksport jednego i wielu zasobów do szablonu w Azure Portal](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Użyj rozszerzenia narzędzi Menedżer zasobów

Visual Studio Code i rozszerzenie narzędzi Azure Resource Manager ułatwia dokładne sprawdzenie, które właściwości są potrzebne dla każdego typu zasobu. Udostępniają one funkcje IntelliSense i fragmenty kodu, które upraszczają sposób definiowania zasobów w szablonie. Aby uzyskać więcej informacji, zobacz [Szybki Start: tworzenie Azure Resource Manager szablonów z Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

Poniższy zrzut ekranu przedstawia zasób konta magazynu został dodany do szablonu:

![Fragmenty kodu rozszerzenia narzędzi Menedżer zasobów Tools](./media/view-resources/resource-manager-tools-extension-snippets.png)

Rozszerzenie zawiera również listę opcji właściwości konfiguracji.

![Konfigurowalne wartości rozszerzenia narzędzi Menedżer zasobów](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Używanie dokumentacji szablonu

Odwołanie do szablonu Azure Resource Manager to najbardziej kompleksowy zasób dla schematu szablonu. Można znaleźć wersje interfejsu API, format szablonu i informacje o właściwościach.

1. Przejdź do [dokumentacji szablonu Azure Resource Manager](/azure/templates/).
1. W lewym okienku nawigacji wybierz pozycję **Magazyn**, a następnie wybierz pozycję **wszystkie zasoby**. Na stronie wszystkie zasoby są zestawione typy zasobów i wersje.

    ![wersje zasobów odwołania do szablonu](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Jeśli znasz typ zasobu, możesz przejść bezpośrednio do tej strony przy użyciu następującego formatu adresu URL: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Wybierz najnowszą wersję. Zalecane jest użycie najnowszej wersji interfejsu API.

    Sekcja **Format szablonu** zawiera wszystkie właściwości konta magazynu. **jednostka SKU** znajduje się na liście:

    ![Format konta magazynu odwołań do szablonu](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Przewiń w dół, aby wyświetlić **obiekt SKU** w sekcji **wartości właściwości** . W tym artykule przedstawiono dozwolone wartości w polu Nazwa jednostki SKU:

    ![wartości jednostki SKU konta magazynu odwołania do szablonu](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    Na końcu strony sekcja **Szablony szybkiego startu** zawiera listę szablonów szybkiego startu platformy Azure, które zawierają typ zasobu:

    ![Szablony szybkiego startu dla konta magazynu odwołań szablonów](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

Odwołanie do szablonu jest powiązane z każdą lokacją dokumentacji usługi platformy Azure.  Na przykład [Witryna dokumentacji Key Vault](../../key-vault/general/overview.md):

![Menedżer zasobów odwołanie do szablonu Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>Użyj Eksplorator zasobów

Eksplorator zasobów jest osadzony w Azure Portal. Przed użyciem tej metody musisz mieć konto magazynu. Jeśli go nie masz, wybierz poniższy przycisk, aby go utworzyć:

[![Wdrażanie na platformie Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W polu wyszukiwania wprowadź nazwę **Eksplorator zasobów**, a następnie wybierz pozycję **Eksplorator zasobów**.

    ![Zrzut ekranu przedstawia wyszukiwanie Eksplorator zasobów w Azure Portal.](./media/view-resources/azure-portal-resource-explorer.png)

1. W obszarze po lewej stronie rozwiń pozycję **subskrypcje**, a następnie rozwiń swoją subskrypcję platformy Azure. Konto magazynu można znaleźć w obszarze **dostawcy** lub **ResourceGroups**.

    ![Azure Portal Eksplorator zasobów](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Dostawcy**: rozwiń węzeł **dostawcy**  ->  **Microsoft. Storage**  ->  **storageAccounts**, a następnie wybierz konto magazynu.
    - **ResourceGroups**: Wybierz grupę zasobów, która zawiera konto magazynu, wybierz pozycję **zasoby**, a następnie wybierz konto magazynu.

    Po prawej stronie zostanie wyświetlona konfiguracja jednostki SKU dla istniejącego konta magazynu podobna do:

    ![Jednostka SKU konta magazynu Eksplorator zasobów Azure Portal](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Użyj Resources.azure.com

Resources.azure.com jest publiczną witryną sieci Web dostępną dla wszystkich użytkowników z subskrypcją platformy Azure. Jest on w wersji zapoznawczej.  Zamiast tego Rozważ użycie [Eksplorator zasobów](#use-resource-explorer) . To narzędzie zapewnia następujące funkcje:

- Odkryj interfejsy API zarządzania zasobami platformy Azure.
- Pobierz dokumentację interfejsu API i informacje o schemacie.
- Twórz wywołania interfejsu API bezpośrednio we własnych subskrypcjach.

Aby zademonstrować sposób pobierania informacji o schemacie przy użyciu tego narzędzia, potrzebne jest konto magazynu. Jeśli go nie masz, wybierz poniższy przycisk, aby go utworzyć:

[![Wdrażanie na platformie Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Przejdź do [resources.Azure.com](https://resources.azure.com/). Potrwa kilka chwil dla narzędzia do popularnego okienka po lewej stronie.
1. Wybierz pozycję **subskrypcje**.

    ![Mapowanie interfejsu API resource.azure.com](./media/view-resources/resources-azure-com-api-mapping.png)

    Węzeł po lewej stronie jest zgodny z wywołaniem interfejsu API po prawej stronie. Wywołanie interfejsu API można wykonać, wybierając przycisk **Pobierz** .
1. W obszarze po lewej stronie rozwiń pozycję **subskrypcje**, a następnie rozwiń swoją subskrypcję platformy Azure. Konto magazynu można znaleźć w obszarze **dostawcy** lub **ResourceGroups**.

    - **Dostawcy**: rozwiń węzeł **dostawcy**  ->  **Microsoft. Storage**  ->  **storageAccounts**, a następnie przejdź do konta magazynu.
    - **ResourceGroups**: Wybierz grupę zasobów, która zawiera konto magazynu, a następnie wybierz pozycję **zasoby**.

    Po prawej stronie zostanie wyświetlona konfiguracja jednostki SKU dla istniejącego konta magazynu podobna do:

    ![Jednostka SKU konta magazynu Eksplorator zasobów Azure Portal](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób znajdowania informacji o schemacie szablonu. Aby dowiedzieć się więcej na temat tworzenia szablonów Menedżer zasobów, zobacz [Omówienie struktury i składni szablonów ARM](./template-syntax.md).
