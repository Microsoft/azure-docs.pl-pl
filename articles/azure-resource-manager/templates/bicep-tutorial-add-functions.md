---
title: Samouczek — dodawanie funkcji do Azure Resource Manager Bicep
description: Dodaj funkcje do plików Bicep, aby konstruować wartości.
author: mumian
ms.date: 04/20/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: references_regions
ms.openlocfilehash: 43dec6ceb21a6604bc0034b3f14b79ffd2cbe263
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773805"
---
# <a name="tutorial-add-functions-to-azure-resource-manager-bicep-file"></a>Samouczek: dodawanie funkcji do Azure Resource Manager Bicep

Z tego samouczka dowiesz się, jak dodać [funkcje szablonu](template-functions.md) do pliku Bicep. Funkcje są służące do dynamicznego konstruowania wartości. Obecnie bicep nie obsługuje funkcji zdefiniowanych przez użytkownika. Ukończenie tego **samouczka zajmuje 7** minut.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie samouczka [na temat parametrów](bicep-tutorial-add-parameters.md), ale nie jest to wymagane.

Musisz mieć rozszerzenie Visual Studio Code Bicep oraz interfejs wiersza polecenia Azure PowerShell platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przeglądanie pliku Bicep

Na końcu poprzedniego samouczka plik bicep miał następującą zawartość:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.bicep":::

Lokalizacja konta magazynu jest zakodowana w lokalizacji **Wschodnie usa.** Może być jednak konieczne wdrożenie konta magazynu w innych regionach. Ponownie występuje problem z plikiem Bicep, który nie jest elastyczny. Możesz dodać parametr lokalizacji, ale byłoby świetnie, gdyby jego wartość domyślna miała większe znaczenie niż tylko zakodowana wartość.

## <a name="use-function"></a>Korzystanie z funkcji

Funkcje zapewniają elastyczność pliku Bicep przez dynamiczne pobieranie wartości podczas wdrażania. W tym samouczku użyjemy funkcji , aby uzyskać lokalizację grupy zasobów, która jest używana do wdrożenia.

W poniższym przykładzie pokazano zmiany w celu dodania parametru o nazwie `location` . Wartość domyślna parametru wywołuje [funkcję resourceGroup.](template-functions-resource.md#resourcegroup) Ta funkcja zwraca obiekt z informacjami o grupie zasobów używanej do wdrożenia. Jedną z właściwości obiektu jest właściwość location. Jeśli używasz wartości domyślnej, lokalizacja konta magazynu ma taką samą lokalizację jak grupa zasobów. Zasoby w grupie zasobów nie muszą współużytkować tej samej lokalizacji. W razie potrzeby możesz również podać inną lokalizację.

Skopiuj cały plik i zastąp plik Bicep jego zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep" range="1-30" highlight="18,22":::

## <a name="deploy-bicep-file"></a>Wdrażanie pliku Bicep

W poprzednich samouczkach utworzono konto magazynu w regionach Wschodnie usa, ale grupa zasobów została utworzona w środkowych usa. W tym samouczku konto magazynu zostanie utworzone w tym samym regionie co grupa zasobów. Użyj wartości domyślnej dla lokalizacji, aby nie trzeba było podać tej wartości parametru. Musisz podać nową nazwę konta magazynu, ponieważ tworzysz konto magazynu w innej lokalizacji. Na przykład użyj **store2** jako prefiksu zamiast **store1**.

Jeśli grupa zasobów nie została jeszcze utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że zmienna została ustawiona na ścieżkę do pliku Bicep, jak pokazano `bicepFile` w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrożenia, musisz mieć najnowszą [wersję](/powershell/azure/install-az-ps) Azure PowerShell.

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
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika , aby uzyskać informacje o tworzonach zasobach. Użyj `debug` przełącznika , aby uzyskać więcej informacji na temat debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Wdrożenie można zweryfikować, eksplorując grupę zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. W menu po lewej stronie wybierz pozycję **Grupy zasobów.**
1. Wybierz grupę zasobów, w przypadku których wdrożono program .
1. Zobaczysz, że zasób konta magazynu został wdrożony i ma tę samą lokalizację co grupa zasobów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przenosisz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli teraz się zatrzymujesz, możesz wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. W menu Azure Portal pozycję **Grupa zasobów** z menu po lewej stronie.
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. Wybierz **pozycję Usuń grupę zasobów** z górnego menu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku używaliśmy funkcji podczas definiowania wartości domyślnej dla parametru. W tej serii samouczków będziesz nadal używać funkcji. Na końcu serii dodasz funkcje do każdej sekcji pliku Bicep.

> [!div class="nextstepaction"]
> [Dodawanie zmiennych](bicep-tutorial-add-variables.md)
