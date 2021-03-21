---
title: Samouczek — Dodawanie modułów do Azure Resource Manager pliku Bicep
description: Za pomocą modułów można hermetyzować złożone Szczegóły deklaracji zasobów nieprzetworzonych.
author: mumian
ms.date: 03/10/2021
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 6efd9c230df49c83adc17361082af85b0ef9edc5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633142"
---
# <a name="tutorial-add-modules-to-azure-resource-manager-bicep-file"></a>Samouczek: Dodawanie modułów do Azure Resource Manager pliku Bicep

W [poprzednim samouczku](bicep-tutorial-use-parameter-file.md)przedstawiono sposób użycia pliku parametrów do wdrożenia pliku Bicep. W ramach tego samouczka nauczysz się, jak za pomocą modułów Bicep hermetyzować złożone Szczegóły deklaracji zasobów nieprzetworzonych. Moduły mogą być udostępniane i ponownie używane w ramach rozwiązania.  Ukończenie może potrwać około **12 minut** .

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zalecamy ukończenie [samouczka dotyczącego pliku parametrów](bicep-tutorial-use-parameter-file.md), ale nie jest to wymagane.

Musisz mieć Visual Studio Code z rozszerzeniem Bicep i Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Narzędzia Bicep](bicep-tutorial-create-first-bicep.md#get-tools).

## <a name="review-bicep-file"></a>Przejrzyj plik Bicep

Na końcu poprzedniego samouczka plik Bicep miał następującą zawartość:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.bicep":::

Ten plik Bicep działa poprawnie. Jednak w przypadku większych rozwiązań trzeba podzielić plik Bicep na wiele modułów powiązanych, aby można było udostępniać i ponownie używać tych modułów. Bieżący plik Bicep wdraża konto magazynu, plan usługi App Service i witrynę sieci Web.  Oddzielmy konto magazynu do modułu.

## <a name="create-bicep-module"></a>Utwórz moduł Bicep

Każdy plik Bicep może być używany jako moduł, więc nie ma określonej składni do definiowania modułu. Utwórz plik _Storage. Bicep_ o następującej zawartości:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/storage.bicep":::

Ten moduł zawiera zasób konta magazynu i powiązane z nim parametry i zmienne. Wartości parametru _Location_ i _resourceTags_ Parameters zostały usunięte. Te wartości zostaną przesłane z głównego pliku Bicep.

## <a name="consume-bicep-module"></a>Korzystanie z modułu Bicep

Zastąp definicję zasobu konta magazynu w istniejącej _azuredeploy. Bicep_ następującą zawartością Bicep:

```bicep
module stg './storage.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: storagePrefix
    location: location
    resourceTags: resourceTags
  }
}
```

- **module**: słowo kluczowe.
- **Nazwa symboliczna** (STG): jest to identyfikator modułu.
- **plik modułu**: ścieżka do modułu w tym przykładzie jest określona za pomocą ścieżki względnej (./Storage.Bicep). Wszystkie ścieżki w Bicep muszą być określone przy użyciu separatora katalogu ukośnika (/), aby zapewnić spójną międzyplatformę kompilacji. Ukośnik odwrotny systemu Windows ( \) znak nie jest obsługiwany.

Aby pobrać punkt końcowy magazynu, zaktualizuj dane wyjściowe w _azuredeploy. Bicep_ do poniższego Bicep:

```bicep
output storageEndpoint object = stg.outputs.storageEndpoint
```

Ukończony azuredeploy. Bicep ma następującą zawartość:

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-module/azuredeploy.bicep":::

## <a name="deploy-template"></a>Wdrażanie szablonu

Użyj interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby wdrożyć szablon.

Jeśli grupa zasobów nie została utworzona, zobacz [Tworzenie grupy zasobów](bicep-tutorial-create-first-bicep.md#create-resource-group). W przykładzie założono, że ustawiono `bicepFile` zmienną na ścieżkę do pliku Bicep, jak pokazano w [pierwszym samouczku](bicep-tutorial-create-first-bicep.md#deploy-bicep-file).

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Aby uruchomić to polecenie cmdlet wdrażania, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) Azure PowerShell.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addmodule `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $bicepFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Aby uruchomić to polecenie wdrażania, musisz mieć [najnowszą wersję](/cli/azure/install-azure-cli) interfejsu wiersza polecenia platformy Azure.

```azurecli
az deployment group create \
  --name addmodule \
  --resource-group myResourceGroup \
  --template-file $bicepFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
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

Gratulacje, wprowadzenie do wdrożenia plików Bicep na platformie Azure zostało zakończone. Daj nam znać, jeśli masz jakieś komentarze i sugestie w sekcji Opinie. Dziękujemy!

Kolejna seria samouczków prowadzi do bardziej szczegółowych informacji na temat wdrażania szablonów.

> [!div class="nextstepaction"]
> [Dodaj moduły](./bicep-tutorial-add-modules.md)
