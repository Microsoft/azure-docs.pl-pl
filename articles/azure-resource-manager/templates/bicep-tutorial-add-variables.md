---
title: Samouczek — Dodawanie zmiennej do Azure Resource Manager pliku Bicep
description: Dodaj zmienne do pliku Bicep, aby uprościć składnię.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: da2755c1f2c0f9fa891fe1a99b1fed21f64492c8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632479"
---
# <a name="tutorial-add-variables-to-azure-resource-manager-bicep-file"></a>Samouczek: Dodawanie zmiennych do Azure Resource Manager pliku Bicep

W tym samouczku dowiesz się, jak dodać zmienną do pliku Bicep. Zmienne upraszczają pliki Bicep, umożliwiając wpisanie wyrażenia jednokrotnie i ponowne użycie w pliku Bicep. Ukończenie tego samouczka trwa **7 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego funkcji](bicep-tutorial-add-functions.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka plik Bicep miał następującą zawartość:

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.bicep":::

Parametr nazwy konta magazynu jest trudny do użycia, ponieważ trzeba podać unikatową nazwę. Jeśli wcześniej samouczki zostały wykonane w tej serii, prawdopodobnie nie można już złamać unikatowej nazwy. Ten problem można rozwiązać, dodając zmienną, która konstruuje unikatową nazwę konta magazynu.

## <a name="use-variable"></a>Użyj zmiennej

Poniższy przykład pokazuje zmiany w celu dodania zmiennej do pliku Bicep, który tworzy unikatową nazwę konta magazynu. Skopiuj cały plik i Zastąp plik Bicep jego zawartością.

:::code language="bicep" source="~/resourcemanager-templates/get-started-with-templates/add-variable/azuredeploy.bicep" range="1-31" highlight="1-3,19,22":::

Należy zauważyć, że zawiera zmienną o nazwie `uniqueStorageName` . Ta zmienna używa trzech funkcji do konstruowania wartości ciągu.

Znasz funkcję [ResourceManager](template-functions-resource.md#resourcegroup) . W takim przypadku uzyskasz `id` Właściwość, a nie `location` Właściwość, jak pokazano w poprzednim samouczku. `id`Właściwość zwraca pełny identyfikator grupy zasobów, w tym identyfikator subskrypcji i nazwę grupy zasobów.

Funkcja [uniqueString](template-functions-string.md#uniquestring) tworzy 13-znakową wartość skrótu. Zwracana wartość jest określana na podstawie parametrów, które są przekazywane. W tym samouczku użyjesz identyfikatora grupy zasobów jako danych wejściowych dla wartości skrótu. Oznacza to, że można wdrożyć ten plik Bicep w różnych grupach zasobów i uzyskać inną unikatową wartość ciągu. Jednak ta sama wartość jest pobierana w przypadku wdrożenia w tej samej grupie zasobów.

Bicep obsługuje składnię [interpolacji ciągów](https://en.wikipedia.org/wiki/String_interpolation#) . Dla tej zmiennej Pobiera ciąg z parametru i ciąg z `uniqueString` funkcji, a następnie łączy je w jeden ciąg.

`storagePrefix`Parametr umożliwia przekazywanie prefiksu, który ułatwia identyfikowanie kont magazynu. Można utworzyć własną konwencję nazewnictwa, która ułatwia identyfikowanie kont magazynu po wdrożeniu z długiej listy zasobów.

Na koniec należy zauważyć, że nazwa magazynu jest teraz ustawiona na zmienną zamiast parametru.

## <a name="deploy-bicep-file"></a>Wdróż plik Bicep

Wdróżmy plik Bicep. Wdrażanie tego pliku Bicep jest łatwiejsze niż w przypadku poprzednich plików Bicep, ponieważ podano tylko prefiks nazwy magazynu.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnamevariable `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addnamevariable \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
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
> [Dodawanie danych wyjściowych](bicep-tutorial-add-outputs.md)
