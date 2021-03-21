---
title: Samouczek — Dodawanie zmiennej do szablonu
description: Dodaj zmienne do szablonu Azure Resource Manager (szablon ARM), aby uprościć składnię.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 46ed1fc55a108bf80089d249abc58bc5d1a6479a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97106958"
---
# <a name="tutorial-add-variables-to-your-arm-template"></a>Samouczek: Dodawanie zmiennych do szablonu ARM

W tym samouczku dowiesz się, jak dodać zmienną do szablonu Azure Resource Manager (szablon ARM). Zmienne upraszczają szablony, umożliwiając jednokrotne wpisanie wyrażenia i ponowne użycie go przez cały szablon. Ukończenie tego samouczka trwa **7 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego funkcji](template-tutorial-add-functions.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Na końcu poprzedniego samouczka szablon zawierał następujący kod JSON:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json":::

Parametr nazwy konta magazynu jest trudny do użycia, ponieważ trzeba podać unikatową nazwę. Jeśli wcześniej samouczki zostały wykonane w tej serii, prawdopodobnie nie można już złamać unikatowej nazwy. Ten problem można rozwiązać, dodając zmienną, która konstruuje unikatową nazwę konta magazynu.

## <a name="use-variable"></a>Użyj zmiennej

Poniższy przykład wyróżnia zmiany w celu dodania zmiennej do szablonu, który tworzy unikatową nazwę konta magazynu. Skopiuj cały plik i Zastąp jego zawartość.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.json" range="1-47" highlight="5-9,29-31,36":::

Należy zauważyć, że zawiera zmienną o nazwie `uniqueStorageName` . Ta zmienna używa czterech funkcji do konstruowania wartości ciągu.

Znasz już funkcję [Parameters](template-functions-deployment.md#parameters) , więc nie sprawdzimy jej.

Znasz również funkcję [ResourceManager](template-functions-resource.md#resourcegroup) . W takim przypadku uzyskasz `id` Właściwość, a nie `location` Właściwość, jak pokazano w poprzednim samouczku. `id`Właściwość zwraca pełny identyfikator grupy zasobów, w tym identyfikator subskrypcji i nazwę grupy zasobów.

Funkcja [uniqueString](template-functions-string.md#uniquestring) tworzy 13-znakową wartość skrótu. Zwracana wartość jest określana na podstawie parametrów, które są przekazywane. W tym samouczku użyjesz identyfikatora grupy zasobów jako danych wejściowych dla wartości skrótu. Oznacza to, że można wdrożyć ten szablon w różnych grupach zasobów i uzyskać inną unikatową wartość ciągu. Jednak ta sama wartość jest pobierana w przypadku wdrożenia w tej samej grupie zasobów.

Funkcja [concat](template-functions-string.md#concat) przyjmuje wartości i łączy je. Dla tej zmiennej Pobiera ciąg z parametru i ciąg z `uniqueString` funkcji, a następnie łączy je w jeden ciąg.

`storagePrefix`Parametr umożliwia przekazywanie prefiksu, który ułatwia identyfikowanie kont magazynu. Można utworzyć własną konwencję nazewnictwa, która ułatwia identyfikowanie kont magazynu po wdrożeniu z długiej listy zasobów.

Na koniec należy zauważyć, że nazwa magazynu jest teraz ustawiona na zmienną zamiast parametru.

## <a name="deploy-template"></a>Wdrażanie szablonu

Wdróżmy szablon. Wdrażanie tego szablonu jest łatwiejsze niż w przypadku poprzednich szablonów, ponieważ podano tylko prefiks nazwy magazynu.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](template-tutorial-create-first-template.md#create-resource-group). W przykładzie przyjęto założenie, że ustawiono `templateFile` zmienną na ścieżkę do pliku szablonu, jak pokazano w [pierwszym samouczku](template-tutorial-create-first-template.md#deploy-template).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Można zweryfikować wdrożenie, przeeksplorowanie grupy zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Wybierz grupę zasobów, do której została wdrożona.
1. Zobaczysz, że zasób konta magazynu został wdrożony. Nazwa konta magazynu jest **przechowywana** wraz z ciągiem znaków losowych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeniesiesz się do następnego samouczka, nie musisz usuwać grupy zasobów.

Jeśli zatrzymasz się teraz, możesz chcieć wyczyścić wdrożone zasoby, usuwając grupę zasobów.

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

W tym samouczku Dodano zmienną, która tworzy unikatową nazwę konta magazynu. W następnym samouczku zwracasz wartość ze wdrożonego konta magazynu.

> [!div class="nextstepaction"]
> [Dodawanie danych wyjściowych](template-tutorial-add-outputs.md)
