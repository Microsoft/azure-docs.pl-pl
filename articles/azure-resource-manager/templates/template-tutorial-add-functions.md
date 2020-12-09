---
title: Samouczek — Dodawanie funkcji szablonu
description: Dodaj funkcje szablonu do szablonu Azure Resource Manager (szablon ARM), aby skonstruować wartości.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 67c88e98d966a21163aafefcad8363086d5b3bf4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931048"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>Samouczek: Dodawanie funkcji szablonu do szablonu ARM

W tym samouczku dowiesz się, jak dodać [funkcje szablonu](template-functions.md) do szablonu Azure Resource Manager (szablon ARM). Funkcje służą do dynamicznego konstruowania wartości. Oprócz tych funkcji szablonu udostępnianych przez system można także tworzyć [funkcje zdefiniowane przez użytkownika](./template-user-defined-functions.md). Ukończenie tego samouczka trwa **7 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego parametrów](template-tutorial-add-parameters.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

Lokalizacja konta magazynu jest trwale kodowana dla **regionu Wschodnie stany USA**. Jednak może być konieczne wdrożenie konta magazynu w innych regionach. Nastąpi ponowne wystawienie problemu związanego z brakiem możliwości elastyczności szablonu. Można dodać parametr do lokalizacji, ale byłoby to doskonałe, jeśli jego wartość domyślna była większa niż tylko zakodowana wartość.

## <a name="use-function"></a>Use — funkcja

Po ukończeniu poprzedniego samouczka w tej serii funkcja została już użyta. Po dodaniu `"[parameters('storageName')]"` użyto funkcji [Parameters](template-functions-deployment.md#parameters) . Nawiasy wskazują, że składnia wewnątrz nawiasów jest [wyrażeniem szablonu](template-expressions.md). Menedżer zasobów rozpoznaje składnię zamiast traktować ją jako wartość literału.

Funkcje zwiększają elastyczność szablonu przez dynamiczne pobieranie wartości podczas wdrażania. W tym samouczku użyjesz funkcji, aby pobrać lokalizację grupy zasobów używanej do wdrożenia.

Poniższy przykład wyróżnia zmiany w celu dodania parametru o nazwie **Location**.  Wartość domyślna parametru wywołuje funkcję [ResourceManager](template-functions-resource.md#resourcegroup) . Ta funkcja zwraca obiekt z informacjami o grupie zasobów używanej do wdrożenia. Jedną z właściwości obiektu jest właściwość Location. W przypadku użycia wartości domyślnej Lokalizacja konta magazynu ma tę samą lokalizację co grupa zasobów. Zasoby w grupie zasobów nie muszą udostępniać tej samej lokalizacji. W razie konieczności można także podać inną lokalizację.

Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>Wdrażanie szablonu

W poprzednich samouczkach utworzono konto magazynu w regionie Wschodnie stany USA, ale Grupa zasobów została utworzona w środkowych stanach USA. W tym samouczku konto magazynu zostanie utworzone w tym samym regionie, w którym znajduje się grupa zasobów. Użyj wartości domyślnej dla lokalizacji, więc nie musisz podawać tej wartości parametru. Musisz podać nową nazwę konta magazynu, ponieważ tworzysz konto magazynu w innej lokalizacji. Na przykład użyj **Store2** jako prefiksu zamiast **store1**.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie założono, że ustawiono zmienną **TemplateFile** na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj **pełnego** przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj przełącznika **debugowania** , aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Zobaczysz, że zasób konta magazynu został wdrożony i ma tę samą lokalizację co grupa zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto funkcji podczas definiowania wartości domyślnej dla parametru. W tej serii samouczków będziesz używać funkcji. Na końcu serii dodasz funkcje do każdej sekcji szablonu.

> [!div class="nextstepaction"]
> [Dodawanie zmiennych](template-tutorial-add-variables.md)
