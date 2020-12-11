---
title: Samouczek — używanie pliku parametrów do wdrażania szablonu
description: Użyj plików parametrów, które zawierają wartości używane do wdrażania szablonu Azure Resource Manager (szablon ARM).
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8dc625237c03cf38f9fe2eb0446c55dcf96f5f3a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106771"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>Samouczek: Wdrażanie szablonu ARM przy użyciu plików parametrów

W ramach tego samouczka nauczysz się używać [plików parametrów](parameter-files.md) do przechowywania wartości przekazywanych podczas wdrażania. W poprzednich samouczkach użyto parametrów wbudowanych z poleceniem wdrożenia. To podejście działało do testowania szablonu Azure Resource Manager (szablon ARM), ale podczas automatyzowania wdrożeń można łatwiej przekazać zestaw wartości dla danego środowiska. Pliki parametrów ułatwiają pakowanie wartości parametrów dla określonego środowiska. W tym samouczku utworzysz pliki parametrów dla środowisk deweloperskich i produkcyjnych. Ukończenie może potrwać około **12 minut** .

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego tagów](template-tutorial-add-tags.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem narzędzi Menedżer zasobów i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia szablonu](template-tutorial-create-first-template.md#get-tools).

## <a name="review-template"></a>Przejrzyj szablon

Szablon zawiera wiele parametrów, które można podać podczas wdrażania. Na końcu poprzedniego samouczka szablon wyglądał następująco:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

Ten szablon działa dobrze, ale teraz chcesz łatwo zarządzać parametrami przekazywanymi do szablonu.

## <a name="add-parameter-files"></a>Dodaj pliki parametrów

Pliki parametrów to pliki JSON ze strukturą podobną do szablonu. W pliku podaj wartości parametrów, które mają być przekazywane podczas wdrażania.

W pliku parametrów podaj wartości parametrów w szablonie. Nazwa każdego parametru w pliku parametrów musi być zgodna z nazwą parametru w szablonie. W nazwie nie jest rozróżniana wielkość liter, ale w celu łatwego wyświetlenia pasujących wartości zalecamy dopasowanie wielkości liter z szablonu.

Nie musisz podawać wartości dla każdego parametru. Jeśli nieokreślony parametr ma wartość domyślną, ta wartość jest używana podczas wdrażania. Jeśli parametr nie ma wartości domyślnej i nie jest określony w pliku parametrów, zostanie wyświetlony monit o podanie wartości podczas wdrażania.

Nie można określić nazwy parametru w pliku parametrów, która nie jest zgodna z nazwą parametru w szablonie. Wystąpił błąd podczas podanych nieznanych parametrów.

W Visual Studio Code Utwórz nowy plik z następującą zawartością. Zapisz plik o nazwie _azuredeploy.parameters.dev.jsna_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

Ten plik jest plikiem parametrów środowiska deweloperskiego. Zwróć uwagę, że używa **Standard_LRS** dla konta magazynu, nazw zasobów z prefiksem **dev** i ustawia `Environment` tag na **dev**.

Utwórz nowy plik o następującej zawartości. Zapisz plik o nazwie _azuredeploy.parameters.prod.jsna_.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

Ten plik jest plikiem parametrów dla środowiska produkcyjnego. Zwróć uwagę, że używa **Standard_GRS** dla konta magazynu, nazw zasobów z prefiksem **contoso** i ustawia tag _środowiska_ na **produkcyjny**. W rzeczywistym środowisku produkcyjnym warto również używać usługi App Service z jednostką SKU inną niż bezpłatna, ale będziemy nadal korzystać z tej jednostki SKU dla tego samouczka.

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon.

Jako ostatni test szablonu Utwórzmy dwie nowe grupy zasobów. Jeden dla środowiska deweloperskiego i jeden dla środowiska produkcyjnego.

W przypadku zmiennych szablonu i parametrów Zastąp ciągi `{path-to-the-template-file}` , `{path-to-azuredeploy.parameters.dev.json}` , i klamrowe `{path-to-azuredeploy.parameters.prod.json}` nawiasy `{}` z szablonem i ścieżkami plików parametrów.

Po pierwsze wdrożenie zostanie wdrożone w środowisku deweloperskim.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

Teraz wdrażamy je w środowisku produkcyjnym.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> Jeśli wdrożenie nie powiodło się, użyj `verbose` przełącznika, aby uzyskać informacje o tworzonych zasobach. Użyj `debug` przełącznika, aby uzyskać więcej informacji na potrzeby debugowania.

## <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Możesz zweryfikować wdrożenie, przeeksplorowanie grup zasobów z Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
1. Zobaczysz dwie nowe grupy zasobów wdrożone w tym samouczku.
1. Wybierz pozycję Grupa zasobów i Wyświetl wdrożone zasoby. Zwróć uwagę, że są one zgodne z wartościami określonymi w pliku parametrów dla danego środowiska.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

1. Z Azure Portal z menu po lewej stronie wybierz pozycję **Grupa zasobów** .
2. Wprowadź nazwę grupy zasobów w polu **Filtruj według nazwy**. Jeśli ta seria została ukończona, istnieją trzy grupy zasobów do usunięcia — **zasobu**, **myResourceGroupDev** i **myResourceGroupProd**.
3. Wybierz nazwę grupy zasobów.
4. W górnym menu wybierz pozycję **Usuń grupę zasobów** .

## <a name="next-steps"></a>Następne kroki

Gratulacje, wprowadzenie do wdrożenia szablonów na platformie Azure. Daj nam znać, jeśli masz jakieś komentarze i sugestie w sekcji Opinie. Dziękujemy!

Kolejna seria samouczków prowadzi do bardziej szczegółowych informacji na temat wdrażania szablonów.

> [!div class="nextstepaction"]
> [Wdrażanie szablonu lokalnego](./deployment-tutorial-local-template.md)
