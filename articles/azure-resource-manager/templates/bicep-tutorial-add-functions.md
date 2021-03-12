---
title: Samouczek — Dodawanie funkcji do Azure Resource Manager plików Bicep
description: Dodaj funkcje do plików Bicep, aby utworzyć wartości.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: b909beb0cce9ad04ba00068ee25247520dcff47d
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102633159"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Samouczek: Dodawanie funkcji do Azure Resource Manager pliku Bicep

W tym samouczku dowiesz się, jak dodać [funkcje szablonu](template-functions.md) do pliku Bicep. Funkcje służą do dynamicznego konstruowania wartości. Oprócz tych funkcji szablonu udostępnianych przez system można także tworzyć [funkcje zdefiniowane przez użytkownika](./template-user-defined-functions.md). Ukończenie tego samouczka trwa **7 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego parametrów](bicep-tutorial-add-parameters.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka plik Bicep miał następującą zawartość:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

Lokalizacja konta magazynu jest trwale kodowana dla **regionu Wschodnie stany USA**. Jednak może być konieczne wdrożenie konta magazynu w innych regionach. Nastąpi ponowne wystawienie problemu związanego z brakiem możliwości pliku Bicep. Można dodać parametr do lokalizacji, ale byłoby to doskonałe, jeśli jego wartość domyślna była większa niż tylko zakodowana wartość.

## <a name="use-function"></a>Use — funkcja

Funkcje umożliwiają dodawanie elastyczności do pliku Bicep przez dynamiczne pobieranie wartości podczas wdrażania. W tym samouczku użyjesz funkcji, aby pobrać lokalizację grupy zasobów używanej do wdrożenia.

Poniższy przykład pokazuje zmiany w celu dodania parametru o nazwie `location` . Wartość domyślna parametru wywołuje funkcję [ResourceManager](template-functions-resource.md#resourcegroup) . Ta funkcja zwraca obiekt z informacjami o grupie zasobów używanej do wdrożenia. Jedną z właściwości obiektu jest właściwość Location. W przypadku użycia wartości domyślnej Lokalizacja konta magazynu ma tę samą lokalizację co grupa zasobów. Zasoby w grupie zasobów nie muszą udostępniać tej samej lokalizacji. W razie konieczności można także podać inną lokalizację.

Skopiuj cały plik i Zastąp plik Bicep jego zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

W poprzednich samouczkach utworzono konto magazynu w regionie Wschodnie stany USA, ale Grupa zasobów została utworzona w środkowych stanach USA. W tym samouczku konto magazynu zostanie utworzone w tym samym regionie, w którym znajduje się grupa zasobów. Użyj wartości domyślnej dla lokalizacji, więc nie musisz podawać tej wartości parametru. Musisz podać nową nazwę konta magazynu, ponieważ tworzysz konto magazynu w innej lokalizacji. Na przykład użyj **Store2** jako prefiksu zamiast **store1**.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

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

W tym samouczku użyto funkcji podczas definiowania wartości domyślnej dla parametru. W tej serii samouczków będziesz używać funkcji. Na końcu serii dodasz funkcje do każdej sekcji pliku Bicep.

> [!div class="nextstepaction"]
> [Dodawanie zmiennych](bicep-tutorial-add-variables.md)
